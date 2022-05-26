Generating synthetic data can be performed via WhiteRabbit, by hand or via the use of co-connect-tools.


## CLI for generating synthetic data

Synthetic data can be generated from the CCOM website or from the original `xlsx` ScanReport file.

```
$ coconnect generate synthetic --help
Usage: coconnect generate synthetic [OPTIONS] COMMAND [ARGS]...

  Commands to generate synthetic data.

Options:
  --help  Show this message and exit.

Commands:
  ccom  generate synthetic data from a ScanReport ID from CCOM
  xlsx  generate synthetic data from a ScanReport xlsx file
  
```

### XLSX

If you have the original WhiteRabbit scan report locally, you can use this command to generate the synthetic data.

```
$ coconnect generate synthetic xlsx --help
Usage: coconnect generate synthetic xlsx [OPTIONS] REPORT

  generate synthetic data from a ScanReport xlsx file

Options:
  -n, --number-of-events INTEGER  number of rows to generate  [required]
  -o, --output-directory TEXT     folder to save the synthetic data to
                                  [required]
  --fill-column-with-values TEXT  select columns to fill values for
  --help                          Show this message and exit.
```

!!! example
    The following command with use a local scan report (`ScanReport.xlsx`) to generate `1000` synthetic data events for each table in the report, it will fill all columns labelled `ID` with incrementing values (this is a feature to mimic genuine IDs) and put the output into a folder `synthetic_data`.
    ```
    coconnect generate synthetic xlsx --fill-column-with-values ID -n 1000 -o synthetic_data ScanReport.xlsx
    ```


### CCOM 

If you cannot obtain the original WhiteRabbit scan report, you can also generate synthetic data using our CCOM website by connecting via the [api](/docs/MappingPipeline/API/) to the database.

```
$ coconnect generate synthetic ccom --help
Usage: coconnect generate synthetic ccom [OPTIONS]

  generate synthetic data from a ScanReport ID from CCOM

Options:
  -i, --report-id INTEGER         ScanReport ID on the website  [required]
  -n, --number-of-events INTEGER  number of rows to generate  [required]
  -o, --output-directory TEXT     folder to save the synthetic data to
                                  [required]
  --fill-column-with-values TEXT  select columns to fill values for
  -t, --token TEXT                specify the coconnect_token for accessing
                                  the CCOM website
  -u, --url TEXT                  url endpoint for the CCOM website to ping
  --help                          Show this message and exit.
```

!!! warning
    To be able to use this feature you need to have an access token to be able to connect to the database. If you are using this locally, you can supply `--url http://localhost:8080` and via `http://localhost:8080/admin` you can generate a token to be used. 


!!! example
    This feature can be used to also generate `1000` events for the [scan report table (ID: 106)](https://ccom.azurewebsites.net/tables/?search=106):
    ```
    coconnect generate synthetic ccom -i 106 -n 1000 -o synthetic_data --token <hashed token>
    ```
