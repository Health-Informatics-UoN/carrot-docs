The following guide documents how you can use the co-connect command line tools to automate the ETL process for by defining a `yaml` configuration file.


ETL workflows, configured with a `yaml` file are executed with the synthax `coconnect etl --config <path to config file> [optional additional COMMAND]` 

```
coconnect etl --config config.yml --help
```
```
Usage: coconnect etl [OPTIONS] COMMAND [ARGS]...

  Command group for running the full ETL of a dataset

Options:
  --config, --config-file TEXT  specify a yaml configuration file
  -d, --daemon                  run the ETL as a daemon process
  -l, --log-file TEXT           specify the log file to write to
  --help                        Show this message and exit.

Commands:
  check-tables   check tables
  clean-table    clean (delete all rows) of a given table name
  clean-tables   clean (delete all rows) in the tables defined in the...
  create-tables  create new bclink tables
  delete-tables  delete some tables
```


To run the full ETL you need a `.yml`(or `.yaml`) file to configure various settings.

<center>
[Example yamls](https://github.com/CO-CONNECT/co-connect-tools/tree/master/coconnect/data/test/automation){ .md-button .md-button--secondary}
</center>

### Get to the point...

"TL;DR, I want a yaml configuration file that I can run out the box..."



### Transform Tab 
This tab specifies how the data is going to be transformed.

??? "Configuration"
    The following is the most basic example of a configuration, where the rules, input data, output data folder and tables names are specified as such:
	```yaml
    transform:
       data:
         - input: <input data folder>
           output: <output folder location>
		   rules: <rules.json location>
    ```
	When this configuration is executed, a local storage folder is used to write the files, i.e. there is no load performed. Effectively this is just the T in the ETL.
	
	Example:
	```yaml
	transform:
       data:
         - input: /usr/lib/bcos/MyWorkingDirectory/Temp/demo-dataset/data/part1/
           rules: /usr/lib/bcos/MyWorkingDirectory/Temp/demo-dataset/data/rules.json
           output: /usr/lib/bcos/MyWorkingDirectory/Temp/cache/basic/
	```

	Additional valid keyword arguments to the `map` function (which can be found in [in `def map`](/docs/CoConnectTools/CLI/Run/)), can be passed here:
	```yaml
    transform:
       data:
         - input: <input data folder>
           output: < output folder location>
		   rules: < rules.json location>
		   split_outputs: <true/false if to save the outputs as one file e.g. person.tsv or not>
           number_of_rows_to_process: <number of rows of data to run over>
		   dont_automatically_fill_missing_columns: <true/false dont automatically fill missing columns e.g. year_of_birth from birth_datetime>
	```
	
	Multiple input folders can be added:
	```yaml
    transform:
       data:
         - input: < input data folder>
           output: < output folder location>
		   rules: < rules.json location>
	     - input: < another input data folder>
           output: < output folder location>
		   rules: < rules.json location>
	```
	Anchors can be used to reduce the number of repeats:
	```yaml
 	transform:
	   settings: &settings
	     output: < output folder location>
		 rules: < rules.json location>
	   data:
	     - input: < input data folder>
		   <<:*settings
         - input: < another input data folder> 
		   <<:*settings
	```
		
	Alternatively the data tab can be a master/main folder, containing subfolders of multiple data dumps. If specified as follows, the code will use the main folder to look for subfolders to process.
	```yaml
    transform:
       data:
         input: < input main folder>
         output: < output folder location>
		 rules: < rules.json location>
	```
	
### Load Tab 

??? "Local Configuration"
	A very basic example shows how the load tab can be used to do the same as above (load/dump to a local folder i.e. `LocalDataCollection`)
	```yaml
	load: &load-local
       output: < folder for the output>
    transform:
	   settings: &settings
	     output: *load-local
		 rules: < rules.json location>
	   data:
	     - input: < input data folder>
		   <<:*settings
 	```
??? "BCLink Configuration"
    To specify we want to load to BCLink instead, we can create a load tab section as so:
	```yaml
	load: &load-bclink
      cache: /usr/lib/bcos/MyWorkingDirectory/Temp/cache/
	  bclink:
        dry_run: false
	transform:
	   settings: &settings
	     output: *load-bclink
		 rules: < rules.json location>
	   data:
	     - input: < input data folder>
		   <<:*settings
 	
	```
	By specifying that the output should go to the `load-bclink` configuration, the tool will be able to write the output to a `BCLinkDataCollection`.
	
	The default behaviour is to assume that the destination table (e.g. `person` table) should be loaded to a BCLink table which exactly the same name. 
	If this is not the case, you must manually specify the names of the tables so the tool knows which BCLink table it should upload to.
	
	```yaml
	load: &load-bclink
      cache: < cache directory for writing files>
	  bclink:
         tables:
		    <cdm table>: <bclink table>
			...
	
	```
		
	Example:
	```yaml
	load: &load-bclink
      cache: /usr/lib/bcos/MyWorkingDirectory/Temp/cache/
	  bclink:
         tables:
		    person: ds100000
			observation: ds100001
			measurement: ds100003
			.....
	```
	


## Execute as daemon

To run as a background process (more specifically as a "daemon"), that watches for changes in a directory (ideal for data dumps of regularly updated data) the tool can be started with the flag `-d` or `--daemon`.

### Start the ETL from a yaml file

Start the ETL process with the following command:
```
$ coconnect etl bclink --config config.yml execute --daemon
2021-10-13 10:50:03 - Execute - INFO - running as a daemon process, logging to /usr/lib/bcos/OMOP-test-data/tests_06Oct/etl.log
2021-10-13 10:50:03 - Execute - INFO - process_id in <TimeoutPIDLockFile: 'etl.pid' -- 'etl.pid'>
$
```

### Check the logs

The yaml file configures where log messages are saved. For example, you can `tail` the last two lines of the log to see the output:
```
$  tail -20 etl.log
2021-10-13 10:51:40 - bclink_helpers - INFO - ======== SUMMARY ========
2021-10-13 10:51:40 - bclink_helpers - INFO - {
      "person": {
            "bclink_table": "person_001",
            "nrows": "1000"
      },
      "observation": {
            "bclink_table": "observation_001",
            "nrows": "900"
      },
      "condition_occurrence": {
            "bclink_table": "condition_occurrence_001",
            "nrows": "400"
      },
      "measurement": {
            "bclink_table": "measurement_001",
            "nrows": "1000"
      }
}
2021-10-13 10:51:40 - _process_data - INFO - Refreshing /usr/lib/bcos/OMOP-test-data/tests_06Oct/data every 0:00:05 to look for new subfolders....
```

### Find the process ID

By default a (lock) file `etl.pid` is created while the ETL process is running as a background process. The PID (process ID) is saved inside the file, e.g.:
```
$ cat etl.pid 
75107
```

### Kill the daemon

To stop the background process, you can do:
```
kill -9 $(cat etl.pid)
```


## Setup your `yaml` configuration

This section documents additional options and how to configure your `yaml` file.


### Rules **[required]**

* Specify the location of the [transform rules file](/docs/CoConnectTools/ETL/Rules/) provided by the co-connect team

```yaml
...
rules: /usr/lib/bcos/MyWorkingDirectory/rules.json 
...
```

### Input/Output Data **[required]**

* Specify a list of folders (or individual csv files) for where the input data is located   
* Data must be [standardised](/docs/CoConnectTools/ETL/Extract/)
    * manually converted to `csv` format, abiding by [co-connect data standards](https://co-connect.ac.uk/co-connect-data-files-and-meta-data-standardisation/)
    * already pseudonymised
* Specify the output folder of where the `tsv` files created in the transform process will be written to disk before any upload to BCLink.


??? example "Example input data structure"
    ```yaml
	/data/dataset_drops/
	├── 001
	│   ├── Demo.csv
	│   └── Questionnaire.csv
	└── 002
	    └── Questionnaire.csv
    ```


=== "Input Folders"
    If you have (multiple) folder(s) containing the input (csv) files, the should be specified as such:
	```yaml
	...
	data: 
	  - input: /data/dataset_drops/001/
	    output: /data/output/001/
	  - input: /data/dataset_drops/002/
	    output: /data/output/002/
		...
	```
=== "Input Files"
    If you want to specify the full path to files, that may be in different folders, this can be achieved via:
    ```yaml
	...
	data: 
     - input: 
	    - /data/001/Demo.csv
		- /data/002/Questionaire.csv 
       output: /data/output/
    ...
	```

=== "Folder containing subfolders"
    Alternative you can specify the ETL to watch a directory for new data-drops that are contained within subfolders of that folder.
	```yaml
	...
	data: 
	  watch: 
        days: 1
	  input: /data/dataset_drops
      output: /data/output
    ...
    ```


### Clean **[optional]**

* When starting the ETL process from this configuration file:

1. clean all results folders (folders containing the mapped `tsv` files)
2. delete all existing rows in the BCLink tables

```yaml
...
clean: true
..
```

### Log **[optional]**

* Specify the location on the log file created when running the ETL.   
* If not specified, the file is written to `./coconnect.log` (in the working directory of where the command was executed from).   

```yaml
...
log: /usr/lib/bcos/MyWorkingDirectory/coconnect-etl.log
...
```

### Pseudonymisation **[optional]**

* Include an automated pseudonymisation procedure, specifying the output folder location of where to store these files
* A salt hash (provided to you by the co-connect team)

=== "Example #1" 
    Simple configuration for data list:
    ```yaml
	...
	data:
	   - input: ...
	     output: ...
	     pseudonymise: 
		    output: /usr/lib/bcos/MyWorkingDirectory/pseudo_data
			salt: 00ed1234da
	...
    ```
=== "Example #2" 
    When watching a folder for subfolder data dumps
    ```yaml
	...
	data:
	   input: ...
	   pseudonymise: 
		   output: /usr/lib/bcos/MyWorkingDirectory/pseudo_data
		   salt: 00ed1234da
	...
    ```
=== "Example #3" 
    Using a hook/anchor to prevent copy&pasting code
    ```yaml
	rules: rules_update.json
    log: coconnect.log
    pseudonymise: &pseudonymise_opts
       salt: 00ed1234da
       chunksize: 100
       do: true
	data:
	   - input: data/001/
	     output: output/001/ 
		 pseudonymise: 
		   <<: *pseudonymise_opts
		   output: pseudo/001/
	   - input: data/002/
	     output: output/002/ 
		 pseudonymise: 
		   <<: *pseudonymise_opts
		   output: pseudo/002/
	   ...
	...
    ```

### bclink **[optional]**

#### tables
* By default it is assumed the bclink table to be inserted to has an id that is the same as the CDM table name.

* However, you may want to upload to a different table e.g. insert a `person` table into the bclink table `ds10001`. To be able to to this you can specify a map between the CDM table and the BCLink take dataset id.

```yaml
bclink:
   tables:
      person: ds10001
      observation: ds10002
      condition_occurrence: ds10003
      measurement: ds10004
```
#### global ids

Similarly a name of the BCLink table where the lookup between the hashed global identifiers and the integer indentifiers inserted into the person table can be specified to point to a particular table:

```yaml
bclink:
   global_ids: ds10005
```

#### dry-run

* To execute the "load" as a dry-run, you can specify to only `echo` the bclink commands and not insert into `bclink`.

```yaml
bclink:
  dry_run: true
```
