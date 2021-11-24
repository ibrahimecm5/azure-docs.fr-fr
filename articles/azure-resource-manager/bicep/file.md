---
title: Structure et syntaxe des fichiers Bicep
description: Décrit la structure et les propriétés d’un fichier Bicep en utilisant une syntaxe déclarative.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 352ff708b9b36eff06be8f3a3dda10b28b02e37b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493980"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Comprendre la structure et la syntaxe des fichiers Bicep

Cet article décrit la structure et la syntaxe d’un fichier Bicep. Il présente les différentes sections du fichier et les propriétés disponibles dans ces sections.

Pour obtenir un didacticiel pas à pas qui vous guide tout au long du processus de création d’un fichier Bicep, consultez [Démarrage rapide : créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Format Bicep

Bicep est un langage déclaratif, ce qui signifie que les éléments peuvent apparaître dans n’importe quel ordre. Contrairement aux langages impératifs, l’ordre des éléments n’a pas d’incidence sur le mode de traitement du déploiement.

Un fichier Bicep contient les éléments suivants.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>
```

L’exemple suivant montre une implémentation de ces éléments.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Étendue cible

Par défaut, l’étendue cible est définie sur `resourceGroup`. Si vous effectuez un déploiement au niveau du groupe de ressources, vous n’avez pas besoin de définir l’étendue cible dans votre fichier Bicep.

Les valeurs autorisées sont les suivantes :

* **resourceGroup** : valeur par défaut, utilisée pour les [déploiements de groupes de ressources](deploy-to-resource-group.md).
* **subscription** : utilisée pour les [déploiements d’abonnements](deploy-to-subscription.md).
* **managementGroup** : utilisée pour les [déploiements de groupes d’administration](deploy-to-management-group.md).
* **tenant** : utilisée pour les [déploiements de locataires](deploy-to-tenant.md).

Dans un module, vous pouvez spécifier une étendue différente de celle du reste du fichier Bicep. Pour plus d'informations, consultez [Configurer l’étendue du module](modules.md#set-module-scope)

## <a name="parameters"></a>Paramètres

Utilisez des paramètres pour les valeurs qui doivent varier en fonction des différents déploiements. Vous pouvez définir une valeur par défaut pour le paramètre qui sera utilisé si aucune valeur n’est fournie pendant le déploiement.

Par exemple, vous pouvez ajouter un paramètre SKU pour spécifier différentes tailles pour une ressource. Vous pouvez transmettre des valeurs différentes selon que vous effectuez le déploiement en test ou en production.

```bicep
param storageSKU string = 'Standard_LRS'
```

Le paramètre peut être utilisé dans votre fichier Bicep.

```bicep
sku: {
  name: storageSKU
}
```

Pour plus d’informations, consultez [Paramètres dans Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Éléments décoratifs de paramètres

Vous pouvez ajouter un ou plusieurs éléments décoratifs pour chaque paramètre. Ces décorateurs décrivent le paramètre et définissent des contraintes pour les valeurs qui sont passées. L’exemple suivant montre un décorateur, mais il en existe de nombreux autres disponibles.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Pour plus d’informations, y compris les descriptions de tous les décorateurs disponibles, consultez [Décorateurs](parameters.md#decorators).

## <a name="variables"></a>Variables

Vous pouvez rendre votre fichier Bicep plus lisible en encapsulant des expressions complexes dans une variable. Par exemple, vous pouvez ajouter une variable pour un nom de ressource construit en concaténant plusieurs valeurs ensemble.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Appliquez cette variable partout où vous avez besoin de l’expression complexe.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
```

Pour plus d’informations, consultez [Variables dans Bicep](./variables.md).

## <a name="resource"></a>Ressource

Utilisez le mot clé `resource` pour définir une ressource à déployer. Votre déclaration de ressource comprend un nom symbolique pour la ressource. Vous utiliserez ce nom symbolique dans d’autres parties du fichier Bicep pour obtenir une valeur de la ressource.

La déclaration de ressource comprend le type de ressource et la version de l’API. Dans le corps de la déclaration de ressource, incluez les propriétés spécifiques au type de ressource.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}
```

Pour plus d’informations, consultez [Déclaration de ressource dans Bicep](resource-declaration.md).

## <a name="modules"></a>Modules

Les modules vous permettent de réutiliser du code à partir d’un fichier Bicep dans d’autres fichiers Bicep. Dans la déclaration de module, vous pouvez créer un lien vers le fichier à réutiliser. Lorsque vous déployez le fichier Bicep, les ressources du module sont également déployées.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Le nom symbolique vous permet de référencer le module à partir d’un autre emplacement dans le fichier. Par exemple, vous pouvez obtenir une valeur de sortie à partir d’un module en utilisant le nom symbolique et le nom de la valeur de sortie.

Pour plus d’informations, consultez [Utiliser des modules Bicep](./modules.md).

## <a name="resource-and-module-decorators"></a>Éléments décoratifs de ressources et de modules

Vous pouvez ajouter un élément décoratif à une définition de ressource ou de module. Le seul élément décoratif pris en charge est `batchSize(int)`. Vous pouvez uniquement l’appliquer à une définition de ressource ou de module qui utilise une expression `for`.

Par défaut, les ressources sont déployées en parallèle. Lorsque vous ajoutez l’élément décoratif `batchSize`, vous déployez des instances en série.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Pour plus d’informations, consultez [Déployer par lots](loops.md#deploy-in-batches).

## <a name="outputs"></a>Sorties

Utilisez des sorties pour renvoyer des valeurs à partir du déploiement. En règle générale, vous renvoyez une valeur d’une ressource déployée lorsque vous devez réutiliser cette valeur pour une autre opération.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Pour plus d’informations, consultez [Sorties dans Bicep](./outputs.md).

## <a name="loops"></a>Boucles

Vous pouvez ajouter des boucles itératives à votre fichier Bicep pour définir plusieurs copies d’un :

* resource
* module
* variable
* propriété
* sortie

Utilisez l’expression `for` pour définir une boucle.

```bicep
param moduleCount int = 2

