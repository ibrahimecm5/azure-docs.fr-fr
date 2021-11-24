---
title: Ingérer des données depuis Event Grid pour Azure Synapse Data Explorer (préversion)
description: Découvrez comment ingérer (charger) des données dans l’Explorateur de données Azure Synapse à partir d’Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 2c472ae5950bc035612987c35e76291dc5c23f49
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485659"
---
# <a name="ingest-blobs-into-azure-synapse-data-explorer-by-subscribing-to-event-grid-notifications-preview"></a>Ingérer des objets blob dans Azure Synapse Data Explorer en s’abonnant à des notifications Event Grid (préversion)

<!-- > [!div class="op_single_selector"]
> * [One-click](one-click-ingestion-new-table.md)
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager template](data-connection-event-grid-resource-manager.md) -->

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

Cet article vous montre comment ingérer des objets blob de votre compte de stockage vers Azure Synapse Data Explorer en utilisant une connexion de données Event Grid. Vous allez créer une connexion de données Event Grid qui définit un abonnement [Azure Event Grid](/azure/event-grid/overview). L’abonnement Event Grid achemine les événements de votre compte de stockage vers l’Explorateur de données via un hub d’événements Azure. Ensuite, vous verrez un exemple de flux de données dans tout le système.

Pour obtenir des informations générales sur l’ingestion dans l’Explorateur de données à partir d’Event Grid, consultez [Se connecter à Event Grid](data-explorer-ingest-event-grid-overview.md).<!-- To create resources manually in the Azure portal, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md). -->

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Créer une table cible à laquelle Event Hubs enverra des données
    1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Développer**.
    1. Sous **Scripts KQL**, sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Script KQL**. Dans le volet de droite, vous pouvez nommer votre script.
    1. Dans le menu **Connecter à**, sélectionnez *contosodataexplorer*.
    1. Dans le menu **Utiliser la base de données**, sélectionnez *TestDatabase*.
    1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer la table.

        ```Kusto
        .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
        ```

        > [!TIP]
        > Vérifiez que la table a bien été créée. Dans le volet gauche, sélectionnez **Sonnées**, sélectionnez le menu Plus *contosodataexplorer*, puis cliquez sur **Actualiser**. Sous *contosodataexplorer*, développez **Tables** et assurez-vous que la table *TestTable* apparaît dans la liste.

    1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).

        ```Kusto
        .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
        ```

