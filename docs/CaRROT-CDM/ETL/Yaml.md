The following guide documents how you can use the co-connect command line tools to automate the ETL process for by defining a `yaml` configuration file.


ETL workflows, configured with a `yaml` file are executed with the synthax `carrot etl --config <path to config file> [optional additional COMMAND]` 

```
carrot etl --config config.yml --help
```
```
Usage: carrot etl [OPTIONS] COMMAND [ARGS]...

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
[Example yamls](https://github.com/HDRUK/CaRROT-CDM/tree/master/carrot/data/test/automation){ .md-button .md-button--secondary}
</center>

## Setup your YAML config

Here are some details on how you can setup a yaml configuration file

### Get to the point...

"TL;DR, I want a yaml configuration file that I can run out the box..."

??? "Locally"
	Run on some input data and perform a local load by merging the split outputs (e.g. create one file called `person.tsv` in the output folder)
	```yaml
	settings:
		listen_for_changes: true
		
	load: &load-local
		output: cache/
		merge_output: true

	transform:
		settings: &settings
			rules: demo-dataset/data/rules_small.json
		data:
			- input: demo-dataset/data/part1/
			  <<: *settings
              <<: *load-local
	```

	!!! warning
		The default behaviour is to load all data into memory and begin processing. This behaviour is not optimal for very large datasets, or if your computing environment has a small amount of memory (or you have a crash due to memory usage when running).
		
	To change the chunking of data, you can tell the tool to only read in X number of rows at a time, like so:
    ```yaml
	- input: ...
	  ...
	  number_of_rows_per_chunk: 10000
    ```
	
	The names of these can be found in the [source documentation](../CLI/Run/), corresponding to the options you will see via the command `carrot run map --help`.
	For example, to not perform any column formatting and to not automatically fill missing columns (e.g. `year_of_birth` in the person table):
	```yaml
	- input: ...
	  ...
      dont_automatically_fill_missing_columns: true
      format_level: 0
    ```

	

??? "BCLink"
	Configuration to upload to BCLink. Will wait for changes (additional data added to the transform section, changes to the rules file etc...)
	```yaml
	settings:
		listen_for_changes: true
		clean: true
	load: &load-bclink
		cache: /usr/lib/bcos/MyWorkingDirectory/Temp/cache/
		bclink:
			dry_run: false
	transform:
		settings: &settings
			output: *load-bclink
			rules: /usr/lib/bcos/MyWorkingDirectory/Temp/demo-dataset/data/rules.json
		data:
		- input: /usr/lib/bcos/MyWorkingDirectory/Temp/demo-dataset/data/part1/
		  <<: *settings
	```

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

	Additional valid keyword arguments to the `map` function (which can be found in [in `def map`](CaRROT-CDM/CLI/Run/)), can be passed here:
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
	
### Extract Tab

By defining an extract tab in the `yaml` you are able to execute code __before__ the transform is run.

??? "Configuration"
	
	The following shows how a bash command can be defined to run on an input and extract an output. It's important that you define an extract job that will run on a single input and output a single output. The configuration must specify a `{input}` and a  `{output}`.
	
	Any configuration must be also hooked up with the transform part, so the tool knows that it should perform the extract before processing..
	
	For example...
	```yaml
	extract: &pseudonymise
		bash: |
			echo "Going to run {input} and put output in {output}"
			pseudonymise csv --salt 12345 --id ID --output-folder {output} {input}
	...
	transform:
		data:
			- input:
				<<: *pseudonymise
				input: demo-dataset/data/part1/
				output: data_pseudonymised/
			  output: output/
			  rules: demo-dataset/data/rules_small.json
	```
	As can be seen, in the transform tab, the `data.input` now itself specifies an `input`/`output` because the `*pseudonymise` specifies how a pre-processing on the `input` should be run to produce an `output` - which is then used as the _input_ for transform.
	
	Example output, you will see the `pseudonymise csv` start to run before the transform.
	```
	2022-03-16 11:35:46 - run_etl - INFO - running etl on config.yaml (last modified: 1647430135.1740856)
	"Going to run demo-dataset/data/part1/Symptoms.csv and put output in data_pseudonymised/"
	
	2022-03-16 11:35:46.656 | INFO     | cli.cli:csv:16 - Working on file demo-dataset/data/part1/Symptoms.csv, pseudonymising columns '['ID']' with salt '12345'
	2022-03-16 11:35:46.656 | INFO     | cli.cli:csv:22 - Saving new file to data_pseudonymised//Symptoms.csv
	2022-03-16 11:35:46.761 | DEBUG    | cli.cli:csv:32 - 0        e428669397a3d0c72d46f6d5afe9a8ae20ea675883c0e7...
	1        a37861e3f9bb0fd4385b7c6fddcf6d4ba366a4f3c9b17b...
	2        a37861e3f9bb0fd4385b7c6fddcf6d4ba366a4f3c9b17b...
	3        a37861e3f9bb0fd4385b7c6fddcf6d4ba366a4f3c9b17b...
	4        a37861e3f9bb0fd4385b7c6fddcf6d4ba366a4f3c9b17b...
	...
	```


## Running the ETL

To run the ETL-Tool given a configuration file, simply run:
```
carrot etl --config config.yaml
```

### Daemon Mode
To run as a background process (more specifically as a "daemon"), that watches for changes in a directory (ideal for data dumps of regularly updated data) the tool can be started with the flag `-d` or `--daemon`.

#### Start the ETL from a yaml file

Start the ETL process with the following command:
```
carrot etl --config config.yaml --daemon
```
You'll see an output like:
```
2022-03-16 11:10:30 - etl - INFO - running as a daemon process, logging to carrot.log
2022-03-16 11:10:30 - etl - INFO - process_id in <TimeoutPIDLockFile: 'etl.pid' -- 'etl.pid'>
```

The file `etl.pid` will exist as long as the process is running, if you dont see this (while you specified to listen for changes), something when wrong and you should check the logs.


#### Check the logs

The yaml file configures where log messages are saved. For example, you can `tail` the last two lines of the log to see the output:
```
tail carrot.log
```
```
2022-03-16 11:14:14 - CommonDataModel - INFO - working on drug_exposure
2022-03-16 11:14:14 - CommonDataModel - INFO - starting on drug_exposure.COVID_19_vaccine_3035.0x117dff910.2022-03-16T111409
2022-03-16 11:14:14 - CommonDataModel - INFO - finished drug_exposure.COVID_19_vaccine_3035.0x117dff910.2022-03-16T111409 (0x119468eb0) ... 1/1 completed, 23522 rows
2022-03-16 11:14:14 - CommonDataModel - INFO - saving dataframe (0x11ba44ca0) to <carrot.io.plugins.local.LocalDataCollection object at 0x117dfffd0>
2022-03-16 11:14:14 - LocalDataCollection - INFO - saving drug_exposure to cache//drug_exposure.tsv
2022-03-16 11:14:14 - LocalDataCollection - INFO - finished save to file
2022-03-16 11:14:14 - CommonDataModel - INFO - finalised drug_exposure on iteration 0 producing 23522 rows from 1 tables
2022-03-16 11:14:14 - LocalDataCollection - INFO - Getting next chunk of data
2022-03-16 11:14:14 - LocalDataCollection - INFO - All input files for this object have now been used.
2022-03-16 11:14:14 - run_etl - INFO - Finished!... Listening for changes every 5 seconds to data in config.yaml
```

#### Find the process ID

By default a (lock) file `etl.pid` is created while the ETL process is running as a background process. The PID (process ID) is saved inside the file, e.g.:
```
$ cat etl.pid 
75107
```

#### Kill the daemon

To stop the background process, you can do:
```
kill -9 $(cat etl.pid)
```

## Additional Commands

### Check Tables

A simple command that only works if you have BCLink options setup in your `yaml` file
```
carrot etl --config config.yaml check-tables
```
The command will display information about the number of rows in the BCLink tables the configuration file configures for.


### Delete Tables
The following command can be run even when the ETL tool is already running as a background process
```
carrot etl --config config.yaml delete-tables 
```
Starting the command provides you with a prompt, asking which files you want to delete
```
2022-03-16 11:17:10 - run_etl - INFO - running etl on config.yaml (last modified: 1647356757.6764326)
[?] Which tables do you want to delete? ... : 
 > o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/person_ids.0x7f0c5783dcf8.2022-03-15T134800.tsv
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/person.MALE_3025.0x7f0c57843668.2022-03-15T134803.tsv
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/person_ids.0x7f0c56ad6ac8.2022-03-15T134807.tsv
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/person.FEMALE_3026.0x7f0c57843908.2022-03-15T134809.tsv
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/observation.Antibody_3027.0x7f0c56ad6908.2022-03-15T134814.tsv
   ...
