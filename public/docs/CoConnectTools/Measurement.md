

```python
class Measurement(Base):
    """
    CDM Measurement object class
    """
    
    name = 'measurement'
    def __init__(self):
        self.measurement_id                = DataType(dtype="INTEGER"     , required=True , pk=True)
        self.person_id                     = DataType(dtype="INTEGER"     , required=True)
        self.measurement_concept_id        = DataType(dtype="INTEGER"     , required=True)
        self.measurement_date              = DataType(dtype="DATE"        , required=False)
        self.measurement_datetime          = DataType(dtype="DATETIME"    , required=True)
        self.measurement_time              = DataType(dtype="VARCHAR(10)" , required=False)
        self.measurement_type_concept_id   = DataType(dtype="INTEGER"     , required=False)
        self.operator_concept_id           = DataType(dtype="INTEGER"     , required=False)
        self.value_as_number               = DataType(dtype="FLOAT"       , required=False)
        self.value_as_concept_id           = DataType(dtype="INTEGER"     , required=False)
        self.unit_concept_id               = DataType(dtype="INTEGER"     , required=False)
        self.range_low                     = DataType(dtype="FLOAT"       , required=False)
        self.range_high                    = DataType(dtype="FLOAT"       , required=False)
        self.provider_id                   = DataType(dtype="INTEGER"     , required=False)
        self.visit_occurrence_id           = DataType(dtype="INTEGER"     , required=False)
        self.visit_detail_id               = DataType(dtype="INTEGER"     , required=False)
        self.measurement_source_value      = DataType(dtype="VARCHAR(50)" , required=False)
        self.measurement_source_concept_id = DataType(dtype="INTEGER"     , required=False)
        self.unit_source_value             = DataType(dtype="VARCHAR(50)" , required=False)
        self.value_source_value            = DataType(dtype="VARCHAR(50)" , required=False)
```