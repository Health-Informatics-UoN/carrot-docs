
!!! danger
	This section is decrepid, please instead use [co-connect-pseudonymise](../../Carrot-Pseudonymise/Pseudonymisation.md) instead.

!!! warning
	Please use the command:
	```
	pseudonymise csv --help
	```
	That comes with the [co-connect-pseudonymise](../../Carrot-Pseudonymise/Pseudonymisation.md) package, and is installed when you install `carrot-cdm`.


# Decrepid Tool

A dataset can be pseudonymised __in-house__, or via the use of the carrot-cdm `pseudonymise` command (also the command executed in the ETL automation process)

```
$ carrot pseudonymise --help
Usage: carrot pseudonymise [OPTIONS] INPUT

  Command to help pseudonymise data.

Options:
  -s, --salt TEXT             salt hash  [required]
  -i, --person-id, --id TEXT  name of the person_id  [required]
  -o, --output-folder TEXT    path of the output folder  [required]
  --chunksize INTEGER         set the chunksize when loading data
  --help                      Show this message and exit.
```

## Example

```
carrot pseudonymise -s 123456 -i PersonID -o pseudoynmised_data $(carrot info data_folder)/test/inputs/original/Demographics.csv 			      
```

Which outputs:

```
2021-10-07 14:55:55 - pseudonymise - INFO - Working on file /Users/calummacdonald/Usher/CO-CONNECT/Software/carrot-cdm/carrot/data/test/inputs/original/Demographics.csv, pseudonymising column 'PersonID' with salt '123456'
2021-10-07 14:55:55 - pseudonymise - INFO - Saving new file to pseudoynmised_data/Demographics.csv
2021-10-07 14:55:55 - InputData - INFO - InputData Object Created
2021-10-07 14:55:56 - InputData - INFO - Registering  /Users/calummacdonald/Usher/CO-CONNECT/Software/carrot-cdm/carrot/data/test/inputs/original/Demographics.csv [<class 'pandas.core.frame.DataFrame'>]
2021-10-07 14:55:56 - pseudonymise - INFO - 0      16dc368a89b428b2485484313ba67a3912ca03f2b2b424...
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
2021-10-07 14:55:56 - pseudonymise - INFO - Done!
```

