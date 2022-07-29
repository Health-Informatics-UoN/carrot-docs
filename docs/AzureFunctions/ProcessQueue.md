!!! note
    This page is for developers of Azure Functions - not users

## Introduction
**ProcessQueue** refers to an Azure Queue Trigger function which processes and saves a scan report and data dictionary to the CCOM database. It is also the name of the directory in the webapp's root directory which houses the Azure Function code. The function is triggered when a new message is added to the queue. Messages are added to the queue when a user uploads a Scan Report and Data Dictionary to the webapp.

This guide documents the process when a scan report **and** and a data dictionary are uploaded to the system (i.e. the most complex scenario). Note, however, that a user can choose to upload a scan report without a data dictionary. The code snippets below have been extracted from `ScanReportFormView` in views.py. The snippets contain only the key code to illustrate a point/functionality, rather than offering the full FormView class code required for successful execution. Please refer to views.py for unabridged code.

## Triggering the function
The function is triggered when a White Rabbit Scan Report file and a data dictionary are uploaded on the webapp. The code in `ScanReportFormView` carries out the following steps after form submission and during form validation:

1. Creates a new object in the `ScanReport` model
2. Connects to our Azure account
3. Creates unique file names for the scan report and data dictionary
4. Creates a dictionary holding the ScanReport model object and file names from Step 3 called `azure_dict`
5. Uploads data to our Azure blob storage accounts (`scan-reports` and `data-dictionaries`) using the connecting string from Step 2
6. Submits `azure_dict` to the message queue so the Azure function using the connection string from Step 2. This messages is requires so the function ca get the correct files from Azure blob storage

After  uploading the raw data and submitting a message to the queue, views.py doesn't carry out any further code execution - the online Azure function takes over, processes the scan report and dictionary (more on this below) and then POSTs the data back to the webapp via the CCOM API using the `requests` library. 

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
The table entries are then appended to a JSON array `json_data` which forms the input send in a POST request to the [API](/CaRROT-Docs/CaRROT-Mapper/API).
The API automatically generates the `id` for each table that was created. These ids are extracted from the POST request response and saved in a list (`table_ids`)

### Scan Report Field
**Field Overview** contains all the information on the fields including the name, description and other useful columns. ProcessQueue iterates through the rows in the sheet and generates a new `ScanReportField` entry.
Once it detects an empty line (end of a table) it saves the fields found in that table.
This is done by appending the field entries to a JSON array `json_data`, which forms the input to a POST request made to the [API](/CaRROT-Docs/CaRROT-Mapper/API). From the response it saves the `field_ids` and `field_names` and stores them into a dictionary as key value pairs e.g "Field Name": Field ID.

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

A ScanReportValue entry is generated by iterating through the output of `process_scan_report_sheet_table()`. The value entries are linked to the `ScanReportField` model using the function output and the dictionary with all the field names and ids. Same as with the other two models the value entries are appended to a JSON array `json_data`, and form the input to a POST request made to the [API](/CaRROT-Docs/CaRROT-Mapper/API).

### Data Dictionary Creation
If a user uploads a data dictionary, there is a small subroutine during the `ScanReportValue` code which matches the scan report value name against the supplied data dictionary. If there's a match, the code saves the dictionary's value description to the `value_description` field in the `ScanReportValue` model:

```python
val_desc = next((row['value_description'] for row in data_dictionary if str(row['field_name']) == str(name) and str(row['code']) == str(value)), None)
```

## Converting Concept Codes to ConceptIDs
### Introduction
Some scan reports (most likely from the larger data providers) will be supplied with concept *codes* as values. In effect the mapping has already been done for us-- we just need to make sure we return valid and standard OMOP conceptIDs. In instances where concept *codes* are provided, they will be coded within a given vocabulary e.g. SNOMED or RxNorm. The ProcessQueue Azure function has therefore been updated with the ability to look up concept codes and convert them to standard and valid OMOP codes. This removes the requirement from the data team of manually looking up concept codes and finding the relevant conceptID.

The Data Team have specified a list of vocabularies that are most likely to be used. These can be found at the top of `ProcessQueue/__init__.py`.

### Operation
The concept code to conceptID lookup occurs as each scan report value is processed in `__init__.py`. Immediately after a value description is looked up (see above), the code checks to see whether the dictionary contains any information on a vocabulary coding system. A dictionary may look something like:

 csv_file_name  | field_name    | code |value
----------| -------------|----------|---------
  questionnaire | sex  | 0  | male
  survey | question1  | SNOMED | 

In this example, all of the values within the field `question1` will be encoded as SNOMED concept codes. For example, the question might be: "Main symptom" and the answer of 'cough' would be encoded as "49727002" under the SNOMED vocabulary.

For a given value, we determine whether it falls within a field defined as a vocabulary field with:

```python
code = next(
    (
        row["code"]
        for row in data_dictionary
        if str(row["field_name"]) == str(name)
    ),
    None,
)
```
If the value of `code` is within our list of vocabularies, we use `omop_helpers.get_concept_from_concept_code()` to look up the standard and valid OMOP conceptID with the following:

