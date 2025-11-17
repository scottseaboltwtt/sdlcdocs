# Infrastructure as a Service Specification

## Executive Summary
This document outlines the Infrastructure as a Service (IaaS) specification for the Chemistry Test Tool, an educational application hosted on Microsoft Azure. Utilizing Pulumi for infrastructure as code, this specification details the necessary resources, configurations, and best practices to ensure a reliable, secure, and efficient deployment.

## Overview
The Chemistry Test Tool IaaS setup provisions a variety of Azure resources, including a resource group, virtual network, application service, monitoring tools, and a secure database. This infrastructure supports the simulation of educational scenarios such as temperature control, flow monitoring, and safety notifications, with an emphasis on operational excellence through monitoring and logging.

## Resources
The following key resources will be provisioned:

- **Resource Group**: A logical container for managing Azure resources.
- **Virtual Network (VNet) and Subnet**: For network segmentation and security.
- **Network Security Group (NSG)**: To control inbound and outbound traffic.
- **Azure App Service Plan**: For hosting the front-end application.
- **Azure Application Insights**: For monitoring application performance.
- **Azure Key Vault**: For managing sensitive information.
- **Azure SQL Server and Database**: For data storage and management.

## Code Structure
The implementation consists of the following files:

### 1. `main.ts`
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
        // Deny all other inbound traffic by default
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
        // Enable always on to improve reliability
        alwaysOn: true,
        // Enable application logging
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
    // Retain telemetry for 30 days
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

/*
Well-Architected Framework Alignment:
- Reliability: Multi-AZ availability in region via Azure managed App Service and SQL Database
- Security: Key Vault for secrets, NSG for network security, HTTPS only for the web app
- Operational Excellence: Application Insights for monitoring, logging enabled on App Service
- Performance Efficiency: Standard SKU App Service Plan and SQL to handle expected load
- Cost Optimization: Use standard tier resources matching educational tool usage
- Sustainability: Use PaaS services to minimize overhead and optimize resource utilization
*/
```

### 2. `Pulumi.yaml`
```yaml
name: chemistryTestTool
runtime: nodejs

config:
  azure:location: East US
  sqlAdminUser: chemadmin
  sqlAdminPassword: "REPLACE_WITH_SECURE_PASSWORD"
```

### 3. `README.md`
# Chemistry Test Tool Infrastructure

This Pulumi program provisions Azure resources for the Chemistry Test Tool educational application.

## Resources
- Resource Group
- Virtual Network and Subnet with Network Security Group
- Azure App Service Plan and Web App (front-end)
- Azure Application Insights for monitoring
- Azure Key Vault for secrets management
- Azure SQL Server and Database

## Instructions
1. Update `Pulumi.yaml` with your SQL admin password.
2. Run `pulumi up` to provision the infrastructure.
3. Deploy application code to the Azure App Service.

This infrastructure supports the simulation of scenarios defined in the project, such as temperature control, flow monitoring, and safety notifications, with monitoring and logging enabled for operational excellence.

## Technical Specifications

### Dependencies
- **Subnet** depends on **VNet**.
- **SubnetNsgAssociation** depends on **Subnet** and **NSG**.
- **App** depends on **AppServicePlan** and **AppInsights**.
- **SQLDatabase** depends on **SQLServer**.
- **AppSettings** in **App** depends on **AppInsights** instrumentation key.

### Variables
- **location**: Default is East US.
- **sqlAdminUser**
- **sqlAdminPassword**: Secret.

### Outputs
- **resourceGroupOutput**: Name of Resource Group.
- **appUrl**: App Service hostname URL.
- **keyVaultUri**: URI of Key Vault.
- **sqlServerName**: SQL Server name.
- **sqlDbName**: SQL Database name.

## Well-Architected Framework Alignment
- **Reliability**: Hosted Azure App Service and Azure SQL Database offer regional high availability, with always-on enabled and failover built-in. Azure Load Balancer is implicitly used to distribute traffic. Application Insights enables health monitoring.
- **Security**: Network Security Groups restrict inbound traffic, HTTPS only enabled for app, Azure Key Vault for secret and credential management, Azure AD