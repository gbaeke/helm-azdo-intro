# Deploying Helm Charts with Azure DevOps

This repository accompanies the following video on YouTube: https://youtu.be/1bC-fZEFodU

Direct link to the demo part: https://youtu.be/1bC-fZEFodU?t=756

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
- import this repository
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

