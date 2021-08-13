
The command-line ETL (`coconnect map run`) can be ran as a module via `-m coconnect.cli.cli map run`. To include [`cProfile`](https://docs.python.org/3/library/profile.html), the following command can be executed to run the ETL-Tool while using `cProfile`:
```
python3 -m cProfile -m coconnect.cli.cli map run --rules rules.json input_data/
```