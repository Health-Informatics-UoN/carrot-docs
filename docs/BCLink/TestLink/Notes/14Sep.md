## Working directory setup

In the directory:
```
/usr/lib/bcos/OMOP-test-data/calum_tests_14Sep
```

I have the `tsv` file created by the ETL-Tool that I want to test uploading `person.tsv`.


## Clean the database

```
datasettool2 delete-all-rows ds100394 --database=bclink
```

## CommandLine Options

```
dataset_tool --load --table=ds100123 --user=<test_user>
    --data_file=ds100123.txt [or --data_file_list=my_filelist.txt]
    [--extra='...'] [--no-backup] [--support]
    [--move-original-file] [--grab-original-file] [--immediate-write]
    [--force-prepare-data] [--job-parameters=<extra-job-params-file>]
    [--bcqueue] [--bcqueue-res-path='job-result-path'] [--niceness=10]
    [--device=XX] [--subj_filter=xx] [--samples_table=xx] database
```


```
datasettool2 load --dataset=<DATASET> [--datafile=<FILE> | --source-dataset=<SOURCE_DATASET>]
                  [--submission-id] [--queue] [--resfolder=PATH] [--force-prepare-data]
		  [--timing] [--hierarchical-timing] [--sqltimeout=<SEC>]
		  [--database=<DATABASE>] [--user=<USER>] [--developer=<DEVELOPER>]
		  [--pooledconnection] [--job-id=<JOB>] [--job=<JOB>]
```

`--resfolder=<PATH>` : appears to have no effect, all the jobs end up in `/data/var/lib/bcos/download/<user>`, this would be useful if you could specify the job output path.


```
datasettool2 load2 --dataset=<DATASET> --datafile=<FILE> [--force-prepare-data]
                  [--timing] [--hierarchical-timing] [--sqltimeout=<SEC>]
		  [--database=<DATABASE>] [--user=<USER>] [--developer=<DEVELOPER>]
		  [--pooledconnection] [--job-id=<JOB>] [--job=<JOB>]
```


## Insert into the database

Using `datasettool2 load2`

```
[bcos_srv@link-test-dt calum_tests_14Sep]$ datasettool2 load2 --dataset=ds100394 --datafile=`pwd`/person.tsv --database=bclink --user=data 
Upload started: dataset=PERSON_V2 (ds100394), file=/usr/lib/bcos/OMOP-test-data/calum_tests_14Sep/person.tsv, id=9 , resultfolder=datasettool2_uploads/9
Upload done: dataset=PERSON_V2 (ds100394), file=/usr/lib/bcos/OMOP-test-data/calum_tests_14Sep/person.tsv, id=9 , resultfolder=datasettool2_uploads/9
```

These jobs get run on the BCLink queue, and the output results folder can be seen here:

By using `--user=data` I can see the job via the GUI too.

```
$ ls -ltr /data/var/lib/bcos/download/data/datasettool2_uploads/9
total 8
-rw-rw---- 1 bcos_srv bcos_srv 103 Sep 14 17:13 invalid_values.dat
-rw-rw---- 1 bcos_srv bcos_srv 755 Sep 14 17:13 cover.10126
```


Using `datasettool2 load`

```

```