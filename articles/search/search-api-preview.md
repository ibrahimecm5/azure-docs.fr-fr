---
title: Liste des fonctionnalités en préversion
titleSuffix: Azure Cognitive Search
description: Les fonctionnalités d’évaluation sont publiées afin que les clients puissent fournir des commentaires sur leur conception et leur utilité. Cet article est une liste complète de toutes les fonctionnalités d’évaluation actuelles.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: a7a056a04963a8dd48a791c3851674b9abf54841
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501359"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Fonctionnalités d’évaluation dans Recherche cognitive Azure

Cet article est une liste complète de toutes les fonctionnalités disponibles en préversion publique. Les fonctionnalités d’évaluation sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les fonctionnalités d’évaluation qui passent en disponibilité générale sont supprimées de cette liste. Si une fonctionnalité n’est pas listée ci-dessous, vous pouvez supposer qu’elle est en disponibilité générale. Pour accéder aux annonces concernant la disponibilité générale, consultez [Mises à jour de service](https://azure.microsoft.com/updates/?product=search) ou [Nouveautés](whats-new.md).

|Fonctionnalité&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Description | Disponibilité  |
|---------|------------------|-------------|---------------|
|  [**Indexeur Azure Files**](search-file-storage-integration.md) | Source de données d’indexeur | Ajoute la prise en charge de l’API REST afin de permettre la création d’indexeurs pour [Azure Files](https://azure.microsoft.com/services/storage/files/) | Préversion publique, [Recherche API REST 2021-04-30 - Préversion](/rest/api/searchservice/index-preview) |
| [**Recherche API REST 2021-04-30 - Préversion**](/rest/api/searchservice/index-preview) | Sécurité | Modifie la [Création ou la Mise à jour de la source de données](/rest/api/searchservice/preview-api/create-or-update-data-source) pour prendre en charge les identités managées sous le Répertoire actif Azure, pour les indexeurs qui se connectent à des sources de données externes. | Préversion publique, [Recherche API REST 2021-04-30 - Préversion](/rest/api/searchservice/index-preview) |
| [**Prise en charge d’Azure RBAC**](search-security-rbac.md) | Sécurité | Utilisez de nouveaux rôles intégrés pour contrôler l’accès aux index et à l’indexation, en éliminant ou en réduisant la dépendance sur les clés API. | Préversion publique ([sur demande](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up)). Une fois votre abonnement intégré, utilisez le Portail Azure ou l’API REST de gestion version 2021-04-01 - préversion pour configurer un service de recherche pour l’authentification du plan de données. |
| [**API REST Gestion 2021-04-01 - Préversion**](/rest/api/searchmanagement/) | Sécurité | Modifie la [Création ou la Mise à jour du service](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) pour prendre en charge les nouvelles [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions). | Préversion publique, [API REST Gestion ](/rest/api/searchmanagement/), API version 2021-04-01 - Préversion .|
| [**Réinitialiser des documents**](search-howto-run-reset-indexers.md) | Indexation | Retraite des documents de recherche sélectionnés individuellement dans les charges de travail d’indexeur. | Utilisez [API REST Réinitialisation de documents](/rest/api/searchservice/preview-api/reset-documents), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion. |
|  [**Connecteurs Power Query**](search-how-to-index-power-query-data-sources.md) | Source de données d’indexeur | Les indexeurs peuvent désormais indexer à partir d’autres plateformes cloud. Si vous utilisez un indexeur pour analyser des sources de données externes pour indexation, vous pouvez désormais utiliser des connecteurs Power Query pour vous connecter à Amazon Redshift, Elasticsearch, PostgreSQL, Salesforce Objects, Salesforce Reports, Smartsheet et Snowflake. | L’[inscription](https://aka.ms/azure-cognitive-search/indexer-preview) est nécessaire pour que la prise en charge puisse être activée pour votre abonnement sur le back-end. Configurez cette source de données à l’aide de [Créer ou mettre à jour une source de données](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion ou le Portail Azure.|
| [**Indexeur SharePoint Online**](search-howto-index-sharepoint-online.md) | Source de données d’indexeur | Nouvelle source de données pour l’indexation de contenu SharePoint basée sur un indexeur. | L’[inscription](https://aka.ms/azure-cognitive-search/indexer-preview) est nécessaire pour que la prise en charge puisse être activée pour votre abonnement sur le back-end. Configurez cette source de données à l’aide de [Créer ou mettre à jour une source de données](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion ou le Portail Azure. |
|  [**Source de données d’indexeur MySQL**](search-howto-index-mysql.md) | Source de données d’indexeur | Indexez le contenu et les métadonnées des sources de données MySQL Azure.| L’[inscription](https://aka.ms/azure-cognitive-search/indexer-preview) est nécessaire pour que la prise en charge puisse être activée pour votre abonnement sur le back-end. Configurez cette source de données à l’aide de [Créer ou mettre à jour une source de données](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30 ou 2020-06-30 - Préversion [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) et le Portail Azure. |
| [**Cosmos Indexeur DB : API MongoDB, API Gremlin**](search-howto-index-cosmosdb.md) | Source de données d’indexeur | Pour Cosmos DB, l’API SQL est généralement disponible, mais les API MongoDB et Gremlin sont en préversion. | Pour MongoDB et Gremlin, [inscrivez-vous au préalable](https://aka.ms/azure-cognitive-search/indexer-preview) afin que la prise en charge puisse être activée pour votre abonnement sur le serveur principal. Les sources de données MongoDB peuvent être configurées dans le portail. Configurez cette source de données à l’aide de [Créer ou mettre à jour une source de données](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion. |
| [**Suppression réversible native d’objets blob**](search-howto-index-changed-deleted-blobs.md) | Source de données d’indexeur | L’indexeur Stockage Blob Azure dans Recherche cognitive Azure reconnaît les objets blob qui sont dans un état de suppression réversible, et supprime le document de recherche correspondant durant l’indexation. | Configurez cette source de données à l’aide de [Créer ou mettre à jour une source de données](/rest/api/searchservice/preview-api/create-or-update-data-source), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion. |
| [**Recherche sémantique**](semantic-search-overview.md) | Pertinence (scoring) | Classement sémantique des résultats, des légendes et des réponses. | Configurez la recherche sémantique à l’aide de [Documents de recherche](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion et Navigateur de recherche (portail). |
| [**Vérificateur d’orthographe**](cognitive-search-aml-skill.md) | Requête | Correction orthographique facultative des entrées de terme de requête pour les requêtes simples, complètes et sémantiques. | [Rechercher API REST Préversion](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion et Navigateur de recherche (portail). |
| [**Normaliseurs**](search-normalizers.md) | Requête |  Les normaliseurs fournissent un prétraitement de texte simple : casse toujours identique, suppression des accents et conversion ASCII, sans appeler la chaîne d’analyse en texte intégral.| Utilisez [Documents de recherche](/rest/api/searchservice/preview-api/search-documents), API versions 2021-04-30 - Préversion ou 2020-06-30 - Préversion.|
| [**Paramètre featuresMode**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Pertinence (scoring) | Expansion du score de pertinence pour inclure des détails : score de similarité par champ, fréquence de terme par champ, et nombre de jetons uniques correspondants par champ. Vous pouvez consommer ces points de données dans des [solutions de scoring personnalisées](https://github.com/Azure-Samples/search-ranking-tutorial). | Ajoutez ce paramètre de requête à l’aide de [Documents de recherche](/rest/api/searchservice/preview-api/search-documents) API versions 2021-04-30 - Préversion, 2020-06-30 - Préversion ou 2019-05-06 - Préversion. |
| [**Compétence Azure Machine Learning (AML)**](cognitive-search-aml-skill.md) | Enrichissement AI (compétences) | Nouveau type de compétence permettant d'intégrer un point de terminaison d'inférence à partir d'Azure Machine Learning. Pour bien démarrer, suivez [ce tutoriel](cognitive-search-tutorial-aml-custom-skill.md). | Utilisez [Rechercher API REST Préversion](/rest/api/searchservice/), API versions 2021-04-30 - Préversion, 2020-06-30 - Préversion ou 2019-05-06 - Préversion. Également disponible sur le portail, dans la conception de compétences, si les services Recherche cognitive et Azure ML sont déployés sur le même abonnement. |
| [**Enrichissement incrémentiel**](cognitive-search-incremental-indexing-conceptual.md) | Enrichissement IA (compétences) | Ajoute la mise en cache à un pipeline d'enrichissement, ce qui vous permet de réutiliser la sortie existante si une modification ciblée, telle que la mise à jour d'un ensemble de compétences ou d'un autre objet, ne change pas le contenu. La mise en cache s’applique uniquement aux documents enrichis générés par un ensemble de compétences.| Ajoutez ce paramètre de configuration à l’aide de [Créer ou mettre à jour l’indexeur API REST Préversion](/rest/api/searchservice/create-indexer), API versions 2021-04-30 - Préversion, 2020-06-30 - Préversion ou 2019-05-06 - Préversion. |
| [**Sessions de débogage**](cognitive-search-debug-session.md) | Portail, enrichissement IA (compétences) | Éditeur d’ensemble de compétences dans la session utilisé pour examiner et résoudre les problèmes liés aux ensembles de compétences. Les correctifs appliqués au cours d’une session de débogage peuvent être enregistrés dans un ensemble de compétences dans le service. | Portail uniquement, à l’aide des liens en milieu de page Vue d’ensemble pour ouvrir une session de débogage. |
| [**moreLikeThis**](search-more-like-this.md) | Requête | Recherche les documents correspondant à un document spécifique. Cette fonctionnalité existait dans les préversions antérieures. | Ajoutez ce paramètre de requête dans les appels [Documents de recherche API REST Préversion](/rest/api/searchservice/search-documents) avec API versions 2021-04-30 - Préversion, 2020-06-30 - Préversion, 2019-05-06 - Préversion, 2016-09-01 - Préversion ou 2017-11-11 - Préversion. |

## <a name="how-to-call-a-preview-rest-api"></a>Comment appeler une API REST en préversion

Le service Recherche cognitive Azure publie toujours les fonctionnalités expérimentales par le biais de l’API REST, puis par le biais des préversions du SDK .NET.

Les fonctionnalités en préversion sont disponibles pour le test et l’expérimentation en vue de recueillir des commentaires sur la conception et la mise en œuvre de la fonctionnalité. Ainsi, les fonctionnalités en préversion peuvent changer au fil du temps, voire de manière à empêcher la compatibilité descendante. Ce comportement diffère de celui des fonctionnalités dans la version à disposition générale, qui sont stables et peu susceptibles de changer à l’exception d’améliorations et de correctifs peu importants pour la compatibilité descendante. En outre, les fonctionnalités en préversion ne sont pas toujours intégrées à une version à disposition générale.

Alors que certaines fonctionnalités d’évaluation peuvent être disponibles dans le portail et le SDK .NET, l’API REST dispose toujours de fonctionnalités d’évaluation.

+ Pour les opérations de recherche, [ **`2021-04-30-Preview`**](/rest/api/searchservice/index-preview) correspond à la préversion actuelle.

+ Pour les opérations de gestion, [ **`2021-04-01-Preview`**](/rest/api/searchmanagement/management-api-versions) correspond à la préversion actuelle.

Les préversions plus anciennes sont toujours opérationnelles mais deviennent obsolètes au fil du temps. Si votre code appelle `api-version=2019-05-06-Preview` ou `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, ces appels sont toujours valides, mais ces versions n’incluront pas de nouvelles fonctionnalités et les correctifs de bogues ne seront pas garantis.

La syntaxe de l’exemple suivant illustre un appel à l’API en préversion.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2021-04-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Le service de Recherche cognitive Azure est disponible dans plusieurs versions et bibliothèques clientes. Pour plus d’informations, consultez [Versions d’API](search-api-versions.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation de référence relative à l’API REST en préversion du service de recherche. Si vous rencontrez des problèmes, sollicitez notre aide sur [Stack Overflow](https://stackoverflow.com/) ou [contactez le support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Informations de référence sur l’API REST du service de recherche (préversion)](/rest/api/searchservice/index-preview)