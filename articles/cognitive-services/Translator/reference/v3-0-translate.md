---
title: Méthode Translate de Translator
titleSuffix: Azure Cognitive Services
description: Comprendre les paramètres, les en-têtes et les messages de corps de la méthode Translate de d’Azure Cognitive Services Translator pour traduire du texte.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/12/2021
ms.author: lajanuar
ms.openlocfilehash: 8e3c343aede5959b30e4732e74eb6a56a683e365
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848531"
---
# <a name="translator-30-translate"></a>Translator 3.0 : Translate

Traduit du texte.

## <a name="request-url"></a>URL de la demande

Envoyez une demande `POST` à :

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

### <a name="required-parameters"></a>Paramètres obligatoires

| Paramètre de requête. | Description |
| --- | --- |
| api-version | _Paramètre obligatoire_.  <br>Version de l’API demandée par le client. La valeur doit être `3.0`. |
| to  | _Paramètre obligatoire_.  <br>Spécifie la langue du texte de sortie. La langue cible doit être l’une des [langues prises en charge](v3-0-languages.md) incluses dans l’étendue `translation`. Par exemple, utilisez `to=de` pour traduire en allemand.  <br>Il est possible de traduire en plusieurs langues simultanément en répétant le paramètre dans la chaîne de requête. Par exemple, utilisez `to=de&to=it` pour traduire en allemand et italien. |

### <a name="optional-parameters"></a>Paramètres facultatifs



| Paramètre de requête. | Description |
| --- | --- |


