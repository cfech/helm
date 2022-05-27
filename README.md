# Helm Kubernetes Packaging Manager for Developers and DevOps on Udemy


https://helm.sh/docs/
https://helm.sh/docs/helm/

# Section 2 Helm Fundamentals#

## 5 Before Helm ##
- Needed to create a deployment and service and all objects for each piece of an app, example the app itself and the database needed.
- All the files are static, cannot take different variables
- Did not have any revision history, helm provides this among many other features

## 6 What is Helm ##
- written in google GO language
- package manager for kubernetes
- takes the package we want and installs/upgrades/uninstalls for us
- works with charts, charts are our packages
- charts includes all the template files and configurations required to create Kubernetes resources
- can use helm to pull charts and install dependencies we want, 

        "helm install apache bitnami/apache -namespace=web" 
        
would install the apache chart

- upgrade

        "helm upgrade apache bitnami/apache -namespace=web" 

- rollback

        "helm rollback apache 1 -namespace=web" 

- uninstall

        helm uninstall apache

## 7 Why Use Helm ##

- helm simplifies kubernetes installation process by extracting out complexity

- if we need to install a mongodb in a kubernetes cluster we would need to figure out all the pieces needed such as deployment, service, configmap, which can be overwhelming

- with helm we just want to pull a mongodb chart from a chart repository

- this chart has all the required resources and comes with default values with can override if necessary

- helm can will kep history of our revisions

- Helm combines templates with values from a values.yaml to create dynamic kubernetes installations

- Use helm cli instead of kubectl to update installations

- helm utilizes intelligent installations, it will know which order to create kubernetes resources, we dont have to specify

- utilizes life cycle hooks, they can be hooked into the lifecycle of the application 

- built in support for charts downloaded from the repository. Verifies signatures and hashes when installing packages. 


## 8 Charts and Repos ##

- Charts are stored in chart repositories , bitnami is a famous one

https://bitnami.com/stacks/helm
https://github.com/bitnami/charts

# Section 3 Helm In Action #

## 9 Installing Helm ##

### Setting up AWS VDI ###
- installed homebrew

        /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

- vscode 

https://code.visualstudio.com/docs/setup/linux

        sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
        
        sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'

        yum check-update
        
        sudo yum install code
        

- installed chrome

        sudo curl https://intoli.com/install-google-chrome.sh | bash

- installed helm with homebrew
**helm should be using the same config file as kubectl**
        brew install helm

https://www.devopszones.com/2022/01/how-to-install-minikube-in-amazon-linux.html

- install docker as hypervisor for minikube 

        sudo yum install docker 

        sudo docker start #start docker daemon

        # have to change so non root user can run docker commands, must run from root dir

        sudo chmod 666 var/run/docker.sock


- installed conntrack for minikube setup

        sudo yum install conntrack

- installed kubectl with homebrew


        brew install kubectl

- updated yum

        sudo yum update -y 

- installed minikube 

        curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

        sudo install minikube-linux-amd64 /usr/local/bin/minikube

- have to start minikube

        minikube start --vm-driver=none

- other commands

        minikube status
        minikube delete 

        minikube dashboard

        minikube pause

        minikube unpause

        minikube update-context

        minikube service

        minikube ssh # ssh into the node


**helm should be using the same config file as kubectl**

## 10 Working with chart repositories ##

- helm does not come with any presintalled repos
- see repos with 

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

## 11 The Magic of Helm ##

### minikube connection issue ###
https://lifesaver.codes/answer/hyperkit-+-docker-proxy-get-https-registry-1-docker-io-v2-net-http-request-canceled-while-waiting-for-connection-4589

- installing a package with helm

        helm install mydb bitnami/mysql 
                    [my name ] [chart to be used]

        helm uninstall mydb

- name must be unique to namespace
        
        NAME: mydb
        LAST DEPLOYED: Fri May 20 18:01:44 2022
        NAMESPACE: default
        STATUS: deployed
        REVISION: 1
        TEST SUITE: None
        NOTES:
        CHART NAME: mysql
        CHART VERSION: 9.0.3
        APP VERSION: 8.0.29

        ** Please be patient while the chart is being deployed **

        Tip:

        Watch the installation status using the command: kubectl get pods -w --namespace default

        Services:

        echo Primary: mydb-mysql.default.svc.cluster.local:3306

        Execute the following to get the administrator credentials:

        echo Username: root
        MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mydb-mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode)

        To connect to your database:

        1. Run a pod that you can use as a client:

            kubectl run mydb-mysql-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.0.29-debian-10-r22 --namespace default --env MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD --command -- bash

        2. To connect to primary service (read/write):

            mysql -h mydb-mysql.default.svc.cluster.local -uroot -p"$MYSQL_ROOT_PASSWORD"


