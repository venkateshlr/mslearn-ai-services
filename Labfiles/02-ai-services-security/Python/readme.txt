This folder contains Python code

curl -X POST "https://laguduva-l1-azure-instance.cognitiveservices.azure.com/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: 279b46ad530d403ca1b4270c7b8c0f3e" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"

az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1

279b46ad530d403ca1b4270c7b8c0f3e / 830a8024c7e6404b9785c5ed1461fe41
37d9ae422af24805acc5e7b795f3e1a9

	We can get the azure keys by this command 
	az cognitiveservices account keys list --name <resourcename> --resource-group <resourceGroup>
	 az cognitiveservices account keys list --name laguduva-l1-azure-instance --resource-group AI-102
	
	az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
	az cognitiveservices account keys regenerate --name laguduva-l1-azure-instance --resource-group AI-102 --key-name Key1
	
	Creating Key vault and selecting the user to access this key vault. Created a Secret ( Azure instance key value )
	Inorder to access the Key value, Need service principal

    az account show --> will show enter details of your account --> will have subscription details
    {
    "environmentName": "AzureCloud",
    "homeTenantId": "274102ec-cd24-4af2-a4c2-832941ce526f",
    "id": "006fe776-5572-4d00-a190-78275423a70f",
    "isDefault": true,
    "managedByTenants": [],
    "name": "CS-SUB-0468",
    "state": "Enabled",
    "tenantId": "274102ec-cd24-4af2-a4c2-832941ce526f",
        "user": {
            "cloudShellID": true,
            "name": "studentNJN1W@t000.gdazcs.com",
            "type": "user"
        }
    }
az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>	
az ad sp create-for-rbac -n "https://laguduva-l1-azure-instance.cognitiveservices.azure.com://CS-SUB-0468" --role owner --scopes subscriptions/006fe776-5572-4d00-a190-78275423a70f/resourceGroups/AI-102

Above command will create service prinipal account to the subscription ID under the subscription name under the resource group

{
  "appId": "b473afe3-d0a2-4988-a0b4-2dc25367be7f",
  "displayName": "https://laguduva-l1-azure-instance.cognitiveservices.azure.com://CS-SUB-0468",
  "password": "et-8Q~C4w2e3npXPwU0Wdp2aUvqYXq6so1VsxdBC",
  "tenant": "274102ec-cd24-4af2-a4c2-832941ce526f"
}

az ad sp show --id <appId> --> Will full details of service principles account. Note the id of it.
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#servicePrincipals/$entity",
  "accountEnabled": true,
  "addIns": [],
  "alternativeNames": [],
  "appDescription": null,
  "appDisplayName": "https://laguduva-l1-azure-instance.cognitiveservices.azure.com://CS-SUB-0468",
  "appId": "b473afe3-d0a2-4988-a0b4-2dc25367be7f",
  "appOwnerOrganizationId": "274102ec-cd24-4af2-a4c2-832941ce526f",
  "appRoleAssignmentRequired": false,
  "appRoles": [],
  "applicationTemplateId": null,
  "createdDateTime": "2024-05-13T11:04:53Z",
  "deletedDateTime": null,
  "description": null,
  "disabledByMicrosoftStatus": null,
  "displayName": "https://laguduva-l1-azure-instance.cognitiveservices.azure.com://CS-SUB-0468",
  "homepage": null,
  "id": "6c1ddb85-07c0-47cf-8f2a-6f982f67e46f",
  "info": {
    "logoUrl": null,
    "marketingUrl": null,
    "privacyStatementUrl": null,
    "supportUrl": null,
    "termsOfServiceUrl": null
  },
  "keyCredentials": [],
  "loginUrl": null,
  "logoutUrl": null,
  "notes": null,
  "notificationEmailAddresses": [],
  "oauth2PermissionScopes": [],
  "passwordCredentials": [],
  "preferredSingleSignOnMode": null,
  "preferredTokenSigningKeyThumbprint": null,
  "replyUrls": [],
  "resourceSpecificApplicationPermissions": [],
  "samlSingleSignOnSettings": null,
  "servicePrincipalNames": [
    "b473afe3-d0a2-4988-a0b4-2dc25367be7f"
  ],
  "servicePrincipalType": "Application",
  "signInAudience": "AzureADMyOrg",
  "tags": [],
  "tokenEncryptionKeyId": null,
  "verifiedPublisher": {
    "addedDateTime": null,
    "displayName": null,
    "verifiedPublisherId": null
  }
}

