
## Setup

ssh to the test link machine:
```
ssh 52.XXX.XXX.XXX
```
This is with the configuration:
```
$ cat ~/.ssh/config
Host 52.XXX.XXX.XXX
HostName 52.XXX.XXX.XXX
PreferredAuthentications publickey
IdentityFile <path to private key identity file >
```
Otherwise you can do something like:
```
ssh -i <path to private key identity file > 52.XXX.XXX.XXX
```
If you ssh keys were generated without a /with a different user, you may need to do:
```
ssh <user>@52.XXX.XXX.XXX
```

To take `root` control:
```
sudo -s
```

To then use the user `bscos_srv` that has permissions to upload to BCLink use:
```
su - bcos_srv
```

## Transfering a file

Using a tool like `scp` or `rsync` you can transfer an output from the ETLTool to the test link like this:
```
rsync -avz --progress <local file> 52.XXX.XXX.XXX:/home/<user>
```

!!! tip 
    The option `-z` can compress files over a slow upload connection and make the transfer of large files fast.


## Uploading a file

Once the file (e.g. person) is on the link, you can transfer it to whatever folder you prefer, I am storing files in:
```
/usr/lib/bcos/OMOP-test-data/calum_tests_4Aug/
```

The command line tool `datasettool2` can then be used  to load the file into the BCLink
```
[bcos_srv@link-test-dt calum_tests_4Aug]$ datasettool2 load --dataset=person --datafile=./person.csv --database=bclink
Loading file ./person.csv to dataset PERSON (person)
debug1: client_input_channel_req: channel 0 rtype keepalive@openssh.com reply 1
```

!!! tip
    While in `root` you can change the permissions of a folder with `chmod u+x -R <folder>` to give access to all, and make sure the files have the correct permissions for the user `bcos_srv`