## 12 Using same installation name ##

- cannot use mydb again if it already exists in the same namespace 

- could make the install a different name or create a new namespace with 

        kubectl create ns namespace2
                            [my name]

        helm install --namespace namespace2 mydb bitnami/mysql
## 13 List and Uninstall ##
- how to list all packages installed on cluster

    helm list

- to remove a package from cluster 

    helm uninstall [name]


    Minikub start


## 14 Providing custom values ##
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

## 15 Helm Upgrade ##

- update charts in local cache

        helm repo update 

- checks status and gives us the update info

        helm status mydb
                    [name]    

- to upgrade 

        helm upgrade mydb bitnami/mysql --values ~/Desktop/values.yaml

## 16 More about upgrade ##

- if you dont pass in the values it will use the default configuration or could use 

        helm upgrade mydb bitnami/mysql --reuse-values

## 17 Release Records ##

- see all helm installs

        helm ls

- get kubernetes secrets, secret includes all the info about the release, useful when we are using rollbacks

        kubectl get secrets

- could retain secrets if we use flag --keep-history


        helm uninstall mydb --keep-history
## Quiz 1 ##
- helm in action

Question 1:
Which of the following command will list all the helm repositories

        helm repo ls

Question 2:
We can re-use the same release/installation name when a release with that name already exists

        false

Question 3:
We can use the same release/installation name in a namespace even when a release with that name already exists in another namespace

        true, name is only specific to the namespace

Question 4:
Helm uses which of the following kubernetes resources to store release information

        secret

Question 5:
When we do a helm uninstall all the release information stored in kube secrets will be deleted

        true, except if we use the --keep-secrets flag

## Assignment 1 ##

- install the tomcat web server, bitnami/tomcat

       helm install tomcatserver bitnami/tomcat
        
