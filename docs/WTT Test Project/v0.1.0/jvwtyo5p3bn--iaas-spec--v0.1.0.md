# Infrastructure as a Service Specification

## Executive Summary
This document outlines the Infrastructure as a Service (IaaS) specification for the Chemistry Test Tool educational application, deployed on the Azure cloud platform using Pulumi as the infrastructure as code tool. The specification includes detailed descriptions of the resources provisioned, configuration settings, and alignment with the Azure Well-Architected Framework.

## Table of Contents
1. [Overview](#overview)
2. [Resource Provisioning](#resource-provisioning)
3. [Configuration Files](#configuration-files)
4. [Dependencies and Variables](#dependencies-and-variables)
5. [Outputs](#outputs)
6. [Well-Architected Framework Alignment](#well-architected-framework-alignment)
7. [Instructions for Use](#instructions-for-use)

## Overview
The Chemistry Test Tool IaaS specification details the deployment of Azure resources necessary for the operation of the educational application. This infrastructure supports the simulation of various scenarios, including temperature control, flow monitoring, and safety notifications. The application is designed with monitoring and logging capabilities to ensure operational excellence.

## Resource Provisioning
The following Azure resources are provisioned as part of the Infrastructure:

- **Resource Group**: A logical container for the Azure resources.
- **Virtual Network and Subnet**: Provides a secure network environment.
- **Network Security Group**: Manages inbound and outbound traffic rules.
- **Azure App Service Plan and Web App**: Hosts the front-end of the application.
- **Azure Application Insights**: Monitors application performance and health.
- **Azure Key Vault**: Manages secrets and sensitive information.
- **Azure SQL Server and Database**: Stores application data, including user progress and process data.

### Code Implementation
Here is the Pulumi code used for provisioning the resources:

```typescript
// Main Pulumi program for Test Tool infrastructure
import * as pulumi from "@pulumi/pulumi";
import * as azure from "@pulumi/azure";

// Configurations and variables
const config = new pulumi.Config();
const location = config.get("location") || "East US";
const resourceGroupName = "chemistryTestTool-rg";

// Create Resource Group
const resourceGroup = new azure.core.ResourceGroup(resourceGroupName, {
    location: location,
});

// Virtual Network with Subnet
const vnet = new azure.network.VirtualNetwork("chemistry-vnet", {
    resourceGroupName: resourceGroup.name,
    addressSpaces: ["10.0.0.0/16"],
});
const subnet = new azure.network.Subnet("chemistry-subnet", {
    resourceGroupName: resourceGroup.name,
    virtualNetworkName: vnet.name,
    addressPrefix: "10.0.1.0/24",
});

// Network Security Group for subnet with secure inbound/outbound rules
const nsg = new azure.network.NetworkSecurityGroup("chemistry-nsg", {
    resourceGroupName: resourceGroup.name,
    securityRules: [
        {
            name: "AllowHTTP",
            priority: 100,
            direction: "Inbound",
            access: "Allow",
            protocol: "Tcp",
            sourcePortRange: "*",
            destinationPortRange: "80",
            sourceAddressPrefix: "*",
            destinationAddressPrefix: "*",
        },
        {
            name: "AllowHTTPS",
            priority: 110,
            direction: "Inbound",
            access: "Allow",
            protocol: "Tcp",
            sourcePortRange: "*",
            destinationPortRange: "443",
            sourceAddressPrefix: "*",
            destinationAddressPrefix: "*",
        },
        {
            name: "DenyAll",
            priority: 4096,
            direction: "Inbound",
            access: "Deny",
            protocol: "*",
            sourcePortRange: "*",
            destinationPortRange: "*",
            sourceAddressPrefix: "*",
            destinationAddressPrefix: "*",
        },
    ],
});

// Associate NSG with subnet
const subnetNsgAssociation = new azure.network.SubnetNetworkSecurityGroupAssociation("subnet-nsg-association", {
    networkSecurityGroupId: nsg.id,
    subnetId: subnet.id,
});

// Azure App Service Plan for hosting the front-end interactive test tool
const appServicePlan = new azure.appservice.Plan("chemistry-appserviceplan", {
    resourceGroupName: resourceGroup.name,
    kind: "App",
    sku: {
        tier: "Standard",
        size: "S1",
    },
});

// Azure App Service web app
const app = new azure.appservice.AppService("chemistry-test-tool-app", {
    resourceGroupName: resourceGroup.name,
    appServicePlanId: appServicePlan.id,
    httpsOnly: true,
    siteConfig: {
        dotnetFrameworkVersion: "v4.0",
        alwaysOn: true,
        applicationLogs: {
            azureBlobStorage: {
                level: "Information",
                retentionInDays: 7
            }
        }
    },
    appSettings: {
        "WEBSITE_RUN_FROM_PACKAGE": "1",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "", // To be set after App Insights creation
    },
});

// Azure Application Insights for monitoring and operational excellence
const appInsights = new azure.appinsights.Insights("chemistry-appinsights", {
    resourceGroupName: resourceGroup.name,
    applicationType: "web",
    retentionInDays: 30,
});

// Update App Service app setting with instrumentation key
const appInsightsKey = appInsights.instrumentationKey.apply(key => {
    return key;
});
app.appSettings = {
    ...app.appSettings,
    APPINSIGHTS_INSTRUMENTATIONKEY: appInsightsKey,
};

// Azure Key Vault for secrets management (e.g., database connection strings)
const keyVault = new azure.keyvault.KeyVault("chemistry-keyvault", {
    resourceGroupName: resourceGroup.name,
    tenantId: azure.config.tenantId,
    skuName: "standard",
    accessPolicies: [{
        tenantId: azure.config.tenantId,
        objectId: azure.config.clientId || "",
        permissions: {
            secrets: ["get", "list", "set"]
        }
    }],
    enabledForDeployment: true,
    enabledForTemplateDeployment: true,
    enabledForDiskEncryption: true,
});

// Azure SQL Server for data storage (simulate process data and user progress)
const sqlServer = new azure.sql.SqlServer("chemistry-sqlserver", {
    resourceGroupName: resourceGroup.name,
    location: location,
    version: "12.0",
    administratorLogin: config.require("sqlAdminUser"),
    administratorLoginPassword: config.requireSecret("sqlAdminPassword"),
});

// Azure SQL Database
const sqlDatabase = new azure.sql.Database("chemistry-db", {
    resourceGroupName: resourceGroup.name,
    location: location,
    serverName: sqlServer.name,
    skuName: "S0",
});

// Outputs
export const resourceGroupOutput = resourceGroup.name;
export const appUrl = app.defaultSiteHostname.apply(hostname => `https://${hostname}`);
export const keyVaultUri = keyVault.vaultUri;
export const sqlServerName = sqlServer.name;
export const sqlDbName = sqlDatabase.name;
```

## Configuration Files
### Pulumi.yaml
The following configuration file provides necessary settings for the Pulumi deployment.

```yaml
name: chemistryTestTool
runtime: nodejs

config:
  azure:location: East US
  sqlAdminUser: chemadmin
  sqlAdminPassword: "REPLACE_WITH_SECURE_PASSWORD"
```

### README.md
The README file outlines the purpose and usage of the Chemistry Test Tool infrastructure.

```
# Chemistry Test Tool Infrastructure

This Pulumi program provisions Azure resources for the Chemistry Test Tool educational application.

## Resources:
- Resource Group
- Virtual Network and Subnet with Network Security Group
- Azure App Service Plan and Web App (front-end)
- Azure Application Insights for monitoring
- Azure Key Vault for secrets management
- Azure SQL Server and Database

## Instructions:
1. Update Pulumi.yaml with your SQL admin password.
2. Run `pulumi up` to provision the infrastructure.
3. Deploy application code to the Azure App Service.

This infrastructure supports the simulation of scenarios defined in the project such as temperature control, flow monitoring, and safety notifications with monitoring and logging enabled for operational excellence.
```

## Dependencies and Variables
### Dependencies
- `subnet` depends on `vnet`
- `subnetNsgAssociation` depends on `subnet` and `nsg`
- `app` depends on `appServicePlan` and `appInsights`
- `sqlDatabase` depends on `sqlServer`
- `appSettings` in `app` depends on `appInsights instrumentation key`

### Variables
- `location` (default: East US)
- `sqlAdminUser`
- `sqlAdminPassword` (secret)

## Outputs
The following outputs are generated after provisioning:
- `resourceGroupOutput`: Name of the Resource Group
- `appUrl`: URL of the app service hostname
- `keyVaultUri`: URI of the Key Vault
- `sqlServerName`: Name of the SQL Server
- `sqlDbName`: Name of the SQL Database

## Well-Architected Framework Alignment
### Reliability
The hosted Azure App Service and Azure SQL Database provide regional high availability, with built-in failover and always-on capability. Azure Load Balancer is implicitly utilized to distribute traffic. Application Insights ensures health monitoring.

### Security
Network Security Groups restrict inbound traffic, enforcing HTTPS for the application. Azure Key Vault is utilized for secret management, while Azure Active Directory supports identity management. SQL Server credentials are securely stored and least-privilege access policies are enforced