![images](../../images/ETL.jpeg)


The co-connect ETL process runs via a Command Line Interface, installed with the co-connect-tools package.


{++ETL++} stands for:

* {++Extract++}: input data is extracted into `.csv` format and pseudonymised    
* {++Transform++}: a CDM model is created and processed, given the pseudonymised data and a [`json` transformation rules file](/docs/CoConnectTools/ETL/Rules/) which tells the software how to map and transform the data.    
* {++Load++}: inserts the data into a database or other destination.

Currently, the only fully automated ETL process co-connect-tools can perform is one intergrated with __bclink__ for the __load__ step of the ETL.

```
$ coconnect etl --help
Usage: coconnect etl [OPTIONS] COMMAND [ARGS]...

  Command group for running the full ETL of a dataset

Options:
  --help  Show this message and exit.

Commands:
  bclink  Command group for ETL integration with bclink
```

## CO-CONNECT/BC-LINK

### Architecture Overview
A schematic diagram of the co-connect/bclink ETL is given below:
![overview](../../images/etltool.png)


{== The following breaks down what each part of the process is doing... ==}

### Extract
* Formats the input data to abide by the [co-connect data-standards](https://co-connect.ac.uk/co-connect-data-files-and-meta-data-standardisation/) **[manual]**
* Creates data-dumps of the input datasets in the form of `csv` files for each dataset table. **[manual]**
* Pseudonymises the input datasets, masking any person identifiers or person IDs **[optionally automated]**

### Transform
* The transform mapping is executed with the command `coconnect map run [arguments]`, where additional arguments pass the paths to a mapping-rules [`json` file](/docs/CoConnectTools/ETL/Rules/) and input data `csv` files **[optionally automated]**:

    * A new pythonic [`CommonDataModel`](/docs/CoConnectTools/CommonDataModel/) is created.   
    * [`InputData`](/docs/CoConnectTools/InputData/) is created to handle/chunk the input files and is added to the `CommonDataModel`.  
    * The mapping-rules `json` is used to create new [CDM Tables](/docs/CoConnectTools/Common/#coconnect.cdm.objects.common.DestinationTable) (e.g. [Person](/docs/CoConnectTools/Person/)).
        * For each CDM Table, multiple tables can be created. E.g. there may be multiple [Condition Occurrences](/docs/CoConnectTools/ConditionOccurrences/) defined across multiple input data files and columns (fields).  
        * The rules `json` encodes so-called "term-mapping" - how to map raw values into OHDSI concept IDs for the output. These are setup as lambda functions and passed to the object's [`define` function](/docs/CoConnectTools/Common/#coconnect.cdm.objects.common.DestinationTable.define)  
    * [Processing](/docs/CoConnectTools/CommonDataModel/#coconnect.cdm.model.CommonDataModel.process) of the `CommonDataModel` is triggered:   
        * A new chunk of `InputData` is grabbed.   
        * Each CDM table is looped over:  
            * All objects of this CDM table are found and looped over:
                 * The `define` function is called to apply the rules.
                 * A new dataframe is retrieved for the object.
	    * All retrieved dataframes are merged.   
	    * The merged dataframe is has a formatting check applied
	    * The merged dataframe primary keys are correctly indexed
	    * The final dataframe is saved/appended to an output `tsv` file.   
        * This is repeated until all chunks are processed.   

### Loads
   * Uploads the output `tsv` files into the bclink job-queue which formats and uploads these into the bclink database **[optionally automated]**

