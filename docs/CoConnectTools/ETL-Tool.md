This section describes how CO-CONNECT-Tools is used to perform ETL (Extract, Transform, Load) on a dataset (series of input `.csv` files) and a set of transform rules (defined in a `json` file).

The following guide will take you through the main steps to make sure the tool is installed correctly and that the ETL is performed correctly.

For those more familiar with the CO-CONNECT-Tools ETL, [a Quick Run guide can be found here](/docs/CoConnectTools/QuickRun/)

### 1. Checking The Package

To verify the package is installed you can test the following information commands:

* show the tool version (this may take a few seconds, the first time you run the package)

```
coconnect info version
```

* show the location of the installation folder  

```
coconnect info install_folder
```


### 2. Gather Inputs

To run the transformation to CDM you will need:   

1. Input Data, in the form of `csv` files
1. `json` file containing the "mapping rules"


### 3. Check Inputs

Input data is expected in `csv` format, 

It is possible to do a quick check to display the first 10 rows of an input `csv`.
Run:
```
coconnect display dataframe --head 10 <input data csv file> 
```
!!! example
    === "Unix Users"
        A test dataset is located in the install folder (`coconnect info install_folder`)
        ```
        coconnect display dataframe --head 10 $(coconnect info install_folder)/data/test/inputs/Demographics.csv
        ```

    === "Windows Users"
        A test dataset is located in the install folder (`coconnect info install_folder`), by listing this directory (`dir`) you can find the full path to an example file to test inplace of `<input data csv file>`
	
With a `json` file for the rules, you can quickly check the tool is able to read and display them via:
```
coconnect display json rules.json
```

!!! example
    === "Unix Users"
        As with the input `csv` files, the test dataset comes packaged with a rules `json` file.
        ```
        coconnect display json  $(coconnect info install_folder)/data/test/rules/rules_14June2021.json
        ```   
    === "Windows Users"
        As with the input `csv` files, the test dataset comes packaged with a rules `json` file, which can be found via the folder `coconnect info install_folder` followed by `\data\test\rules\`.




### 4. Run The Tool

The synthax for running the tool can be seen from using `--help`:
```
coconnect map run --help
Usage: coconnect map run [OPTIONS] INPUTS...

  Perform OMOP Mapping given an json file and a series of input files

  INPUTS should be a space separated list of individual input files or
  directories (which contain .csv files)

Options:
  --rules TEXT                    input json file containing all the mapping
                                  rules to be applied  [required]
  --csv-separator [;|:|	|,| ]     choose a separator to use when dumping
                                  output csv files
  --use-profiler                  turn on saving statistics for profiling CPU
                                  and memory usage
  --output-folder TEXT            define the output folder where to dump csv
                                  files to
  -nc, --number-of-rows-per-chunk TEXT
                                  Choose the number of rows (INTEGER) of input
                                  data to load (chunksize). The default 'auto'
                                  will work out the ideal chunksize. Inputing
                                  a value <=0 will turn off data chunking.
  -np, --number-of-rows-to-process INTEGER
                                  the total number of rows to process
  -l, --log-level [0|1|2|3]       change the level for log messaging. 0 -
                                  ERROR, 1 - WARNING, 2 - INFO (default), 3 -
                                  DEBUG
  --help                          Show this message and exit.
```

The tool ==requires== you to pass a `.json` file for the rules, as well as space separated list of `.csv` files 

```
coconnect map run --rules <.json file for rules> <csv file 1> <csv file 2> <csv file 3> ...
```


!!! example

    === "Unix Users"
    
        For macOS/Ubuntu/Centos etc. users, you can easily run from the CLI with a wildcard. Assuming your input data is located in the folder `data/` you can run:

        ``` bash
    	coconnect map run --rules $(coconnect info install_folder)/data/test/rules/rules_14June2021.json  $(coconnect info install_folder)/data/test/inputs/*.csv
        ```

    	The tool has the capability to also run on a folder containing the `.csv` files. The tool will look in the folder for `.csv` files and load them:

        ``` bash
        coconnect map run --rules $(coconnect info install_folder)/data/test/rules/rules_14June2021.json  $(coconnect info install_folder)/data/test/inputs/
        ```


    === "Windows Users"
    
        For Windows users, you can run by passing the full-path to the folder containing your `.csv` input files.


        ``` 
    	coconnect map run --rules rules.json D:\Foo\Bar\data
        ```

        Or by manually passing the individual input csv files:
        
        ``` 
        coconnect map run --rules rules.json D:\Foo\Bar\data\file_1.csv D:\Foo\Bar\data\file_2.csv
        ```

        Wildcards for inputs ....
        {== THIS NEEDS INSTRUCTIONS FOR WINDOWS USERS ==}
	
### 5. Check The Output

By default, mapped `tsv` files are created in the folder `output_data` within your current working directory.
!!! tip
    To specify a different output folder, use the command line argument `--output-folder` when running `coconnect map run`

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
coconnect display dataframe --drop-na output_data/condition_occurrence.tsv
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
coconnect display dataframe --markdown --drop-na output_data/person.tsv
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
2021-07-27 10:31:48 - CommonDataModel - INFO - Writen the memory/cpu statistics to /Users/calummacdonald/Usher/CO-CONNECT/Software/co-connect-tools/alspac/output_data//logs//statistics_2021-07-27T093143.csv
```