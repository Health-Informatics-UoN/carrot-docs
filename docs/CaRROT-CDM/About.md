
Welcome to our repo for `python` tools used by/with the CaRROT project. The primary functionality is used for performing ETL on health datasets, converting them to the OHDSI Common Data Model (CDM), via a command line interface.

CaRROT-CDM contains a pythonic version[^1] of the OHDSI CDM, implemented via the class `CommonDataModel`. CDM tables, such as "Person" are defined as classes (e.g. [`Person`](/docs/CoConnectTools/Person/)) within the code base. 

[^1]: In the default setup a __slightly__ (`visit_detail_id` link has been removed from Measurement, Observation and Condition Occurrence tables) modified CDM version [`5.3.1`](https://github.com/OHDSI/CommonDataModel/releases/tag/v5.3.1) is used to define a subset of tables in python.

![](../../images/data-mapping.png)


The primary purpose of the co-connect-tools package is to {==Extract==} input datasets and {==Transform==} them using __mapping rules__ defined in a json file, outputting formatted datasets in `tsv` format that can be {==Loaded==} into a database or other destination ({==ETL==}).


## Getting Started

To get started with the [ETL](/docs/CaRROT-CDM/ETL/About/) process, follow the instructions for installing the co-connect-tools package and running the ETL on the following pages:

!!! success "ETL"
    1. [Frequently Asked Questions](/docs/CaRROT-CDM/FAQ/)  
    1. [Installing](/docs/CaRROT-CDM/Installing/)  
    1. [About the ETL Process](/docs/CaRROT-CDM/ETL/About/)
    1. [Guide](/docs/CaRROT-CDM/ETL/ETL-Guide)  
	    1. [Configuration](/docs/CaRROT-CDM/ETL/Yaml)
        1. [Extract](/docs/CaRROT-CDM/ETL/Extract/)
        1. [Transform](/docs/CaRROT-CDM/ETL/Transform/)
        1. [Load](/docs/CaRROT-CDM/ETL/Load/)
    
    
## Guides

More detailed guides and documentation for users and developers can be found in the following locations:

<center>
[User Guide](/docs/CoConnectTools/Installing/){ .md-button .md-button--primary}
[Developer Guide](/docs/CoConnectTools/CommonDataModel/){ .md-button .md-button--secondary }
</center>
