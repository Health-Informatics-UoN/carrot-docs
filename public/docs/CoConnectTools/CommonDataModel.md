
The Pythonic version of the CommonDataModel is built object-orientated in the subfolder `coconnect/cdm/`:
```
coconnect/cdm/
├── __init__.py
├── classes
│   └── __init__.py
├── decorators.py
├── model.py
├── objects
│   ├── __init__.py
│   ├── base.py
│   ├── condition_occurrence.py
│   ├── measurement.py
│   ├── observation.py
│   ├── person.py
│   └── visit_occurrence.py
└── operations.py
```

## CDM Objects

All CDM objects are defined in `coconnect/cdm/objects` and build upon a base class (`Base`, defined in `base.py`):

   * [Person](/CoConnectTools/Person.md)
   * [Condition Occurrence](/CoConnectTools/ConditionOccurrence.md)
   * [Observation](/CoConnectTools/Observation.md)
   * [Measurement](/CoConnectTools/Measurement.md)
   