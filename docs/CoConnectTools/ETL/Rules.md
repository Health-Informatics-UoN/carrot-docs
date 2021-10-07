The transform (mapping) rules `json` file encodes rules on how to create CDM objects.

For co-connect datapartners, you will be supplied this `json` file by the co-connect team, who create this file based on a WhiteRabbit ScanReport uploaded and then mapped on the [CCOM](/docs/MappingPipeline/about/) mapping website.

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
echo $(coconnect info data_folder)/test/rules/rules_14June2021.json
```

It can be displayed to the terminal (Unix):
```
coconnect display json $(coconnect info data_folder)/test/rules/rules_14June2021.json
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