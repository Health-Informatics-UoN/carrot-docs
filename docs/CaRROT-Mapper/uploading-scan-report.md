# Overview of process

A Scan Report is the basic unit representing a single data source. 
In order to create a new Scan Report in the CaRROT-Mapper system, 
one must upload a _Scan Report Form_ to the system. Once uploaded, 
an ingestion process reads this file and populates the system with
its contents. The documentation below explains this process and the
options available to users.

# The Scan Report Upload form
To upload a new _Scan Report Form_, navigate to the _Scan Reports_ 
page and then click "New Scan Report". This opens a new web form.

Firstly select an existing _Data Partner_ from the dropdown list.
This will populate the _Dataset_ field with existing _Datasets_
linked to this _Data Partner_. These are grouped by _Project_ that 
the user has access to. Select the correct _Dataset_ that the new
_Scan Report_ should belong to.

!!! note "Creating a new Dataset"
    If there is no appropriate _Dataset_ already existing, click the 
    _Add New_ button to the right of the _Dataset_ dropdown. This opens
    a panel.

    Enter a name for the _Dataset_ (which must be globally unique - if 
    you receive an error then it is likely that this _Dataset_ name is 
    already taken), and select at least one _Project_ to which it will 
    be associated. Then select the visibility, editor and admin options
    as appropriate (see 
    [Projects, Datasets, Scan Reports, and Permissions ](CaRROT-Mapper/projects-datasets-and-scanreports) 
    for an explanation of these. The _Dataset_ creator is automatically 
    assigned as an editor.

    Finally, click the "Add new Dataset" button 
    to create the new _Dataset_.

## Setting visibility/viewers and editors - permissions
Once a _Dataset_ has been selected, select the appropriate 
permissions for the _Scan Report_. The user performing the upload
is set as the _Scan Report_'s Author, and has administrator rights 
to the _Scan Report_. Additional users can be set as editors, and,
if the _Scan Report_ is set as _RESTRICTED_, viewers. Note that 
if a user has a given level of access to the _Dataset_, then they 
inherit that same access to the _Scan Report_ automatically.

## Upload Form
Choose a Scan Report in the "WhiteRabbit ScanReport" field, and 
optionally choose a dictionary file in the "Data Dictionary" field.
The formats of these two files are as described below. Then click 
the "Submit" button to begin the upload process. This will launch an
instance of the [ProcessQueue](../AzureFunctions/ProcessQueue.md).

## The Scan Report file format
The Scan Report File should be in the format as output by the 
White Rabbit tool. Broadly, this is an Excel .xlsx file with the 
following non-exhaustive format. At upload, a series of checks are
run upon the file to ensure it meets certain standards before it
is further processed, and an error message is presented if these 
checks fail.

1. `.xlsx` file format
2. The first sheet is 'Field Overview' where:
      1. There is no empty row or column at the start of the sheet.
      2. The first 10 column headers are:
      "Table", 
      "Field",
      "Description",
      "Type",
      "Max length",
      "N rows",
      "N rows checked",
      "Fraction empty",
      "N unique values",
      "Fraction unique"
      3. A contiguous group of rows is associated to each table in the 
      dataset. Each group should be separated from the next by a 
      blank row. There is no blank between the header row and the 
      first row of the first group. Two contiguous blank rows will be 
      detected as the end of the sheet, and no further entries will be 
      added.
      4. Within each group, a single row corresponds to a single field 
      in the associated table.
      5. Each table name in the 'Field Overview' table must be associated 
      to a single sheet with identical name. This imposes a 30-character 
      limit on the table names including `.csv` ending.
3. 2 additional sheets named 'Table Overview' and '_' must also be 
present, although they are never used.
4. In each of the sheets associated to a table from the 'Field Overview'
tab:
   1. Each field in the table is represented by 2 columns in the sheet.
   The first column has in its header row the name of the field, while
   the second column is always headed 'Frequency'. Thus a typical sheet
   might be headed with `"Person", "Frequency", "DOB", "Frequency", 
   "Observation", "Frequency"` etc.
   2. The fields in the sheet must match exactly with the field names
   provided for this table in the 'Field Overview' sheet.
   3. Each pair of columns is populated by all values that appear in the 
   given field, with their corresponding frequency. Usually, this 
   frequency should be no less than 5 due to applying count thresholding.
   4. Pairs of columns associated to a given field can differ in length
   from pairs of columns associated to other fields.
   5. An entry in a given pair of columns which is blank in both the field
   name column and Frequency column will count as the end of the column.
   6. If all entries in a given field should be redacted, it can be helpful
   to add a single entry in the field, wth value "List truncated..." to 
   show the intent.
   7. There is no empty row or column at the start of the sheet.
   
These stipulations are summarised in the following two annotated screenshots.

The 'Field Overview' sheet:
![](images/scanreport_format1.png)

Single sheet associated to a single table:
![](images/scanreport_format2.png)

## The Data Dictionary file format
The optionally-supplied Data Dictionary file must be in .csv format, and 
with the format as defined at 
[the CO-CONNECT data standards page](https://co-connect.ac.uk/co-connect-data-files-and-meta-data-standardisation/) 