- output 
                
                NAME: tomcatserver
                LAST DEPLOYED: Sat May 21 18:55:12 2022
                NAMESPACE: default
                STATUS: deployed
                REVISION: 1
                TEST SUITE: None
                NOTES:
                CHART NAME: tomcat
                CHART VERSION: 10.2.3
                APP VERSION: 10.0.21

                ** Please be patient while the chart is being deployed **

                1. Get the Tomcat URL by running:

                NOTE: It may take a few minutes for the LoadBalancer IP to be available.
                        Watch the status with: 'kubectl get svc --namespace default -w tomcatserver'

                export SERVICE_IP=$(kubectl get svc --namespace default tomcatserver --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
                echo "Tomcat URL:            http://$SERVICE_IP:/"
                echo "Tomcat Management URL: http://$SERVICE_IP:/manager"

                2. Login with the following credentials

                echo Username: user
                echo Password: $(kubectl get secret --namespace default tomcatserver -o jsonpath="{.data.tomcat-password}" | base64 --decode)



- get the tomcat password

        echo Password: $(kubectl get secret --namespace default tomcatserver -o jsonpath="{.data.tomcat-password}" | base64 --decode)
        
- upgrade tomcat, pass it the password and change the service type to NodePort via tomcat-values.yaml

        helm upgrade tomcatserver bitnami/tomcat --values ~/Desktop/tomcat-values.yaml

- tomcat-values.yaml

                tomcatPassword: UJcEOFqGiA
                service:
                  type: NodePort
                  nodePort: 30007


- uninstall the tomcat server

        helm uninstall tomcatserver

# Section 4 Advanced Commands #

## 18 Helm Release Workflow ##

- step 1: helm loads the chart
- step 2: parse the values.yaml
- step 3: generate the final yaml files by injecting the values into the templates
- step 4: parse the yaml and validate the schema against kubernetes object requirements
- step 5: Generate yaml and send to kubernetes

## 19 Helm Dry Run ##

- helm --dry-run 

        helm install mydb bitnami/mysql --values file/path/yaml --dry-run

- dry run performs the first 4 steps but never actually sends the yaml for kubernetes for object creation, this will tell us if we have any error along the way in our helm charts though 

- it will show all of the real yaml files that will be submitted to kubernetes

- for mysql a serviceAccount, Secret, ConfigMap, Service, StatefulSet (Creates the pods), last section is the release notes

- the dry run commands provide some non valid yaml
- can have dry runs for upgrades and installs

StatefulSet vs Deployment?

https://cloud.netapp.com/blog/cvo-blg-kubernetes-deployment-vs-statefulset-which-is-right-for-you

## 20 Helm Template ##
        
        helm template mydb bitnami/mysql --values /path/to/values.yaml

- also goes through the first four steps of generating the yaml, this gives us the exact yaml that will be passed to the cluster
- does not validate the output with kubernetes cluster so can be run without a cluster

- i guess just to practice?

**Always acts like an installation**


### Dry Run VS Template ###
- dry run always communicates with kubernetes and does schema validation, template does not

## 21 More About Release Records ##

- helm generates a secret for each release, this holds every file we would need to generate a new copy of the exact same thing that is deployed, 

- view secrets with 

        kubectl get secret

- view what is in the secret with 

        kubectl get secret [secret name] -o yaml
                                        [output yaml format]

- output is encoded in base 64, it can be decoded if necessary, it can then be rolled back there if necessary. (Other options for rollback as well) 



## 22 Helm Get ##

- see notes of the installation 


        helm get notes mydb
                        [name of installation]

- show all the custom values we have used in the file , all shows the default's, --revision will show the values for that specific revision

        helm get values mydb [--all] [--revision 1]
                  [installation name]

- get the manifest or entire yaml currently being used, with values already injected

        helm get manifest mydb [--revision]


## 23 Helm History ##

- show the history , with successful installations and errors throughout the history

        helm history mydb

## 24 Helm Rollback ##

- to rollback

        helm rollback mydb 1
                        [name] [version]

## 25 Create Namespace ##

- can create new namespace with 

        helm install mywebserver bitnami/apache --namespace mynamespace --create-namespace 

- can then see installation with 

        helm ls --namespace mynamespace

- to see all namspaces

        kubectl get namespace

- to delete namespace 

        kubectl delete namspace mynamspace 

## 26 Install or Upgrade ##


- to upgrade or install, it will first check if the installation is there, if so upgrade, else install

        helm upgrade --install mywebserver bitnami/apache


## 27 Generate Release Names ##

- can have helm generate a name automatically for us if we want

        helm install bitnami/apache --generate-name         

- can also provide a template for name generation, will generate a name with mywebserver-[7 random lower case letters ], randAlpha and lower case are functions, we pipe the output of randAlpha to the function lower

        helm install bitnami/apache --generate-name --name-template "mywebserver-{{randAlpha 7 | lower}}

## 28 Wait and Timeout ##

- by default helm does not wait for the pods to be up and running to be considered successful, it usually just wait until the handoff of the yaml to kubernetes, we can tell it to wait with with the --wait flag , by default it will wait for 5 mins, can change timeout if we want 

        helm install mywebserver bitnami/apache --wait [--timeout 5m10s]


## 29 Atomic Install ##
- if we use the wait and and timeout flags but our pod is not up and running in that time period, the installation will be marked as a failure, we can use the --atomic flag to always revert back the the previous successful release, if we do not specify a --wait and --timeout it will just use the default 5 min timer 

        helm install mywebserver bitnami/apache --atomic

## 30 Forceful Upgrades ##
- when we do an upgrade kubernetes only touches the pods that will have changes, but if we have a requirement where we need all the pods rebuilt we can forcefully upgrade with --force

- this cause helm to delete the installation and remake the whole thing. 

        helm upgrade mywebserver bitnami/apache --force

## 31 Clean Up on Failed Updates ##

- will clean up all resources created if any upgrade fails such as configmaps, secrets, pv and pvcs etc ... Would not use this if need to debug

        helm upgrade mywebserver bitnami/apache --cleanup-on-failure
## Quiz 2 Commands Deep Dive ##

Question 1:
The "helm template" command validates the generated objects/yaml by communicating with Kubernetes
        
        false

Question 2:
Which of the following command give us all the values used for a particular installation

        helm get values mydb --all
                        [installation name]

Question 3:
helm get manifest returns the templates that were used for particular installation or upgrade

        false, gets the filled in yaml 

Question 4:
Which of the following flags should be used to retain version history while uninstalling a release

        --keep-history

Question 5:
Which of the following can be used to specify a format while generating a name for the template

        --name-template


Question 6:
If you want helm to wait for the pods to be up and running when you do a helm install which of the following options should be used

        --wait

Question 7:
Which of the following should be used to rollback to a previous successful installation if the current installation fails

        --atomic


## Assignment 2 Advanced Commands ##

- release tomcat to the kubernetes cluster with a generated name 
        helm install bitnami/tomcat --generate-name --name-template "mws-{{randAlpha 2 | lower}}"

- do a dry run to see the installation templates 

        helm install bitnami/tomcat --generate-name --name-template "mws-{{randAlpha 2 | lower}}" --dry-run
        
- output 
                NAME: mws-kt
                LAST DEPLOYED: Sun May 22 00:13:11 2022
                NAMESPACE: default
                STATUS: pending-install
                REVISION: 1
                TEST SUITE: None
                HOOKS:
                MANIFEST:
                ---
                # Source: tomcat/templates/secrets.yaml
                apiVersion: v1
                kind: Secret
                metadata:
                name: mws-kt-tomcat
                namespace: default
                labels:
                app.kubernetes.io/name: tomcat
                helm.sh/chart: tomcat-10.2.3
                app.kubernetes.io/instance: mws-kt
                app.kubernetes.io/managed-by: Helm
                type: Opaque
                data:
                tomcat-password: "c0E0VXVkU09qUA=="
                ---
                # Source: tomcat/templates/pvc.yaml
                kind: PersistentVolumeClaim
                apiVersion: v1
                metadata:
                name: mws-kt-tomcat
                namespace: default
                labels:
                app.kubernetes.io/name: tomcat
                helm.sh/chart: tomcat-10.2.3
                app.kubernetes.io/instance: mws-kt
                app.kubernetes.io/managed-by: Helm
                spec:
                accessModes:
                - "ReadWriteOnce"
                resources:
                requests:
                storage: "8Gi"
                ---
                # Source: tomcat/templates/svc.yaml
                apiVersion: v1
                kind: Service
                metadata:
                name: mws-kt-tomcat
                namespace: default
                labels:
                app.kubernetes.io/name: tomcat
                helm.sh/chart: tomcat-10.2.3
                app.kubernetes.io/instance: mws-kt
                app.kubernetes.io/managed-by: Helm
                spec:
                type: LoadBalancer
                externalTrafficPolicy: "Cluster"
                ports:
                - name: http
                port: 80
                targetPort: http
                selector: 
                app.kubernetes.io/name: tomcat
                app.kubernetes.io/instance: mws-kt
                ---
                # Source: tomcat/templates/deployment.yaml
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                name: mws-kt-tomcat
                namespace: default
                labels:
                app.kubernetes.io/name: tomcat
                helm.sh/chart: tomcat-10.2.3
                app.kubernetes.io/instance: mws-kt
                app.kubernetes.io/managed-by: Helm
                spec:
                replicas: 1
                selector:
                matchLabels:
                app.kubernetes.io/name: tomcat
                app.kubernetes.io/instance: mws-kt
                strategy:
                type: RollingUpdate
                template:
                metadata:
                labels:
                app.kubernetes.io/name: tomcat
                helm.sh/chart: tomcat-10.2.3
                app.kubernetes.io/instance: mws-kt
                app.kubernetes.io/managed-by: Helm
                spec:

                affinity:
                podAffinity:

                podAntiAffinity:
                preferredDuringSchedulingIgnoredDuringExecution:
                - podAffinityTerm:
                labelSelector:
                matchLabels:
                app.kubernetes.io/name: tomcat
                app.kubernetes.io/instance: mws-kt
                namespaces:
                - "default"
                topologyKey: kubernetes.io/hostname
                weight: 1
                nodeAffinity:

                securityContext:
                fsGroup: 1001
                initContainers:
                containers:
                - name: tomcat
                image: docker.io/bitnami/tomcat:10.0.21-debian-10-r2
                imagePullPolicy: "IfNotPresent"
                securityContext:
                runAsNonRoot: true
                runAsUser: 1001
                env:
                - name: BITNAMI_DEBUG
                value: "false"
                - name: TOMCAT_USERNAME
                value: "user"
                - name: TOMCAT_PASSWORD
                valueFrom:
                secretKeyRef:
                name: mws-kt-tomcat
                key: tomcat-password
                - name: TOMCAT_ALLOW_REMOTE_MANAGEMENT
                value: "0"
                ports:
                - name: http
                containerPort: 8080
                livenessProbe:
                httpGet:
                path: /
                port: http
                failureThreshold: 6
                initialDelaySeconds: 120
                periodSeconds: 10
                successThreshold: 1
                timeoutSeconds: 5
                readinessProbe:
                httpGet:
                path: /
                port: http
                failureThreshold: 3
                initialDelaySeconds: 30
                periodSeconds: 5
                successThreshold: 1
                timeoutSeconds: 3
                resources:
                limits: {}
                requests:
                cpu: 300m
                memory: 512Mi
                volumeMounts:
                - name: data
                mountPath: /bitnami/tomcat
                volumes:
                - name: data
                persistentVolumeClaim:
                claimName: mws-kt-tomcat

                NOTES:
                CHART NAME: tomcat
                CHART VERSION: 10.2.3
                APP VERSION: 10.0.21

                ** Please be patient while the chart is being deployed **

                1. Get the Tomcat URL by running:

                NOTE: It may take a few minutes for the LoadBalancer IP to be available.
                Watch the status with: 'kubectl get svc --namespace default -w mws-kt-tomcat'

                export SERVICE_IP=$(kubectl get svc --namespace default mws-kt-tomcat --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
                echo "Tomcat URL:            http://$SERVICE_IP:/"
                echo "Tomcat Management URL: http://$SERVICE_IP:/manager"

                2. Login with the following credentials

                echo Username: user
                echo Password: $(kubectl get secret --namespace default mws-kt-tomcat -o jsonpath="{.data.tomcat-password}" | base64 --decode)




-  check the generated templates by passing in the templates options

        helm template bitnami/tomcat --generate-name --name-template "mws-{{randAlpha 2 | lower}}"


- get the release notes

        helm get notes mws-de

- this will give you notes 
        helm status mws-de

- get the release records/history? 

        helm history mws-de
- ouput 
        REVISION	UPDATED                 	STATUS  	CHART        	APP VERSION	DESCRIPTION     
        1       	Sun May 22 00:15:21 2022	deployed	tomcat-10.2.3	10.0.21    	Install complete


- another usefull comand 

        kubectl get secret sh.helm.release.v1.mws-de.v1 -o yaml
                                [secret name]

- list secrets with 

        kubectl get secrets





# Section 5 Creating Charts #

## 33 Creating First Chart ##

- to create a chart, by default helm will use nginx to create a chart  

        helm create firstchart

- when you create a chart a directory is created with 

- charts (empty dir) # other charts our chart depends on
- templates (dir) # includes all the templates used to create the kubernetes manifest

        - deployment.yaml
        - _helpers.tpl
        - hpa.yaml # horizontal pod autoscaling 
        - ingress.yaml
        - service.yaml
        - serviceaccount.yaml
        - Notes.txt # hold release notes, values.yaml values are injected in
        - tests (dir)
                - test-connection.yaml
- chart.yaml # metadata of our chart 
- values.yaml # contains all the values that will be used in the templates, can be overridden or used to override defaults
- .helmigore # specifies files you dont want included in your helm chart


## 34 Install The Chart ##

- to install our own custom chart

        helm install myfirstchart firstchart
                       [name]      [path to chart]



- output for nginx

- get pod name and store it in a variable
        
        export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=firstchart,app.kubernetes.io/instance=fcc" -o jsonpath="{.items[0].metadata.name}")

- get port number and store it in a variable
        
        export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
        
- where to view application, must port forward first        

        echo "Visit http://127.0.0.1:8080 to use your application"

- port forward outside the cluster using the previously created variables

        kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT


## 35 Chart YAML Part 1 ##

firstchart/Chart.yaml

- learning about the chart.yaml file

- contains metadata of the project 
- this info can be used to reference this chart on the GUI 
- 3 mandatory elements, apiVersion, name, version, everything else is optional

- apiVersion determines the structure of the rest of the document. 

- helm 3.0 uses apiVersion: v2

- description:  can be anything description about the chart

- type : can either be an application or a library 

        # A chart can be either an 'application' or a 'library' chart.
        #
        # Application charts are a collection of templates that can be packaged into versioned archives
        # to be deployed.
        #
        # Library charts provide useful utilities or functions for the chart developer. They're included as
        # a dependency of application charts to inject those utilities and functions into the rendering
        # pipeline. Library charts do not define any templates and therefore cannot be deployed.

- when we are using a chart for installation we will use the application type 

- library type has no templates and is not used for installation, it provides reusable functions that can be used in other charts 

- version: the version of the chart

- appVersion : version of the app


## 36 Chart YAML Part 2 ##
- can provide an icon iwth a jpg or url, tool like rancher will use this when rendering your app

- keywords: words that match your project, in the form of yaml list 

- home: url of the project

- sources : list of sources, points to the url for your source code,

- maintainers: list of maintainers for your chart 

## 37 Templates In Brief

firstchart/templates

- templates folder is the meat of every helm chart

- all these files are responsible for generating the final manifest

- the are built with yaml and go template syntax

## 38 Helpers File ##
firstchart/templates/_helpers.tpl

- _helpers.tpl tpl = template 

- does not generate any kubernetes manifest, but houses methods that will be used in the other files 

- defines methods for example that return the name of full name of the app, take the name from the chart and return it to whatever file requests it, also hs methods to create labels that align with what kubernetes is expecting, this allows us to not have to hardcode strings into all these files, so we only have to change most values in 1 spot. 

## 39 Values Yaml ##

firstchart/values.yaml

- includes default values to be used across the template files, when another value is not provided these defaults are used

- they can be overridden by using the --values option and own file or --set when doing installation

- replicaCount: first piece of our values.yaml, says how many pods we want 

- image: spec of the image we want to use, the pullpolicy  and any tags, can also specify the registry, these are referenced in the deployment.yaml, can also pass in any imagePullSecrets to authenticate with the repo 

- serviceAccount: tells us whether to create onem any annotations or names to be passed on to the service account. 

- pod annotations

- pod security context

- security context 

- services : determines how our ports are exposed:  ClusterIP, NodePort, LoadBalancer

- ingress: can be configured, must have an ingress controller running in the cluster 

- resources: determine how much resources our pods will need

- autoscaling: set up autoscaling config


**Can customize which nodes our pods can run on**
- nodeSelector

- toleration

- affinity

## 40 Helm Package ##

- how to package a chart in order to distribute it across projects, deploy it though pipelines etc ...

        helm package firstchart
                        [chart name]

- output 

        Successfully packaged chart and saved it to: /home/cfech/helm/firstchart-0.1.0.tgz

- helm install knows how to install a tgz file

- can pass additional flags 

- update dependencies 

        helm package firstchart [--dependencies-update ] [-u (dependencies update shortcut)]
                        [chart name]

- change where the .tgz file goes with -d or --destination 

        helm package firstchart --d file/path
                        [chart name]

## Assignment 3 ##

- learn how to ignore any files that are in the directory if we want them to no be in the chart

- done with a .helmignore file

- if the package command see a helmignore it will ignore the files referenced in there and not include them in the .tgz

## 41 Helm Lint ##

- lints our yaml files in the chart and checks if there are any syntax issues 

        helm lint firstChart 

- uses 3 levels of lint: info, warning and error 

## Quiz 3 Create Charts ##

Question 1:
The chart type in the chart yaml can have installation and library as values


        false, application and library

Question 2:
Which of the following files will have the default values for a chart used during a release

        values.yaml

Question 3:
Which of the following flag can be used in the helm package command to store the packaged chart in to a particular directory

        -d

Question 4:
Which of the following command can be used to find errors in the chart files

        helm lint

# Section 6 Templates Deep Dive #

- templates are the key for every helm chart
- using go language text templating engine

## 43 Template Actions ##

- basic and most used templating syntax is actions , use the {{}} syntax
- whatever is inside the braces is an action, this can include variables, loops and functions

        {{"This is an"}}, {{"Action"}} 

- most actions start with a hyphen, the hyphen removes any leading or trailing whitespace in order to comply with yaml 

        {{- "This is an"}}, {{- "Action"}}

## 44 Template Information ##
https://helm.sh/docs/chart_template_guide/builtin_objects/

examples in firstchart/templates/deployment.yaml

- when helm renders a template it passes all the information as a single object that we represent by "."

- can then reference subObjects of that "." Object

        .Values.replicas

        .Values.autoscaling.enabled

. has several other sub objects, such as chart and release

        {{.Chart.Name}}
        {{.Chart.Version}}
        {{.Chart.AppVersion}}
        {{.Chart.Annotations}}

- release object 

        {{.Release.Name}}
        {{.Release.Namespace}}
        {{.Release.IsInstall}}
        {{.Release.IsUpgrade}}
        {{.Release.Service}}

- can get the information of the current template

        {{.Template.Name}}
        {{.Template.BasePath}}

## 45 Pipelines ##

- there is a pipe symbol that pass the output of first function to the next function

        labels:
          {{- include "firstchart.labels" . | nindent 4 }}

- default function will take the values that is passed in or provide a default is one is not 

- uppers sends string to upper case

- quotes adds in quotes 

        {{ .Values.my.custom.data | default "not my data" | upper | quote}}

## 46 Functions ##
https://helm.sh/docs/chart_template_guide/function_list/#nindent

- there are many functions used throughout yaml files

- nindent = new ling indent, to handle the indentation yaml needs , it adds a new line and x amount of spaces we ask for 4 in this case

        {{- include "firstchart.labels" . | nindent 4 }}

- toYaml converts objects to yaml

        {{- toYaml . | nindent 8 }}
## 47 Use Conditional Logic ##
- see my flag in values.yaml being reference in deployment.yml

        my: 
        flag: true

        {{- if .Values.my.flag}}
          {{"If output" | nindent 2}}
        {{- end}}

- could also have an if else

        {{- if .Values.my.flag}}
        {{"If output" | nindent 2}}
          {{.Chart.Name}}
          {{.Chart.Version}}
          {{.Chart.AppVersion}}
          {{.Chart.Annotations}}
          {{.Release.Name}}
          {{.Release.Namespace}}
          {{.Release.IsInstall}}
          {{.Release.IsUpgrade}}
          {{.Release.Service}}
          {{.Template.Name}}
          {{.Template.BasePath}}
        {{- else }}
          {{"ELSE"}}
        {{- end}}

- could also use if not 

         {{- if not .Values.my.flag}}
        {{"If output" | nindent 2}}
          {{.Chart.Name}}
          {{.Chart.Version}}
          {{.Chart.AppVersion}}
          {{.Chart.Annotations}}
          {{.Release.Name}}
          {{.Release.Namespace}}
          {{.Release.IsInstall}}
          {{.Release.IsUpgrade}}
          {{.Release.Service}}
          {{.Template.Name}}
          {{.Template.BasePath}}
        {{- else }}
          {{"ELSE"}}
        {{- end}}

- also have an 

        {{- if and .Values.my.flag .Values.my.flag1}}

- also have or 

        {{- if or .Values.my.flag .Values.my.flag1}}


## 48 Use With ##

- with is another conditional
- it will only display the item is something is present , here says if he have some values in >Values.my.countries then change them to yaml, format and inject them 

- in toYaml . starts where with leaves off so at the list of countries, could use $. to start back at the .Values root

        {{- with .Values.my.countries}}
        {{"Output of with" | nindent 2}}
        {{- toYaml . | nindent 2 -}}
        {{- else -}}
        {{"With conditional countries was empty" | nindent 2}}
        {{- end }}

## 49 Define Variables ##
- can define custom variables if we want 

        {{/*can assign variables*/}}
        {{ $myBool := true}}
        {{ $myString := "a string"}}
        {{ $myNum := 5 }}

        {{/*can also assign variables from the values file, once a variable is assigned it cannot later be overriden*/}}
        {{ $myFlag := .Values.my.flag}}

        {{- if $myBool }}
        {{"My bool is true here is a string :"}}
        {{$myString | nindent 2}}
        {{- else }}
        {{"My bool is false here is a number:" | nindent 2}}
        {{$myNum | nindent 2}}
        {{- end }}

## 50 Using Loops ##
        {{/* to loop we use range, . is the current value, can also manage indentation without the nindent fucntion */}}
        looping:
          {{- range .Values.my.loop}}
          - {{"Here is a loop"}}
          - {{.}}
        {{- end }}

## 51 Loop On Dict Types ##
- to loop dictionaries/objects must assign the key and value to the dictionary and then can use the vars in the loop

        {{/* looping through the image dictionary */}}
        loopingDicts:
        {{- range  $key, $value := .Values.image}}
        - {{$key}}: {{$value}}
        {{- end }}

## 53 Debugging Templates ##
- if you have a bug in your template, use dry run and template commands. Dry run performs validation, template does not

## 54 Helm Get Manifest ##
- can see the running manifest on the cluster by running

        helm get manifest fcc
                          [chart name]

## 55 Helpers tpl File ##
- define shared functions used across the chart
- always starts with a name function to easily reference the chart name

- uses syntax 
        
        comment on function
        name of function, what is it called by
        logic of section 
        end of function

- can use helper functions in other template helper functions by using the "include" function to include your created template function

## 56 Create And Use Custom Templates ##
- fololows this syntax 

        {{/* my template comment */}}
        {{- define "firstchart.mytemplatefunction" -}}
        {{- .Values.myvalue}}
        {{- end}}

- include in a function file with the include function, have to pass in the "." as that is the overall object 

        {{- include "firstchart,mytemplatefunction" .}}

## Quiz 4 Templates Deep Dive ##

Question 1:
Which of the following represents the root object created by helm accessible in all the template files

        . 

Question 2:
Which of the following is used to pipe multiple commands and pass output of one command to the next inside a template action


        |

Question 3:
Which of the following helm templating functions can be used to convert objects to yaml


        toYaml

Question 4:
Which of the following changes the scope of . from root to the current element


        with

Question 5:
Which of the following operator is used to initially assign a value to a variable

        :=

Question 6:
Once we assign a value to variable using a particular type if we assign a different type later it will have effect


        false

Question 7:
Which of the following is used to loop in a template

        range

## Assignment 4 Create Chart ##
see tc

- create a chart

        helm create tc

- add tomcat as a dependency to chart yml under dependencies

dependencies:
  - name: tomcat
    version: "10.1.21"
    repository: "https://charts.bitnami.com/bitnami"

- pass it some values 

        tomcat:
          service:
            type: NodePort
            nodePort: 30007

- package the chart, -u to install the dependencies

        helm package tc -u

- install the chart

        helm install tc tc-...tgz


# Section 7 Advanced Charts #

see /dependencies

## 58 Add Dependencies ##
- add the dependencies section at the bottom of a chart yaml file
- can use local repo name if you know it, see all installed with ```helm repo list```


        dependencies:
          - name: mysql
             version: "8.8.6"
            repository: http://charts.bitnami.com/bitnmai


- download the dependencies with, they are stored in the charts folder

        helm dependency update myChart

- then can just install the chart

## 59 Using Version Range ##

- can use a range of versions 

- look for anything equal to or greater than this version
        dependencies:
          - name: mysql
             version: ">=8.8.6"
            repository: http://charts.bitnami.com/bitnmai

- greater and less than option

dependencies:
          - name: mysql
             version: ">=8.8.6 and < 9.0.0"
            repository: http://charts.bitnami.com/bitnmai


- advantage of this is to have the latest version out application can handle 


- some special operators, the caret ^ and the tilde ~

- ^ means , use the next major version 

- can us placeholders for version ^1.3.x will look for the next major version less then 2.0.0

- ~ orks for patching minor versions, ~1.3.4 will look for something > 1.3.4 < 1.4.0

## 60 Using Repo Name ##
- can use a local repo name instead of the url if you want

        dependencies:
          - name: mysql
            version: ">=8.8.6 and < 9.0.0"
            repository: @bitnami
## 61 Use Dependencies Conditionally ##
- define a property in values.yaml

        mysql:
          enabled: false

- update the depenedencies in chart.yml with the condition flag 

        dependencies:
          - name: mysql
            version: ">=8.8.6 and < 9.0.0"
            repository: @bitnami
            condition: mysql.enabled


## 62 Use Multiple Conditional Dependencies ##

- can just add it to the dependencies in the chart.yaml

in values.yaml

        apache:
          enabled: true

- in chart yaml 

        dependencies:
          - name: mysql
            version: "8.8.6"
            repository: http://charts.bitnami.com/bitnami
            condition: mysql.enabled
          - name: apache
            version: "8.8.6"
            repository: http://charts.bitnami.com/bitnami
            condition: apache.enabled

- while the above would work instead of having to have multiple listed we could utilize the tags , tags takes a list of values

- now our chart.yaml  is 

        dependencies:
          - name: mysql
            version: "8.8.6"
            repository: http://charts.bitnami.com/bitnami
            tags:
              - enabled

          - name: apache
            version: "8.8.6"
            repository: http://charts.bitnami.com/bitnami
            tags:
              - enabled

- values.yaml 

        tags:
          enabled: true


## 63 Pass Values To Dependencies ##

## 64 Read Values From Child Charts ##

## 65 Use Values Not Exported ##

## 66 Hooks ##

## 67 Create And Use A Hook ##

## 68 Testing Introduction ##










































# HELM Examples #
- cant leave in real yaml cause will break the installation 


        {{/*can assign variables*/}}
        {{ $myBool := true}}
        {{ $myString := "a string"}}
        {{ $myNum := 5 }}

        {{/*can also assign variables from the values file, once a variable is assigned it cannot later be overriden*/}}
        {{ $myFlag := .Values.my.flag}}


        {{/*#using if logic
        # - is to remove leading whitespace
        # nininent is to format yaml */}}

        {{- if .Values.my.flag}}
        {{"If output" | nindent 2}}
        {{.Chart.Name}}
        {{.Chart.Version}}
        {{.Chart.AppVersion}}
        {{.Chart.Annotations}}
        {{.Release.Name}}
        {{.Release.Namespace}}
        {{.Release.IsInstall}}
        {{.Release.IsUpgrade}}
        {{.Release.Service}}
        {{.Template.Name}}
        {{.Template.BasePath}}
        {{ .Values.my.custom.data | default "not my data" | upper | quote}}
        {{- else }}
        {{"ELSE"}}
        {{- end}}

        {{/*with will only display if there is contents in countries, if it is empty it will display the else case */}}
        countries:
        {{- with .Values.my.countries}}
        {{"Output of with" | nindent 2}}
        {{- toYaml . | nindent 2 -}}
        {{- else -}}
        {{"With conditional countries was empty" | nindent 2}}
        {{- end }}

        {{/*checking my custom variable */}}
        myBool:
        {{- if $myBool }}
        {{"My bool is true here is a string :"}}
        {{$myString | nindent 2}}
        {{- else }}
        {{"My bool is false here is a number:" | nindent 2}}
        {{$myNum | nindent 2}}
        {{- end }}

        {{/* to loop we use range, . is the current value, can also manage indentation without the nindent fucntion */}}
        looping:
        {{- range .Values.my.loop}}
        - {{"Here is a loop"}}
        - {{.}}
        {{- end }}

        {{/* looping through the image dictionary */}}
        loopingDicts:
        {{- range  $key, $value := .Values.image}}
        - {{$key}}: {{$value}}
        {{- end }}








# Comments In Helm Templates #
- must use /* */ or it will be read in, yaml comments dont work
        {{/* .Values.my.cusom.data*/}}
## Other stuff ##
        
        # .bashrc

        # Source global definitions
        if [ -f /etc/bashrc ]; then
        . /etc/bashrc
        fi

        # Uncomment the following line if you don't like systemctl's auto-paging feature:
        # export SYSTEMD_PAGER=

        # User specific aliases and functions


        # .bash_profile

        # Get the aliases and functions
        if [ -f ~/.bashrc ]; then
        . ~/.bashrc
        fi

        # User specific environment and startup programs

        PATH=$PATH:$HOME/.local/bin:$HOME/bin

        export PATH
        eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"

