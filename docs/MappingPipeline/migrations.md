To build a database migration and apply it, we can do the following:

1. Build and run the mapping tool locally, where the `.env` file should 
   contain the credentials for the dev DB.
```
docker build --tag ccom . && docker run -it --volume $PWD/api:/api 
   --env-file .env -p 8080:8000 ccom
```

2. In another terminal tab, run the following the get the name of the 
   running container and open a shell within it (be careful here if 
   you have more than one container running in docker! - It just grabs 
   the first container in the list)
```
docker exec -it `docker container ls | awk 'NR==2 {print $1}'` /bin/bash
```

3. Inside the shell, to create the migrations:
```
cd /api
python manage.py makemigrations mapping
```

4. To show the changes that will be applied:
```
python manage.py migrate --plan mapping

```

5. To apply the changes:
```
python manage.py migrate mapping
```
