---
title: Créer une base de connaissances avec REST
titleSuffix: Azure Cognitive Search
description: Utilisez l’API REST et Postman pour créer une base de connaissances Recherche cognitive Azure pour conserver les enrichissements IA d’un ensemble de compétences.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/03/2021
ms.openlocfilehash: ad30dc4f59816f286f6ffe40909b76410e963c16
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555753"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Créer une base de connaissances à l’aide de REST et Postman

La base de connaissances est une fonctionnalité du service Recherche cognitive Azure, qui envoie le résultat d’un ensemble de compétences provenant d’un [pipeline d’enrichissement par IA](cognitive-search-concept-intro.md) au Stockage Azure pour exploration des connaissances, analyse des données ou traitement en aval. Une fois la base de connaissances remplie, vous pouvez utiliser des outils comme [Navigateur de stockage](knowledge-store-view-storage-explorer.md) ou [Power BI](knowledge-store-connect-power-bi.md) pour explorer le contenu.

Dans cet article, vous allez utiliser l’API REST pour ingérer, enrichir et explorer un ensemble d’évaluations de séjours hôteliers par des clients dans une base de connaissances dans le Stockage Azure. Le résultat final est une base de connaissances qui contient le contenu texte d’origine extrait de la source, plus le contenu généré par l’IA, qui comprend un score de sentiment, l’extraction des expressions clés, la détection de la langue et la traduction texte des commentaires des clients non anglophones.

Pour que le jeu de données initial soit disponible, les avis de l’hôtel sont d’abord importés dans le Stockage Blob Azure. Post-traitement, les résultats sont enregistrés sous la forme d’une base de connaissances dans le service Stockage Table Azure.

