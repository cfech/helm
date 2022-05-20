# Helm Kubernetes Packaging Manager for Developers and DevOps on Udemy


https://helm.sh/docs/

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

- helm simplifies kubernetes deployment process by extracting out complexity

- if we need to install a mongodb in a kubernetes cluster we would need to figure out all the pieces needed such as deployment, service, configmap, which can be overwhelming

- with helm we just want to pull a mongodb chart from a chart repository

- this chart has all the required resources and comes with default values with can override if necessary

- helm can will kep history of our revisions

- Helm combines templates with values from a values.yml to create dynamic kubernetes deployments

- Use helm cli instead of kubectl to update deployments

- helm utilizes intelligent deployments, it will know which order to create kubernetes resources, we dont have to specify

- utilizes life cycle hooks, they can be hooked into the lifecycle of the application 

- built in support for charts downloaded from the repository. Verifies signatures and hashes when installing packages. 


## 8 Charts and Repos ##

- Charts are stored in chart repositories , bitnami is a famous one

https://bitnami.com/stacks/helm
https://github.com/bitnami/charts

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


- installed conntrack for minkube setup

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

        Watch the deployment status using the command: kubectl get pods -w --namespace default

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

-- to remove a package from cluster 

    helm uninstall [name]


    Minikub start

Kubectl 
Helm 
Minikube ssh

Minkube dashboard

## 14 Providing custom values ##

## 15 Helm Upgrade ##

## 16 More about upgrade ##

## 13 List and Uninstall ##


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