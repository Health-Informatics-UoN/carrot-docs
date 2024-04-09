# The Carrot-Mapper API 

The Carrot-Mapper API allows programmatic interaction with the Carrot-Mapper database. This API is developed using the Django REST framework. 
Access to API endpoints is protected by token based authentication. The REST API is also the means of communication between the Django webapp and its supporting Azure Functions.  

This page documents _most_ of the endpoints defined. The only exceptions are some page-view URLs that are not designed for REST access, but rather are for internal use by the webapp.

Our development team typically tests API endpoints using the Postman software. 

## API Root
An API root can be accessed using: **http://localhost:8080/api** and this endpoint lists all the available endpoints in the API (see Figure 1). 
Figure 1 also demonstrates that for testing API endpoints, a token is required, which can be requested from the system administrator.   

![](images/APIRootTest_Postman.png)
**Figure 1** *A sample API endpoint testing through Postman*

Prepend the string `http://localhost:8080/api/` to each of the defined URLs to access a valid endpoint. 
Access to the dev/test/prod systems is similarly via `https://ccom-dev.azurewebsites.net/api/`, `https://ccom-test.azurewebsites.net/api/` and `https://ccom.azurewebsites.net/api/` respectively, with different auth tokens for each.

## Filter fields
Many endpoints support selected filter fields. Here we define the syntax used to express the available filter options
for a given endpoint.
```commandline
filter_fields:
name: exact
```
indicates that the filter field `?name=ABC` can be optionally appended to the URL to filter by the `name` property.

Supplying no filter field will return all results.
```commandline
filter_fields:
    id: exact, in
``` 
indicates that the filter field `?id=ABC` can be appended to the URL to filter by the `id` property, or the filter field
`?id__in=1,2,3` can be appended to return only results with `id` of either  `1`, `2`, or `3`. Similarly, supplying no
filter field will return all results.

Multiple filter fields can be defined for a given URL, leading to more complicated examples. Multiple filters are 
separated by `&`, with no restriction on the ordering of the filters, thus
```commandline
filter_fields:
    id: exact, in
    name: exact
```
allows all of the following combinations:
```commandline
?id=1
?id__in=1,2,3
?name=ABC
?id=1&name=ABC
?name=ABC&id__in=1,2,3
```

### Specifying returning fields 
For some endpoints (those defined using DynamicFieldsMixin), users have an option of specifying return fields.
On this page, these endpoints are marked with :material-filter:.
If no return fields parameter is supplied, all fields will be returned.

Return fields are specified by the syntax `?fields=id,name`. When combined with filters, the syntax is thus for example `?dataset_name=test&fields=id`.

## OMOP DB 
Read-only endpoints exist for 8 tables of the OMOP CDM DB. 

