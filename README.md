# Deploying Helm Charts with Azure DevOps

This repository accompanies the following video on YouTube: https://youtu.be/1bC-fZEFodU

Direct link to the demo part: https://youtu.be/1bC-fZEFodU?t=756

You need an Azure subscription and the following resources:
- Azure Container Registry (ACR): to store the container image and Helm chart
- Azure Kubernetes Services (AKS): to deploy the app to qa and prod namespaces
    - do not deploy a private cluster; we will use MS hosted build agents that need to talk to the K8S API server via a public endpoint
    - cluster can be AAD integrated: pipeline contains comments to use kubelogin when you do not want to use --admin to obtain admin credentials to the cluster
- service principal in Azure AD: used to authenticate to Azure and obtain a kube config file to access the AKS cluster; make sure the service principal has the required Azure role (e.g., Contributor)
- AKS service principal (or managed identity) has AcrPull to ACR (if you used the portal and selected or created your container registry you should be good)

variables in ci pipeline:
- registryLogin: admin login to Azure Container Registry (ACR)
- registryName: azurecr.io will be appended to this variable to obtain the FQDN to ACR
- registryPassword: password to login to ACR

variables in ./common/ci-vars.yaml:
-  helmVersion: version of helm to download
-  registryServerName: '$(registryName).azurecr.io' --> FQDN for ACR; uses registryName pipeline variable
-  projectName: ${{ parameters.projectName }} 
-  imageName: ${{ parameters.projectName }} --> used as part of the full name of the image

variables in cd pipeline:
- same 3 variables as in ci pipeline
- aks: name of AKS cluster (just the short name e.g., mycluster)
- aksSpId: app id of the service principal
- aksSpSecret: password (secret) of the service principal
- rg: resource group that contains the AKS cluster (e.g. myrg)

variables in ./common/cd-vars.yaml:
- helmVersion
- registryServerName: '$(registryName).azurecr.io'
- projectName

# what you need to do

- create a project in Azure Devops
- import this repository (or use GitHub for the repo, that's fine too)
- add the CI pipeline and use ci.yaml as the source
- add CI pipeline variables (see above)
- set the values of the CI pipeline variables and variables in ./common/ci-vars.yaml to your ACR, AKS, etc...

Next...
- add the CD pipeline and use cd.yaml as the source
- add CD pipeline variables (see above)
- set the values of the CD pipeline variables and variables in ./common/cd-vars.yaml to your ACR, AKS, etc...

Then...
- run the pipeline manaually or commit a change to main
- if all goes well, you should have two deployments to your AKS cluster
- you should have two environments; add a manual approval step to the prod environment and add yourself as approver

