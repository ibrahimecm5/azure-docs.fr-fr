---
title: Boucles itératives dans Bicep
description: Utiliser des boucles pour effectuer une itération sur des collections dans Bicep
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 9c38824fd3fdf10ce807b66aeccda2c0631cbc58
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270715"
---
# <a name="iterative-loops-in-bicep"></a>Boucles itératives dans Bicep

Cet article explique comment utiliser la syntaxe `for` pour effectuer une itération sur des éléments d’une collection. Vous pouvez utiliser des boucles pour définir plusieurs copies d’une ressource, d’un module, d’une variable, d’une propriété ou d’une sortie. Utilisez des boucles pour éviter de répéter une syntaxe dans votre fichier Bicep, ainsi que pour définir de façon dynamique le nombre de copies à créer au cours du déploiement.

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les boucles et pour obtenir des conseils pratiques, consultez [Créer des modèles Bicep flexibles en utilisant des conditions et des boucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) sur **Microsoft Learn**.

## <a name="loop-syntax"></a>Syntaxe de boucle

Des boucles peuvent être déclarées comme suit :

- En utilisant un **index d’entiers**. Cette option fonctionne lorsque votre scénario est : « Je souhaite créer ce nombre d’instances ». La [fonction range](bicep-functions-array.md#range) crée un tableau d’entiers qui commence à l’index de début et contient le nombre d’éléments spécifiés. Dans la boucle, vous pouvez utiliser l’index d’entiers pour modifier des valeurs. Pour plus d’informations, consultez [Index d’entiers](#integer-index).

  ```bicep
  [for <index> in range(<startIndex>, <numberOfElements>): {
    ...
  }]
  ```

- En utilisant les **éléments d’un tableau**. Cette option fonctionne quand votre scénario est : « Je souhaite créer une instance pour chaque élément d’un tableau. » Dans la boucle, vous pouvez utiliser la valeur de l’élément de tableau actuel pour modifier des valeurs. Pour plus d’informations, consultez [Éléments de tableau](#array-elements).

  ```bicep
  [for <item> in <collection>: {
    ...
  }]
  ```

- En utilisant les **éléments d’un objet dictionnaire**. Cette option fonctionne quand votre scénario est : « Je souhaite créer une instance pour chaque élément d’un objet ». La [fonction items](bicep-functions-array.md#items) convertit l’objet en tableau. Au sein de la boucle, vous pouvez utiliser des propriétés de l’objet pour créer des valeurs. Pour plus d’informations, consultez [Objet dictionnaire](#dictionary-object).

  ```bicep
  [for <item> in items(<object>): {
    ...
  }]
  ```

- En utilisant un **index d’entiers et les éléments d’un tableau**. Cette option fonctionne quand votre scénario est : « Je souhaite créer une instance pour chaque élément d’un tableau, mais j’ai également besoin de l’index actuel pour créer une autre valeur ». Pour plus d’informations, consultez [Tableau de boucles et index](#array-and-index).

  ```bicep
  [for (<item>, <index>) in <collection>: {
    ...
  }]
  ```

- En ajoutant un **déploiement conditionnel**. Cette option fonctionne quand votre scénario est : « Je souhaite créer plusieurs instances mais, pour chaque instance, je ne souhaite déployer que quand une condition est vraie ». Pour plus d’informations, consultez [Boucle avec condition](#loop-with-condition).

  ```bicep
  [for <item> in <collection>: if(<condition>) {
    ...
  }]
  ```

## <a name="loop-limits"></a>Limites des boucles

L’utilisation de boucles dans Bicep est sujette aux limitations suivantes :

- Le nombre d’itérations de boucle ne peut être ni négatif, ni supérieur à 800.
- Impossible de faire une boucle sur une ressource avec des ressources enfants imbriquées. Remplacez les ressources enfants par les ressources de niveau supérieur.  Voir [Itération d’une ressource enfant](#iteration-for-a-child-resource).
- Impossible de faire une boucle sur plusieurs niveaux de propriétés.

## <a name="integer-index"></a>Index d’entiers

Pour obtenir un exemple simple d’utilisation d’un index, créez une **variable** contenant un tableau de chaînes.

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

L’exemple suivant crée le nombre de comptes de stockage spécifié dans le paramètre `storageCount`. Il retourne trois propriétés pour chaque compte de stockage.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: storageAcct[i].id
  blobEndpoint: storageAcct[i].properties.primaryEndpoints.blob
  status: storageAcct[i].properties.statusOfPrimary
}]
```

Notez que l’index `i` est utilisé pour créer le nom de ressource du compte de stockage.

L’exemple suivant déploie un module plusieurs fois.

```bicep
param location string
param storageCount int = 2

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

## <a name="array-elements"></a>Éléments de tableau

L’exemple suivant crée un compte de stockage par nom fourni dans le paramètre `storageNames`.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

L’exemple suivant effectue une itération sur un tableau pour définir une propriété. Il crée deux sous-réseaux au sein d’un réseau virtuel.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/loops/loopproperty.bicep" highlight="23-28" :::

## <a name="array-and-index"></a>Tableau et index

L’exemple suivant utilise à la fois l’élément de tableau et la valeur d’index lors de la définition du compte de stockage.

```bicep
param storageAccountNamePrefix string

var storageConfigurations = [
  {
    suffix: 'local'
    sku: 'Standard_LRS'
  }
  {
    suffix: 'geo'
    sku: 'Standard_GRS'
  }
]

resource storageAccountResources 'Microsoft.Storage/storageAccounts@2021-02-01' = [for (config, i) in storageConfigurations: {
  name: '${storageAccountNamePrefix}${config.suffix}${i}'
  location: resourceGroup().location
  properties: {
    supportsHttpsTrafficOnly: true
    accessTier: 'Hot'
    encryption: {
      keySource: 'Microsoft.Storage'
      services: {
        blob: {
          enabled: true
        }
        file: {
          enabled: true
        }
      }
    }
  }
  kind: 'StorageV2'
  sku: {
    name: config.sku
  }
}]
```

L’exemple suivant utilise à la fois les éléments d’un tableau et un index pour produire des informations sur les nouvelles ressources.

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

## <a name="dictionary-object"></a>Objet dictionnaire

Pour effectuer une itération sur des éléments d’un objet dictionnaire, utilisez la [fonction items](bicep-functions-array.md#items) qui convertit l’objet en tableau. Utilisez la propriété `value` pour obtenir les propriétés sur les objets.

```bicep
param nsgValues object = {
  nsg1: {
    name: 'nsg-westus1'
    location: 'westus'
  }
  nsg2: {
    name: 'nsg-east1'
    location: 'eastus'
  }
}

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for nsg in items(nsgValues): {
  name: nsg.value.name
  location: nsg.value.location
}]
```

## <a name="loop-with-condition"></a>Boucle avec condition

Pour des **ressources et modules**, vous pouvez ajouter une expression `if` avec la syntaxe de boucle pour déployer la collection de manière conditionnelle.

L’exemple suivant montre une boucle associée à une instruction de condition. Dans cet exemple, une condition unique est appliquée à toutes les instances du module.

```bicep
param location string
param storageCount int = 2
param createNewStorage bool = true

var baseName = 'store${uniqueString(resourceGroup().id)}'

module stgModule './storageAccount.bicep' = [for i in range(0, storageCount): if(createNewStorage) {
  name: '${i}deploy${baseName}'
  params: {
    storageName: '${i}${baseName}'
    location: location
  }
}]
```

L’exemple suivant montre comment appliquer une condition spécifique de l’élément actuel dans le tableau.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

## <a name="deploy-in-batches"></a>Déployer par lots

Par défaut, les ressources Azure sont déployées en parallèle. Lorsque vous utilisez une boucle pour créer plusieurs instances d’un type de ressource, ces instances sont toutes déployées en même temps. L’ordre de création n’est pas garanti. Il n’existe aucune limite au nombre de ressources déployées en parallèle, à l’exception de la limite totale de 800 ressources dans le fichier Bicep.

Il se peut que vous ne souhaitiez pas mettre à jour toutes les instances d’un type de ressource en même temps. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné. Vous pouvez spécifier qu’un sous-ensemble des instances soit traité par lots ensemble et déployé en même temps. Les autres instances attendent que ce lot soit finalisé.

Pour déployer en série des instances d’une ressource, ajoutez l’[élément décoratif BatchSize](./file.md#resource-and-module-decorators). Définissez sa valeur comme le nombre d’instances à déployer simultanément. Une dépendance est créée sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

Pour un déploiement séquentiel, définissez la taille du lot sur 1.

L’élément décoratif `batchSize` se trouve dans l’[espace de noms sys](bicep-functions.md#namespaces-for-functions). Si vous devez différencier cet élément décoratif d'un autre élément portant le même nom, faites précéder l’élément décoratif de **sys**: `@sys.batchSize(2)`

## <a name="iteration-for-a-child-resource"></a>Itération d’une ressource enfant

Vous ne pouvez pas utiliser une boucle pour une ressource enfant imbriquée. Pour créer plusieurs instances d’une ressource enfant, remplacez la ressource enfant par une ressource de niveau supérieur.

Supposons, par exemple, que vous définissiez habituellement un service de fichiers et un partage de fichiers en tant que ressources imbriquées pour un compte de stockage.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

Pour créer plusieurs partages de fichiers, déplacez-le en dehors du compte de stockage. Vous définissez la relation avec la ressource parente par le biais de la propriété `parent`.

L’exemple suivant montre comment créer un compte de stockage, un service de fichiers et plusieurs partages de fichiers :

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="next-steps"></a>Étapes suivantes

- Pour définir des dépendances sur des ressources créées dans une boucle, consultez [Dépendances des ressources](./resource-declaration.md#dependencies).
