### What is the ETL-Tool?

Our command line interface tool for performing a transformation of your dataset into a Common Data Model (CDM):
```
coconnect map run
```

[Overview](/CoConnectTools/About/#etl-tool){ .md-button .md-button--primary }
[Walkthrough](/CoConnectTools/ETL-Tool/){ .md-button .md-button--secondary }


### What is the rules `.json`?

A `json` encoded file that contains information of how multiple CDM tables and CDM objects need to be created by the ETL-Tool


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
