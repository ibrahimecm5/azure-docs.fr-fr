---
title: Blobs modifiés et supprimés
titleSuffix: Azure Cognitive Search
description: Après création d’un index de recherche initial qui importe à partir du service Stockage Blob Azure, l’indexation qui suit peut récupérer uniquement les blobs modifiés ou supprimés. Cet article explique cela en détail.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: ab13e8901d507f6499a0eeba336a3f7717795ee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892188"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Détection des modifications et des suppressions dans l’indexation d’objets blob (Recherche cognitive Azure)

Après avoir créé un index de recherche initial, vous souhaiterez peut-être que les travaux de l’indexeur récupèrent uniquement les documents nouveaux et changés. Pour le contenu de recherche en provenance du service Stockage Blob Azure ou d’Azure Data Lake Storage Gen2, la détection des modifications se produit automatiquement lorsque vous utilisez une planification pour déclencher l’indexation. Par défaut, le service réindexe uniquement les blobs modifiés, déterminés par l’horodateur `LastModified`. Contrairement à d’autres sources de données prises en charge par les indexeurs de recherche, les blobs ont toujours un horodateur, ce qui évite d’avoir à configurer manuellement une stratégie de détection des modifications.

Bien que la détection des modifications soit acquise, la détection des suppressions ne l’est pas. Si vous souhaitez détecter des documents supprimés, veillez à adopter une approche de « suppression réversible ». Si vous supprimez complètement les objets blob, les documents correspondants ne seront pas supprimés de l’index de recherche.

Il existe deux façons d’implémenter l’approche de suppression réversible :

+ Suppression réversible native de blobs (préversion), décrite ci-après
+ [Suppression réversible à l’aide de métadonnées personnalisées](#soft-delete-using-custom-metadata)

> [!NOTE] 
> Azure Data Lake Storage Gen2 permet le changement de nom des répertoires. Quand un répertoire est renommé, les horodateurs des objets blob qu’il contient ne sont pas mis à jour. Par conséquent, l’indexeur ne réindexe pas ces objets blob. Si vous avez besoin que les objets blob d’un répertoire soient réindexés après le changement de nom de celui-ci parce que leurs URL ont changé, vous devez mettre à jour l’horodateur `LastModified` pour tous les objets blob dans l’annuaire afin que l’indexeur sache les réindexer lors d’une exécution ultérieure. Les répertoires virtuels dans le service Stockage Blob Azure ne pouvant pas être modifiés, ils ne rencontrent pas ce problème.

## <a name="native-blob-soft-delete-preview"></a>Suppression réversible native de blobs (préversion)

Pour cette approche de la détection de la suppression, le service Recherche cognitive s’appuie sur la fonctionnalité de [suppression réversible native de blobs](../storage/blobs/soft-delete-blob-overview.md) du service Stockage Blob Azure pour déterminer si les objets blob ont migré vers un état de suppression réversible. Quand des objets blob sont détectés dans cet état, un indexeur de recherche utilise ces informations pour supprimer le document correspondant de l’index.

> [!IMPORTANT]
> La prise en charge de la suppression réversible native de blobs est disponible en préversion. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2020-06-30-Preview](./search-api-preview.md) fournit cette fonctionnalité. Il n’y a actuellement pas de prise en charge du portail ou du SDK .NET.

### <a name="prerequisites"></a>Prérequis

+ [Activez la suppression réversible pour les objets blob](../storage/blobs/soft-delete-blob-enable.md).
+ Les blobs doivent se trouver dans un conteneur du service Stockage Blob Azure. La stratégie de suppression réversible native de blobs de Recherche cognitive n’est pas prise en charge pour les objets blob à partir d’Azure Data Lake Storage Gen2.
+ Les clés de document des documents de votre index doivent être mappées sur une propriété blob ou des métadonnées blob.
+ Vous devez utiliser l’API REST en préversion (`api-version=2020-06-30-Preview`) pour configurer la prise en charge de la suppression réversible.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Comment configurer la détection de suppression à l’aide de la suppression réversible native

