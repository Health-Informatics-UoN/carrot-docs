Welcome to the documentation of the CaRROT project!

!!! carrot "CaRROT"
    *C*onvenient *a*nd *R*eusable *R*apid *O*MOP *T*ransformer: bringing together health data from across the UK

What we do in a nutshell...
```python
researcher.data = coconnect.find('patient_data',
                                 fields = [
                                            'birth_date',
                                            'ethnicity',
                                            'existing_conditions'
                                          ],
                                 anonymous=True)
```

The Carrot tools depend on a set of [data standards]()

If you are a Data Partner using our [CaRROT-CDM ETL Tool](https://github.com/HDRUK/CaRROT-CDM), please go straight to the documentation [here](CaRROT-CDM/About).
