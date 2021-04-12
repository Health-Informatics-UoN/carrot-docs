Here is how you can restore a __single__ ScanReport..

## 1. Find 

Download the backup of the db, which contains the ScanReport you need to restore.

_See the previous section for details on how to do this_

You should use a version of mapping-pipeline with a commit hash for the master from the time the backup was created. You can do this by looking in the master commit history - https://github.com/CO-CONNECT/mapping-pipeline/commits/master?after=d277209a83fea3e5a5bd344c002acc21e85a747d+139&branch=master

And then doing:
```
#on master branch
git checkout <hash>
```


## 2. Delete

Once mapping-pipeline is running, remove all reports you don't need, so you're left with just the single report you want to restore.

## 3. Update with Master

Switch to the master branch now and run `makemigratations` to make sure any fields that have been updated get migrated.

**this gave me a lot of problems, what worked...**   

  * do `makemigrations`/`migrate` on the old hash of the master  
  * switch to the master branch  
  * go into the folder `api/mapping/migrations` 
  * do `makemigrations`/`migrate`  

^without going into the folder, it kept saying there were no changes. And `makemigrations mapping` kept overwritting the old `0001_initial.py`.

## 4. Dumpdata

Dump the data into json format for the Report, Table(s), Field(s), Value(s).
Put it in a temp folder inside `api` so it can be see when we run `loaddata`

```
mkdir api/recover/
cd api/recover/

docker-compose exec api python manage.py dumpdata mapping.scanreport --indent 6 >> scanreport.json

docker-compose exec api python manage.py dumpdata mapping.scanreporttable --indent 6 >> scanreporttable.json

docker-compose exec api python manage.py dumpdata mapping.scanreportfield --indent 6 >> scanreportfield.json

docker-compose exec api python manage.py dumpdata mapping.scanreportvalue --indent 6 >> scanreportvalue.json
```

## 5. Loaddata

On the VM, inside the `api` folder I transfered the files to `recover/MATCH/`

Then ran these commands to restore the deleted MATCH data..
```
docker-compose exec api python manage.py loaddata recover/MATCH/scanreport.json
docker-compose exec api python manage.py loaddata recover/MATCH/scanreporttable.json
docker-compose exec api python manage.py loaddata recover/MATCH/scanreportfield.json
docker-compose exec api python manage.py loaddata recover/MATCH/scanreportvalue.json

```