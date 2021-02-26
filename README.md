# training-demos

## Pre-requisites

Make sure you have a resource group

```bash
az group create --name [resourcegroup] --location [location]
```

Make sure you have a service principal

```bash
az ad sp create-for-rbac --name githubactiontrainingdemos --role owner --scopes /subscriptions/[subscriptionid]/resourceGroups/[resourcegroup] --sdk-auth
```

Find your user id

```bash
az ad signed-in-user show --query objectId -o tsv
```

Create following secrets in Git Hub:

| Secret | Value |
| :------ | :-------------- |
| **ADMIN_PASSWORD** | a password that will be used for the [azureuser] (virtual machines, sql databases, synapse, etc) | 
| **AZURE_CREDENTIALS** | the output of the `az ad sp create-for-rbac command` |
| **AZURE_RG** | the name of the resource-group you want to deploy to |
| **AZURE_SUBSCRIPTION** | the id of the subscription |
| **USER_OBJECT_ID** | your user object id (result of `az ad signed-in-user show`) |
| **USER_UPN** | your UPN (example: alias@company.com) |
| **USER_ALIAS** | your alias (will be used for naming resources that should be unique) |

## Deploy manually or with GitHub Actions

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fweslbo%2Ftraining-demos%2Fmaster%2Ftemplates%2Fazuredeploy.json)

When redeploying, following issues can occur:

- DeploymentFailed: UpdateNotAllowedOnPausedDatabase. Attempted to perform an update on a paused database. Current work around: start the SQLPOOL1
- Microsoft.Compute/virtualMachines/vm-sqlserver is not in running state. Current work around: start the vm

## Additional scripts to execute

Add yourself as the System Administrator for Azure Synapse Analytics

```bash
$alias = "alias"
$upn = "alias@company.com"
$resourcegroup = "rg-training-demos"
$workspacename = "synapse-$alias"

az role assignment create --assignee $upn --role "owner" --resource-group $resourcegroup
az synapse role assignment create --workspace-name $workspacename --assignee $upn --role "System Administrator"
```

Connect the Synapse Analytics to GitHub by following [article](https://docs.microsoft.com/en-us/azure/synapse-analytics/cicd/source-control#connect-with-github).

| Setting | Value |
| :------ | :-------------- |
| **Repository type** | GitHub |
| **GitHub account** | [your account] |
| **Repository name** | training-demos |
| **Collaboration branch** | master |
| **Publish branch** | workspace_publish |
| **Root folder** | /synapse |

Connect the Data Factory to GitHub

| Setting | Value |
| :------ | :-------------- |
| **Repository type** | GitHub |
| **GitHub account** | [your account] |
| **Repository name** | training-demos |
| **Collaboration branch** | master |
| **Publish branch** | adf_publish |
| **Root folder** | /data-factory |