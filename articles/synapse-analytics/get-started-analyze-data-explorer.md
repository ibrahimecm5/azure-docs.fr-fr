---
title: 'Démarrage rapide : Prise en main de l’analyse avec des pools Data Explorer (préversion)'
description: Dans ce démarrage rapide, vous apprendrez à analyser des données avec Azure Data Explorer.
ms.topic: quickstart
ms.date: 09/30/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 1473b0db71d0ceaa4e7f78d20b10319047427311
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479444"
---
# <a name="quickstart-analyze-with-data-explorer-preview"></a>Démarrage rapide : Analyser à l’aide d’Azure Data Explorer (préversion)

Dans cet article, vous allez découvrir les étapes de base permettant de charger et d’analyser des données avec Azure Data Explorer pour Azure Synapse.

## <a name="create-a-data-explorer-pool"></a>Créer un pool Data Explorer

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools Data Explorer**.
1. Sélectionnez **Nouveau**, puis entrez les informations suivantes dans l’onglet **Informations de base** :

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom du pool Data Explorer | contosodataexplorer | Il s'agit du nom que portera le pool Data Explorer. |
    | Charge de travail | Optimisé pour le calcul | Cette charge de travail offre un ratio plus élevé entre le CPU et le stockage SSD. |
    | Taille du nœud | Petite (4 cœurs) | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    > [!IMPORTANT]
    > Notez qu'il existe des limitations spécifiques concernant les noms que les pools Data Explorer peuvent utiliser. Les noms ne doivent contenir que des lettres minuscules et des chiffres. Ils doivent comporter entre 4 et 15 caractères, et commencer par une lettre.

1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool Data Explorer commencera le processus d’approvisionnement.

## <a name="create-a-data-explorer-database"></a>Créer une base de données Data Explorer

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Données**.
1. Sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Pool Data Explorer** et collez les informations suivantes :

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom du pool | *contosodataexplorer* | Nom du pool Data Explorer à utiliser |
    | Nom | *TestDatabase* | Ce nom de base de données doit être unique dans le cluster. |
    | Période de conservation par défaut | *365* | Intervalle de temps (en jours) pendant lequel vous avez la garantie d’avoir les données à disposition pour les interroger. Cet intervalle se mesure à partir du moment où les données sont ingérées. |
    | Période de cache par défaut | *31* | Intervalle de temps (en jours) pendant lequel les données fréquemment interrogées restent disponibles dans le stockage SSD ou la RAM, plutôt que dans un stockage à plus long terme. |

1. Sélectionnez **Créer** pour créer la base de données. La création prend généralement moins d’une minute.

## <a name="ingest-sample-data-and-analyze-with-a-simple-query"></a>Ingérer des exemples de données et les analyser à l’aide d’une requête simple

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Développer**.
1. Sous **Scripts KQL**, sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Script KQL**. Dans le volet de droite, vous pouvez nommer votre script.
1. Dans le menu **Connecter à**, sélectionnez *contosodataexplorer*.
1. Dans le menu **Utiliser la base de données**, sélectionnez *TestDatabase*.
1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer une table StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vérifiez que la table a bien été créée. Dans le volet gauche, sélectionnez **Sonnées**, sélectionnez le menu Plus *contosodataexplorer*, puis cliquez sur **Actualiser**. Sous *contosodataexplorer*, développez **Tables** et assurez-vous que la table *StormEvents* apparaît dans la liste.

1. Collez la commande suivante, puis sélectionnez **Exécuter** pour ingérer des données dans la table StormEvents.

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. Une fois l’ingestion terminée, collez la requête suivante, sélectionnez la requête dans la fenêtre, puis **Exécuter**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

    La requête retourne les résultats suivants à partir des exemples de données ingérés.

    :::image type="content" source="data-explorer/media/get-started-analyze-data-explorer/sample-query-results.png" alt-text="Résultats de la requête exécutée sur les exemples de données":::

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Utiliser des requêtes KQL](/azure/data-explorer/kusto/query/tutorial?context=/azure/synapse-analytics/context/context&pivots=synapse)
- [Surveiller les pools de l’Explorateur de données](data-explorer/data-explorer-monitor-pools.md)
