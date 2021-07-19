

```python
class ConditionOccurrence(DestinationTable):
    """
    CDM Condition Occurrence object class
    """
    name = 'condition_occurrence'
    def __init__(self):
        self.condition_occurrence_id       = DestinationField(dtype="INTEGER"     , required=True , pk=True)
        self.person_id                     = DestinationField(dtype="INTEGER"     , required=True)
        self.condition_concept_id          = DestinationField(dtype="INTEGER"     , required=True)
        self.condition_start_date          = DestinationField(dtype="DATE"        , required=False)
        self.condition_start_datetime      = DestinationField(dtype="DATETIME"    , required=True)
        self.condition_end_date            = DestinationField(dtype="DATE"        , required=False)
        self.condition_end_datetime        = DestinationField(dtype="DATETIME"    , required=False)
        self.condition_type_concept_id     = DestinationField(dtype="INTEGER"     , required=False)
        self.stop_reason                   = DestinationField(dtype="VARCHAR(20)" , required=False)
        self.provider_id                   = DestinationField(dtype="INTEGER"     , required=False)
        self.visit_occurrence_id           = DestinationField(dtype="INTEGER"     , required=False)
        self.visit_detail_id               = DestinationField(dtype="INTEGER"     , required=False)
        self.condition_source_value        = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.condition_source_concept_id   = DestinationField(dtype="INTEGER"     , required=False)
        self.condition_status_source_value = DestinationField(dtype="VARCHAR(50)" , required=False)
        self.condition_status_concept_id   = DestinationField(dtype="INTEGER"     , required=False)

```