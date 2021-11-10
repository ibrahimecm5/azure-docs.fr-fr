---
title: Structure et syntaxe des fichiers Bicep
description: Décrit la structure et les propriétés d’un fichier Bicep en utilisant une syntaxe déclarative.
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: f890f625abc4a6839cd090a49aa7e641ecef9145
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892018"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Comprendre la structure et la syntaxe des fichiers Bicep

Cet article décrit la structure d’un fichier Bicep. Il présente les différentes sections du fichier et les propriétés disponibles dans ces sections.

Cet article est destiné aux utilisateurs qui possèdent des connaissances sur les fichiers Bicep. Cela fournit des informations détaillées sur la structure du fichier Bicep. Pour obtenir un didacticiel pas à pas qui vous guide tout au long du processus de création d’un fichier Bicep, consultez [Démarrage rapide : créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Format Bicep

Un fichier Bicep contient les éléments suivants. Bicep est un langage déclaratif, ce qui signifie que les éléments peuvent apparaître dans n’importe quel ordre.  Contrairement aux langages impératifs, l’ordre des éléments n’a pas d’incidence sur le mode de traitement du déploiement.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
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

Par exemple, vous pouvez ajouter un paramètre SKU pour spécifier différentes tailles pour une ressource. Vous pouvez utiliser des fonctions Bicep pour créer la valeur par défaut, par exemple obtenir l’emplacement du groupe de ressources.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Pour connaître les types de données disponibles, consultez [Types de données dans Bicep](data-types.md).

Un paramètre ne peut pas avoir le même nom qu’une variable, un module ou une ressource.

Pour plus d’informations, consultez [Paramètres dans Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Éléments décoratifs de paramètres

Vous pouvez ajouter un ou plusieurs éléments décoratifs pour chaque paramètre. Ces éléments décoratifs définissent les valeurs autorisées pour le paramètre. L’exemple suivant spécifie les SKU qui peuvent être déployés par le biais du fichier Bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Le tableau suivant décrit les éléments décoratifs disponibles et leur utilisation.

| Élément décoratif | S’applique à | Argument | Description |
| --------- | ---- | ----------- | ------- |
| autorisé | all | tableau | Valeurs autorisées pour le paramètre. Utilisez cet élément décoratif pour vérifier que l’utilisateur fournit des valeurs correctes. |
| description | all | string | Texte qui explique comment utiliser le paramètre. La description apparaît aux utilisateurs dans le portail. |
| maxLength | array, string | int | Longueur maximale des paramètres de type chaîne et tableau. La valeur est inclusive. |
| maxValue | int | int | Valeur maximale du paramètre de type entier. Cette valeur est inclusive. |
| metadata | all | object | Propriétés personnalisées à appliquer au paramètre. Peut inclure une propriété Description qui est équivalente à l’élément décoratif de description. |
| minLength | array, string | int | Longueur minimale des paramètres de type chaîne et tableau. La valeur est inclusive. |
| minValue | int | int | Valeur minimale du paramètre de type entier. Cette valeur est inclusive. |
| secure | string, object | Aucun | Marque le paramètre comme sécurisé. La valeur d’un paramètre sécurisé n’est pas enregistrée dans l’historique de déploiement et n’est pas journalisée. Pour plus d’informations, consultez [Sécuriser les chaînes et les objets](data-types.md#secure-strings-and-objects). |

Les éléments décoratifs se trouvent dans l’[espace de noms sys](bicep-functions.md#namespaces-for-functions). Si vous devez différencier un élément décoratif d'un autre élément portant le même nom, faites précéder l’élément décoratif de `sys`. Par exemple, si votre fichier Bicep contient un paramètre nommé `description`, vous devez ajouter l’espace de noms sys lors de l’utilisation de l’élément décoratif **description**.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

Pour plus d’informations, consultez [Éléments décoratifs](parameters.md#decorators).

## <a name="variables"></a>Variables

Utilisez des variables pour les expressions complexes qui sont répétées dans un fichier Bicep. Par exemple, vous pouvez ajouter une variable pour un nom de ressource construit en concaténant plusieurs valeurs ensemble.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Vous ne spécifiez pas de [type de données](data-types.md) pour une variable. Au lieu de cela, le type de données est déduit de la valeur.

Une variable ne peut pas avoir le même nom qu’un paramètre, un module ou une ressource.

Pour plus d’informations, consultez [Variables dans Bicep](./variables.md).

## <a name="resource"></a>Ressource

Utilisez le mot clé `resource` pour définir une ressource à déployer. Votre déclaration de ressource comprend un nom symbolique pour la ressource. Vous utiliserez ce nom symbolique dans d’autres parties du fichier Bicep si vous devez obtenir une valeur de la ressource. Les noms symboliques respectent la casse. Ils peuvent contenir des lettres, des chiffres et _ ; mais ils ne peuvent pas commencer par un chiffre.

La déclaration de ressource comprend également le type de ressource et la version de l’API.

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

Dans votre déclaration de ressource, vous incluez des propriétés pour le type de ressource. Ces propriétés sont uniques à chaque type de ressource.

Pour plus d’informations, consultez [Déclaration de ressource dans Bicep](resource-declaration.md).

Pour [déployer une ressource de manière conditionnelle](conditional-resource-deployment.md), ajoutez une expression `if`.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

Pour [déployer plusieurs instances](loops.md) d’un type de ressource, ajoutez une expression `for`. L’expression peut itérer sur les membres d’un tableau.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

Une ressource ne peut pas avoir le même nom qu’un paramètre, un module ou une variable.

## <a name="modules"></a>Modules

Utilisez des modules pour créer un lien vers d’autres fichiers Bicep contenant du code que vous souhaitez réutiliser. Le module contient une ou plusieurs ressources à déployer. Ces ressources sont déployées en même temps que d’autres ressources dans votre fichier Bicep.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Le nom symbolique vous permet de référencer le module à partir d’un autre emplacement dans le fichier. Par exemple, vous pouvez obtenir une valeur de sortie à partir d’un module en utilisant le nom symbolique et le nom de la valeur de sortie. Le nom symbolique peut contenir a-z, A-Z, 0-9 et « _ » ; le nom ne peut pas commencer par un chiffre.

Un module ne peut pas avoir le même nom qu’un paramètre, un module ou une ressource.

À l’instar des ressources, vous pouvez déployer un module de manière conditionnelle ou itérative. La syntaxe est la même pour les modules que pour les ressources.

Pour plus d’informations, consultez [Utiliser des modules Bicep](./modules.md).

## <a name="resource-and-module-decorators"></a>Éléments décoratifs de ressources et de modules

Vous pouvez ajouter un élément décoratif à une définition de ressource ou de module. Le seul élément décoratif pris en charge est `batchSize(int)`. Vous pouvez uniquement l’appliquer à une définition de ressource ou de module qui utilise une expression `for`.

Par défaut, les ressources sont déployées en parallèle. Vous ne connaissez pas l’ordre dans lequel elles se terminent. Lorsque vous ajoutez l’élément décoratif `batchSize`, vous déployez des instances en série. Utilisez l’argument « integer » pour spécifier le nombre d’instances à déployer en parallèle.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

L’élément décoratif `batchSize` se trouve dans l’[espace de noms sys](bicep-functions.md#namespaces-for-functions). Si vous devez différencier cet élément décoratif d'un autre élément portant le même nom, faites précéder l’élément décoratif de **sys**: `@sys.batchSize(2)`

Pour plus d’informations, consultez [Déployer par lots](loops.md#deploy-in-batches).

## <a name="outputs"></a>Sorties

Utilisez des sorties pour renvoyer une valeur à partir du déploiement. En règle générale, vous renvoyez une valeur d’une ressource déployée lorsque vous devez réutiliser cette valeur pour une autre opération.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Spécifiez un [type de données](data-types.md) pour la valeur de sortie.

Une sortie peut avoir le même nom qu’un paramètre, une variable, un module ou une ressource.

Pour plus d’informations, consultez [Sorties dans Bicep](./outputs.md).

## <a name="whitespace"></a>Espace blanc

Les espaces et les tabulations sont ignorés lors de la création de fichiers Bicep. Les nouvelles lignes ont toutefois une signification sémantique ; par exemple, dans les déclarations [object](./data-types.md#objects) et [array](./data-types.md#arrays).

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

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation de Bicep, consultez [Qu’est-ce que Bicep ?](./overview.md) Pour les types de données Bicep, consultez [Types de données](./data-types.md).
