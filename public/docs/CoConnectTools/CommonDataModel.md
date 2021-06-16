
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
│   ├── measurement.py
│   ├── observation.py
│   ├── person.py
│   └── visit_occurrence.py
└── operations.py
```

## CDM Fields
In `common.py` a class called `DestinationField` defines how to handle an input pandas series.
This pandas series is effectively a column in the output of the CDM Tables, in other words, `DestinationField` is an object for the `destination_field`, e.g. `person_id` in the `destinatio_table` `person`.

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

## CDM Tables

All CDM Tables are formed as objects and are defined in `coconnect/cdm/objects`, inheriting from a base class (`DestinationTable`, defined in `common.py`):

   * [Person](/CoConnectTools/Person.md)
   * [Condition Occurrence](/CoConnectTools/ConditionOccurrence.md)
   * [Observation](/CoConnectTools/Observation.md)
   * [Measurement](/CoConnectTools/Measurement.md)

