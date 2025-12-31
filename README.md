# Bicep Infrastructure Project

This project contains Azure infrastructure as code using Bicep templates.

## Overview

This Bicep template deploys an Azure Storage Account with the following configuration:
- **Storage Type**: StorageV2
- **SKU**: Premium_LRS
- **Location**: Uses the resource group's location
- **Naming**: Auto-generates a unique name using the resource group ID

## Prerequisites

- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) installed
- An active Azure subscription
- Appropriate permissions to create resources in Azure

## Files

- `main.bicep` - Main Bicep template defining the infrastructure
- `main.json` - ARM template (compiled from Bicep)
- `.github/workflows/deploy.yml` - GitHub Actions workflow for automated deployment

## Deployment

### Automated Deployment with GitHub Actions

This repository includes a GitHub Actions workflow that automatically deploys the infrastructure on every push to the repository.

#### Setup Required Secrets

Before the workflow can run, configure the following secrets in your GitHub repository (Settings → Secrets and variables → Actions):

1. **AZURE_CREDENTIALS** - Service principal credentials in JSON format:
   ```json
   {
     "clientId": "<client-id>",
     "clientSecret": "<client-secret>",
     "subscriptionId": "<subscription-id>",
     "tenantId": "<tenant-id>"
   }
   ```

2. **AZURE_SUBSCRIPTION** - Your Azure subscription ID

3. **AZURE_RG** - Target resource group name (e.g., `Bicep`)

#### Creating a Service Principal

To create the service principal credentials:

```bash
az ad sp create-for-rbac --name "GitHub-Actions-Bicep" \
  --role contributor \
  --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
  --sdk-auth
```

Copy the JSON output and add it as the `AZURE_CREDENTIALS` secret.

#### Workflow Trigger

The workflow automatically triggers on:
- Every push to the repository
- Manual trigger from the Actions tab

### Manual Deployment using Azure CLI

1. Log in to Azure:
```bash
az login
```

2. Create a resource group (if it doesn't exist):
```bash
az group create --name Bicep --location eastus
```

3. Deploy the template:
```bash
az deployment group create --resource-group Bicep --template-file main.bicep
```

### Custom Parameters

You can override the default parameters during deployment:

```bash
az deployment group create \
  --resource-group Bicep \
  --template-file main.bicep \
  --parameters storagePrefix=mystg location=westus
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `storagePrefix` | string | `stg` | Prefix for the storage account name |
| `storageName` | string | `{prefix}{uniqueString}` | Full name of the storage account |
| `location` | string | Resource group location | Azure region for deployment |

## Resources Deployed

- **Microsoft.Storage/storageAccounts** - Premium LRS Storage Account

## Validation

To validate the template without deploying:

```bash
az deployment group validate \
  --resource-group Bicep \
  --template-file main.bicep
```

## What-If Analysis

To preview changes before deployment:

```bash
az deployment group what-if \
  --resource-group Bicep \
  --template-file main.bicep
```

## Clean Up

To delete the deployed resources:

```bash
az group delete --name Bicep --yes
```

## Learn More

- [Azure Bicep Documentation](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/)
- [Azure Storage Documentation](https://docs.microsoft.com/en-us/azure/storage/)
- [GitHub Actions for Azure](https://github.com/Azure/actions)
