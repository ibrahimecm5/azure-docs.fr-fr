---
title: Exécuter ou réinitialiser des indexeurs
titleSuffix: Azure Cognitive Search
description: Exécutez entièrement les indexeurs ou réinitialisez un indexeur, des compétences ou des documents individuels pour actualiser tout ou partie d’un index de recherche ou d’une base de connaissances.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: e29c511a59d8b446b497a8fd4ff393c9a9c683e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449295"
---
# <a name="run-or-reset-indexers-skills-or-documents"></a>Exécuter ou réinitialiser des indexeurs, des compétences ou des documents

Les indexeurs peuvent être appelés de trois façons : à la demande, selon une planification ou lors de la [création de l’indexeur](/rest/api/searchservice/create-indexer). Après l’exécution initiale, un indexeur effectue le suivi des documents de recherche qui ont été indexés à l’aide d’une « limite supérieure » interne. Le marqueur n’est jamais exposé dans l’API, mais en interne, l’indexeur sait où l’indexation s’est arrêtée afin de pouvoir reprendre là où elle s’est arrêtée lors de la prochaine exécution.

Vous pouvez effacer la limite supérieure en réinitialisant l’indexeur si vous souhaitez recommencer le traitement à partir de zéro. Les API de réinitialisation sont disponibles à des niveaux décroissants dans la hiérarchie d’objets :

