To restore the database to a backup file, or to be able to import the existing `prod` database (the one running on VM that people are using) into the `test` database, you need to follow the following steps:

## 1. Delete/Move the existing database

The instructions so far are just to delete your existing database that you have locally. You might not want to do this, so you could always move it somewhere else, or work how how to merge databases.

In other words:
```
mv data/pgdata/ /tmp/pgdata
```

## 2. Download a backup file

From the online server, retrieve a backup file. These will be located in `/opt/apps/mapping-pipeline`.

Example:
```
scp XXX.XXX.XXX.XXX:/opt/apps/mapping-pipeline/coconnect-YYYYYYY.bak .
```

## 3. Place the `.bak` file in the correct location

You need the `.bak` file to be see by the docker container. In other words, it needs to be in a folder that is in a __mounted__ volume.

The folder `data` is already mounted, as this is where the database is stored, so it is a good choice.

```
mkdir data/backups/
mv coconnect-YYYYYYY.bak data/backups/
```

Alternatively, if you really wanted, you could change the `docker-compose.yml` file:
```
  db:
    volumes:
      - ./data/pgdata:/var/lib/postgresql/data
      - ./data:/data
      - /my/folder/:/data/backups
```

## 4. Build and run the stack

Set the docker-compose stack running
```
docker-compose up --build 
```

## 5. Start up `psql`

Use the db container to execute `psql` so we can import the data:
```
docker-compose exec db psql -U $POSTGRES_DB $POSTGRES_USER
```
_note_: the name of the db and the user are just what is in your `.env` file

## 6. Import the backup data

Simple using the `\i` command, you can import the database from the `.bak` file..

```
=# \i data/backups/coconnect-20210221-2335.bak
```

_note_: Use the command `\q` to quit


## 7. Refresh and load initial data

It's possible you'll need to refresh everything, especially if changes to any models have been made with the code you are working on.

In other words...
```
docker-compose exec api python manage.py makemigrations
docker-compose exec api python manage.py migrate
docker-compose exec api python manage.py createsuperuser

docker-compose exec api python manage.py loaddata initial_data/data_partner.json
docker-compose exec api python manage.py loaddata initial_data/omoptables.json
docker-compose exec api python manage.py loaddata initial_data/omopfields.json
```