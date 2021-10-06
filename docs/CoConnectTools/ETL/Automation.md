The following guide documents how you can use the co-connect command line tools to automate the ETL process for co-connect+BC-Link.

```
[bcos_srv]$ coconnect etl bclink from_yaml --help
Usage: coconnect etl bclink from_yaml [OPTIONS] CONFIG_FILE

  Run with a yaml configuration file

Options:
  -d, --daemon  run the ETL as a daemon process
  --help        Show this message and exit.
```

## Example `yaml` configuration

The automated ETL is executed with the command `coconnect etl bclink from_yaml` passing a `yaml` configuration file to.

### As daemon

To run as a background process (more specifically as a "daemon"), the tool can be started with the flag `-d` or `--daemon`.

### Full Example
```yaml
clean: true
rules: /usr/lib/bcos/OMOP-test-data/rules.json 
log: /usr/lib/bcos/OMOP-test-data/coconnect-etl.log
data: 
   watch: 
      days: 1
   input: /data/dataset_drops
   output: /usr/lib/bcos/OMOP-test-data/output
   pseudonymise: 
      output: /usr/lib/bcos/OMOP-test-data/pseudo_data
      salt: 00ed1234da
bclink:
   tables:
      person: ds10001
      observation: ds10002
      condition_occurrence: ds10003
      measurement: ds10004
```

### Rules **[required]**
```yaml
...
rules: /usr/lib/bcos/OMOP-test-data/rules.json 
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
log: /usr/lib/bcos/OMOP-test-data/coconnect-etl.log
...
```

### Input Data **[required]**

```yaml
...
data: 
   input:
	- /data/dataset_drops/001/
	- /data/dataset_drops/002/
   output: /usr/lib/bcos/OMOP-test-data/output
...
```

### Output Path **[required]**

```yaml
...
data: 
   output: /usr/lib/bcos/OMOP-test-data/output
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
      output: /usr/lib/bcos/OMOP-test-data/pseudo_data
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
