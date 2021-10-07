## ETL

### What is the co-connect ETL?

Extract, transform and load of your dataset into a Common Data Model (CDM) format that is loaded into bclink.

Our command line tool has the ability to do the full process:
```
coconnect etl bclink --help
```

[Overview](/docs/CoConnectTools/ETL/About/){ .md-button .md-button--primary }
[Setting up automation](/docs/CoConnectTools/ETL/Automation/){ .md-button .md-button--secondary }


Otherwise, you have to do each step manually:

[Extract](/docs/CoConnectTools/ETL/Extract){ .md-button .md-button--primary }
[Transform](/docs/CoConnectTools/ETL/Transform){ .md-button .md-button--primary }
[Load](/docs/CoConnectTools/ETL/Load){ .md-button .md-button--primary }


### How can I pseudonymise my data?

co-connect-tools comes with the command line command:

```
$ coconnect pseudonymise --help
Usage: coconnect pseudonymise [OPTIONS] INPUT

  Command to help pseudonymise data.

Options:
  -s, --salt TEXT             salt hash  [required]
  -i, --person-id, --id TEXT  name of the person_id  [required]
  -o, --output-folder TEXT    path of the output folder  [required]
  --chunksize INTEGER         set the chunksize when loading data
  --help                      Show this message and exit.
```

A detailed guide on how to use this feature can be found here:

[Pseudonymisation guide](/docs/CoConnectTools/ETL/Pseudonymisation/){ .md-button .md-button--primary }


### Can I pseudonymise the data myself?

Yes. You do not need to use the tool packaged in co-connect-tools, you can do this process yourself to create a masked for the person (study) identifiers in your dataset.

## Transforming (mapping) a dataset

### What is the transform tool?

Our command line interface tool for performing only the 'T' part of the 'ETL' process.. a transformation of your dataset into a Common Data Model (CDM):
```
coconnect map run --help
```

[Overview](/docs/CoConnectTools/ETL/About/#transform){ .md-button .md-button--primary }
[Manual from the Command Line](/docs/CoConnectTools/ETL/Transform/){ .md-button .md-button--secondary }
[Manual from a GUI](/docs/CoConnectTools/ETL/Transform-GUI/){ .md-button .md-button--secondary }


This can be ran manually, or is executed as part of the automated ETL.


### What is the rules `.json`?

A `json` encoded file that contains information of how multiple CDM tables and CDM objects need to be created by the [transform process](/docs/CoConnectTools/ETL/Transform/)


### What should my input files be called?

The rules `.json` file can be called anything.

Your input files need to be `csv` files. If the mapping instructions have been followed correctly, the names encoded in the WhiteRabbit scan report should match the names of your input files, and therefore match the names in the `.json` file.

The helper command:
```
coconnect display json <rules file> --list-tables
```
can be used to display what input file names the tool will be trying to load. 

!!! example
    ```
    coconnect display json $(coconnect info data_folder)/test/rules/rules_14June2021.json --list-tables
    ```
    ```
      [
         "Demographics.csv",
         "Symptoms.csv"
      ]
    ```
!!! tip
    If these names differ from the names of your input files, you'll have to rename the files or manually edit the `.json` files and replace all occurrences of a bad file name with the correct file name.


### What should I pass as `INPUTS`?

You need to pass a space separated list of files (or directories containing `.csv` files). 

The rules `.json` file is supplied via the option `--rules `.

The `--help` message displays the following syntax for how the ETL-Tool should be run:

```
Usage: coconnect map run [OPTIONS] INPUTS...

  Perform OMOP Mapping given an json file and a series of input files

  INPUTS should be a space separated list of individual input files or
  directories (which contain .csv files)

Options:
  --rules TEXT                    input json file containing all the mapping
                                  rules to be applied  [required]
```
