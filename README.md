
# n8n Azure Deployment Templates

This repository contains two ARM templates for deploying and managing n8n on Azure Container Instances (ACI) with persistent storage using Azure File Share.

## Templates

1. **Creation Template (deploy-n8n.json)**:
   - Deploys a new n8n instance on ACI.
   - Sets up Azure File Share for persistent data storage.
   - Includes basic authentication for securing the instance.

2. **Update Template (update-n8n.json)**:
   - Updates an existing n8n instance by pulling a new Docker image version.
   - Preserves the existing database and configurations stored in the Azure File Share.

## Parameters

Both templates share common parameters:

- `baseName`: A base name for resources like storage accounts, file shares, and container groups.
- `n8nUsername`: The username for basic authentication.
- `n8nPassword`: The password for basic authentication.
- `location`: The Azure region for deployment (default: `westeurope`).
- For the update template:
  - `n8nImageVersion`: The Docker image version of n8n to deploy (default: `latest`).

## Usage

### Deploy a New Instance

```bash
az deployment group create   --resource-group <RESOURCE_GROUP_NAME>   --template-file deploy-n8n.json   --parameters baseName=<BASE_NAME>               n8nUsername=<USERNAME>               n8nPassword=<PASSWORD>
```

### Update an Existing Instance

```bash
az deployment group create   --resource-group <RESOURCE_GROUP_NAME>   --template-file update-n8n.json   --parameters baseName=<BASE_NAME>               n8nUsername=<USERNAME>               n8nPassword=<PASSWORD>               n8nImageVersion=<IMAGE_VERSION>
```

## Notes

- Ensure the `baseName` matches between creation and update templates.
- Use a secure password for `n8nPassword` to protect access.
