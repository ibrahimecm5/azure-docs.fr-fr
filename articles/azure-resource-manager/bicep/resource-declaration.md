---
title: Déclarer des ressources dans Bicep
description: Explique comment déclarer des ressources en vue de les déployer dans Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 1398215116307cf810d259ac52c30f6588e612ab
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494000"
---
# <a name="resource-declaration-in-bicep"></a>Déclaration de ressources dans Bicep

Cet article décrit la syntaxe utilisée pour ajouter une ressource à votre fichier Bicep.

## <a name="declaration"></a>Déclaration

Ajoutez une déclaration de ressource en utilisant le mot clé `resource`. Vous définissez un nom symbolique pour la ressource. Le nom symbolique n’est pas le même que le nom de la ressource. Vous utiliserez le nom symbolique pour référencer la ressource dans d’autres parties de votre fichier Bicep.

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  <resource-properties>
}
```

Une déclaration pour un compte de stockage peut donc commencer par :

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  ...
}
```

Les noms symboliques respectent la casse. Ils peuvent contenir des lettres, des chiffres et des traits de soulignement (`_`). Ils ne peuvent pas commencer par un chiffre. Une ressource ne peut pas avoir le même nom qu’un paramètre, un module ou une variable.

Pour obtenir les types et versions des ressources disponibles, consultez les [informations de référence sur les ressources Bicep](/azure/templates/). Bicep ne prend pas en charge `apiProfile`, qui est disponible dans le [JSON des modèles Azure Resource Manager (modèles ARM)](../templates/syntax.md).

Pour déployer une ressource de manière conditionnelle, utilisez la syntaxe `if`. Pour plus d’informations, consultez [Déploiement conditionnel dans Bicep](conditional-resource-deployment.md).

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = if (condition) {
  <resource-properties>
}
```

Pour déployer plusieurs instances d’une ressource, utilisez la syntaxe `for`. Vous pouvez utiliser l’élément décoratif `batchSize` pour spécifier si les instances sont déployées en série ou en parallèle. Pour plus d’informations, consultez [Boucles itératives dans Bicep](loops.md).

```bicep
@batchSize(int) // optional decorator for serial deployment
resource <symbolic-name> '<full-type-name>@<api-version>' = [for <item> in <collection>: {
  <properties-to-repeat>
}]
```

Vous pouvez également utiliser la syntaxe `for` sur les propriétés de ressource pour créer un tableau.

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  properties: {
    <array-property>: [for <item> in <collection>: <value-to-repeat>]
  }
}
```

## <a name="resource-name"></a>Nom de la ressource

Chaque ressource a un nom. Lorsque vous définissez le nom de la ressource, soyez attentif aux [règles et restrictions applicables aux noms de ressources](../management/resource-name-rules.md).

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

En règle générale, vous définissez le nom sur un paramètre afin de passer des valeurs différentes lors du déploiement.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="location"></a>Location

De nombreuses ressources nécessitent un emplacement. Vous pouvez déterminer si la ressource a besoin d’un emplacement via IntelliSense ou une [référence de modèle](/azure/templates/). L’exemple suivant ajoute un paramètre d’emplacement utilisé pour le compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

En règle générale, vous définissez l’emplacement sur un paramètre afin de pouvoir le déployer à différents emplacements.

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

Différents types de ressources sont pris en charge à différents emplacements. Pour connaître les régions pris en charge pour un service Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/).  Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez Azure PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="tags"></a>Étiquettes

