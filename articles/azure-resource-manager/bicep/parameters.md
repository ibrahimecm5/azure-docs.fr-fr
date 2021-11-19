---
title: Paramètres des fichiers Bicep
description: Explique comment définir des paramètres dans un fichier Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4345269d9c1191545a28998a38aeedb14b37e0bc
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486614"
---
# <a name="parameters-in-bicep"></a>Paramètres dans Bicep

Cet article explique comment définir et utiliser des paramètres dans un fichier Bicep. En fournissant des valeurs différentes pour les paramètres, vous pouvez réutiliser un fichier Bicep pour différents environnements.

Resource Manager résout les valeurs des paramètres avant de démarrer les opérations de déploiement. Chaque fois que le paramètre est utilisé, Resource Manager le remplace par la valeur résolue.

Chaque paramètre doit être défini sur l’un des [types de données](data-types.md).

### <a name="microsoft-learn"></a>Microsoft Learn

Pour en savoir plus sur les paramètres et pour obtenir des conseils pratiques, consultez [Créer des modèles Bicep réutilisables en utilisant des paramètres](/learn/modules/build-reusable-bicep-templates-parameters) sur **Microsoft Learn**.

## <a name="declaration"></a>Déclaration

Chaque paramètre a un nom et un [type de données](data-types.md). Si vous le souhaitez, vous pouvez attribuer une valeur par défaut à un paramètre.

```bicep
param <parameter-name> <parameter-data-type> = <default-value>
```

Un paramètre ne peut pas avoir le même nom qu’une variable, qu’une ressource, qu’une sortie ou qu’un autre paramètre dans la même étendue.

L’exemple suivant montre des déclarations de base de paramètres.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="default-value"></a>Valeur par défaut

Vous pouvez spécifier une valeur par défaut pour un paramètre. La valeur par défaut est utilisée quand aucune valeur n’est fournie pendant le déploiement.

```bicep
param demoParam string = 'Contoso'
```

