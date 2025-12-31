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

## Deployment

### Deploy using Azure CLI

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
  --parameters storageName=mystorageaccount location=westus
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `storageName` | string | `stg{uniqueString}` | Name of the storage account |
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
