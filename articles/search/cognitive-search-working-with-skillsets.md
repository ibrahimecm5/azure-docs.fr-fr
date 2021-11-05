---
title: Concepts des ensembles de compétences
titleSuffix: Azure Cognitive Search
description: Les ensembles de compétences consistent à créer un pipeline d’enrichissement de l’IA dans Recherche cognitive Azure. Découvrez les concepts et détails importants relatifs à la composition d’ensembles de compétences.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 08bf1a57c4b7d4905693c3f11964feeb04c8beef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014858"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Concepts des ensembles de compétences dans Recherche cognitive Azure

Cet article s’adresse aux développeurs qui ont besoin de comprendre plus en profondeur les concepts et de la composition des ensembles de compétences. Il suppose une bonne connaissance des concepts généraux et des workflows de l’[enrichissement par IA](cognitive-search-concept-intro.md).

Un ensemble de compétences est une ressource réutilisable dans la Recherche cognitive Azure, qui est attachée à [un indexeur](search-indexer-overview.md). Il contient une ou plusieurs compétences, lesquelles sont des opérations atomiques qui appellent l’IA intégrée ou un traitement personnalisé externe sur les documents récupérés à partir d’une source de données externe.

Du début à la fin du traitement de l’ensemble de compétences, les compétences effectuent des opérations de lecture et d’écriture dans un document enrichi. Initialement, un document enrichi n’est que le contenu brut extrait d’une source de données. Toutefois, à chaque exécution de compétence, il gagne en structure et en substance. Enfin, les nœuds d’un document enrichi sont ensuite [mappés à des champs](cognitive-search-output-field-mapping.md) dans un index de recherche, ou [mappés à des projections](knowledge-store-projection-overview.md) dans une base de connaissances. Ainsi, le contenu est routé de manière appropriée pour être interrogé ou consommé par d’autres applications.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline avec ensemble de compétences" border="false":::

## <a name="skillset-definition"></a>Définition du jeu de compétences

Un ensemble de compétences rassemble une ou une plusieurs *compétences* qui représentent une opération d’enrichissement atomique, par exemple la traduction d’un texte, l’extraction de phrases clés ou la reconnaissance optique de caractères à partir d’un fichier image. Les compétences peuvent être des [compétences intégrées](cognitive-search-predefined-skills.md) provenant de Microsoft, ou des [compétences personnalisées](cognitive-search-create-custom-skill-example.md) qui contiennent vos modèles ou votre logique de traitement. Il produit des documents enrichis qui sont soit consommés durant l’indexation, soit projetés vers une base de connaissances.

Les compétences ont un type, un contexte ainsi que des entrées et des sorties qui sont souvent chaînées. L’exemple suivant illustre deux [compétences intégrées](cognitive-search-predefined-skills.md) qui fonctionnent ensemble. Il introduit une partie de la terminologie relative à la définition d’un ensemble de compétences. 

+ La compétence n° 1 est la [compétence Fractionnement de texte](cognitive-search-skill-textsplit.md). Elle accepte le contenu du champ source « reviews_text » en tant qu’entrée, et divise ce contenu en « pages » de 5 000 caractères en tant que sortie. La division d’un texte volumineux en blocs plus petits peut produire de meilleurs résultats durant le traitement du langage naturel.

+ La compétence 2 est une [compétence de détection des sentiments](cognitive-search-skill-sentiment.md) qui accepte « pages » comme entrée et génère un nouveau champ appelé « Sentiment » qui contient les résultats de l’analyse des sentiments.

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

Voici les points clés à noter pour l’exemple ci-dessus : les entrées et les sorties sont des paires nom-valeur, vous pouvez faire correspondre les sorties d’une compétence aux entrées de compétence en aval, toutes les compétences ont un contexte qui détermine le moment où le traitement a lieu dans l’arborescence d’enrichissement.

Pour plus d’informations sur la façon dont les entrées et les sorties sont formulées, consultez [Guide pratique pour référencer des annotations](cognitive-search-concept-annotations-syntax.md).

## <a name="enrichment-tree"></a>Arborescence d’enrichissements

Un document enrichi est une structure de données temporaire sous forme d’arborescence créée au moment de l’exécution de l’ensemble de compétences. Cette structure de données collecte tous les changements introduits par les compétences, et les représente dans une hiérarchie de nœuds adressables. Les nœuds incluent également les champs non enrichis passés en l’état à partir de la source de données externe. Un document enrichi existe pour la durée de l’exécution de l’ensemble de compétences, mais il peut être mis en cache ou conservé dans une base de connaissances. 

