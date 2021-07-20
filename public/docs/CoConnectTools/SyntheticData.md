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


### CCOM (work-in-progress)

