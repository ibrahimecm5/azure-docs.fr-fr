---
title: Activer l’intégrité de l’invité de VM Insights (préversion)
description: Décrit comment activer l’intégrité des invités de VM Insights dans votre abonnement et comment intégrer des machines virtuelles.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/05/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 3855cc3dff2d29c659c6613e1321529745239c7c
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634101"
---
# <a name="enable-vm-insights-guest-health-preview"></a>Activer l’intégrité de l’invité de VM Insights (préversion)
L’intégrité des invités de VM Insights vous permet de voir l’état d’intégrité d’une machine virtuelle tel que défini par un ensemble de mesures de performances échantillonnées à intervalles réguliers. Cet article explique comment activer cette fonctionnalité dans votre abonnement et comment activer la supervision des invités pour chaque machine virtuelle.

## <a name="current-limitations"></a>Limites actuelles
L’intégrité des invités de VM Insights présente les limitations suivantes dans la préversion publique :

- Seules les machines virtuelles Azure sont actuellement prises en charge. Azure Arc pour serveurs n’est pas pris en charge actuellement.
- Les proxys réseau ne sont actuellement pas pris en charge.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
La machine virtuelle doit exécuter l’un des systèmes d’exploitation suivants : 

  - CentOS 7.5, 7.6, 7.7, 7.8, 7.9
  - RedHat 7.5, 7.6, 7.7, 7.8, 7.9
  - Ubuntu 16.04 LTS, Ubuntu 18.04 LTS
  - Windows Server 2012 ou version ultérieure

## <a name="supported-regions"></a>Régions prises en charge

La machine virtuelle doit se trouver dans l’une des régions suivantes :

- Centre de l’Australie
- Australie Est
- Sud-Australie Est
- Brésil Sud
- Centre du Canada
- Inde centrale
- USA Centre
- Asie Est
- USA Est
- USA Est 2
- USA Est 2 (EUAP)
- France Centre
- Allemagne Centre-Ouest
- Japon Est
- OuJapon Est
- Centre de la Corée
- Centre-Nord des États-Unis
- Europe Nord
- Norvège Est
- États-Unis - partie centrale méridionale
- Afrique du Sud Nord
- Asie Sud-Est
- Suisse Nord
- Émirats arabes unis Nord
- Sud du Royaume-Uni
- Ouest du Royaume-Uni
- Centre-USA Ouest
- Europe Ouest
- USA Ouest
- USA Ouest 2


L’espace de travail Log Analytics doit se trouver dans l’une des régions suivantes :

- Centre de l’Australie
- Australie Est
- Sud-Australie Est
- Brésil Sud
- Brésil Sud-Est
- Centre du Canada
- Canada Inde
- USA Centre
- Asie Est
- USA Est
- USA Est 2
- USA Est 2 (EUAP)
- France Centre
- Japon Est
- OuJapon Est
- Centre-Nord des États-Unis
- Europe Nord
- Norvège Est
- États-Unis - partie centrale méridionale
- Asie Sud-Est
- Suisse Nord
- Suisse Ouest
- Émirats arabes unis Nord
- Sud du Royaume-Uni
- Région Europe Ouest
- USA Ouest
- USA Ouest 2

## <a name="prerequisites"></a>Prérequis

- La machine virtuelle doit être intégrée à VM Insights.
- L’utilisateur qui exécute les étapes d’intégration doit avoir un accès minimal de niveau contributeur à l’abonnement dans lequel se trouvent la machine virtuelle et la règle de collecte de données.
- Les fournisseurs de ressources Azure requis doivent être inscrits comme décrit dans la section suivante.

## <a name="register-required-azure-resource-providers"></a>Inscrire les fournisseurs de ressources Azure requis
Si vous souhaitez activer l’intégrité des invités de VM Insights, les fournisseurs de ressources Azure suivants doivent être inscrits pour votre abonnement. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

