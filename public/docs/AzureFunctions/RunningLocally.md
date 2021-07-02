## Introduction

Running Azure Functions locally is a little involved; this guide has been written to get you up and running with Azure CLI and Functions. Note that this guide assumes you're using VSCode. All of the functionality described here can be replicated outside of VSCode (in Azure CLI) but instructions for doing so are not detailed in this guide.

To follow this guide you need to install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## Azure Functions Basics

Conceptually, Azure Functions are simple to follow. A message is posted to a message queue. An Azure Function then executes on messages within a message queue. In Co-Connect, a message is posted to a message queue as JSON. Here's an example message destined for the `scanreports` queue:

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

An [Azure function](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) is made up of three key files. Two are kept in the function's directory (in Co-Connect's root):

1. init.py - This contains the function's logic. The method `main()` should be present for the function to execute.
2. function.json - This tells the Azure what type of function you're developing and also what queue to send it to

Another file is kept outside of the function's directory and should not be commited to Git:

1. local.settings.json - This file contains environment variables for your function. This file must be present to run Azure functions locally. Contact a member of the team to get all keys and connection strings for this file. For reference, a local.settings.json file looks like this:

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

### Azure Functions in Co-Connect

As of July 2021, the Co-Connect project has two different Azure Functions: one for processing scan reports, another to run the NLP service. The code for these functions live in the directories ProcessQueue and NLPQueue, respectively.

## Azure Functions Queues

For each process (NLP and Scan Reports), we have two queues each - a 'local' queue and a 'live' queue (for a total of 4 unique queues).

Local queues:
```
NLP_QUEUE_NAME=nlpqueue-local
SCAN_REPORT_QUEUE_NAME=scanreports-local
```

Deployed queues:
```
NLP_QUEUE_NAME=nlpqueue
SCAN_REPORT_QUEUE_NAME=scanreports
```

The purpose of the local queues is that, when developing locally, messages are sent only to the 'local' queue. This stops 'development' messages from posting to the 'live' message queue, causing confusion.

These environment variables must be maintained in the following locations:

1. local.settings.json - A local file to hold Azure Function environment variables. Should point to local. Not tracked on Git!
2. Within the Azure Function App. Env Vars need to be set within the Azure Portal to the 'live' vars. Speak to Sam Cox about adding vars on the Azure Portal.
3. Within the CCOM webapp, the .env file must include variables for 'live'

More on how exactly these queues are used below.


## Running Azure Functions Locally

Because Azure Functions are cloud-based, it's somewhat of a misnomer to talk of running an Azure Function completely 'locally'. In reality, you're still posting to a message queue in the cloud, even when developing locally. However, Azure CLI--when running in debug mode (more on this later)--will 'hijack' the messages in the message queue and allow you to process them with the code you're developing locally.

