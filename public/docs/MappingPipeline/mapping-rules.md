Mapping Rules determine how to link (and potentially modify) between `source_fields` (input data) and `destination_fields` (output data) when building `CDM` objects. 

## Validating Rules

A validation of the `ScanReportConcept` step is needed ({==!not implmented yet==}).

1. `ScanReportConcept` maps between a source {++field++} or {++value++} and a __concept__ via `ScanReportConcept.content_type`. From either of these, it is possible to find the associated source {++table++}.
1. The associated table must have a column (field) marked to be the `person_id`
1. `ScanReportConcept.concept.domain_id` can be used to work out what `cdm` object is to be created, e.g. `person`, `condition_occurrence`, `measurement`, `observation`... etc. The associated table must __also__ have a column marked to be a `date_event` for this domain/object.

## Building Rules

For each `ScanReportConcept` that is created the procedure to build Mapping-Rules proceeds as follows:

1. The {++destination_field++} (`OmopField` object) of the `<domain>_concept_id` is found using `ScanReportConcept.concept.domain_id`. The {++destination_table++} is linked to the object.
1. From the {++source_field++} or {++source_value++} of the `ScanReportConcept.content_type` the {++source_table++} is found.
1. A MappingRule is created linking the `person_id` of the found {++destination_table++}, a field which every `CDM` object contains, with the current {++source_table++} and {++source_value++} or {++source_field++}, as well as with the `concept`.


### Schematic Diagram
![](images/save_mapping_rules.png)

## Downloading Rules