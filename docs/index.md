Welcome to the documentation of the CO-CONNECT project!

!!! co-connect "CO-CONNECT"
    Bringing together COVID-19 data from across the UK

What we do in a nutshell...
```python
reasearcher.data = coconnect.find('patient_data',
                                  fields = [
                                             'birth_date',
                                             'ethnicity',
                                             'existing_conditions'
                                           ]
                                  anonymous=True)
```

If you are a Data Partner using our [ETL Tool](https://github.com/CO-CONNECT/co-connect-tools), please go straight to the documentation [here](/docs/CoConnectTools/About/).
