# Infrastructure as a Service Specification

## Executive Summary
This document outlines the Infrastructure as a Service (IaaS) specification for the Chemistry Educational Test Tool deployed on Microsoft Azure using Pulumi as the Infrastructure as Code (IaC) tool. It details the architectural components, their configurations, and the alignment with the Azure Well-Architected Framework. The primary goal is to ensure a secure, reliable, and efficient deployment that adheres to best practices.

## Overview
- **Type:** IaaS
- **Cloud Platform:** Microsoft Azure
- **IaC Tool:** Pulumi

## Resources and Configuration Files
The following resources and configurations are defined for the Chemistry Educational Test Tool.

### Resource Files
1. **main.ts**
   ```typescript
   // Pulumi Infrastructure as Code for Chemistry Educational Test Tool
   // Implements Azure resources following Azure Well-Architected Framework best practices
   // Includes resource groups, compute, storage, monitoring, and security configurations

   import * as azure from "@pulumi/azure";
   import * as pulumi from "@pulumi/pulumi";

   // Configuration variables
   const config = new pulumi.Config();
   const location = config.get("location") || "East US";

   // Resource Group
   const resourceGroup = new azure.core.ResourceGroup("chemistry-test-tool-rg", {
       location: location,
   });

   // Storage Account for application data and logs
   const storageAccount = new azure.storage.Account("cttstorage", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       accountTier: "Standard",
       accountReplicationType: "LRS",
       enableHttpsTrafficOnly: true, // Security: enforce HTTPS
       minTlsVersion: "TLS1_2",
       allowBlobPublicAccess: false, // Security: no public blob access
   });

   // Key Vault for secrets management
   const keyVault = new azure.keyvault.KeyVault("cttKeyVault", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       tenantId: azure.core.getClientConfig().then(c => c.tenantId),
       skuName: "standard",
       softDeleteRetentionDays: 90,
       purgeProtectionEnabled: true,
       accessPolicies: [{
           tenantId: azure.core.getClientConfig().then(c => c.tenantId),
           objectId: azure.core.getClientConfig().then(c => c.objectId),
           secretPermissions: ["get", "set", "list", "delete"],
           certificatePermissions: [],
           keyPermissions: [],
       }],
   });

   // App Service Plan for hosting the web app
   const appServicePlan = new azure.appservice.Plan("cttAppServicePlan", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       kind: "App",
       reserved: true, // Linux hosting
       sku: {
           tier: "PremiumV2",
           size: "P1v2",
       },
   });

   // App Service (Web App)
   const appService = new azure.appservice.AppService("chemistryTestToolWebapp", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       appServicePlanId: appServicePlan.id,
       httpsOnly: true, // Security: enforce https
       siteConfig: {
           linuxFxVersion: "NODE|16-lts", // Example runtime for Node.js
           alwaysOn: true, // Reliability: keeps app responsive
       },
       appSettings: {
           "WEBSITE_RUN_FROM_PACKAGE": "1",
           "AzureWebJobsStorage": storageAccount.primaryConnectionString,
           "KEY_VAULT_URI": keyVault.vaultUri,
       },
   });

   // Application Insights for monitoring
   const appInsights = new azure.appinsights.Insights("cttAppInsights", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       applicationType: "web",
   });

   // Link App Insights with App Service
   const appInsightsKey = appInsights.instrumentationKey;

   // Diagnostic Settings for App Service Logs to App Insights
   const diagnosticSetting = new azure.monitor.DiagnosticSetting("cttAppSvcDiag", {
       targetResourceId: appService.id,
       logs: [{
           category: "AppServiceHTTPLogs",
           enabled: true,
           retentionPolicy: { enabled: false, days: 0 },
       }, {
           category: "AppServiceConsoleLogs",
           enabled: true,
           retentionPolicy: { enabled: false, days: 0 },
       }],
       metrics: [{
           category: "AllMetrics",
           enabled: true,
           retentionPolicy: { enabled: false, days: 0 },
       }],
   });

   // Network Security Group
   const nsg = new azure.network.NetworkSecurityGroup("cttNSG", {
       resourceGroupName: resourceGroup.name,
       location: resourceGroup.location,
       securityRules: [{
           name: "Allow-HTTPS",
           priority: 100,
           direction: "Inbound",
           access: "Allow",
           protocol: "Tcp",
           sourcePortRange: "*",
           destinationPortRange: "443",
           sourceAddressPrefix: "*",
           destinationAddressPrefix: "*",
       }],
   });

   // Outputs
   export const resourceGroupName = resourceGroup.name;
   export const appServiceEndpoint = appService.defaultSiteHostname.apply(hostname => `https://${hostname}`);
   export const storageAccountName = storageAccount.name;
   export const keyVaultUri = keyVault.vaultUri;
   export const appInsightsInstrumentationKey = appInsights.instrumentationKey;
   
