# INSTALLATION GUIDE
The following is a step by step installation guide that will let you use the following components on minikube:
-   Postgres
-   Nifi
-   Minio
-   Nuclio
-   Grafana
-   Dremio

# PREREQUISITES
Be sure to have the following elements installed before proceeding:
-   Linux OS
-   Minikube (Documentation: https://minikube.sigs.k8s.io/docs/start/)
-   Kubectl (Documentation: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
-   Helm (Documentation: https://helm.sh/docs/intro/install/)

# STEP 1: PREPARING MINIKUBE
From the command line, start your minikube profile by typing:

```
minikube start -p <NAME_OF_YOUR_PROFILE> --memory 8192
```

You can decide to use more memory if you wish, but do not go below this number.
Once it's ready, install the ingress addon by typing:

```
minikube -p <NAME_OF_YOUR_PROFILE> addons enable ingress
```

Before proceeding, wait until all the pods/jobs are ready/completed. To check status of all the resources mentioned, type:

```
kubecl get pods -A
```

If you want to check the status of the resources in real-time while they are being downloaded and installed, type:

```
kubecl get pods -A -w
```


# STEP 2: CONFIGURE YOUR PREFERENCES
You can modify the file dh-migration/values.yaml to your own needs.
You will notice that usernames and passwords for the components of this package are all called dhmigrationuser and dhmigrationpassword (except from postgres, see the comment in the file) as default, but you can change them to whatever you prefer.

# STEP 3: ADDING INGRESS ROUTES
First, check your IP on minikube by typing:

```
minikube -p <NAME_OF_YOUR_PROFILE> ip
```

Copy the address.
Next, let's modify the file etc/hosts.
Open the file with administrator privileges with an editor of your choice, and add the following entry to the list of IPv4 addresses:

<MINIKUBE_IP>    <NUCLIO_HOST> <MINIO_HOST> <GRAFANA_HOST> <NIFI_HOST> <DREMIO_HOST> 

Replace the field <MINIKUBE_IP> with the ip you got from the previous command.
Replace the <SOMETHING_HOST> fields with the hosts you have chosen in the values. If you use the default ones and supposing that your minikube ip is 192.168.76.2, it will look like this:

192.168.76.2   nuclio.local minio.local grafana.local nifi.local dremio.local 

Be sure to save the changes.

# STEP 4: INSTALL THE HELM CHART
To install, type the following:

```
helm install -n <NAME_OF_YOUR_NAMESPACE> <NAME_OF_YOUR_RELEASE> <PATH-OF-DH-MIGRATION-FOLDER> --create-namespace
```

For example, if you type:

```
helm install -n dhmigration-namespace dhmigration-release helm/dh-migration --create-namespace
```

The release dhmigration-release will be installed using the chart in the path helm/dh-migration in the namespace dhmigration-namespace.
Even after getting the installation's success confirmation, you will still need to wait for all pods to be ready. To check the status of the resources in the namespace you have created and selected, type the following:

```
kubectl -n <NAME_OF_YOUR_NAMESPACE> get pods
```

If you want to check the status of the resources in real-time while they are being downloaded and installed, type:

```
kubectl -n <NAME_OF_YOUR_NAMESPACE> get pods -w
```

Please note that it's normal to get error or configerror statuses while the installation is progressing, because some installations are dependant on other ones.
Once every pod is stated as READY, the installation is completed.

# STEP 5: ACCESSING YOUR RESOURCES
If you want to access your resources, type the command:

```
helm get notes <NAME_OF_YOUR_RELEASE> -n <NAME_OF_YOUR_NAMESPACE>
```

This will prompt the message that you received after the installation of the chart with the list of addresses.

# ADDITIONAL NOTES
- Values for the postgres db and user are editable in the file dh-migration/templates/dhmigration-postgresql-cluster.yaml.
- The first time you use dremio, you will be asked to register, so you can choose your username and password as you see fit.