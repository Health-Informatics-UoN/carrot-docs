# The CaRROT-Mapper API 

The CaRROT-Mapper API allows programmatic interaction with the CaRROT-Mapper database. This API is developed using the Django REST framework. 
Access to API endpoints is protected by token based authentication. The REST API is also the means of communication between the Django webapp and its supporting Azure Functions.  

Our development team typically tests API endpoints using the Postman software. 

## API Root
An API root can be accessed using: **http://localhost:8080/api** and this endpoint lists all the available endpoints in the API (see Figure 1). 
Figure 1 also demonstrates that for testing API endpoints, a token is required, which can be requested from the system administrator.   

![](images/APIRootTest_Postman.png)
**Figure 1** *A sample API endpoint testing through Postman*


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


## OMOP DB 
Read-only endpoints exist for 8 tables of the OMOP CDM DB. 

1. Concept table: 
    * **http://localhost:8080/api/omop/concepts/** Returns all records in the `concept` table.
    * **http://localhost:8080/api/omop/concepts/1/** Returns concept details from the `concept` table for `concept_id=1`.
    * **http://localhost:8080/api/omop/conceptsfilter/**
      ```
      filter_fields:
        concept_id: in, exact
        concept_code: in, exact
        vocabulary_id: in, exact
      ```
 
2. Vocabulary table: 
    * **http://localhost:8080/api/omop/vocabularies/** Returns all records from the `vocabulary` table
    * **http://localhost:8080/api/omop/vocabularies/Cost/**	Returns a record from a `vocabulary` table with `vocabulary_id=Cost`

3. Concept_relationship table: 
    * **http://localhost:8080/api/omop/conceptrelationships/** Returns all records from the `conceptrelationship` table
      ```
      filter_fields:
        concept_id_1: exact
        concept_id_2: exact
        relationship_id: exact
      ```
    * **http://localhost:8080/api/omop/conceptrelationshipfilter/** Returns all records from the `conceptrelationship` table
      ```
      filter_fields:
        concept_id_1: in, exact
        concept_id_2: in, exact
        relationship_id: in, exact
      ```

4. Concept_ancestor table: 
    * **http://localhost:8080/api/omop/conceptancestors/** Returns all records from the `concept_ancestor` table
    * **http://localhost:8080/api/omop/conceptancestors/262/** Returns all records from the `concept_ancestor` table with `concept_ancestor_id=262`
    * **http://localhost:8080/api/omop/conceptancestors/**
      ```
      filter_fields:
        ancestor_concept_id: exact
        descendant_concept_id: exact
      ```
	
5. Concept_class table: 
    * **http://localhost:8080/api/omop/conceptclasses**	Returns all records from the `concept_class` table
    * **http://localhost:8080/api/omop/conceptclasses/10th%20level/** Returns all records from `concept_class` table with `concept_class_id='10th level'`

6. Concept_synonym table: 
    * **http://localhost:8080/api/omop/conceptsynonyms/** Returns all records from the `concept_synonym` table
    * **http://localhost:8080/api/omop/conceptsynonyms/2/** Returns all records from the `concept_synonym` table with `concept_id=2`
	
7. Domain table: 
    * **http://localhost:8080/api/omop/domains** Returns all records from the `domain` table
    * **http://localhost:8080/api/omop/domains/Condition/**	Returns all records from the `domain` table with `domain_id=Condition`

8. Drug_strength table: 
    * **http://localhost:8080/api/omop/drugstrengths/**	Returns all records from the `drug_strength` table
      ```
      filter_fields:
        drug_concept_id: in, exact
        ingredient_concept_id: in, exact
      ```

## Django User Models

We have implemented enpoints for following 1 table of django user model.

1. User table
    * **http://localhost:8080/api/users/** This endpoint returns user details (user ids and usernames) of all users. 
    * **http://localhost:8080/api/users/6/** or **http://localhost:8080/api/usersfilter/?id=6** This endpoint returns user details (user ids and usernames) of a specific user with an id=6. 
    * **http://localhost:8080/api/usersfilter/?id__in=6,10** This endpoint returns user details for a given list of user ids (i.e. 6 and 10). 

