---
title: Fonctions Bicep – chaîne
description: Décrit les fonctions à utiliser dans un fichier Bicep pour travailler avec des chaînes.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: a73df839ff7dcaad992b8930c8fb2545e854951c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028075"
---
# <a name="string-functions-for-bicep"></a>Fonctions de chaîne pour Bicep

Cet article décrit les fonctions Bicep pour l’utilisation de chaînes.

## <a name="base64"></a>base64

`base64(inputString)`

Retourne la représentation en base 64 de la chaîne d'entrée.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |Oui |string |La valeur à retourner sous la forme d’une représentation en base64. |

### <a name="return-value"></a>Valeur retournée

Une chaîne contenant la représentation en base64.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser la fonction base64.

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Convertit une représentation en base64 en un objet JSON.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Oui |string |La représentation en base64 à convertir en un objet JSON. |

### <a name="return-value"></a>Valeur retournée

Un objet JSON.

### <a name="examples"></a>Exemples

L’exemple suivant utilise la fonction base64ToJson pour convertir une valeur base64 :

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Convertit une représentation en base64 en une chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Oui |string |La représentation en base64 à convertir en une chaîne. |

### <a name="return-value"></a>Valeur retournée

Une chaîne de la valeur base64 convertie.

### <a name="examples"></a>Exemples

L’exemple suivant utilise la fonction base64ToString pour convertir une valeur base64 :

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

Au lieu d’utiliser la fonction concat, utilisez une interpolation de chaîne.

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}And${uniqueString(resourceGroup().id)}'
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| concatOutput | String | prefixAnd5yj4yjf5mbg72 |

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Vérifie si un tableau contient une valeur, un objet contient une clé ou une chaîne contient une sous-chaîne. La comparaison de chaînes est sensible à la casse. Cependant, quand vous testez si un objet contient une clé, la comparaison n’est pas sensible à la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| conteneur |Oui |tableau, objet ou chaîne |La valeur qui contient la valeur à rechercher. |
| itemToFind |Oui |chaîne ou entier |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

**True** si l’élément est trouvé ; sinon, **False**.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser contains avec différents types :

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Convertit une valeur en un URI de données.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Oui |string |Valeur à convertir en URI de données. |

### <a name="return-value"></a>Valeur retournée

Une chaîne formatée en tant qu’URI de données.

### <a name="examples"></a>Exemples

L’exemple suivant convertit une valeur en un URI de données et convertit un URI de données en chaîne :

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| dataUriOutput | String | data: texte/brut;jeu de caractèresdata:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Convertit une valeur formatée en URI de données en chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Oui |string |Valeur d’URI de données à convertir. |

### <a name="return-value"></a>Valeur retournée

Chaîne contenant la valeur convertie.

### <a name="examples"></a>Exemples

L’exemple suivant convertit une valeur en un URI de données et convertit un URI de données en chaîne :

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| dataUriOutput | String | data: texte/brut;jeu de caractèresdata:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Détermine si un tableau, un objet ou une chaîne est vide.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Oui |tableau, objet ou chaîne |Valeur à vérifier pour voir si elle est vide. |

### <a name="return-value"></a>Valeur retournée

Retourne **True** si la valeur est vide ; sinon, **False**.

### <a name="examples"></a>Exemples

L’exemple suivant vérifie si un tableau, un objet et une chaîne sont vides.

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Détermine si une chaîne se termine par une valeur. La comparaison respecte la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

**True** si le ou les derniers caractères de la chaîne correspondent à la valeur ; sinon, **False**.

### <a name="examples"></a>Exemples

L'exemple suivant montre comment utiliser les fonctions startsWith et endsWith :

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Retourne le premier caractère de la chaîne ou le premier élément du tableau.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le premier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Chaîne du premier caractère ou type (chaîne, entier, tableau ou objet) du premier élément d’un tableau.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser la première fonction avec un tableau et une chaîne.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Crée une chaîne mise en forme à partir des valeurs d’entrée.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| formatString | Oui | string | Chaîne de format composite. |
| arg1 | Oui | chaîne, entier ou valeur booléenne | Valeur à inclure dans la chaîne mise en forme. |
| arguments supplémentaires | Non | chaîne, entier ou valeur booléenne | Valeurs supplémentaires à inclure dans la chaîne mise en forme. |

