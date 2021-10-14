The following guide documents how you can use the co-connect command line tools to automate the ETL process for co-connect+BC-Link.

!!! caution
    This tool is designed to work with BCLink installed on a CentOS machine, while logged in as the user `bcos_srv` that has permissions to view the input data (optionally already pseudonymised).


Many commands are available to use for the bclink integration, requiring a `yaml` configuration file.
```
$ coconnect etl bclink --help
Usage: coconnect etl bclink [OPTIONS] COMMAND [ARGS]...

  Command group for ETL integration with bclink

Options:
  -f, --force                   Force running of this, useful for development
                                purposes
  --config, --config-file TEXT  specify a yaml configuration file  [required]
  --help                        Show this message and exit.

Commands:
  check_tables   check the bclink tables
  clean_tables   clean (delete all rows) in the bclink tables defined in...
  create_tables  crate new bclink tables
  execute        Run the full ETL process for CO-CONNECT integrated with...
```

All can be executed with the synthax `coconnect etl bclink --config <path to config file> COMMAND` 

To actually execute the ETL runs with one argument, a `yaml` configuration file via the command `execute`:

[bcos_srv]$ coconnect etl bclink --config config.yml execute --help
Usage: coconnect etl bclink execute [OPTIONS]

  Run the full ETL process for CO-CONNECT integrated with BCLink

Options:
  -d, --daemon  run the ETL as a daemon process
  --help        Show this message and exit.
```


## Run 

To run the full ETL you need a `.yml`(or `.yaml`) file to configure various settings.

[Example yamls](https://github.com/CO-CONNECT/co-connect-tools/tree/master/coconnect/data/test/automation){ .md-button .md-button--secondary}


### Template yaml file

"TL;DR, I want a yaml configuration file that I can run out the box, that will watch a folder for my data dumps"


```yaml
clean: true
rules: <full path of rules json file>
log: <full path of where to save a log file>
data: 
   input: <full path of where input data dumps will be saved>
   output: <full path of output folder for the transformed CDM tsv file>
bclink:
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

A simple example `yaml` file that will run the transform on an input folder containing the input `csv` files and uploads them to `bclink` is shown here:
```yaml
clean: true
rules: rules/rules_14June2021.json
log: automation/log
data: 
   input: inputs/
   output: automation/results/
bclink:
  tables:
    person: person_001
    observation: observation_001
    measurement: measurement_001
    condition_occurrence: condition_occurrence_001
```

### Example yaml file (watches for new data dumps)

A full `yaml` example is given below, subsequent sections detail what each setting is doing:
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
```


## Run as daemon

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

Since the automated ETL is executed passing a `yaml` configuration to the command command `coconnect etl bclink from_yaml <yaml file>`, the following documents what the settings and doing and how they can be tuned.


### Rules **[required]**

* Specify the location of the [transform rules file](/docs/CoConnectTools/ETL/Rules/) provided by the co-connect team

```yaml
...
rules: /usr/lib/bcos/MyWorkingDirectory/rules.json 
...
```

### Input Data **[required]**

* Specify a list of folders (or individual csv files) for where the input data is located   
* Data must be [standardised](/docs/CoConnectTools/ETL/Extract/)
    * manually converted to `csv` format, abiding by [co-connect data standards](https://co-connect.ac.uk/co-connect-data-files-and-meta-data-standardisation/)
    * optionally already pseudonymised

```yaml
...
data: 
   input:
	- /data/dataset_drops/001/
	- /data/dataset_drops/002/
...
```

#### Watching a directory for new data-drops **[optional]**

* Alternative you can specify the ETL to watch a directory for new data-drops.

```yaml
...
data: 
   watch: 
      days: 1
   input: /data/dataset_drops
...
```

### Output Path **[required]**

* Also specify where the output CDM tables (in `tsv` format) are saved, so they can be uploaded to BCLink.

```yaml
...
data: 
   output: /usr/lib/bcos/MyWorkingDirectory/output
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

```yaml
...
data: 
   pseudonymise: 
      output: /usr/lib/bcos/MyWorkingDirectory/pseudo_data
      salt: 00ed1234da
...
```

### bclink **[optional]**

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

#### dry-run

* To execute the "load" as a dry-run, you can specify to only `echo` the bclink commands and not insert into `bclink`.

```yaml
bclink:
  dry-run: true
```