# On-Premise Deployments

## Project Dscription:

This project aims to solve on-premise application deployment. This is demonstrated by deploying parse-server on kubernetes. The entire project focueses on resolving issues including Ease of clustered enterprise level deployments,Incremental remotely triggered application updates,Easy remote debugging, Health Alerts and Monitoring, Application Security (with source code protection) and Disaster management.

GitHub repo link: https://blahh

## Installation and Usage Instructions

Tools/Softwares/Services used:

- Kuberntes using Microk8s

- Lens (IDE for Kubernetes) 

- Helm/Helm Charts

- Parse Server

- Prometheus 

- Grafana

- Weavely Cloud

- GitHub

### Pre-requisites

**Note**:I am solving this problem on my local machine as I do not have access to any Cloud services.

Operating System : Ubuntu 18.04 LTE

### Steps

#### Kubernetes Setup using [microk8s](https://microk8s.io/docs):
Initially we need to setup Kubernetes :

Install microk8s :

`sudo snap install microk8s --classic`

`sudo usermod -a -G microk8s $USER`

`sudo chown -f -R $USER ~/.kube`

This will give us Kubectl command line tool and kube config file.

Add an alias in .bashrc as follows to make things simpler:

`
alias kubectl="microk8s.kubectl"`
`

Run `source .bashrc` after making changes to .bashrc file.

Incase config file doesn't exist in ~/.kube directory follow the steps below

`
microk8s config > ~/.kube/config
`

Extra careful while giving read,write and execute permissions 

`chmod 700 ~/.kube/config`

Now let's run kubernetes cluster

`microk8s start`

Check status using the following commands and make sure everything is running.

`kubectl get nodes` (Check if node is ready. We are using a single node.)

`kubectl get all --all-namespaces`



#### [Lens](https://k8slens.dev/) Setup:

Lens is and IDE for working Kubernetes clusters. You just need to pass in the kube config file and it takes care of the rest.

[Installing Lens](https://github.com/lensapp/lens/releases/tag/v3.6.7)

I have downloaded the AppImage which is extremely easy to use. After download give executable permissions to file and run using the following command

`./Lens-3.6.6.AppImage`

Provide path to kubernetes config file and it gets all the information about the cluster.


#### [Helm](https://helm.sh/) Setup:

Helm is the Package Manager for Kubernetes.Helm is the best way to find, share, and use software built for Kubernetes.

[Installing helm](https://helm.sh/docs/intro/install/)

I prefer installing it from script.

Helm now has an installer script that will automatically grab the latest version of Helm and install it locally.

Run the following commands:

`curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3`

`chmod 700 get_helm.sh`

`./get_helm.sh`

#### [Parse Server](https://github.com/parse-community/parse-server) Setup:

Parse Server is an open source backend that can be deployed to any infrastructure that can run Node.js.

We are going to setup parse server using  helm charts. [Artifact Hub](https://artifacthub.io/) has a lot of kubernetes package.
Search for the required package and it provides us with clear instructions about how to use it. We can also do it using command line as follows.

Repo used for pulling parse chart : Bitnami

Bitnami makes it easy to get your favorite open source software up and running on any platform, including your laptop, Kubernetes and all the major clouds.

Pull Bitnami repo

`helm repo add bitnami https://charts.bitnami.com/bitnami`

Search for for the parse package

`helm search repo parse`

We will use bitnami/parse which is stable

Let's inspect the values of bitnami/parse chart

`helm inspect values bitnami/parse >> ~/atlanwork/parse.values`

We need to change the service type from LoadBalance to NodePort as we are running it in local environment.Make the following changes (nodePort: --your-choice(between 30000-32767 type: NodePort)

Create a namespace which helps monitoring easy

`kubectl create namespace parse`

**Deploying parse server using helm chart**

name: parse-server 
namespace: parse

`helm install parse-server bitname/parse --values parse.values -n parse`

Wait for sometime until the parse-server is deployed. Check the status on Lens which is user-friendly.

Now we need to follow the instructions provided by bitnami chart i.e to get the port numbers from services deployed.

Run the following commands


Finally make curl request 

`
curl -X POST \
-H "X-Parse-Application-Id: APPLICATION_ID" \
-H "Content-Type: application/json" \
-d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
http://$SERVICE_HOST/parse/classes/GameScore
`

You should get a response similar to this:

`
{
  "objectId": "2ntvSpRGIK",
  "createdAt": "2016-03-11T23:51:48.050Z"
}
`

#### [Prometheus](https://prometheus.io/) Setup :

Prometheus is an open-source systems monitoring and alerting toolkit originally built at SoundCloud.

Now things get a little interesting. We can setup Prometheus from scratch using helm just like we setup parse-server. However, since 
we installed Lens we can just do it with a click!

Background workflow when we click *install*

- lens-metrics namespace is created
- helm is used to setup prometheus from stable/prometheus helm-chart with metrics enabled (this saves a lot of time metrics are autoamatically enabled which is a huge bonus)

Check the status of lens-metrics namespace and then we can access prometheus from the pod just by a click.

#### [Grafana](https://grafana.com/) Setup:

Grafana has become the world’s most popular technology used to compose observability dashboards with everything from Prometheus & Graphite metrics, to logs and application data to power plants and beehives

We will be using this amazing tool to  query, visualize, alert on, and explore your metrics no matter where they are stored.
In plain English, it provides you with tools to turn your time-series database (TSDB) data into beautiful graphs and visualizations.

We will setup grafana using helm charts as well

Search for for the grafana package

`helm search repo grafana`

We will use bitnami/grafana which is stable

Let's inspect the values of bitnami/grafana chart

`helm inspect values bitnami/grafana >> ~/atlanwork/grafana.values`

We need to change the service type from LoadBalance to NodePort as we are running it in local environment.Make the following changes (nodePort: --your-choice(between 30000-32767) type: NodePort)

Create a namespace which helps monitoring easy

`kubectl create namespace grafana`

**Deploying grafana using helm chart**

name: grafana
namespace: grafana

`helm install grafana bitname/grafana --values grafana.values -n grafana`

Wait for sometime until the grafana is deployed. Check the status on Lens which is user-friendly.

Now we need to follow the instructions provided by bitnami chart i.e to get the port numbers from services deployed.

Run the following commands



#### [Weavely](https://www.weave.works/product/cloud/) Setup:

Weave Cloud is an automation and management platform for development and DevOps teams.

**NOTE**: We will take a free-trail account to demonstrate the use case.

Login to weave cloud and add a cluster

We have to select
- platform: Kubernetes(in our case)
- environment: Generic Kubernetes(in our case)
- install using helm 

***Prior to helm installation, create a namespace "weave" in kubernetes as it is a pre-requisite.***

Now we can add our github repo for setting up CI/CD pipeline.Go to settings>configure and add our github repo url.

Weavely offers prometheus and grafana monitoring as well, however we already have a reliable local setup.

Installation and Setup is complete.


## Use Cases and Edge Conditions

- It is assumed that we 



