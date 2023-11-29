## Prerequisites

- Docker
- Python Environment >3.8
- Pip
- Azure Functions Core Tools

## Getting Started

The most direct route to running the application is using the Docker quickstart.

The repository contains a docker-compose for development, after you have setup the [configuration](#configuration), just run `docker-compose up -d` to start it running. This runs the database, will build and run the web app, and Azurite emulator.

Docker will mount the `api` directory to the web app, so any changes in the Python code will be reflected in the running app. It will run on `localhost:8000`

You will need to run some commands using the Django webapp entrypoint, to access it run:
`docker exec -it carrot-mapper-web-1 bash`

## Configuration

.env files

## Database Setup

The application stack interacts with a PostgreSQL Server database, and uses code-first migrations for managing the database schema.

### OMOP Tables

You need a pre-seeded OMOP CDM database, with the schema `omop`.

### Postgres

When setting up a new environment, or running a newer version of the codebase if there have been schema changes, you need to run the migrations against your database server.

Inside the web app container, run `python manage.py migrate`.

### Seed Data

You'll need to seed the web app database with the OMOP table values, inside the web app container run `python manage.py loaddata mapping`.  

## Azure Functions

Whilst the rest of the stack runs in containers, the worker functions run directly in your Python environment.

In the project root:

1. Install the dependencies: `pip install -r requirements.txt`
2. Run the functions `func start`

## Python Environment

You can run the web app directly in a Python environment instead of the Docker image.

1. Be sure to have a separate environment for the web app and Azure functions.
2. Inside `/api`, install the dependencies `pip install -r requirements.txt`
3. Change the configuration to point to the running docker containers, for example `localhost` instead of `Azurite`.
4. Run `python manage.py runserver`.
