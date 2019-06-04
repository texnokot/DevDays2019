# https://medium.com/microsoftazure/kubernetes-github-and-devops-putting-it-all-together-to-increase-confidence-in-pull-request-3797e8fea3f7

# Azure login
az login --use-device-code

# Create RG where to put all resources
az group create --name DevDaysOslo2019 --location westeurope
# Set up AKS cluster
az aks create -g DevDaysOslo2019 -n aksdevdays --location westeurope --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
# Enable Azure Dev SPaces on AKS
az aks use-dev-spaces -g DevDaysOslo2019 -n aksdevdays --space dev --yes
# Meanwhile create project in Azure DevOps and clone repository 
# Import in project: https://github.com/Azure/dev-spaces.git
# Going to use BikeSharingApp 
# clone project to your computer

# Retrieve the HostSuffix for dev (1st time installs Dev Spaces Package)
azds show-context 

# Output for my version: 
aksdevdays  DevDaysOslo2019  dev       5bsfq5jg95.weu.azds.io

# in home directory of code:
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

# list services:
azds list-uris

# Configure CI/CD pipeline
# ENable multi stage preview feature

# ACR create
az acr create --resource-group DevDaysOslo2019 --name acrdevdays --sku Basic
# check it
az acr login --name acrdevdays

# Grant AKS access to ACR
# make aksgrant.sh script

# Create a service connections strings
# Docker Registry (Azure Container Registry)
# Kubernetes (Azure Subscription)
# Azure Resource Manager (Service Principal Authentication)

#Navigate to pipelines and create a new pipeline
# from: DevDaysOslo2019/samples/BikeSharingApp/Bikes/azds_pipeline.yaml
# Replace service connections strings
Replace CONTAINER-REGISTRY-CONNECTION-NAME (line 20) with the Docker Registry service connection name.
Replace CONTAINER-REGISTRY-URL (line 23). You can find this information in the Azure Portal under your Azure Container Registry. It should look something like this: login server: builddemo.azurecr.io.
Replace KUBERNETES-CONNECTION-NAME (line 26) with the Kubernetes service connection name.
Replace the KUBERNETES-CLUSTER-NAME (line 29) with your AKS cluster name.
Replace the KUBERNETES-CLUSTER-RESOURCE-GROUP (line 32) with your Azure Resource Group name in which you have created the cluster.
Replace the AZURE-CONNECTION-NAME (line 35) with your Azure Resource Manager service connection name.
Replace GITHUB-CONNECTION-NAME (line 38) with your GitHub service connection name.

# Run it, explore it