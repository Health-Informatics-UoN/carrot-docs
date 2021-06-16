
The Pythonic version of the CommonDataModel is built object-orientated in the subfolder `coconnect/cdm/`:
```
coconnect/cdm/
├── __init__.py
├── classes
│   └── __init__.py
├── decorators.py
├── model.py
├── objects
│   ├── __init__.py
│   ├── base.py
│   ├── condition_occurrence.py
│   ├── measurement.py
│   ├── observation.py
│   ├── person.py
│   └── visit_occurrence.py
└── operations.py
```

## CDM Fields
In `base.py` a class called `DataType` defines how to handle an input pandas series.
This pandas series is effectively a column in the output of the CDM Tables, in other words, `DataType` is the `destination_field`, e.g. `person_id` for `person`.

```python
class DataType(object):
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

All CDM Tables are formed as objects and are defined in `coconnect/cdm/objects`, inheriting from a base class (`Base`, defined in `base.py`):

   * [Person](/CoConnectTools/Person.md)
   * [Condition Occurrence](/CoConnectTools/ConditionOccurrence.md)
   * [Observation](/CoConnectTools/Observation.md)
   * [Measurement](/CoConnectTools/Measurement.md)

