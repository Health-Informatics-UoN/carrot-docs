
The Pythonic version of the CommonDataModel is built object-orientated in the subfolder `carrot/cdm/`:
```
carrot/cdm/
├── __init__.py
├── decorators.py
├── model.py
├── objects
│   ├── __init__.py
│   ├── common.py
│   └── versions
│       ├── __init__.py
│       └── v5_3_1
│           ├── __init__.py
│           ├── condition_occurrence.py
│           ├── drug_exposure.py
│           ├── measurement.py
│           ├── observation.py
│           ├── person.py
│           ├── procedure_occurrence.py
│           ├── specimen.py
│           └── visit_occurrence.py
```

## Destination Tables

All CDM destination tables are formed as objects and are defined in `carrot/cdm/objects`, inheriting from a base class (`DestinationTable`, defined in `common.py`):

   * [Person](Person.md)
   * [Condition Occurrence](ConditionOccurrence.md)
   * [Visit Occurrence](VisitOccurrence.md)
   * [Observation](Observation.md)
   * [Measurement](Measurement.md)
   * [Drug Exposure](DrugExposure.md)
   * [Procedure Occurrence](ProcedureOccurrence.md)
   * [Specimen](Specimen.md)


### Generating More Tables

The package contains `.csv` dumps taken from BCLink that give descriptions of what fields are contained within each CDM table.

At present are only dumps from version `5.3.1` of the CDM:

```
$ ls $(carrot info data_folder)/cdm/BCLINK_EXPORT/5.3.1
export-CONDITION_OCCURRENCE.csv export-MEASUREMENT.csv          export-PERSON.csv
export-DRUG_EXPOSURE.csv        export-OBSERVATION.csv
``` 

To help generate a pythonic template for a CDM template, the CLI can be used to do this
```
carrot generate cdm drug_exposure 5.3.1
```
This command tool outputs the following code that you could use to copy, paster and edit to create a new table for `drug_exposure`
```python
self.drug_exposure_id = DestinationField(dtype="Integer", required=False , pk=True)
self.person_id = DestinationField(dtype="Integer", required=False )
self.drug_concept_id = DestinationField(dtype="Integer", required=False )
self.drug_exposure_start_date = DestinationField(dtype="Date", required=False )
self.drug_exposure_start_datetime = DestinationField(dtype="Timestamp", required=False )
self.drug_exposure_end_date = DestinationField(dtype="Date", required=False )
self.drug_exposure_end_datetime = DestinationField(dtype="Timestamp", required=False )
self.verbatim_end_date = DestinationField(dtype="Date", required=False )
self.drug_type_concept_id = DestinationField(dtype="Integer", required=False )
self.stop_reason = DestinationField(dtype="Text20", required=False )
self.refills = DestinationField(dtype="Integer", required=False )
self.quantity = DestinationField(dtype="Float", required=False )
self.days_supply = DestinationField(dtype="Integer", required=False )
self.sig = DestinationField(dtype="Integer", required=False )
self.route_concept_id = DestinationField(dtype="Integer", required=False )
self.lot_number = DestinationField(dtype="Text50", required=False )
self.provider_id = DestinationField(dtype="Integer", required=False )
self.visit_occurrence_id = DestinationField(dtype="Integer", required=False )
self.drug_source_value = DestinationField(dtype="Text50", required=False )
self.drug_source_concept_id = DestinationField(dtype="Integer", required=False )
self.route_source_value = DestinationField(dtype="Text50", required=False )
self.dose_unit_source_value = DestinationField(dtype="Text50", required=False )
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


::: carrot.cdm.model.CommonDataModel 
