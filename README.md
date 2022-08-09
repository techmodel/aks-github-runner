# Github Actions - Self Hosted Agents on AKS

This repo provides instructions and configuration to setup Self Hosted Agents for Github running on an AKS cluster.  It was derived from this [article](https://github.blog/2020-08-04-github-actions-self-hosted-runners-on-google-cloud/) by [John Bohannon](https://github.com/imjohnbo).   This project utilizes terraform and helm to provide support for a repeatable infrastructure as code approach.  The process can also be orchestrated through an **Github workflow**. 

## Setup

Fork this repo and pull your fork to your computer

Cd into the repo

Ensure you have the following dependencies:
- [jq](https://stedolan.github.io/jq/download/)
- [azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) (logged in to a subscription where you have contributor rights)
- [github-cli](https://cli.github.com/) (logged in)
- [Create a github personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) -- and export the value -- export GH_TOKEN=paste_your_token_here

Run the setup.sh script
- Syntax: **. ./setup.sh** [-c CLUSTER_NAME] [-g RESOURCE_GROUP_NAME] [-s SUBSCRIPTION_ID] [-r REGION] (the extra dot is important - https://superuser.com/questions/1136409/what-is-the-dot-space-filename-command-doing-in-bash)
- make setup_cmd provides an example version

This script does the following:
    - Create a service principal for use by terraform
    - Create a storage account to keep the terraform state
    - Create a resource group where your AKS cluster will be deployed
    - Save service principal and other provided variables in github secrets
    
If running locally:

- export the following variables with the appropriate values
    - GITHUB_REPO_OWNER: "your github id"
    - GITHUB_REPO_NAME: "your github repo name"
    - GITHUB_REPO_URL: "https://github.com/${GITHUB_REPO_OWNER}/${GITHUB_REPO_URL}"
- make all_terraform
- make all_ghr

This uses the repo makefile to create your AKS cluster, create an ACR, and deploy the runner to the cluster


### Setting AZURE_CREDENTIALS Secret
https://github.com/Azure/actions-workflow-samples/blob/master/assets/create-secrets-for-GitHub-workflows.md

### TODO: 
- create a config file for kubernetes to set context to ghrunner cluster
- configure the ci.yml to run helm and update the runners


### Github Self Hosted Runner Docs
- https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners
- security hardening - https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#hardening-for-self-hosted-runners

### How I Ran It
ATM, it only works if we try to run it locally using terraform and the make file.
Using terraform all over again is probably not necessary, but using the make file might be, to perform changes to our self hosted runners