## Co-Connect DB

We have implemented enpoints for following 16 tables of co-connect DB. 

1. mapping_scanreport table
    * **http://localhost:8080/api/scanreports/** All scan reports in a mapping_scanreport table. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **http://localhost:8080/api/scanreports/31/** A record in a mapping_scanreport table with id=31
	
2. mapping_scanreporttable table
    * **http://localhost:8080/api/scanreporttables/** All scan report tables in a mapping_scanreporttables table. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **http://localhost:8080/api/scanreporttables/1** or **http://localhost:8080/api/scanreporttablesfilter/?id=1** A record in a mapping_scanreportables table with id=1
    * **http://localhost:8080/api/scanreporttablesfilter/?scan_report=1&name=Freezer.csv** This will return a record that has a "scan_report=1" and "name=Freezer.csv"
    * **http://localhost:8080/api/scanreporttablesfilter/?scan_report=1** This endpoint returns all records in a scanreporttable for a scan_report having an id=1. 
    * **http://localhost:8080/api/scanreporttablesfilter/?scan_report__in=1,40** This endpoint returns all records in a scanreporttable for a scan_report either having an id=1 or id=40.
    * **http://localhost:8080/api/scanreporttablesfilter/?name=Freezer.csv** This returns a record(s) that has a "name=Freezer.csv".
    * **http://localhost:8080/api/scanreporttablesfilter/?name__in=Freezer.csv,Questionnaire.csv** This returns records that either has a "name=Freezer.csv" or "name=Questionnaire.csv"
    * **http://localhost:8080/api/scanreporttablesfilter/?id__in=1,284** This returns records that either has an "id=1" or "id=284" (as id is a primary key there will be a single record for each value).
	
3. mapping_scanreportfield table
    * **http://localhost:8080/api/scanreportfields/** All scan report fields in a mapping_scanreportfield table. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **http://localhost:8080/api/scanreportfields/4638/** or **http://localhost:8080/api/scanreportfieldsfilter/?id=4638** A record in a mapping_scanreportfields with id=4638 
    * **http://localhost:8080/api/scanreportfieldsfilter/?scan_report_table=419&name=altered_conscious_state** This will return a record from a mapping_scanreportfield table with "scan_report_table=419" and "name=altered_conscious_state"
    * **http://localhost:8080/api/scanreportfieldsfilter/?scan_report_table=419** This will return all records from a mapping_scanreportfield table with "scan_report_table=419"
    * **http://localhost:8080/api/scanreportfieldsfilter/?scan_report_table__in=694,281** This will return all records from a mapping_scanreportfield for a scan_report_table having an id either equal to 694 or 281. 
    * **http://localhost:8080/api/scanreportfieldsfilter/?name=mrn** This return all records from a mapping_scanreportfield table having a "name=mrn".
    * **http://localhost:8080/api/scanreportfieldsfilter/?name__in=mrn,personid** This return all records from a mapping_scanreportfield table either having a "name=mrn" or "name=personid=personid".
    * **http://localhost:8080/api/scanreportfieldsfilter/?id__in=4638,9942,9943** This return records from a mapping_scanreportfield table either having an "id=4638" or "id=9942" or "id=9943" (as id is a primary key there will be a single record for each value).

