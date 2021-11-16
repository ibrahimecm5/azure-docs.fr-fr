---
title: 'Démarrage rapide : Prendre en main l’ingestion des données à l’aide de pipelines (préversion)'
description: Dans ce démarrage rapide, vous allez apprendre à ingérer des données dans des pools Data Explorer à l’aide d’Azure Synapse Pipelines.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 746f37fae42b117853d2f808d9fc5ff9fa9f980b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479141"
---
# <a name="quickstart-ingest-data-using-azure-synapse-pipelines-preview"></a>Démarrage rapide : Ingérer des données à l’aide d’Azure Synapse Pipelines (préversion)

Dans ce démarrage rapide, vous allez apprendre à charger des données à partir d’une source de données dans un pool Azure Synapse Data Explorer.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Créez une table.[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vérifiez que la table a bien été créée. Dans le volet gauche, sélectionnez **Sonnées**, sélectionnez le menu Plus *contosodataexplorer*, puis cliquez sur **Actualiser**. Sous *contosodataexplorer*, développez **Tables** et assurez-vous que la table *StormEvents* apparaît dans la liste.

- Obtenez les points de terminaison de requête et d’ingestion de données. Vous aurez besoin du point de terminaison de requête pour configurer votre service lié.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="create-a-linked-service"></a>Créer un service lié

Dans Azure Synapse Analytics, un service lié vous permet de définir vos informations de connexion à d’autres services. Dans cette section, vous allez créer un service lié pour Azure Data Explorer.

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Services liés**.
1. Sélectionnez **&plus; Nouveau**.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-linked-service.png" alt-text="Capture d’écran de l’écran Services liés, montrant la liste des services existants et mettant en évidence le bouton Ajouter nouveau.":::

1. Sélectionnez le service **Azure Data Explorer** à partir de la galerie, puis sélectionnez **Continuer**.

    :::image type="content" source="../media/ingest-data-pipeline/select-new-data-explorer-linked-service.png" alt-text="Capture d’écran du nouveau volet Services liés, montrant la liste des services disponibles et mettant en évidence le nouveau service Azure Data Explorer à ajouter.":::

1. Dans la page Nouveaux services liés, utilisez les informations suivantes :

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom | *contosodataexplorerlinkedservice* | Nom du nouveau service lié Azure Data Explorer. |
    | Méthode d'authentification | *Identité gérée* | Méthode d’authentification du nouveau service. |
    | Méthode de sélection du compte | *Entrer manuellement* | Méthode permettant de spécifier le point de terminaison de requête. |
    | Point de terminaison | *https:\/\/contosodataexplorer.contosoanalytics.dev.kusto.windows.net* | Point de terminaison de requête que vous avez [noté précédemment](#prerequisites). |
    | Base de données | *TestDatabase* | Base de données dans laquelle vous souhaitez ingérer les données. |

    :::image type="content" source="../media/ingest-data-pipeline/create-new-data-explorer-linked-service.png" alt-text="Capture d’écran du volet d’informations Nouveaux services liés, montrant les champs qui doivent être renseignés pour le nouveau service.":::

1. Sélectionnez **Tester la connexion** pour vérifier les paramètres, puis sélectionnez **Créer**.

## <a name="create-a-pipeline-to-ingest-data"></a>Créer un pipeline pour ingérer des données

Un pipeline contient le flux logique pour l’exécution d’un ensemble d’activités. Dans cette section, vous allez créer un pipeline contenant une activité de copie qui ingère les données de votre source préférée dans un pool Data Explorer.

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Intégrer**.

1. Sélectionnez **&plus;**  > **Pipeline**. Dans le volet de droite, vous pouvez nommer votre pipeline.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-pipeline.png" alt-text="Capture d’écran montrant la sélection pour la création d’un nouveau pipeline.":::

1. Sous **Activités** > **Déplacer et transformer**, faites glisser **Copier des données** sur le canevas du pipeline.
1. Sélectionnez l’activité de copie et accédez à l’onglet **Source**. Sélectionnez ou créez un jeu de données source comme source de copie des données.
1. Accédez à l’onglet **Récepteur**. Sélectionnez **Nouveau** pour créer un jeu de données récepteur.

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink.png" alt-text="Capture d’écran de l’activité de copie du pipeline, montrant la sélection permettant de créer un nouveau récepteur.":::

1. Sélectionnez le jeu de données **Azure Data Explorer** à partir de la galerie, puis sélectionnez **Continuer**.
1. Dans le volet **Définir les propriétés**, utilisez les informations suivantes, puis sélectionnez **OK**.

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom | *AzureDataExplorerTable* | Nom du nouveau pipeline. |
    | Service lié | *contosodataexplorerlinkedservice* | Service lié que vous avez créé précédemment. |
    | Table | *StormEvents* | Table que vous avez créée précédemment. |

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink-set-properties.png" alt-text="Capture d’écran du volet Définir les propriétés de l’activité de copie du pipeline, montrant les champs qui doivent être renseignés pour le nouveau récepteur.":::

1. Pour valider le pipeline, sélectionnez **Valider** dans la barre d’outils. Le résultat de la validation du pipeline s’affiche sur le côté droit de la page.

## <a name="debug-and-publish-the-pipeline"></a>Déboguer et publier le pipeline

Une fois la configuration de votre pipeline terminée, avant de publier vos artefacts, vous pouvez exécuter un débogage pour vérifier que tout est correct.

1. Sélectionnez **Déboguer** dans la barre d’outils. L’état d’exécution du pipeline apparaît dans l’onglet **Sortie** au bas de la fenêtre.

1. Une fois que le pipeline s’exécute correctement, sélectionnez **Publier tout** dans la barre d’outils supérieure. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans le service Synapse Analytics.
1. Patientez jusqu’à voir le message **Publication réussie**. Pour voir les messages de notification, sélectionnez le bouton avec l’icône en forme de cloche en haut à droite.

## <a name="trigger-and-monitor-the-pipeline"></a>Déclencher et surveiller le pipeline

Dans cette section, vous déclenchez manuellement le pipeline publié à l’étape précédente.

1. Sélectionnez **Ajouter déclencheur** dans la barre d’outils, puis **Déclencher maintenant**. Dans la page **Exécution du pipeline**, sélectionnez **OK**.

1. Accédez à l’onglet **Surveiller** dans la barre latérale gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel.
1. Quand l’exécution du pipeline s’effectue correctement, sélectionnez le lien dans la colonne **Nom du pipeline** pour afficher les détails de l’exécution d’activité ou réexécuter le pipeline. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste.
1. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne **Nom de l’activité**. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées.
1. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Toutes les exécutions de pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste.
1. Vérifiez que vos données sont correctement écrites dans le pool Data Explorer.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
