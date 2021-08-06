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


## Common Errors

Below are a list of common errors that are often encountered. 

### `coconnect.tools.file_helpers.MissingInputFiles`

One of the most common errors is due to missing or badly named files.

For example you might see the following error:
```
coconnect.tools.file_helpers.MissingInputFiles: Found the following files ['foo.csv','bar.csv'] in the json file, that are not in the loaded file list... ['Foo.csv','Bar.csv']
```

The message is saying that the tool loaded the files `['Foo.csv','Bar.csv']` but in the `json` file for the rules, it is expecting the files `['foo.csv','bar.csv']`.

This is a common problem due to files being renamed based on what has been used to generate the `json` file.

!!! Danger
    The file names in the `json` file must match the file names, or the files in the folder, you pass as inputs. Otherwise the tool is not going to be able to find or known about that dataset and be able to perform the mapping.

#### When supplying a directory

For the `INPUTS` argument, if you supply a directory, the tool will look in that directory for any file matching the extention `.csv`, if you file names do not match this ending, then the tool wont be able to pick them up and you'll have to supply them individually.


### `json.decoder.JSONDecodeError`

A message along the lines of:
```
json.decoder.JSONDecodeError: Unexpected UTF-8 BOM (decode using utf-8-sig): line 1 column 1 (char 0)
```

Tells you that something is wrong with the input `json` file for your rules.

First you should check that the file supplied with the `--rules` argument is a valid `json` file containing the mapping-rules needed by the tool.

Often the `json.decoder.JSONDecodeError` will tell you what line and column there is an error coming from. Common issues are due to bad formating or accidental insertions of whitespace characters for a text editor, if the file has been opened up and manually edited. 