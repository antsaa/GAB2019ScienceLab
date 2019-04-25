![Global Azure Bootcamp 2019 - Science Lab](https://github.com/intelequia/GAB2019ScienceLab/raw/master/images/ScienceLab2019.jpg)

# Global Azure Bootcamp 2019 - Science Lab
This project contains instructions to deploy the Global Azure Bootcamp 2019 Science Lab

# Quickstart
To quickly deploy the infrastructure for the science lab (Azure Kubernetes Cluster), click on the button below. **If it's your first deployment, we strongly recommend to read the instructions below.**

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fantsaa%2FGAB2019ScienceLab%2Fmaster%2Flab%2F%2Finfrastructure%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

# Introduction

## PLEASE ADVISE

**This is a fork of the official repository. It uses Azure Kubernetes Service instead of ACI. Therefore the costs described in the ACI version do not apply for this fork. AKS is charged based on the node VMs only, so the price will depend on the amount and size of VMs you deploy.**

This project contains all the source code for the Global Azure Bootcamp 2019 Science Lab. Created by David Rodriguez ([@davidjrh](http://twitter.com/davidjrh)), Martin Abbott ([@martinabbott](http://twitter.com/martinabbott)) and Santiago Porras ([@saintwukong](http://twitter.com/saintwukong)) for the Global Azure Bootcamp 2019 Science Lab running Enric Pallé, Diego Hidalgo and Sebastian Hidalgo's Machine Learning algorithms for exoplanet hunting at the [Instituto de Astrofisica de Canarias](http://www.iac.es/index.php?lang=en) using TESS mission data from NASA.

See more at https://global.azurebootcamp.net/global-azure-science-lab-2019/

# Getting Started

## Requirements

In order to participate on the GAB Science Lab you will need:
* An active Azure subscription. You can signup for a free subscription [here](https://azure.microsoft.com/free/) or use the Azure Passes shared on the Global Azure Bootcamp event. 
* You can deploy the client on any other Docker powered environment (see deployment instructions at the end of this document):
    * locally on your laptop using Docker Desktop, follow instructions at https://www.docker.com/products/docker-desktop
    * on any other environment, check https://docs.docker.com/install/

## Deploying the lab using Azure Kubernetes Service (AKS)
Deploy the lab using Azure Kubernetes Service. 

1. Create a Service Principal
    * `az ad sp create-for-rbac --skip-assignment`
    * Copy or memorize the appid and password attribute values, you'll need these in the next step

2. Click on the deployment button below to start the process (or use CLI, up to you):

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fantsaa%2FGAB2019ScienceLab%2Fmaster%2Flab%2F%2Finfrastructure%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

2. Fill the form. You can get info about each field if you hold the cursor over the info icon.
    * You can use default values where set
    * For SP client ID use the appid from previous step and for secret the password from previous step
    * Also enter an SSH public key, if you don't have one you can create one by running `ssh-keygen -t rsa`

3. Fetch credentials for your newly created cluster:
    * `az aks get-credentials -g turkulab -n gabakscluster`

4. Install the NGINX ingress controller and load balancer:
    * `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/mandatory.yaml`
    * `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/provider/cloud-generic.yaml`

5. Apply deployment:
    * `kubectl apply -f lab/manifests/deployment.yaml`

6. Apply service:
    * `kubectl apply -f lab/manifests/service.yaml`

7. Apply ingress controller:
    * `kubectl apply -f lab/manifests/ingress.yaml`

# Decomissioning the Science Lab
Your lab deployment will continue working processing inputs until you delete the deployment resources. Note that this year our intention is to continue processing information after the GAB day. 

In order to delete your deployment:
1. Select the Resource Group containing your AKS cluster
2. Click on Delete and confirm by typing your resource group name
3. Note there is a separate resource group containing the cluster infrastructure which should be automatically deleted when the cluster is deleted - but it is adviced to verify this

Thanks for your support on Global Azure Bootcamp 2019 Science Lab. Live Long and Prosper!

# Frequently Asked Questions
1. **How much will cost?**

Azure Kubernetes Service pricing is based on the nodes only - mainly node count and size. For least cost deploy with nodeCount 1 and set the VM size to something relatively small (for example A1 series) in the ARM template.

2. **Can I start crunching data before April 27th?**

You can deploy the lab before April 27th just for testing purposes, but note that we will reset all the data, stats and dashboards on April 27th. 

3. **Can I continue processing data after April 27th?**

Yes, this year we want to continue hosting the Science Lab after the Global Azure Bootcamp day. Our intention is to continue processing data until the end of the TESS mission.

# Running the lab outside Azure
You can also run the science lab client on Windows, Linux or Mac, just because is implemented as a Docker container. The container image is available at Docker Hub https://hub.docker.com/r/globalazurebootcamp/sciencelab2019

You can deploy the client on any other Docker powered environment:
* locally on your Windows/Mac laptop using Docker Desktop, follow instructions at https://www.docker.com/products/docker-desktop
* on any other environment, check https://docs.docker.com/install/

## Deploying a local container with the science lab client
Once you have Docker installed locally, follow this steps:

1. Create a text file called `variables.env` with the following data (replace the values with your own data):
```
BatchClient__Email=johndoe@foo.com
BatchClient__Fullname=John Doe
BatchClient__TeamName=Global Azure Team
BatchClient__CompanyName=Global Azure Bootcamp Org.
BatchClient__CountryCode=XX
BatchClient__LabKeyCode=THE-GAB-ORG
```

2. Run the following Docker command:
```
docker run -d -p 8080:80 --env-file variables.env --restart always globalazurebootcamp/sciencelab2019:latest
```

This downloads the science lab client image and runs it on a container instance. If you browse http://localhost:8080, you will notice how the science lab is progressing.

## Deleting the science lab client on a local environment
Once you don'w want to continue running the science lab, run the following commands:
1. Search the container id by executing the following command and writing down the container id with the name "globalazurebootcamp/sciencelab2019:latest"
```
docker ps -l
```

2. Run the command to delete the container instance:
```
docker rm <containerid> -f
```

# Troubleshooting

## I'm seeing "Minimum client version must be GAB.Client/1.x.x.x. Please, upgrade your container instance to the latest version" on the logs
This is because we needed to break the backwards compatibility with the previous client, and you need to redeploy your container. When running on Azure Container instances, this can be easily accomplished by clicking on the "Restart" button available on the Overview section of your container when using the Azure Portal. 
