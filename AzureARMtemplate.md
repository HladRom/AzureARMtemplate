  # **AzureARMtemplate**
  
### Automation using ARM templates
*Create an ARM template to automatically create resources on Azure resource group,service backup copy.
Service backup copy*
## **template**
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountType": {
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Premium_LRS",
                "Premium_ZRS",
                "Standard_GRS",
                "Standard_GZRS",
                "Standard_LRS",
                "Standard_RAGRS",
                "Standard_RAGZRS",
                "Standard_ZRS"
            ],
            "type": "String",
            "metadata": {
                "description": "Storage Account type"
            }
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "The storage account location."
            }
        },
        "storageAccountName": {
            "defaultValue": "[format('store{0}', uniqueString(resourceGroup().id))]",
            "type": "String",
            "metadata": {
                "description": "The name of the storage account"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2022-09-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageAccountName": {
            "type": "String",
            "value": "[parameters('storageAccountName')]"
        },
        "storageAccountId": {
            "type": "String",
            "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
        }
    }
}

## **parameters**

{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "value": "Standard_LRS"
    },
    "location": {
      "value": "eastus"
    },
    "storageAccountName": {
      "value": "storehe76djmpjydqk"
    }
  }
}
___
### **Resources on Azure resource group**
___
## **template**

{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "maxLength": 11,
            "type": "String"
        },
        "newResourceGroupName": {
            "type": "String"
        },
        "nestedSubscriptionID": {
            "type": "String"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2021-04-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2021-04-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2021-04-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            },
            "subscriptionId": "[parameters('nestedSubscriptionID')]"
        }
    ]
}

## **parameters**

{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "my"
    },
    "newResourceGroupName": {
      "value": "mygroup"
    },
    "nestedSubscriptionID": {
      "value": "6eec7c4d-b853-40dd-8e88-bd85675d146b"
    },
    "location": {
      "value": "eastus"
    }
  }
}
