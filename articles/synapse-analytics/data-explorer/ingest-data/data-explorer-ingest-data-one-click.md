---
title: 'Démarrage rapide : Prendre en main l’ingestion des données à l’aide de l’ingestion en un clic (préversion)'
description: Dans ce démarrage rapide, vous allez apprendre à ingérer des données dans des pools Data Explorer à l’aide de l’ingestion en un clic.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d5fef80a72e210e71eea0331b0d6098d5414d78a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479143"
---
# <a name="quickstart-ingest-data-using-one-click-preview"></a>Démarrage rapide : Ingérer des données à l’aide de l’ingestion en un clic (préversion)

L’ingestion en un clic rend le processus d’ingestion de données simple, rapide et intuitif. L’ingestion en un clic vous aide à ingérer des données, créer des tables de base de données et mapper des structures rapidement. Sélectionnez des données de différents types de sources dans des formats différents, qu’il s’agisse d’un processus d’ingestion unique ou continu.

Les fonctionnalités suivantes rendent l’ingestion en un clic particulièrement utile :

* Expérience intuitive guidée par l’Assistant Ingestion
* Ingestion des données en quelques minutes
* Ingestion des données de différents types de sources : fichier local, objets blob et conteneurs (jusqu’à 10 000 objets blob)
* Ingestion des données dans divers [formats](#file-formats)
* Ingestion des données dans des tables nouvelles ou existantes
* Schéma et mappage de table suggérés et faciles à changer
<!-- * Continue ingestion easily and quickly from a container with [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion) -->

L’ingestion en un clic est particulièrement utile lorsque vous procédez à l’ingestion de données pour la première fois, ou lorsque le schéma de vos données ne vous est pas familier.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Créez une table.[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vérifiez que la table a bien été créée. Dans le volet gauche, sélectionnez **Sonnées**, sélectionnez le menu Plus *contosodataexplorer*, puis cliquez sur **Actualiser**. Sous *contosodataexplorer*, développez **Tables** et assurez-vous que la table *StormEvents* apparaît dans la liste.

## <a name="access-the-one-click-wizard"></a>Accéder à l’Assistant Ingestion en un clic

L’Assistant Ingestion en un clic vous guide tout au long du processus d’ingestion en un clic.

* Pour accéder à l’Assistant à partir d’Azure Synapse :

    1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Données**.
    1. Sous **Bases de données de l’Explorateur de données**, cliquez avec le bouton droit sur la base de données appropriée, puis sélectionnez **Ouvrir dans Azure Data Explorer**.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Capture d’écran d’Azure Synapse Studio, montrant l’ouverture d’Azure Data Explorer dans le contexte d’un pool spécifique.":::

    1. Cliquez avec le bouton droit sur le pool approprié, puis sélectionnez **Ingérer de nouvelles données**.

* Pour accéder à l’Assistant à partir du portail Azure :

    1. Dans le portail Azure, recherchez et sélectionnez l’espace de travail Synapse approprié.
    1. Sous **Pools Data Explorer**, sélectionnez le pool approprié.
    1. Dans l’écran d’accueil **Bienvenue dans le pool Data Explorer**, sélectionnez **Ingérer de nouvelles données**.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-portal.png" alt-text="Capture d’écran du portail Azure, montrant l’ouverture d’Azure Data Explorer dans le contexte d’un pool spécifique.":::


* Pour accéder à l’Assistant à partir de l’interface utilisateur web d’Azure Data Explorer :

    1. Avant de commencer, procédez comme suit pour obtenir les points de terminaison de requête et d’ingestion des données.
        [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]
    1. Dans l’interface utilisateur web d’Azure Data Explorer, ajoutez une connexion au *point de terminaison de requête*.
    1. Sélectionnez **Requête** dans le menu gauche, cliquez avec le bouton droit sur la **base de données** ou la **table**, puis sélectionnez **Ingérer de nouvelles données**.

## <a name="one-click-ingestion-wizard"></a>Assistant Ingestion en un clic

> [!NOTE]
> Cette section décrit l’Assistant utilisant Event Hub comme source de données. Vous pouvez également utiliser ces étapes pour ingérer des données depuis un blob, un fichier, un conteneur de blobs et un conteneur ADLS Gen2.
>

1. Dans l’onglet **Destination**, choisissez la base de données et la table des données ingérées.

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Capture d’écran de l’Assistant d’ingestion en un clic Azure Data Explorer, montrant la sélection d’une base de données et d’une table.":::

1. Sous l’onglet **Source** :
    1. Sélectionnez *Event Hub* comme **type de source** pour l’ingestion.

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-source-type.png" alt-text="Capture d’écran de l’Assistant d’ingestion en un clic Azure Data Explorer, montrant la sélection d’un type de source.":::

    1. Renseignez les détails de la connexion de données de l’Event Hub en utilisant les informations suivantes :

        | Paramètre | Valeur suggérée | Description |
        |--|--|--|
        | Nom de la connexion de données | *ContosoDataConnection* | Nom de la connexion de données de l’Event Hub. |
        | Abonnement | *Contoso_Synapse* | Abonnement dans lequel se trouve l’Event Hub. |
        | Espace de noms de l’Event Hub | *contosoeventhubnamespace* | Espace de noms de l’Event Hub. |
        | Groupe de consommateurs | *contosoconsumergroup* | Nom du groupe de consommateurs de l’Event Hub. |

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Capture d’écran de l’Assistant d’ingestion en un clic Azure Data Explorer, montrant les détails de connexion de l’Event Hub.":::

    1. Sélectionnez **Suivant**.

### <a name="schema-mapping"></a>Mappage de schéma

Le service génère automatiquement les propriétés de schéma et d’ingestion, que vous pouvez changer. Vous pouvez utiliser une structure de mappage existante ou en créer une, selon que l’ingestion est destinée à une table existante ou nouvelle.

Sous l’onglet **Schéma**, effectuez les actions suivantes :
   1. Vérifier le type de compression généré automatiquement
   1. Choisir le [format de vos données](#file-formats) Les formats différents vous permettront d’apporter des modifications supplémentaires.
   1. Changez le mappage dans la [fenêtre Éditeur](#editor-window).

#### <a name="file-formats"></a>Formats de fichiers

L’ingestion en un clic prend en charge l’ingestion à partir de données sources sous tous les [formats de données pris en charge par Data Explorer pour l’ingestion](data-explorer-ingest-data-supported-formats.md).

### <a name="editor-window"></a>Fenêtre Éditeur

Dans la fenêtre **Éditeur** de l’onglet **Schéma**, vous pouvez ajuster les colonnes de la table de données, si nécessaire.

Les modifications que vous pouvez apporter dans une table dépendent des paramètres suivants :

* Si le type de la **table** est nouveau ou existant
* Si le type du **mappage** est nouveau ou existant

Type de la table | Type de mappage | Ajustements disponibles|
|---|---|---|
|Nouvelle table   | Nouveau mappage |Modifier le type de données, Renommer la colonne, Nouvelle colonne, Supprimer la colonne, Mettre à jour la colonne, Trier par ordre croissant, Trier par ordre décroissant  |
|Table existante  | Nouveau mappage | Nouvelle colonne (vous pourrez ensuite modifier le type de données, la renommer ou la mettre à jour)<br> Mettre à jour la colonne, Tri croissant, Tri décroissant  |
| | Mappage existant | Tri croissant, Tri décroissant

> [!NOTE]
> Lorsque vous ajoutez une nouvelle colonne ou mettez à jour une colonne, vous pouvez modifier les transformations de mappage. Pour plus d’informations, consultez [Transformations de mappage](#mapping-transformations).

<!-- >[!NOTE]
> At any time, you can open the [command editor](one-click-ingestion-new-table.md#command-editor) above the **Editor** pane. In the command editor, you can view and copy the automatic commands generated from your inputs. -->

#### <a name="mapping-transformations"></a>Mappage des transformations

Certains mappages de format de données (Parquet, JSON et Avro) prennent en charge des transformations simples au moment de l’ingestion. Pour appliquer des transformations de mappage, créez ou mettez à jour une colonne dans la [fenêtre de l’Éditeur](#editor-window).

Les transformations de mappage peuvent être effectuées sur une colonne de **type** string ou datetime, avec la **source** dont le type de données est int ou long. Les transformations de mappage prises en charge sont :

* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

### <a name="data-ingestion"></a>Ingestion de données

Une fois que vous avez terminé le mappage de schéma et les manipulations de colonnes, l’Assistant Ingestion démarre le processus d’ingestion de données.

* Quand l’ingestion de données provient de sources **autres que des conteneurs**, l’ingestion prend effet immédiatement.

* Si la source de données est un **conteneur** :
    * La [stratégie de traitement par lot](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) de Data Explorer agrège vos données.
    * Après l’ingestion, vous pouvez télécharger le rapport d’ingestion et passer en revue les performances de chaque objet blob qui a été traité.
    <!-- * You can select **Create continuous ingestion** and set up [continuous ingestion using Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion). -->

### <a name="initial-data-exploration"></a>Exploration initiale des données

Une fois l’ingestion terminée, vous pouvez effectuer une exploration initiale de vos données à l’aide des **commandes rapides** que l’Assistant met à votre disposition.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
