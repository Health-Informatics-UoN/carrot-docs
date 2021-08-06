Below are a list of common errors that are often encountered. 

## `coconnect.tools.file_helpers.MissingInputFiles`

One of the most common errors is due to missing or badly named files.

For example you might see the following error:
```
coconnect.tools.file_helpers.MissingInputFiles: Found the following files ['foo.csv','bar.csv'] in the json file, that are not in the loaded file list... ['Foo.csv','Bar.csv']
```

The message is saying that the tool loaded the files `['Foo.csv','Bar.csv']` but in the `json` file for the rules, it is expecting the files `['foo.csv','bar.csv']`.

This is a common problem due to files being renamed based on what has been used to generate the `json` file.

!!! Danger
    The file names in the `json` file must match the file names, or the files in the folder, you pass as inputs. Otherwise the tool is not going to be able to find or known about that dataset and be able to perform the mapping.

### When supplying a directory

For the `INPUTS` argument, if you supply a directory, the tool will look in that directory for any file matching the extention `.csv`, if you file names do not match this ending, then the tool wont be able to pick them up and you'll have to supply them individually.


## `json.decoder.JSONDecodeError`

A message along the lines of:
```
json.decoder.JSONDecodeError: Unexpected UTF-8 BOM (decode using utf-8-sig): line 1 column 1 (char 0)
```

Tells you that something is wrong with the input `json` file for your rules.

First you should check that the file supplied with the `--rules` argument is a valid `json` file containing the mapping-rules needed by the tool.

Often the `json.decoder.JSONDecodeError` will tell you what line and column there is an error coming from. Common issues are due to bad formating or accidental insertions of whitespace characters for a text editor, if the file has been opened up and manually edited.


## Requiring non-null values in <destination_field> removed X rows, leaving Y rows.

This may come as a `WARNING` or `ERROR` message in your logs.
```
WARNING - Requiring non-null values in <destination_field> removed X rows, leaving Y rows.
ERROR - Requiring non-null values in <destination_field> removed X rows, leaving Y rows
```

This is an `ERROR` message if `Y` is 0. In that instances, if `X` is small then it's not likely to be a genuine problem. You want to watch out for if `X>>Y` and `Y = 0`, this indicates that the mapping of the `<destination_field>` is failing.

!!! example
    ```
    person_0 - WARNING - Requiring non-null values in gender_concept_id removed 4 rows, leaving 6 rows.
    ```
    You are most likely to see two person objects in the rules `json` file, as males and females are mapped separately. I.e. the `source_value` (e.g. 'M' or 'F') is mapped to the `concept_id` for male and female separately. 

    This `WARNING` message is saying that for 10 rows of data (10 people), 4 rows were removed because the gender_concept_id did not map to anything. The most likely explanation for this is that `person_0` is mapping only one sex (e.g females) and therefore drops all rows for the other (e.g. males).

    There is most likely a `person_1` object which will be the other sex, and you should see that it maps the other 6 people (and removes 4 people instead):
    ```
    person_1 - WARNING - Requiring non-null values in gender_concept_id removed 6 rows, leaving 4 rows.
    ```
    In the output file produced for the `person` table, which is a merge of `person_0` and `person_1`, you'll see there are 10 rows of people.

