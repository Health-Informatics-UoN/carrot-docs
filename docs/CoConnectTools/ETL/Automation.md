The following guide documents how you can use the co-connect command line tools to automate the ETL process for co-connect+BC-Link.

```
[bcos_srv]$ coconnect etl bclink from_yaml --help
Usage: coconnect etl bclink from_yaml [OPTIONS] CONFIG_FILE

  Run with a yaml configuration file

Options:
  -d, --daemon  run the ETL as a daemon process
  --help        Show this message and exit.
```

## Initial Setup

To use ETL with `bclink`, you must be the user `bcos_srv`, i.e. when ssh'd into the machine hosting `bclink`:
```
sudo -s
su - bcos_srv
```

The best practise is to create a working directory in the following location:
```
mkdir /usr/lib/bcos/MyWorkingDirectory/
cd /usr/lib/bcos/MyWorkingDirectory/
```

It is also best practise to setup a virtual python environment and install the tool:
```
python3 -m venv automation
source automation/bin/activate
pip install pip --upgrade
pip install co-connect-tools
```

[Detailed Installation Instructions](/docs/CoConnectTools/Installing/){ .md-button .md-button--primary}

## Run 

To run the full ETL you need a `.yml`(or `.yaml`) file to configure various settings.

[Example yamls](https://github.com/CO-CONNECT/co-connect-tools/tree/master/coconnect/data/test/automation){ .md-button .md-button--secondary}

### Example yaml file

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
$ coconnect etl bclink from_yaml example.yml -d
2021-10-07 10:30:02 - from_yaml - INFO - running as a daemon process, logging to coconnect-etl.log
2021-10-07 10:30:02 - from_yaml - INFO - process_id in <TimeoutPIDLockFile: 'etl.pid' -- 'etl.pid'>
$
```

### Check the logs

The yaml file configures where log messages are saved. For example, you can `tail` the last two lines of the log to see the output:
```
$ tail -2 coconnect-etl.log 
2021-10-07 10:30:02 - from_yaml - INFO - Refreshing _input_data every 0:00:05 to look for new subfolders....
2021-10-07 10:30:02 - from_yaml - WARNING - No subfolders for data dumps yet found...
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

Specify the location of the transform rules file provided by the co-connect team
```yaml
...
rules: /usr/lib/bcos/MyWorkingDirectory/rules.json 
...
```


### Clean **[optional]**

```yaml
...
clean: true
..
```

### Log **[optional]**


```yaml
...
log: /usr/lib/bcos/MyWorkingDirectory/coconnect-etl.log
...
```

### Input Data **[required]**

```yaml
...
data: 
   input:
	- /data/dataset_drops/001/
	- /data/dataset_drops/002/
   output: /usr/lib/bcos/MyWorkingDirectory/output
...
```

### Output Path **[required]**

```yaml
...
data: 
   output: /usr/lib/bcos/MyWorkingDirectory/output
...
```

### Watching a directory for new data-drops **[optional]**

Alternative you can specify the ETL to watch a directory for new data-drops.
```yaml
...
data: 
   watch: 
      days: 1
   input: /data/dataset_drops
...
```

### Pseudonymisation **[optional]**
```yaml
...
data: 
   pseudonymise: 
      output: /usr/lib/bcos/MyWorkingDirectory/pseudo_data
      salt: 00ed1234da
...
```

### bclink **[optional]**

By default it is assumed the bclink table to be inserted to has an id that is the same as the CDM table name.

However, you may want to upload to a different table e.g. insert a `person` table into the bclink table `ds10001`. To be able to to this you can specify a map between the CDM table and the BCLink take dataset id.

```yaml
bclink:
   tables:
      person: ds10001
      observation: ds10002
      condition_occurrence: ds10003
      measurement: ds10004
```
