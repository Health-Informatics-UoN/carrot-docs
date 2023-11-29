## Prerequisites

- Docker
- Python Environment >3.8
- Pip
- Azure Functions Core Tools
- Azure CLI

## Getting Started

The most direct route to running the application is using the Docker quickstart.

The repository contains a docker-compose for development, so after you have setup the [configuration](#configuration), just run `docker-compose up -d` to start the application stack. This runs the database, Azurite emulator, and will build and run the web app container.

Docker will mount the `api` directory to the web app container, so any changes in the Python code will be reflected in the running application.

You will need to run some commands using the web app entrypoint, to access the container run: `docker exec -it carrot-mapper-web-1 bash`

## Configuration

The application is configured through environment variables, and a `local.settings.json` file.

Rename the existing `sample-env.txt` to `.env`, and `sample-local.settings.json` to `local.settings.json` to use the default values with the Docker setup.

## Database Setup

The application stack interacts with a PostgreSQL Server database, and uses code-first migrations for managing the database schema.

### OMOP Tables

You need a pre-seeded OMOP CDM database, with the schema `omop`.

### Postgres

When setting up a new environment, or running a newer version of the codebase if there have been schema changes, you need to run the migrations against your web app database.

Inside the web app container, run: `python manage.py migrate`.

### Seed Data

You need to seed the web app database with the OMOP table and field names, inside the web app container run: `python manage.py loaddata mapping`.  

To add a new admin user run: `python manage.py createsuperuser`.

## Azure Functions

Whilst the rest of the stack runs in containers, the worker functions run directly in your Python environment.

To create the storage containers and queues, use the Azure CLI:

- `az storage container create -n scan-reports --connection-string <CONNECTIONSTRING>`
- `az storage container create -m data-dictionaries --connection-string <CONNECTIONSTRING>`
- `az storage queue create -n nlpqueue --connection-string <CONNECTIONSTRING>`
- `az storage queue create -n scan-reports --connection-string <CONNECTIONSTRING>`

To run the functions, in the project root:

1. Install the dependencies: `pip install -r requirements.txt`
2. Run the functions: `func start`

## Python Environment

You can run the web app directly in a Python environment instead of the Docker image.

1. Be sure to have a separate Python environment for the web app and Azure functions.
2. Inside the `/api` directory, install the dependencies: `pip install -r requirements.txt`
3. Change the environment [configuration](#configuration) to point to the running docker containers, for example `localhost` instead of `Azurite`.
4. TODO: Javascript.
5. To run the app: `python manage.py runserver`.
