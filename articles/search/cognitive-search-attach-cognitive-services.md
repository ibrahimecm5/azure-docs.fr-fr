---
title: Joindre Cognitive Services à un ensemble de compétences
titleSuffix: Azure Cognitive Search
description: Découvrez comment attacher une ressource Cognitive Services multiservice à un pipeline d’enrichissement par IA dans Recherche cognitive Azure.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: ce369b60fa1b12823acdb1f5045e403bcaa3f5dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037660"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Attacher une ressource Cognitive Services à un ensemble de compétences dans Recherche cognitive Azure

Lorsque vous configurez un [pipeline d’enrichissement par IA](cognitive-search-concept-intro.md) dans la Recherche cognitive Azure, vous pouvez enrichir un nombre limité de documents gratuitement. Pour les charges de travail plus importantes et plus fréquentes, vous devez attacher une [**ressource Cognitive Services multiservice facturable**](../cognitive-services/cognitive-services-apis-create-account.md). Une ressource multiservice référence « Cognitive Services » en tant qu’offre (et non en tant que services individuels) dont l’accès est octroyé via une seule clé API.

Une clé de ressource multi-service est spécifiée dans un ensemble de compétences et permet à Microsoft de vous facturer l’utilisation des API suivantes :

+ [Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour l’analyse d’images et la reconnaissance optique de caractères (OCR)
+ [Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour la détection de la langue, la reconnaissance des entités, l’analyse des sentiments et l’extraction des expressions clés
+ [Traduction de texte](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> L’enrichissement par IA offre une petite quantité de traitement gratuit, ce qui vous permet d’effectuer des opérations courtes sans avoir à joindre une ressource Cognitive Services. Les enrichissements libres sont de 20 documents par jour, par indexeur. Vous pouvez [réinitialiser l’indexeur](search-howto-run-reset-indexers.md) pour réinitialiser le compteur si vous souhaitez répéter une opération.

## <a name="azure-portal"></a>[**Portail Azure**](#tab/cogkey-portal)

1. Créez une [ressource Cognitive Services multi-service](../cognitive-services/cognitive-services-apis-create-account.md) dans la [même région](#same-region-requirement) que votre service de recherche.

1. Ajoutez la clé à une définition d’ensemble de compétences :

   + Si vous utilisez l'[assistant iIportation de données](search-import-data-portal.md), entrez la clé à la deuxième étape, « Ajouter des enrichissements par IA ».

   + Si vous ajoutez la clé à un nouvel ensemble de compétences ou à un ensemble de compétences existant, indiquez la clé sous l’onglet **Cognitive Services**.

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="Capture d’écran de la page Clé" border="true":::

## <a name="rest"></a>[**REST**](#tab/cogkey-rest)

1. Créez une [ressource Cognitive Services multi-service](../cognitive-services/cognitive-services-apis-create-account.md) dans la [même région](#same-region-requirement) que votre service de recherche.

1. Créez ou mettez à jour un ensemble de compétences, en spécifiant la section `cognitiveServices` dans le corps de la [demande d’ensemble de compétences](/rest/api/searchservice/create-skillset) :

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="net-sdk"></a>[**Kit de développement logiciel (SDK) .NET**](#tab/cogkey-dotnet)

L’extrait de code suivant provient de [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs), ajusté par souci de concision.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## <a name="how-the-key-is-used"></a>Utilisation de la clé

La clé est utilisée pour la facturation, mais pas pour les connexions. Pour les connexions, un service de recherche se connecte par le biais du réseau interne à une ressource Cognitive Services colocalisée dans la [même région physique](https://azure.microsoft.com/global-infrastructure/services/?products=search). 

La facturation basée sur les clés s’applique lorsque les appels d’API à des ressources Cognitive Services dépassent 20 appels d’API par indexeur, par jour. Vous pouvez réinitialiser l’indexeur après chaque appel de l’indexeur pour réinitialiser le compteur d’API, mais le nombre maximal d’appels passés gratuits est limité à 20.

 Pendant l’enrichissement par IA, la Recherche cognitive appelle les API Cognitive Services pour les [compétences intégrées](cognitive-search-predefined-skills.md) basées sur la vision par ordinateur, la traduction de texte et l’analyse de texte. Les compétences intégrées qui effectuent des appels backend à Cognitive Services incluent la [liaison d’entités](cognitive-search-skill-entity-linking-v3.md), la [reconnaissance d’entité](cognitive-search-skill-entity-recognition-v3.md), l’[analyse d’image](cognitive-search-skill-image-analysis.md), l’[extraction de phrases clés](cognitive-search-skill-keyphrases.md), la [détection de langue](cognitive-search-skill-language-detection.md), la [reconnaissance optique de caractères](cognitive-search-skill-ocr.md), la [détection de PII](cognitive-search-skill-pii-detection.md), le [sentiment](cognitive-search-skill-sentiment-v3.md) et la [traduction de texte](cognitive-search-skill-text-translation.md).

Vous pouvez omettre la clé et la section Cognitive Services pour les ensembles de compétences qui se composent uniquement de compétences personnalisées ou de compétences utilitaires. Vous n’êtes pas tenu de spécifier la propriété si votre utilisation des compétences facturables est inférieure à 20 transactions par indexeur et par jour.

### <a name="exceptions-and-special-cases"></a>Exceptions et cas spéciaux

+ Les compétences utilitaires qui n’appellent pas Cognitive Services (y compris, les [compétences conditionnels](cognitive-search-skill-conditional.md), l’[extraction de documents](cognitive-search-skill-document-extraction.md), le [modélisateur](cognitive-search-skill-shaper.md), la [fusion de texte](cognitive-search-skill-textmerger.md), et le [fractionnement de texte](cognitive-search-skill-textsplit.md)) ne sont pas facturables. 

+ La fonctionnalité [Recherche d’entité personnalisée](cognitive-search-skill-custom-entity-lookup.md) est mesurée par la Recherche cognitive Azure, et non par Cognitive Services. Toutefois, elle nécessite une clé de ressource Cognitive Services pour déverrouiller les transactions au-delà de 20 transactions par indexeur et par jour. Pour cette compétence uniquement, même si la clé de ressource permet de débloquer le nombre de transactions, elle n’est pas liée à la facturation.

### <a name="other-costs-of-ai-enrichment"></a>Autres coûts de l’enrichissement par IA

L’extraction d’images est une opération de la Recherche cognitive Azure qui se produit lorsque les documents sont décodés avant l’enrichissement. L’extraction d’images est facturable pour tous les niveaux, à l’exception des 20 extractions quotidiennes gratuites du niveau Gratuit. Les coûts d’extraction d’images s’appliquent aux fichiers image contenus dans les objets blob, aux images incorporées dans d’autres fichiers (fichiers PDF et autres fichiers d’application) ainsi qu’aux images extraites à l’aide de l’[extraction de documents](cognitive-search-skill-document-extraction.md). Pour connaître les prix appliqués à l’extraction d’images, voir la [page de tarification du service Recherche cognitive Azure](https://azure.microsoft.com/pricing/details/search/).

L’extraction de texte a lieu également durant la phase de [craquage de document](search-indexer-overview.md#document-cracking). Elle n’est pas facturable.

> [!TIP]
> Pour réduire le coût du traitement des ensembles de compétences, activez l’[enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md) afin de mettre en cache et de réutiliser les enrichissements non affectés par les changements apportés à un ensemble de compétences. La mise en cache nécessite Stockage Azure (consultez les [tarifs](https://azure.microsoft.com/pricing/details/storage/blobs/)). Toutefois, le coût cumulé de l’exécution des ensembles de compétences est moindre si les enrichissements existants peuvent être réutilisés, en particulier pour les ensembles de compétences qui utilisent l’analyse et l’extraction d’images.

## <a name="same-region-requirement"></a>Exigence de même région

Recherche cognitive et Cognitive Services doivent être présents dans la même région physique, comme indiqué sur la page [Disponibilité des produits](https://azure.microsoft.com/global-infrastructure/services/?products=search). La plupart des régions qui offrent Recherche cognitive proposent également Cognitive Services.

Si vous essayez l’enrichissement par IA dans une région qui ne présente pas les deux services, le message suivant s’affiche : « La clé fournie n’est pas une clé de type CognitiveServices valide pour la région de votre service de recherche ».

> [!NOTE]
> Certaines compétences intégrées sont basées sur des services cognitifs non régionaux (par exemple la [compétence de traduction de texte](cognitive-search-skill-text-translation.md)). L’utilisation d’une compétence non régionale signifie que votre requête peut être desservie dans une région autre que la région de la Recherche cognitive Azure. Pour plus d’informations sur les services non régionaux, consultez la page [Produit Cognitive Services par région](https://aka.ms/allinoneregioninfo).

## <a name="example-estimate-costs"></a>Exemple : Estimer les coûts

Pour estimer les coûts associés à l’indexation de Recherche cognitive, commencez par vous représenter à quoi ressemble un document moyen afin de pouvoir donner une approximation. Par exemple, vous pourriez faire une approximation :

+ 1 000 fichiers PDF.
+ Six pages par fichier.
+ Une image par page (6 000 images).
+ 3 000 caractères par page.

Supposons un pipeline consistant en un craquage de document PDF, une extraction d’image et de texte, une reconnaissance optique de caractères (OCR) d’images, et une reconnaissance d’entités d’organisations.

Les prix indiqués dans cet article sont hypothétiques. Ils sont utilisés pour illustrer le processus d’estimation. Vos coûts peuvent être inférieurs. Pour obtenir les prix réels des transactions, voir [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pour le décodage de documents avec contenu de texte et d’image, l’extraction de texte est actuellement gratuite. Pour 6 000 images, supposez un prix de 1 USD pour chaque tranche de 1 000 images extraites. Cela revient à un coût de 6,00 USD pour cette étape.

2. Pour la reconnaissance optique de caractères (OCR) de 6 000 images en anglais, la qualification cognitive OCR utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape vous coûterait 15 $.

3. Pour l’extraction d’entité, vous auriez un total de trois enregistrements texte par page. Chaque enregistrement contient 1 000 caractères. Trois enregistrements texte par page multipliés par 6 000 pages équivalent à 18 000 enregistrements texte. À raison de 2 $ par lot de 1 000 enregistrements texte, cette étape vous coûterait 36 $.

En additionnant tout cela, l’ingestion de 1 000 documents PDF de ce type avec l’ensemble de qualifications décrit vous reviendrait à environ 57 USD.

## <a name="next-steps"></a>Étapes suivantes

+ [Page de tarification de Recherche cognitive Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
