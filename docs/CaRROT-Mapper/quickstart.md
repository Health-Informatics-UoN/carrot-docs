!!! warning
    This page is a work in progress, and should not be relied upon.

This Quickstart guide will guide you through the usage of CaRROT-Mapper. Specifically, it covers:

1. [Gaining access](#Gaining Access)
2. Uploading a new Scan Report
3. Navigating within a Scan Report
4. Creating Mapping Rules
5. Downloading Mapping Rules
6. Understanding Datasets and Projects
7. [The Home page](#The-Home-Page)
8. Administrating Datasets and Scan Reports
9. Managing Project access
10. Analysing Mapping Rules
11. Getting help

This Quickstart guide is intended as a brief introduction to each of the areas. 
Users requiring more details should follow the links or search the documentation for their specific query.
Developers should use the Developer Guide for technical documentation.

# Gaining Access
The deployed CaRROT-Mapper is available at https://ccom.azurewebsites.net.

User login is required to access the site. 
A user account can be requested from the site administrator. 

A user must be a member of at least one Project.
Project membership is currently handled by the site administrator.

Once logged in, the user is presented with the [Home page](#The-Home-Page). 
This is a simple dashboard displaying statistics about the data the user has
access to.

# My First Scan Report
## Upload
To begin, navigate using the bar across the top of the screen to "Scan Reports > Upload New Scan Report" to open the Scan Report Upload form.

Fill in the details as required, and upload a valid Scan Report File. 
You may optionally provide a data Dictionary file.
If an error message occurs, fix the error in the Scan Report File (or Data Dictionary file if provided) and retry upload.
!!! note
    Not all possible errors are checked in the files. If you are directed to the Scan Reports page after upload, this indicates that all pre-checks have completed.
    However, other errors can be checked only during the processing of the file. If these occur, the `Status` of the Scan Report may change to `Upload Failed`, or it may stay as `Upload in Progress` forever (over 30 minutes indicates an error).
    If this occurs, please check your files yourself for possible errors, and if that fails then contact the system administrator to check the logs.

Once a valid Scan Report File is uploaded, the user will be redirected to the Scan Report list page.
This should show the newly created Scan Report at the top of the table.

After upload, an automated task is launched to ingest the contents of the file and perform a number of processing steps. 
This process can take several minutes for large Scan Report Files, with a timeout of 30 minutes.
While this process is running, the "Status" visible to the right of the row will be "Upload in Progress".
Once the process completes, the status will change (once the page is reloaded) to "Upload Complete".
In the case of an unrecoverable error in processing, the status will be set to "Upload Failed".
Please note that sometimes errors in processing do not result in the status being set to "Upload Failed", with the status incorrectly remaining as "Upload in Progress".
If this status remains for more than 30 minutes then you can be sure that the process has failed.
Please report this to the development team with a copy of the Scan Report File being used, to help us improve the checks run.

## Navigation
While the Scan Report continues to be processed, the contents will be populated over time.
Click on the name of the Scan Report to navigate to the list of Tables (Tables List) within the Scan Report.
Assuming that processing has progressed sufficiently far, you can click on the name of each Table to navigate to the lift of Fields (Field List) within that Table.
Similarly, clicking on a Field name will display the list of Values (Value List) within that Field.
In this way, the structure of the Scan Report can be navigated.
Return via the breadcrumbs along the top of the page.

The Field List and Value List pages will display any Concepts mapped to the Fields and Values in the Scan Report.
