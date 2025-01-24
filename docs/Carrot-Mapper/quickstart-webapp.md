## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [MS Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer/#Download-4)

## Getting Started

The most direct route to running the application locally is using the Docker quickstart.

The repository contains a docker-compose for development, so after you have setup the [configuration](#configuration), just run

```bash
docker-compose up -d

or

docker compose up
```

to start the application stack. This runs the database, Azurite emulator, Azure functions (`workers`) and will build Carrot's backend (`web`) and frontend (`next-client`). After the command run successfully, Carrot can be accessed through `http://localhost:8000/`

<!-- Should be changed to http://localhost:3000/ after the PR about next-auth merged -->

Docker will mount the `app/api` directory to the web app container, so any changes in the Python code of the backend will be reflected in the running application.

Any code changes in the frontend app, i.e., `next-client-app`, will be reflected as well in the UI, thanks to the `app/next-client-app` directory mounted on Docker environment.

Note: You may need to refresh the browser to see the UI changes after code changed in `next-client-app`.

 <!--After React client app to be deleted, the note above may be removed, cause the hot reload function of NextJS may then be fully applied  -->

## Configuration

There are a few steps to set up the data and environment for Carrot to run with Docker compose.

### OMOP CDM setting

You need a pre-seeded OMOP CDM database inside the database of Carrot.

To do this, add a folder named `vocabs` in the `root` directory of Carrot, then place there vocabulary files downloaded from [Athena](https://athena.ohdsi.org/vocabulary/list). This should be done before running `docker compose up` for the first time.

When running `docker compose up`, OMOP CDM will be created and loaded to Carrot's database, thanks to [OMOP Lite](https://github.com/andyrae/omop-lite/pkgs/container/omop-lite) container.

### Preparing initial data

You need to seed the web app database with the OMOP table and field names. You can do this by using your terminal and access the `web` container on Docker or you can use Docker Desktop directly. The following instructions is for the latter method.

Navigate to your Docker Desktop, choose `web` container, choose `Exec` tab, then run: `python manage.py loaddata mapping filetypes`.

To add a new admin user (Django superuser) run: `python manage.py createsuperuser`.

Following this order, create inital `Data Partner`, `Dataset` (with admin as the admin user) and `Project` (with `Datasets` as the created dataset and `Members` as the admin user) by using Django Admin panel `http://localhost:8000/admin/`

### Azure Functions

#### Local Storage

You need to set up a local storage for local development of Carrot

With the `workers` container in Docker Desktop is running, open `MS Azure Storage Explorer`, and create the following `queues` (under the tab `Storage Accounts - Emulator - Queues`) and `blob containers` (under the tab `Storage Accounts - Emulator - Blob Containers`)

For `queues`:

```
rules-local
scanreports-local
rules-exports-local
uploadreports-local
```

For `blob containers`:

```
scan-reports
data-dictionaries
rules-exports
```

This setting will create the local storages for Azure functions of Carrot.

#### Authentication for Azure functions

For local developement using Docker, when `web` container wants to send request to `workers` container, an authentication need setting up. This should be done before running `docker compose up` for the first time.

In the `app/workers` directory, add a folder named `Secrets`, and then inside that folder, add a new file named `rulestrigger.json` with the following content:

```json
{
  "keys": [
    {
      "name": "default",
      "value": "rules_key",
      "encrypted": false
    }
  ]
}
```

You should now be setup to run the [user quickstart](quickstart.md) using the credentials of the Django Admin user.
