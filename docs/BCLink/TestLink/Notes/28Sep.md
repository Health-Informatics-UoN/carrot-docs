## Problems as `bcos_srv` user

Issues because of `bcos_srv`'s home directory, which it does not own.
```
$ echo $HOME
/data/usr/lib/bcos
$ ls -ld $HOME
drwxrwxr-x. 9 root root 4096 Jul 20 09:56 /data/usr/lib/bcos
```

Examples:

   * When trying to use a text editor, e.g. `emacs`:    
   `Creating directory: permission denied, /data/usr/lib/bcos/.emacs.d/`
   
   * When trying to use `pip`:

```
$ python3 -m pip install pip --upgrade
WARNING: The directory '/data/usr/lib/bcos/.cache/pip' or its parent directory is not owned or is not writable by the current user. The cache has been disabled. Check the permissions and owner of that directory. If executing pip with sudo, you should use sudo's -H flag.
Defaulting to user installation because normal site-packages is not writeable
Requirement already satisfied: pip in /usr/local/lib/python3.6/site-packages (21.2.4)
ERROR: Could not install packages due to an OSError: [Errno 13] Permission denied: '/data/usr/lib/bcos/.local'
Check the permissions.
```

Will changing the `bcos_srv` home directory mess anything up? i.e.:
```
usermod -d <new_dir> bcos_srv
```
Most likely?


## Solutions

Setting up a python3 virtual enviroment is ok
```
mkdir /usr/lib/bcos/OMOP-test-data/tests_28Sep
cd /usr/lib/bcos/OMOP-test-data/tests_28Sep
python3 -m venv automation
source automation/bin/activate
pip install pip --upgrade
pip install co-connect-tools
```

## Using the GUI

```
yum install -y xorg-x11-server-Xorg xorg-x11-xauth xorg-x11-apps
```

X11 forwarding for GUI:
```
ssh -YX <VM>
```

To make sure `tkinter` is installed (on CentOS):
```
sudo yum install python3-tkinter
```