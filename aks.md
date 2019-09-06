# AKS

## Introduction

This tutorial aims to showcase the work of microservices demo
application in private AKS cluster.
This tutotial assumes base knowlegde of Azure and Kubernetes concepts.

## Accessing the Cloud Shell

### Steps

- Login to Azure Cloud Shell visiting https://shell.azure.com
- In the left top corner, select environment by choosing `Bash`
- Show the active account name with this command:

```bash
az account show
```

expected output:

```bash
{
  "environmentName": "AzureCloud",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "isDefault": true,
  "name": "Subscription name",
  "state": "Enabled",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "user": {
    "cloudShellID": true,
    "name": "user@email.com",
    "type": "user"
  }
}
```

### Expected result

- Cloud shell commands can be executed

## Check dependencies

### Steps

- In Cloud Shell, check if all needed prerequisites are installed
- Use the below BASH commands, copy them and paste in Cloud Shell
- Check terraform

```bash
terraform version
```

expected output:

```bash
Terraform v0.12.7
```

- Check kubectl

```bash
kubectl version
```

expected output:

```bash
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
Error from server (BadRequest): the server rejected our request for an unknown reason
```

### Expected result

- Terraform command can be executed
- kubectl command can be executed

## Deploy AKS with help of az cli

Here is the [full instruction](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) how to do it, we will use shorter version.

### Steps

- Create a demo resource group

```bash
az group create --name aks-istio-demo-rg --location westeurope
```

expected output:

```bash
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/aks-istio-demo-rg",
  "location": "westeurope",
  "managedBy": null,
  "name": "aks-istio-demo-rg",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

- Deploy AKS cluster

```bash
az aks create \
    --resource-group aks-istio-demo-rg \
    --name aks-istio-demo-aks \
    --node-count 3 \
    --enable-addons monitoring \
    --no-ssh-key
```

### Expected result

- Long running AKS creation operation is in progress and completes successfully

## Connect to the AKS cluster and verify successful deployment

### Steps

- To configure kubectl to connect to your Kubernetes cluster, use the az aks get-credentials command. This command downloads credentials and configures the Kubernetes CLI to use them.

```bash
az aks get-credentials --resource-group aks-istio-demo-rg --name aks-istio-demo-aks
```

- To verify the connection to your cluster, use the kubectl get command to return a list of the cluster nodes.

```bash
kubectl get nodes
```

expected output:

```bash
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-0   Ready    agent   2m49s   v1.13.10
aks-nodepool1-xxxxxxxx-1   Ready    agent   2m45s   v1.13.10
aks-nodepool1-xxxxxxxx-2   Ready    agent   2m52s   v1.13.10
```

### Expected result

- AKS cluster deployed and in working condition
- Kubectl tool connected to AKS cluster

## Install Istio

The full instalation guide is available on Istio official site [here](https://istio.io/docs/setup/kubernetes/install/).

### Steps

- Check latest version of Istio
Go to [releases](https://github.com/istio/istio/releases) page and write down latest version
- Download latest Istio release by executing the command (where 1.2.5 latest version):

```bash
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.2.5 sh -
```

- Ensure that istio-1.2.5 folder appeared in root
- Install all the Istio Custom Resource Definitions (CRDs) using kubectl apply, and wait a few seconds for the CRDs to be committed in the Kubernetes API-server:

```bash
cd istio-1.2.5
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
```

- Install Istio

```bash
kubectl apply -f install/kubernetes/istio-demo.yaml
```

- Verify the installation

```bash
kubectl get svc -n istio-system -o name
```

expected result

```bash
service/grafana
service/istio-citadel
service/istio-egressgateway
service/istio-galley
service/istio-ingressgateway
service/istio-pilot
service/istio-policy
service/istio-sidecar-injector
service/istio-telemetry
service/jaeger-agent
service/jaeger-collector
service/jaeger-query
service/kiali
service/prometheus
service/tracing
service/zipkin
```

- Get External LoadBalancer IP address and write down it
Creating of this IP might take some time, so please wait a bit if it is not available at the moment.

```bash
kubectl get svc istio-ingressgateway -n istio-system -o jsonpath="{.status.loadBalancer.ingress[*].ip}"
```

expected result (some IP)

```bash
40.115.41.157
```

### Expected result

- Istio installed and in working condition
- External LoadBalancer created and attached to istio-ingressgateway
- Public IP address assigned on LoadBalancer

## Clone aks-private-istio-lab repository

### Steps

- In cloud shell, execute

```bash
git clone https://github.com/Shenderchuk/aks-private-istio-lab.git
```

- Check if files were downloaded by inspecting `aks-private-istio-lab` folder

### Expected result

- Lab  repository cloned and available for use
