# Carrot Documentation

[Link to documentation.](https://health-informatics-uon.github.io/carrot-docs/)

[Carrot Mapper repository.](https://github.com/Health-Informatics-UoN/Carrot-Mapper)

[Carrot CDM repository.](https://github.com/HDRUK/CaRROT-CDM)


## Develop

Use 
```
git clone --recurse-submodules git@github.com:health-informatics-uon/carrot-docs.git
```
to get the required Carrot CDM submodule.

For local dev, after creating a virtual environment:
```
pip install -r requirements
pip install docs/CaRROT-CDM/source_code/
mkdocs serve
```