1. Dans le stockage Blob, lors de l’activation de la suppression réversible, définissez la stratégie de conservation sur une valeur bien supérieure à celle de la planification d’intervalle de votre indexeur. Ainsi, en cas de problème lors de l’exécution de l’indexeur ou si vous avez un grand nombre de documents à indexer, l’indexeur a tout le temps nécessaire pour traiter les blobs supprimés de manière réversible. Les indexeurs Recherche cognitive Azure suppriment un document de l’index uniquement s’ils traitent le blob alors que son état est Supprimé de manière réversible.

1. Dans Recherche cognitive, définissez une stratégie de détection des suppressions réversibles natives de blobs sur la source de données. Voici un exemple. Étant donné qu’il s’agit d’une fonctionnalité d’évaluation, vous devez utiliser l’API REST en préversion.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Exécutez l’indexeur](/rest/api/searchservice/run-indexer) ou configurez l’indexeur pour qu’il s’exécute [selon une planification](search-howto-schedule-indexers.md). Quand l’indexeur s’exécute et traite un objet blob ayant un état de suppression réversible, le document de recherche correspondant est supprimé de l’index.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Réindexation de blobs dont la suppression a été annulée (à l’aide de stratégies de suppression réversible natives)

Si vous restaurez un objet blob supprimé de manière réversible dans le stockage Blob, l’indexeur ne le réindexe pas systématiquement. En effet, l’indexeur utilise l’horodateur `LastModified` de l’objet blob pour déterminer si l’indexation est nécessaire. Quand la suppression de manière réversible d’un blob est annulée, son horodateur `LastModified` n’est pas mis à jour. Ainsi, si l’indexeur a déjà traité des blobs dont les horodateurs `LastModified` sont plus récents, il ne réindexe pas le blob dont la suppression a été annulée. 

Pour vous assurer qu’un blob dont la suppression a été annulée est réindexé, vous devez mettre à jour le timestamp `LastModified` du blob. Pour ce faire, vous pouvez réenregistrer les métadonnées de ce blob. Vous n’avez pas besoin de changer les métadonnées, mais le réenregistrement de celles-ci a pour effet de mettre à jour l’horodateur `LastModified` du blob de sorte que l’indexeur sait qu’il peut le récupérer.

## <a name="soft-delete-using-custom-metadata"></a>Suppression réversible à l’aide de métadonnées personnalisées

Cette méthode utilise les métadonnées d’un objet blob pour déterminer si un document de recherche doit être supprimé de l’index. Cette méthode requiert deux actions distinctes, la suppression du document de recherche de l’index, puis la suppression du blob dans le service Stockage Azure.

Vous devez suivre des étapes dans le stockage Blob et dans Recherche cognitive, mais il n’y a pas d’autres dépendances de fonctionnalités. Cette fonctionnalité est prise en charge dans les API mises à la disposition générale.

1. Ajoutez une paire clé-valeur de métadonnées personnalisées au blob pour indiquer à Recherche cognitive Azure qu’il est logiquement supprimé.

1. Configurez une stratégie de détection des colonnes de suppression réversible sur la source de données. Par exemple, la stratégie suivante considère qu’un objet blob est supprimé s’il présente une propriété de métadonnées `IsDeleted` avec la valeur `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Une fois que l’indexeur a traité le blob et supprimé le document de l’index, vous pouvez supprimer le blob dans le service Stockage Blob Azure.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Réindexation de blobs dont la suppression a été annulée (à l’aide de métadonnées personnalisées)

Une fois qu’un indexeur a traité un blob supprimé, et supprimé de l’index le document de recherche correspondant, il ne réexaminera pas ce blob en cas de restauration ultérieure si l’horodateur `LastModified` du blob est antérieur à la dernière exécution de l’indexeur.

Si vous souhaitez réindexer ce document, modifiez la valeur `"softDeleteMarkerValue" : "false"` pour ce blob, puis réexécutez l’indexeur.

## <a name="next-steps"></a>Étapes suivantes

+ [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md)
+ [Comment configurer un indexeur de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Vue d’ensemble de l’indexation de blobs](search-blob-storage-integration.md)