```python
# If 'code' is in our vocab list, try and convert the ScanReportValue (concept code) to conceptID
# If there's a faulty concept code for the vocab, fail gracefully and set concept_id to default (-1)
if code in vocabs:
    try:
        concept_id = omop_helpers.get_concept_from_concept_code(
            concept_code=value,
            vocabulary_id=code,
            no_source_concept=True,
        )
        concept_id = concept_id["concept_id"]
    except:
        concept_id = -1
else:
    concept_id = -1
```

Where `value` is the scan report value (i.e. the concept code) in that loop's iteration and `code` is the name of the vocabulary from the data dictionary's 'code' column (see [here](https://co-connect.ac.uk/co-connect-data-files-and-meta-data-standardisation/) for a standard data dictionary template.) If the supplied scan report value (concept code) is incorrect for any reason, ProcessQueue will simply default to setting `concept_id` to the default of -1.

Here is an example if you wanted to manually look up a concept code:

```python
concept_id = omop_helpers.get_concept_from_concept_code(
                                concept_code=49727002,
                                vocabulary_id="SNOMED,
                                no_source_concept=True,
                            )
```
The argument `no_source_concept` tells the function to not return the non-standard conceptID. It will only return the standard and valid conceptID when this is set to `True`.

With a conceptID in hand, we construct a dictionary called `scan_report_value_entry` with all the necessary details and append it to a list for a single `POST` to the database:

```python

data = []

scan_report_value_entry = {
    "created_at": datetime.utcnow().strftime("%Y-%m-%dT%H:%M:%S.%fZ"),
    "updated_at": datetime.utcnow().strftime("%Y-%m-%dT%H:%M:%S.%fZ"),
    "value": value,
    "frequency": int(frequency),
    "conceptID": concept_id,
    "value_description": val_desc,
    "scan_report_field": names_x_ids[name],
}

data.append(scan_report_value_entry)

```

After iterating over all scan report values we serialise `data` to JSON and POST it to the model `ScanReportValue`:

```python
# Create JSON array for POSTing
json_data = json.dumps(data)

# POST data to ScanReportValues model
response = requests.post(
    url=api_url + "scanreportvalues/", data=json_data, headers=headers
)
```

!!! Important
    In the above process we are actually doing something a little hacky - we're leveraging a redundant field in `ScanReportValue` called `conceptID` to *temporarily* hold information on the newly looked up concept code -> conceptID operation. The `conceptID` field was  created in the early days of the project when it was assumed that scan report values mapped 1:1 with a conceptID. After moving to a 1:n relationship and saving conceptIDs to the `ScanReportConcept` model, the `conceptID` field was retired but never removed from the `ScanReportValue` model.

### Updating ScanReportConcept
By this point, we have not associated conceptIDs with scan report values using the `ScanReportConcept` model. In effect, any conceptID information from the scan report is--at present--held in the model `ScanReportValue`. We therefore need to 'move' the conceptID data from the `conceptID` field and create a record for it in `ScanReportConcept`. 

!!! Important
    The reason we have to do this is--prior to POSTing scan report values to `ScanReportValue`--we have no primary keys for any scan report values. Without the PKs, we can't establish a relationship between `ScanReportValue` and `ScanReportConcept`. After the `POST` request we have the PKs required to associate a `ScanReportConcept` to a `ScanReportValue`.

After POSTing the scan report values to the database, we must then return all `ScanReportValues` where the conceptID != -1. In other words, we want to return all records where we performed a concept code -> conceptID operation. We do this with:

```python
new_data = requests.get(
    url=api_url+ "scanreportvaluepks/?scan_report="+ str(body["scan_report_id"]),
    headers=headers,
)
```
We created a custom `ViewSet` called `ScanReportValuePKViewSet` which takes a Scan Report ID and returns all scan report values where there is a conceptID in the `conceptID` field. We then iterate over these model objects and create legitimate concept entries in `ScanReportConcept`:

```python
data = json.loads(new_data.content.decode("utf-8"))

# Create a list for a bulk data upload to the ScanReportConcept model
concept_id_data = []
    for concept in data:

        entry = {
            "nlp_entity": None,
            "nlp_entity_type": None,
            "nlp_confidence": None,
            "nlp_vocabulary": None,
            "nlp_processed_string": None,
            "concept": concept["conceptID"],
            "object_id": concept["id"],
            "content_type": 17,
        }

    concept_id_data.append(entry)

concept_id_data_json = json.dumps(concept_id_data)

# POST the ScanReportConcept data to the model
response = requests.post(
    url=api_url + "scanreportconcepts/",
    headers=headers,
    data=concept_id_data_json,
)
```
As mentioned previously, saving data to `conceptID` in `ScanReportValue` was a temporary operation. To avoid any confusion, we therefore must remove the data in the `conceptID` field and replace it with the default value of -1:

```python
put_update = {"conceptID": -1}
put_update_json = json.dumps(put_update)

for concept in data:
    response = requests.patch(
        url=api_url + "scanreportvalues/" + str(concept["id"]) + "/",
        headers=headers,
        data=put_update_json,
    )
```