module stgModule './example.bicep' = [for i in range(0, moduleCount): {
  name: '${i}deployModule'
  params: {
  }
}]
```

Vous pouvez effectuer une itération sur un tableau, un objet ou un index d’entiers.

Pour plus d’informations, consultez [Boucles itératives dans Bicep](loops.md).

## <a name="conditional-deployment"></a>Déploiement conditionnel

Vous pouvez ajouter une ressource ou un module à votre fichier Bicep qui est déployé de manière conditionnelle. Pendant le déploiement, la condition est évaluée et le résultat détermine si la ressource ou le module est déployé. Utilisez l’expression `if` pour définir un déploiement conditionnel.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Pour plus d’informations, consultez [Déploiement conditionnel dans Bicep](conditional-resource-deployment.md).

## <a name="whitespace"></a>Espace blanc

Les espaces et les tabulations sont ignorés lors de la création de fichiers Bicep.

Bicep est sensible aux sauts de ligne. Par exemple :

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  ...
}
```

Ne peut pas être écrit sous la forme :

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
    if (newOrExisting == 'new') {
      ...
    }
```

Définir des [objets](./data-types.md#objects) et des [tableaux](./data-types.md#arrays) sur plusieurs lignes.

## <a name="comments"></a>Commentaires

Utilisez `//` pour les commentaires d’une seule ligne ou `/* ... */` pour les commentaires de plusieurs lignes.

L’exemple suivant montre un commentaire d’une seule ligne.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

L’exemple suivant montre un commentaire de plusieurs lignes.

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Chaînes à lignes multiples

Vous pouvez scinder une chaîne en plusieurs lignes. Utilisez trois caractères guillemets simples `'''` pour commencer et terminer la chaîne à plusieurs lignes.

Les caractères de la chaîne à plusieurs lignes sont traités tels quels. Les caractères d’échappement ne sont pas nécessaires. Vous ne pouvez pas inclure `'''` dans la chaîne à plusieurs lignes. L’interpolation de chaîne n’est pas prise en charge actuellement.

Vous pouvez démarrer votre chaîne juste après l’ouverture `'''` ou inclure une nouvelle ligne. Dans les deux cas, la chaîne résultante n’inclut pas de nouvelle ligne. Selon les fins de ligne dans votre fichier Bicep, les nouvelles lignes sont interprétées comme `\r\n` ou `\n`.

L’exemple suivant montre une chaîne à plusieurs lignes.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

L’exemple précédent équivaut au code JSON suivant.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="known-limitations"></a>Limitations connues

- Aucun support pour le concept d’apiProfile utilisé pour mapper un apiProfile à une apiVersion définie pour chaque type de ressource.
- Aucun support pour les fonctions définies par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation de Bicep, consultez [Qu’est-ce que Bicep ?](./overview.md) Pour les types de données Bicep, consultez [Types de données](./data-types.md).
