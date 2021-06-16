In `base.py` a class called `DataType` defines how to handle an input pandas series.
This pandas series is effectively a column in the output of the CDM Objects, in other words, `DataType` is the `destination_field`, e.g. `person_id` for `person`.

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