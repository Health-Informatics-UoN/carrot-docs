Welcome to the documentation of the Carrot project!

!!! carrot "Carrot"
    *C*onvenient *a*nd *r*eusable *r*apid *O*MOP *t*ransformer: bringing together health data from across the UK.
    consists of two tools: [CaRROT-Mapper web tool](CaRROT-Mapper/about.md) for generating mapping rules, and 
    [CaRROT-CDM ETL tool](CaRROT-CDM/About.md) for applying mapping rules to data.

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

If you are a Data Partner using our [CaRROT-CDM ETL tool](https://github.com/HDRUK/CaRROT-CDM), please go straight to the documentation [here](CaRROT-CDM/About.md).
