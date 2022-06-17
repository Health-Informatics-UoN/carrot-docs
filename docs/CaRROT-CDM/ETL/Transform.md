This section describes how CO-CONNECT-Tools can be used to manually perform the Transform part of the co-connect ETL (Extract, Transform, Load). By transforming (mapping) a dataset (series of input `.csv` files) given a set of rules defined in a `json` file.

The following guide will take you through the main steps to make sure the tool is installed correctly and that the ETL is performed correctly.

!!! note
	Running the transform part of the ETL process with `carrot run map OPTIONS` instead of `carrot etl --config <file>` gives you a lot more control over the command line options, although all options can be parsed as key-word-arguments in the config `yaml` used with the `carrot etl`


### 1. Install the tool

Follow the install guide here:

[Install Guide](/docs/CaRROT-CDM/Installing/){ .md-button .md-button--primary }

### 2. Gather Inputs

To run the transformation to CDM you will need:   

1. [Input Data](/docs/CaRROT-CDM/ETL/Extract/), in the form of `csv` files
1. [`json`](/docs/CaRROT-CDM/ETL/Rules/) file containing the "mapping rules"


### 3. Check Inputs

Input data is expected in `csv` format, 

It is possible to do a quick check to display the first 10 rows of an input `csv`.
Run:
```
carrot display dataframe --head 10 <input data csv file> 
```
!!! example
    === "Unix Users"
        A test dataset is located in the install folder (`carrot info install_folder`)
        ```
        carrot display dataframe --head 10 $(carrot info install_folder)/data/test/inputs/Demographics.csv
        ```

    === "Windows Users"
        A test dataset is located in the install folder (`carrot info install_folder`), by listing this directory (`dir`) you can find the full path to an example file to test inplace of `<input data csv file>`
	
With a `json` file for the rules, you can quickly check the tool is able to read and display them via:
```
carrot display json rules.json
```

!!! example
    === "Unix Users"
        As with the input `csv` files, the test dataset comes packaged with a rules `json` file.
        ```
        carrot display json  $(carrot info install_folder)/data/test/rules/rules_14June2021.json
        ```   
    === "Windows Users"
        As with the input `csv` files, the test dataset comes packaged with a rules `json` file, which can be found via the folder `carrot info install_folder` followed by `\data\test\rules\`.




### 4. Run The Tool

The synthax for running the tool can be seen from using `--help`:
```
carrot run map --help
```
```
Usage: carrot run map [OPTIONS] [INPUTS]...

  Perform OMOP Mapping given an json file and a series of input files

  INPUTS should be a space separated list of individual input files or
  directories (which contain .csv files)

Options:
  --rules TEXT                    input json file containing all the mapping
                                  rules to be applied  [required]
  --indexing-conf TEXT            configuration file to specify how to start
                                  the indexing
  --csv-separator [;|:|	|,| ]     choose a separator to use when dumping
                                  output csv files
  --use-profiler                  turn on saving statistics for profiling CPU
                                  and memory usage
  --format-level [0|1|2]          Choose the level of formatting to apply on
                                  the output data. 0 - no formatting. 1 -
                                  automatic formatting. 2 (default) - check
                                  formatting (will crash if input data is not
                                  already formatted).
  --output-folder TEXT            define the output folder where to dump csv
                                  files to
  --write-mode [w|a]              force the write-mode on existing files
  --split-outputs                 force the output files to be split into
                                  separate files
  --allow-missing-data            don't crash if there is data tables in rules
                                  file that hasnt been loaded
  --database TEXT                 define the output database where to insert
                                  data into
  -nc, --number-of-rows-per-chunk INTEGER
                                  Choose the number of rows (INTEGER) of input
                                  data to load (chunksize). The option 'auto'
                                  will work out the ideal chunksize. Inputing
                                  a value <=0 will turn off data chunking
                                  (default behaviour).
  -np, --number-of-rows-to-process INTEGER
                                  the total number of rows to process
  --person-id-map TEXT            pass the location of a file containing
                                  existing masked person_ids
  --db TEXT                       instead, pass a connection string to a db
  --merge-output                  merge the output into one file
  --parse-original-person-id      turn off automatic conversion (creation) of
                                  person_id to (as) Integer
  --no-fill-missing-columns       Turn off automatically filling missing CDM
                                  columns
  --log-file TEXT                 specify a path for a log file
  --max-rules INTEGER             maximum number of rules to process
  --object TEXT                   give a list of objects by name to process
  --table TEXT                    give a list of tables by name to process
  --help                          Show this message and exit.