2. **Pulumi.yaml**
   ```yaml
   name: chemistry-test-tool
   runtime: nodejs
   description: Pulumi infrastructure for Chemistry Educational Test Tool
   ```

3. **package.json**
   ```json
   {
     "name": "chemistry-test-tool",
     "version": "1.0.0",
     "description": "Pulumi infrastructure as code for Chemistry Educational Test Tool",
     "main": "main.js",
     "scripts": {
       "build": "tsc",
       "start": "pulumi up",
       "preview": "pulumi preview",
       "destroy": "pulumi destroy"
     },
     "dependencies": {
       "@pulumi/azure": "^4.0.0",
       "@pulumi/pulumi": "^3.0.0"
     },
     "devDependencies": {
       "typescript": "^4.3.5"
     }
   }
   ```

4. **tsconfig.json**
   ```json
   {
     "compilerOptions": {
       "target": "es6",
       "module": "commonjs",
       "outDir": "./dist",
       "strict": true,
       "esModuleInterop": true,
       "skipLibCheck": true
     },
     "include": ["main.ts"]
   }
   ```

### Summary of Resources
- **Resource Group:** `azure.core.ResourceGroup (chemistry-test-tool-rg)`
- **Storage Account:** `azure.storage.Account (cttstorage)`
- **Key Vault:** `azure.keyvault.KeyVault (cttKeyVault)`
- **App Service Plan:** `azure.appservice.Plan (cttAppServicePlan)`
- **App Service:** `azure.appservice.AppService (chemistryTestToolWebapp)`
- **Application Insights:** `azure.appinsights.Insights (cttAppInsights)`
- **Diagnostic Settings:** `azure.monitor.DiagnosticSetting (cttAppSvcDiag)`
- **Network Security Group:** `azure.network.NetworkSecurityGroup (cttNSG)`

## Dependencies
- **Storage Account (cttstorage)** depends on:
  - Resource Group (chemistry-test-tool-rg)

- **Key Vault (cttKeyVault)** depends on:
  - Resource Group (chemistry-test-tool-rg)

- **App Service Plan (cttAppServicePlan)** depends on:
  - Resource Group (chemistry-test-tool-rg)

- **App Service (chemistryTestToolWebapp)** depends on:
  - App Service Plan (cttAppServicePlan)
  - Storage Account (cttstorage)
  - Key Vault (cttKeyVault)

- **Application Insights (cttAppInsights)** depends on:
  - Resource Group (chemistry-test-tool-rg)

- **Diagnostic Setting (cttAppSvcDiag)** depends on:
  - App Service (chemistryTestToolWebapp)
  - Application Insights (cttAppInsights)

- **Network Security Group (cttNSG)** depends on:
  - Resource Group (chemistry-test-tool-rg)

## Configuration Variables
| Name     | Description                                     | Default Value |
|----------|-------------------------------------------------|---------------|
| location | Azure region location for all resources         | East US       |

## Outputs
| Name                       | Description                                                    |
|----------------------------|----------------------------------------------------------------|
| resourceGroupName          | Name of the Azure resource group                               |
| appServiceEndpoint          | URL endpoint of the hosted chemistry test tool web app        |
| storageAccountName         | Name of the Azure Storage Account used for application storage |
| keyVaultUri                | URI of the Azure Key Vault for secrets management              |
| appInsightsInstrumentationKey | Instrumentation key of Azure Application Insights for monitoring |

## Well-Architected Alignment
### Operational Excellence
- Implementation of Pulumi infrastructure as code for repeatability and version control.
- Integration of Application Insights for monitoring and diagnostics.
- Use of diagnostic settings to forward application logs to monitoring.
- Logical grouping of resources through Azure Resource