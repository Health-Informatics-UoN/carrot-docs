## 1. Initial Setup

The following is a walkthrough of how to run an automated ETL process using test data available as part of the package.

It is assumed that BCLink systems has already been an installed on a host machine. 

??? example "Setting up a working directory"
    To use ETL with `bclink`, you must be the user `bcos_srv`, i.e. when ssh'd into the machine hosting `bclink`:
    ```
    ssh <IP address of host machine>
    sudo -s
    su - bcos_srv
    ``` 
    
    The best practise is to create a working directory in the following location:
    ```
    mkdir /usr/lib/bcos/MyWorkingDirectory/
    cd /usr/lib/bcos/MyWorkingDirectory/
    ```
    
??? example "Installing co-connect-tools"  
    It is also best practise to setup a virtual python environment and install the tool:

    ```
    python3 -m venv automation
    source automation/bin/activate
    pip install pip --upgrade
    pip install co-connect-tools
    ```

    Check the version:
    ```
    coconnect info version
    ```
    Which should display version `>=0.4.1` for the automation to work.

    [Detailed Installation Instructions](/docs/CoConnectTools/Installing/){ .md-button .md-button--primary}

??? example "Get input data"

    Test data can be found in th `data_folder`:
    ```
    $ ls $(coconnect info data_folder)/test/
    automation  expected_outputs  inputs  rules  scan_report
    ```

    Copy the data (or use a symbolic link) into your current working directory:
    ```
    $ pwd
    /usr/lib/bcos/MyWorkingDirectory
    $ mkdir input_data
    $ cp -r $(coconnect info data_folder)/test/inputs/original input_data/001
    $ ls input_data/001
    covid19_antibody.csv  Covid19_test.csv  Demographics.csv  Symptoms.csv  vaccine.csv
    ```

??? example "Get a rules `json` file"

    As associated example mapping rules `json` file for this test dataset can be found and copied over to the working directory:
    ```
    $ cp -r $(coconnect info data_folder)/test/rules/rules_14June2021.json rules.json
    $ coconnect display json rules.json |& head -15
    {
      "metadata": {
            "date_created": "2021-06-14T15:27:37.123947",
            "dataset": "Test"
      },
      "cdm": {
            "observation": {
                  "observation_0": {
                        "observation_concept_id": {
                              "source_table": "Demographics.csv",
                              "source_field": "ethnicity",
                              "term_mapping": {
                                    "Asian": 35825508
                              }
                        },

    ```

## 2. Setup BCLink tables

??? example "via the GUI"

    Log in to the bclink web-gui as the 'data' user.

    Create a table (example `person`)
    ![1](../../images/bclink_gui/1.png)

    Make a not of the dataset name and ID of the dataset i.e. `ds100421`
    ![2](../../images/bclink_gui/2.png)



??? example "via the command line"

    Creating a person table called `person_test_data_v1`
    ```
    cd /usr/lib/bcos/OMOP
    $dataset_tool --create --form=PERSON --table=person_test_data_v1 --setname='PERSON_TEST_DATA_V1' --user=data bclink
    Created dataset with table person_test_data_v1
    ```

??? example "via co-connect-tools"
    < document ?>

## 3. Setup a `yaml` configuration file

??? example "Complete yaml"
    Create a new file called `config.yaml` that will do the following:

    1. Look every 1 minute in the folder `input_data` for any subfolders containing input `csv` data files
    2. Run pseudonymisation on this data with a salt of value `00ed1234da` and save the pseudonymised data in the folder `pseudonymised_input_data`
    3. Run the transform of the dataset into CDM based on the rules saved in `rules.json` 
    4. Upload this data into bclink link tables (e.g. person.tsv --> person_test_data_v1)

    ```yaml
    clean: true
    rules: /usr/lib/bcos/MyWorkingDirectory/rules.json
    log: /usr/lib/bcos/MyWorkingDirectory/coconnect.log
    data: 
      watch: 
         minutes: 1
      input: /usr/lib/bcos/MyWorkingDirectory/input_data
      output: /usr/lib/bcos/MyWorkingDirectory/mapped_data
      pseudonymise: 
         output: /usr/lib/bcos/MyWorkingDirectory/pseudonymised_input_data
         salt: 00ed1234da
    bclink:
      tables:
         person: person_test_data_v1
         observation: observation_test_data_v1
         condition_occurrence: condition_occurrence_test_data_v1
         measurement: measurement_test_data_v1
    ```