+ Le corpus de recherche entier (utilisez [Réinitialiser les indexeurs](#reset-indexers))
+ Un document ou une liste de documents spécifique (utilisez [Réinitialiser les documents [préversion]](#reset-docs))
+ Une compétence ou un enrichissement spécifiques (utilisez [Réinitialiser les compétences – Préversion](#reset-skills))

Les API de réinitialisation sont utilisées pour actualiser le contenu mis en cache (applicable dans les scénarios d’[enrichissement par IA](cognitive-search-concept-intro.md)) ou pour effacer la limite supérieure et reconstruire l’index.

La réinitialisation, suivie d’une exécution, permet de retraiter les documents existants et les nouveaux documents, mais ne supprime pas les documents de recherche orphelins de l’index de recherche qui ont été créés lors des exécutions précédentes. Pour plus d’informations sur la suppression, consultez [Ajouter, mettre à jour ou supprimer des documents](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-run-indexers"></a>Comment exécuter des indexeurs

[Créer un indexeur](/rest/api/searchservice/create-indexer) crée et exécute l’indexeur, sauf si vous le créez dans un état désactivé ("disabled": true). La première exécution prend un peu plus de temps, car elle couvre également la création de l’objet.

[Exécuter l’indexeur](/rest/api/searchservice/run-indexer) détectera et traitera uniquement ce qui est nécessaire pour synchroniser l’index de recherche avec la source de données. Stockage Blob intègre la détection des modifications. D’autres sources de données, telles qu’Azure SQL ou Cosmos DB, doivent être configurées pour la détection des modifications avant que l’indexeur ne puisse lire uniquement les nouvelles lignes et les lignes mises à jour.

Vous pouvez exécuter un indexeur à l’aide de l’une des approches suivantes :

+ Portail Azure, à l’aide de la commande **Run** sur la page de l’indexeur
+ [Exécuter l’indexeur (REST)](/rest/api/searchservice/run-indexer)
+ [Méthode RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) dans le Kit de développement logiciel (SDK) Azure .NET (ou à l’aide de la méthode RunIndexers équivalente dans un autre SDK)

## <a name="indexer-execution"></a>Exécution de l’indexeur

L’exécution de l’indexeur est soumise aux limites suivantes :

+ Le nombre maximal de travaux de l’indexeur est de 1 par réplica.

  Si l’exécution de l’indexeur est déjà à pleine capacité, vous recevrez cette notification : « Échec de l’exécution de l’indexeur "\<indexer-name\>" », avec l’erreur : « Une autre invocation de l’indexeur est actuellement en cours ; les invocations simultanées ne sont pas autorisées. »

+ Le temps d’exécution maximal est de 2 heures si vous utilisez des compétences, ou 24 heures sans. 

  Si vous [indexez un jeu de données volumineux](search-howto-large-index.md), vous pouvez allonger le traitement en planifiant l’indexeur. Le niveau Gratuit a des limites de temps d’exécution inférieures. Pour obtenir la liste complète, consultez [Limites de l’indexeur](search-limits-quotas-capacity.md#indexer-limits).

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Réinitialiser un indexeur

La réinitialisation d’un indexeur est un processus global. Dans l’index de recherche, tout document de recherche qui a été initialement rempli par l’indexeur est marqué pour un traitement complet. Tout nouveau document trouvé dans la source sous-jacente sera ajouté à l’index en tant que document de recherche. Si l’indexeur est configuré pour utiliser un ensemble de compétences et la [mise en cache](search-howto-incremental-index.md), l’ensemble de compétences est réexécuté et le cache est actualisé.

Vous pouvez réinitialiser un indexeur en utilisant l’une de ces approches, puis exécuter un indexeur en utilisant l’une des méthodes mentionnées ci-dessus :

+ Portail Azure, à l’aide de la commande **Reset** sur la page de l’indexeur
+ [Réinitialiser l’indexeur (REST)](/rest/api/searchservice/reset-indexer)
+ [Méthode ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) dans le Kit de développement logiciel (SDK) Azure .NET (ou à l’aide de la méthode ResetIndexers équivalente dans un autre SDK)

L’indicateur de réinitialisation est effacé une fois l’exécution terminée. Toute logique normale de détection des modifications applicable à votre source de données reprendra lors de la prochaine exécution, en récupérant toute autre valeur nouvelle ou mise à jour dans le reste du jeu de données.

> [!NOTE]
> Une demande de réinitialisation détermine ce qui est retraité (indexeur, compétence ou document), mais n’affecte pas autrement le comportement d’exécution de l’indexeur. Si l’indexeur a des paramètres d’exécution, des mappages de champs, une mise en cache, des options de lot, etc., ces paramètres sont tous en vigueur lorsque vous exécutez un indexeur après l’avoir réinitialisé.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Réinitialiser les compétences (préversion)

Pour les indexeurs qui ont des ensembles de compétences, vous pouvez réinitialiser des compétences spécifiques pour forcer le traitement de cette compétence et de toute compétence en aval qui dépend de sa sortie. Les [enrichissements mis en cache](search-howto-incremental-index.md) sont également actualisés. La réinitialisation des compétences invalide les résultats des compétences mis en cache, ce qui est utile quand une nouvelle version d’une compétence est déployée et que vous souhaitez que l’indexeur réexécute cette compétence pour tous les documents. 

[Réinitialiser les compétences](/rest/api/searchservice/preview-api/reset-skills) est disponible par le biais de REST **`api-version=2020-06-30-Preview`** ou version ultérieure.

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Vous pouvez spécifier des compétences individuelles, comme indiqué dans l’exemple ci-dessus, mais si l’une de ces compétences requiert la sortie de compétences non répertoriées (« #2 » à « #4 »), les compétences non répertoriées sont exécutées, sauf si le cache peut fournir les informations nécessaires. Pour que cela soit vrai, les enrichissements mis en cache pour les compétences « #2 » à « #4 » ne doivent pas avoir de dépendances vis-à-vis de la compétence « #1 » (répertoriée pour la réinitialisation).

Si aucune compétence n’est spécifiée, la totalité de l’ensemble de compétences est exécutée et, si la mise en cache est activée, le cache est également actualisé.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Réinitialiser les documents (préversion)

L’[API Réinitialiser les documents](/rest/api/searchservice/preview-api/reset-documents) accepte une liste de clés de document afin que vous puissiez actualiser des documents spécifiques. S’ils sont spécifiés, les paramètres de réinitialisation deviennent le seul déterminant de ce qui est traité, quelles que soient les autres modifications apportées aux données sous-jacentes. Par exemple, si 20 blobs ont été ajoutés ou mis à jour depuis la dernière exécution de l’indexeur, mais que vous ne réinitialisez qu’un seul document, seul celui-ci sera traité.

Pour chaque document, tous les champs de ce document de recherche sont actualisés avec les valeurs de la source de données. Vous ne pouvez pas choisir les champs à actualiser. 

Si le document est enrichi par le biais d’un ensemble de compétences et contient des données en cache, l’ensemble de compétences est appelé uniquement pour les documents spécifiés, et les données en cache sont mises à jour pour les documents retraités.

Lorsque vous testez cette API pour la première fois, les API suivantes vous aideront à valider et à tester les comportements :

+ [Obtenir l’état de l’indexeur](/rest/api/searchservice/get-indexer-status) avec la version d’API **`api-version=2020-06-30-Preview`** ou ultérieure pour vérifier l’état de réinitialisation et l’état d’exécution. Vous pouvez trouver des informations sur la demande de réinitialisation à la fin de la réponse d’état.
+ [Réinitialiser les documents](/rest/api/searchservice/preview-api/reset-documents) avec la version d’API **`api-version=2020-06-30-Preview`** ou ultérieure pour spécifier les documents à traiter.
+ [Exécuter l’indexeur](/rest/api/searchservice/run-indexer) pour exécuter l’indexeur (toute version d’API).
+ [Recherchez des documents](/rest/api/searchservice/search-documents) pour rechercher les valeurs mises à jour, ainsi que pour renvoyer les clés de document si vous n’êtes pas sûr de la valeur. Utilisez `"select": "<field names>"` si vous souhaitez limiter les champs qui apparaissent dans la réponse.

### <a name="formulate-and-send-the-reset-request"></a>Formuler et envoyer la demande de réinitialisation

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Les clés de document fournies dans la demande sont des valeurs de l’index de recherche, qui peuvent être différentes des champs correspondants dans la source de données. Si vous n’êtes pas sûr de la valeur de clé, [envoyez une requête](search-query-create.md) pour retourner la valeur. Vous pouvez utiliser `select` pour retourner uniquement le champ Clé du document.

Pour les blobs analysés dans plusieurs documents de recherche (par exemple, si vous avez utilisé [jsonLines ou jsonArrays](search-howto-index-json-blobs.md) ou [delimitedText](search-howto-index-csv-blobs.md)) comme mode d’analyse, la clé de document est générée par l’indexeur et peut vous être inconnue. Dans ce cas, une requête portant sur la clé de document permettra de fournir la valeur correcte.

En appelant l’API plusieurs fois avec des clés différentes, les nouvelles clés sont ajoutées à la liste des clés de document à réinitialiser. L’appel de l’API avec le paramètre **`overwrite`** défini sur true remplacera la liste actuelle des clés de document à réinitialiser avec la charge utile de la requête :

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Vérifier l’état de réinitialisation

Pour vérifier l’état d’une réinitialisation et voir quelles clés de document sont mises en file d’attente pour traitement, utilisez [Obtenir l’état de l’indexeur](/rest/api/searchservice/get-indexer-status) avec **`api-version=06-30-2020-Preview`** ou version ultérieure. L’API de préversion retourne la section **`currentState`** , que vous pouvez trouver à la fin de la réponse Obtenir l’état de l’indexeur.

Le « mode » sera **`indexingAllDocs`** pour Réinitialiser les compétences (dans la mesure où tous les documents sont potentiellement concernés, pour les champs qui sont remplis par l’enrichissement par IA).

Pour Réinitialiser les documents, le mode est défini sur **`indexingResetDocs`** . L’indexeur conserve cet état jusqu’à ce que toutes les clés de document fournies dans l’appel de réinitialisation des documents soient traitées et qu’aucun autre travail d’indexation ne soit exécuté pendant la progression de l’opération. Pour trouver tous les documents de la liste des clés de documents, il faut craquer chaque document pour le localiser et le faire correspondre à la clé, ce qui peut prendre un certain temps si le jeu de données est volumineux. Si un conteneur de blobs contient des centaines de blobs et que les documents que vous souhaitez réinitialiser se trouvent à la fin, l’indexeur ne trouvera pas les blobs correspondants tant que tous les autres n’auront pas été vérifiés au préalable.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Une fois les documents retraités, l’indexeur revient au mode **`indexingAllDocs`** et traite tous les autres documents nouveaux ou mis à jour lors de la prochaine exécution.

## <a name="next-steps"></a>Étapes suivantes

Les API de réinitialisation sont utilisées pour indiquer la portée de la prochaine exécution de l’indexeur. Pour le traitement proprement dit, vous devrez invoquer une exécution d’indexeur à la demande ou autoriser un travail planifié pour terminer le travail. Une fois l’exécution terminée, l’indexeur revient au traitement normal, qu’il s’agisse d’un traitement planifié ou d’un traitement à la demande.

Après avoir réinitialisé et réexécuté les travaux d’indexation, vous pouvez surveiller l’état à partir du service de recherche ou obtenir des informations détaillées grâce à la journalisation des diagnostics.

+ [Opérations d’indexeur (REST)](/rest/api/searchservice/indexer-operations)
+ [Surveiller l’état de l’indexeur de recherche](search-howto-monitor-indexers.md)
+ [Collecter et analyser des données de journal](search-monitor-logs.md)
+ [Planifier un indexeur](search-howto-schedule-indexers.md)