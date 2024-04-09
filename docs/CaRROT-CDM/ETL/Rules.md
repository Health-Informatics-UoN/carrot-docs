The transform (mapping) rules `json` file encodes rules on how to create CDM objects.

For co-connect datapartners, you will be supplied this `json` file by the co-connect team, who create this file based on a WhiteRabbit ScanReport uploaded and then mapped on the [Carrot-Mapper](../../Carrot-Mapper/index.md) mapping website.

## CLI helper

The co-connect package comes with a CLI helper for displaying/manipulating rules files
```
carrot display rules --help
```
```
Usage: carrot display rules [OPTIONS] COMMAND [ARGS]...

  Commands for displaying json rules in various ways.

Options:
  --help  Show this message and exit.

Commands:
  dag      Display the OMOP mapping json as a DAG
  delta    display a delta of two rules files
  flatten  flattern a rules json file
  json     Show the OMOP mapping json
```

## JSON Structure

```json
{
      "metadata": { ... },
      "cdm": {
             "<cdm table e.g. 'person' >": {
                  "<object name: e.g 'MALE' >": {
                        "<cdm field: e.g. 'gender_concept_id'>": {
                              "source_table": "<name of input .csv>",
                              "source_field": "<name of field in input .csv>",
                              "term_mapping": {
                                    "<name of value in field>": "<ohdsi concept id e.g. 8507 for 'MALE' >"
                              }
                        },
			"<next cdm field>": {...},
			...
	          },
		  "<next object name: e.g. 'FEMALE'>" : {...}
	     },
	     "<next cdm table e.g. 'condition_occurrence'>" : {...}
      }
 }
```


## Full json example

An example `json` file can be found in the following location:
```
echo $(carrot info data_folder)/test/rules/rules_14June2021.json
```

It can be displayed to the terminal (Unix):
```
carrot display rules json $(carrot info data_folder)/test/rules/rules_14June2021.json
```

```json
{
      "metadata": {
            "date_created": "2021-06-14T15:27:37.123947",
            "dataset": "Test"
      },
      "cdm": {
            "observation": {
                  "observation_0": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Asian": 35825508
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Asian": 35825508
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "observation_1": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Bangladeshi": 35825531
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Bangladeshi": 35825531
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "observation_2": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Indian": 35826241
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Indian": 35826241
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "observation_3": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "White": 35827394
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "White": 35827394
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "observation_4": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Black": 35825567
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Black": 35825567
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "observation_5": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "White and Asian": 35827395
                              }
                        },
                        "observation_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "observation_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "White and Asian": 35827395
                              }
                        },
                        "observation_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  }
            },
            "condition_occurrence": {
                  "condition_occurrence_0": {
                        "condition_concept_id": {
                              "source_table": "Symptoms.csv",
                              "source_field": "symptom1",
                              "term_mapping": {
                                    "Y": 254761
                              }
                        },
                        "condition_end_datetime": {
                              "source_table": "Symptoms.csv",
                              "source_field": "visit_date"
                        },
                        "condition_source_concept_id": {
                              "source_table": "Symptoms.csv",
                              "source_field": "symptom1",
                              "term_mapping": {
                                    "Y": 254761
                              }
                        },
                        "condition_source_value": {
                              "source_table": "Symptoms.csv",
                              "source_field": "symptom1"
                        },
                        "condition_start_datetime": {
                              "source_table": "Symptoms.csv",
                              "source_field": "visit_date"
                        },
                        "person_id": {
                              "source_table": "Symptoms.csv",
                              "source_field": "PersonID"
                        }
                  }
            },
            "person": {
                  "female": {
                        "birth_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "gender_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex",
                              "term_mapping": {
                                    "F": 8532
                              }
                        },
                        "gender_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex",
                              "term_mapping": {
                                    "F": 8532
                              }
                        },
                        "gender_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  },
                  "male": {
                        "birth_datetime": {
                              "source_table": "Demographics.csv",
                              "source_field": "date_of_birth"
                        },
                        "gender_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex",
                              "term_mapping": {
                                    "M": 8507
                              }
                        },
                        "gender_source_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex",
                              "term_mapping": {
                                    "M": 8507
                              }
                        },
                        "gender_source_value": {
                              "source_table": "Demographics.csv",
                              "source_field": "sex"
                        },
                        "person_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "PersonID"
                        }
                  }
            },
            "measurement": {
                  "covid_antibody": {
                        "value_as_number": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "IgG"
                        },
                        "measurement_source_value": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "IgG"
                        },
                        "measurement_concept_id": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "IgG",
                              "term_mapping": 37398191
                        },
                        "measurement_source_concept_id": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "IgG",
                              "term_mapping": 37398191
                        },
                        "measurement_datetime": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "date"
                        },
                        "person_id": {
                              "source_table": "covid19_antibody.csv",
                              "source_field": "PersonID"
                        }
                  }
            }
      }
}
```


## Demo Dataset

If you have downloaded the [demo-dataset](https://github.com/CO-CONNECT/demo-dataset.git) you will find two rules json files:
```
ls demo-dataset/data/*json
```
```
demo-dataset/data/rules.json       demo-dataset/data/rules_small.json
```

### DAG Display

Example:
```
carrot display rules dag demo-dataset/data/rules_small.json
carrot display rules dag demo-dataset/data/rules.json
```

<center>
<img src="../../../images/rules_small.gv.png" width="300"/>
<img src="../../../images/rules.gv.png" width="300"/>
</center>


### JSON Delta

You may want to extract a difference between two `json` rules files, for which the command `display rules delta` is appropriate.

Extract the delta between two rules files (a small old, and bigger new), pipeing the output to a file:
```
carrot display rules delta demo-dataset/data/rules_small.json demo-dataset/data/rules.json | tee rules_delta.json
```
Output example...
```
2022-03-16 10:35:09 - load_json_delta - INFO - loading a json from 'demo-dataset/data/rules.json' as a delta
2022-03-16 10:35:09 - load_json_delta - INFO - Original JSON date: 2022-02-11T12:22:48.465257
2022-03-16 10:35:09 - load_json_delta - INFO - New JSON date: 2022-02-11T12:22:48.465257
...
2022-03-16 10:34:42 - load_json_delta - INFO - Detected a new rule for Hypertensive disorder 3050
2022-03-16 10:34:42 - load_json_delta - INFO - Detected a new rule for COVID-19 vaccine 3034
2022-03-16 10:34:42 - load_json_delta - INFO - Detected a new rule for COVID-19 vaccine 3036
2022-03-16 10:34:42 - load_json_delta - INFO - Detected a new rule for SARS-CoV-2 (COVID-19) vaccine, mRNA-1273 0.2 MG/ML Injectable Suspension 3040
2022-03-16 10:34:42 - load_json_delta - INFO - Detected a new rule for SARS-CoV-2 (COVID-19) vaccine, mRNA-BNT162b2 0.1 MG/ML Injectable Suspension 3041~
...
 },
      "cdm": {
            "observation": {
                  "H/O: heart failure 3043": {
                        "observation_concept_id": {
                              "source_table": "Hospital_Visit.csv",
                              "source_field": "reason",
                              "term_mapping": {
                                    "Heart Attack": 4059317
                              }
                        },
...
```

And displaying the delta of the rules created:
```
carrot display rules dag rules_delta.json
```
<center>
<img src="../../../images/rules_delta.gv.png" width="550"/>
</center>
