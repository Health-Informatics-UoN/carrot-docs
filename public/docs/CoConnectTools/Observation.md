

```python
class Observation(Base):
    """
    CDM Observation object class
    """
    
    name = 'observation'
    def __init__(self):
        self.observation_id                = DataType(dtype="INTEGER"     , required=True , pk=True)
        self.person_id                     = DataType(dtype="INTEGER"     , required=True)
        self.observation_concept_id        = DataType(dtype="INTEGER"     , required=True)
        self.observation_date              = DataType(dtype="DATE"        , required=False)
        self.observation_datetime          = DataType(dtype="DATETIME"    , required=True)
        self.observation_type_concept_id   = DataType(dtype="INTEGER"     , required=False)
        self.value_as_number               = DataType(dtype="FLOAT"       , required=False)
        self.value_as_string               = DataType(dtype="VARCHAR(60)" , required=False)
        self.value_as_concept_id           = DataType(dtype="INTEGER"     , required=False)
        self.qualifier_concept_id          = DataType(dtype="INTEGER"     , required=False)
        self.unit_concept_id               = DataType(dtype="INTEGER"     , required=False)
        self.provider_id                   = DataType(dtype="INTEGER"     , required=False)
        self.visit_occurrence_id           = DataType(dtype="INTEGER"     , required=False)
        self.visit_detail_id               = DataType(dtype="INTEGER"     , required=False)
        self.observation_source_value      = DataType(dtype="VARCHAR(50)" , required=False)
        self.observation_source_concept_id = DataType(dtype="INTEGER"     , required=False)
        self.unit_source_value             = DataType(dtype="VARCHAR(50)" , required=False)
        self.qualifier_source_value        = DataType(dtype="VARCHAR(50)" , required=False)
```