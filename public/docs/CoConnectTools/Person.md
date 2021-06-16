


```python
class Person(DestinationTable):
    """
    CDM Person object class
    """
    name = 'person'
    def __init__(self):
        self.person_id                   = DestinationField(dtype="INTEGER"     , required=True , pk=True)
        self.gender_concept_id           = DestinationField(dtype="INTEGER"     , required=True)
        self.year_of_birth               = DestinationField(dtype="INTEGER"     , required=False)
        self.month_of_birth              = DestinationField(dtype="INTEGER"     , required=False)
        self.day_of_birth                = DestinationField(dtype="INTEGER"     , required=False)
        self.birth_datetime              = DestinationField(dtype="DATETIME"    , required=True)
        self.race_concept_id             = DestinationField(dtype="INTEGER"     , required=False)
        self.ethnicity_concept_id        = DestinationField(dtype="INTEGER"     , required=False)
        self.location_id                 = DestinationField(dtype="INTEGER"     , required=False)
        self.provider_id                 = DestinationField(dtype="INTEGER"     , required=False)
        self.care_site_id                = DestinationField(dtype="INTEGER"     , required=False)
        self.person_source_value         = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.gender_source_value         = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.gender_source_concept_id    = DestinationField(dtype="INTEGER"     , required=False)
        self.race_source_value           = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.race_source_concept_id      = DestinationField(dtype="INTEGER"     , required=False)
        self.ethnicity_source_value      = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.ethnicity_source_concept_id = DestinationField(dtype="INTEGER"     , required=False)
```