Vous pouvez utiliser des expressions avec la valeur par défaut. Les expressions ne sont pas autorisées avec d’autres propriétés de paramètre. Vous ne pouvez pas utiliser la fonction [reference](bicep-functions-resource.md#reference) ni aucune des fonctions [list](bicep-functions-resource.md#list) dans la section parameters. Ces fonctions obtiennent l’état d’exécution d’une ressource et ne peuvent pas être exécutées avant le déploiement quand des paramètres sont résolus.

```bicep
param location string = resourceGroup().location
```

Vous pouvez utiliser une autre valeur de paramètre pour générer une valeur par défaut. Le modèle suivant construit un nom de plan d’hôte à partir du nom de site.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterswithfunctions.bicep" highlight="2":::

## <a name="decorators"></a>Décorateurs

Les paramètres utilisent des décorateurs pour les contraintes ou les métadonnées. Les décorateurs sont au format `@expression` et sont placés au-dessus de la déclaration du paramètre. Vous pouvez marquer un paramètre comme sécurisé, spécifier des valeurs autorisées, définir la longueur minimale et maximale d’une chaîne, définir la valeur minimale et maximale d’un entier et fournir une description du paramètre.

L’exemple suivant montre deux utilisations courantes des éléments décoratifs.

```bicep
@secure()
param demoPassword string

@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

Le tableau suivant décrit les éléments décoratifs disponibles et leur utilisation.

| Élément décoratif | S’applique à | Argument | Description |
| --------- | ---- | ----------- | ------- |
| [autorisé](#allowed-values) | all | tableau | Valeurs autorisées pour le paramètre. Utilisez cet élément décoratif pour vérifier que l’utilisateur fournit des valeurs correctes. |
| [description](#description) | all | string | Texte qui explique comment utiliser le paramètre. La description apparaît aux utilisateurs dans le portail. |
| [maxLength](#length-constraints) | array, string | int | Longueur maximale des paramètres de type chaîne et tableau. La valeur est inclusive. |
| [maxValue](#integer-constraints) | int | int | Valeur maximale du paramètre de type entier. Cette valeur est inclusive. |
| metadata | all | object | Propriétés personnalisées à appliquer au paramètre. Peut inclure une propriété Description qui est équivalente à l’élément décoratif de description. |
| [minLength](#length-constraints) | array, string | int | Longueur minimale des paramètres de type chaîne et tableau. La valeur est inclusive. |
| [minValue](#integer-constraints) | int | int | Valeur minimale du paramètre de type entier. Cette valeur est inclusive. |
| [secure](#secure-parameters) | string, object | Aucun | Marque le paramètre comme sécurisé. La valeur d’un paramètre sécurisé n’est pas enregistrée dans l’historique de déploiement et n’est pas journalisée. Pour plus d’informations, consultez [Sécuriser les chaînes et les objets](data-types.md#secure-strings-and-objects). |

Les éléments décoratifs se trouvent dans l’[espace de noms sys](bicep-functions.md#namespaces-for-functions). Si vous devez différencier un élément décoratif d'un autre élément portant le même nom, faites précéder l’élément décoratif de `sys`. Par exemple, si votre fichier Bicep contient un paramètre nommé `description`, vous devez ajouter l’espace de noms sys lors de l’utilisation de l’élément décoratif **description**.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

Les éléments décoratifs disponibles sont décrits dans les sections suivantes.

### <a name="secure-parameters"></a>Paramètres sécurisés

Vous pouvez marquer les paramètres de chaîne ou d’objet comme sécurisés. La valeur d’un paramètre sécurisé n’est pas enregistrée dans l’historique de déploiement et n’est pas journalisée.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

### <a name="allowed-values"></a>Valeurs autorisées

Vous pouvez définir des valeurs autorisées pour un paramètre. Vous fournissez les valeurs autorisées dans un tableau. Le déploiement échoue pendant la validation si une valeur transmise pour le paramètre n’est pas l’une des valeurs autorisées.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

### <a name="length-constraints"></a>Contraintes de longueur

Vous pouvez spécifier des longueurs minimale et maximale pour les paramètres de chaîne et de tableau. Vous pouvez définir une contrainte ou les deux. Pour les chaînes, la longueur indique le nombre de caractères. Pour les tableaux, la longueur indique le nombre d’éléments dans le tableau.

L’exemple suivant déclare deux paramètres. Un paramètre est destiné à un nom de compte de stockage qui doit compter entre 3 et 24 caractères. L’autre paramètre est un tableau qui doit compter entre 1 et 5.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

### <a name="integer-constraints"></a>Contraintes d’entier

Vous pouvez définir des valeurs minimales et maximales pour les paramètres de type entier. Vous pouvez définir une contrainte ou les deux.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

### <a name="description"></a>Description

Pour aider les utilisateurs à comprendre la valeur qu’ils doivent fournir, ajoutez une description au paramètre. Lorsque vous déployez le modèle par le biais du portail, le texte de la description est automatiquement utilisé comme une info-bulle pour ce paramètre. Ajoutez une description uniquement quand le texte fournit des informations en plus de celles qui peuvent être déduites du nom du paramètre.

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>Utiliser un paramètre

Pour référencer la valeur d’un paramètre, utilisez le nom du paramètre. L’exemple suivant utilise une valeur de paramètre comme nom de coffre de clés.

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>Objets en tant que paramètres

Il peut s’avérer plus facile d’organiser des valeurs connexes en les transmettant en tant qu’objets. Cette approche réduit également le nombre de paramètres dans le modèle.

L’exemple suivant illustre un paramètre qui est un objet. La valeur par défaut affiche les propriétés attendues pour l’objet. Ces propriétés sont utilisées lors de la définition de la ressource à déployer.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/parameters/parameterobject.bicep":::


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les propriétés qui sont disponibles pour les paramètres, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
- Pour en savoir plus sur le passage de valeurs de paramètre sous forme de fichier, consultez [Créer un fichier de paramètres Bicep](parameter-files.md).
