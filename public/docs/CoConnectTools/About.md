
Welcome to our repo for `python` tools used by/with the CO-CONNECT project. The primary functionality is used for performing ETL on health datasets, converting them to the OHDSI Common Data Model (CDM), via a tool referred to as the _ETL Tool_ .

CO-CONNECT-Tools contains a pythonic version of the OHDSI CDM, implemented via the class `CommonDataModel`. CDM tables, such as "Person" are also defined as classes (e.g. [`Person`](/CoConnectTools/Person/)) within the code base. 

!!! note 
    In the default setup a __slightly__ (`visit_detail_id` link has been removed from Measurement, Observation and Condition Occurrence tables) modified CDM version [`5.3.1`](https://github.com/OHDSI/CommonDataModel/releases/tag/v5.3.1) is used to define a subset of tables in python.


The primary purpose of this package is to perform ETL of a dataset based upon a supplied set of transform rules encoded within a json file. [Click here](/CoConnectTools/QuickRun/) to jump to a short walkthrough of the steps necessary to run the tool.
The tools is designed to handle the output json of the CO-CONNECT Mapping-Pipeline web-tool known as CCOM.


[User Guide](/CoConnectTools/Installing/){ .md-button .md-button--primary }
[Developer Guide](/CoConnectTools/CommonDataModel/){ .md-button .md-button--secondary }


## ETL-Tool

Our ETL-Tool runs via a Command Line Interface. It defines and processes a CDM model given input data and given a `json` rules file, the latter which tells code how to map and transform the input data. 
 

### Architecture Overview
![overview](../images/etltool.png)
   
* The ETL-Tool is executed with the command `coconnect map run [arguments]`, where additional arguments pass the paths to a mapping-rules `json` file and input data `csv` files.  
* A new [`CommonDataModel`](/CoConnectTools/CommonDataModel/) is created.  
* [`InputData`](/CoConnectTools/InputData/) is created to handle/chunk the input files and is added to the `CommonDataModel`.  
* The mapping-rules `json` is used to create new [CDM Tables](/CoConnectTools/Common/#coconnect.cdm.objects.common.DestinationTable) (e.g. [Person](/CoConnectTools/Person/)).
    * For each CDM Table, multiple tables can be created. E.g. there may be multiple [Condition Occurrences](/CoConnectTools/ConditionOccurrences/) defined across multiple input data files and columns (fields).  
    * The rules `json` encodes so-called "term-mapping" - how to map raw values into OHDSI concept IDs for the output. These are setup as lambda functions and passed to the object's [`define` function](/CoConnectTools/Common/#coconnect.cdm.objects.common.DestinationTable.define)  
* [Processing](/CommonDataModel/#coconnect.cdm.model.CommonDataModel.process) of the `CommonDataModel` is triggered:   
    * A new chunk of `InputData` is grabbed.   
    * Each CDM table is looped over:  
        * All objects of this CDM table are found and looped over:
             * The `define` function is called to apply the rules.
	     * A new dataframe is retrieved for the object.
	* All retrieved dataframes are merged.   
	* The merged dataframe is formatted and finalised.   
	* The final dataframe is saved to a file.   
    * This is repeated until all chunks are processed.   



### Getting Started

To get started, follow the instructions for installing and running the ETL-Tool on the following pages:
#### Table of Contents
1. [Installing](/CoConnectTools/Installing/)
1. [Running ETL-CDM](/CoConnectTools/ETL-Tool/)