az keyvault set-policy -n <keyVaultName> --object-id <sp-objectId> --secret-permissions get list
az keyvault set-policy -n laguduva-l1-key-vault-v2 --object-id 6c1ddb85-07c0-47cf-8f2a-6f982f67e46f --secret-permissions get list

{
  "id": "/subscriptions/006fe776-5572-4d00-a190-78275423a70f/resourceGroups/AI-102/providers/Microsoft.KeyVault/vaults/laguduva-l1-key-vault-v2",
  "location": "eastus",
  "name": "laguduva-l1-key-vault-v2",
  "properties": {
    "accessPolicies": [
      {
        "applicationId": null,
        "objectId": "0580abc0-d751-44b5-bb5f-e7e01b8e87e4",
        "permissions": {
          "certificates": [
            "Get",
            "List",
            "Update",
            "Create",
            "Import",
            "Delete",
            "Recover",
            "Backup",
            "Restore",
            "ManageContacts",
            "ManageIssuers",
            "GetIssuers",
            "ListIssuers",
            "SetIssuers",
            "DeleteIssuers"
          ],
          "keys": [
            "Get",
            "List",
            "Update",
            "Create",
            "Import",
            "Delete",
            "Recover",
            "Backup",
            "Restore",
            "GetRotationPolicy",
            "SetRotationPolicy",
            "Rotate"
          ],
          "secrets": [
            "Get",
            "List",
            "Set",
            "Delete",
            "Recover",
            "Backup",
            "Restore"
          ],
          "storage": null
        },
        "tenantId": "274102ec-cd24-4af2-a4c2-832941ce526f"
      },
      {
        "applicationId": null,
        "objectId": "6c1ddb85-07c0-47cf-8f2a-6f982f67e46f",
        "permissions": {
          "certificates": null,
          "keys": null,
          "secrets": [
            "list",
            "get"
          ],
          "storage": null
        },
        "tenantId": "274102ec-cd24-4af2-a4c2-832941ce526f"
      }
    ],
    "createMode": null,
    "enablePurgeProtection": null,
    "enableRbacAuthorization": false,
    "enableSoftDelete": true,
    "enabledForDeployment": false,
    "enabledForDiskEncryption": false,
    "enabledForTemplateDeployment": false,
    "hsmPoolResourceId": null,
    "networkAcls": null,
    "privateEndpointConnections": null,
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Enabled",
    "sku": {
      "family": "A",
      "name": "Standard"
    },
    "softDeleteRetentionInDays": 90,
    "tenantId": "274102ec-cd24-4af2-a4c2-832941ce526f",
    "vaultUri": "https://laguduva-l1-key-vault-v2.vault.azure.net/"
  },
  "resourceGroup": "AI-102",
  "systemData": {
    "createdAt": "2024-05-13T10:56:06.207000+00:00",
    "createdBy": "studentNJN1W@t000.gdazcs.com",
    "createdByType": "User",
    "lastModifiedAt": "2024-05-13T11:15:12.337000+00:00",
    "lastModifiedBy": "studentNJN1W@t000.gdazcs.com",
    "lastModifiedByType": "User"
  },
  "tags": {},
  "type": "Microsoft.KeyVault/vaults"
}

pip install --trusted-host pypi.org --trusted-host pypi.python.org --trusted-host files.pythonhosted.org azure-keyvault-secrets==4