### <a name="remarks"></a>Notes

Utilisez cette fonction pour mettre en forme une chaîne dans votre fichier Bicep. Cette fonction utilise les mêmes options de mise en forme que la méthode [System.String.Format](/dotnet/api/system.string.format) dans .NET.

### <a name="examples"></a>Exemples

L’exemple suivant explique comment utiliser la fonction format.

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| formatTest | String | Bonjour. Nombre mis en forme : 8 175 133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Crée une valeur sous la forme d’un identificateur global unique basé sur les valeurs fournies comme paramètres.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Oui |string |Valeur utilisée dans la fonction de hachage pour créer le GUID. |
| paramètres supplémentaires le cas échéant |Non |string |Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité. |

### <a name="remarks"></a>Notes

Cette fonction est utile quand vous devez créer une valeur sous la forme d’un identificateur global unique. Vous fournissez des valeurs de paramètre qui limitent l’étendue d’unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour l’abonnement, le groupe de ressources ou le déploiement.

La valeur retournée n’est pas une chaîne aléatoire, mais le résultat d’une fonction de hachage exécutée sur les paramètres. La valeur renvoyée comprend 36 caractères. Elle n’est pas globalement unique. Pour créer un autre GUID non basé sur cette valeur de hachage des paramètres, utilisez la fonction [newGuid](#newguid).

Les exemples suivants montrent comment utiliser guid pour créer une valeur unique pour des niveaux couramment utilisés.

Unique limité à l’abonnement

```bicep
guid(subscription().subscriptionId)
```

Unique limité au groupe de ressources

```bicep
guid(resourceGroup().id)
```

Unique limité au déploiement pour un groupe de ressources

```bicep
guid(resourceGroup().id, deployment().name)
```

### <a name="return-value"></a>Valeur retournée

Chaîne contenant 36 caractères sous la forme d’un identificateur global unique.

> [!NOTE]
> Importance de l’ordre : les paramètres doivent être non seulement identiques, mais aussi dans le même ordre. Par exemple : `guid('hello', 'world') != guid('world', 'hello')`

### <a name="examples"></a>Exemples

L’exemple suivant retourne les résultats du guid :

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Retourne la première position d’une valeur dans une chaîne. La comparaison respecte la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

Entier qui représente la position de l’élément à rechercher. La valeur est basée sur zéro. Si l’élément est introuvable, la valeur -1 est retournée.

### <a name="examples"></a>Exemples

L'exemple suivant montre comment utiliser les fonctions indexOf et lastIndexOf :

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Convertit une chaîne JSON valide en un type de données JSON. Pour plus d’informations, consultez la [fonction json](./bicep-functions-object.md#json).

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

## <a name="last"></a>last

`last (arg1)`

Retourne le dernier caractère de la chaîne ou le dernier élément du tableau.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le dernier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Chaîne du dernier caractère ou type (chaîne, entier, tableau ou objet) du dernier élément d’un tableau.

### <a name="examples"></a>Exemples

L’exemple suivant indique comment utiliser la dernière fonction avec un tableau et une chaîne.

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Retourne la dernière position d’une valeur dans une chaîne. La comparaison respecte la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

Entier qui représente la dernière position de l’élément à rechercher. La valeur est basée sur zéro. Si l’élément est introuvable, la valeur -1 est retournée.

### <a name="examples"></a>Exemples

L'exemple suivant montre comment utiliser les fonctions indexOf et lastIndexOf :

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Retourne le nombre d’éléments d’un tableau, les caractères d’une chaîne ou les propriétés au niveau de la racine d’un objet.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau, chaîne ou objet |Tableau à utiliser pour l’obtention du nombre d’éléments, ou chaîne à utiliser pour l’obtention du nombre de caractères, ou l’objet à utiliser pour l’obtention du nombre de propriétés au niveau de la racine. |

### <a name="return-value"></a>Valeur retournée

Un entier.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser la longueur avec un tableau et une chaîne :

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Retourne une valeur sous la forme d’un identificateur global unique. **Cette fonction peut uniquement être utilisée dans la valeur par défaut d’un paramètre.**

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Notes

Vous pouvez uniquement utiliser cette fonction dans une expression pour la valeur par défaut d’un paramètre. Son utilisation partout ailleurs dans un fichier Bicep retourne une erreur. La fonction n’est pas autorisée dans d’autres parties du fichier Bicep, car elle retourne une valeur différente chaque fois qu’elle est appelée. Le déploiement du même fichier Bicep avec les mêmes paramètres ne produit pas forcément les mêmes résultats.

La fonction newGuid diffère de la fonction [guid](#guid), car elle ne prend aucun paramètre. Quand vous appelez la fonction guid avec les mêmes paramètres, elle retourne toujours le même identificateur. Utilisez guid quand vous devez générer invariablement le même GUID dans un environnement spécifique. Utilisez newGuid pour générer un identificateur différent chaque fois, par exemple pour le déploiement de ressources dans un environnement de test.

La fonction newGuid utilise la [structure Guid](/dotnet/api/system.guid) dans le .NET Framework pour générer l’identificateur global unique.

Si vous choisissez l’[option de redéployer un déploiement précédent réussi](../templates/rollback-on-error.md) et que ce déploiement inclut un paramètre qui utilise newGuid, le paramètre n’est pas réévalué. Au lieu de cela, la valeur du paramètre du déploiement précédent est automatiquement réutilisée dans le déploiement de la restauration.

Dans un environnement de test, vous devrez peut-être déployer à plusieurs reprises des ressources utilisables uniquement pendant une courte période. Au lieu de construire des noms uniques, vous pouvez utiliser newGuid avec [uniqueString](#uniquestring) pour créer des noms uniques.

Soyez prudent quand vous redéployez un fichier Bicep basé sur la fonction newGuid pour une valeur par défaut. Si vous effectuez un tel déploiement sans fournir de valeur pour le paramètre, la fonction est réévaluée. Si vous souhaitez mettre à jour une ressource existante au lieu d’en créer une autre, passez la valeur du paramètre qui était utilisée dans le déploiement précédent.

### <a name="return-value"></a>Valeur retournée

Chaîne contenant 36 caractères sous la forme d’un identificateur global unique.

### <a name="examples"></a>Exemples

L’exemple suivant montre un paramètre avec un nouvel identificateur.

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

La sortie de l’exemple précédent varie pour chaque déploiement, mais elle sera semblable à celle-ci :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

L’exemple suivant utilise la fonction newGuid pour créer un nom unique de compte de stockage. Ce fichier Bicep peut convenir dans un environnement de test où le compte de stockage est utilisé pendant une courte période et n’est pas redéployé.

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

La sortie de l’exemple précédent varie pour chaque déploiement, mais elle sera semblable à celle-ci :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| valeur_à_remplir |Oui |chaîne ou entier |Valeur à aligner à droite. |
| longueur_totale |Oui |int |Nombre total de caractères de la chaîne renvoyée. |
| caractère_de_remplissage |Non |caractère unique |Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte. La valeur par défaut est un espace. |

Si la chaîne d’origine est plus longue que le nombre de caractères de remplissage, aucun caractère n’est ajouté.

### <a name="return-value"></a>Valeur retournée

Chaîne avec au moins le nombre de caractères spécifié.

### <a name="examples"></a>Exemples

L’exemple ci-après indique comment remplir la valeur de paramètre fournie par l’utilisateur avec le caractère zéro jusqu’à atteindre le nombre total de caractères.

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>remplacer

`replace(originalString, oldString, newString)`

Renvoie une nouvelle chaîne dans laquelle toutes les instances d’une chaîne ont été remplacées par une autre.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_initiale |Oui |string |La valeur qu’ont toutes les instances d’une chaîne ont été remplacées par une autre. |
| oldString |Oui |string |Chaîne à supprimer de la chaîne initiale. |
| newString |Oui |string |Chaîne à ajouter à la place de la chaîne supprimée. |

### <a name="return-value"></a>Valeur retournée

Chaîne contenant les caractères remplacés.

### <a name="examples"></a>Exemples

L’exemple suivant illustre comment supprimer tous les tirets de la chaîne fournie par l’utilisateur et comment remplacer une partie de la chaîne par une autre chaîne.

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourne une chaîne avec tous les caractères après le nombre spécifié de caractères, ou un tableau avec tous les éléments après le nombre spécifié d’éléments.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à utiliser pour ignorer les caractères. |
| numberToSkip |Oui |int |Nombre d’éléments ou de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments ou caractères de la valeur sont renvoyés. Si elle est supérieure à la longueur du tableau ou de la chaîne, un tableau ou une chaîne vide est retourné. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="examples"></a>Exemples

L’exemple suivant ignore le nombre spécifié d’éléments dans le tableau et le nombre spécifié de caractères dans une chaîne.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Renvoie un tableau de chaînes qui contient les sous-chaînes de la chaîne d’entrée séparées par les délimiteurs spécifiés.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| inputString |Oui |string |Chaîne à fractionner. |
| delimiter |Oui |chaîne ou tableau de chaînes |Le séparateur à utiliser pour fractionner la chaîne. |

### <a name="return-value"></a>Valeur retournée

Tableau de chaînes.

### <a name="examples"></a>Exemples

L’exemple suivant fractionne la chaîne d’entrée par une virgule et par une virgule ou un point-virgule.

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Détermine si une chaîne commence par une valeur. La comparaison respecte la casse.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

**True** si le ou les premiers caractères de la chaîne correspondent à la valeur ; sinon, **False**.

### <a name="examples"></a>Exemples

L'exemple suivant montre comment utiliser les fonctions startsWith et endsWith :

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Convertit la valeur spécifiée en chaîne.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui | Quelconque |Valeur à convertir en chaîne. N’importe quel type de valeur peut être converti, y compris les objets et des tableaux. |

### <a name="return-value"></a>Valeur retournée

Chaîne de la valeur convertie.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment convertir différents types de valeurs de chaînes :

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Retourne une sous-chaîne qui commence à la position de caractère spécifiée et qui contient le nombre de caractères spécifié.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_analyser |Oui |string |La chaîne d’origine de laquelle la sous-chaîne est extraite. |
| index_début |Non |int |La position de caractère (commençant à zéro) de la sous-chaîne. |
| length |Non |int |Le nombre de caractères de la sous-chaîne. Doit faire référence à un emplacement au sein de la chaîne. Doit être égal à zéro ou supérieur. En cas d’omission, le reste de la chaîne à partir de la position de début sera retourné.|

### <a name="return-value"></a>Valeur retournée

Sous-chaîne. Ou une chaîne vide si la longueur est égale à zéro.

### <a name="remarks"></a>Notes

La fonction échoue lorsque la sous-chaîne s’étend au-delà de la fin de la chaîne ou lorsque la longueur est inférieure à zéro. L’exemple suivant échoue avec l’erreur « Les paramètres d’index et de longueur doivent correspondre à un emplacement au sein de la chaîne. Le paramètre d'index : '0', le paramètre de longueur : '11', le paramètre de longueur de la chaîne : '10' ».

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

### <a name="examples"></a>Exemples

L’exemple suivant extrait une sous-chaîne à partir d’un paramètre.

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourne une chaîne avec le nombre spécifié de caractères à partir du début de la chaîne, ou un tableau avec le nombre spécifié d’éléments à partir du début du tableau.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à partir duquel les éléments sont tirés. |
| numberToTake |Oui |int |Nombre d’éléments ou de caractères à prendre. Si cette valeur est inférieure ou égale à 0, une chaîne ou un tableau vide est renvoyé. Si elle est supérieure à la longueur du tableau ou de la chaîne, tous les éléments du tableau ou de la chaîne sont retournés. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="examples"></a>Exemples

L’exemple suivant prend le nombre spécifié d’éléments du tableau, et les caractères d’une chaîne.

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | sur |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Convertit la chaîne spécifiée en minuscules.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |string |La valeur à convertir en minuscules. |

### <a name="return-value"></a>Valeur retournée

Chaîne convertie en minuscules.

### <a name="examples"></a>Exemples

L’exemple ci-après convertit une valeur de paramètre en minuscules et en majuscules.

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Convertit la chaîne spécifiée en majuscules.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |string |La valeur à convertir en majuscules. |

### <a name="return-value"></a>Valeur retournée

Chaîne convertie en majuscules.

### <a name="examples"></a>Exemples

L’exemple ci-après convertit une valeur de paramètre en minuscules et en majuscules.

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Supprime tous les espaces de début et de fin de la chaîne indiquée.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Oui |string |La valeur à supprimer. |

### <a name="return-value"></a>Valeur retournée

Chaîne sans les premiers et derniers caractères d’espace.

### <a name="examples"></a>Exemples

L’exemple suivant supprime les espaces à partir du paramètre.

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Crée une chaîne de hachage déterministe basée sur les valeurs fournies en tant que paramètres.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Oui |string |La valeur utilisée dans la fonction de hachage pour créer une chaîne unique. |
| paramètres supplémentaires le cas échéant |Non |string |Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité. |

### <a name="remarks"></a>Notes

Cette fonction est utile lorsque vous avez besoin de créer un nom unique pour une ressource. Vous fournissez des valeurs de paramètre qui limitent l’étendue d’unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour l’abonnement, le groupe de ressources ou le déploiement.

La valeur retournée n’est pas une chaîne aléatoire, mais le résultat d’une fonction de hachage. La valeur renvoyée comprend 13 caractères. Elle n’est pas globalement unique. Il se peut que vous souhaitiez associer un préfixe de votre convention d’affectation de noms à la valeur pour créer un nom explicite. L’exemple suivant montre le format de la valeur renvoyée. La valeur réelle varie en fonction des paramètres fournis.

`tcvhiyu5h2o5o`

Les exemples suivants montrent comment utiliser uniqueString afin de créer une valeur unique pour des niveaux couramment utilisés.

Unique limité à l’abonnement

```bicep
uniqueString(subscription().subscriptionId)
```

Unique limité au groupe de ressources

```bicep
uniqueString(resourceGroup().id)
```

Unique limité au déploiement pour un groupe de ressources

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

L'exemple suivant montre comment créer un nom unique pour un compte de stockage basé sur votre groupe de ressources. Dans le groupe de ressources, le nom n’est pas unique s’il est construit de la même façon.

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

Si vous devez créer un nom unique chaque fois que vous déployez un fichier Bicep, et que vous n’envisagez pas de mettre à jour la ressource, utilisez la fonction [utcNow](./bicep-functions-date.md#utcnow) avec uniqueString. Vous pouvez utiliser cette approche dans un environnement de test. Pour obtenir un exemple, consultez [utcNow](./bicep-functions-date.md#utcnow). Notez que la fonction utcNow peut uniquement être utilisée dans une expression pour la valeur par défaut d’un paramètre.

### <a name="return-value"></a>Valeur retournée

Chaîne contenant 13 caractères.

### <a name="examples"></a>Exemples

L’exemple suivant renvoie les résultats à partir d’uniquestring :

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Crée un URI absolu en combinant le baseUri et la chaîne relativeUri.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Oui |string |La chaîne d’URI de base. Veillez à observer le comportement relatif à la gestion de la barre oblique (« / ») finale, tel qu’il est décrit après ce tableau.  |
| relativeUri |Oui |string |La chaîne d’URI relatif à ajouter à la chaîne d’URI de base. |

* Si **baseUri** se termine par une barre oblique finale, le résultat est simplement **baseUri** suivi de **relativeUri**.

* Si **baseUri** ne se termine pas par une barre oblique finale, deux événements sont possibles.

   * Si **baseUri** n’a aucune barre oblique (hormis « // » près du début), le résultat est simplement **baseUri** suivi de **relativeUri**.

   * Si **baseUri** comporte des barres obliques, mais ne se termine pas par une barre oblique, tout ce qui se trouve après la dernière barre oblique est supprimé de **baseUri** et le résultat est **baseUri** suivi de **relativeUri**.

Voici quelques exemples :

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```

Pour obtenir des détails complets, les paramètres **baseUri** et **relativeUri** sont résolus comme indiqué dans la [RFC 3986, section 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Valeur retournée

Chaîne représentant l’URI absolu pour les valeurs de base et relative.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Encode un URI.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Oui |string |Valeur à encoder. |

### <a name="return-value"></a>Valeur retournée

Chaîne de la valeur encodée de l’URI.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Retourne une chaîne de la valeur encodée de l’URI.

Espace de noms : [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Oui |string |Valeur encodée de l’URI à convertir en une chaîne. |

### <a name="return-value"></a>Valeur retournée

Chaîne décodée de la valeur encodée de l’URI.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).
* Pour itérer un nombre spécifié de fois lors de la création d’un type de ressource, consultez [Boucles itératives dans Bicep](loops.md).
* Pour découvrir comment déployer le fichier Bicep que vous avez créé, consultez [Déployer des ressources avec Bicep et Azure PowerShell](./deploy-powershell.md).