Vous pouvez appliquer des étiquettes à une ressource pendant le déploiement. Les étiquettes vous aident à organiser logiquement vos ressources déployées. Pour obtenir des exemples de différentes façons de spécifier les étiquettes, consultez [Étiquettes de modèle ARM](../management/tag-resources.md#arm-templates).

## <a name="managed-identities-for-azure-resources"></a>Identités gérées pour les ressources Azure

Certaines ressources prennent en charge des [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Ces ressources ont un objet Identité au niveau racine de la déclaration de ressource.

Vous pouvez utiliser des identités attribuées par le système ou attribuées par l’utilisateur.

L’exemple suivant montre comment configurer une identité attribuée par le système pour un cluster Azure Kubernetes Service.

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

L’exemple suivant montre comment configurer une identité attribuée par l’utilisateur pour une machine virtuelle.

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="resource-specific-properties"></a>Propriétés spécifiques aux ressources

Les propriétés précédentes sont génériques pour la plupart des types de ressources. Après avoir défini ces valeurs, vous devez définir les propriétés qui sont spécifiques du type de ressource que vous déployez.

Utilisez IntelliSense ou les [informations de référence sur les ressources Bicep](/azure/templates/) pour déterminer les propriétés disponibles et celles qui sont exigées. L’exemple suivant définit les propriétés restantes pour un compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="dependencies"></a>Les dépendances

Quand vous déployez des ressources, vous devez éventuellement vous assurer que certaines ressources existent au préalable avant d’autres ressources. Par exemple, vous avez besoin d’un serveur SQL logique avant de déployer une base de données. Vous établissez cette relation en marquant une seule ressource comme dépendante de l’autre ressource. L’ordre de déploiement des ressources peut être influencé de deux manières : une [dépendance implicite](#implicit-dependency) et une [dépendance explicite](#explicit-dependency)

Azure Resource Manager évalue les dépendances entre les ressources et les déploie dans leur ordre dépendant. Quand les ressources ne dépendent pas les unes des autres, Resource Manager les déploie en parallèle. Vous devez uniquement définir des dépendances pour les ressources qui sont déployées dans un même fichier Bicep.

### <a name="implicit-dependency"></a>Dépendance implicite

Une dépendance implicite est créée lorsqu’une déclaration de ressource référence une autre ressource dans le même déploiement. Par exemple, dans l’exemple suivant, *dnsZone* est référencé par la deuxième définition de ressource :

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

Une ressource imbriquée a également une dépendance implicite à la ressource qui la contient.

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

Lorsqu’il existe une dépendance implicite, **n’ajoutez pas de dépendance explicite**.

Pour plus d’informations sur les ressources imbriquées, consultez [Définir le nom et le type des ressources enfants dans Bicep](./child-resource-name-type.md).

### <a name="explicit-dependency"></a>Dépendance explicite

Une dépendance explicite est déclarée avec la propriété `dependsOn`. La propriété accepte un tableau d’identificateurs de ressource, ce qui vous permet de spécifier plusieurs dépendances.

L’exemple suivant montre une zone DNS nommée `otherZone` qui dépend d’une zone DNS nommée `dnsZone` :

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

Vous pouvez être tenté d’utiliser `dependsOn` pour mapper les relations entre vos ressources. Il est toutefois important de comprendre pourquoi vous le faites. Par exemple, pour documenter la manière dont les ressources sont liées entre elles, `dependsOn` n’est pas la bonne approche. Vous ne pouvez pas lancer de requête pour savoir quelles ressources ont été définies dans l’élément `dependsOn` après le déploiement. La définition de dépendances inutiles ralentit le temps de déploiement, Resource Manager ne pouvant pas déployer ces ressources en parallèle.

Les dépendances explicites sont rarement nécessaires. Dans la plupart des cas, vous pouvez utiliser un nom symbolique pour supposer la dépendance entre les ressources. Si vous vous retrouvez à définir des dépendances explicites, vous devez vous demander s’il existe un moyen de les supprimer.

### <a name="visualize-dependencies"></a>Visualiser les dépendances

Visual Studio Code fournit un outil qui permet de visualiser les dépendances. Ouvrez un fichier Bicep dans Visual Studio Code, puis sélectionnez le bouton du visualiseur dans le coin supérieur gauche.  La capture d’écran suivante montre les dépendances d’une machine virtuelle.

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Capture d’écran du visualiseur de ressources Bicep dans Visual Studio Code":::

## <a name="existing-resources"></a>Ressources existantes

Pour référencer une ressource qui ne se trouve pas dans le fichier Bicep actuel, utilisez le mot clé `existing` dans une déclaration de ressource.

Si vous utilisez le mot clé `existing`, indiquez le `name` de la ressource. L’exemple suivant montre l’obtention d’un compte de stockage existant dans le même groupe de ressources que le déploiement actuel.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

Vous pouvez éventuellement définir la propriété `scope` pour accéder à une ressource dans une étendue différente. L’exemple suivant référence un compte de stockage existant dans un autre groupe de ressources.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

Si vous tentez de référencer une ressource qui n’existe pas, vous recevez l’erreur `NotFound` et votre déploiement échoue.

Pour plus d’informations sur la définition de l’étendue, consultez [Fonctions d’étendue pour Bicep](bicep-functions-scope.md).

Les exemples précédents ne déploient pas le compte de stockage. Au lieu de cela, vous pouvez accéder aux propriétés de la ressource existante en utilisant le nom symbolique.

## <a name="next-steps"></a>Étapes suivantes

- Pour déployer une ressource de manière conditionnelle, consultez [Déploiement conditionnel dans Bicep](./conditional-resource-deployment.md).