* Créez [un compte de stockage](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-event-grid-data-connection"></a>Créer une connexion de données à Event Grid

Connectez dès à présent le compte de stockage à l’Explorateur de données afin que le flux de données dans le stockage soit transmis à la table de test. Cette connexion peut être créée dans le Portail Azure sous l’Explorateur de données.

1. Sous le pool de l’Explorateur de données que vous avez créé, sélectionnez **Bases de données** > **TestDatabase**.

    :::image type="content" source="../media/ingest-data-event-grid/select-test-database.png" alt-text="Sélectionner la base de données de test.":::

1. Sélectionnez **Connexion de données**, puis **Ajouter une connexion de données**.

    :::image type="content" source="../media/ingest-data-event-grid/event-hub-connection.png" alt-text="Sélectionnez Ingestion des données, puis Ajouter une connexion de données.":::

#### <a name="data-connection---basics-tab"></a>Connexion de données - Onglet Informations de base

1. Sélectionnez le type de connexion : **Stockage Blob**.

1. Renseignez le formulaire avec les informations suivantes :

    :::image type="content" source="../media/ingest-data-event-grid/data-connection-basics.png" alt-text="Remplir le formulaire Event Grid avec les informations de base de la connexion.":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**|
    |---|---|---|
    | Nom de la connexion de données | *test-grid-connection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données.|
    | Abonnement du compte de stockage | Votre ID d’abonnement | ID d’abonnement où se trouve votre compte de stockage.|
    | Compte de stockage | *gridteststorage1* | Nom du compte de stockage que vous avez créé précédemment.|
    | Type d'événement | *Objet blob créé* ou *Objet blob renommé* | Type d’événement qui déclenche l’ingestion. Le *renommage d’objets blob* est pris en charge uniquement pour le stockage ADLSv2. Types pris en charge : Microsoft.Storage.BlobCreated ou Microsoft.Storage.BlobRenamed. |
    | Création de ressources | *Automatique* | Spécifiez si vous voulez que l’Explorateur de données crée un abonnement Event Grid, un espace de noms Event Hub et un hub d’événements pour vous. <!-- To create resources manually, see [Manually create resources for Event Grid ingestion](ingest-data-event-grid-manual.md) -->|

1. Sélectionnez **Paramètres de filtre** si vous voulez suivre des sujets spécifiques. Définissez les filtres pour les notifications comme suit :
    * Le champ **Préfixe** est le préfixe *littéral* du sujet. Comme le modèle appliqué est *startswith*, il peut englober plusieurs conteneurs, dossiers ou objets blob. Les caractères génériques ne sont pas autorisés.
        * Pour définir un filtre sur le conteneur d’objets blob, le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container prefix]`* .
        * Pour définir un filtre sur un préfixe d’objet blob (ou un dossier dans Azure Data Lake Gen2), le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * Le champ **Suffixe** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés.
    * Le champ **Sensible à la casse** indique si les filtres de préfixe et de suffixe respectent la casse.
    * Pour plus d’informations sur le filtrage des événements, consultez [Événements du stockage Blob](/azure/storage/blobs/storage-blob-event-overview#filtering-events).

    :::image type="content" source="../media/ingest-data-event-grid/filter-settings.png" alt-text="Paramètres de filtre Event Grid.":::

1. Sélectionnez **Suivant : Propriétés d’ingestion**.

> [!NOTE]
> Nous vous recommandons de mettre à jour la connexion de données afin d’utiliser des identités managées pour accéder au compte de stockage dès que cette option est disponible pour votre cluster.

#### <a name="data-connection---ingest-properties-tab"></a>Connexion de données - Onglet Propriétés d’ingestion

1. Renseignez le formulaire avec les informations suivantes. Les noms de table et de mappage sont sensibles à la casse :

   :::image type="content" source="../media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="Vérifier et créer les propriétés d’ingestion de table et de mappage.":::

    Propriétés d’ingestion :

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la table | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
    | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW et W3CLOG. Les options de compression prises en charge sont Zip et Gzip. |
    | Mappage | *TestMapping* | Le mappage que vous avez créé dans **TestDatabase**, qui mappe les données JSON entrantes dans les colonnes des noms de colonne et les types de données de **TestTable**.|
    | Paramètres avancés | *Mes données comprennent des en-têtes* | Ignore les en-têtes. Pris en charge pour les fichiers de type *SV.|

   > [!NOTE]
   > Vous n’êtes pas obligé de spécifier tous les **paramètres de routage par défaut**. Des paramètres partiels sont également acceptés.
1. Sélectionnez **Suivant : Vérifier + créer**

#### <a name="data-connection---review--create-tab"></a>Connexion de données - Onglet Vérifier + créer

1. Passez en revue les ressources qui ont été créées automatiquement pour vous et sélectionnez **Créer**.

    :::image type="content" source="../media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Vérifier et créer une connexion de données pour Event Grid.":::

### <a name="deployment"></a>Déploiement

Attendez la fin du déploiement. Si votre déploiement a échoué, sélectionnez **Détails de l’opération** à côté de la phase qui a échoué pour obtenir plus d’informations sur la raison de l’échec. Sélectionnez **Redéployer** pour retenter de déployer les ressources. Vous pouvez modifier les paramètres avant le déploiement.

:::image type="content" source="../media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Déployer les ressources Event Grid.":::

## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant que l’Explorateur de données et le compte de stockage sont connectés, vous pouvez créer des exemples de données.

### <a name="upload-blob-to-the-storage-container"></a>Charger l’objet blob sur le conteneur de stockage

Nous allons maintenant utiliser un petit script de shell qui exécute quelques commandes Azure CLI de base pour interagir avec les ressources de Stockage Azure. Ce script effectue les actions suivantes :

1. Crée un nouveau conteneur dans votre compte de stockage.
1. Charge un fichier existant (comme un objet blob) sur ce conteneur.
1. Liste les objets blob du conteneur.

Vous pouvez utiliser [Azure Cloud Shell](/azure/cloud-shell/overview) pour exécuter le script directement dans le portail.

Enregistrez les données dans un fichier et chargez celui-ci avec ce script :

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
    #!/bin/bash
    ### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Pour obtenir les meilleures performances d’ingestion, la taille *non compressée* des objets blob compressés soumis pour l’ingestion doit être communiquée. Étant donné que les notifications Event Grid contiennent uniquement des détails de base, les informations de taille doivent être communiquées explicitement. Les informations de taille non compressée peuvent être fournies en définissant la propriété `rawSizeBytes` sur les métadonnées d’objet blob avec la taille de données *non compressée* en octets.

### <a name="rename-blob"></a>Renommer l’objet blob

Si vous ingérez des données à partir du stockage ADLSv2 et que vous avez défini le *renommage d’objets blob* comme type d’événement pour la connexion de données, le déclencheur pour l’ingestion d’objets blob est le renommage d’objets blob. Pour renommer un objet blob, accédez à l’objet blob dans le portail Azure, cliquez dessus avec le bouton droit et sélectionnez **Renommer** :

   :::image type="content" source="../media/ingest-data-event-grid/rename-blob-in-the-portal.png" alt-text="Renommer l’objet blob dans le portail Azure.":::

### <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [propriétés d’ingestion](data-explorer-ingest-event-grid-overview.md#ingestion-properties) de l’ingestion d’objets blob via les métadonnées d’objet blob.

> [!NOTE]
> L’Explorateur de données ne supprimera pas les objets blob après l’ingestion.
> Conservez les objets blob pendant trois à cinq jours.
> Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal).

## <a name="review-the-data-flow"></a>Examiner le flux de données

> [!NOTE]
> L’Explorateur de données est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données.
> Par défaut, la stratégie est configurée sur 5 minutes.
> Vous pouvez modifier la stratégie ultérieurement si nécessaire. Dans cet article, vous pouvez vous attendre à une latence de quelques minutes.

1. Dans le portail Azure, sous Event Grid, vous voyez le pic d’activité pendant l’exécution de l’application.

    :::image type="content" source="../media/ingest-data-event-grid/event-grid-graph.png" alt-text="Graphe d’activité pour Event Grid.":::

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```kusto
    TestTable
    | count
    ```

1. Pour voir le contenu des messages, exécutez la requête suivante dans votre base de données de test.

    ```kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à l’image suivante :

    :::image type="content" source="../media/ingest-data-event-grid/table-result.png" alt-text="Jeu de résultats de message pour Event Grid.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser Event Grid, nettoyez l’abonnement Event Grid, l’espace de noms Event Hub et le hub d’événements qui ont été créés automatiquement pour vous, de façon à éviter d’engendrer des coûts.