4. mapping_scanreportvalue table
    * **http://localhost:8080/api/scanreportvalues/** All scan report values in a mapping_scanreportvalues. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **http://localhost:8080/api/scanreportvalues/2/** or **http://localhost:8080/api/scanreportvaluesfilter/?id=2** A record in a mapping_scanreportvalues with id=2
    * **http://localhost:8080/api/scanreportvaluesfilter/?scan_report_field=222&value=Surgery** This will return a record from a mapping_scanreportvalue table with "scan_report_field=222" and "value=surgery"
    * **http://localhost:8080/api/scanreportvaluesfilter/?scan_report_field=222** This will return all records from a mapping_scanreportvalue table with "scan_report_field=222"
    * **http://localhost:8080/api/scanreportvaluesfilter/?scan_report_field=222&fields=value,frequency** This will return all records from a mapping_scanreportvalue table with "scan_report_field=222" but will only be returning specified fields in the "fields" parameters (i.e. in this case they are "value" and "frequency").
    * **http://localhost:8080/api/scanreportvaluesfilterscanreport/?scan_report=651** This will return all records from a mapping_scanreportvalue table for a scan report with id=651 (i.e. scan_report=651)
    * **http://localhost:8080/api/scanreportvaluesfilterscanreporttable/?scan_report_table=8** This will return all records from a mapping_scanreportvalue table for a scan report table with id=8 (i.e. scan_report_table=8)
    * **http://localhost:8080/api/scanreportvaluesfilter/?scan_report_field__in=222,1,284** This will return all records from a mapping_scanreportvalue table for a list of of ids (222, 1, 284). 
    * **http://localhost:8080/api/scanreportvaluesfilter/?value__in=Surgery,YES** This will return all records from a mapping_scanreportvalue table matching any of values from the list (i.e. Surgery, YES) in a value field. 
    * **http://localhost:8080/api/scanreportvaluesfilter/?id__in=2,301,286,1360** This return records from a mapping_scanreportvalue table either having an "id=2" or "id=301" or "id=286" or "id=1360" (as id is a primary key there will be a single record for each value).
    * **http://localhost:8080/api/scanreportvaluepks/?scan_report=56** This will return all values where the conceptID!= -1 for a scan report with id=56

5. mapping_scanreportconcept table	
    * **http://localhost:8080/api/scanreportconcepts/** All scan report concepts in a mapping_scanreportconcepts. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
    * **http://localhost:8080/api/scanreportconcepts/2/** or **http://localhost:8080/api/scanreportconceptsfilter/?id=2** A record in a mapping_scanreportconcepts with id=2
    * **http://localhost:8080/api/scanreportconceptsfilter/?object_id=513874** This returns all records (i.e. assigned scan report concepts) for a given object_id (i.e. object_id can be of an id of scanreportvalue or scanreportfield).
    * **http://localhost:8080/api/scanreportconceptsfilter/?object_id__in=513874,513856** This returns all records (i.e. assigned scan report concepts) for a given list of object_id (i.e. object_id can be of an id of scanreportvalue or scanreportfield).
    * **http://localhost:8080/api/scanreportconceptsfilter/?concept__concept_id=8507** This returns all records (i.e. assigned scan report concepts) for a given concept_id.
    * **http://localhost:8080/api/scanreportconceptsfilter/?concept__concept_id__in=8507,8532** This returns all records (i.e. assigned scan report concepts) for a given list of concept_id.
    * **http://localhost:8080/api/scanreportconceptsfilter/?id__in=2,4** This return records from a mapping_scanreportconcepts table either having an "id=2" or "id=4" (as id is a primary key there will be a single record for each value).
	
6. mapping table	
    * **http://localhost:8080/api/mappings/** All records in a mapping table
    * **http://localhost:8080/api/mappings/1/** A record in a mapping table with id=1

7. mapping_classificationsystem table	
    * **http://localhost:8080/api/classificationsystems/** All records in a mapping_classificationsystem table
    * **http://localhost:8080/api/classificationsystems/2/** A record in a mapping_classificationsystem table with id=2

8. mapping_datadictionary table	
    * **http://localhost:8080/api/datadictionaries** All records in a mapping_datadictionary table
    * **http://localhost:8080/api/datadictionaries/2/** A record in a mapping_datadictionary table with id=2

9. mapping_document table	
    * **http://localhost:8080/api/documents/** All records in a mapping_document table
    * **http://localhost:8080/api/documents/2** A record in a mapping_document table with id=2

10. mapping_documentfile table	
    * **http://localhost:8080/api/documentfiles/** All records in a mapping_documentfiles table
    * **http://localhost:8080/api/documentfiles/2** A record in a mapping_documentfiles table with id=2

