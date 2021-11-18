---
title: Fonctions personnalisées dans IoT Connector-API de santé Azure
description: cet article explique comment utiliser des fonctions personnalisées avec IoT Connector modèles de mappages d’appareils.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 37e720e3623105f5d3a6131a1df5d6d53edb6db7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733429"
---
# <a name="how-to-use-custom-functions"></a>Comment utiliser des fonctions personnalisées

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

Cet article explique comment utiliser les fonctions client du connecteur IoT.

De nombreuses fonctions sont disponibles lors de l’utilisation de **JmesPath** en tant que langage d’expression. Outre les fonctions disponibles dans le cadre de la spécification JmesPath, de nombreuses fonctions personnalisées peuvent également être utilisées. Cet article décrit les fonctions personnalisées spécifiques aux connecteurs IoT à utiliser avec le modèle de mappages d’appareils pendant le processus de normalisation.

> [!TIP]
> Pour plus d’informations sur les fonctions JmesPath, consultez la [spécification](https://jmespath.org/specification.html#built-in-functions)JmesPath.

## <a name="function-signature"></a>Signature de fonction

Chaque fonction a une signature qui respecte la spécification JmesPath. Cette signature peut être représentée comme suit :

```jmespath
return_type function_name(type $argname)
```

La signature indique les types valides pour les arguments. Si un type non valide est passé pour un argument, une erreur se produit.

> [!NOTE]
> Quand des fonctions mathématiques sont effectuées, le résultat final **doit** pouvoir tenir dans une valeur [long](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types) C#. Si le résultat final ne peut pas être contenu dans une valeur long C#, une erreur mathématique se produit.

## <a name="exception-handling"></a>Gestion des exceptions

Des exceptions peuvent se produire à différents stades du cycle de vie de traitement des événements. Voici les différents points où ils peuvent se produire :

|Action|Lorsque le répertoire|Exceptions qui peuvent se produire pendant l’analyse du modèle|Résultat|
|------|----|-------------------------------------------------|-------|
|**Analyse de modèle**|Chaque fois qu’un nouveau lot de messages est reçu, le modèle de mappage d’appareil est chargé et analysé.|Échec de l’analyse du modèle.|Le système va tenter de recharger et d’analyser le dernier modèle de mappage d’appareil jusqu’à ce que l’analyse aboutisse. Aucun nouveau message ne sera traité tant que l’analyse n’aura pas réussi.|
|**Analyse de modèle**|Chaque fois qu’un nouveau lot de messages est reçu, le modèle de mappage d’appareil est chargé et analysé.|Échec de l’analyse des expressions.|Le système va tenter de recharger et d’analyser le dernier modèle de mappage d’appareil jusqu’à ce que l’analyse aboutisse. Aucun nouveau message ne sera traité tant que l’analyse n’aura pas réussi.|
|**Exécution de la fonction**|Chaque fois qu’une fonction est exécutée sur des données dans un message.|Les données d’entrée ne correspondent pas à celles de la signature de la fonction.|Le système arrête le traitement de ce message. Le message n’est pas retenté.|
|**Exécution de la fonction**|Chaque fois qu’une fonction est exécutée sur des données dans un message.|Toutes les autres exceptions énumérées dans la description de la fonction.|Le système arrête le traitement de ce message. Le message n’est pas retenté.|

## <a name="mathematical-functions"></a>Fonctions mathématiques

### <a name="add"></a>add

```jmespath
number add(number $left, number $right)
```

Retourne le résultat de l’ajout de l’argument de gauche à droite.

Exemples :

| Étant donné                       | Expression       | Résultats |
|-----------------------------|------------------|--------|
| n/a                         | Ajouter (10, 10)      | 20     |
| {« Left » : 40, « Right » : 50}   | Ajouter (gauche, droite) | 90     |
| {"left" : 0, "Right" : 50}    | Ajouter (gauche, droite) | 50     |

### <a name="divide"></a>diviser

```jmespath
number divide(number $left, number $right)
```

Retourne le résultat de la Division de l’argument de gauche par la droite.

Exemples :

| Étant donné                       | Expression          | Résultats                           |
|-----------------------------|---------------------|----------------------------------|
| n/a                         | diviser (10, 10)      | 1                                |
| {« Left » : 40, « Right » : 50}   | diviser (gauche, droite) | 0,8                              |
| {"left" : 0, "Right" : 50}    | diviser (gauche, droite) | 0                                |
| {« Left » : 50, « Right » : 0}    | diviser (gauche, droite) | erreur solutions mathématiques : Division par zéro |

### <a name="multiply"></a>multiplier

```jmespath
number multiply(number $left, number $right)
```

Retourne le résultat de la multiplication de l’argument de gauche par le droit.

Exemples :

| Étant donné                       | Expression            | Résultats |
|-----------------------------|-----------------------|--------|
| n/a                         | multiplier (10, 10)      | 100    |
| {« Left » : 40, « Right » : 50}   | multiplier (gauche, droite) | 2000   |
| {"left" : 0, "Right" : 50}    | multiplier (gauche, droite) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

Retourne le résultat de l’élévation de l’argument de gauche à la puissance de droite.

Exemples :

| Étant donné                         | Expression       | Résultats                     |
|-------------------------------|------------------|----------------------------|
| n/a                           | Pow (10, 10)      | 10000000000                |
| {« Left » : 40, « Right » : 50}     | Pow (gauche, droite) | erreur solutions mathématiques : dépassement de capacité |
| {"left" : 0, "Right" : 50}      | Pow (gauche, droite) | 0                          |
| {« Left » : 100, « Right » : 0,5}   | Pow (gauche, droite) | 10                         |

### <a name="subtract"></a>soustraction

```jmespath
number subtract(number $left, number $right)
```

Retourne le résultat de la soustraction de l’argument de droite de gauche.

Exemples :

| Étant donné                       | Expression            | Résultats |
|-----------------------------|-----------------------|--------|
| n/a                         | soustraire (10, 10)      | 0      |
| {« Left » : 40, « Right » : 50}   | soustraire (gauche, droite) | -10    |
| {"left" : 0, "Right" : 50}    | soustraire (gauche, droite) | \- 50    |

## <a name="string-functions"></a>Fonctions de chaînes

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

Produit une nouvelle chaîne en insérant la valeur de *toinsert* dans la chaîne *d’origine*. La chaîne sera insérée à la position *pos* dans la chaîne *d’origine*.

Si l’argument positionnel est basé sur zéro, la position de zéro fait référence au premier caractère dans la chaîne. 

Si l’argument positionnel fourni est hors limites de la longueur d' *origine*, une erreur se produit.

Exemples :

| Étant donné                                                     | Expression                                         | Résultats              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {« original » : « mple », « toInsert » : « sa », « Pos » : 0}          | insertString (original, toInsert, pos)              | exemple            |
| {"original" : "Suess", "toInsert" : "CC", "POS" : 2}         | insertString (original, toInsert, pos)              | fructueux           |
| {"original" : "myString", "toInsert" : " !!", "POS" : 8}      | insertString (original, toInsert, pos)              | « myString !! »        |
| {"original" : "myString", "toInsert" : " !!"}                | insertString (original, toInsert, longueur (original)) | « myString !! »        |
| {"original" : "myString", "toInsert" : " !!", "POS" : 100}    | insertString (original, toInsert, pos)              | erreur : en dehors de la plage |
| {"original" : "myString", "toInsert" : " !!", "POS" :-1}     | insertString (original, toInsert, pos)              | erreur : en dehors de la plage |

## <a name="date-functions"></a>Fonctions de date

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

Produit un horodatage conforme à la [norme ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) à partir de l’horodateur UNIX donné. L’horodateur est représenté sous la forme du nombre de secondes depuis l’époque (janvier 1 1970).

Exemples :

| Étant donné                 | Expression              | Résultats                  |
|-----------------------|-------------------------|-------------------------|
| {"UNIX" : 1625677200} | fromUnixTimestamp (UNIX)  | « 2021-07-07T17:00:00 + 0 » |
| {"UNIX" : 0}          | fromUnixTimestamp (UNIX)  | "1970-01-01T00:00:00 + 0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

Produit un horodatage conforme à la [norme ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) à partir de l’horodateur UNIX donné. L’horodateur est représenté comme le nombre de millisecondes depuis l’époque (janvier 1 1970).

Exemples :

| Étant donné                    | Expression                | Résultats                  |
|--------------------------|---------------------------|-------------------------|
| {"UNIX" : 1626799080000}  | fromUnixTimestampMs (UNIX) | « 2021-07-20T16:38:00 + 0 » |
| {"UNIX" : 0}              | fromUnixTimestampMs (UNIX) | "1970-01-01T00:00:00 + 0" |

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les fonctions personnalisées du connecteur IoT. Pour savoir comment utiliser des fonctions personnalisées avec des mappages de périphériques, consultez.

>[!div class="nextstepaction"]
>[Comment utiliser les mappages de périphérique](how-to-use-device-mappings.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
