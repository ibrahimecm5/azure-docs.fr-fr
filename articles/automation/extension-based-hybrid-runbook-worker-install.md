---
title: Déployer un Runbook Worker hybride utilisateur Windows ou Linux basé sur une extension dans Azure Automation (préversion)
description: Cet article décrit comment déployer un Runbook Worker hybride Windows ou Linux basé sur une extension qui vous permet d’exécuter des runbooks sur des ordinateurs Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 48f83cd92f682b6fb66a1d7b5bebd49cd5053b62
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400772"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>Déployer un Runbook Worker hybride utilisateur Windows ou Linux basé sur une extension dans Automation (préversion)

L’intégration basée sur l’extension ne concerne que les Runbooks Workers hybrides **d’utilisateur**. Pour l’intégration d’un Runbook Worker hybride **système**, consultez [Déployer un Runbook Worker hybride Windows basé sur un agent dans Automation](./automation-windows-hrw-install.md) ou [Déployer un Runbook Worker hybride Linux basé sur un agent dans Automation](./automation-linux-hrw-install.md). 

Vous pouvez utiliser la fonctionnalité Runbook Worker hybride utilisateur d’Azure Automation pour exécuter des runbooks directement sur une machine Azure ou non Azure, y compris les serveurs inscrits auprès de [serveurs avec Azure Arc](../azure-arc/servers/overview.md). Sur l’ordinateur ou le serveur qui héberge le rôle, vous pouvez exécuter les runbooks directement et avec les ressources disponibles dans l’environnement pour gérer ces ressources locales.

Azure Automation stocke et gère les runbooks, puis les livre à une ou plusieurs machines choisies. Cet article explique comment déployer un Runbook Worker hybride utilisateur sur une machine Windows ou Linux, supprimer le Worker et supprimer un groupe de Runbooks Workers hybrides.

Une fois le Runbook Worker déployé, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos Runbooks en vue d’automatiser les processus de votre centre de données local ou autre environnement cloud.

## <a name="prerequisites"></a>Prérequis

### <a name="machine-minimum-requirements"></a>Configuration minimale de la machine
- 2 cœurs
- 4 Go de RAM
- L’identité managée affectée par le système doit être activée sur la machine virtuelle Azure ou le serveur Arc.  Si l’identité managée affectée par le système n’est pas activée, elle le sera dans le cadre du processus d’ajout.
- L’agent des serveurs Azure Arc (l’agent de la machine connectée) doit être installé sur les machines non Azure. Pour obtenir des instructions sur l’installation de `AzureConnectedMachineAgent`, consultez [Connecter des machines hybrides à Azure à partir du portail Azure](../azure-arc/servers/onboard-portal.md).

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
| Windows | Linux (x64)|
|---|---|
| &#9679; Windows Server 2019 (avec Server Core) ; <br> &#9679; Windows Server 2016, version 1709 et 1803 (à l’exception de Server Core) ; et <br> &#9679; Windows Server 2012, 2012 R2. <br><br> | &#9679; Debian GNU/Linux 7 et 8 ; <br> &#9679; Ubuntu 18.04 et 20.04 LTS ; <br> &#9679; SUSE Linux Enterprise Server 15 et 15.1 (SUSE n’a pas publié de numéros de version 13 ni 14) ; et <br> &#9679; Red Hat Enterprise Linux Server 7 et 8. |

### <a name="other-requirements"></a>Autres conditions requises
| Windows | Linux (x64)|
|---|---|
| Windows PowerShell 5.1 (télécharger WMF 5.1). PowerShell Core n’est pas pris en charge.| Le durcissement de Linux ne doit pas être activé.  |
| .NET Framework 4.6.2 ou ultérieur. ||

### <a name="package-requirements-for-linux"></a>Exigences relatives aux packages pour Linux

| Package requis | Description |Version minimale|
|--------------------- | --------------------- | -------------------|
|Glibc |Bibliothèque C de GNU| 2.5-12 |
|Openssl| Bibliothèques OpenSSL | 1.0 (TLS 1.1 et TLS 1.2 sont pris en charge)|
|Curl | Client web cURL | 7.15.5|
|Python-ctypes | Bibliothèque de fonctions étrangères pour Python| Python 2.x ou Python 3.x sont obligatoire |
|PAM | Modules d’authentification enfichable|

| Package facultatif | Description | Version minimale|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | Pour exécuter des runbooks PowerShell, vous devez avoir installé PowerShell Core. Pour obtenir des instructions, consultez [Installation de PowerShell Core sur Linux](/powershell/scripting/install/installing-powershell-core-on-linux) | 6.0.0 |