1. Dans le portail Azure, accédez au menu de gauche et sélectionnez **Toutes les ressources**.

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Sélectionner toutes les ressources pour le nettoyage d’Event Grid.":::

1. Recherchez votre espace de noms Event Hub et sélectionnez **Supprimer** pour le supprimer :

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-find-event-hub-namespace-delete.png" alt-text="Nettoyer l’espace de noms Event Hub.":::

1. Dans le formulaire Supprimer les ressources, confirmez la suppression pour supprimer l’espace de noms Event Hub et les ressources Event Hub.

1. Accédez à votre compte de stockage. Dans le menu de gauche, sélectionnez **Événements** :

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Sélectionner les événements à nettoyer pour Event Grid.":::

1. Sous le graphe, sélectionnez votre abonnement Event Grid, puis sélectionnez **Supprimer** pour le supprimer :

    :::image type="content" source="../media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Supprimer l’abonnement Event Grid.":::

1. Pour supprimer votre connexion de données Event Grid, accédez à votre cluster de l’Explorateur de données. Dans le menu de gauche, sélectionnez **Bases de données**.

1. Sélectionnez votre base de données **TestDatabase** :

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Sélectionner la base de données pour nettoyer les ressources.":::

1. Dans le menu de gauche, sélectionnez **Ingestion des données** :

    :::image type="content" source="../media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Sélectionner l’ingestion des données pour nettoyer les ressources.":::

1. Sélectionnez votre connexion de données *test-grid-connection*, puis sélectionnez **Supprimer** pour la supprimer.

## <a name="next-steps"></a>Étapes suivantes

- [Analyser avec l’Explorateur de données](../../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](../data-explorer-monitor-pools.md)
