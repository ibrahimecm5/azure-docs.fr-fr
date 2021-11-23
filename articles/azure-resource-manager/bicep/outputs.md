---
title: Sorties dans Bicep
description: Décrit comment définir des variables dans Bicep
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4ed640cd639b7a1b69cd07ae8ac50a4ddff436fb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491314"
---
# <a name="outputs-in-bicep"></a>Sorties dans Bicep

Cet article explique comment définir des valeurs de sortie dans un fichier Bicep. Vous utilisez des sorties quand vous devez retourner des valeurs à partir des ressources déployées.

## <a name="define-output-values"></a>Définir des valeurs de sortie

La syntaxe permettant de définir une valeur de sortie est la suivante :

```bicep
output <name> <data-type> = <value>
```

Une sortie peut avoir le même nom qu’un paramètre, une variable, un module ou une ressource. Chaque valeur de sortie doit être résolue en l’un des [types de données](data-types.md).

L’exemple suivant montre comment renvoyer une propriété à partir d’une ressource déployée. Dans l’exemple, `publicIP` est le nom symbolique d’une adresse IP publique déployée dans le fichier Bicep. La valeur de sortie obtient le nom de domaine complet pour l’adresse IP publique.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

L’exemple suivant montre comment retourner des sorties de types différents.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

Si vous avez besoin de générer une propriété dont le nom comporte un trait d’union, utilisez des crochets autour du nom au lieu de la notation par points. Par exemple, utilisez `['property-name']` au lieu de `.property-name`.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>Sortie conditionnelle

Lorsque la valeur à retourner dépend d’une condition dans le déploiement, utilisez l’opérateur `?`.

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

En général, vous utilisez une sortie conditionnelle quand vous avez [déployé de manière conditionnelle](conditional-resource-deployment.md) une ressource. L’exemple suivant montre comment retourner de façon conditionnelle l’ID de ressource pour une adresse IP publique si une nouvelle a été déployée.

Pour spécifier une sortie conditionnelle dans Bicep, utilisez l'opérateur `?`. L’exemple suivant renvoie une URL de point de terminaison ou une chaîne vide en fonction d’une condition.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>Nombre dynamique de sorties

Dans certains scénarios, vous ne connaissez pas le nombre d’instances d’une valeur que vous devez retourner lors de la création du modèle. Vous pouvez retourner un nombre variable de valeurs à l’aide de l’expression `for`.

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

L’exemple suivant itère au sein d’un tableau.

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

Pour plus d’informations sur les boucles, consultez [Boucles itératives dans Bicep](loops.md).

## <a name="outputs-from-modules"></a>Sorties des modules

Pour récupérer une valeur de sortie à partir d’un module, utilisez la syntaxe suivante :

```bicep
<module-name>.outputs.<property-name>
```

L’exemple suivant montre comment définir l’adresse IP sur un équilibreur de charge en récupérant une valeur à partir d’un module. Le nom du module est `publicIP`.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="get-output-values"></a>Obtenir des valeurs de sortie

Une fois le déploiement terminé, les valeurs de sortie sont automatiquement retournées dans les résultats du déploiement.

Pour obtenir des valeurs de sortie à partir de l’historique des déploiements, vous pouvez utiliser un script Azure CLI ou Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les propriétés disponibles pour les sorties, consultez [Présentation de la structure et de la syntaxe de Bicep](./file.md).
