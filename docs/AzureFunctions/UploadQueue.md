!!! note
    This page is for developers of Azure Functions - not users

## Introduction
**UploadQueue** refers to an Azure Queue Trigger function which processes and saves a scan report and data dictionary to the Carrot database. It is also the name of the directory in the webapp's root directory which houses the Azure Function code. The function is triggered when a new message is added to the queue. Messages are added to the queue when a user uploads a Scan Report and Data Dictionary to the webapp.

This guide documents the process when a scan report **and** and a data dictionary are uploaded to the system (i.e. the most complex scenario). Note, however, that a user can choose to upload a scan report without a data dictionary. The code snippets below have been extracted from `ScanReportFormView` in `views.py`. The snippets contain only the key code to illustrate a point/functionality, rather than offering the full FormView class code required for successful execution. Please refer to `api/views.py` for unabridged code.

## Triggering the function
The function is triggered when a White Rabbit Scan Report file and a data dictionary are uploaded on the webapp. The code in `ScanReportFormView` carries out the following steps after form submission and during form validation:

1. Creates a new object in the `ScanReport` model
2. Connects to our Azure account
3. Creates unique file names for the scan report and data dictionary
4. Creates a dictionary holding the ScanReport model object and file names from Step 3 called `azure_dict`
5. Uploads data to our Azure blob storage accounts (`scan-reports` and `data-dictionaries`) using the connecting string from Step 2
6. Submits `azure_dict` to the message queue so the Azure function using the connection string from Step 2. This messages is requires so the function ca get the correct files from Azure blob storage

After  uploading the raw data and submitting a message to the queue, views.py doesn't carry out any further code execution - the online Azure function takes over, processes the scan report and dictionary (more on this below) and then POSTs the data back to the webapp via the Carrot API using the `requests` library. 

