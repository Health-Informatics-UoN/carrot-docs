
!!! caution
    This tool is only stable and can run with python versions `>=3.6` on the latest Unix distributions (macOS, Ubuntu, Centos7) and on Windows. 


!!! tip
    It is recommended that you setup your own virtual python environment. This can be done via the use of [`venv`](https://docs.python.org/3/library/venv.html) or other methods such as `conda`. 


To install the package, the easiest way is to do this is via pip:
```
python -m pip install co-connect-tools
```

To ensure the right version of python (python3) is used to install from pip you can also do:
```
python3 -m pip install co-connect-tools
```
Or
```
pip3 install co-connect-tools
```

If you are struggling to install from `pip` due to lack of root permissions, or you are not using a virtual python environment (e.g. conda), you can install as a user with the command:
```
 python3 -m pip install co-connect-tools --user
```
This will install the package into a local user folder.

Alterative you can [download the source code](https://github.com/CO-CONNECT/co-connect-tools/tags), unpack and install as a local package:
```
cd < downloaded source code folder >
python3 -m pip install -e . 
```

!!! tip
    If you have trouble with `pip` hanging on installing dependencies, try to install using the argument `--no-cache-dir`. Also make sure that you have updated `pip` via `pip3 install --upgrade pip`.


### Manual Install 
If you are on a system and need to install manual without pip (e.g. yum), you can install from source but will also need to install the additional dependencies that are located in `requirements.txt`:

```bash
$ cat requirements.txt 
numpy
pandas
coloredlogs
Jinja2
graphviz
click
sqlalchemy
tabulate
psutil
```

## Upgrading

To upgrade the tool to the latest version, run:
```
python -m pip install co-connect-tools --upgrade
```
Otherwise, to be sure, uninstall the package and reinstall it.

!!! tip
    Running the command:
    ```
    coconnect info version
    ```
    will display the installed verison of the tool.