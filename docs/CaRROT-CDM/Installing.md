
!!! caution
    This tool is only stable with python versions `>=3.6` on Unix distributions (macOS, Ubuntu, Centos7) and on Windows. 

<!---
[![asciicast](https://asciinema.org/a/443714.svg)](https://asciinema.org/a/443714)
--->

The carrot-cdm package is available to be [installed via pip](https://pypi.org/project/carrot-cdm/). The latest tagged version of the tool can be found here. See [the next section](/CaRROT-Docs/CaRROT-CDM/Installing/#upgrading) on how to upgrade the package to the latest version.

!!! tip
    It is recommended that you setup your own virtual python environment, for example using [`venv`](https://docs.python.org/3/library/venv.html).
    ```
    python3 -m venv  .
    source bin/activate
    ```

!!! tip
    When installing via `pip`, you should remember to update `pip` first, otherwise the install can hang:
    ```
    python -m pip install --upgrade pip
    ```


To install the package via pip you should execute the command:
```
python -m pip install carrot-cdm
```

Otherwise, to ensure the right version of python (python3) is used to install (as you may have installed `python2` and `python3`) from pip you can also do:
```
python3 -m pip install carrot-cdm
```


!!! tip
    If you are struggling to install from `pip` due to lack of root permissions, or you are not using a virtual python environment (e.g. conda), you can install as a user with the command:
    ```
    python3 -m pip install carrot-cdm --user
    ```
    This will install the package into a local user folder.

!!! tip
    If you have trouble with `pip` hanging on installing dependencies, try to install using the argument `--no-cache-dir`. Also make sure that you have updated `pip` via `pip3 install --upgrade pip`.


## Upgrading

To upgrade the tool to the latest version, run:
```
python -m pip install carrot-cdm --upgrade
```
Otherwise, to be sure, uninstall the package and reinstall it.

!!! tip
    Running the command:
    ```
    carrot info version
    ```
    will display the installed verison of the tool.

## Installing from Source

Alternatively you can [download the source code](https://github.com/HDRUK/CaRROT-CDM/tags), unpack and install as a local package:
```
cd <downloaded_source_code_folder>
python3 -m pip install -e . 
```


## Verifying

To verify the package is installed you can test the following information commands:

* show the tool version (this may take a few seconds, the first time you run the package)

```
carrot info version
```
Alternatively...
```
carrot --version
```

* show the location of the installation folder  

```
carrot info install_folder
```

