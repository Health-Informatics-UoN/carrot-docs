

```python
class Measurement(DestinationTable):
    """
    CDM Measurement object class
    """
    
    name = 'measurement'
    def __init__(self):
        self.measurement_id                = DestinationField(dtype="INTEGER"     , required=True , pk=True)
        self.person_id                     = DestinationField(dtype="INTEGER"     , required=True)
        self.measurement_concept_id        = DestinationField(dtype="INTEGER"     , required=True)
        self.measurement_date              = DestinationField(dtype="DATE"        , required=False)
        self.measurement_datetime          = DestinationField(dtype="DATETIME"    , required=True)
        self.measurement_time              = DestinationField(dtype="VARCHAR(10)" , required=False)
        self.measurement_type_concept_id   = DestinationField(dtype="INTEGER"     , required=False)
        self.operator_concept_id           = DestinationField(dtype="INTEGER"     , required=False)
        self.value_as_number               = DestinationField(dtype="FLOAT"       , required=False)
        self.value_as_concept_id           = DestinationField(dtype="INTEGER"     , required=False)
        self.unit_concept_id               = DestinationField(dtype="INTEGER"     , required=False)
        self.range_low                     = DestinationField(dtype="FLOAT"       , required=False)
        self.range_high                    = DestinationField(dtype="FLOAT"       , required=False)
        self.provider_id                   = DestinationField(dtype="INTEGER"     , required=False)
        self.visit_occurrence_id           = DestinationField(dtype="INTEGER"     , required=False)
        self.visit_detail_id               = DestinationField(dtype="INTEGER"     , required=False)
        self.measurement_source_value      = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.measurement_source_concept_id = DestinationField(dtype="INTEGER"     , required=False)
        self.unit_source_value             = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.value_source_value            = DestinationField(dtype="VARCHAR(50)" , required=False)
```