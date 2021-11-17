---
title: Créer un index de recherche
titleSuffix: Azure Cognitive Search
description: Créer un index de recherche à l’aide du portail Azure, des API REST ou d’un SDK Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 59e883f318533ee768d6568c8f6ae673aa356ded
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063986"
---
# <a name="create-a-search-index-in-azure-cognitive-search"></a>Créer un index de recherche dans Recherche cognitive Azure

Les demandes de requête dans Recherche cognitive Azure ciblent le texte pouvant faire l’objet d’une recherche dans un index de recherche. Dans cet article, découvrez les étapes de définition et de publication d’un index de recherche à l’aide de l’une des modalités prises en charge par Recherche cognitive Azure. 

À moins que vous n’utilisiez un [indexeur](search-howto-create-indexers.md), la création d’un index et le remplissage d’un index sont des tâches distinctes. Pour les scénarios sans indexeur, l’étape suivante après la création de l’index est [l’importation de données](search-what-is-data-import.md). Pour plus de renseignements, consultez [Index de recherche dans Recherche cognitive Azure](search-what-is-an-index.md).

## <a name="prerequisites"></a>Prérequis

Les autorisations d’écriture sur le service de recherche sont requises pour la création et le chargement d’index. La plupart des opérations requièrent que vous fournissiez une [clé d’API d’administration](search-security-api-keys.md) sur la demande de création d’index. Si vous participez à la [préversion publique du contrôle d’accès en fonction du rôle](search-security-rbac.md) Azure Active Directory, vous pouvez également émettre votre demande en tant que membre du rôle Contributeur de recherche.

La création d’index est en grande partie un exercice de définition de schéma. Avant d’en créer un, vous devez disposer des éléments suivants :