### Creating a `ScanReport` object
We create a model object in `ScanReport` so that tables, fields and values can later be associated with the correct scan report (as the scan report/dictionary is processed within the Azure Function and POST'd back to the webapp.)

```python
scan_report = ScanReport.objects.create(
    data_partner=form.cleaned_data["data_partner"],
    dataset=form.cleaned_data["dataset"]
)
```

### Connect to Azure Account
We connect to our Azure account with the following:

```python
blob_service_client = BlobServiceClient.from_connection_string(os.getenv('STORAGE_CONN_STRING'))
```

Note that `STORAGE_CONN_STRING` has to be set either in your .env file when developing locally or within the the Azure Portal. See Sam/Vas for more details on where environment variables are kept online.

### Create unique file names
We create unique datetime and random alphanumeric strings to append to the file names of the scan report and data dictionary:

```python
rand = ''.join(random.choices(string.ascii_lowercase + string.digits, k=8))
dt = '{:%Y%m%d-%H%M%S_}'.format(datetime.datetime.now())
```
We do this so that if we ever need to cross-reference a particular scan report with a particular data dictionary, we can easily see (1) when the files were upload (specifed with `dt`) and (2) what dictionary belongs to which scan report (unique alphanumeric string defined in `rand`)

### Create dictionary 
We then create a dictionary containing:
1. The newly created scan report ID 
2. Unique scan report file name
3. Unique data dictionary nam
```python
azure_dict={
    "scan_report_id":scan_report.id,
    "scan_report_blob":os.path.splitext(str(form.cleaned_data.get('scan_report_file')))[0]+"_"+dt+rand+".xlsx",
    "data_dictionary_blob":os.path.splitext(str(form.cleaned_data.get('data_dictionary_file')))[0]+"_"+dt+rand+".csv",
}
```

### Upload data to Azure blob storage
We then upload our data to Azure blob storage. Scan reports go to the container called `scan-reports` and data dictionaries go to the container called `data-dictionaries`. 

```python
blob_client = blob_service_client.get_blob_client(container="scan-reports", blob=os.path.splitext(str(form.cleaned_data.get('scan_report_file')))[0]+"_"+dt+rand+".xlsx")
blob_client.upload_blob(form.cleaned_data.get('scan_report_file').open())
blob_client = blob_service_client.get_blob_client(container="data-dictionaries", blob=os.path.splitext(str(form.cleaned_data.get('data_dictionary_file')))[0]+"_"+dt+rand+".csv")
blob_client.upload_blob(form.cleaned_data.get('data_dictionary_file').open())
```

### Submit message to Azure queue
We then encode and submit the message defined in `azure_dict` to the Azure message queue:
```python
queue_message=json.dumps(azure_dict)
message_bytes = queue_message.encode('ascii')
base64_bytes = base64.b64encode(message_bytes)
base64_message = base64_bytes.decode('ascii')

...
...
...

queue = QueueClient.from_connection_string(
  conn_str=os.environ.get("STORAGE_CONN_STRING"),
  queue_name=os.environ.get("SCAN_REPORT_QUEUE_NAME")
  )
queue.send_message(base64_message)
```
Once the message has been sent, the Azure Function will automatically pick up the message and execute the code in `__init__.py`. Using the data in the message, the function will know what files to grab from blob storage for processing. By defining `scan_report_id` the function knows how to relate newly created tables, fields and values to the correct scan report.

# Azure Function Operation

## Accessing the File
As soon as the ProcessQueue is triggered it uses `scan_report_blob` and `data_dictionary_blob` from the message body to download the correct files from Azure Blob Storage into a BytesIO() object.

The message body is accessed via:
```python

# Get message from queue
message = json.dumps(
    {
        "id": msg.id,
        "body": msg.get_body().decode("utf-8"),
        "expiration_time": (
            msg.expiration_time.isoformat() if msg.expiration_time else None
        ),
        "insertion_time": (
            msg.insertion_time.isoformat() if msg.insertion_time else None
        ),
        "time_next_visible": (
            msg.time_next_visible.isoformat() if msg.time_next_visible else None
        ),
        "pop_receipt": msg.pop_receipt,
        "dequeue_count": msg.dequeue_count,
    }
)

# Grab message body from storage queues, 
# extract filenames for scan reports and dictionaries
message = json.loads(message)
body = ast.literal_eval(message["body"])
scan_report_blob = body["scan_report_blob"]
data_dictionary_blob = body["data_dictionary_blob"]
```
The files are accessed from blob storage with the following code:
```python
# Grab scan report data from blob
container_client = blob_service_client.get_container_client("scan-reports")
blob_scanreport_client = container_client.get_blob_client(scan_report_blob)
streamdownloader = blob_scanreport_client.download_blob()
scanreport = BytesIO(streamdownloader.readall())

# Grab data dictionary data from blob
container_client = blob_service_client.get_container_client("data-dictionaries")
blob_dict_client = container_client.get_blob_client(data_dictionary_blob)
streamdownloader = blob_dict_client.download_blob()
data_dictionary = list(csv.DictReader(streamdownloader.readall().decode('utf-8').splitlines()))
```

## Processing
The next stage is to step through the Excel workbook and create model entries for each **ScanReportTable**, **ScanReportField** and **ScanReportValue** present in the file.

### Scan Report Table
The **Field Overview** sheet in a White Rabbit Scan Report has a 'Table' column which seperates different table names using a blank row. ProcessQueue main method iterates through the rows in the 'Table' column and appends each unique table name to the list `table_names`. 
For each table in `table_names` a new `ScanReportTable` entry is generated. The table entries are linked to the `ScanReport` model using the `scan_report_id` from the queue message body.
The table entries are then appended to a JSON array `json_data` which forms the input send in a POST request to the [API](../Carrot-Mapper/API.md).
The API automatically generates the `id` for each table that was created. These ids are extracted from the POST request response and saved in a list (`table_ids`)

### Scan Report Field
**Field Overview** contains all the information on the fields including the name, description and other useful columns. ProcessQueue iterates through the rows in the sheet and generates a new `ScanReportField` entry.
Once it detects an empty line (end of a table) it saves the fields found in that table.
This is done by appending the field entries to a JSON array `json_data`, which forms the input to a POST request made to the [API](../Carrot-Mapper/API.md). From the response it saves the `field_ids` and `field_names` and stores them into a dictionary as key value pairs e.g "Field Name": Field ID.

### Scan Report Value

Scan report values are stored in sheets named after their corresponding table. Once the fields in a table are saved, it moves to the corresponding sheet (where the sheet is the same as the table_name) and the `process_scan_report_sheet_table()` function is called on that worksheet. The function extracts the data in the following format:

**Input**

 ID  | Frequency    | Date |Frequency
----------| -------------|----------|---------
  1 | 20           | 02/12/2020  |5
  2 | 3            | 12/11/2020 |37

**Output**

(ID, 1, 20)
(Date, 02/12/2020, 5)
(ID, 2, 3)
(Date, 12/11/2020, 37)

A ScanReportValue entry is generated by iterating through the output of `process_scan_report_sheet_table()`. The value entries are linked to the `ScanReportField` model using the function output and the dictionary with all the field names and ids. Same as with the other two models the value entries are appended to a JSON array `json_data`, and form the input to a POST request made to the [API](../Carrot-Mapper/API.md).

### Data Dictionary Creation
If a user uploads a data dictionary, there is a small subroutine during the `ScanReportValue` code which matches the scan report value name against the supplied data dictionary. If there's a match, the code saves the dictionary's value description to the `value_description` field in the `ScanReportValue` model:

```python
val_desc = next((row['value_description'] for row in data_dictionary if str(row['field_name']) == str(name) and str(row['code']) == str(value)), None)
```

