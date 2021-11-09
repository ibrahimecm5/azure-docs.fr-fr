---
title: Extension Guest Configuration
description: En savoir plus sur l’extension utilisée pour auditer/configurer les paramètres dans les machines virtuelles
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25e167f80ff209a60b30113ffe681e033cc612e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046524"
---
# <a name="overview-of-the-guest-configuration-extension"></a>Vue d’ensemble de l’extension Guest Configuration

L’extension Guest Configuration est un composant Azure Policy qui effectue des opérations d’audit et de configuration sur des machines virtuelles.
Les stratégies telles que les définitions de la ligne de base de sécurité pour [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) et [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) ne peuvent pas vérifier les paramètres dans les machines tant que l’extension n’est pas installée.

## <a name="prerequisites"></a>Prérequis

Pour que la machine s’authentifie auprès du service Guest Configuration, elle doit disposer d’une [identité managée affectée par le système](../../active-directory/managed-identities-azure-resources/overview.md).
L’exigence d’identité sur une machine virtuelle est satisfaite si la propriété suivante est définie.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Systèmes d'exploitation

La prise en charge de l’extension Guest Configuration est identique à celle du système d’exploitation [documentée pour la solution de bout en bout](../../governance/policy/concepts/guest-configuration.md#supported-client-types).

### <a name="internet-connectivity"></a>Connectivité Internet

L’agent installé par l’extension Guest Configuration doit être en mesure d’atteindre les packages de contenu répertoriés par les attributions de Guest Configuration et de signaler l’état au service Guest Configuration.
La machine peut se connecter à l’aide du protocole HTTPS sortant sur le port TCP 443 ou si une connexion est fournie via un réseau privé.
Pour en savoir plus sur la mise en réseau privée, consultez les articles suivants :

- [Guest Configuration, communiquer via une liaison privée dans Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Utiliser des points de terminaison privés pour Stockage Azure](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Comment installer l’extension ?

Le nom d’instance de l’extension doit être défini sur « AzurePolicyforWindows » ou « AzurePolicyforLinux », car les stratégies référencées ci-dessus nécessitent ces chaînes spécifiques.

Par défaut, tous les déploiements sont mis à jour vers la version la plus récente. La valeur de la propriété _autoUpgradeMinorVersion_ est définie sur « true » par défaut, sauf spécification contraire. Vous n’avez pas à vous soucier de la mise à jour de votre code lorsque de nouvelles versions de l’extension sont publiées.

## <a name="automatic-upgrade"></a>Mise à jour automatique

L’extension de configuration d’invité prend en charge la propriété `enableAutomaticUpgrade`. Lorsque cette propriété est définie sur `true`, Azure effectue automatiquement une mise à niveau vers la version la plus récente de l’extension au fur et à mesure que les versions ultérieures sont disponibles. Pour plus d’informations, consultez la page [Mise à niveau automatique des extensions pour les machines virtuelles et les groupes identiques dans Azure](../automatic-extension-upgrade.md).

### <a name="azure-policy"></a>Azure Policy

Pour déployer à grande échelle la dernière version de l’extension, notamment les exigences en matière d’identité, [attribuez](../../governance/policy/assign-policy-portal.md) l’instance Azure Policy :

[Déployer les prérequis pour activer les stratégies Guest Configuration sur les machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).

### <a name="azure-cli"></a>Azure CLI

Pour déployer l’extension pour Linux :


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM --enable-auto-upgrade true
```

Pour déployer l’extension pour Windows :

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM --enable-auto-upgrade true
```

### <a name="powershell"></a>PowerShell

Pour déployer l’extension pour Linux :

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM' -EnableAutomaticUpgrade $true
```

Pour déployer l’extension pour Windows :

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM' -EnableAutomaticUpgrade $true
```

### <a name="resource-manager-template"></a>Modèle Resource Manager

Pour déployer l’extension pour Linux :

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "enableAutomaticUpgrade": true, 
    "settings": {},
    "protectedSettings": {}
  }
}
```

Pour déployer l’extension pour Windows :

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2020-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "enableAutomaticUpgrade": true, 
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="bicep"></a>Bicep

Pour déployer l’extension pour Linux :

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforLinux'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforLinux'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    enableAutomaticUpgrade: true
    settings: {}
    protectedSettings: {}
  }
}
```

Pour déployer l’extension pour Windows :

```bicep
resource virtualMachine 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: 'VMName'
}
resource windowsVMGuestConfigExtension 'Microsoft.Compute/virtualMachines/extensions@2020-12-01' = {
  parent: virtualMachine
  name: 'AzurePolicyforWindows'
  location: resourceGroup().location
  properties: {
    publisher: 'Microsoft.GuestConfiguration'
    type: 'ConfigurationforWindows'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    enableAutomaticUpgrade: true
    settings: {}
    protectedSettings: {}
  }
}
```

### <a name="terraform"></a>Terraform

Pour déployer l’extension pour Linux :

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforLinux"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforLinux"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

Pour déployer l’extension pour Windows :

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                       = "AzurePolicyforWindows"
  virtual_machine_id         = "myVMID"
  publisher                  = "Microsoft.GuestConfiguration"
  type                       = "ConfigurationforWindows"
  type_handler_version       = "1.0"
  auto_upgrade_minor_version = "true"
}
```

## <a name="settings"></a>Paramètres

Il n’est pas nécessaire d’inclure des paramètres ou des propriétés de paramètre protégé sur l’extension.
Toutes ces informations sont récupérées par l’agent à partir des ressources d’[attribution de Guest Configuration](/rest/api/guestconfiguration/guestconfigurationassignments). Par exemple, les propriétés [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode) et [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) sont toutes gérées par configuration plutôt que sur l’extension de machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure Policy Guest Configuration, consultez [Comprendre Azure Policy Guest Configuration](../../governance/policy/concepts/guest-configuration.md)
* Pour en savoir plus sur le fonctionnement des extensions et de l’agent Linux, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Linux](features-linux.md).
* Pour en savoir plus sur le fonctionnement des extensions et de l’agent invité de Windows, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Windows](features-windows.md).  
* Pour installer l’agent invité Windows, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Windows](agent-windows.md).  
* Pour installer l’agent Linux, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Linux](agent-linux.md).  
