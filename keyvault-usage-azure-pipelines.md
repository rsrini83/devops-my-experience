# Use Azure KeyVault in the DevOps Pipelines
  Azure Key Vault provides a secure solution for storing and handling sensitive data, including passwords, API keys, and certificates. With Azure Key Vault, you can effortlessly generate and oversee encryption keys to safeguard your data. Additionally, it enables centralized management of certificates for all your resources. This article will guide you through the process of utilizing Azure Key Vault to achieve these functionalities.
## Create a KeyVault in Azure 
  1. az account set --subscription <subscription_id>
  2. az config set defaults.location=<require_region>
  3. az group create --name <your-resource-group>
  4. az keyvault create --name <key-vault-name> --resource-group <resource-group>
## Create a service principal
  1. az ad sp create-for-rbac --name <service_principal_name>
     a. Output should be
     ```
       {
          "appId": "**************************",
          "displayName": "**************************",
          "password": "***********************************",
          "tenant": "<tenant_id>"
        }
     ```
## Configure Key Vault access permissions
## Create a new service connection
  1. Sign in to your Azure DevOps portal, and then navigate to your project.
  2. Select Gear icon Project settings, and then select Service connections.
  3. If you are setting up a service connection for the first time in your project, choose 'Create service connection'. If you have made service connections earlier, select 'New service connection'.
  4. Select Azure Resource Manager, and then select Next.
  5. Select Service principal (manual), and then select Next.
  6. Select Subscription for the Scope Level, and fill in the required fields with information from the previously created service principal. Select Verify when you're done:
      a. Service Principal Id: Your service principal appId.
      b. Service Principal key: Your service principal password.
      c. Tenant ID: Your service principal tenant.
  7. Provide a name for your service connection, and make sure you check the Grant access permission to all pipelines checkbox.
  8. Select Verify and save when you're done.
## Query and use secrets in your pipeline
  1. Include the following task as the initial task in the pipeline YAML file.
     ```
       - task: AzureKeyVault@1
         inputs:
            azureSubscription: '<service_connection_name>'
            KeyVaultName: '<key_vault_name>'
            SecretsFilter: '<secret_name>'
            RunAsPreJob: false    ## Make the secret(s) available to the whole job
     ```
