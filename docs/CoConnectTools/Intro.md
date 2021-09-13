
Welcome to our repo for `python` tools used by/with the CO-CONNECT project. The primary functionality is used for performing ETL on health datasets, converting them to the OHDSI Common Data Model (CDM), via a tool referred to as the _ETL Tool_ .

CO-CONNECT-Tools contains a pythonic version[^1] of the OHDSI CDM, implemented via the class `CommonDataModel`. CDM tables, such as "Person" are also defined as classes (e.g. [`Person`](/docs/CoConnectTools/Person/)) within the code base. 

[^1]: In the default setup a __slightly__ (`visit_detail_id` link has been removed from Measurement, Observation and Condition Occurrence tables) modified CDM version [`5.3.1`](https://github.com/OHDSI/CommonDataModel/releases/tag/v5.3.1) is used to define a subset of tables in python.

The primary purpose of this package is to perform ETL of a dataset based upon a supplied set of transform rules encoded within a json file.
The tools is designed to handle the output json of the CO-CONNECT Mapping-Pipeline web-tool known as CCOM. Though other output formats are supported, the __default__ output of the tool writes data to `tsv` files.

## Getting Started

To get started with the ETL, follow the instructions for installing the co-connect-tools package and running the ETL-Tool on the following pages:

!!! success "ETL"
    1. [Installing](/docs/CoConnectTools/Installing/)  
    1. [Running ETL-CDM](/docs/CoConnectTools/ETL-Tool/)  

## Guides

More detailed guides and documentation for users and developers can be found in the following locations:

<center>
[User Guide](/docs/CoConnectTools/Installing/){ .md-button .md-button--primary}
[Developer Guide](/docs/CoConnectTools/CommonDataModel/){ .md-button .md-button--secondary }
</center>

