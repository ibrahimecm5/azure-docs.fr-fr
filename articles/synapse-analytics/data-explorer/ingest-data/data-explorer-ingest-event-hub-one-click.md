---
title: Utiliser l’ingestion en un clic pour ingérer des données dans l’Explorateur de données Azure Synapse à partir d’Event Hub (préversion)
description: Découvrez comment utiliser l’ingestion en un clic pour ingérer (charger) des données dans l’Explorateur de données Azure Synapse à partir d’Event Hub.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: dbdf77da2434c71de29f45885d3e08ad304e54ea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479419"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-synapse-data-explorer-preview"></a>Utiliser l’ingestion en un clic afin de créer une connexion de données Event Hub pour l’Explorateur de données Azure Synapse (préversion)

> [!div class="op_single_selector"]
> * [Portail](data-explorer-ingest-event-hub-portal.md)
> * [En un clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

L’Explorateur de données Azure Synapse offre une ingestion (chargement de données) à partir d’Event Hubs, plateforme de streaming de Big Data et service d’ingestion d’événements. [Event Hubs](/azure/event-hubs/event-hubs-about) peut traiter des millions d’événements par seconde en quasi-temps réel. Dans cet article, vous allez connecter un hub d’événements à une table dans l’Explorateur de données Azure Synapse à l’aide de l’expérience d’[ingestion en un clic](data-explorer-ingest-data-one-click.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Hub d’événements avec des données pour l’ingestion](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> L’ingestion de données à partir d’Event Hub dans des pools de l’Explorateur de données ne fonctionne pas si votre espace de travail Synapse utilise un réseau virtuel managé avec la protection contre l’exfiltration des données activée.

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Données**.

1. Sous **Bases de données de l’Explorateur de données**, cliquez avec le bouton droit sur la base de données appropriée, puis sélectionnez **Ouvrir dans Azure Data Explorer**.

    :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Capture d’écran d’Azure Synapse Studio, montrant l’ouverture d’Azure Data Explorer dans le contexte d’un pool spécifique.":::

1. Dans le menu de gauche de l’interface utilisateur web, sélectionnez l’onglet **Données**. 

    :::image type="content" source="../media/ingest-data-event-hub/one-click-ingestion-event-hub.png" alt-text="Sélectionnez les données d’ingestion en un clic à partir d’Event Hub dans l’interface utilisateur web.":::

1. Dans la carte **Ingérer des données à partir d’Event Hub**, sélectionnez **Ingérer**. 

La fenêtre **Ingérer de nouvelles données** s’ouvre, avec l’onglet **Destination** sélectionné.

### <a name="destination-tab"></a>Onglet Destination

:::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Capture d’écran de l’onglet Destination. Les champs Cluster, Base de données et Table doivent être remplis avant de passer à Suivant : Source.":::

1. Les champs **Cluster** et **Base de données** sont remplis automatiquement. Vous pouvez sélectionner un autre cluster ou une autre base de données dans les menus déroulants.

1. Sous **Table**, sélectionnez **Créer une table** et entrez un nom pour la nouvelle table. Vous pouvez également utiliser une table existante. 

    > [!NOTE]
    > Les noms de table doivent comprendre entre 1 et 1024 caractères. Vous pouvez utiliser des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

1. Sélectionnez **Suivant : Source**.

### <a name="source-tab"></a>Onglet Source

1. Sous **Type de source**, sélectionnez **Event Hub**. 

1. Sous **Connexion de données**, renseignez les champs suivants :

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Capture d’écran de l’onglet Source avec les champs Détails du projet à renseigner : Ingérer de nouvelles données dans l’Explorateur de données Azure Synapse avec Event Hub en un clic.":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *ContosoDataConnection*  | Nom qui permet d’identifier votre connexion de données.
    | Abonnement |      | ID d’abonnement dans lequel se trouve la ressource de hub d’événements.  |
    | Espace de noms Event Hub |  | Nom unique qui permet d’identifier votre espace de noms. |
    | Event Hub |  | Hub d’événements que vous souhaitez utiliser. |
    | Groupe de consommateurs |  | Groupe de consommateurs défini dans votre hub d’événements. |
    | Propriétés du système d’événements | Sélectionner les propriétés pertinentes | [Propriétés système du hub d’événements](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). S’il existe plusieurs enregistrements par message d’événement, les propriétés système sont ajoutées au premier. Lors de l’ajout des propriétés système, [créez](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) ou [mettez à jour](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) le schéma de table et le [mappage](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) pour inclure les propriétés sélectionnées. |

1. Sélectionnez **Suivant : Schéma**.

## <a name="schema-tab"></a>Onglet Schéma

Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata). Les formats pris en charge sont CSV, JSON, PSV, SCsv, SOHsv TSV, TXT et TSVE.

<!-- For information on schema mapping with JSON-formatted data, see [Edit the schema](one-click-ingestion-existing-table.md#edit-the-schema).
For information on schema mapping with CSV-formatted data, see [Edit the schema](one-click-ingestion-new-table.md#edit-the-schema). -->

:::image type="content" source="../media/ingest-data-event-hub/schema-tab.png" alt-text="Capture d’écran de l’onglet Schéma dans la page Ingérer de nouvelles données dans l’Explorateur de données Azure Synapse avec Event Hub en un clic.":::

1. Si les données que vous voyez dans la fenêtre d’aperçu sont incomplètes, il est possible que vous ayez besoin de davantage de données pour créer une table avec tous les champs de données nécessaires. Utilisez les commandes suivantes pour récupérer (fetch) de nouvelles données à partir de votre hub d’événements :
    * **Ignorer et récupérer de nouvelles données** : ignore les données présentées et recherche les nouveaux événements.
    * **Récupérer plus de données** : recherche d’autres événements, en plus de ceux déjà trouvés. 
    
    > [!NOTE]
    > Pour que vous puissiez voir un aperçu de vos données, votre hub d’événements doit envoyer des événements.
        
1. Sélectionnez **Suivant : Résumé**.

## <a name="continuous-ingestion-from-event-hub"></a>Ingestion continue à partir d’Event Hub

Dans la fenêtre **Ingestion continue à partir d’Event Hub établie**, toutes les étapes sont marquées par des coches vertes lorsque l’établissement se termine correctement. Les cartes ci-dessous vous permettent d’explorer vos données à l’aide de **requêtes rapides**, d’annuler les modifications apportées à l’aide d’**outils** ou de **superviser** les connexions et les données du hub d’événements.

:::image type="content" source="../media/ingest-data-event-hub/data-ingestion-completed.png" alt-text="Capture de l’écran final du processus d’ingestion dans l’Explorateur de données Azure Synapse à partir d’Event Hub en un clic.":::

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Analyser les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
