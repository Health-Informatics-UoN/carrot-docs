## Getting Started:

To run Carrot-Mapper you need a Postgres database running a schema called "OMOP", based on the OMOP CDM v5.4.0.

## What is the OMOP CDM?

For an introduction to the OMOP CDM, see:

- [OHDSI Website](https://www.ohdsi.org/data-standardization)
- [EHDEN Academy OMOP Course](https://academy.ehden.eu/)

## Run locally

The [CDM R package](https://github.com/OHDSI/CommonDataModel/) can be used to create the DDL scripts and instantiate the tables.

If an OMOP vocabulary has not been supplied to populate the tables, you can download them from [Athena](https://athena.ohdsi.org/).

If you would rather not use the R package, here's a manual approach, each database step can also be replicated using a database tool such as PGAdmin or Datagrip.

1. Download the [pregenerated OMOP v5.4.0 DDL's](https://github.com/OHDSI/CommonDataModel/tree/v5.4.0/inst/ddl/5.4/postgresql) for Postgres.
2. Change the DDLs to use OMOP as a schema, for example using sed: `sed -i '' 's/@cdmDatabaseSchema/omop/g' OMOPCDM_postgresql_5.4_ddl.sql`
3. Copy the DDLSs, and vocabulary files to the postgres container: `docker cp <PATH> carrot-mapper-dev-db-1`
4. Access the database container's shell: `docker exec -it carrot-mapper-dev-db-1 bash`
5. Connect to the Postgres process: `psql -U postgres -d postgres`
6. Create the OMOP schema: `CREATE SCHEMA omop;`
7. Create the tables by running the first DDL: `\i OMOPCDM_postgresql_5.4_ddl.sql`
8. Load in your vocabulary data to the created tables. For example the concept table: `\copy concept FROM 'concept.csv' DELIMITER E'\t' CSV HEADER;`
9. Apply the primary keys, constraints, and indexes by running the remaining DDLs:

    * `\i OMOPCDM_postgresql_5.4_primary_keys.sql`
    * `\i OMOPCDM_postgresql_5.4_constraints.sql`
    * `\i OMOPCDM_postgresql_5.4_indices.sql`
