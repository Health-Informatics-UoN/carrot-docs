# API for OMOP and Co-Connect DBs

The mapping-pipeline API allows programmtic interaction with co-connect DB and OMOP DB contents and the API is developed using the Django REST framework.  Endpoints for accessing OMOP CDM Contents and mapping tool DB contents:

## API Root
An API root can be accessed using: http://127.0.0.1:8080/api

## OMOP DB 
We have implemented enpoints for following 8 tables. The API endpoints for these tables are read only. 

1. concept table: 
  a.  http://127.0.0.1:8080/api/omop/concepts/         			Returns all concepts of a concept table
  b.  http://127.0.0.1:8080/api/omop/concepts/1/		 	Returns concept details fromt the concept table for concept id=1 
  c.  http://localhost:8080/api/omop/conceptsfilter/?concept_code=R51&vocabulary_id=ICD10CM