Au départ, un document enrichi correspond simplement au contenu extrait d’une source de données durant le [*craquage de document*](search-indexer-overview.md#document-cracking), où le texte et les images sont extraits de la source à des fins d’analyse du langage ou des images. 

Le contenu initial est le *nœud racine* (`document\content`) et il s’agit généralement d’un document entier ou d’une image normalisée extraite d’une source de données lors du craquage de document. La façon dont il est articulé dans une arborescence d’enrichissement varie pour chaque type de source de données. Le tableau suivant montre l’état d’un document entrant dans le pipeline d’enrichissement pour plusieurs sources de données prises en charge :

|Data Source\Parsing Mode|Default|JSON, JSON Lines & CSV|
|---|---|---|
|Stockage Blob|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

Au fur et à mesure de l’exécution des compétences, la sortie est ajoutée à l’arborescence d’enrichissement sous forme de nouveaux nœuds. Ces nœuds peuvent ensuite être utilisés en tant qu’entrées pour les compétences en aval. Ils seront finalement projetés dans une base de connaissances ou mappés à des champs d’index. Les compétences qui créent du contenu, par exemple les chaînes traduites, écrivent leur sortie dans le document enrichi. De même, les compétences qui consomment la sortie des compétences en amont lisent le document enrichi pour obtenir les entrées nécessaires.

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="Lecture et écriture des compétences à partir de l’arborescence d’enrichissement" border="false":::

Une arborescence d’enrichissement se compose d’un contenu extrait et de métadonnées extraites de la source ainsi que de tous les nœuds créés par une compétence, par exemple `translated_text` de la [compétence de traduction de texte](cognitive-search-skill-text-translation.md), `locations` de la [compétence de reconnaissance d’entités](cognitive-search-skill-entity-recognition-v3.md) ou `keyPhrases` de la [compétence d’extraction de phrases clés](cognitive-search-skill-keyphrases.md). Bien que vous puissiez [visualiser et utiliser une arborescence d’enrichissement](cognitive-search-debug-session.md) via un éditeur visuel, il s’agit principalement d’une structure interne. 

Les enrichissements ne sont pas mutables : une fois créés, les nœuds ne peuvent pas être modifiés. Plus votre ensemble de compétences est complexe, plus votre arborescence d’enrichissements l’est aussi. Toutefois, vous n’avez pas besoin d’inclure systématiquement tous les nœuds de l’arborescence d’enrichissements dans l’index ou la base de connaissances. Vous pouvez conserver de manière sélective uniquement un sous-ensemble des sorties d’enrichissement pour ne garder que ce que vous comptez utiliser.

Dans la mesure où les entrées et les sorties d’une compétence lisent et écrivent dans les arborescences d’enrichissement, l’une des tâches que vous allez effectuer dans le cadre de la conception d’un ensemble de compétences consiste à créer des [mappages de champs de sortie](cognitive-search-output-field-mapping.md) qui déplacent le contenu hors de l’arborescence d’enrichissement vers un champ d’un index de recherche. De même, si vous créez une base de connaissances, vous pouvez mapper les sorties à des [modélisations](knowledge-store-projection-shape.md) affectées à des projections.

> [!NOTE]
> Le format de l’arborescence d’enrichissement permet au pipeline d’enrichissement d’attacher les métadonnées même aux types de données primitifs. Les métadonnées ne représentent pas un objet JSON valide. Toutefois, elles peuvent être projetées vers un format JSON valide dans les définitions de projection d’une base de connaissances. Pour plus d’informations, consultez [Compétence Modélisateur](cognitive-search-skill-shaper.md).

## <a name="context"></a>Context

Chaque compétence a un contexte, lequel peut correspondre à l’ensemble du document (`/document`) ou à un nœud situé plus bas dans l’arborescence (`/document/countries/`). Un contexte détermine :

+ Nombre d’exécutions de la compétence pour une seule valeur (une seule fois par champ, par document) ou pour les valeurs de contexte de la collection de types, où l’ajout de `/*` entraîne l’appel de la compétence, une fois par instance de la collection. 

+ Déclaration de sortie, ou emplacement d’ajout des sorties de compétence dans l’arborescence d’enrichissement. Les sorties sont toujours ajoutées à l’arborescence en tant qu’enfants du nœud de contexte.

+ La forme des entrées. Pour les collections multiniveaux, la définition du contexte sur la collection parente affecte la modélisation de l’entrée de la compétence. Par exemple, si vous avez une arborescence d’enrichissement avec une liste de pays/régions, chacun/chacune enrichi(e) d’une liste d’états contenant une liste de codes postaux, la façon dont vous définissez le contexte détermine la façon dont l’entrée est interprétée.

|Context|Entrée|Forme de l’entrée|Appel de compétence|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Liste de tous les codes postaux du pays/de la région |Une fois par pays/région |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |Liste de tous les codes postaux de l’état | Une fois par combinaison pays/région et État|

## <a name="enrichment-example"></a>Exemple d’enrichissement

En prenant l’[ensemble de compétences relatif aux avis sur les hôtels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) comme point de référence, cet exemple explique comment une [arborescence d’enrichissement](cognitive-search-working-with-skillsets.md#enrichment-tree) évolue via l’exécution des compétences à l’aide de diagrammes conceptuels.

Cet exemple montre également que :

+ Le contexte et les entrées d’une compétence déterminent le nombre d’exécutions de celle-ci
+ Le contexte affecte la forme de l’entrée.

Dans cet exemple, les champs sources d’un fichier CSV incluent les avis des clients sur les hôtels (« reviews_text ») et les évaluations (« reviews_rating »). L’indexeur ajoute les champs de métadonnées à partir de Stockage Blob, alors que les compétences ajoutent le texte traduit, les scores de sentiments et la détection de phrases clés.

Dans l’exemple des avis sur les hôtels, un « document » situé dans le processus d’enrichissement représente un seul avis sur un hôtel.

> [!TIP]
> Vous pouvez créer un index de recherche et une base de connaissances pour ces données dans le [portail Azure](knowledge-store-create-portal.md) ou via [Postman et les API REST](knowledge-store-create-rest.md). Vous pouvez également utiliser les [sessions de débogage](cognitive-search-debug-session.md) pour obtenir des insights sur la composition des ensembles de compétences, les dépendances et les effets sur une arborescence d’enrichissement. Les images de cet article sont extraites de sessions de débogage.

D’un point de vue conceptuel, l’arborescence d’enrichissement initiale se présente comme suit :

![arborescence d’enrichissement après le craquage de document](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Arborescence d’enrichissement après le craquage de document et avant l’exécution de la compétence")

Le nœud racine de tous les enrichissements est `"/document"`. Quand vous utilisez des indexeurs d’objets blob, le nœud `"/document"` contient les nœuds enfants `"/document/content"` et `"/document/normalized_images"`. Si vous utilisez des données CSV, comme dans cet exemple, les noms de colonne sont mappés aux nœuds figurant sous `"/document"`.

### <a name="skill-1-split-skill"></a>Compétence n° 1 : Division

Lorsque le contenu source est constitué de gros blocs de texte, il est utile de le diviser en plus petits composants pour plus de précision dans la détection de la langue, du sentiment et des expressions clés. Deux grains sont disponibles : les pages et les phrases. Une page se compose d’environ 5000 caractères.

La compétence de division de texte se trouve généralement d’abord dans un ensemble de compétences.

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

Avec le contexte de compétence `"/document/reviews_text"`, la compétence de division s’exécute une seule fois pour `reviews_text`. La sortie de la compétence est une liste où `reviews_text` est segmenté en 5 000 séquences de caractères. La sortie de la compétence de division est nommée `pages` et elle est ajoutée à l’arborescence d’enrichissements. Avec `targetName`, vous pouvez renommer une sortie de compétence avant de l’ajouter à l’arborescence d’enrichissements.

L’arborescence d’enrichissements comporte maintenant un nouveau nœud, situé sous le contexte de la compétence. Ce nœud peut être utilisé pour d’autres compétences, projections ou mappages de champs de sortie. 
 
![arborescence d’enrichissement après la compétence n° 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "L’arborescence d’enrichissement après la compétence n° 1 s’exécute")

Pour accéder à un enrichissement qui a été ajouté à un nœud par une compétence, vous devez indiquer le chemin complet de l’enrichissement. Par exemple, si vous souhaitez utiliser le texte du nœud ```pages``` comme entrée dans une autre compétence, vous devez spécifier le chemin de cette façon : ```"/document/reviews_text/pages/*"```. Pour plus d’informations sur les chemins, consultez [Référencer des annotations](cognitive-search-concept-annotations-syntax.md).

### <a name="skill-2-language-detection"></a>Compétence n° 2 : Détection de la langue

Les documents d’avis sur les hôtels comprennent des commentaires clients exprimés en plusieurs langues. La compétence de détection de langue détermine la langue utilisée. Le résultat est ensuite passé à l’extraction de phrases clés et à la détection de sentiments (hors illustration), en tenant compte de la langue au moment de la détection des sentiments et des phrases.

La compétence de détection de la langue est la troisième compétence (compétence n° 3) définie dans l’ensemble de compétences, mais c’est la compétence suivante à exécuter. Comme elle n’est pas bloquée dans l’attente d’entrées, elle s’exécute parallèlement à la compétence précédente. À l’instar de la compétence de division qui l’a précédée, la compétence de détection de la langue est également appelée une fois pour chaque document. L’arborescence d’enrichissements comporte désormais un nouveau nœud pour la langue.

 ![arborescence d’enrichissement après la compétence n° 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Arborescence d’enrichissement après l’exécution de la compétence n°2")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>Compétences n°3 et n°4 : Analyse des sentiments et détection de phrases clés

Les commentaires des clients reflètent un éventail d’expériences positives et négatives. La compétence d’analyse des sentiments analyse les commentaires et affecte un score parmi un continuum de nombres négatifs à positifs, ou une valeur neutre si le sentiment est indéterminé. Parallèlement à l’analyse des sentiments, la détection de phrases clés identifie et extrait les mots ainsi que les phrases courtes qui semblent importants.

Compte tenu du contexte de `/document/reviews_text/pages/*`, les compétences d’analyse des sentiments et des phrases clés sont appelées une seule fois pour chacun des éléments de la collection `pages`. La sortie de la compétence est un nœud placé sous l’élément page associé. 

Vous pouvez maintenant examiner le reste des compétences dans l’ensemble de compétences et regarder comment l’arborescence des enrichissements continue de croître à l’exécution de chaque compétence. Certaines compétences, telles que la compétence de fusion et la compétence de modélisation, créent également des nœuds, mais utilisent uniquement les données de nœuds existants et ne créent pas d’enrichissements supplémentaires.

![arborescence d’enrichissement après toutes les compétences](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Arborescence d’enrichissement après toutes les compétences")

Les couleurs des connecteurs dans l’arborescence ci-dessus indiquent que les enrichissements ont été créés par différentes compétences, c’est-à-dire que les nœuds devront être traités individuellement et qu’ils ne feront pas partie de l’objet retourné lors de la sélection du nœud parent.

### <a name="skill-5-shaper-skill"></a>Compétence n°5 : Compétence Modélisateur

Si la sortie comprend une [base de connaissances](knowledge-store-concept-intro.md), ajoutez une [compétence Modélisateur](cognitive-search-skill-shaper.md) en tant que dernière étape. La compétence Modélisateur crée des modélisations de données à partir de nœuds dans une arborescence d’enrichissement. Par exemple, vous pouvez être amené à regrouper plusieurs nœuds en une seule modélisation. Vous pouvez ensuite projeter cette modélisation sous forme de table (les nœuds deviennent les colonnes d’une table), en passant la modélisation par son nom à une projection de table.

Il est facile d’utiliser la compétence Modélisateur, car elle se concentre sur la modélisation à travers une seule compétence. Vous pouvez également opter pour une modélisation inline au sein de projections individuelles. La compétence Modélisateur n’ajoute ou n’enlève rien à une arborescence d’enrichissement, elle n’est donc pas visualisée. À la place, vous pouvez considérer la compétence Modélisateur comme un moyen de réorganiser l’arborescence d’enrichissement dont vous disposez déjà. Sur le plan conceptuel, cela s’apparente à la création de vues à partir de tables d’une base de données.

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
    },
    {
      "name": "reviews_text",
      "source": "/document/reviews_text"
    },
    {
      "name": "reviews_title",
      "source": "/document/reviews_title"
    },
    {
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

En vous appuyant sur cette introduction et un exemple, essayez de créer votre premier ensemble de compétences à l’aide des [compétences intégrées](cognitive-search-predefined-skills.md).

> [!div class="nextstepaction"]
> [Créer votre premier ensemble de compétences](cognitive-search-defining-skillset.md)
