
The Pythonic version of the CommonDataModel is built object-orientated in the subfolder `coconnect/cdm/`:
```
coconnect/cdm/
├── __init__.py
├── classes
│   ├── __init__.py
├── decorators.py
├── model.py
├── objects
│   ├── __init__.py
│   ├── common.py
│   ├── condition_occurrence.py
│   ├── drug_exposure.py
│   ├── measurement.py
│   ├── observation.py
│   ├── person.py
│   └── visit_occurrence.py
└── operations.py
```

## Destination Tables

All CDM destination tables are formed as objects and are defined in `coconnect/cdm/objects`, inheriting from a base class (`DestinationTable`, defined in `common.py`):

   * [Person](/CoConnectTools/Person.md)
   * [Condition Occurrence](/CoConnectTools/ConditionOccurrence.md)
   * [Visit Occurrence](/CoConnectTools/VisitOccurrence.md)
   * [Observation](/CoConnectTools/Observation.md)
   * [Measurement](/CoConnectTools/Measurement.md)
   * [Measurement](/CoConnectTools/DrugExposure.md)


### Generating More Tables

The package contains `.csv` files taken from [https://github.com/OHDSI/CommonDataModel/tags](https://github.com/OHDSI/CommonDataModel/tags) that give descriptions of what fields are contained within each CDM table.

At present there is just `OMOP_CDM_v5_3_1.csv` from this url that is present:

```
$ ls $(coconnect info data_folder)/cdm/
OMOP_CDM_ONLINE_LATEST.csv OMOP_CDM_v5_3_1.csv
``` 


!!! danger
    There are known inconstitencies with the `.csv` files that are stored on the OHDSI GitHub.

!!! info
    For this reason we store the file `OMOP_CDM_ONLINE_LATEST.csv` in the package. This is a `csv` file taken from a raw dump of the latest OMOP CDM that the coconnect team are using. At this moment in time, it is version 5.3.1, and slightly differs from what should be the v5.3.1 in `OMOP_CDM_v5_3_1.csv`


To help generate a pythonic template for a CDM template, the CLI can be used to do this
```
$ coconnect generate cdm drug_exposure 5.3.1
```
This command tool outputs the following code that you could use to copy, paster and edit to create a new table for `drug_exposure`
```python
self.drug_exposure_id = DestinationField(dtype="INTEGER", required=True)
self.person_id = DestinationField(dtype="INTEGER", required=True)
self.drug_concept_id = DestinationField(dtype="INTEGER", required=True)
self.drug_exposure_start_date = DestinationField(dtype="DATE", required=True)
self.drug_exposure_start_datetime = DestinationField(dtype="DATETIME", required=False)
self.drug_exposure_end_date = DestinationField(dtype="DATE", required=True)
self.drug_exposure_end_datetime = DestinationField(dtype="DATETIME", required=False)
self.verbatim_end_date = DestinationField(dtype="DATE", required=False)
self.drug_type_concept_id = DestinationField(dtype="INTEGER", required=True)
self.stop_reason = DestinationField(dtype="VARCHAR(20)", required=False)
self.refills = DestinationField(dtype="INTEGER", required=False)
self.quantity = DestinationField(dtype="FLOAT", required=False)
self.days_supply = DestinationField(dtype="INTEGER", required=False)
self.sig = DestinationField(dtype="VARCHAR(MAX)", required=False)
self.route_concept_id = DestinationField(dtype="INTEGER", required=False)
self.lot_number = DestinationField(dtype="VARCHAR(50)", required=False)
self.provider_id = DestinationField(dtype="INTEGER", required=False)
self.visit_occurrence_id = DestinationField(dtype="INTEGER", required=False)
self.visit_detail_id = DestinationField(dtype="INTEGER", required=False)
self.drug_source_value = DestinationField(dtype="VARCHAR(50)", required=False)
self.drug_source_concept_id = DestinationField(dtype="INTEGER", required=False)
self.route_source_value = DestinationField(dtype="VARCHAR(50)", required=False)
self.dose_unit_source_value = DestinationField(dtype="VARCHAR(50)", required=False)
```


## Destination Fields
In `common.py` a class called `DestinationField` defines how to handle an input pandas series.
This pandas series is effectively a column in the output of the CDM Tables, in other words, `DestinationField` is an object for the `destination_field`, e.g. `person_id` in the `destination_table` `person`.

```python
class DestinationField(object):
    def __init__(self, dtype: str, required: bool, pk=False):
        self.series = None
        self.dtype = dtype
        self.required = required
        self.pk = pk
```

   * `self.series`: initialises as `None` and will hold a `pandas.Series` object if the column is to be filled in the output.    
   * `self.dtype`: specifies a string for handling how to format the output of this column so it's in the right format when saved to the final `.csv` to be uploaded successfully to a BCLink.  
   * `self.required`:  if the `destination_field` is required (`True`), any rows of the final table that do not have this column filled (`None`, `NaN`), are removed (dropped) from the output.
   * `self.pk`: specifies if this column is the __primary key__ for its associated table. This can be used to order the tables based on this.



 