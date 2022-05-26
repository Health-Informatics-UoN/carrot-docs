
A separate package, to help with pseudonymisation of columns in data files by passing a ==salt==. [co-connect-pseudonymise](https://pypi.org/project/co-connect-pseudonymise/) is available to be installed via pypi, or via the [source code on GitHub](https://github.com/CO-CONNECT/Pseudonymisation).

This is provided as a separate package for those who no not wish (e.g. for security reasons) or need to install the full package ([co-connect-tools](https://pypi.org/project/co-connect-tools/)).

!!! info 
	{++co-connect-pseudonymise++} will be installed if the packge {++co-connect-tools++} is installed, as it is a dependancy of the latter. 

co-connect-pseudonymise contains:   

   * a simple CLI (command line interface) to pseudonymise `csv` files   
   * a [python workbook](https://github.com/CO-CONNECT/Pseudonymisation/tree/main/notebooks) showing how pseudonymisation can be performed manually   
   * additional [example code](https://github.com/CO-CONNECT/Pseudonymisation/tree/main/examples) for C# and Java    


## Installation (via pip)

To install the package, it's recommended to use a python virtual environment, using `python >= 3.6.8`. It's best to install via `pip` with a connection to the internet.

### Setup a virtual environment

Navigate to a clean or working directory and setup a virtual environment:

```
python3 -m venv  .
source bin/activate
```

### Upgrade Pip

You'll first need to upgrade pip to make sure the latest packages can be found:
```
pip install pip --upgrade
```

### Install the package

```
pip install co-connect-pseudonymise
```

### Check the package

```
pseudonymise --help
```
Print the message:
```
Usage: pseudonymise [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  csv  Command to pseudonymise csv files, given a salt, the name of the...
```


## Run for pseudonymisation of a CSV file

### Display help
To display the help message to see what options are available, run:
```
pseudonymise csv --help 
```
Which shows:
```
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

### Execute

Executing a command to pseudonymise the data in the column `PersonID` of the file `data/Demographics.csv` using the ==salt== `beb2839bd78` and outputing the file to the folder `pseudonymised_data/`:
```
pseudonymise csv --salt beb2839bd78 --column PersonID  data/Demographics.csv -o pseudonymised_data/
```

Outputs the following:
```
2022-01-10 09:56:11.155 | INFO     | cli.cli:csv:16 - Working on file Demographics.csv, pseudonymising columns '['PersonID']' with salt 'beb2839bd78'
2022-01-10 09:56:11.157 | INFO     | cli.cli:csv:22 - Saving new file to pseudonymised_data/Demographics.csv
2022-01-10 09:56:11.173 | DEBUG    | cli.cli:csv:32 - 0      16dc368a89b428b2485484313ba67a3912ca03f2b2b424...
1      37834f2f25762f23e1f74a531cbe445db73d6765ebe608...
2      454f63ac30c8322997ef025edff6abd23e0dbe7b8a3d51...
3      5ef6fdf32513aa7cd11f72beccf132b9224d33f271471f...
4      1253e9373e781b7500266caa55150e08e210bc8cd8cc70...
                             ...                        
995    8352dd9eb8b64669e0a8347fd37ae6e5cd67c817f2b4b1...
996    235aa062e6372588dbae00552abf36b8ff9c315e3da56c...
997    4aec429ac0bfafdbb8dab14f41d1b7a98dacf1ce3478b7...
998    330e14d4ae80612334d94c488d29eb469626b476864abd...
999    ab9828ca390581b72629069049793ba3c99bb8e5e9e7b9...
Name: PersonID, Length: 1000, dtype: object
2022-01-10 09:56:11.183 | INFO     | cli.cli:csv:52 - Done with pseudonymised_data/Demographics.csv!
```