> [!NOTE]
> Cet article suppose la présence de l’[application de bureau Postman](https://www.getpostman.com/). Le [code source](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) de cet article comprend une collection Postman contenant toutes les requêtes. 

## <a name="create-services-and-load-data"></a>Créer des services et charger des données

Cet exercice utilise les services Recherche cognitive Azure, Stockage Blob Azure et [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’IA. 

En raison de la taille réduite de la charge de travail, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions par jour. Une petite charge de travail signifie que vous pouvez ignorer la création ou l’attachement d’une ressource Cognitive Services.

1. [Téléchargez le fichier HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ce fichier CSV contient des données d’avis d’hôtel (issues de Kaggle.com). Il rassemble 19 commentaires de clients relatifs à un seul hôtel. 

1. [Créez un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Vous allez utiliser le Stockage Azure à la fois pour le contenu brut à importer et pour la base de connaissances qui est le résultat final.

   Choisissez le type de compte **StorageV2 (usage général v2)** .

1. Dans la ressource Stockage Azure, utilisez **Navigateur de stockage** pour créer un conteneur de blobs nommé **hotel-reviews**.

1. Sélectionnez **Charger** en haut de la page pour charger le fichier **HotelReviews-Free.csv** que vous avez téléchargé à l’étape précédente.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="Capture d’écran de Navigateur de stockage avec le fichier chargé et le volet de navigation gauche" border="true":::

1. Vous avez presque terminé avec cette ressource, mais avant de quitter ces pages, sélectionnez **Clés d’accès** dans le volet de navigation gauche pour obtenir une chaîne de connexion afin de pouvoir récupérer ces données en utilisant l’indexeur.

1. Dans **Clés d’accès**, sélectionnez **Afficher les clés** en haut de la page pour afficher les chaînes de connexion, puis copiez la chaîne de connexion pour key1 ou key2.

   Une chaîne de connexion a le format suivant : `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

## <a name="configure-requests"></a>Configurer les demandes

1. Téléchargez [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) depuis GitHub et décompressez le fichier. Il y a plusieurs collections dans le dépôt. Vous allez utiliser la collection du dossier knowledge-store.

1. Dans Postman, sélectionnez **File** > **Import** pour importer le fichier KnowledgeStore.postman_collection.json.

1. Sélectionnez l’onglet **Collections**, puis le bouton **...** (points de suspension).

1. Sélectionnez **Modifier**.

   ![Application Postman montrant la navigation](media/knowledge-store-create-rest/postman-edit-menu.png "Accéder au menu Edition dans Postman")

1. Dans la boîte de dialogue **Edit**, sélectionnez l’onglet **Variables**. 

Sous l’onglet **Variables**, vous pouvez ajouter les valeurs que Postman récupère chaque fois qu’il trouve une variable spécifique entre double accolades. Par exemple, Postman remplace le symbole `{{admin-key}}` par la valeur actuelle que vous avez définie pour `admin-key`. Postman effectue la substitution dans les URL, les en-têtes, le corps de la requête, etc. 

Des variables sont définies pour les services Azure, les connexions de service et les noms d’objets. Remplacez les valeurs des espaces réservés pour le service et la connexion par les valeurs réelles pour votre service de recherche et votre compte de stockage. Vous pouvez trouver ces valeurs dans le portail Azure.

+ Pour obtenir les valeurs de `search-service-name` et `search-service-admin-key`, accédez au service Recherche cognitive Azure dans le portail et copiez les valeurs depuis les pages **Vue d’ensemble** et **Clés**.

+ Pour obtenir les valeurs de `storage-account-name` et `storage-account-connection-string`, consultez la page **Clés d’accès**.

![Onglet Variables de l’application Postman](media/knowledge-store-create-rest/postman-variables-window.png "Fenêtre des variables de Postman")

| Variable    | Comment les obtenir |
|-------------|-----------------|
| `admin-key` | Dans la page **Clés** du service Recherche cognitive Azure.  |
| `api-version` | Laisser **2020-06-30**. |
| `datasource-name` | Laisser **hotel-reviews-ds**. | 
| `indexer-name` | Laisser **hotel-reviews-ixr**. | 
| `index-name` | Laisser **hotel-reviews-ix**. | 
| `search-service-name` | Nom du service Recherche cognitive Azure. Si l’URL est `https://mySearchService.search.windows.net`, la valeur que vous devez entrer est `mySearchService`. | 
| `skillset-name` | Laisser **hotel-reviews-ss**. | 
| `storage-account-name` | Nom du compte de stockage Azure. | 
| `storage-connection-string` | Dans le compte de stockage, sous l’onglet **Clés d’accès**, sélectionnez **Afficher la clé** en haut de la page, puis copiez **key1** > **Chaîne de connexion**. | 
| `storage-container-name` | Laisser **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Passer en revue la collection de requêtes dans Postman

Les magasins de connaissances sont définis dans des ensembles de compétences, qui sont à leur tour attachés aux indexeurs. Pour créer un magasin de connaissances, vous devez créer tous les objets en amont, y compris un index, une source de données, des compétences et un indexeur. Bien qu’un index ne soit pas lié à un magasin de connaissances, un indexeur l’exige pour l’exécution. Vous allez donc en créer un en tant que composant requis de l’indexeur.

Quand vous créez une base de connaissances, vous allez émettre quatre requêtes HTTP : 

+ **Requête PUT pour créer l’index** : Cet index va contenir les données avec possibilité de recherche retournées dans les demandes des requêtes émises pour votre service de recherche.

+ **Requête POST pour créer la source de données** : Cette source de données fournit des informations de connexion à l’indexeur. L’indexeur se connecte à votre compte de stockage Azure pour récupérer les exemples de données.

+ **Requête PUT pour créer l’ensemble de compétences** : L’ensemble de compétences spécifie les enrichissements qui sont appliqués à vos données. Il spécifie également la structure de la base de connaissances.

+ **Requête PUT pour créer l’index** : L’exécution de l’indexeur lit les données, applique l’ensemble de compétences, crée la base de connaissances dans le Stockage Azure et enregistre les résultats. Vous devez exécuter cette requête en dernier.

Les variables que vous avez configurées précédemment sont utilisées dans les en-têtes et l’URL. La capture d’écran suivante pour la demande de création d’index montre où ces variables apparaissent dans la demande.

![Capture d’écran présentant l’interface de Postman pour les en-têtes](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Toutes les requêtes dans les en-têtes du jeu d’éléments de collecte `api-key` et `Content-type`, qui sont requis. Si Postman reconnaît une variable, celle-ci se apparaît en orange, comme pour `{{admin-key}}` dans la capture d’écran précédente. Si la variable est mal orthographiée, elle apparaît en rouge.
>

## <a name="create-an-index"></a>Création d'un index

Utilisez [Create index (API REST)](/rest/api/searchservice/create-index) pour créer un index de recherche sur le service de recherche. Un index de recherche n’est pas lié à une base de connaissances, mais l’indexeur nécessite la création d’un index. L’index de recherche va contenir le même contenu que la base de connaissances. Si vous voulez une autre approche pour explorer votre contenu, vous pouvez interroger cet index en envoyant des demandes de requête à votre service de recherche. 

Créez l’index en émettant une requête PUT vers `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Le schéma de l’index est fourni dans le corps de la quête.

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "translated_text", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "sentiment", "type": "Collection(Edm.String)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}
```

Cette définition d’index est une combinaison de données que vous voulez présenter à l’utilisateur. Elle comprend des champs mappés directement au fichier CSV (nom de l’hôtel, date de l’évaluation, etc.) et des champs créés par l’ensemble de compétences (sentiment, expressions clés, langue et texte traduit).

Sélectionnez **Send** (Envoyer) pour émettre la requête PUT.

L’état `201 - Created` doit alors s’afficher. Si vous un autre état s’affiche, dans le volet **Body**, recherchez une réponse JSON qui contient un message d’erreur. Si vous utilisez un service de recherche gratuit, vérifiez que vous disposez de suffisamment d’espace pour les nouveaux objets (le service gratuit a un maximum de trois).

À ce stade, l’index est créé mais il n’est pas chargé. L’importation de documents se produit ultérieurement lorsque vous exécutez l’indexeur. 

## <a name="create-a-data-source"></a>Création d'une source de données

Ensuite, connectez Recherche cognitive Azure aux données relatives aux hôtels que vous avez stockées dans le stockage Blob. Pour créer la source de données, envoyez une requête POST [Create Data Source](/rest/api/searchservice/create-data-source) à `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. 

Dans Postman, accédez à la requête **Create Datasource** (Créer une source de données), puis au volet **Body** (Corps). Le code suivant doit s’afficher :

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Sélectionnez **Send** pour émettre la requête POST. 

## <a name="create-a-skillset"></a>Créer un ensemble de compétences 

L’étape suivante crée l’ensemble de compétences, qui spécifie à la fois les améliorations à appliquer et la base de connaissances où les résultats sont stockés. Cette demande envoie une requête PUT [Create Skillset](/rest/api/searchservice/create-skillset) (Créer un ensemble de compétences) à `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.

Il y deux grands objets de plus haut niveau : `skills` et `knowledgeStore` :

+ « skills » est l’ensemble de compétences. Chaque objet contenu dans l’objet `skills` est un service d’enrichissement. Chaque service d’enrichissement comporte `inputs` et `outputs`. `LanguageDetectionSkill` présente une sortie `targetName` qui correspond à `Language`. La valeur de ce nœud est utilisée par la plupart des autres compétences comme entrée. La source est `document/Language`. La possibilité d’utiliser la sortie d’un nœud comme entrée d’un autre nœud est encore plus évidente dans `ShaperSkill`, qui spécifie les flux de données dans les tables de la base de connaissances.

+ « knowledgeStore » comprend la chaîne de connexion au compte de stockage et une série de projections. Chaque élément de projection dans le tableau des projections produit une table dans le Stockage Azure. Les colonnes de la table vont inclure des colonnes générées utilisées pour les liaisons croisées et les champs de contenu. Elles vont aussi inclure des champs de contenu créés lors de l’enrichissement.

  Les projections acceptent des formes de données comme entrée. Vous pouvez fournir les formes via une compétence Modélisateur, en mappant la sortie des compétences à une projection, ou bien vous pouvez définir des formes incluses dans chaque élément de projection. 

Pour générer l’ensemble de compétences, sélectionnez le bouton **Send** dans Postman pour effectuer un PUT de la requête :

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, translate text, extract key phrases, and score sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/language" }
            ],
            "outputs": [
                { "name": "sentiment", "targetName": "sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
            "context": "/document/reviews_text/pages/*",
            "defaultFromLanguageCode": null,
            "defaultToLanguageCode": "en",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" }
            ],
            "outputs": [
                { "name": "translatedText", "targetName": "translated_text" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "reviews_username",  "source": "/document/reviews_username" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                {
                "name": "pages",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                    {
                    "name": "languageCode",
                    "source": "/document/language"
                    },
                    {
                    "name": "translatedText",
                    "source": "/document/reviews_text/pages/*/translated_text"
                    },
                    { 
                    "name": "sentiment",
                    "source": "/document/reviews_text/pages/*/sentiment"
                    },
                    {
                    "name": "keyPhrases",
                    "source": "/document/reviews_text/pages/*/keyphrases/*"
                    },
                    {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*"
                    }
                ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviews1Document", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviews2Pages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviews3KeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyPhrases/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviews4InlineProjectionDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_username", "source": "/document/reviews_username"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews5InlineProjectionPages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "Sentiment", "source": "/document/reviews_text/pages/*/sentiment"},
                            { "name": "LanguageCode", "source": "/document/language"},
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases"},
                            { "name": "TranslatedText", "source": "/document/reviews_text/pages/*/translated_text"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviews6InlineProjectionKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-an-indexer"></a>Créer un indexeur

L’étape finale est la requête [Create Indexer](/rest/api/searchservice/create-indexer). L’indexeur lit les données et active l’ensemble de compétences. La définition de l’indexeur fait référence à plusieurs autres ressources que vous avez déjà créées : la source de données, l’index et l’ensemble de compétences. 

+ L’objet `parameters/configuration` contrôle la manière dont l’indexeur ingère les données. Dans ce cas, les données d’entrée se trouvent dans un même fichier CSV qui comporte une ligne d’en-tête et des valeurs séparées par des virgules. 

+ Les mappages de champs créent « AzureSearch_DocumentKey », qui est un identificateur unique pour chaque document généré par l’indexeur d’objets blob (basé sur le chemin de stockage des métadonnées). 

+ Les mappages de champs de sortie spécifient comment les champs enrichis sont mappés aux champs d’un index de recherche. Les mappages de champs de sortie ne sont pas utilisés dans les bases de connaissances (les bases de connaissances utilisent des formes et des projections pour exprimer les structures de données physiques).

Sélectionnez **Send** dans Postman pour créer et exécuter l’indexeur. L’importation de données, l’exécution de l’ensemble de compétences et la création de la base de connaissances se produisent dans cette étape.

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="check-status"></a>Vérification du statut

Une fois que vous avez envoyé chaque demande, le service de recherche doit répondre avec un message de réussite 201. Si vous recevez des erreurs, revérifiez vos variables et que le service de recherche dispose de suffisamment d’espace pour le nouvel index, l’indexeur, la source de données et l’ensemble de compétences (le niveau gratuit est limité à trois pour chacun).

Dans le portail Azure, accédez à la page **Vue d’ensemble** du service Recherche cognitive Azure. Sélectionnez l’onglet **Indexeurs**, puis **hotels-reviews-ixr**. Au bout d’une ou deux minutes, l’état doit passer de « En cours » à « Réussite », sans aucune erreur ni avertissement.

## <a name="check-tables-in-storage-browser"></a>Vérifier les tables dans Navigateur de stockage

Dans le portail Azure, basculez sur votre compte de stockage Azure et utilisez **Navigateur de stockage** pour visualiser les nouvelles tables. Vous devez voir six tables, une pour chaque projection définie dans l’ensemble de compétences.

Chaque table est générée avec les ID nécessaires à la liaison croisée des tables dans les requêtes. Quand vous ouvrez une table, faites défiler au-delà de ces champs pour voir les champs de contenu ajoutés par le pipeline.

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Capture d’écran des tables de la base de connaissances dans Navigateur de stockage" border="true":::

Dans cette procédure pas à pas, la base de connaissances se compose de différentes tables montrant différentes façons de mettre en forme et de structurer une table. Les tables un à trois utilisent la sortie d’une compétence Modélisateur pour déterminer les colonnes et les lignes. Les tables quatre à six sont créées à partir d’instructions de mise en forme incluse, incorporées dans la projection elle-même. Vous pouvez utiliser l’une ou l’autre approche pour obtenir le même résultat.

| Table de charge de travail | Description |
|-------|-------------|
| hotelReviews1Document | Contient des champs provenant du fichier CSV, comme reviews_date et reviews_text. |
| hotelReviews2Pages | Contient des champs enrichis créés par l’ensemble de compétences, comme le score de sentiment et le texte traduit. |
| hotelReviews3KeyPhrases | Contient une longue liste des expressions clés uniquement. |
| hotelReviews4InlineProjectionDocument | Alternative à la première table, utilisant la mise en forme incluse au lieu de la compétence Modélisateur pour mettre en forme les données de la projection. |
| hotelReviews5InlineProjectionPages | Alternative à la deuxième table, utilisant une mise en forme incluse. |
| hotelreviews6InlineProjectionKeyPhrases | Alternative à la troisième table, utilisant une mise en forme incluse. |

## <a name="clean-up"></a>Nettoyer

Lorsque vous travaillez dans votre propre abonnement, il est judicieux à la fin d’un projet de déterminer si vous avez encore besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite.

> [!TIP]
> Si vous voulez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur **hotel-reviews-idxr** et les objets associés pour les recréer. La suppression de l’indexeur remet le compteur de transactions quotidiennes gratuites à zéro.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez enrichi vos données en utilisant Cognitive Services et que vous avez projeté les résultats dans une base de connaissances, vous pouvez utiliser Navigateur de stockage ou d’autres applications pour explorer votre jeu de données enrichi.

Pour savoir comment explorer cette base de connaissances à l’aide de Navigateur de stockage, consultez cette procédure pas à pas :

> [!div class="nextstepaction"]
> [Afficher avec Navigateur de stockage](knowledge-store-view-storage-explorer.md)