```

The tool ==requires== you to pass a `.json` file for the rules, as well as space separated list of `.csv` files 

```
carrot run map --rules <.json file for rules> <csv file 1> <csv file 2> <csv file 3> ...
```


!!! example

    === "Unix Users"
    
        For macOS/Ubuntu/Centos etc. users, you can easily run from the CLI with a wildcard. Assuming your input data is located in the folder `data/` you can run:

        ``` bash
    	carrot run map --rules $(carrot info install_folder)/data/test/rules/rules_14June2021.json  $(carrot info install_folder)/data/test/inputs/*.csv
        ```

    	The tool has the capability to also run on a folder containing the `.csv` files. The tool will look in the folder for `.csv` files and load them:

        ``` bash
        carrot run map --rules $(carrot info install_folder)/data/test/rules/rules_14June2021.json  $(carrot info install_folder)/data/test/inputs/
        ```


    === "Windows Users"
    
        For Windows users, you can run by passing the full-path to the folder containing your `.csv` input files.


        ``` 
    	carrot run map --rules rules.json D:\Foo\Bar\data
        ```

        Or by manually passing the individual input csv files:
        
        ``` 
        carrot run map --rules rules.json D:\Foo\Bar\data\file_1.csv D:\Foo\Bar\data\file_2.csv
        ```

        Wildcards for inputs ....
        {== THIS NEEDS INSTRUCTIONS FOR WINDOWS USERS ==}
	
### 5. Check The Output

By default, mapped `tsv` files are created in the folder `output_data` within your current working directory.
!!! tip
    To specify a different output folder, use the command line argument `--output-folder` when running `carrot map run`

Log files are also created in a subdirectory of the output folder, for example:
```
output_data/
├── condition_occurrence.tsv
├── logs
│   └── 2021-09-13T101629_slice_0.json
├── observation.tsv
└── person.tsv
```

Other than opening up the output csv in your favourite viewer, you can also use the command line tools to display a simple dataframe
```
carrot display dataframe --drop-na output_data/condition_occurrence.tsv
```
```
       condition_occurrence_id  person_id  condition_concept_id  ... condition_end_datetime condition_source_value  condition_source_concept_id
0                            1          9                312437  ...    2020-04-10 00:00:00                      1                       312437
1                            2         18                312437  ...    2020-04-11 00:00:00                      1                       312437
2                            3         28                312437  ...    2020-04-10 00:00:00                      1                       312437
3                            4         38                312437  ...    2020-04-10 00:00:00                      1                       312437
4                            5         44                312437  ...    2020-04-10 00:00:00                      1                       312437
```


Markdown format can be outputed for convenience too:
```
carrot display dataframe --markdown --drop-na output_data/person.tsv
```

|    |   person_id |   gender_concept_id | birth_datetime      | gender_source_value   |   gender_source_concept_id |
|---:|------------:|--------------------:|:--------------------|:----------------------|---------------------------:|
|  0 |         101 |                8507 | 1951-12-25 00:00:00 | M                     |                       8507 |
|  1 |         102 |                8507 | 1981-11-19 00:00:00 | M                     |                       8507 |
|  2 |         103 |                8532 | 1997-05-11 00:00:00 | F                     |                       8532 |
|  3 |         104 |                8532 | 1975-06-07 00:00:00 | F                     |                       8532 |
|  4 |         105 |                8532 | 1976-04-23 00:00:00 | F                     |                       8532 |
|  5 |         106 |                8507 | 1966-09-29 00:00:00 | M                     |                       8507 |
|  6 |         107 |                8532 | 1956-11-12 00:00:00 | F                     |                       8532 |
|  7 |         108 |                8507 | 1985-03-01 00:00:00 | M                     |                       8507 |
|  8 |         109 |                8532 | 1950-10-31 00:00:00 | F                     |                       8532 |
|  9 |         110 |                8532 | 1993-09-07 00:00:00 | F                     |                       8532 |

!!! note
    The argument `--drop-na` removes columns that are entirely `nan`/`null` in order to get a better display of the mapped CDM table, this is because many of the CDM table columns are not used in the examples.

### 6. Additional Options

To override the default data chunking options and process a dataset in specific chunks, you can use the following flag:

```
  -nc, --number-of-rows-per-chunk INTEGER
                                  choose to chunk running the data into nrows
```

For a dataset of order millions of records, it is advised that you run with `-nc 100000` which will process the data in 100k row chunks. 



For testing or debugging purposes, especially if you are transforming a large dataset, you can use the option `-np` to only run on a certain number of initial rows of the input `csv` files. 
```
  -np, --number-of-rows-to-process INTEGER
                                  the total number of rows to process
```



If you wish to profile the CPU/memory usage as a function of time, you can run with the following flag:
```
  --use-profiler                  turn on saving statistics for profiling CPU
                                  and memory usage
```

Which will additionally save and output a time series from the start of executing to the end of executing the ETL.
```
2021-07-27 10:31:48 - Profiler - INFO -      time[s]  memory[GB]  cpu[%]
0   0.000384    0.056290   0.000
1   0.104976    0.057865  24.650
2   0.205735    0.058556  23.325
3   0.308194    0.060932  24.625
4   0.415116    0.061394  24.650
...
2021-07-27 10:31:48 - Profiler - INFO - finished profiling
2021-07-27 10:31:48 - CommonDataModel - INFO - Writen the memory/cpu statistics to /Users/calummacdonald/Usher/CO-CONNECT/Software/carrot-cdm/alspac/output_data//logs//statistics_2021-07-27T093143.csv
```
