---
title: Créer une requête sémantique
titleSuffix: Azure Cognitive Search
description: Définissez un type de requête sémantique pour attacher les modèles Deep Learning (apprentissage profond) au traitement des requêtes, en déduisant l’intention et le contexte dans le cadre du classement et de la pertinence de la recherche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e89f449d62a86d8a935647d0a60af25415f65b85
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058332"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>Créer une requête qui appelle le classement sémantique et renvoie les légendes sémantiques

> [!IMPORTANT]
> La fonctionnalité de recherche sémantique est en préversion publique et soumise à des [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Elle est disponible via le portail Azure, la préversion de l’API REST et les Kits de développement logiciel (SDK) bêta. Ces fonctionnalités sont facturables. Pour plus d’informations, consultez [Disponibilité et tarification](semantic-search-overview.md#availability-and-pricing).

La recherche sémantique est une fonctionnalité premium de Recherche cognitive Azure qui appelle un algorithme de classement sémantique sur un jeu de résultats et renvoie des légendes sémantiques (et éventuellement des [réponses sémantiques](semantic-answers.md)), avec une mise en évidence des termes et expressions les plus pertinents. Tant les légendes que les réponses sont renvoyées dans des demandes de requête formulées à l’aide du type de requête « sémantique ».

Les légendes et réponses sont extraites du texte littéral du document de recherche. Le sous-système sémantique détermine quelle partie de votre contenu présente les caractéristiques d’une légende ou d’une réponse, mais ne compose pas de nouvelles phrases ou expressions. Pour cette raison, le contenu qui comprend des explications ou des définitions est le mieux adapté à la recherche sémantique.

## <a name="prerequisites"></a>Prérequis

+ Un service Recherche cognitive de niveau Standard (S1, S2, S3), situé dans l’une des régions suivantes : USA Centre Nord, USA Ouest, USA Ouest 2, USA Est 2, Europe Nord, Europe Ouest. Si vous disposez déjà d’un service S1 ou supérieur dans l’une de ces régions, vous pouvez vous inscrire à la préversion sans devoir créer un nouveau service.

+ [S’inscrire pour la préversion](https://aka.ms/SemanticSearchPreviewSignup). Le délai d’exécution prévu est d’environ deux jours ouvrables.

+ Un index de recherche existant avec du contenu dans une [langue prise en charge](/rest/api/searchservice/preview-api/search-documents#queryLanguage). La recherche sémantique fonctionne mieux sur du contenu qui est à titre d’information ou descriptif.

+ Un client de recherche pour l’envoi de requêtes.

  Le client de recherche doit prendre en charge les API REST en préversion dans la demande de requête. Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou du code effectuant des appels REST aux API en préversion. Vous pouvez également utiliser l’[Explorateur de recherche](search-explorer.md) du portail Azure pour soumettre une requête sémantique. Vous pouvez également utiliser [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2).

+ Une [demande de requête](/rest/api/searchservice/preview-api/search-documents) doit inclure `queryType=semantic` et d’autres paramètres décrits dans cet article.

## <a name="whats-a-semantic-query-type"></a>Qu’est-ce qu’un type de requête sémantique ?

Dans la Recherche cognitive, une requête désigne une requête paramétrable qui détermine le traitement des requêtes et la forme de la réponse. Une *requête sémantique* [a des paramètres](#query-using-rest) qui appellent le modèle de reclassement sémantique. Celui-ci peut évaluer le contexte et la signification des résultats, faire remonter les résultats les plus pertinents et renvoyer des réponses et des légendes sémantiques.

La requête suivante est représentative d’une requête sémantique minimale (sans réponse).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Comme pour toutes les requêtes dans la Recherche cognitive, la requête cible la collection de documents d’un seul index. Par ailleurs, une requête sémantique subit la même séquence de décomposition, d’analyse, de recherche et de scoring qu’une requête non sémantique. 

La différence réside dans la pertinence et le scoring. Comme défini dans cette préversion, une requête sémantique désigne une requête dont les *résultats* sont reclassés au moyen d’un modèle de langage sémantique, ce qui permet de faire émerger les correspondances considérées comme les plus pertinentes par le classement sémantique, plutôt que les scores attribués par l’algorithme de classement de similarité par défaut.

Seules les 50 premières correspondances des résultats initiaux peuvent être classées de façon sémantique, et tous les résultats incluent des légendes dans la réponse. Si vous le souhaitez, vous pouvez spécifier un paramètre **`answer`** dans la requête pour extraire une réponse potentielle. Pour plus d’informations, consultez [Réponses sémantiques](semantic-answers.md).

## <a name="query-in-azure-portal"></a>Requête dans le portail Azure

L’[Explorateur de recherche](search-explorer.md) a été mis à jour pour inclure des options pour les requêtes sémantiques. Ces options deviennent visibles dans le portail après accomplissement des étapes suivantes :

1. Ouvrez le portail avec la syntaxe suivante : `https://portal.azure.com/?feature.semanticSearch=true`, sur un service de recherche pour lequel la préversion est activée.

1. Cliquez sur **Explorateur de recherche** en haut de la page de présentation.

1. Choisissez un index dont le contenu est dans une [langue prise en charge](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

1. Dans Explorateur de recherche, définissez les options de requête qui activent les requêtes sémantiques, les searchFields et la correction orthographique. Vous pouvez également coller les paramètres de requête requis dans la chaîne de requête.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Options de requête dans l’Explorateur de recherche" border="true":::

## <a name="query-using-rest"></a>Requête utilisant REST

Utilisez la [recherche de documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents) pour formuler la requête par programme. Une réponse comprend des sous-titres et une mise en surbrillance automatique. Si vous souhaitez une correction orthographique ou des réponses dans la réponse, ajoutez **`speller`** ou **`answers`** à la demande.

L’exemple suivant utilise l’index [hotels-sample-index](search-get-started-portal.md) pour créer une demande de requête sémantique avec une vérification orthographique, des réponses sémantiques et des légendes :

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Le tableau suivant récapitule les paramètres utilisés dans une requête sémantique. Pour obtenir la liste de tous les paramètres d’une requête, consultez [Recherche dans des documents (préversion REST)](/rest/api/searchservice/preview-api/search-documents).

| Paramètre | Type | Description |
|-----------|-------|-------------|
| queryType | String | Les valeurs valides sont « simple », « full » et « semantic ». La valeur « semantic » est requise pour les requêtes sémantiques. |
| queryLanguage | String | Obligatoire pour les requêtes sémantiques. Le lexique que vous spécifiez s’applique également au classement sémantique, aux légendes, aux réponses et à la vérification orthographique. Pour plus d’informations, consultez [Langues prises en charge (informations de référence sur l’API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). |
| searchFields | String | Liste délimitée par des virgules des champs pouvant faire l’objet d’une recherche. Spécifie les champs sur lesquels le classement sémantique se produit, à partir desquels les légendes et réponses sont extraites. </br></br>Contrairement aux types requête simple et requête complète, l’ordre dans lequel les champs sont répertoriés détermine la priorité. Pour plus d’informations sur l’utilisation, reportez-vous à [Étape 2 : définir searchFields](#searchfields). |
| Vérificateur d’orthographe | String | Paramètre facultatif, non spécifique aux requêtes sémantiques, qui corrige les termes mal orthographiés avant qu’ils n’atteignent le moteur de recherche. Pour plus d’informations, consultez [Ajouter une correction orthographique à des requêtes](speller-how-to-add.md). |
| answers |String | Paramètres facultatifs indiquant que les réponses sémantiques doivent être incluses dans le résultat ou pas. Actuellement, seule l’option « extractive » est implémentée. Les réponses peuvent être configurées pour être retournées au nombre de cinq maximum. La valeur par défaut est 1. Cet exemple montre un nombre de trois réponses :`extractive\|count-3`. Pour plus d’informations, consultez [Retourner des réponses sémantiques](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formuler la requête

Cette section décrit les étapes de la formulation des requêtes.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Étape 1 : Définir queryType et queryLanguage

Ajoutez les paramètres suivants au reste. Ces deux paramètres sont obligatoires.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

Le paramètre queryLanguage doit être une [langue prise en charge](/rest/api/searchservice/preview-api/search-documents#queryLanguage) et doit être cohérent avec tous les [analyseurs linguistiques](index-add-language-analyzers.md) affectés aux définitions de champ dans le schéma d’index. Par exemple, si vous indexez des chaînes en français à l’aide d’un analyseur de langue française (par exemple, « fr.microsoft » ou « fr.lucene »), alors le paramètre queryLanguage doit également être une variante de la langue française.

Dans une demande de requête, si vous utilisez également la [vérification orthographique](speller-how-to-add.md), le paramètre queryLanguage que vous définissez s’applique aussi bien au vérificateur d’orthographe qu’aux réponses et aux légendes. Aucune partie individuelle n’est remplacée. La vérification orthographique prend en charge [moins de langues](speller-how-to-add.md#supported-languages). Par conséquent, si vous utilisez cette fonctionnalité, vous devez définir queryLanguage sur une langue de cette liste.

Même si le contenu d’un index de recherche peut inclure plusieurs langues, l’entrée de requête est généralement formulée en une langue unique. Le moteur de recherche ne vérifie pas la compatibilité du paramètre queryLanguage, de l’analyseur linguistique et de la langue dans laquelle le contenu est composé. Veillez donc à limiter les requêtes en conséquence pour éviter la production de résultats incorrects.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Étape 2 : Définir searchFields

Ajoutez searchFields à la demande. Ceci est facultatif, mais fortement recommandé.

```json
"searchFields": "HotelName,Category,Description",
```

Le paramètre searchFields permet d'identifier les passages dont la « similarité sémantique » avec la requête doit être évaluée. Dans le cadre de la préversion, nous vous conseillons vivement de renseigner searchFields, car le modèle a besoin d’une indication sur les champs les plus importants à traiter.

Contrairement à d’autres paramètres, searchFields n’est pas nouveau. Vous utilisez peut-être déjà searchFields dans le code existant pour des requêtes Lucene simples ou complètes. Si c’est le cas, reportez-vous à la façon dont le paramètre est utilisé afin de pouvoir vérifier l’ordre des champs lorsque vous passez à un type de requête sémantique.

##### <a name="allowed-data-types"></a>Types de données autorisés

Lorsque vous définissez searchFields, choisissez uniquement les champs des [types de données pris en charge](/rest/api/searchservice/supported-data-types)suivants. Si vous incluez un champ non valide, aucune erreur ne survient, mais ce champ ne sera pas utilisé dans le classement sémantique.

| Type de données | Exemple tiré de l’index hotels-sample-index |
|-----------|----------------------------------|
| Edm.String | HotelName, Category, Description |
| Edm.ComplexType | Address.StreetNumber, Address.City, Address.StateProvince, Address.PostalCode |
| Collection(Edm.String) | Tags (liste de chaînes délimitées par des virgules) |

##### <a name="order-of-fields-in-searchfields"></a>Ordre des champs dans searchFields

L’ordre des champs est essentiel, car le classement sémantique limite la quantité de contenu qu’il peut traiter tout en conservant un temps de réponse raisonnable. Le contenu des champs au début de la liste est plus susceptible d’être inclus ; le contenu situé à la fin de la liste peut être tronqué si la limite maximale est atteinte. Pour plus d’informations, consultez [Prétraitement pendant le classement sémantique](semantic-ranking.md#pre-processing).

+ Si vous ne spécifiez qu’un seul champ, choisissez un champ descriptif où la réponse aux requêtes sémantiques pourrait se trouver (contenu principal d’un document, par exemple). 

+ Pour deux champs ou plus dans searchFields :

  + Le premier champ doit toujours être concis (titre ou nom, par exemple), idéalement une chaîne de moins de 25 mots.

  + Si l’index contient un champ URL lisible par l’utilisateur, tel que `www.domain.com/name-of-the-document-and-other-details` (plutôt que par la machine, tel que `www.domain.com/?id=23463&param=eis`), placez-le en deuxième position dans la liste (ou en première position en l’absence de champ de titre concis).

  + Faites suivre les champs ci-dessus d’autres champs descriptifs, où la réponse aux requêtes sémantiques peut être trouvée (contenu principal d’un document, par exemple).

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>Étape 3 : supprimer ou mettre entre guillemets les fonctionnalités de requête qui contournent le score de pertinence

Plusieurs fonctionnalités de requête dans Recherche cognitive ne subissent pas de notation de pertinence, et d’autres contournent complètement le moteur de recherche en texte intégral. Si votre logique de requête comprend les fonctionnalités suivantes, vous n’obtiendrez pas de score de pertinence ou de classement sémantique sur vos résultats :

+ Les filtres, les requêtes de recherche approximative et les expressions régulières effectuent une itération sur du texte qui n’a pas de jetons et analysent les correspondances textuelles dans le contenu. Les scores de recherche pour tous les formulaires de requête ci-dessus sont uniformes 1.0 et ne fourniront pas d’entrée significative pour le classement sémantique.

+ Le tri (clauses orderBy) sur des champs spécifiques remplacera également les scores de recherche et le score sémantique. Étant donné que le score sémantique est utilisé pour ordonner les résultats, même la logique de tri explicite renverra une erreur HTTP 400.

#### <a name="step-4-add-answers"></a>Étape 4 : ajouter des réponses

Vous pouvez ajouter des réponses (« answers ») si vous souhaitez inclure un traitement supplémentaire permettant de fournir une réponse. Pour plus d’informations sur ce paramètre, consultez [Comment spécifier des réponses sémantiques](semantic-answers.md).

```json
"answers": "extractive|count-3",
```

Les réponses (et les légendes) sont extraites à partir de passages trouvés dans les champs répertoriés dans le paramètre searchFields. C’est pourquoi vous souhaitez inclure des champs riches en contenu dans searchFields, afin d’obtenir les meilleures réponses dans une réponse. Les réponses ne sont pas garanties à chaque demande. La requête doit ressembler à une question, et le contenu doit inclure du texte qui ressemble à une réponse.

#### <a name="step-5-add-other-parameters"></a>Étape 5 : Ajouter d'autres paramètres

Définissez tous les autres paramètres que vous souhaitez inclure dans la requête. D’autres paramètres tels que [speller](speller-how-to-add.md), [select](search-query-odata-select.md) et « count » améliorent la qualité de la requête et la lisibilité de la réponse.

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

Le style de surbrillance est appliqué aux légendes dans la réponse. Vous pouvez utiliser le style par défaut ou personnaliser éventuellement le style de surbrillance appliqué aux légendes. Les légendes appliquent le format de surbrillance aux passages importants dans le document qui résument la réponse. Par défaut, il s’agit de `<em>`. Si vous souhaitez spécifier le type de mise en forme (par exemple un arrière-plan jaune), vous pouvez définir highlightPreTag et highlightPostTag.

## <a name="query-using-azure-sdks"></a>Interroger en utilisant les Kits de développement logiciel (SDK) Azure

Les versions bêta des kits de développement logiciel (SDK) Azure incluent la prise en charge de la recherche sémantique. Étant donné que les kits de développement logiciel (SDK) sont des versions bêta, il n’existe pas de documentation ou d’exemples, mais vous pouvez vous reporter à la section API REST ci-dessus pour obtenir des informations sur le fonctionnement des API.

| Kit de développement logiciel (SDK) Azure | Paquet |
|-----------|---------|
| .NET | [Azure.Search.Documents package 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>Évaluer la réponse

Comme pour toutes les requêtes, une réponse se compose de tous les champs marqués comme récupérables, ou seulement des champs listés dans le paramètre de sélection. Elle inclut le score de pertinence d’origine et peut également inclure un nombre, ou des résultats par lot, en fonction de la façon dont vous avez formulé la demande.

Dans une requête sémantique, la réponse possède des éléments supplémentaires : un nouveau score de pertinence sémantiquement classé, des légendes en texte brut et des mises en surbrillance, voire une réponse.

Dans une application cliente, vous pouvez structurer la page de recherche pour inclure une légende comme description de la correspondance, plutôt que l’intégralité du contenu d’un champ spécifique. Cela est utile lorsque les champs individuels sont trop denses pour la page des résultats de la recherche.

La réponse à la requête ci-dessus retourne la correspondance suivante comme premier choix. Les légendes sont retournées automatiquement, avec les versions en texte brut et en surbrillance. Les réponses sont omises de l’exemple, car il n’est pas possible de les déterminer pour cette requête et ce corpus.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Étapes suivantes

Rappelez-vous que le classement sémantique et les réponses sont générés à partir d’un jeu de résultats initial. Toute logique qui améliore la qualité des résultats initiaux sera reportée sur la recherche sémantique. À l’étape suivante, examinez les fonctionnalités qui participent aux résultats initiaux (y compris les analyseurs affectant la façon dont les chaînes sont tokenisées), les profils de score pouvant affiner les résultats et l’algorithme de pertinence par défaut.

+ [Analyseurs du traitement de texte](search-analyzers.md)
+ [Algorithme de classement de similarité](index-similarity-and-scoring.md)
+ [Profils de score](index-add-scoring-profiles.md)
+ [Vue d’ensemble de la recherche sémantique](semantic-search-overview.md)
+ [Algorithme de classement sémantique](semantic-ranking.md)
