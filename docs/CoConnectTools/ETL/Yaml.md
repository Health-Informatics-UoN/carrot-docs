The following guide documents how you can use the co-connect command line tools to automate the ETL process for co-connect--bc-link by defining a `yaml` file.

!!! caution
    This tool is designed to work with BCLink installed on a CentOS machine, while logged in as the user `bcos_srv` that has permissions to view the input data (optionally already pseudonymised).


ETL workflows, configured with a `yaml` file are executed with the synthax `coconnect etl bclink --config <path to config file> COMMAND` 

Fukk ETL runs with one argument (`--config`) and the command `execute`:
```
[bcos_srv]$ coconnect etl bclink --config config.yml execute --help
Usage: coconnect etl bclink execute [OPTIONS]

  Run the full ETL process for CO-CONNECT integrated with BCLink

Options:
  -d, --daemon  run the ETL as a daemon process
  --help        Show this message and exit.
```


## Execute

To run the full ETL you need a `.yml`(or `.yaml`) file to configure various settings.

<center>
[Example yamls](https://github.com/CO-CONNECT/co-connect-tools/tree/master/coconnect/data/test/automation){ .md-button .md-button--secondary}
</center>

### Template yaml file

"TL;DR, I want a yaml configuration file that I can run out the box..."

=== "Specify Input Folder(s)"
    The following is the most basic example of a configuration, where the rules, input data, output data folder and tables names are specified as such:
	```yaml
	rules: <full path of rules json file>
	data: 
 	  - input: <full path of where input csv files are>
	    output: <full path of output folder for the transformed CDM tsv file>
 	  - input: <full path of a second data dump of csv files>
	    output: <full path of output folder for the second data dump>
	  - ...
	bclink:
	  global_ids: <name of global id subject table in bclink>
	  tables:
	    person: <name of person table in bclink>
	    <other cdm table>: <<name of cdm table in bclink>
    ....
    ```
=== "Watch a folder for subfolders"
    With this configuration the tool will automatically watch the `input` folder for new data dumps.
	```yaml
	rules: <full path of rules json file>
	data: 
	  input: <full path of where input data dumps will be placed>
	  output: <full path of output folder for the transformed CDM tsv file>
	bclink:
	  global_ids: <name of global id subject table in bclink>
	  tables:
	    person: <name of person table in bclink>
	    <other cdm table>: <<name of cdm table in bclink>
    ....
    ```

Edit and save as:
```
config.yml
```


Run with:
```
coconnect etl bclink --config config.yml execute
```

Use the flag `-d` (or `--daemon`) to run this as a daemon background process. [See the docs here for more explaination](/docs/CoConnectTools/ETL/Automation/#run-as-daemon).


### Example yaml file

=== "Simple"
    A simple example `yaml` file that will run the transform on an input folder containing the input `csv` files and uploads them to `bclink` is shown here:
	```yaml
	clean: true
	rules: rules/rules_14June2021.json
	log: automation/log
	data: 
	  - input: inputs/
	    output: automation/results/
	bclink:
      tables:
	    person: person
		observation: observation
		measurement: measurement
		condition_occurrence: condition_occurrence
	```

   !!! note 
       The tab for `bclink:` is not neccessary as the default behaviour is to assume the IDs of the tables in `bclink` match the names of the tables e.g. `person: person`

=== "Watches for new data dumps"
    A full `yaml` example is given below, that watches a folder for subfolders containing data dumps, subsequent sections detail what each setting is doing:
	```yaml
	clean: true
	rules: /usr/lib/bcos/MyWorkingDirectory/rules.json 
	log: /usr/lib/bcos/MyWorkingDirectory/coconnect-etl.log
	data: 
	  watch: 
	    days: 1
      input: /data/dataset_drops
	  output: /usr/lib/bcos/MyWorkingDirectory/output
	  pseudonymise: 
	    output: /usr/lib/bcos/MyWorkingDirectory/pseudo_data
		salt: 00ed1234da
	bclink:
	  tables:
        person: ds10001
        observation: ds10002
        condition_occurrence: ds10003
        measurement: ds10004
      global_ids: ds10005
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
  dry-run: true
```