+ Une idée claire des champs pouvant être consultés, récupérés, filtrés, à choix multiples et triés dans votre index.

  Les [affectations d’attributs de champ](search-what-is-an-index.md#index-attributes) déterminent la structure de stockage physique du service de recherche. Au cours de la conception et du développement, commencez avec des exemples de données afin de pouvoir supprimer et reconstruire facilement l’index à mesure que vous finalisez l’affectation des champs.

+ Un champ source qui identifie de façon unique chaque ligne, enregistrement ou élément dans les données sources. Si vous effectuez l’indexation à partir d’un objet Stockage Blob, le chemin de stockage est souvent utilisé comme clé de document. 

  Chaque index requiert un champ qui sert de *clé de document* (parfois appelé « ID de document ») et cette clé est mappée à un champ source contenant un identificateur unique. La possibilité d’identifier de façon unique des documents de recherche spécifiques est nécessaire pour récupérer un document spécifique dans l’index de recherche, et pour le traitement sélectif des données en tirant (pull) le bon document des données sources.

+ Emplacement de l’index. Le déplacement d’un index existant vers un autre service de recherche n’est pas pris en charge instantanément. Consultez à nouveau les spécifications de l’application et assurez-vous que le service de recherche existant, sa capacité et son emplacement, sont suffisants pour répondre à vos besoins.

Enfin, tous les niveaux de service ont des [limites d’index](search-limits-quotas-capacity.md#index-limits) sur le nombre d’objets que vous pouvez créer. Par exemple, si vous expérimentez le niveau Gratuit, vous ne pouvez avoir que trois index à la fois. Dans l’index lui-même, il existe des limites sur le nombre de champs et de collections complexes.

## <a name="allowed-updates"></a>Mises à jour autorisées

[Create Index](/rest/api/searchservice/create-index) est une opération qui crée des structures de données physiques (fichiers et index inversés) sur votre service de recherche. La possibilité d’appliquer des modifications à l’aide de [Update Index](/rest/api/searchservice/update-index) dépend du fait que la modification invalide ou non ces structures physiques. La plupart des attributs de champ ne peuvent pas être modifiés une fois que le champ est créé dans votre index.

Pour réduire l’attrition dans le processus de conception, le tableau suivant décrit les éléments fixes et flexibles dans le schéma. La modification d’un élément fixe nécessite la reconstruction de l’index, alors que les éléments flexibles peuvent être modifiés à tout moment sans impacter l’implémentation physique. 

| Élément | Mise à jour autorisée |
|---------|----------------|
| Nom | Non |
| Clé | Non |
| Noms et types de champs | Non |
| Attributs de champ (consultable, filtrable, à choix multiples, triable) | Non |
| Attribut de champ (récupérable) | Oui |
| [Analyseur](search-analyzers.md) | Vous pouvez ajouter et modifier des analyseurs personnalisés dans l’index. En ce qui concerne les affectations de l’analyseur aux champs de chaîne, vous pouvez uniquement modifier « searchAnalyzer ». Toutes les autres affectations et modifications requièrent une reconstruction. |
| [Profils de score](index-add-scoring-profiles.md) | Oui |
| [Générateurs de suggestions](index-add-suggesters.md) | Non |
| [CORS (cross-origin remote scripting)](search-what-is-an-index.md#corsoptions) | Oui |
| [Chiffrement](search-security-manage-encryption-keys.md) | Oui |

> [!NOTE]
> Les [cartes de synonymes](search-synonyms.md) ne font pas partie d’une définition d’index. Les modifications apportées à une carte de synonymes n’ont aucun impact sur l’index de recherche physique.

## <a name="schema-checklist"></a>Check-list du schéma

Utilisez cette check-list pour vous aider à prendre les décisions de conception de votre index de recherche.

1. Examinez les [conventions d’affectation de noms](/rest/api/searchservice/naming-rules) afin que les noms d’index et de champs soient conformes aux règles d’affectation de noms.

1. Consultez les [types de données pris en charge](/rest/api/searchservice/supported-data-types). Le type de données aura un impact sur l’utilisation du champ. Par exemple, le contenu numérique peut être filtré, mais ne peut pas faire l’objet d’une recherche en texte intégral.

1. Identifiez un champ dans la source de données qui contient des valeurs uniques, en lui permettant de fonctionner en tant que champ clé dans votre index.

1. Identifiez les champs dans votre source de données qui peuvent contribuer au contenu pouvant faire l’objet d’une recherche dans l’index. Le contenu pouvant faire l’objet d’une recherche sont des chaînes courtes ou longues qui sont interrogées à l’aide du moteur de recherche en texte intégral. Si le contenu est détaillé (petites phrases ou plus grands morceaux), expérimentez des analyseurs différents pour voir comment le texte est tokénisé.

1. Identifiez les champs sources qui peuvent être utilisés comme filtres. Le contenu numérique et les champs de texte courts, en particulier ceux qui ont des valeurs répétées, constituent de bons choix. Lorsque vous utilisez des filtres, n’oubliez pas :

   + Les champs filtrables peuvent éventuellement être utilisés dans la navigation à facettes.

   + Les champs filtrables sont retournés dans un ordre arbitraire, donc pensez à les rendre également triables.

## <a name="formulate-a-request"></a>Formuler une demande

Lorsque vous êtes prêt à créer l’index, il existe plusieurs façons de procéder. Nous vous recommandons le portail Azure ou les API REST pour les premières étapes de développement et les tests de preuve de concept.

Pendant le développement, prévoyez des régénérations fréquentes. Comme les structures physiques sont créées dans le service, il est nécessaire de [supprimer et de recréer les index](search-howto-reindex.md) pour la plupart des modifications. Vous pouvez envisager de travailler sur une partie de vos données pour regénérer plus rapidement.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/indexer-portal)

La conception d’index via le portail applique des exigences et des règles de schéma pour des types de données spécifiques, telles que l’interdiction de la recherche en texte intégral sur les champs numériques. Le portail propose deux options pour créer un index de recherche : 

+ **Ajouter un index** est un éditeur incorporé pour la spécification d’un schéma d’index
+ [**Importation de données**](search-import-data-portal.md) est un assistant

L’Assistant permet d’effectuer des opérations supplémentaires en créant également un indexeur, une source de données et en chargeant des données. Si c’est plus qu’il n’en faut, vous pouvez simplement utiliser l’option **Ajouter un index** ou une autre approche.

La capture d’écran suivante montre où vous pouvez trouver **Ajouter un index** et **Importation de données** dans la barre de commandes. Une fois qu’un index a été créé, vous pouvez le retrouver dans l’onglet **Index**.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Add index command" border="true":::

> [!Tip]
> Après avoir créé un index dans le portail, vous pouvez copier la représentation JSON et l’ajouter au code de votre application.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Create Index (REST)** ](/rest/api/searchservice/create-index) est utilisé pour créer un index. Postman et Visual Studio Code (avec une extension pour Recherche cognitive Azure) peuvent tous deux fonctionner comme client d’index de recherche. En utilisant l’un ou l’autre de ces outils, vous pouvez vous connecter à votre service de recherche et envoyer des requêtes :

+ [Créer un index de recherche à l’aide de REST et de Postman](search-get-started-rest.md)
+ [Bien démarrer avec Visual Studio Code et Recherche cognitive Azure](search-get-started-vs-code.md)

L’API REST fournit des valeurs par défaut pour l’affectation de champs. Par exemple, tous les champs Edm.String peuvent faire l’objet d’une recherche par défaut. Les attributs sont affichés en intégralité ci-dessous à des fins d’illustration, mais vous pouvez omettre l’affectation dans les cas où les valeurs par défaut s’appliquent.

Reportez-vous aux [opérations d’index (REST)](/rest/api/searchservice/index-operations) pour obtenir de l’aide dans la formulation de requêtes d’index.

```json
POST https://[servicename].search.windows.net/indexes?api-version=[api-version] 
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "retrievable": true, "searchable": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Address", "type": "Edm.ComplexType", 
      "fields": [
          { "name": "StreetAddress", "type": "Edm.String", "retrievable": true, "filterable": false, "sortable": false, "facetable": false, "searchable": true },
          { "name": "City", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true },
          { "name": "StateProvince", "type": "Edm.String", "retrievable": true, "searchable": true, "filterable": true, "sortable": true, "facetable": true }
        ]
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ]
  }
}
```

### <a name="net-sdk"></a>[**Kit de développement logiciel (SDK) .NET**](#tab/kstore-dotnet)

Le SDK Azure pour .NET a [**SearchIndexClient**](/dotnet/api/azure.search.documents.indexes.searchindexclient) avec des méthodes de création et de mise à jour des index.

```csharp
// Create the index
string indexName = "hotels";
SearchIndex index = new SearchIndex(indexName)
{
    Fields =
    {
        new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
        new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
        new SearchableField("description") { AnalyzerName = LexicalAnalyzerName.EnLucene },
        new SearchableField("descriptionFr") { AnalyzerName = LexicalAnalyzerName.FrLucene }
        new ComplexField("address")
        {
            Fields =
            {
                new SearchableField("streetAddress"),
                new SearchableField("city") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("stateProvince") { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("country") { SynonymMapNames = new[] { synonymMapName }, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new SearchableField("postalCode") { IsFilterable = true, IsSortable = true, IsFacetable = true }
            }
        }
    }
};

await indexClient.CreateIndexAsync(index);
```

Pour plus d’exemples, consultez [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11).

### <a name="other-sdks"></a>[**Autres SDK**](#tab/other-sdks)

Pour Recherche cognitive, les Kits de développement logiciel (SDK) Azure implémentent des fonctionnalités généralement disponibles. Ainsi, vous pouvez utiliser n’importe lequel des Kits de développement logiciel (SDK) pour créer un index de recherche. Ils fournissent tous un **SearchIndexClient** qui contient des méthodes pour la création et la mise à jour d’index.

| Azure SDK | Client | Exemples |
|-----------|--------|----------|
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Index](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

---

## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens suivants pour vous familiariser avec le chargement d’un index avec des données.

+ [Vue d’ensemble de l’importation des données](search-what-is-data-import.md)

+ [Ajout, mise à jour ou suppression de documents (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 