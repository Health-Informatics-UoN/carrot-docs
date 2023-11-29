## Getting Started:

To run Carrot-Mapper you need a Postgres database running a schema called "OMOP", based on the OMOP CDM v5.4.0.

## What is the OMOP CDM?

For an introduction to the OMOP CDM, see:

- [OHDSI Website](https://www.ohdsi.org/data-standardization)
- [EHDEN Academy OMOP Course](https://academy.ehden.eu/)

## How to implement it locally

The [CDM R package](https://github.com/OHDSI/CommonDataModel/) can be used to create the DDL scripts and instantiate the tables.

If an OMOP vocabulary has not been supplied to populate the tables, you can download them from [Athena](https://athena.ohdsi.org/).

If you would rather not use the R package, here's a manual approach:

1. Download the [pregenerated OMOP v5.4.0 DDL's](https://github.com/OHDSI/CommonDataModel/tree/v5.4.0/inst/ddl/5.4/postgresql) for Postgres.
2. Change the ddls to use OMOP as a schema: ``
3. Copy the ddls to the postgres container: `scp`
4. Access the database container's shell: `docker exec -it carrot-mapper-dev-db-1 bash`
5. Connect to the Postgres process: `psql -U postgres -d postgres`
6. Create the OMOP schema: `CREATE SCHEMA omop;`
7. Run the create tables ddl: `\i OMOPCDM_postgresql_5.4_ddl.sql`
8. Load in the vocabulary data.
9. Now run the other ddls:

    * OMOPCDM_postgresql_5.4_primary_keys.sql
    * OMOPCDM_postgresql_5.4_constraints.sql
    * OMOPCDM_postgresql_5.4_indices.sql
