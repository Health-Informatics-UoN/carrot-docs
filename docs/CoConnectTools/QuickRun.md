
Two things are needed for the ETL: a rules json; and the input data:
```
Usage: coconnect map run [OPTIONS] [INPUTS]
```

The default syntax is to pass the rules json via the `OPTION` `--rules`, followed by a space separated list of `INPUT` files (or directories containing `.csv` files).


=== "Unix Users"

    ```
    $ coconnect map run --rules test/rules/rules_14June2021.json test/inputs/*.csv
    ```