---
title: Ingérer des données d’Event Hub dans l’explorateur de données Azure Synapse (préversion)
description: Découvrez comment ingérer (charger) des données dans l’Explorateur de données Azure Synapse à partir d’Event Hub.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 624658dda4f78270e6e3da75920c2fe76e112fb6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479101"
---
# <a name="ingest-data-from-event-hub-into-azure-synapse-data-explorer"></a>Ingérer des données d’Event Hub dans l’explorateur de données Azure Synapse

> [!div class="op_single_selector"]
> * [Portail](data-explorer-ingest-event-hub-portal.md)
> * [En un clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

L’Explorateur de données Azure Synapse offre une ingestion (chargement de données) à partir d’Event Hubs, plateforme de streaming de Big Data et service d’ingestion d’événements. [Event Hubs](/azure/event-hubs/event-hubs-about) peut traiter des millions d’événements par seconde en quasi-temps réel. Dans cet article, vous créez un hub d’événements, vous vous y connectez à partir de l’Explorateur de données Azure Synapse et vous voyez le flux de données dans le système.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Créer une table cible à laquelle Event Hubs enverra des données
    1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Développer**.
    1. Sous **Scripts KQL**, sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Script KQL**. Dans le volet de droite, vous pouvez nommer votre script.
    1. Dans le menu **Connecter à**, sélectionnez *contosodataexplorer*.
    1. Dans le menu **Utiliser la base de données**, sélectionnez *TestDatabase*.
    1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer la table.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
        ```
    
        > [!TIP]
        > Vérifiez que la table a bien été créée. Dans le volet gauche, sélectionnez **Sonnées**, sélectionnez le menu Plus *contosodataexplorer*, puis cliquez sur **Actualiser**. Sous *contosodataexplorer*, développez **Tables** et assurez-vous que la table *TestTable* apparaît dans la liste.

    1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).
    
        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
        ```

- Nous vous recommandons d’utiliser une [identité managée affectée par l’utilisateur](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity) ou une [identité managée affectée par le système](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) pour la connexion de données (facultatif).
- [Un exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements. Téléchargez l’exemple d’application sur votre système.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour exécuter l’exemple d’application.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Créez un hub d’événements à l’aide d’un modèle Azure Resource Manager dans le portail Azure.

1. Afin de créer un hub d’événements, utilisez le bouton suivant pour démarrer le déploiement. Cliquez avec le bouton droit et sélectionnez **Ouvrir dans une nouvelle fenêtre** pour pouvoir suivre le reste des étapes de l’article.

    [![Bouton Déployer dans Azure](../media/ingest-data-event-hub/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.eventhub%2Fevent-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Le bouton **Déployer sur Azure** vous permet d’accéder au Portail Azure.

    ![Créer un formulaire Event Hub](../media/ingest-data-event-hub/deploy-to-azure.png)

1. Sélectionnez l’abonnement dans lequel créer le hub d’événements et créez un groupe de ressources nommé *test-hub-rg*.

    ![Créer un groupe de ressources](../media/ingest-data-event-hub/create-resource-group.png)

1. Renseignez le formulaire avec les informations suivantes.

    Utilisez les valeurs par défaut pour tous les paramètres non listés dans le tableau suivant.

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour votre hub d’événements.|
    | Resource group | *test-hub-rg* | Créez un groupe de ressources. |
    | Emplacement | *USA Ouest* | Pour cet article, sélectionnez *USA Ouest*. Pour un système de production, sélectionnez la région qui répond le mieux à vos besoins. Pour des performances optimales, créez l’espace de noms Event Hub dans la même localisation que le cluster de l’Explorateur de données Azure Synapse (en particulier pour les espaces de noms Event Hub dont le débit est élevé).
    | Nom de l’espace de noms | Nom unique de l’espace de noms | Choisissez un nom unique qui identifie votre espace de noms. Par exemple, *mytestnamespace*. Le nom de domaine *servicebus.windows.net* est ajouté au nom que vous fournissez. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le nom doit commencer par une lettre et se terminer par une lettre ou un chiffre. La valeur doit être comprise entre 6 et 50 caractères.
    | Nom de l’Event Hub | *test-hub* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. Le nom du hub d’événements doit être unique dans l’espace de noms. |
    | Nom du groupe de consommateurs | *test-group* | Les groupes de consommateurs permettent que chacune des applications de consommation ait une vue distincte du flux d’événements. |
    | | |

1. Sélectionnez **Revoir + créer**.

1. Passez en revue le **Récapitulatif** des ressources créées. Sélectionnez **Créer** pour confirmer que vous créez des ressources dans votre abonnement.

    :::image type="content" source="../media/ingest-data-event-hub/review-create.png" alt-text="Capture d’écran du portail Azure pour vérifier et créer un espace de noms Event Hub, un hub d’événements et un groupe de consommateurs.":::

1. Dans la barre d’outils, sélectionnez **Notifications** pour superviser le processus de provisionnement. Le déploiement peut prendre plusieurs minutes, mais vous pouvez passer à l’étape suivante sans attendre.

    ![Icône Notifications](../media/ingest-data-event-hub/notifications.png)

### <a name="authentication-considerations"></a>Considérations relatives à l’authentification

Selon le type d’identité que vous utilisez pour vous authentifier auprès d’Event Hub, vous pouvez avoir besoin de configurations supplémentaires.

- Si vous vous authentifiez avec Event Hub à l’aide d’une identité managée affectée par l’utilisateur, accédez à votre Event Hub > **Mise en réseau**, puis sous **Autoriser l’accès à partir de**, sélectionnez **Tous les réseaux** et enregistrez les modifications.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-all-networks.png" alt-text="Capture d’écran de la page mise en réseau Event Hub, montrant la sélection de l’autorisation d’accès à tous les réseaux.":::

- Si vous vous authentifiez avec Event Hub à l’aide d’une identité managée attribuée par le système, accédez à votre Event Hub > **Mise en réseau**, puis autorisez l’accès à partir de tous les réseaux ou sous **Autoriser l’accès à partir de**, sélectionnez **Réseaux sélectionnés**, sélectionnez **Autoriser les services Microsoft approuvés pour contourner ce pare-feu** et enregistrez les modifications.

    :::image type="content" source="../media/ingest-data-event-hub/configure-event-hub-trusted-services.png" alt-text="Capture d’écran de la page mise en réseau Event Hub, montrant la sélection de l’autorisation d’accès aux services approuvés.":::

## <a name="connect-to-the-event-hub"></a>Se connecter au hub d’événements

Vous vous connectez maintenant au Event Hub à partir du pool de l’Explorateur de données. Quand cette connexion est en place, les données qui circulent dans le hub d’événements sont diffusées en streaming vers la table de test que vous avez créée précédemment dans cet article.

1. Sélectionnez **Notifications** dans la barre d’outils pour vérifier que le déploiement du hub d’événements a réussi.

1. Sous le pool de l’Explorateur de données que vous avez créé, sélectionnez **Bases de données** > **TestDatabase**.

    :::image type="content" source="../media/ingest-data-event-hub/select-test-database.png" alt-text="Sélectionner la base de données de test.":::

1. Sélectionnez **Connexion de données**, puis **Ajouter une connexion de données**.

    :::image type="content" source="../media/ingest-data-event-hub/event-hub-connection.png" alt-text="Sélectionnez Ingestion des données, puis Ajouter une connexion de données.":::

### <a name="create-a-data-connection-preview"></a>Créer une connexion de données (préversion)

Remplissez le formulaire avec les informations suivantes, puis sélectionnez **Créer**.

:::image type="content" source="../media/ingest-data-event-hub/data-connection-pane.png" alt-text="Volet Connexion de données d’Event Hub - Explorateur de données Azure Synapse.":::

**Paramètre** | **Valeur suggérée** | **Description du champ**
|---|---|---|
| Nom de la connexion de données | *test-hub-connection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure Synapse.|
| Abonnement |      | ID d’abonnement dans lequel se trouve la ressource de hub d’événements. Ce champ est renseigné automatiquement. |
| Espace de noms Event Hub | Nom unique de l’espace de noms | Nom choisi précédemment qui identifie votre espace de noms. |
| Event Hub | *test-hub* | Hub d’événements que vous avez créé. |
| Groupe de consommateurs | *test-group* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
| Propriétés du système d’événements | Sélectionner les propriétés pertinentes | [Propriétés système du hub d’événements](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). S’il existe plusieurs enregistrements par message d’événement, les propriétés système sont ajoutées au premier enregistrement. Lors de l’ajout des propriétés système, [créez](/azure/data-explorer/kusto/management/create-table-command?context=/azure/synapse-analytics/context/context) ou [mettez à jour](/azure/data-explorer/kusto/management/alter-table-command?context=/azure/synapse-analytics/context/context) le schéma de table et le [mappage](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) pour inclure les propriétés sélectionnées. |
| Compression | *Aucun* | Type de compression de la charge utile des messages Event Hub. Types de compression pris en charge : *Aucun, GZip*.|
| Identité managée | Attribué par le système | Identité managée utilisée par le cluster Data Explorer pour l’accès en lecture à partir du hub d’événements.<br /><br />**Remarque** :<br />Quand la connexion de données est créée :<br/>\- Les identités *affectées par le système* sont automatiquement créées si elles n’existent pas<br />\- L’identité managée reçoit automatiquement le rôle *Récepteur de données Azure Event Hubs* et est ajoutée à votre cluster Data Explorer. Nous vous recommandons de vérifier que le rôle a été attribué et que l’identité a été ajoutée au cluster. |

#### <a name="target-table"></a>Table cible

Deux options sont disponibles pour le routage des données ingérées : *statique* et *dynamique*.
Dans le cadre de cet article, vous utilisez le routage statique, où vous spécifiez le nom de table, le format des données et le mappage comme valeurs par défaut. Si le message du hub d’événements comprend des informations de routage de données, ces informations de routage remplacent les paramètres par défaut.

1. Renseignez les paramètres de routage suivants :

    :::image type="content" source="../media/ingest-data-event-hub/default-routing-settings.png" alt-text="Paramètres de routage par défaut pour l’ingestion des données dans Event Hub - Explorateur de données Azure Synapse.":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la table | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
    | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO et W3CLOG. |
    | Mappage | *TestMapping* | [Mappage](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context) que vous avez créé dans **TestDatabase**, qui mappe les données entrantes aux noms de colonnes et aux types de données de **TestTable**. Obligatoire pour les formats JSON, MULTILINE JSON et AVRO, et facultatif pour les autres formats.|

    > [!NOTE]
    >
    > * Vous n’êtes pas obligé de spécifier tous les **paramètres de routage par défaut**. Des paramètres partiels sont également acceptés.
    > * Seuls les événements mis en file d’attente après que vous avez créé la connexion de données sont ingérés.

1. Sélectionnez **Create** (Créer).

### <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure des [propriétés système](data-explorer-ingest-event-hub-overview.md#system-properties) dans le schéma et le mappage de table.

## <a name="copy-the-connection-string"></a>Copier la chaîne de connexion

Quand vous exécutez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) listée dans les prérequis, vous avez besoin de la chaîne de connexion de l’espace de noms Event Hub.

1. Sous l’espace de noms Event Hub que vous avez créé, sélectionnez **Stratégies d’accès partagé**, puis **RootManageSharedAccessKey**.

    ![Stratégies d’accès partagé.](../media/ingest-data-event-hub/shared-access-policies.png)

1. Copiez **Chaîne de connexion - Clé primaire**. Collez-la dans la prochaine section.

    ![Chaîne de connexion.](../media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Générer un exemple de données

Utilisez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que vous avez téléchargé pour générer les données.

1. Ouvrez la solution de l’application exemple dans Visual Studio.
1. Dans le fichier *program.cs*, mettez à jour la constante `eventHubName` avec le nom de votre hub d’événements et mettez à jour la constante `connectionString` avec la chaîne de connexion que vous avez copiée à partir de l’espace de noms Event Hub.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Générez et exécutez l'application. L’application envoie des messages au Event Hub et imprime son état toutes les 10 secondes.
1. Une fois que l’application a envoyé quelques messages, passez à l’étape suivante : examiner le flux de données dans votre hub d’événements et votre table de test.

## <a name="review-the-data-flow"></a>Examiner le flux de données

Comme l’application génère des données, vous pouvez maintenant voir le flux de ces données entre le hub d’événements et la table dans votre cluster.

1. Dans le portail Azure, sous votre hub d’événements, vous voyez le pic d’activité pendant l’exécution de l’application.

    ![Graphe Event Hub.](../media/ingest-data-event-hub/event-hub-graph.png)

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```Kusto
    TestTable
    | count
    ```

1. Exécutez la requête suivante pour voir le contenu des messages :

    ```Kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à l’image suivante :

    ![Jeu de résultats des messages.](../media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    >
    > * L’Explorateur de données Azure Synapse est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données. La stratégie de traitement par lot par défaut est configurée pour sceller un lot une fois que l’une des conditions suivantes est remplie pour le lot : délai maximal de 5 minutes, taille totale de 1 Go ou 1 000 objets blob. Il existe donc un risque de latence. Pour plus d’informations, consultez la [stratégie de traitement par lot](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context).
    > * L’ingestion de Event Hub comprend le temps de réponse de Event Hub de 10 secondes ou 1 Mo.
    > * Pour réduire le décalage du temps de réponse, configurez votre table pour prendre en charge la diffusion en streaming. Consultez la [stratégie de diffusion en continu](/azure/data-explorer/kusto/management/streamingingestionpolicy?context=/azure/synapse-analytics/context/context).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser votre hub d’événements, nettoyez **test-hub-rg** pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé.

    Si le menu de gauche est réduit, sélectionnez le ![Bouton Développer.](../media/ingest-data-event-hub/expand.png) pour le développer.

   ![Sélectionner un groupe de ressources à supprimer.](../media/ingest-data-event-hub/delete-resources-select.png)

1. Sous **test-resource-group**, sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer (*test-hub-rg*), puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
