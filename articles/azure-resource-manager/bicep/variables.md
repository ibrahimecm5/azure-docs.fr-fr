---
title: Variables dans Bicep
description: Décrit comment définir des variables dans Bicep
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 35175cff08b1470725da87015a3faef9f5bc3ed4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494783"
---
# <a name="variables-in-bicep"></a>Variables dans Bicep

Cet article décrit comment définir et utiliser des variables dans votre fichier Bicep. Vous utilisez des variables pour simplifier le développement de vos fichiers Bicep. Au lieu de répéter des expressions complexes tout au long de votre fichier Bicep, vous définissez une variable qui contient l’expression complexe. Ensuite, vous utilisez cette variable en fonction des besoins dans votre fichier Bicep.

Resource Manager résout les variables avant de démarrer les opérations de déploiement. Chaque fois que la variable est utilisée dans le fichier Bicep, Resource Manager la remplace par la valeur résolue.

## <a name="define-variable"></a>Définir une variable

La syntaxe permettant de définir une variable est la suivante :

```bicep
var <variable-name> = <variable-value>
```

Une variable ne peut pas avoir le même nom qu’un paramètre, un module ou une ressource.

Notez que vous ne spécifiez pas de [type de données](data-types.md) pour la variable. Le type est déduit de la valeur. Dans l’exemple suivant, la valeur d’une variable est définie sur une chaîne.

```bicep
var stringVar = 'example value'
```

Vous pouvez utiliser la valeur d’un paramètre ou d’une autre variable lors de la construction de la variable.

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'preset variable'
var concatToVar =  '${stringVar}AddToVar'
var concatToParam = '${inputValue}AddToParam'

output addToVar string = concatToVar
output addToParam string = concatToParam
```

L’exemple précédent renvoie :

```json
{
  "addToParam": {
    "type": "String",
    "value": "deployment parameterAddToParam"
  },
  "addToVar": {
    "type": "String",
    "value": "preset variableAddToVar"
  }
}
```

Vous pouvez utiliser les [fonctions Bicep](bicep-functions.md) pour construire la valeur de la variable. L’exemple suivant utilise des fonctions Bicep pour créer une valeur de chaîne pour un nom de compte de stockage.

```bicep
param storageNamePrefix string = 'stg'
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

output uniqueStorageName string = storageName
```

L’exemple suivant retourne une valeur telle que la suivante :

```json
"uniqueStorageName": {
  "type": "String",
  "value": "stghzuunrvapn6sw"
}
```

Vous pouvez utiliser des boucles itératives lors de la définition d’une variable. L’exemple suivant crée un tableau d’objets avec trois propriétés.

```bicep
param itemCount int = 3

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

La sortie retourne un tableau avec les valeurs suivantes :

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Pour plus d’informations sur les types de boucles que vous pouvez utiliser avec des variables, consultez [Boucles itératives dans Bicep](loops.md).

## <a name="use-variable"></a>Utiliser une variable

L’exemple suivant montre comment utiliser la variable pour une propriété de ressource. Vous faites référence à la valeur de la variable en indiquant le nom de la variable : `storageName`.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variableswithfunction.bicep" highlight="4,7,15" :::

Étant donné que les noms de compte de stockage doivent utiliser des minuscules, la variable `storageName` utilise la fonction `toLower` pour mettre la valeur `storageNamePrefix` en minuscules. La fonction `uniqueString` crée une valeur unique à partir de l’ID du groupe de ressources. Les valeurs sont concaténées en une chaîne.

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez définir des variables qui contiennent des valeurs associées pour la configuration d’un environnement. Vous définissez la variable en tant qu’objet avec les valeurs. L’exemple suivant illustre un objet qui contient des valeurs pour deux environnements : **test** et **prod**. Transmettez l’une de ces valeurs au cours du déploiement.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/variables/variablesconfigurations.bicep":::

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les propriétés disponibles pour les variables, consultez [Présentation de la structure et de la syntaxe des fichiers Bicep](file.md).
- Pour en savoir plus sur l’utilisation de la syntaxe de boucle, consultez [Boucles itératives dans Bicep](loops.md).