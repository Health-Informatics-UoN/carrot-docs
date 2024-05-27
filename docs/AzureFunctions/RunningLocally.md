!!! note
    This page is for developers of Azure Functions - not users

## Introduction

Running Azure Functions locally is a little involved; this guide has been written to get you up and running 
with Azure CLI and Functions. Note that this guide assumes you're using VSCode. All of the functionality 
described here can be replicated outside of VSCode (in Azure CLI) but instructions for doing so are not 
detailed in this guide.

>Note that this guide is not intended to demonstrate how to create an Azure Function from scratch. 
> Its purpose is to show you how to run pre-coded Functions.

To follow this guide you need to install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## Azure Functions Basics

Conceptually, queue-based Azure Functions are simple to follow. A **_message_** is posted to a **_message queue_**. 
A Function then executes on messages within a message queue. In Carrot-Mapper, a message is posted to a message 
queue as JSON but other formats are possible (e.g. XML). Here's an example message destined for the `scanreports` queue:

```
{
  "scan_report_id": 105, 
  "blob_name": "GOSH_CoStar_WhiteRabbit_MetaData_V24_T29kygl.xlsx"
}
```

And here is an example message destined for the `nlpqueue` queue:

```
{
  "documents": 
  [{
    "language": "en", 
    "id": "17730_field", 
    "text": "The patient has a fever"
  }]
}

```

An [Azure function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) is made up of three key files. Two are kept in the function's directory (`app/workers`) and define what and how the function should run:

1. `init.py` - This contains the function's logic. The method `main()` should be present for the function to execute.
2. `function.json` - This tells Azure what type of function you're developing and also what queue from which to pick messages up from.

Another file is kept outside of the function's directory and should not be commited to Git:

1. `local.settings.json` - Contains environment variables for your function. It must be present to run Azure functions locally. Contact a member of the development team to get all keys and connection strings for this file. For reference, a local.settings.json file looks like this:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "REDACTED,
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "STORAGE_CONN_STRING": "REDACTED",
    "NLP_API_KEY": "REDACTED",
    "APP_URL": "REDACTED",
    "AZ_FUNCTION_KEY": "REDACTED",
    "SCAN_REPORT_QUEUE_NAME": "scanreports-local",
    "NLP_QUEUE_NAME": "nlpqueue-local"

  }
}

```
You only need to have 1 `local.settings.json` file for all functions within the Carrot-Mapper project. Note, 
however, that it should be updated with extra queue names if/when the project requires them.

### Azure Functions in Carrot-Mapper

The Carrot-Mapper project has two different Azure Functions: one for processing scan reports (called 
`ProcessQueue` which posts to the message queue called `scanreports`), another to run the NLP service 
(called `NLPQueue` which posts to the message queue called `nlpqueue`). The code for these functions lives 
in the directories ProcessQueue and NLPQueue, respectively.

## Azure Functions Queues

For each process (NLP and Scan Reports), we have two queues each - a 'local' queue and a 'live' queue 
(for a total of 4 unique queues.) As environment variables, they are encoded as:

##### Local queues
```
NLP_QUEUE_NAME=nlpqueue-local
SCAN_REPORT_QUEUE_NAME=scanreports-local
```

##### Deployed queues
```
NLP_QUEUE_NAME=nlpqueue
SCAN_REPORT_QUEUE_NAME=scanreports
```

The purpose of the local queues is that, when developing locally, messages are sent only to a 'local' queue. 
This stops 'development' messages from posting to the 'live' message queue.

These environment variables which control where messages are sent must be maintained in the following locations:

1. `local.settings.json` - A local file to hold Azure Function environment variables. Should point to 'local' 
(e.g. `NLP_QUEUE_NAME=nlpqueue-local`). To reiterate, this file must not be tracked on GitHub, otherwise 
secrets will be exposed!
2. Within the Azure Function App. Env Vars need to be set within the Azure Portal to the 'live' vars. Speak to 
Sam Cox about adding vars on the Azure Portal.
3. Within the CCOM webapp, the `.env` file must include variables for 'local' on your local machine and set to 
'live' variables on App Service.


## Running Azure Functions Locally

Because Azure Functions are cloud-based, it's somewhat of a misnomer to talk of running an Azure Function 
completely 'locally'. In reality, you're still posting to a message queue in the cloud, even when developing 
locally. However, Azure CLI--when running in debug mode (more on this later)--will 'hijack' the messages in the 
message queue (depending on how you've set the environment vairables in `local.settings.json` and `.env`) 
and allow you to process them with the code you're developing locally.

To start debugging locally in VSCode you must first ensure that CCOM is up and running locally 
([see here for building and running the Carrot-Mapper Docker image](https://github.com/Health-Informatics-UoN/CaRROT-Mapper#readme) ). 
Once your local server is running, you can start Azure Function's debugging mode by clicking: 

Run (top toolbar) -> Start Debugging

After a few seconds, you'll see something like the following printed to the debugging terminal that's 
started when you run debugging:

```
> Executing task: . .venv/bin/activate && func host start <

