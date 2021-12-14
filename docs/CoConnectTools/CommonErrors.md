Below are a list of common errors that are often encountered. 

## `ERROR: No matching distribution found for co-connect-tools`

When installing co-connect-tools you see messages like this:
```
$ pip install co-connect-tools
Collecting co-connect-tools
  WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'NewConnectionError('<pip._vendor.urllib3.connection.VerifiedHTTPSConnection object at 0x10d7c39a0>: Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known')': /simple/co-connect-tools/
...
...
ERROR: Could not find a version that satisfies the requirement co-connect-tools (from versions: none)
ERROR: No matching distribution found for co-connect-tools
```

The most common cause of this error is a lack of internet connection or lack of connection to pypi.org

Check this via the `ping` command, which should give you a response, e.g.:
```
$ ping pypi.org
PING pypi.org (151.101.192.223): 56 data bytes
64 bytes from 151.101.192.223: icmp_seq=0 ttl=58 time=20.124 ms
```
If you see
```
$ ping pypi.org
ping: cannot resolve pypi.org: Unknown host
```
This means your machine cannot establish a connection to `pypi` to download and install the tool. 


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

## coconnect.cdm.objects.common.FormattingError

This is an error message telling you that something in your input data is not in the right format for the tool to handle and format. 

!!! example
    ```
    coconnect.cdm.objects.common.FormattingError: The column person_id using the formatter function Integer produced NaN values in a required column
    ```

In the above example, this error message means that while trying to format the `person_id` column into a Integer, every single value failed, meaning the column in the input data that is used for the `person_id` is not in a format that can be converted into an Integer.

Inspecting further you may see something like:
```
2021-08-06 16:55:23 - person_0 - ERROR - Something wrong with the formatting of the field 'person_id'.
2021-08-06 16:55:23 - person_0 - ERROR - Using the formatter 'Integer' failed on all values.
2021-08-06 16:55:23 - person_0 - INFO - Sample of this column before formatting:
2021-08-06 16:55:23 - person_0 - ERROR - index
s4    s4
d2    d2
s3    s3
s1    s1
Name: person_id, dtype: object
```
Which shows that indeed the `person_id` column contains strings and was not able to be convert to an Integer.

!!! note
    By default, the `person_id` column is expected to already be an integer. 

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


