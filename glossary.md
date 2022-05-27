# Glossary of Commands #

# Minikube #

https://gitlab.com/cfech44/docker#minikube

- have to start minikube, can feed none to driver and will pickup docker, or could feed docker, vmware, virtualbox etc... 

        minikube start --vm-driver=none

- check status of cluster 

        minikube status

- delete cluster 
        minikube delete 

- pull up dashboard in browser window
  
        minikube dashboard

- pause cluster
        minikube pause

- unpause cluster 

        minikube unpause

- Retrieves the IP address of the running cluster, checks it with IP in kubeconfig, and corrects kubeconfig if incorrect.

        minikube update-context

- see the services 

        minikube service

- ssh into the minikube container/node 
        minikube ssh 

# Kubectl #

https://gitlab.com/cfech44/docker#kubernetes

- get all pods

    kubectl get pods 

kubectl get pod fcc-firstchart-84bb59f994-xj4fv -o yaml

- create new namespace

    kubectl create namespace namespace2
                    [ns for short]

- get all secrets

    kubectl get secrets

- view what is in the secret with 

        kubectl get secret [secret name] -o yaml
                                        [output yaml format]

- get all namespaces

    kubectl get namespace

- delete a namspace 

    kubectl delete namespace mynamespace
                                [name]



# Helm #

## Install ##
- can use helm to pull charts and install dependencies we want, 

        "helm install apache bitnami/apache [-namespace=web"]
                    [custom name]               [namespace it will go in]

- can create new namespace with 

        helm install mywebserver bitnami/apache --namespace mynamespace --create-namespace 

- can then see installation with 

        helm ls --namespace mynamespace

    
- providing a custom password to our mysql instance


                helm install mydb bitnami/mysql --set auth.rootPassword = test12345
                                                 [--set passes in custom config]

- recommended way is to pass in a values.yaml file, not just pass through set, set is fine for 1 off

                helm install mydb bitnami/mysql --values values.yaml

                helm install mydb bitnami/mysql --values ~/Desktop/values.yaml
                                                           [path]
- inside values.yaml

                auth:
                  rootPassword: "test1234"

- can have helm generate a name automatically for us if we want

        helm install bitnami/apache --generate-name         

- can also provide a template for name generation, will generate a name with mywebserver-[7 random lower case letters ], randAlpha and lower case are functions, we pipe the output of randAlpha to the function lower

        helm install bitnami/apache --generate-name --name-template "mywebserver-{{randAlpha 7 | lower}}


- by default helm does not wait for the pods to be up and running to be considered successful, it usually just wait until the handoff of the yaml to kubernetes, we can tell it to wait with with the --wait flag , by default it will wait for 5 mins, can change timeout if we want 

        helm install mywebserver bitnami/apache --wait [--timeout 5m10s]

- if we use the wait and and timeout flags but our pod is not up and running in that time period, the installation will be marked as a failure, we can use the --atomic flag to always revert back the the previous successful release, if we do not specify a --wait and --timeout it will just use the default 5 min timer 

        helm install mywebserver bitnami/apache --atomic


- to install our own custom chart

        helm install myfirstchart firstchart
                       [name]      [path to chart]

- download the dependencies with, they are stored in the charts folder

        helm dependency update myChart

## Upgrade ##
- upgrade

        "helm upgrade apache bitnami/apache [-namespace=web"]


- to upgrade 

        helm upgrade mydb bitnami/mysql --values ~/Desktop/values.yaml

- to upgrade or install, it will first check if the installation is there, if so upgrade, else install

        helm upgrade --install mywebserver bitnami/apache

- when we do an upgrade kubernetes only touches the pods that will have changes, but if we have a requirement where we need all the pods rebuilt we can forcefully upgrade with --force

- this cause helm to delete the installation and remake the whole thing. 

        helm upgrade mywebserver bitnami/apache --force

- will clean up all resources created if any upgrade fails such as configmaps, secrets, pv and pvcs etc ... Would not use this if need to debug

        helm upgrade mywebserver bitnami/apache --cleanup-on-failure


## RollBack #

- rollback

        "helm rollback apache 1 [-namespace=web"]

- show the history , with successful installations and errors throughout the history

        helm history mydb

- to rollback

        helm rollback mydb 1
                        [name] [version]


## Uninstall ##

- uninstall

        helm uninstall apache

- could retain secrets if we use flag --keep-history

    helm uninstall mydb --keep-history


## Repositories ##

- see all repos with 

        helm repo list

- can add with 

        helm repo add bitnami https://charts.bitnami.com/bitnami
                      [my name] [chart url]

- can search through the repo with to see all the apache charts in the repo

        helm search repo apache 
                         [name]
- will show the app version chart version and description

- to see all versions

        helm search repo apache --version

- to remove repo

        helm repo remove bitnami
                        [name]

## Charts ##

- how to package a chart in order to distribute it across projects, deploy it though pipelines etc ...

        helm package firstchart
                        [chart name]

- update dependencies 

        helm package firstchart [--dependencies-update ] [-u (dependencies update shortcut)]
                        [chart name]

- change where the .tgz file goes with -d or --destination 

        helm package firstchart --d file/path
                        [chart name]

## Other ##
- how to list all packages installed on cluster

    helm list


- update charts in local cache

        helm repo update 

- checks status and gives us the update info

        helm status mydb
                    [name]    

- dry run


        helm install mydb bitnami/mysql --values file/path/yaml --dry-run

- templates 

    helm template mydb bitnami/mysql --values /path/to/values.yaml


- see notes of the installation 


        helm get notes mydb
                        [name of installation]

- show all the custom values we have used in the file , all shows the default's, --revision will show the values for that specific revision

        helm get values mydb [--all] [--revision 1]
                  [installation name]

- get the manifest or entire yaml currently being used, with values already injected

        helm get manifest mydb [--revision]



- lints our yaml files in the chart and checks if there are any syntax issues 

        helm lint firstChart 