11. datapartner table	
     * **http://localhost:8080/api/datapartners/** All records in a datapartner table. For this endpoint, making a put request allows to accept a json array that is beneficial in its own right with a single call to an api endpoint. 
     * **http://localhost:8080/api/datapartners/2/** A record in a datapartner table with id=2
     * **http://localhost:8080/api/datapartnersfilter/?name=University%20of%20Liverpool** This will return a record that has "name=University of Liverpool"
	
12. mapping_omoptable table	
     * **http://localhost:8080/api/omoptables/** All records in a mapping_omoptable table
     * **http://localhost:8080/api/omoptables/1673/** or **http://localhost:8080/api/omoptablesfilter/?id=1673** A record in a mapping_omoptable table with "id=1673"
     * **http://localhost:8080/api/omoptablesfilter/?id__in=1673,1674** This return records from a mapping_omoptable table either having an "id=1673" or "id=1674" (as id is a primary key there will be a single record for each value).

13. mapping_omopfield table	
     * **http://localhost:8080/api/omopfields/** All records in a mapping_omopfield table
     * **http://localhost:8080/api/omopfields/450/** or **http://localhost:8080/api/omopfieldsfilter/?id=450** A record in a mapping_omopfield table with id=2
     * **http://localhost:8080/api/omopfieldsfilter/?id__in=449,450** This return records from a mapping_omopfield table either having an "id=449" or "id=450" (as id is a primary key there will be a single record for each value). 

14. mapping_mappingrule table	
     * **http://localhost:8080/api/mappingrules/** All records in a mapping_mappingrule
     * **http://localhost:8080/api/mappingrules/12200** or **http://localhost:8080/api/mappingrulesfilter/?id=12200** A record in a mapping_mappingrule table with id=8194
     * **http://localhost:8080/api/mappingrulesfilter/?scan_report=40** This returns all records in mapping_mappingrule table for a scan_report=40. 
     * **http://localhost:8080/api/mappingrulesfilter/?scan_report__in=40,89** This returns all records in mappingrule table either having a scan_report=40 or 89.
     * **http://localhost:8080/api/mappingrulesfilter/?id__in=12200,12208** This returns records from a mapping_mappingrul table either having an "id=1673" or "id=1674" (as id is a primary key there will be a single record for each value).
     * **http://localhost:8080/api/mappingruleslist/?id=56** This returns all mapping rules for scan report with id=56
	
15. source table	
     * **http://localhost:8080/api/sources/** All records in a source table
     * **http://localhost:8080/api/sources/1/** A record in a source table with id=1   (Currently there is no record in the azure dev DB version)
		
16. documenttype table	
     * **http://localhost:8080/api/documenttypes/** All records in a documenttype table
     * **http://localhost:8080/api/documenttypes/2/** A record in a documenttype table with id=2

## Count Stats

* **http://localhost:8080/api/countstats/** Returns the total number of Scan Reports, Scan Report Tables, Scan Report Fields, Scan Report Values & Mapping Rules in the database
* **http://localhost:8080/api/countstatsscanreport/?scan_report=435** Returns the total number of tables, fields, values and mapping rules in a scan report with id=435
* **http://localhost:8080/api/countstatsscanreporttable/?scan_report_table=435** Returns the total number of fields and values in a table with id=435
* **http://localhost:8080/api/countstatsscanreporttablefield/?scan_report_field=6781** Returns the total number of values in a field with id=6781

## General use
* **http://localhost:8080/api/json/?id=526** Returns a json representation of the mapping rules for a scan report with id=526.
* **http://localhost:8080/api/scanreports/526/download** Downloads the scan report with id=526 from azure blob storage and returns an .xlsx file as an HTTP Response.

### Specifying returning fields 
For all the endpoints, user have an option of specifying returning fields and in case if you don't supply returning fields parameter, it will return values of all fields.

(API endpoints without filter)
API_URL/?fields=returningfield1,returningfield2
(e.g. http://localhost:8080/api/scanreporttables/?fields=id,name
and http://localhost:8080/api/scanreporttables/1/?fields=id,name)

(API endpoints with filter)
API_URL/?filterfield=value1&filterfield2=value2&fields=returningfield1,returningfield2
(e.g. http://localhost:8080/api/scanreporttablesfilter/?scan_report=1&fields=id,name,person_id)

