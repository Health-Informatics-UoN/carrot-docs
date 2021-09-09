!!! note
    This page is for developers of Azure Functions - not users

## Introduction
'NLP Processing' is the general term for converting Field/Value text strings from Scan Reports to standard and valid OMOP CDM conceptIDs using the Microsoft Health API. For example:
> "The patient has a cough" 

can--via several intermediate stages detailed below--be converted to the valid and standard conceptID of 254761. The NLP service works by picking out what it thinks is the most pertinent information in a string (in this case, the symptom 'cough') and then returning concept codes for the various vocabularies in its database. We then convert concept codes to conceptIDs by looking them up with our deployed version of the OMOP CDM and save the results to the model `ScanReportConcept`.

This page is not intended to be a highly detailed dive into the exact workings of the code. Rather, it is meant to provide a reasonably detailed account of the overall NLP process. Reference is made to key functionality within the code base which can be read if more detail is required.

We are currently using Microsoft Health text API version 3.1 Preview 3.

## Workflow
When a user clicks the 'Run NLP' button, the code performs a number of checks to determine what text string data to send to the NLP service. The final decision on what text to send for processing is determined by a combination of user input and what field/value data is available for analysis. The general rule is: if field/value metadata is available which describes the field/value in detail, then preferentially use the field/value descriptions over field/value names. This is because the NLP service stands a better chance of finding a match if it has more information to work with (up to a point).

![Decision tree for sending text data to the NLP service](https://user-images.githubusercontent.com/27423094/119468717-9c219280-bd3e-11eb-9f18-de72e955bcee.png)
**Figure 1** *Decision tree for sending text data to the NLP Service*

### Decision Tree Workflow

After a user clicks the 'Run NLP' button:

1. Check whether the field is 'Pass From Source'. This is a user-defined flag set at the *field* level. If 'Pass from source' is set to 'Yes' then the code only processes data at the *field* level. In other words, we do not process any values associated with that field. Examples of fields which are typically set to 'pass from source' are continuous variables such as age, height and weight. We do not typically map individual heights, so there is no need to send *n* heights to the NLP service for analysis; we are only interested in mapping the field itself (in this example, 'height').

2. If 'pass from source' is True, we check whether meta data is available for a given field. Here, meta data is defined as supplementary data describing the field. For some fields, the field name itself will be sufficient to attempt mapping with NLP (e.g. Field name of 'Was the patient administered ibuprofen'). However, some datasets have coded fields and the field name itself contains no useful information for NLP to work with. For example, a field name could be 'AH12345'. Such a field name can only be processed if there is some accompanying text describing the field. If metadata is available, we send the *field description*. If meta data is unavailable, we send the *field name*.

By the end of the decision tree, there are two different combinations for *fields*:

- Field description
- Field name

If processing a value (when 'Pass From Source' is set to 'No'):

3. Check whether the value is a 'negative assertion'. Negative assertions are currently set at the level of the scan report and a user must tell the system what values in the scan report are negative values. For example, in Dataset 1, negative values may be 'N' and '0' whereas in Dataset 2, negative values may be 'No' and 'Negative'. At the point, the code skips the value if it has been assigned a negative assertion. If the value is positive then:
4. Check whether a field description is available for that value. If so, use the *field description*. If not, use the *field name*.
5. Check whether a value description is available for that value. If so, use the *value descirption*. If not, use the *value name*. Concatenate the text strings from steps 4 and 5 for the final input into NLP.

By the end of the decision tree, there are four different combinations for *values*:

- Field description + value description
- Field name + value name
- Field description + value name
- Field name + value description

## Sending/recieving data to/from the NLP service

After creating the various combinations of field/value names and descriptions, the data are sent as JSON to the NLP service using a `POST` request via the `requests` library. During testing, the time taken for the NLP service to process requests has varied substantially. Sometimes it can be a few seconds, other times it can take 30-40 seconds to process a single query (perhaps a function of the NLP service still being in preview.)

When attempting to `GET` data back from the NLP service, the code contains a short `while` loop (in the function `get_data_from_nlp`). This is to give the API time to receive the job, queue it and process it. Only when the job status is 'complete' can a successful `GET` request take place. Data are returned as JSON. If successful, results are appended to a list for later conversion to conceptIDs.

Official documentation for the NLP API can be found [here](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health).

## Processing the return from NLP
The functions `process_nlp_response` and `concept_code_to_id` (both in `services_nlp.py`) are used to drill down into the returned JSON, pick out the required data, save it to a list and then look up the conceptID from the concept code. At present, we return concept code data from the following vocabularies: ICD9CM, ICD10CM, RXNORM and SNOMEDCT_US. Note that the NLP service has slightly different names/cases for some vocabularies compared to our working version of the OMOP CDM. For example: NLP = SNOWMEDCT_US/OMOP = SNOMED, NLP = RXNORM/OMOP = RxNorm. These subtle differences are taken into consideration in `get_concept_from_concept_code` in `services.py` where we convert the vocabulary names for a successful concept code lookup.

Because we return data from 4 different vocabularies, it is possible to get four different concept codes for the same input string. For example, the input string "The patient has a fever" could return 4 subtly different concepts (e.g. SNOMED = "fever", ICD9 = "temperature", ICD10 = "high fever", RXNORM = "mild fever"). In such cases, the code saves all 4 concepts back to `ScanReportConcept` - the user must then delete the unwanted concepts. However, where all vocaublaries converge to the same standard and valid conceptID, the app returns only the SNOMED valid and standard conceptID. This functionality is designed to save user input when all concept codes converge to the same conceptID.
