


```python
class Person(Base):
    """
    CDM Person object class
    """
    name = 'person'
    def __init__(self):
        self.person_id                   = DataType(dtype="INTEGER"     , required=True , pk=True)
        self.gender_concept_id           = DataType(dtype="INTEGER"     , required=True)
        self.year_of_birth               = DataType(dtype="INTEGER"     , required=False)
        self.month_of_birth              = DataType(dtype="INTEGER"     , required=False)
        self.day_of_birth                = DataType(dtype="INTEGER"     , required=False)
        self.birth_datetime              = DataType(dtype="DATETIME"    , required=True)
        self.race_concept_id             = DataType(dtype="INTEGER"     , required=False)
        self.ethnicity_concept_id        = DataType(dtype="INTEGER"     , required=False)
        self.location_id                 = DataType(dtype="INTEGER"     , required=False)
        self.provider_id                 = DataType(dtype="INTEGER"     , required=False)
        self.care_site_id                = DataType(dtype="INTEGER"     , required=False)
        self.person_source_value         = DataType(dtype="VARCHAR(50)" , required=False)
        self.gender_source_value         = DataType(dtype="VARCHAR(50)" , required=False)
        self.gender_source_concept_id    = DataType(dtype="INTEGER"     , required=False)
        self.race_source_value           = DataType(dtype="VARCHAR(50)" , required=False)
        self.race_source_concept_id      = DataType(dtype="INTEGER"     , required=False)
        self.ethnicity_source_value      = DataType(dtype="VARCHAR(50)" , required=False)
        self.ethnicity_source_concept_id = DataType(dtype="INTEGER"     , required=False)
```