Vous pouvez utiliser toute méthode disponible pour inscrire un fournisseur de ressources, comme décrit dans [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Vous pouvez également utiliser l’exemple de commande suivant à l’aide de la méthode armclient, de la méthode postman ou d’une autre méthode pour effectuer un appel authentifié à Azure Resource Manager :

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Activer une machine virtuelle à l’aide du portail Azure
Lorsque vous activez l’intégrité des invités pour une machine virtuelle dans le portail Azure, toute la configuration requise est effectuée pour vous. Cela comprend la création de la règle de collecte de données requise, l’installation de l’extension d’intégrité des invités sur la machine virtuelle, et la création d’une association avec la règle de collecte de données.

Dans l’affichage **Prise en main** dans VM Insights, cliquez sur le lien en regard du message de mise à niveau d’une machine virtuelle, puis cliquez sur le bouton **Mettre à niveau**. Vous pouvez également sélectionner plusieurs machines virtuelles pour les mettre à niveau ensemble.

![Activer la fonctionnalité d’intégrité sur une machine virtuelle](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Activer une machine virtuelle à l’aide d’un modèle Resource Manager
Trois étapes sont nécessaires pour activer des machines virtuelles à l’aide d’Azure Resource Manager.

- Créer une règle de collecte de données.
- Installer l’extension d’intégrité des invités sur chaque machine virtuelle.
- Créer une association entre la machine virtuelle et la règle de collecte de données.

### <a name="create-data-collection-rule-dcr"></a>Créer une règle de collecte de données

> [!NOTE]
> Si vous activez une machine virtuelle à l’aide du portail Azure, la règle de collecte de données décrite ici est créée pour vous. Dans ce cas, vous n’avez pas besoin de suivre cette étape.

La configuration des analyses dans l’intégrité des invités de VM Insights est stockée dans des [règles de collecte de données](../agents/data-collection-rule-overview.md). Chaque machine virtuelle avec l’extension d’intégrité des invités aura besoin d’une association avec cette règle.

> [!NOTE]
> Vous pouvez créer des règles de collecte de données supplémentaires pour modifier la configuration par défaut des analyses, comme décrit dans [Configurer la supervision de l’intégrité des invités de VM Insights (préversion)](vminsights-health-configure.md).

Le modèle requiert des valeurs pour les paramètres suivants :

- **defaultHealthDataCollectionRuleName** : conservez le nom par défaut défini dans le modèle.
- **destinationWorkspaceResourceId** : ID de ressource de l’espace de travail Log Analytics utilisé pour la collecte de données de machine virtuelle.
- **dataCollectionRuleLocation** : région de la règle de collecte de données. Elle doit correspondre à la région de l’espace de travail Log Analytics.


Déployez le modèle à l’aide de toute [méthode de déploiement de modèles Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Les commandes suivantes déploient le modèle et le fichier de paramètres à l’aide de PowerShell ou d’Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

La règle de collecte de données définie dans le modèle Resource Manager ci-dessous permet d'activer tous les moniteurs pour les machines virtuelles dotées de l'extension d'intégrité des invités. Elle doit inclure les sources de données de chacun des compteurs de performances utilisés par les moniteurs.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Exemple de fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Installer l’extension d’intégrité des invités et associer à la règle de collecte de données
Utilisez le modèle Resource Manager suivant pour activer une machine virtuelle pour l’intégrité des invités. Cela a pour effet d’installer l’extension d’intégrité des invités et de créer l’association avec la règle de collecte de données. Vous pouvez déployer ce modèle à l’aide de toute [méthode de déploiement de modèles Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md).


Par exemple, utilisez les commandes suivantes pour déployer le fichier de modèle et celui des paramètres sur un groupe de ressources à l’aide de PowerShell ou d’Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Fichier de modèle

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": true 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": true 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Exemple de fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Personnaliser les analyses activées par VM Insights](vminsights-health-configure.md)