| Paramètre de requête. | Description |
| --- | --- |
| de | _Paramètre facultatif_.  <br>Spécifie la langue du texte d’entrée. Trouvez les langues disponibles pour la traduction en recherchant [langues prises en charge](../reference/v3-0-languages.md) à l’aide de l’étendue `translation`. Si le paramètre `from` n’est pas spécifié, une détection automatique de la langue est appliquée pour déterminer la langue source.  <br>  <br>Vous devez utiliser le paramètre `from` au lieu de la détection automatique lors de l’utilisation de la fonctionnalité de [dictionnaire dynamique](../dynamic-dictionary.md). |
| textType | _Paramètre facultatif_.  <br>Définit si le texte en cours de traduction est au format texte brut ou HTML. Tout code HTML doit être un élément bien formé et complet. Les valeurs possibles sont : `plain` (par défaut) ou `html`. |
| catégorie | _Paramètre facultatif_.  <br>Chaîne spécifiant la catégorie (domaine) de la traduction. Ce paramètre est utilisé pour obtenir des traductions d’un système personnalisé créé avec [Custom Translator](../customization.md). Ajoutez l’ID de catégorie de vos [détails de projet](../custom-translator/how-to-create-project.md#view-project-details) Custom Translator à ce paramètre pour utiliser votre système personnalisé déployé. La valeur par défaut est `general`. |
| ProfanityAction | _Paramètre facultatif_.  <br>Spécifie comment les vulgarités doivent être traitées dans les traductions. Les valeurs possibles sont : `NoAction` (valeur par défaut), `Marked` ou `Deleted`. Pour comprendre comment traiter les vulgarités, voir [Gestion de la vulgarité](#handle-profanity). |
| ProfanityMarker | _Paramètre facultatif_.  <br>Spécifie comment vulgarités doit être marquées dans les traductions. Les valeurs possibles sont : `Asterisk` (par défaut) ou `Tag`. Pour comprendre comment traiter les vulgarités, voir [Gestion de la vulgarité](#handle-profanity). |
| includeAlignment | _Paramètre facultatif_.  <br>Spécifie s’il faut inclure une projection d’alignement du texte source vers le texte traduit. Les valeurs possibles sont `true` ou `false` (par défaut). |
| includeSentenceLength | _Paramètre facultatif_.  <br>Spécifie s’il faut inclure des limites de longueur de phrase aux texte d’entrée et au texte traduit. Les valeurs possibles sont `true` ou `false` (par défaut). |
| suggestedFrom | _Paramètre facultatif_.  <br>Spécifie une langue de base si la langue du texte d’entrée ne peut pas être identifiée. La détection automatique de la langue est appliquée en cas d’omission du paramètre `from`. Si la détection échoue, la langue `suggestedFrom` est présupposée. |
| fromScript | _Paramètre facultatif_.  <br>Spécifie le script du texte d’entrée. |
| toScript | _Paramètre facultatif_.  <br>Spécifie le script du texte traduit. |
| allowFallback | _Paramètre facultatif_.  <br>Spécifie que le service est autorisé à revenir à un système général quand il n’existe pas de système personnalisé. Les valeurs possibles sont : `true` (par défaut) ou `false`.  <br>  <br>`allowFallback=false` spécifie que la traduction doit utiliser uniquement les systèmes entraînés pour le `category` spécifié par la requête. Si une traduction de langue X en langue Y exige un chaînage via une langue relais E, tous les systèmes présents dans la chaîne (X->E et E->Y) doivent être personnalisés et avoir la même catégorie. Si aucun système n’est trouvé avec une catégorie spécifique, la requête retourne le code d’état 400. `allowFallback=true` spécifie que le service est autorisé à revenir à un système général quand il n’existe pas de système personnalisé. |

Les en-têtes de demande sont les suivants :

| headers | Description |
| --- | --- |
| En-tête(s) d’authentification | _En-tête de demande obligatoire_.  <br>Voir les [options disponibles pour l’authentification](./v3-0-reference.md#authentication). |
| Content-Type | _En-tête de demande obligatoire_.  <br>Spécifie le type de contenu de la charge utile.  <br>La valeur acceptée est `application/json; charset=UTF-8`. |
| Content-Length | _En-tête de demande obligatoire_.  <br>Longueur du corps de la demande. |
| X-ClientTraceId | _Facultatif_.  <br>GUID généré par le client pour identifier de façon unique la demande. Vous pouvez omettre cet en-tête si vous incluez l’ID de trace dans la chaîne de requête à l’aide d’un paramètre de requête appelé `ClientTraceId`. |

## <a name="request-body"></a>Corps de la demande

Le corps de la demande est un tableau JSON. Chaque élément du tableau est un objet JSON avec une propriété de chaîne nommée `Text`, qui représente la chaîne à traduire.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Les limites suivantes s'appliquent :

* Le tableau ne peut pas compter plus de 100 éléments.
* L’intégralité du texte inclus dans la demande ne peut pas dépasser 10 000 caractères, espaces comprises.

## <a name="response-body"></a>Response body

Une réponse correcte est un tableau JSON avec un résultat pour chaque chaîne dans le tableau d’entrée. Un objet de résultat inclut les propriétés suivantes :

  * `detectedLanguage`: objet décrivant la langue détectée au moyen des propriétés suivantes :

      * `language`: chaîne représentant le code de la langue détectée.

      * `score`: valeur flottante indiquant le niveau de confiance dans le résultat. Le score est compris entre zéro et un, un score faible indiquant un niveau de confiance bas.

    La propriété `detectedLanguage` n’est présente dans l’objet de résultat que quand la détection automatique de la langue est demandée.

  * `translations`: tableau des résultats de la traduction. La taille du tableau correspond au nombre de langues cibles spécifié par le paramètre de requête `to`. Chaque élément dans le tableau inclut ce qui suit :

    * `to`: chaîne représentant le code de langue de la langue cible.

    * `text`: chaîne fournissant le texte traduit.

    * `transliteration`: objet fournissant le texte traduit dans le script spécifié par le paramètre `toScript`.

      * `script`: chaîne spécifiant le script cible.

      * `text`: chaîne fournissant le texte traduit dans le script cible.

    L’objet `transliteration` n’est pas inclus si une translittération n’a pas lieu.

    * `alignment`: objet avec une propriété de chaîne unique nommée `proj`, qui mappe le texte d’entrée au texte traduit. Les informations d’alignement ne sont fournies que quand le paramètre de requête `includeAlignment` est `true`. L’alignement est renvoyé en tant que valeur de chaîne au format suivant : `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Le signe deux-points sépare les index de début et de fin, le signe tiret sépare les langues, et une espace sépare les mots. Un mot peut s’aligner avec zéro, un ou plusieurs mots dans l’autre langue, et les mots alignés peuvent ne pas être contigus. Quand aucune information d’alignement n’est disponible, l’élément d’alignement est vide. Pour obtenir un exemple et les restrictions, voir [Obtenir les informations d’alignement](#obtain-alignment-information).

    * `sentLen`: objet retournant les limites de longueur de phrase dans les textes d’entrée et de sortie.

      * `srcSentLen`: tableau d’entiers représentant les longueurs des phrases dans le texte d’entrée. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

      * `transSentLen`:  tableau d’entiers représentant les longueurs des phrases dans le texte traduit. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

    Les limites de longueur de phrase ne sont incluses que si le paramètre de requête `includeSentenceLength` est `true`.

  * `sourceText`: objet avec une propriété de chaîne unique nommée `text`, qui fournit le texte d’entrée dans le script par défaut de la langue source. La propriété `sourceText` est présente uniquement quand l’entrée est exprimée dans un script qui n’est pas le script habituel pour la langue. Par exemple, si l’entrée est de l’arabe écrit dans un script latin, `sourceText.text` est le même texte en arabe converti en script arabe.

Des exemples de réponses JSON sont fournis dans la section [exemples](#examples).

## <a name="response-headers"></a>En-têtes de réponse

| headers | Description |
| --- | --- |
| X-RequestId | Valeur générée par le service pour identifier la demande. Elle sert à des fins de dépannage. |
| X-MT-System | Spécifie le type de système qui a été utilisé pour la traduction pour chaque langue de destination (« to ») demandée pour la traduction. La valeur est une liste de chaînes séparées par des virgules. Chaque chaîne indique un type :  <br><br>* Personnalisé : la requête inclut un système personnalisé, et au moins un système personnalisé a été utilisé lors de la traduction.<br>* Équipe : toutes les autres requêtes. |

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner.

| ProfanityAction | Action |
| --- | --- |
| `NoAction` |NoAction est le comportement par défaut. La vulgarité de la source est reflétée dans la cible.  <br>  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (français)**  : Il est un imbécile. |
| `Deleted` | Les mots vulgaires sont retirés de la cible sans remplacement.  <br>  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (anglais)**  : Il est  |
| `Marked` | Les mots vulgaires sont remplacés par un marqueur dans la sortie. Le marqueur varie selon le paramètre `ProfanityMarker`.  <br>  <br>Pour `ProfanityMarker=Asterisk`, les mots vulgaires sont remplacés par `***` :  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (anglais)**  : il est un \\ *\\* \\*.  <br>  <br>Pour `ProfanityMarker=Tag`, les mots vulgaires sont entourés de balises XML &lt;profanity&gt; et &lt;/profanity&gt; :  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (français)**  : C’est un &lt;profanity&gt;con&lt;/profanity&gt;. |

Si une erreur se produit, la requête renvoie également une réponse d'erreur JSON. Le code d’erreur est un nombre à 6 chiffres qui combine le code d’état HTTP à 3 chiffres et un nombre à 3 chiffres qui sert à catégoriser plus précisément l’erreur. Vous trouverez les codes d’erreur les plus courants sur la [page Référence de Translator v3](./v3-0-reference.md#errors).

## <a name="examples"></a>Exemples

### <a name="translate-a-single-input"></a>Traduire une entrée unique

Cet exemple montre comment traduire une phrase unique de l’anglais en chinois simplifié.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Le `translations` tableau inclut un élément qui fournit la traduction de l’élément de texte dans l’entrée.

### <a name="translate-a-single-input-with-language-autodetection"></a>Traduire une entrée unique avec détection automatique de la langue

Cet exemple montre comment traduire une phrase unique de l’anglais en chinois simplifié. La demande ne spécifie pas la langue d’entrée. La détection automatique de la langue source est utilisée à la place.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
La réponse est similaire à la réponse de l’exemple précédent. Étant donné que la détection automatique de la langue a été demandée, la réponse inclut également des informations sur la langue détectée pour le texte d’entrée. La détection automatique de la langue fonctionne mieux avec un texte d’entrée plus long.

### <a name="translate-with-transliteration"></a>Traduire avec translittération

Étendons l’exemple précédent en ajoutant la translittération. La requête suivante demande une traduction chinoise écrite en script Latin.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Le résultat de la traduction inclut à présent une `transliteration` propriété qui fournit le texte traduit en caractères latins.

### <a name="translate-multiple-pieces-of-text"></a>Traduire plusieurs éléments de texte

La traduction de plusieurs chaînes en une fois nécessite simplement de spécifier un tableau de chaînes dans le corps de la demande.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

La réponse contient la traduction de tous les éléments de texte dans le même ordre que dans la requête.
Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Traduire en plusieurs langues

Cet exemple montre comment traduire une même entrée en plusieurs langues en utilisant une seule requête.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Le corps de la réponse est le suivant :

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Traiter la vulgarité

Le service Translator conserve normalement dans la traduction toute vulgarité présente dans la source. Le degré de vulgarité et le contexte qui rend des mots vulgaires diffèrent selon les cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute vulgarité dans la traduction, indépendamment de la présence de vulgarité dans le texte source, vous disposez d’une option de filtrage de la vulgarité. L’option vous permet de spécifier si vous souhaitez que la vulgarité soit supprimée, que les vulgarités soient marquées à l’aide de balises appropriées (ce qui vous offre la possibilité d’ajouter votre propre post-traitement), ou qu’aucune action ne soit appliquée. Les valeurs acceptées de `ProfanityAction` sont `Deleted`, `Marked` et `NoAction` (par défaut).


| ProfanityAction | Action |
| --- | --- |
| `NoAction` | NoAction est le comportement par défaut. La vulgarité de la source est reflétée dans la cible.  <br>  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (français)**  : Il est un imbécile. |
| `Deleted` | Les mots vulgaires sont retirés de la cible sans remplacement.  <br>  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (français)**  : C’est un. |
| `Marked` | Les mots vulgaires sont remplacés par un marqueur dans la sortie. Le marqueur varie selon le paramètre `ProfanityMarker`.  <br>  <br>Pour `ProfanityMarker=Asterisk`, les mots vulgaires sont remplacés par `***` :  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (anglais)**  : il est un \\ *\\* \\*.  <br>  <br>Pour `ProfanityMarker=Tag`, les mots vulgaires sont entourés de balises XML &lt;profanity&gt; et &lt;/profanity&gt; :  <br>**Exemple de source (japonais)**  : 彼はジャッカスです。  <br>**Exemple de traduction (français)**  : C’est un &lt;profanity&gt;con&lt;/profanity&gt;. |

Par exemple :

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is an <expletive> good idea.'}]"
```

Cette requête retourne :

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Comparez à :

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is an <expletive> good idea.'}]"
```

Cette dernière demande renvoie :

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduire du contenu incluant un balisage et décider ce qui est traduit

Il est courant de traduire du contenu incluant un balisage, tel que le contenu d’une page HTML ou d’un document XML. Lors de la traduction de contenu incluant des balises, incluez le paramètre de requête `textType=html`. De plus, il est parfois utile d’exclure du contenu spécifique de la traduction. Vous pouvez utiliser l’attribut `class=notranslate` pour spécifier le contenu qui doit rester dans la langue d’origine. Dans l’exemple suivant, le contenu du premier élément `div` ne sera pas traduit, tandis que le contenu du deuxième élément `div` le sera.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Voici un exemple de demande.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

La réponse est la suivante :

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Obtenir les informations d’alignement

L’alignement est retourné en tant que valeur de chaîne au format suivant pour chaque mot de la source. Les informations pour chaque mot sont séparées par un espace, y compris pour les langues non séparées par des espaces (scripts) comme le chinois :

[[SourceTextStartIndex]\:[SourceTextEndIndex]–[TgtTextStartIndex]\:[TgtTextEndIndex]] *

Exemple de chaîne d’alignement : « 0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21 ».

En d’autres termes, le signe deux-points sépare les index de début et de fin, le signe tiret sépare les langues et un espace sépare les mots. Un mot peut s’aligner avec zéro, un ou plusieurs mots dans l’autre langue, et les mots alignés peuvent ne pas être contigus. Quand aucune information d’alignement n’est disponible, l’élément Alignment est vide. La méthode ne retourne aucune erreur dans ce cas.

Pour recevoir les informations d’alignement, spécifiez `includeAlignment=true` sur la chaîne de requête.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

La réponse est la suivante :

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Les informations d’alignement commencent par `0:2-0:1`, ce qui signifie que les trois premiers caractères dans le texte source (`The`) mappent aux deux premiers caractères dans le texte traduit (`La`).

#### <a name="limitations"></a>Limites
L'obtention des informations d'alignement est une fonctionnalité expérimentale dédiée au prototypage de la recherche et des expériences avec des mappages de phrases potentiels. Nous pourrions décider de ne plus la prendre en charge à l'avenir. Voici quelques restrictions notables pour lesquelles les alignements ne sont pas pris en charge :

* L’alignement n’est pas disponible pour du texte au format HTML, par exemple textType=html
* L’alignement est renvoyé uniquement pour un sous-ensemble de paires de langues :
  - Anglais vers/depuis n’importe quelle autre langue, à l’exception du chinois traditionnel, du cantonais (traditionnel) ou du serbe (cyrillique).
  - du japonais au coréen ou inversement.
  - du japonais au chinois simplifié et du chinois simplifié au japonais.
  - du chinois simplifié au chinois traditionnel et du chinois traditionnel au chinois simplifié.
* Vous ne recevrez pas d’alignement si la phrase est une traduction définie. Des traductions définies sont, par exemple, « Ceci est un test », « Je t’aime » et d’autres phrases extrêmement fréquentes.
* L'alignement n'est pas possible lorsque vous appliquez l'une des approches visant à empêcher la traduction comme décrit [ici](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Obtenir les limites de longueur de phrase

Pour recevoir des informations sur la longueur des phrases dans le texte source et le texte traduit, spécifiez `includeSentenceLength=true` dans la chaîne de requête.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

La réponse est la suivante :

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Traduire avec un dictionnaire dynamique

Si vous connaissez déjà la traduction que vous souhaitez appliquer à un mot ou à une phrase, vous pouvez la fournir en tant que balisage dans la demande. Le dictionnaire dynamique n’est sûr que pour des noms propres comme les noms de personnes et les noms de produits.

Le balisage à fournir utilise la syntaxe suivante.

```
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Par exemple, considérez la phrase : « Le mot wordomatic est une entrée de dictionnaire ». Pour conserver le mot _wordomatic_ dans la traduction, envoyez la demande suivante :

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Le résultat est le suivant :

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Cette fonctionnalité opère de la même façon avec `textType=text` ou `textType=html`. Elle doit être utilisée avec parcimonie. La façon appropriée et de loin préférable de personnaliser une traduction consiste à utiliser Custom Translator. Custom Translator utilise totalement le contexte et les probabilités statistiques. Si vous avez ou pouvez vous permettre de créer des données d’apprentissage qui montrent votre mot ou phrase en contexte, vous obtenez de bien meilleurs résultats. [En savoir plus sur Custom Translator](../customization.md).