
## Common Errors

Below are a list of common errors

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

### `json.decoder.JSONDecodeError`

A message along the lines of:
```
json.decoder.JSONDecodeError: Unexpected UTF-8 BOM (decode using utf-8-sig): line 1 column 1 (char 0)
```

Tells you that something is wrong with the input `json` file for your rules.

First you should check that the file supplied with the `--rules` argument is a valid `json` file containing the mapping-rules needed by the tool.

Often the `json.decoder.JSONDecodeError` will tell you what line and column there is an error coming from. Common issues are due to bad formating or accidental insertions of whitespace characters for a text editor, if the file has been opened up and manually edited. 