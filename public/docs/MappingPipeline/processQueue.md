## Introduction
**ProcessQueue** refers to the Azure QueueTrigger function which executes  when a new message appears in the Scan Report queue. A new message is added when a Scan Report file is uploaded from the user. 

## Triggering the function
When a Scan Report file is uploaded on the site, it is saved into Azure Blob Storage and a message is send to the storage Queue. This message includes the `scan_report_id` from the `ScanReport` model and `blob_name` which is the name of the uploaded file.
![Queue Trigger](images/trigger.png)

**Figure 1** Triggering the Queue when uploading a Scan Report

## Accessing the File
As soon as the ProcessQueue is triggered it uses the `blob_name` from the message body to download the file from Azure Blob Storage into a BytesIO() object. 