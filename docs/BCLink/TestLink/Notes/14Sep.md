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

### dataset_tool

The help gives the following information:
```
dataset_tool --load --table=ds100123 --user=<test_user>
    --data_file=ds100123.txt [or --data_file_list=my_filelist.txt]
    [--extra='...'] [--no-backup] [--support]
    [--move-original-file] [--grab-original-file] [--immediate-write]
    [--force-prepare-data] [--job-parameters=<extra-job-params-file>]
    [--bcqueue] [--bcqueue-res-path='job-result-path'] [--niceness=10]
    [--device=XX] [--subj_filter=xx] [--samples_table=xx] database
```

Some additional information is also given:
```
Arguments for load mode:
  --table=ds100123
     Must contain the name of the destination table.
  --no-backup
     Don't do a input file backup. Product dataset data needs no backup.
  --support
     Uses supp-dataload-batch queue instead of dataload-batch, 
     or supp-dataupd2-batch queue instead of dataupd2-batch.
  --immediate-write
     Avoid job queue altogether: calls simpleupd directly.
  --user=test_user
     Must contain the SQL user, as whom the data will be inserted into the data set.
  --data_file=ds100123.txt
     Must contain the data file in textdb format, to be loaded into the database.
  --data_file_list=one_line_per_uploadfile.txt Upload multiple files with one submit.
  --extra=... Add extra parameters into multiple file upload (--data_file_list) supplied. 
  --update_type=direct or --update_type=incremental:      incremental mode skips row updates with missing alleles.
  --move-original-file
     Moves the original file in --data_file=<file> into /data/var/lib/bcos/tmp/ to be used on load operation.
  --grab-original-file
     Uses the file <file> in place, so it will not move the file for the following upload job.
  --subj_filter=[--no,--skip,--strict]
     '--no' means no samples filter,
     '--skip' means pass mapped rows thrue, others are discarded,
     '--strict' means if one unmappable row is found, don't do the upload.
  --action_mode=[load,insert,?] (please verify the meaning first if you use this).
  --device=UPLOAD_DEVICE
  --samples_table=samples means that skip / strict upload is done against this samples table.
```

### datasettool2 load

```
datasettool2 load --dataset=<DATASET> [--datafile=<FILE> | --source-dataset=<SOURCE_DATASET>]
                  [--submission-id] [--queue] [--resfolder=PATH] [--force-prepare-data]
		  [--timing] [--hierarchical-timing] [--sqltimeout=<SEC>]
		  [--database=<DATABASE>] [--user=<USER>] [--developer=<DEVELOPER>]
		  [--pooledconnection] [--job-id=<JOB>] [--job=<JOB>]
```

`--resfolder=<PATH>` : appears to have no effect, all the jobs end up in `/data/var/lib/bcos/download/<user>`, this would be useful if you could specify the job output path.


### datasettool2 load2

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