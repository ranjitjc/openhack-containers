# OpenHack Notes

## Create a network

### <https://docs.docker.com/network/>

### <https://docs.docker.com/engine/reference/commandline/network_connect/>

> docker network create sqlnet

## Install sql server

> docker run --network sqlnet -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Sql2017" -p 1433:1433 --name sql1 -d  mcr.microsoft.com/mssql/server:2017-latest

## Create a database

>docker exec sql1 /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Sql2017' -Q "CREATE DATABASE mydrivingDB"

## Load the database

### Poweshell (It worked)

>docker run --network sqlnet -e SQLFQDN=sql1 -e SQLUSER=SA -e SQLPASS=Password@123 -e SQLDB=mydrivingDB openhack/data-load:v1

### bash shell

>docker run  -e SQLFQDN=sql1 -e SQLUSER=SA -e SQLPASS=Password@123 -e SQLDB=mydrivingDB openhack/data-load:v1

## login to sql server

### <https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-2017&pivots=cs1-bash>

>docker exec -it sql1 "bash"

## build docker container from source components

>C:\SPEG\github\openhack\kubernetes\openhack-containers\src\poi> docker build -t poi:v1 -f ..\..\dockerfiles\Dockerfile_3 .

## List all containers

> docker ps -a

## check the container log for poi

>docker logs poi

## Force remove the container even if it is running

>docker rm -f poi

### Create a Password-based Azure service principal (Not used)

> hacker@Azure:~$ az ad sp create-for-rbac --name tripInsights5

Changing "tripInsights5" to a valid URI of "http://tripInsights5", which is the required format used for service principal names
Creating a role assignment under the scope of "/subscriptions/c33ec5ff-f1e4-402b-b532-7d4c5bf75117"
  Retrying role assignment creation: 1/36
{
  "appId": "5bdd69dc-175f-4c2b-9288-63961857da02",
  "displayName": "tripInsights5",
  "name": "http://tripInsights5",
  "password": "SQy-J4zVVmO2wZ8KasYV136RjBVaGCB8-5",
  "tenant": "9d33f284-17b6-4478-8d4f-f743785974ff"
}

### azure login with above service principal  (Not used)

> az login --service-principal

## Login to azure subscription, this command opens browser to authenticate

> az login

## Login to registry

> docker login registrykme9469.azurecr.io

#### <https://docs.microsoft.com/en-us/azure/container-registry/container-registry-concepts>

## Create a tag based on local image

> docker tag poi:v1 registrykme9469.azurecr.io/tripinsights/poi

## push tjhe image to azure container registry

#### <https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli>

> docker push registrykme9469.azurecr.io/tripinsights/poi

#### <https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks>

## Create a resource

> az group create --name OpenHack-team12-RG --location westus2

## Create an AKS cluster with 1 node with basic networking

> az aks create --resource-group OpenHack-team12-RG --name openHackAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys

## Deploy the sample image from ACR to AKS

### Ensure you have the proper AKS credentials

#### <https://docs.microsoft.com/en-us/azure/aks/cluster-container-registry-integration>

> az aks get-credentials --resource-group OpenHack-team12-RG --name openHackAKSCluster

## Authenticate with Azure Container Registry from Azure Kubernetes Service

### Configure ACR integration for existing AKS clusters

### <https://thorsten-hans.com/3-ways-to-integrate-acr-with-aks>

#### <https://docs.microsoft.com/en-us/azure/aks/cluster-container-registry-integration>

>az aks update -n openHackAKSCluster -g OpenHack-team12-RG --attach-acr registrykme9469.azurecr.io

## Kubernetes

### <https://www.stratoscale.com/blog/kubernetes/kubernetes-exposing-pods-service/>



***

> ## References
>
> - ### Service principals with Azure Kubernetes Service
>
>   #### <https://docs.microsoft.com/en-us/azure/aks/kubernetes-service-principal>
>
> - ### Kubernetes deployments
>
>   #### <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/>
>
> - ### Azure networking
>
>   - #### Network concepts for applications in Azure Kubernetes Service
>
>       #### <https://docs.microsoft.com/en-us/azure/aks/concepts-network#azure-virtual-networks>
>
>   - #### Configure Azure CNI networking
>
>       #### <https://docs.microsoft.com/en-us/azure/aks/configure-azure-cni#configure-networking---cli>
>
>   - #### Create a virtual network for multi-tier applications
>
>       #### <https://docs.microsoft.com/en-us/azure/virtual-network/scripts/virtual-network-cli-sample-multi-tier-application>
>
> - ### Build and deploy container images in the cloud with Azure Container Registry Tasks
>
>   #### <https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tutorial-quick-task>
>
> - ### YAML
>
>   #### <https://k8syaml.com/>