1. Concept table: 
    * **omop/concepts/** Returns all records in the `concept` table.
    * **omop/concepts/1/** Returns concept details from the `concept` table for `concept_id=1`.
    * **omop/conceptsfilter/**
      ```
      filter_fields:
        concept_id: in, exact
        concept_code: in, exact
        vocabulary_id: in, exact
      ```
    
2. Concept_ancestor table: 
    * **omop/conceptancestors/** Returns all records from the `concept_ancestor` table
    * **omop/conceptancestors/262/** Returns all records from the `concept_ancestor` table with `concept_ancestor_id=262`
    * **omop/conceptancestors/**
      ```
      filter_fields:
        ancestor_concept_id: exact
        descendant_concept_id: exact
      ```
	
3. Concept_class table: 
    * **omop/conceptclasses**	Returns all records from the `concept_class` table
    * **omop/conceptclasses/10th%20level/** Returns all records from `concept_class` table with `concept_class_id='10th level'`

4. Concept_relationship table: 
    * **omop/conceptrelationships/** Returns all records from the `conceptrelationship` table
      ```
      filter_fields:
        concept_id_1: exact
        concept_id_2: exact
        relationship_id: exact
      ```
    * **omop/conceptrelationshipfilter/** Returns all records from the `conceptrelationship` table
      ```
      filter_fields:
        concept_id_1: in, exact
        concept_id_2: in, exact
        relationship_id: in, exact
      ```
      
5. Concept_synonym table: 
    * **omop/conceptsynonyms/** Returns all records from the `concept_synonym` table
    * **omop/conceptsynonyms/2/** Returns all records from the `concept_synonym` table with `concept_id=2`
	
6. Domain table: 
    * **omop/domains** Returns all records from the `domain` table
    * **omop/domains/Condition/**	Returns all records from the `domain` table with `domain_id=Condition`

7. Drug_strength table: 
    * **omop/drugstrengths/**	Returns all records from the `drug_strength` table
      ```
      filter_fields:
        drug_concept_id: in, exact
        ingredient_concept_id: in, exact
      ```

8. Vocabulary table: 
    * **omop/vocabularies/** Returns all records from the `vocabulary` table
    * **omop/vocabularies/Cost/**	Returns a record from a `vocabulary` table with `vocabulary_id=Cost`

## Carrot

Note that many of these endpoints implement user permissions checks, and may restrict or filter results based upon the 
user rights associated to the Token provided.

1. User table
    * **users/** Returns all records from the `auth_users` table. 
    * **users/<User_id\>/**  Returns user details from the `auth_users` table by `id`.
    * **usersfilter/** 
      ```
      filter_fields:
        id: in, exact
      ``` 

2. mapping_scanreport table
    * **scanreports/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreport` table. 
      * For this endpoint, making a GET/POST request shows the results. Making a PUT/PATCH/DELETE request allows 
        for editing. 
      ```
      filter_fields:
        parent_dataset: exact
      ``` 
    * **scanreports/<ScanReport_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreport` table by `id`.
	
3. mapping_scanreporttable table
    * **scanreporttables/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreporttables` table.
      ```
      filter_fields:
        id: in, exact
        name: in, exact
        scan_report: in, exact
      ``` 
      * For this endpoint, making a GET/POST request shows the results. Making a PUT/PATCH/DELETE request allows 
        for editing.
    * **scanreporttables/<ScanReportTable_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreporttable` table by `id`.
    
	
4. mapping_scanreportfield table
    * **scanreportfields/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportfield` table.
      ```
      filter_fields:
        id: in, exact
        name: in, exact
        scan_report_table: in, exact
      ``` 
      * For this endpoint, making a GET/POST request shows the results. Making a PUT/PATCH/DELETE request allows 
        for editing.
    * **scanreportfields/<ScanReportField_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records in the `mapping_scanreportfields` table by `id`.

5. mapping_scanreportvalue table
    * **scanreportvalues/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportvalues` table. 
      ```
      filter_fields:
        id: in, exact
        name: in, exact
        scan_report_field: in, exact
      ``` 
      * For this endpoint, making a GET/POST request shows the results. Making a PUT/PATCH/DELETE request allows 
        for editing.
    * **scanreportvalues/<ScanReportValue_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportvalues` table by `id`.
    
    * **scanreportvaluepks/?scan_report=<ScanReport_id\>** Returns all records from the `mapping_scanreportvalues` table 
      which are linked to the scan report by `id` through the chain of `ScanReportValue -> ScanReportField -> ScanReportTable -> ScanReport`, and which 
      additionally do not have `conceptID=-1` (which is the default for those without an associated ScanReportConcept).
    * **scanreportvaluesfilterscanreporttable** [:material-filter:](#specifying-returning-fields) Returns all ScanReportValues associated to the supplied `ScanReportTable` id.
      ```
      filter_fields:
        scan_report_table: exact
      ``` 

6. mapping_scanreportconcept table	
    * **scanreportconcepts/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportconcepts` table. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **scanreportconcepts/<ScanReportConcept_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportconcepts` table by `id`.
    * **scanreportconceptsfilter/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportconcepts` table, with additional filtering capabilities on `concept_id`.
      ```
      filter_fields:
        id: in, exact
        object_id: in, exact
        content_type: in, exact
        concept_id: in, exact
      ``` 
    * **scanreportactiveconceptfilter/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_scanreportconcepts` table
      which (1) are associated to an object with the given `content_type` (`15` for `ScanReportField`, `17` for `ScanReportValue` - any other value will return None);
      and (2) are associated to a `ScanReport` which is both active (not hidden) and has Status `Mapping Complete`.
      This endpoint is only available to the Azure Function superuser.
      ```
      filter_fields:
        content_type: exact
      ``` 
      
7. mapping_classificationsystem table	
    * **UNUSED!** **classificationsystems/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_classificationsystem` table.
    * **classificationsystems/<ClassificationSystem_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_classificationsystem` table by `id`.

8. mapping_datadictionary table	
    * **UNUSED!** **datadictionaries/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_datadictionary` table.
    * **datadictionaries/<DataDictionary_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_datadictionary` table by `id`.

9. datapartner table	
    * **datapartners/** [:material-filter:](#specifying-returning-fields) Returns all records from the `datapartner` table.  
    * **datapartners/<DataPartner_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `datapartner` table by `id`.
    * **datapartnersfilter/** [:material-filter:](#specifying-returning-fields)
      ```
      filter_fields:
        name: exact
      ```
    
10. mapping_omoptable table	
     * **omoptables/** Returns all records from the `mapping_omoptable` table.
     * **omoptables/<OmopTable_id\>/** Returns all records from the `mapping_omoptable` table by `id`.
     * **UNUSED!** **omoptablesfilter/**
       ```
       filter_fields:
         id: in, exact
       ```

11. mapping_omopfield table	
     * **omopfields/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_omopfield` table.
     * **omopfields/<OmopField_id\>/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_omopfield` table by `id`.
     * **UNUSED!** **omopfieldsfilter/** [:material-filter:](#specifying-returning-fields)
       ```
       filter_fields:
         id: in, exact
       ``` 

12. mapping_mappingrule table	
     * **mappingrules/** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_mappingrule` table.
     * **mappingrules/<MappingRule_id\>** [:material-filter:](#specifying-returning-fields) Returns all records from the `mapping_mappingrule` table by `id`.
     * **mappingrulesfilter/** [:material-filter:](#specifying-returning-fields)
       ```
       filter_fields:
         scan_report: in, exact
         concept: in, exact
       ``` 
     * **mappingruleslist/?id=<ScanReport_id\>** This returns all mapping rules associated to the `ScanReport` with `id`. This requires looking up a number of foreign keys and may be rather slow.
       * This also supports pagination, using the page number parameter `p` and page size parameter `page_size`, e.g. `mappingruleslist/?id=56&p=1&page_size=30` 

13. mapping_dataset table
    * **datasets/** [:material-filter:](#specifying-returning-fields) Returns all `Datasets` which the user is able to view.
      ```
      filter_fields:
        id: in, exact
        data_partner: in, exact
        hidden: in, exact 
      ```
    * **datasets/<Dataset_id\>** [:material-filter:](#specifying-returning-fields) Return a single `Dataset` by `id`.
    * **datasets/update/<Dataset_id\>** [:material-filter:](#specifying-returning-fields) Update a single `Dataset` by `id`.
    * **datasets/delete/<Dataset_id\>** [:material-filter:](#specifying-returning-fields) Delete a single `Dataset` by `id`.
    * **datasets/create** Create a single `Dataset`.
    * **datasets_data_partners/** [:material-filter:](#specifying-returning-fields) Returns all `Datasets` which the user is able to view, while providing a pagination option, and pre-fetching (for performance) Data Partner information associated to each.
      ```
      filter_fields:
        id: in, exact
        data_partner: in, exact
        hidden: in, exact 
      ```
      * This also supports pagination, using the page number parameter `p` and page size parameter `page_size`, e.g. `datasets_data_partners/?id=56&p=1&page_size=30` 

14. mapping_projects table
    * **projects/** [:material-filter:](#specifying-returning-fields) Returns all `Projects` which the user is able to view.
      ```
      filter_fields:
        name: in, exact
        dataset: exact 
      ```
    * **projects/<Project_id\>** [:material-filter:](#specifying-returning-fields) Returns a single `Project` by `id`.
    * **projects/update/<Project_id\>** [:material-filter:](#specifying-returning-fields) Update a single `Project` by `id`.
    
## Count Stats

* **countstats/** Returns the total number of `ScanReports`, `ScanReportTables`, `ScanReportFields`, `ScanReportValues` & `MappingRules` in the database.
* **countstatsscanreport/?scan_report=<ScanReport_id\>** Returns the total number of tables, fields, values and mapping rules in a `ScanReport` by `id`.
* **countstatsscanreporttable/?scan_report_table=<ScanReportTable_id\>** Returns the total number of `ScanReportFields` and `ScanReportValues` in a `ScanReportTable` by `id`.
* **countstatsscanreporttablefield/?scan_report_field=<ScanReportField_id\>** Returns the total number of `ScanReportValues` in a `ScanReportField` by `id`.

## Assorted others
* **analyse/<ScanReport_id\>/** [:material-filter:](#specifying-returning-fields) Returns an analysis of the `ScanReportConcepts` in the `ScanReport` with `id`. This analysis compares to all other `ScanReports`, 
  finding ancestor and descendants of each, and reporting the results (specifically, any mismatched `Concepts` found between this `ScanReport` and any other)

* **countprojects/<Dataset_id\>/** Returns the count of distinct projects to which the provided `Dataset` `id` is associated.

* **scanreports/<ScanReport_id\>/download** Returns the `ScanReport` file in `.xlsx` format with `id` from Azure blob storage as an HTTP Response.