Found Python version 3.9.5 (python3).

Azure Functions Core Tools
Core Tools Version:       3.0.3477 Commit hash: 5fbb9a76fc00e4168f2cc90d6ff0afe5373afc6d  (64-bit)
Function Runtime Version: 3.0.15584.0

[2021-07-02T09:34:28.929Z] Cannot create directory for shared memory usage: /dev/shm/AzureFunctions
[2021-07-02T09:34:28.929Z] System.IO.FileSystem: Access to the path '/dev/shm/AzureFunctions' is denied. Operation not permitted.

Functions:

        NLPQueue: queueTrigger
        ProcessQueue: queueTrigger

For detailed output, run func with --verbose flag.
[2021-07-02T09:34:32.711Z] Traceback (most recent call last):
[2021-07-02T09:34:32.711Z]   File "/usr/local/Cellar/azure-functions-core-tools@3/3.0.3477/workers/python/3.9/OSX/X64/azure_functions_worker/bindings/shared_memory_data_transfer/file_accessor_unix.py", line 127, in _get_valid_mem_map_dirs
[2021-07-02T09:34:32.711Z]     os.makedirs(dir_path)
[2021-07-02T09:34:32.711Z]   File "/usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9/lib/python3.9/os.py", line 215, in makedirs
[2021-07-02T09:34:32.711Z]     makedirs(head, exist_ok=exist_ok)
[2021-07-02T09:34:32.711Z]   File "/usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9/lib/python3.9/os.py", line 225, in makedirs
[2021-07-02T09:34:32.711Z]     mkdir(name, mode)
[2021-07-02T09:34:32.711Z] PermissionError: [Errno 1] Operation not permitted: '/dev/shm'
[2021-07-02T09:34:32.812Z] Worker process started and initialized.
[2021-07-02T09:34:36.731Z] Host lock lease acquired by instance ID '000000000000000000000000DC4198B8'.
```
You'll likely see a few errors/warnings about directories and permissions. At the time of writing this guide, it's safe to ignore these!
Note that two functions are listed as you start debugging mode: `NLPQueue` and `ProcessQueue`. 
These are the names of the **_directories_** which hold the function's code, not the name of the 
**_message queue_** which holds the function's messages. The text after the function name (queueTrigger) 
specifies the _type_ of function it is (defined in `function.json`). In Carrot-Mapper, only queueTriggers are used. 
For more information on Function types, click [here](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview).

With the final console output saying 'Host lock lease acquired', you're ready to run your first Azure Functions job!

### Running an NLP task

The remainder of this guide will show what to expect to see in the debugging console if you run NLP at the field level.

1. Navigate to a field within a scan report.
2. Ensure the field you've chosen has a meaningful description (e.g. "Patient has a cold"). If it doesn't have one, 
click 'Edit Field' and manually put one in.
3. Ensure that `pass_from_source` is set to `True` within the field's settings. This will cause CCOM to process the field only.
4. Click 'Run NLP'
5. After a short wait a green message banner will appear to say NLP is now running.

Hop back to VSCode. After a few seconds, you should see the debugging console updating to say that it has 
received the message from the message queue:
```
Executing 'Functions.NLPQueue' (Reason='New queue message detected on 'nlpqueue-local'.', Id=ea27d6be-eb0b-490c-9cab-82cf2a119355)
Trigger Details: MessageId: f00a438f-9bad-4110-bb8b-5278461d7bb3, DequeueCount: 1, InsertionTime: 2021-07-02T10:57:26.000+00:00
```
If you have configured `.env` and `local.settings.json` correctly, you will see that the job was posted to the queue `nlpqueue-local`.

After some time (around 15-30 seconds), you should notice further updates to the debugging console. Due to various `print()` statements in the init.py file (remember, this holds the function's logic), the console will start logging what's occurring as the function processes the message in the message queue. First, the console shows you what message was sent to the queue:
```
MESSAGE >>>  {'id': 'f00a438f-9bad-4110-bb8b-5278461d7bb3', 'body': '{"documents": [{"language": "en", "id": "17569_field", "text": "patient has a cold"}]}'}
```
After job submission, the `NLPQueue` function works through the stages detailed in [NLP Processing](NlpQueue.md). Having received concept **_codes_** from the NLP API, the function looks up a standard and valid **_conceptID_** using the CCOM API. Sometimes, a concept code will be returned which doesn't have a standard and valid conceptID. In such instances you will see the following in the debugging console:
```
Concept Code C34500 not found!
```
However, if a standard and valid conceptID is found, the Azure function then saves the data to the `ScanReportConcept` model. This is shown to you in the debugging console with the `PAYLOAD` print statement like so:
```
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'ICD10CM', 'nlp_concept_code': 'J00', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'ICD9CM', 'nlp_concept_code': '460', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'SNOMEDCT_US', 'nlp_concept_code': '82272006', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
```
Here, three conceptIDs were found for three different vocabularies (ICD10CM, ICD9CM and SNOWMEDCT_US). Therefore, three records are saved to `ScanReportConcepts` (hence the three `SAVING TO FIELD LEVEL...` statements)

After successful execution, the debug console will finally tell you that the job has finished:
```
Executed 'Functions.NLPQueue' (Succeeded, Id=ea27d6be-eb0b-490c-9cab-82cf2a119355, Duration=30234ms)
```

Here is a full interrupted trace of the above process:
```
Executing 'Functions.NLPQueue' (Reason='New queue message detected on 'nlpqueue-local'.', Id=ea27d6be-eb0b-490c-9cab-82cf2a119355)
Trigger Details: MessageId: f00a438f-9bad-4110-bb8b-5278461d7bb3, DequeueCount: 1, InsertionTime: 2021-07-02T10:57:26.000+00:00
MESSAGE >>>  {'id': 'f00a438f-9bad-4110-bb8b-5278461d7bb3', 'body': '{"documents": [{"language": "en", "id": "17569_field", "text": "patient has a cold"}]}'}
Concept Code C34500 not found!
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'ICD10CM', 'nlp_concept_code': 'J00', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'ICD9CM', 'nlp_concept_code': '460', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
SAVING TO FIELD LEVEL...
PAYLOAD >>> {'nlp_entity': 'cold', 'nlp_entity_type': 'SymptomOrSign', 'nlp_confidence': 0.75, 'nlp_vocabulary': 'SNOMEDCT_US', 'nlp_concept_code': '82272006', 'concept': 260427, 'object_id': '17569', 'content_type': 15}
Executed 'Functions.NLPQueue' (Succeeded, Id=ea27d6be-eb0b-490c-9cab-82cf2a119355, Duration=30234ms)
```
### Stopping Debugging

Debugging can be stopped by clicking:

Run (top menu) -> Stop Debugging