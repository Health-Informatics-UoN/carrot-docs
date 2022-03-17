Before getting started, please take a look a the following frequently asked questions.

## ETL

### What is the co-connect ETL?

Extract, transform and load of your dataset into a Common Data Model (CDM) format that is loaded into bclink.

Our command line tool has the ability to do the full process:
```
coconnect etl --help
```
<center>
[Overview](/docs/CoConnectTools/ETL/About/){ .md-button .md-button--primary }
[Setting up automation](/docs/CoConnectTools/ETL/Yaml/){ .md-button .md-button--secondary }
</center>

Otherwise, you have to do each step manually:

<center>
[Extract](/docs/CoConnectTools/ETL/Extract){ .md-button .md-button--primary }
[Transform](/docs/CoConnectTools/ETL/Transform){ .md-button .md-button--primary }
[Load](/docs/CoConnectTools/ETL/Load){ .md-button .md-button--primary }
</center>

### How can I pseudonymise my data?

When `co-connect-tools` is installed a standalone package is also installed [`co-connect-pseudonymise`](https://pypi.org/project/co-connect-pseudonymise/) ([source code](https://github.com/CO-CONNECT/Pseudonymisation)).

!!! note
     The pseudonymisation script (`co-connect-pseudonymise`) can be install separately if you want to run the pseudonymisation in a separate environment and don't want to install all the addditonal packages that `co-connect-tools` will install.  

A CLI is setup called `pseudonymise` which can be run on `csv` files:
```
$ pseudonymise csv --help
Usage: pseudonymise csv [OPTIONS] INPUT

  Command to pseudonymise csv files, given a salt, the name of the columns to
  pseudonymise and the input file.

Options:
  -s, --salt TEXT           salt hash  [required]
  -c, --column, --id TEXT   name of the identifier columns  [required]
  -o, --output-folder TEXT  path of the output folder  [required]
  --chunksize INTEGER       set the chunksize when loading data, useful for
                            large files
  --help                    Show this message and exit.
```

A detailed guide on how to use this feature can be found here:

<center>
[Pseudonymisation guide](/docs/CoConnectTools/ETL/Pseudonymisation/){ .md-button .md-button--primary }
</center>

### Can I pseudonymise the data myself?

Yes. You do not need to use the tool packaged in co-connect-tools, you can do this process yourself to create a masked for the person (study) identifiers in your dataset.

## Transforming (mapping) a dataset

### What is the transform tool?

Our command line interface tool for performing only the 'T' part of the 'ETL' process.. a transformation of your dataset into a Common Data Model (CDM):

=== "With commandline arguments"
	Alternatively, using commandline arguments, the tool can be run as:
    ```
	coconnect run map --help
	```
=== "Using a configuration file"
	If the input data and rules file are specified in a `yaml` configuration file, and no load section is specified, the `etl` command can be used:
    ```
	coconnect etl --config <config> 
	```

<center>
[Overview](/docs/CoConnectTools/ETL/About/#transform){ .md-button .md-button--primary }
[Manual from the Command Line](/docs/CoConnectTools/ETL/Transform/){ .md-button .md-button--secondary }
[Manual from a GUI](/docs/CoConnectTools/ETL/Transform-GUI/){ .md-button .md-button--secondary }
</center>


### What is the rules `.json`?

A `json` encoded file that contains information of how multiple CDM tables and CDM objects need to be created by the [transform process](/docs/CoConnectTools/ETL/Transform/)

<center>
[Rules JSON](/docs/CoConnectTools/ETL/Rules/){ .md-button .md-button--primary }
</center>

### What should my input files be called?

The rules `.json` file can be called anything.

Your input files need to be `csv` files. If the mapping instructions have been followed correctly, the names encoded in the WhiteRabbit scan report should match the names of your input files, and therefore match the names in the `.json` file.

The helper command:
```
coconnect display rules json <rules file> --list-tables
```
can be used to display what input file names the tool will be trying to load. 

!!! example
    ```
    coconnect display rules json $(coconnect info data_folder)/test/rules/rules_14June2021.json --list-tables
    ```
    ```
      [
         "Demographics.csv",
         "Symptoms.csv",
		 "covid19_antibody.csv"
      ]
    ```
!!! tip
    If these names differ from the names of your input files, you'll have to rename the files or manually edit the `.json` files and replace all occurrences of a bad file name with the correct file name.


### What should I pass as `INPUTS`?

You need to pass a space separated list of files (or directories containing `.csv` files). 

The rules `.json` file is supplied via the option `--rules `.

The `--help` message displays the following syntax for how the `map` tool (for transform) can be run:

```
coconnect run map --help
```
```
Usage: coconnect run map [OPTIONS] [INPUTS]...

  Perform OMOP Mapping given an json file and a series of input files

  INPUTS should be a space separated list of individual input files or
  directories (which contain .csv files)

Options:
  --rules TEXT                    input json file containing all the mapping
                                  rules to be applied  [required]
...
```


## How can I install the software offline (no internet connection)?

You need to download the source code and its dependencies, somewhere which has a stable internet connection.

### Download wheel files

* Make a new directory
* Next execute the command `python3 -m pip download co-connect-tools`:
```
$ python3 -m pip download co-connect-tools
Collecting co-connect-tools
  Downloading co_connect_tools-0.4.13-py3-none-any.whl (300 kB)
     |████████████████████████████████| 300 kB 3.4 MB/s            
Collecting tabulate
  Using cached tabulate-0.8.9-py3-none-any.whl (25 kB)
Collecting requests
  Using cached requests-2.26.0-py2.py3-none-any.whl (62 kB)
Collecting openpyxl
  Using cached openpyxl-3.0.9-py2.py3-none-any.whl (242 kB)
```

Which will download `.whl` files into your directory:
```
$ ls 
Jinja2-3.0.3-py3-none-any.whl
MarkupSafe-2.0.1-cp38-cp38-macosx_10_9_x86_64.whl
PySimpleGUI-4.55.1-py3-none-any.whl
PyYAML-6.0-cp38-cp38-macosx_10_9_x86_64.whl
...
```

### Transfer the wheel files 

Now you can zip and move this folder to a location/machine that you want to install co-connect-tools, one that doesn't have an internet connection to pypi.org.

* In this new environment, create a new working directory and setup a virtual environment

!!! warning
	this new environment should be the same architecture, i.e. if you download on a Windows environment and then try to install on MacOS, this won't work.


* Copy/unzip the .whl files into a new folder, e.g. `deps/`

```
python3 -m venv .
source bin/activate
```


### Install the package offline

You can use `pip` to install 
```
pip install --no-index --find-links <path to folder containing .whl files> co-connect-tools
```

!!! warning
	Dependening on your python version, you made need a recent version of pip. You can [download the latest version of pip](https://pypi.org/project/pip/#modal-close) and tranfer the .whl file to this offline environment, and install the update with:
	```
	pip install pip-21.3.1-py3-none-any.whl
	```

Example:
```
$ pip install --no-index --find-links . co-connect-tools
Looking in links: .
Processing ./co_connect_tools-0.4.13-py3-none-any.whl
Processing ./PySimpleGUI-4.55.1-py3-none-any.whl
Processing ./requests-2.26.0-py2.py3-none-any.whl
Processing ./pandas-1.3.5-cp38-cp38-macosx_10_9_x86_64.whl
Processing ./tabulate-0.8.9-py3-none-any.whl
Processing ./openpyxl-3.0.9-py2.py3-none-any.whl
Processing ./numpy-1.21.4-cp38-cp38-macosx_10_9_x86_64.whl
Processing ./psutil-5.8.0-cp38-cp38-macosx_10_9_x86_64.whl
Processing ./PyYAML-6.0-cp38-cp38-macosx_10_9_x86_64.whl
Processing ./inquirer-2.8.0-py2.py3-none-any.whl
Processing ./click-8.0.3-py3-none-any.whl
Processing ./Jinja2-3.0.3-py3-none-any.whl
...
...
Successfully installed Jinja2-3.0.3 MarkupSafe-2.0.1 blessed-1.19.0 certifi-2021.10.8 charset-normalizer-2.0.9 click-8.0.3 co-connect-tools-0.4.13 coloredlogs-15.0.1 docutils-0.18.1 et-xmlfile-1.1.0 graphviz-0.19.1 humanfriendly-10.0 idna-3.3 inquirer-2.8.0 lockfile-0.12.2 numpy-1.21.4 openpyxl-3.0.9 pandas-1.3.5 psutil-5.8.0 pysimplegui-4.55.1 python-daemon-2.3.0 python-dateutil-2.8.2 python-editor-1.0.4 pytz-2021.3 pyyaml-6.0 readchar-2.0.1 requests-2.26.0 six-1.16.0 tabulate-0.8.9 urllib3-1.26.7 wcwidth-0.2.5
```

Check if the tool installed offline, and you're good to go
```
$ coconnect --version
0.4.13
```