### <a name="network-requirements"></a>Configuration requise pour le réseau

### <a name="proxy-server-use"></a>Utilisation du serveur proxy

Si vous utilisez un serveur proxy pour la communication entre Azure Automation et les machines exécutant le Runbook Worker hybride basé sur une extension, veillez à ce que les ressources appropriées soient accessibles. Le délai d’expiration pour les demandes du Runbook Worker hybride et des services Automation est de 30 secondes. Après trois tentatives, une demande échoue.

### <a name="firewall-use"></a>Utilisation du pare-feu

Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer le pare-feu pour autoriser l’accès. Le port et les URL suivants sont requis pour Runbook Worker hybride et pour la communication entre [Automation State Configuration](./automation-dsc-overview.md) et Azure Automation.

| Propriété | Description |
|---|---|
|Port | 443 pour l’accès Internet sortant|
|URL globale |\* .azure-automation.net|
|URL globale de la région US Gov Virginie |*.azure-automation.us|
|Service Agent |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>Créer un groupe de Workers hybrides 

Pour créer un groupe de Workers hybrides dans le portail Azure, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation. 

1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**. 

1. Sélectionner **+ Créer un groupe de Workers hybrides**. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="Sélection de l’option Groupes de Workers hybrides dans le portail.":::

1. Dans l’onglet  **Informations de base** , dans la zone de texte **Nom**, entrez un nom pour votre groupe de Workers hybrides. 

1. Pour l’option **Utiliser les informations d’identification du compte d’identification** : 
   - Si vous sélectionnez **Non**, l’extension hybride sera installée à l’aide du compte système local.
   - Si vous sélectionnez **Oui**, dans la liste déroulante, sélectionnez la ressource d’informations d’identification.

1. Sélectionnez **Suivant** pour passer à l’onglet **Workers hybrides**. Vous pouvez sélectionner des machines virtuelles Azure ou des serveurs Azure Arc à ajouter à ce groupe de Workers hybrides. Si vous ne sélectionnez aucune machine, un groupe de Workers hybrides vide sera créé. Vous pourrez toujours ajouter des machines ultérieurement. 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="Saisie du nom et des informations d’identification dans l’onglet Informations de base.":::

1. Sélectionnez **Ajouter des machines** pour accéder à la page **Ajouter des machines en tant que Worker hybride**. Seules les machines qui ne font pas partie d’un autre groupe de Workers hybrides s’affichent. 

1. Cochez la case à côté des machines que vous souhaitez ajouter au groupe de Workers hybrides. Si votre machine non Azure n’apparaît pas dans la liste, assurez-vous que l’agent Azure Arc Connected Machine est installé sur la machine.  

1. Sélectionnez **Ajouter**. 

1. Sélectionnez **Suivant** pour passer à l’onglet **Vérifier + créer**. 

1. Sélectionnez **Create** (Créer). L’extension de Worker hybride s’installe sur la machine et le Worker hybride est inscrit auprès du groupe de Workers hybrides. L’ajout d’un Worker hybride au groupe est immédiat, alors que l’installation de l’extension peut prendre quelques minutes. Sélectionnez **Actualiser** pour voir le nouveau groupe. Sélectionnez le nom du groupe pour afficher les détails des Workers hybrides.

   > [!NOTE]
   > Une machine sélectionnée ne sera pas ajoutée à un groupe de Workers hybrides si elle fait déjà partie d’un autre groupe de Workers hybrides.

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>Ajouter une machine à un groupe de Workers hybrides

Vous pouvez également ajouter des machines à un groupe de Workers hybrides existant.

1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**, puis votre groupe de Workers hybrides existant pour accéder à la page **Groupe de Workers hybrides**.

1. Sous **Groupe de Workers hybrides**, sélectionnez **Workers hybrides**.

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="Bouton Ajouter pour ajouter des machines au groupe existant.":::

1. Sélectionnez **+ Ajouter** pour accéder à la page **Ajouter des machines en tant que Worker hybride**. Seules les machines qui ne font pas partie d’un autre groupe de Workers hybrides s’affichent. 

1. Cochez la case à côté des machines que vous souhaitez ajouter au groupe de Workers hybrides. Si votre machine non Azure n’apparaît pas dans la liste, assurez-vous que l’agent Azure Arc Connected Machine est installé sur la machine.

