
Welcome to our repo for `python` tools used by/with the CO-CONNECT project. The primary functionality is used for performing ETL on health datasets, converting them to the OHDSI Common Data Model (CDM), via a command line interface.

CO-CONNECT-Tools contains a pythonic version[^1] of the OHDSI CDM, implemented via the class `CommonDataModel`. CDM tables, such as "Person" are defined as classes (e.g. [`Person`](/docs/CoConnectTools/Person/)) within the code base. 

[^1]: In the default setup a __slightly__ (`visit_detail_id` link has been removed from Measurement, Observation and Condition Occurrence tables) modified CDM version [`5.3.1`](https://github.com/OHDSI/CommonDataModel/releases/tag/v5.3.1) is used to define a subset of tables in python.

![](../../images/data-mapping.png)


The primary purpose of the co-connect-tools package is to {==Extract==} input datasets and {==Transform==} them using __mapping rules__ defined in a json file, outputting formatted datasets in `tsv` format that can be {==Loaded==} into a database or other destination ({==ETL==}).

## Manual ETL V.S. Automated ETL

This process can be performed as either an automated process, or by taking more manual control of each step.

* Automated ETL will be more preferable to users who are expecting regular data dumps.  
    * Users can specify the location of where new data will be dumped and the automated (background) process will be able to detect changes and perform the ETL.   
* Manual ETL may be more prefereable to users who are only extract, transforming and loading data once.
    * They shall handle the extraction of data, manual running of the "transform" (via also co-connect-tools) and the manual loading of the data into a database (e.g. BCLink).    
        * Available as a command line tool   
        * Available as a GUI interface    

Jump to sections for manual and automated running:
<center>
[Manual](/docs/CoConnectTools/ETL/Automation/){ .md-button .md-button--primary}
[Automated](/docs/CoConnectTools/ETL/Transform/){ .md-button .md-button--secondary }
</center>



## Getting Started

To get started with the [ETL](/docs/CoConnectTools/ETL/About/) process, follow the instructions for installing the co-connect-tools package and running the ETL on the following pages:

!!! success "ETL"
    1. [Installing](/docs/CoConnectTools/Installing/)  
    1. [About the ETL Process](/docs/CoConnectTools/ETL/About/)
    1. [Automated ETL](/docs/CoConnectTools/ETL/Automation/)
    1. Manual ETL
        1. [Extract](/docs/CoConnectTools/ETL/Extract/)
        1. [Transform](/docs/CoConnectTools/ETL/Transform/)
        1. [Load](/docs/CoConnectTools/ETL/Load/)
    
    
## Guides

More detailed guides and documentation for users and developers can be found in the following locations:

<center>
[User Guide](/docs/CoConnectTools/Installing/){ .md-button .md-button--primary}
[Developer Guide](/docs/CoConnectTools/CommonDataModel/){ .md-button .md-button--secondary }
</center>

