# GitHub Actions Deployment Setup

This guide explains how to configure GitHub Actions to automatically deploy your ASP.NET Core application to Azure App Service.

## Prerequisites

1. ✅ Azure App Service already created (via `azd up`)
2. ✅ GitHub repository with your code
3. 🔧 Azure Service Principal or Managed Identity (we'll create this)

## Step 1: Create Azure Service Principal

Run these commands in Azure Cloud Shell or Azure CLI:

```bash
# Get your subscription ID
az account show --query id --output tsv

# Create service principal (replace YOUR_SUBSCRIPTION_ID and YOUR_APP_NAME)
az ad sp create-for-rbac --name "github-actions-sp" \
  --role contributor \
  --scopes /subscriptions/YOUR_SUBSCRIPTION_ID/resourceGroups/rg-new_500238003 \
  --sdk-auth
```

Save the JSON output - you'll need it for GitHub secrets.

## Step 2: Get App Service Details

```bash
# Get your App Service name
az webapp list --query "[].{name:name, resourceGroup:resourceGroup}" --output table

# Note your:
# - App Service name (e.g., app-web-2ko5cnpd2fv6k)
# - Resource Group name (e.g., rg-new_500238003)
# - Subscription ID
# - Tenant ID
```

## Step 3: Configure GitHub Repository Secrets

Go to your GitHub repository → Settings → Secrets and variables → Actions

### Add these Repository Secrets:

| Secret Name | Value | Example |
|-------------|-------|---------|
| `AZUREAPPSERVICE_CLIENTID` | Client ID from service principal | `12345678-1234-1234-1234-123456789012` |
| `AZUREAPPSERVICE_TENANTID` | Tenant ID from Azure | `87654321-4321-4321-4321-210987654321` |
| `AZUREAPPSERVICE_SUBSCRIPTIONID` | Your Azure subscription ID | `11111111-2222-3333-4444-555555555555` |
| `AZUREAPPSERVICE_APPNAME` | Your App Service name | `app-web-2ko5cnpd2fv6k` |

## Step 4: Test the Workflow

1. Make a change to your code (e.g., update Index.cshtml)
2. Commit and push to main/master branch:
   ```bash
   git add .
   git commit -m "Test GitHub Actions deployment"
   git push origin master
   ```
3. Go to GitHub → Actions tab to see the workflow running

## Workflow Features

✅ **Automatic Triggers**: Runs on every push to main/master  
✅ **Manual Trigger**: Can be triggered manually via GitHub UI  
✅ **Build & Deploy**: Compiles .NET 8 app and deploys to App Service  
✅ **Security**: Uses Azure federated credentials (OIDC)  
✅ **Artifacts**: Saves build output between jobs  

## Troubleshooting

### Common Issues:

1. **"App Service not found"**
   - Check `AZUREAPPSERVICE_APPNAME` matches exactly
   - Verify the service principal has access to the resource group

2. **"Authentication failed"**
   - Verify all secrets are set correctly
   - Check service principal permissions

3. **"Build failed"**
   - Check .NET version in workflow matches your project
   - Verify project file path is correct

### Getting Help:

- Check GitHub Actions logs for detailed error messages
- Verify Azure resources exist in the portal
- Test Azure CLI commands locally first

## Student Information

**Name**: Nipuna Pamuditha Karunarathna  
**Student ID**: 500238003  
**Project**: ASP.NET Core Deployment with GitHub Actions 