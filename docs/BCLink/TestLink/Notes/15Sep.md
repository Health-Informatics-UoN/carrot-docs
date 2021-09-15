

Inserting each table
```
for table in person measurement observation condition_occurrence;
do
    datasettool2 delete-all-rows $table --database=bclink
    dataset_tool --load --table=$table --user=data --data_file=$table.tsv --support  --bcqueue --bcqueue-res-path=./logs/$table  bclink
done
```

Logs:
```
/data/var/lib/bcos/download/data/logs
```