1. Sélectionnez **Ajouter** pour ajouter la machine au groupe. Une fois ajoutée, vous pouvez voir que le type de machine est une machine virtuelle Azure ou un serveur Arc. Le champ **Plateforme** indique que le Worker est **basé sur un agent (v1)** ou **basé sur une extension (v2)** .

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="Champ Plateforme indiquant « basé sur un agent » ou « basé sur une extension ».":::

## <a name="delete-a-hybrid-runbook-worker"></a>Supprimer un Runbook Worker hybride

Vous pouvez supprimer le Runbook Worker hybride à partir du portail.

1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**, puis votre groupe de Workers hybrides pour accéder à la page **Groupe de Workers hybrides**.

1. Sous **Groupe de Workers hybrides**, sélectionnez **Workers hybrides**.

1. Cochez la case à côté des machines que vous souhaitez supprimer du groupe de Workers hybrides.

1. Sélectionnez **Supprimer**.  La boîte de dialogue **Supprimer le Worker hybride** s’affiche et vous avertit que le Worker hybride sélectionné sera supprimé définitivement. Sélectionnez **Supprimer**. Cette opération permet de supprimer l’extension du Worker **Basé sur une extension (v2)** ou de supprimer l’entrée **Basé sur un agent (v1)** du portail. Toutefois, elle laisse le Worker hybride obsolète sur la machine virtuelle. Pour désinstaller manuellement l’agent, consultez [Désinstaller l’agent](../azure-monitor/agents/agent-manage.md#uninstall-agent).

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="Supprimer la machine virtuelle du groupe existant.":::

   > [!NOTE]
   > Un Worker hybride peut coexister avec les deux plateformes : **Basé sur un agent (v1)** et **Basé sur une extension (v2)** . Si vous installez **Basé sur une extension (v2)** sur un Worker hybride fonctionnant déjà avec **Basé sur un agent (v1)** , vous verrez deux entrées du Runbook Worker hybride dans le groupe. L’un avec la plateforme **Basé sur une extension (v2)** et l’autre avec **Basé sur un agent (v1)** .

## <a name="delete-a-hybrid-runbook-worker-group"></a>Supprimer un groupe de Runbooks Workers hybrides

Vous pouvez supprimer un groupe de Runbooks Workers hybrides vide à partir du portail.

1. Sous **Automatisation de processus**, sélectionnez **Groupes de Workers hybrides**, puis votre groupe de Workers hybrides pour accéder à la page **Groupe de Workers hybrides**.

1. Sélectionnez **Supprimer**. Une boîte de dialogue affiche un avertissement et vous invite à supprimer toutes les machines définies comme des Workers hybrides dans le groupe de Workers hybrides. Si un Worker a déjà été ajouté au groupe, vous devrez d’abord le supprimer du groupe.

1. Sélectionnez **Oui**. Le groupe de Workers hybrides sera supprimé.

## <a name="use-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager (ARM) pour créer une nouvelle machine virtuelle Azure Windows et la connecter à un compte Automation et à un groupe de Workers hybrides existants. Pour en savoir plus sur les modèles ARM, consultez [Que sont les modèles ARM ?](../azure-resource-manager/templates/overview.md)

### <a name="review-the-template"></a>Vérifier le modèle

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

Ressources Azure définies dans le modèle :

- [hybridRunbookWorkerGroups/hybridRunbookWorkers](/azure/templates/microsoft.automation/automationaccounts/hybridrunbookworkergroups/hybridrunbookworkers)
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)

### <a name="review-parameters"></a>Passer en revue les paramètres

Passez en revue les paramètres utilisés dans ce modèle.

|Propriété |Description |
|---|---|
|automationAccount| Nom du compte Automation existant. |
|automationAccountLocation | Région du compte Automation existant. |
|workerGroupName | Nom du groupe de Workers hybrides existant. |
|virtualMachineName| Nom de la machine virtuelle à créer. La valeur par défaut est `simple-vm`.|
|adminUsername| Nom d’utilisateur administrateur de la machine virtuelle. |
|adminPassword| Mot de passe administrateur de la machine virtuelle. |
|vmLocation| Région de la nouvelle machine virtuelle. La valeur par défaut est `North Central US`.|
|vmSize| Taille de la nouvelle machine virtuelle. La valeur par défaut est `Standard_DS1_v2`. |
|osVersion| Système d’exploitation pour la nouvelle machine virtuelle Windows. La valeur par défaut est `2019-Datacenter`.|
|dnsNameForPublicIP| Nom DNS de l’IP publique. |


## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).

* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride](troubleshoot/hybrid-runbook-worker.md#general).
