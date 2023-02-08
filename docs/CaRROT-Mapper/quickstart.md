# Quickstart Guide

!!! warning
    This page is a work in progress, and should not be relied upon.

This Quickstart guide will guide you through the usage of CaRROT-Mapper. Specifically, it covers:

1. [Gaining access](#gaining-access)
2. Uploading a new Scan Report
3. Navigating within a Scan Report
4. [Creating Mapping Rules](#creating-mapping-rules)
5. [Downloading Mapping Rules](#downloading-mapping-rules)
6. Understanding Datasets and Projects
7. [The Home page](#the-home-page)
8. Administrating Datasets and Scan Reports
9. Managing Project access
10. Analysing Mapping Rules
11. Getting help

This Quickstart guide is intended as a brief introduction to each of the areas. 
Users requiring more details should follow the links or search the documentation for their specific query.
Developers should use the Developer Guide for technical documentation.

## Gaining Access
The deployed CaRROT-Mapper is available at [https://ccom.azurewebsites.net](https://ccom.azurewebsites.net).

A user account is required to access the site; one can be requested from the site administrator. 

Users must be a member of at least one Project.
Project membership is currently handled by the site administrator.

Once logged in, the user is presented with the [Home page](#the-home-page). 
This is a simple dashboard displaying statistics about the data the user can access.

## Upload a first Scan Report
To begin, navigate to "Scan Reports > Upload New Scan Report" using the navbar at the top of the screen.

**TODO:** Provide a sample ScanReport file and Data Dictionary file.

Fill in the details as required, and upload a valid Scan Report File. 
You may optionally provide a Data Dictionary file.
If an error message occurs, fix the error in the Scan Report File (or Data Dictionary file if provided) and try again to upload.

!!! caution
    Not all possible errors in the files can be checked before upload. If you are directed to the Scan Reports page after upload, this indicates that all pre-checks have completed.
    However, other errors can be checked only during the processing of the file after upload. If these occur, the `Status` of the Scan Report may change to `Upload Failed`, or it may stay as `Upload in Progress` forever (over 30 minutes indicates an error).
    If this occurs, please check your files yourself for possible errors, and if that fails then contact the system administrator to check the logs.

Once a valid Scan Report File is uploaded, the user will be redirected to the Scan Report list page.
This should show the newly created Scan Report at the top of the table.

After upload, an automated task is launched to ingest the contents of the file and perform a number of processing steps. 
This process can take several minutes for large Scan Report Files.
While this process is running, the "Status" visible to the right of the row will be "Upload in Progress".
Once the process completes, the status will change (once the page is reloaded) to "Upload Complete".
In the case of an unrecoverable error in processing, the status will be set to "Upload Failed".

!!! Caution
    Please note that sometimes errors in processing do not result in the status being set to "Upload Failed", with the status incorrectly remaining as "Upload in Progress".
    If this status remains for more than 30 minutes then you can be sure that the process has failed.
    Please report this to the development team with a copy of the Scan Report File being used, to help us improve the checks run.

Wait for approximately one minute to ensure the example Scan Report has been fully processed.
Refresh the Scan Reports page to check that the Status of your Scan Report has been set to "Upload Complete" before continuing.

## Navigation
After upload, the user is directed to the Scan Reports page before the new Scan Report has been fully processed. 
While the Scan Report continues to be processed, the contents visible on CaRROT-Mapper will be populated over time.

Click on the name of the Scan Report to navigate to the list of Tables ("Tables List") within the Scan Report.
As Tables are processed, the user can click on the name of each Table to navigate to the list of Fields ("Field List") within that Table.
Similarly, clicking on a Field name in a Table will display the list of Values ("Value List") within that Field.
In this way, the structure of the Scan Report can be navigated.

Sometimes a list is too large to be displayed all at once. In this case, the list will be displayed over a number of pages. Use the grey buttons at the top of the page to navigate through the pages.

**TODO**: note somewhere how to navigate pagination URLs.

Use the breadcrumbs at the top of the page to quickly navigate back up the Scan Report.

## Creating Mapping Rules
The example Scan Report and Data Dictionary will have added some OMOP Concepts to fields and values in the Scan Report.
For example, **TODO**(field) shows the OMOP code **TODO**, and **TODO**(value) shows the OMOP code **TODO**.

You can hover over the Concepts to show more details about them (currently this just shows further details of how the Concept was added - in this case, they are added by vocab as defined in the Data Dictionary we supplied)

### Setting Person ID and Date Event for each table
The first task of creating Mapping Rules for a Scan Report is to set the Person ID and Date Event fields of each table.

Navigate to the Tables view of your Scan Report by clicking on its name on the Scan Report list page.
This page will show the list of all the tables in the Scan Report, while the `PERSON ID` and `EVENT DATE` columns are empty. 
On the right-hand side, press the `Edit Table` link on the first table (`Demographics.csv`).
In the first drop-down (Person ID), select `PersonID` which is one of the fields as reported by the Scan Report file.
In the second drop-down (Date Event), select `Date_of_Birth`.
Then press the `Update Demographics.csv` button. 
You will be taken back to the Tables list, and the `PERSON ID` and `EVENT DATE` columns are now populated in the `Demographics.csv` row.

Now do the same for `Covid.csv`, selecting `PersonID` and `Date`.

The Tables list will now show both tables have filled `PERSON ID` and `EVENT DATE` columns, and we are ready to proceed with generating Mapping Rules

### Generate Initial Mapping Rules
Navigate to the Mapping Rules page associated to your Scan Report. 
This can be either **(a)** from the Scan Reports page - press the `Rules` button; or **(b)** from any of the Tables, Fields or Values pages - press the `Go to Rules` button near the top of the page.
This page will contain an empty table with the contents `Nothing`. 

This might be surprising, as some of the Fields and Values already have associated Concepts based on the Dictionary file and reuse of previously seen mappings.

Mapping Rules cannot be generated without the Person ID and Date Event being set.
During the initial upload and processing, these were not set, and so the Mapping Rules associated to the Concepts could not be generated.
Now that `Person ID` and `Date Event` are set for each of the tables, the Mapping Rules associated to those Concepts can be generated.

Press the green `Refresh Rules` button at the top of the page. 
After a few moments, the table will be populated by the Mapping Rules associated to the Concepts already mapped (in this example, there should be 16 Mapping Rules generated).

!!! note
    If you don't see any Mapping Rules appear after refresh, this is usually an indication that you have not set the `Person ID` and `Date Event` on the tables.

Note that there are 5 or 6 rows in the table associated to each Concept. 
In the `Rule ID` column in the table, there are 5 or 6 with a given ID. 
This fully describes how to map the source term to the OMOP standard.

A Summary view is available using the `Show Summary view` button at the top of the page. 
This shows only one of the Rules associated to each source-concept pair, which can be useful for reviewing.

!!! note
    The Summary view only shows Rules that are shown on the current pagination view. 
    Often this means only 5 or 6 Rules are visible in Summary View at any one time.
    By editing the URL so that `page_size=300`, for example, one can increase the number of Rules visible in Summary View at one time.

All the Mapping Rules associated to the Concepts, which were automatically added to our Scan Report by using a Data Dictionary file, are now available.

!!! tip
    The `Refresh Rules` button will only need to be pressed once, as (see below) any later addition or removal of Concepts will be immediately reflected in the Mapping Rules.

!!! warning
    If the Scan Report contains a large number of associated Concepts (more than 100, say) the `Refresh Rules` button can timeout before it finishes its task.
    In this case, please be warned that the list of Mapping Rules generated may only be partial.
    Currently, there is no reporting of when this happens, but this will be addressed in a future update.
    For the time being, if you have a Scan Report with a large number of Concepts, please contact the system administrator who can run the required task offline. This can take a while!
    In a future update, the Refresh Rules button will trigger a background task that will 
    (a) be a lot faster; and 
    (b) not suffer from the same timeout issue.  

### Add further Concepts

Further mappings can be added manually.

Navigate to the `Demographics.csv` table, and the `Sex` field. 
There will be a table with 3 rows: `F`, `M` and an empty row.
Note that, thanks to our Data Dictionary file, the `Value description` column has been populated with helpful information.
It indicates that `F` means "Female", and `M` "Male".
To add the correct OMOP Concepts, click in the text box to the left of the `Add` button on the right-hand side of the screen, in the row associated with `F`.
Type `8532` and press `Add`. After a moment, a green `Success: Mapping Rules created` banner appears, along with a blue Concept tag inside the table.
Similarly, add `8507` for the `M` row.

Now that these Concepts have been added, press the `Go to Rules` button.
Note that new rows have been added associated to the `F` and `M` values. These were generated at the time of adding the Concepts on the previous page.

You can also try _removing_ a Concept by pressing the grey `x` on the right of any Concept tag, then return to the Rules page to see that the associated Mapping Rules have been deleted.

## Downloading Mapping Rules

The final task in the simplest end-to-end process in CaRROT-Mapper is to download the Mapping Rules in a form that can be used as input to CaRROT-CDM (which will perform the transformations on the data).

Navigate to the Mapping Rules page, and press the `Download Mapping JSON` button.
This will download to your machine a `.json` file containing the Mapping Rules.

To view a more human-readable version, press the `Download Mapping CSV` for a `.csv` file instead. This however cannot be used as input to CaRROT-CDM.

Finally, a diagram of the Mapping Rules can be viewed and downloaded using the yellow `View Map Diagram` and red `Download Map Diagram` buttons.
