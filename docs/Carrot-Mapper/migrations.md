# Migrations

Building and applying database migrations relies upon migration files
stored in `api/mapping/migrations/`.

The `makemigrations` command will compare the contents of `models.py`
with the list of _locally held_ migrations files in 
`api/mapping/migrations/`. It will then create a migration file of the 
operations required to convert the effect of the sum of all migrations 
files into the same as `models.py`. **Thus it is important that the
locally held migrations files reflect the state of the database before 
any new migration is applied.** There are three possibilities: the local
`migrations` match the current database state; `migrations` is behind 
the current database state (because the previous migration of the 
database was performed by another individual); or `migrations` is _ahead_ 
of the current database state (for example, if you have migrated 
`model-test-db` but not yet migrated `ccnet-dev-db`)

## Choosing the correct instructions
If the state of locally held migration files is _behind_ the current 
database state, then follow the instructions in the section 
`Bringing the migrations/ directory up-to-date with the current database state`,
before continuing to `Creating and applying the new migrations`.

Alternatively, if the state of `migrations` matches the database state, then skip 
to the section `Creating and applying the new migrations` below. 

Finally, if `migrations` is _ahead_ of the database, then skip 
straight to step 5 of `Creating and applying the new migrations` to apply 
the migrations directly.

## Bringing the `migrations/` directory up-to-date with the current database state
In the more complex case where the local migrations files _do
not_ match the existing state of the database, we must first create a 
migration file to bring the local migrations up to date with the current 
state of the database. This is achieved by checking out the latest `devel` 
branch (make sure you pull the latest version), and applying the steps 
below. 

1. Checkout the `devel` branch, ensuring that you `git pull` in order to 
   have the latest state of `devel` as is deployed.
   
2. Build and run the mapping tool Docker locally, where the `.env` file should 
   contain the credentials for the relevant DB. Please be 
   **extremely careful** when applying changes to the production database,
   which will require the credentials for that DB. The relevant 
   environment variables are `COCONNECT_DB_NAME`, `COCONNECT_DB_USER`, 
   and `COCONNECT_DB_PASSWORD`.
```
docker build --tag ccom . && docker run -it --volume $PWD/api:/api --env-file .env -p 8080:8000 ccom
```

3. In another terminal tab, run the following to get the name of the 
   running container and open a shell within it (be careful here if 
   you have more than one container running in docker! - It just grabs 
   the first container in the list)
```
docker exec -it `docker container ls | awk 'NR==2 {print $1}'` /bin/bash
```

4. Inside the shell, to create the migrations:
```
cd /api
python manage.py makemigrations mapping
```

5. Now _fake-apply_ the migrations files. This will mark the migrations
files as having been applied, but not make any changes to the DB.
```
python manage.py migrate --fake mapping
```

In this way, the local migration files are brought up-to-date with the
database state.

Now continue with the below section to apply new migrations.

## Creating and applying the new migrations

1. Checkout the feature branch which is ready to be merged.
   
2. Build and run the mapping tool Docker locally, where the `.env` file should 
   contain the credentials for the relevant DB. Please be 
   **extremely careful** when applying changes to the production database,
   which will require the credentials for that DB. The relevant 
   environment variables are `COCONNECT_DB_NAME`, `COCONNECT_DB_USER`, 
   and `COCONNECT_DB_PASSWORD`.
```
docker build --tag ccom . && docker run -it --volume $PWD/api:/api --env-file .env -p 8080:8000 ccom
```

3. In another terminal tab, run the following to get the name of the 
   running container and open a shell within it (be careful here if 
   you have more than one container running in docker! - It just grabs 
   the first container in the list)
```
docker exec -it `docker container ls | awk 'NR==2 {print $1}'` /bin/bash
```

4. Inside the shell, to create the migrations:
```
cd /api
python manage.py makemigrations mapping
```

5. Now dry-run the migration. This will show the operations that will be
applied.
```
python manage.py migrate --plan mapping
```

6. To apply the changes:
```
python manage.py migrate mapping
```

# TL;DR
Steps 1-4 in each section are identical, except that the first uses `devel`
and the second the feature branch that is to be merged. The first section 
can be skipped if the state of the `migrations/` directory is up-to-date 
with the status of the database. This will only be the case if you are the
person who applied the previous set of migrations.

In section 1 we fake applying the migrations to bring the state of the 
`migrations/` directory up-to-date with the database.

In contrast, in section 2 we apply the new migrations to bring the database
up-to-date with the `migrations/` directory, which reflects `models.py`.