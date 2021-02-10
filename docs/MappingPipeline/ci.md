
Continuous Integration (CI) in enabled through github actions, everytime you make a pull requestion it will trigger an action to build the stack with `docker-compose` and check for errors when starting it up.

## Setting Environmental Variables (Secrets)

When running the code on a github runner, we need to pass the secret variables we would have in `.env`. To get started with this

If you have admin access to the project, you can go into "Settings":
![image](https://user-images.githubusercontent.com/69473770/107513010-85946880-6b9f-11eb-9589-3e08225a4782.png)

Then you can navigate on the left-hand side to find the "Secrets", where these values corresponding to secrets can be assigned, updated or deleted.

![image](https://user-images.githubusercontent.com/69473770/107513104-a6f55480-6b9f-11eb-8124-9d9fbd89821c.png)

## Configuration

The configuration of what to be ran by the github actions CI is configured in the file `.github/workflows/ci.yml`.


### Triggers

By default the trigger is set on pull_requests:

```yaml
name: ci
on: [pull_request]
```
To run CI for all pushes, you could set `on: [push]`

### Build Job

The CI only runs a single build job, but in theory you can run multiple jobs. It uses a ubuntu docker image, as can be seen at the top of the configuration:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```
### Setting Steps

In each job multiple steps can be specified, like so:
```yaml
steps:
 - step_1
 - step_2
 -...
```


### Configuration Step

The first step make the `.env` file needed to run the stack, by grabbing the github actions secrets and putting them into a file.
```yaml
steps:
  - uses: actions/checkout@v2
  - name: Make envfile
        run: |
          touch .env
          echo POSTGRES_USER=${{ secrets.POSTGRES_USER }} >> .env 
          ....
	  
```

Define other commands to run...
```yaml
- name: Run the dokcer-compose stack
   run: |
   #startup the stack
   docker-compose up -d
   #give it a minute to start properly
   sleep 1m
...
```


It's also useful to 'save artifacts' by uploading them 
```yaml
#save the logs so they can be further checked 
- uses: actions/upload-artifact@v2
   with:
     name: my-artifact
     path: logs.out
     retention-days: 1
```


## Viewing Actions

### All workflows
![image](https://user-images.githubusercontent.com/69473770/107514348-744c5b80-6ba1-11eb-9553-b251ce2433bc.png)

### Build Jobs
![image](https://user-images.githubusercontent.com/69473770/107514366-7adad300-6ba1-11eb-8a92-5bbd912001c8.png)


### Steps within the build job
![image](https://user-images.githubusercontent.com/69473770/107514395-84fcd180-6ba1-11eb-8da2-829b9447e6f2.png)


### Access Artifacts
![image](https://user-images.githubusercontent.com/69473770/107514922-36036c00-6ba2-11eb-84ab-3d42c0cf765d.png)

