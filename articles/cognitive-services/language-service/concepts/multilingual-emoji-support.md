---
title: Prise en charge de plusieurs langues et des emojis dans Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Découvrez les décalages provoqués par les encodages de plusieurs langues et des emojis dans les fonctionnalités du service de langage.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48bda7b1e5ab5eaa62c525997530acd9af31a39a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096199"
---
# <a name="multilingual-and-emoji-support-in-language-service-features"></a>Prise en charge de plusieurs langues et des emojis dans les fonctionnalités du service de langage

La prise en charge multilingue et d’emojis a conduit à des encodages Unicode qui utilisent plusieurs [points de code](https://wikipedia.org/wiki/Code_point) pour représenter un seul caractère affiché, appelé graphème. Par exemple, des emojis tels que 🌷 et 👍 peuvent utiliser plusieurs caractères pour composer la forme, avec des caractères supplémentaires pour des attributs visuels tels que le teint de la peau. De même, le mot Hindi `अनुच्छेद` est encodé sous la forme de cinq lettres et de trois marques qui se combinent.

En raison des différentes longueurs d’encodage de plusieurs langues et des emojis possibles, les fonctionnalités du service de langage peuvent retourner des décalages dans la réponse.

## <a name="offsets-in-the-api-response"></a>Décalages dans la réponse de l’API

Chaque fois que des décalages sont retournés par la réponse de l’API, rappelez-vous de ce qui suit :

* Les éléments de la réponse peuvent être spécifiques du point de terminaison appelé. 
* Les charges utiles HTTP POST/GET sont encodées au format [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), qui peut être ou non l’encodage de caractères par défaut sur votre compilateur ou système d’exploitation côté client.
* Les décalages font référence aux nombres de graphèmes selon la norme [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0), non aux nombres de caractères.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extraction de sous-chaînes d’un texte avec des décalages

Les décalages peuvent occasionner des problèmes lors de l’utilisation de méthodes de sous-chaîne basées sur des caractères, par exemple, la méthode .NET [substring()](/dotnet/api/system.string.substring). Un problème est qu’un décalage peut avoir pour effet qu’une méthode de sous-chaîne se termine au milieu d’un encodage de graphème de plusieurs caractères plutôt qu’à la fin.

Dans .NET, envisagez d’utiliser la classe [StringInfo](/dotnet/api/system.globalization.stringinfo), qui vous permet d’utiliser une chaîne comme une série d’éléments textuels, plutôt que d’objets caractères individuels. Vous pouvez également rechercher des bibliothèques de fractionnements de graphèmes dans votre environnement logiciel de prédilection. 

Les fonctionnalités du service de langage retournent également ces éléments textuels, pour des raisons pratiques.

Les points de terminaison qui retournent un décalage prennent en charge le paramètre `stringIndexType`. Ce paramètre ajuste les attributs `offset` et `length` dans la sortie de l'API pour qu'ils correspondent au schéma d'itération de chaîne demandé. Actuellement, trois types sont pris en charge :

- `textElement_v8` (par défaut) : itération sur les graphèmes, comme défini par la norme [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)
- `unicodeCodePoint` : itération sur les [points de code Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), le schéma par défaut pour Python 3
- `utf16CodeUnit` : itération sur les [unités de code UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), le schéma par défaut pour JavaScript, Java et .NET

Si le `stringIndexType` demandé correspond à l'environnement de programmation de votre choix, l'extraction de sous-chaîne peut être effectuée à l'aide des méthodes substring ou slice standard. 

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble du service de langage](../overview.md)
