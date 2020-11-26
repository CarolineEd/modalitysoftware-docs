# Hosting TWA Automation in Customer Azure Tenant (CAT) Configuration

This page describes the architecture required for hosting TWA Automation in CAT Configuration. In this configuration, the Modality-managed cloud solution that brokers and delivers messages to users is replicated within a customer's own Azure tenant.

> **Important**: because this configuration results in all parts of the TWA Automation service being hosted within a customer's Azure tenant, the customer assumes responsibility for monitoring of the service and raising any issues with Modality for investigation. It may be necessary to export log files or temporarily provide access to installed components for troubleshooting.

## Required Components

The following Azure resources are required to install TWA Automation:

- 1 x Bot Registration S1 Standard
- 1 x App Service Plan S1
- 1 x App Service
- 1 x Application Insights
- 1 x Storage Account General Purpose v2 Hot

## Required Configuration Settings

As part of the setup process, the following configurations will be applied. These are required for the TWA Automation service to operate. The ARM Template deployment will configure these settings automatically; this list is provided for reference:

- The App Service should be publicly accessible via HTTPS 443, and also reachable by the TWA Bot Notification Service (usually installed alongside the Teamwork Analytics Windows Service on the same Virtual Machine). This is to ensure it is accessible to the Microsoft Bot Framework service, which is used to deliver both email and Microsoft Teams messages.

- The Application Registration created as part of the Bot Registration process should be set to Multi Tenant. Single Tenant registration will not work correctly for Bot Registrations.

- A service account will be needed in order to send emails from the Bot Registration service. This account should be enabled for sending emails, and should not be enabled for multi-factor authentication to enable the Bot Registration service to authenticate and send email.

![TWA Automation CAT Architecture](https://raw.githubusercontent.com/modalitysystems/modalitysoftware-docs/master/twa/images/TWA-Automation-CAT-Architecture-1.png)

# Installation Instructions

> **Update Q4 2020**: we are planning to make some architectural changes in the next version of TWA Automation. This will result in some additional resources being required in Azure once the next version is available. This is to enable greater throughput of messages when deployed at scale. This release is expected by the end of CY2020, and the new required resources will be updated here. The ARM template will also be updated.

[Azure ARM Template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/overview) is our preferred installation and configuration method. The instructions below are for an ARM Template deployment.

## Step 1 - Create Azure AD Application

TWA Automation requires a new Azure AD Application registration. Once created, you will provide the App ID and Secret to the ARM Template, which will perform the remainder of the configuration.

### Register Teamwork Analytics with Azure Active Directory
1. Sign in to the [Azure Portal](https://azure.microsoft.com/en-gb/features/azure-portal/)
1. Select the **Azure Active Directory** service from the navigation pane on the left 
1. Select **App Registrations** and click **New Registration** (Take care not to select **App Registrations (Legacy)** as these instructions do not apply to them)
1. Enter the following application registration details
   * **Name** - must be **Modality TWA Automation** (this name is important for the application to work correctly)
   * **Supported Account Types** - Recommend setting this to **Accounts in this Organisational Directory Only** [Why?](https://techcommunity.microsoft.com/t5/iis-support-blog/how-to-create-an-app-registration-in-azure-ad-for-a-bot-solution/ba-p/810761)
   * **Redirect URI** - not required   
1. Click **Register** and Azure AD will create an Application ID and present the Overview page

1. Take note of the **ApplicationID** as this will be required futher in the deployment process
![overview of application](images/applicationOverview.png)

### Add a Client Secret
1. Navigate to the **Azure Active Directory** from the navigation pane on the left
1. Select **App Registrations** and select the **Modality Teamwork Analytics** app 
1. Select **Certificates & secrets** from the Manage menu
1. Select **New client secret** and enter an appropriate description and expiry period
   * **Description** - Recommend setting this to **TWA-Secret**
   * **Expiry** - Recommend setting this to **Never**
1. Click **Add**
1. Immediately take note of the **Secret** as this can not be retrieved later and will be required further in the deployment process
![team work secret](images/teamworkSecrets.png)

## Step 2 - Deploy TWA Automation

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Ftwadeploy.blob.core.windows.net%2Ftwa-dev%2FmainTemplateBot.json" target="_blank">
  <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true" alt="Deploy To Azure" style="max-width:100%;"/>
</a>

This ARM Template will install resources in your Azure tenant. The template will deploy the following resources:

- 1 x Bot Registration S1 Standard
- 1 x App Service Plan S1
- 1 x App Service
- 1 x Application Insights
- 1 x Storage Account General Purpose v2 Hot


> Note: You must have registered an application beforehand. Provide the App ID and App Secret from Step 1.

Following deployment, TWA Automation will require some configuration before being ready to use. The Modality Systems CI Team will work with you to understand your requirements and configure the appropriate scenarios and other settings prior to first use. 

> Note: For an introduction to Azure Resource Manager see [docs.microsoft.com](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).