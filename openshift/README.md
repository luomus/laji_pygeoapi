
# OpenShift Deployment Instructions
Step-by-Step Guide to deploy this API to OpenShift

## 1. Log in to OpenShift

```
oc login https://your-openshift-cluster-url
```

## 2. Set the Project/NAMESPACE

Ensure you are in the correct project/namespace. You can also create a new one if necessary.

```
oc project your-project-name
```

## 3. Encode Database Credentials

Encode your database credentials using base64. This example uses Git Bash:

```
echo -n yourpostgrespassword | base64 # Encode POSTGRES_PASSWORD
echo -n yourpostgresuser | base64 # Encode POSTGRES_USER
echo -n yourpostgresdb | base64 # Encode POSTGRES_DB
```

## 4. Process the Template

Use the encoded parameters to process the template and create a list of objects.

```
oc process -f template.yaml -p |
 POSTGRES_PASSWORD=<encoded yourpostgrespassword>
 POSTGRES_USER=<encoded yourpostgresuser>
 POSTGRES_DB=<encoded yourpostgresdb>
 HOST_URL=<yourhosturl>
 ACCESS_TOKEN=<yourapitoken>
 > processed-template.yaml
```
For example:
```
oc process -f template.yaml -p POSTGRES_PASSWORD=YWRtaW4xMjM= POSTGRES_USER=cG9zdGdyZXM= POSTGRES_DB=bXlfZ2Vvc3BhdGlhbF9kYg== HOST_URL="pygeoapi-route-laji-pygeoapi-main.2.rahtiapp.fi" ACCESS_TOKEN=ftmCFaEjHNgCUWgW8rPriwDQOxuUr > processed-template.yaml
```


## 5. Add Objects from the Processed Template

```
oc apply -f processed-template.yaml
```

## 6. Verify CronJob

As a default, the CronJob tries to start the ```python-script-<branch>``` pod every minute. Wait for it to start the pod and once it has started, change the CronJob schedule to run at a more appropriate interval (e.g., daily at 3 AM).
Edit the schedule from the YAML-file from
```schedule:  "*/1 * * * *"``` to  ```schedule: "0 3 * * *"``` or similar.

## 7. Wait for Pod Completion

Wait for the pod ```python-scripts-<BRANCH>-xxxx``` to complete its tasks. If ```"PAGES"``` environmental variable is a big number or ```"all"```, it can take some time. You can check the logs to see it's progressing. 


## Done!

Your pygeoapi server and postgis database should now be set up and running. Go to the URL you gave and enjoy.