```

For example, selecting one file (by moving your keyboard arrows to navigate and select):
```
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/observation.2019_nCoV_3044.0x7f0c563fa470.2022-03-15T135156.tsv
 > X /usr/lib/bcos/MyWorkingDirectory/Temp/cache/observation.Cancer_3045.0x7f0c563fa278.2022-03-15T135200.tsv
   o /usr/lib/bcos/MyWorkingDirectory/Temp/cache/condition_occurrence.Headache_3028.0x7f0c571c3320.2022-03-15T135459.tsv
```
Starts the process to delete this file (also deletes it from BCLink if it has been uploaded there):
```
...
2022-03-16 11:19:42 - BCLinkHelpers - INFO - Called remove_table on /usr/lib/bcos/MyWorkingDirectory/Temp/cache/observation.Cancer_3045.0x7f0c563fa278.2022-03v
2022-03-16 11:19:42 - LocalDataCollection - INFO - DataCollection Object Created
2022-03-16 11:19:42 - LocalDataCollection - INFO - Using a chunksize of '1000' nrows
2022-03-16 11:19:42 - LocalDataCollection - INFO - Registering  observation [<carrot.io.common.DataBrick object at 0x7fa2a0471630>]
2022-03-16 11:19:42 - BCLinkHelpers - NOTICE - bc_sqlselect --user=bclink --query=SELECT column_name FROM INFORMATION_SCHEMA. COLUMNS WHERE table_name = 'obsek
2022-03-16 11:19:42 - BCLinkHelpers - INFO - got pk observation_id
2022-03-16 11:19:42 - LocalDataCollection - INFO - Retrieving initial dataframe for 'observation' for the first time
2022-03-16 11:19:42 - BCLinkHelpers - NOTICE - bc_sqlselect --user=bclink --query=DELETE FROM observation WHERE observation_id IN (82700,82701,82703,82704,827k
2022-03-16 11:19:42 - LocalDataCollection - INFO - Getting next chunk of data
2022-03-16 11:19:42 - LocalDataCollection - INFO - Getting the next chunk of size '1000' for 'observation'
2022-03-16 11:19:42 - LocalDataCollection - INFO - --> Got 1000 rows
...
2022-03-16 11:19:44 - LocalDataCollection - INFO - All input files for this object have now been used.
2022-03-16 11:19:44 - delete_tables - WARNING - removing /usr/lib/bcos/MyWorkingDirectory/Temp/cache/observation.Cancer_3045.0x7f0c563fa278.2022-03-15T135200
```

## Reference

The following 

### BCLink
