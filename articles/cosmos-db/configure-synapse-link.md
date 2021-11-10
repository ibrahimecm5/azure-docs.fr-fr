---
title: Configurer et utiliser Azure Synapse Link pour Azure Cosmos DB
description: Découvrez comment activer le lien Synapse pour les comptes Azure Cosmos DB, créer un conteneur avec le magasin analytique activé, connecter la base de données Azure Cosmos à l’espace de travail Synapse et exécuter des requêtes.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db, devx-track-azurepowershell
ms.openlocfilehash: 271b0a6c41f37a3ac8efe6e5562af48f3f267692
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425857"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Configurer et utiliser Azure Synapse Link pour Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse Link pour Azure Cosmos DB](synapse-link.md) est une fonctionnalité de traitement transactionnel et analytique (HTAP) hybride et native Cloud qui vous permet d’exécuter des analyses en quasi-temps réel sur les données opérationnelles dans Azure Cosmos DB. Synapse Link crée une intégration transparente entre Azure Cosmos DB et Azure Synapse Analytics.

Azure Synapse Link est disponible pour les conteneurs d’API SQL Azure Cosmos DB, ainsi que pour l’API Azure Cosmos DB pour les collections Mongo DB. Si vous souhaitez exécuter des requêtes analytiques avec Azure Synapse Link pour Azure Cosmos DB, vous devez :

* [Activer les comptes Synapse Link pour Azure Cosmos DB](#enable-synapse-link)
* [Créer un conteneur Azure Cosmos DB pour lequel le magasin analytique est activé](#create-analytical-ttl)
* [Facultatif : mettre à jour la durée de vie du magasin analytique pour un conteneur Azure Cosmos DB](#update-analytical-ttl)
* [Connecter votre base de données Azure Cosmos DB à un espace de travail Synapse](#connect-to-cosmos-database)
* [Interroger le magasin analytique à l’aide de Synapse Spark](#query-analytical-store-spark)
* [Interrogation du magasin analytique avec le pool SQL serverless](#query-analytical-store-sql-on-demand)
* [Analyse et visualisation des données dans Power BI avec le pool SQL serverless](#analyze-with-powerbi)

Vous pouvez également consulter le module d’apprentissage sur la façon de [configurer Azure Synapse Link pour Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/).

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Activer les comptes Azure Synapse Link pour Azure Cosmos DB

> [!NOTE]
> Si vous souhaitez utiliser des clés gérées par le client avec Azure Synapse Link, vous devez configurer l’identité managée de votre compte dans votre stratégie d’accès Azure Key Vault avant d’activer Synapse Link sur votre compte. Pour en savoir plus, consultez l’article [Configurer des clés gérées par le client en utilisant les identités managées des comptes Azure Cosmos DB](how-to-setup-cmk.md#using-managed-identity).

> [!NOTE]
> Si vous souhaitez utiliser le schéma de fidélité complet pour les comptes d’API SQL (CORE), vous ne pouvez pas utiliser le portail Azure pour activer Synapse Link. Cette option ne peut pas être modifiée après l’activation de Synapse Link dans votre compte et pour le configurer, vous devez utiliser Azure CLI ou PowerShell. Pour plus d’informations, consultez la [documentation sur la représentation du schéma du magasin analytique](analytical-store-introduction.md#schema-representation). 

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. [Créez un compte Azure](create-sql-api-dotnet.md#create-account) ou sélectionnez un compte Azure Cosmos DB existant.

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Nouvelles fonctionnalités**.

1. Sélectionnez **Synapse Link** dans la liste des fonctionnalités.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Localisation de la fonctionnalité Synapse Link":::

1. Ensuite, vous êtes invité à activer le lien Synapse sur votre compte. Sélectionnez **Activer**. Ce processus peut prendre de 1 à 5 minutes.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Activer la fonctionnalité Synapse Link":::

1. Votre compte est désormais activé pour l’utilisation de Synapse Link. Ensuite, découvrez comment créer des conteneurs activés pour le magasin analytique afin de démarrer automatiquement la réplication de vos données opérationnelles entre le magasin transactionnel et le magasin analytique.

> [!NOTE]
> L’activation de Synapse Link n’active pas automatiquement le magasin analytique. Une fois que vous avez activé Synapse Link sur le compte Cosmos DB, activez le magasin analytique sur les conteneurs quand vous les créez pour commencer à répliquer vos données d’opération dans le magasin analytique. 

### <a name="azure-cli"></a>Azure CLI

Les liens suivants montrent comment activer Synapse Link via Azure CLI :

* [Créer un nouveau compte Azure Cosmos DB avec Synapse Link activé](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [Mettre à jour un compte Azure Cosmos DB existant pour activer Synapse Link](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

### <a name="powershell"></a>PowerShell

* [Créer un nouveau compte Azure Cosmos DB avec Synapse Link activé](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [Mettre à jour un compte Azure Cosmos DB existant pour activer Synapse Link](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


Les liens suivants montrent comment activer Synapse Link via PowerShell :

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Créer un conteneur Azure Cosmos avec magasin analytique

Vous pouvez activer le magasin analytique sur un conteneur Azure Cosmos lors de la création du conteneur. Vous pouvez utiliser le Portail Azure ou configurer la propriété `analyticalTTL` lors de la création du conteneur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB.

> [!NOTE]
> Actuellement, vous pouvez activer le magasin analytique pour les **nouveaux** conteneurs (à la fois dans les nouveaux comptes et les comptes existants). Vous pouvez migrer des données à partir de vos conteneurs existante vers de nouveaux conteneurs à l’aide des [outils de migration Azure Cosmos DB](cosmosdb-migrationchoices.md).

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) ou à [l’Explorateur Azure Cosmos DB](https://cosmos.azure.com/).

1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**, puis entrez un nom pour la base de données, le conteneur, la clé de partition et les détails du débit. Activez l’option **Magasin analytique**. Une fois que vous avez activé le magasin analytique, il crée un conteneur avec la propriété `AnalyicalTTL` définie sur la valeur par défaut -1 (conservation infinie). Ce magasin analytique conserve toutes les versions historiques des enregistrements.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Activer le magasin analytique pour le conteneur Azure Cosmos":::

1. Si vous n’avez pas encore activé Synapse Link sur ce compte, vous êtes invité à le faire parce qu’il s’agit d’une condition préalable à la création d’un conteneur pour lequel le magasin analytique est activé. Si vous y êtes invité, sélectionnez **Activer Synapse Link**. Ce processus peut prendre de 1 à 5 minutes.

1. Sélectionnez **OK** pour créer un conteneur Azure Cosmos activé pour le magasin analytique.

1. Une fois le conteneur créé, vérifiez que le magasin analytique a été activé en cliquant sur **Paramètres** à droite sous Documents dans l’Explorateur de données, puis vérifiez que l’option **Durée de vie du magasin analytique** est activée.

### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

Le code suivant crée un conteneur avec le magasin analytique à l’aide du kit de développement logiciel (SDK) .NET. Définissez la propriété TTL analytique sur la valeur requise. Pour obtenir la liste des valeurs autorisées, consultez l’article sur les [valeurs de durée de vie analytique prises en charge](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>SDK Java V4

Le code suivant crée un conteneur avec le magasin analytique à l’aide du kit de développement logiciel (SDK) Java V4. Définissez la propriété `AnalyticalStoreTimeToLiveInSeconds` sur la valeur requise :

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Kit SDK Python V4

Le kit SDK 4.1.0 Azure Cosmos DB et Python 2.7 sont les versions minimales requises, et ce kit SDK est compatible uniquement avec l’API SQL.

La première étape consiste à s’assurer que vous utilisez au moins la version 4.1.0 du [kit SDK Python pour Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) :

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
L’étape suivante crée un conteneur avec un magasin analytique à l’aide du kit SDK Python pour Azure Cosmos DB :

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI

Les liens suivants montrent comment créer des conteneurs avec magasin analytique activé via Azure CLI :

* [API Azure Cosmos DB pour Mongo DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [API SQL Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)

### <a name="powershell"></a>PowerShell

Les liens suivants montrent comment créer des conteneurs avec magasin analytique activé via PowerShell :

* [API Azure Cosmos DB pour Mongo DB](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [API SQL Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Facultatif - Mettre à jour la durée de vie du magasin analytique

Une fois que le magasin analytique est activé avec une valeur de durée de vie particulière, vous pouvez le mettre à jour avec une valeur valide différente ultérieurement. Vous pouvez mettre à jour la valeur via les kits de développement logiciel (SDK) du portail Azure, d’Azure CLI, de PowerShell ou de Cosmos DB. Pour plus d’informations sur les différentes options de configuration de la durée de vie analytique, consultez l’article [Valeurs de durée de vie analytique prises en charge](analytical-store-introduction.md#analytical-ttl).


### <a name="azure-portal"></a>Portail Azure

Si vous avez créé un conteneur de magasin analytique activé via le Portail Azure, sa durée de vie analytique par défaut est égale à -1. Pour mettre à jour cette valeur, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) ou à [l’Explorateur Azure Cosmos DB](https://cosmos.azure.com/).
1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.
1. Sélectionnez un conteneur existant pour lequel le magasin analytique est activé. Développez-le et modifiez les valeurs suivantes :
   1. Ouvrez la fenêtre **Mise à l’échelle et paramètres**.
   1. Sous **Paramètre**, recherchez **Durée de vie du magasin analytique**.
   1. Sélectionnez **Activée (pas par défaut)** ou sélectionnez **Activée** et définissez une valeur de TTL.
   1. Cliquez sur **Enregistrer** pour enregistrer les modifications.


### <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET

Le code suivant illustre la mise à jour de la durée de vie du magasin analytique à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>SDK Java V4

Le code suivant illustre la mise à jour de la durée de vie du magasin analytique à l’aide du kit de développement logiciel (SDK) Java V4 :

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Kit SDK Python V4

Non pris en charge actuellement.


### <a name="azure-cli"></a>Azure CLI

Les liens suivants montrent comment mettre à jour la durée de vie analytique des conteneurs via Azure CLI :

* [API Azure Cosmos DB pour Mongo DB](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [API SQL Azure Cosmos DB](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

Les liens suivants montrent comment mettre à jour la durée de vie analytique des conteneurs via PowerShell :

* [API Azure Cosmos DB pour Mongo DB](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [API SQL Azure Cosmos DB](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Se connecter à un espace de travail Synapse

Suivez les instructions de [Se connecter à Azure Synapse Link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) sur la façon d’accéder à une base de données Azure Cosmos DB à partir d’Azure Synapse Analytics Studio avec Azure Synapse Link.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Interroger un magasin analytique avec Apache Spark pour Azure Synapse Analytics

Suivez les instructions de l’article [Interroger le magasin analytique Azure Cosmos DB à l’aide de Spark 3](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md) sur la procédure d’interrogation avec Synapse Spark 3. Cet article donne des exemples sur la façon dont vous pouvez interagir avec le magasin analytique à partir de mouvements Synapse. Ces mouvements sont visibles lorsque vous cliquez avec le bouton droit sur un conteneur. Avec les mouvements, vous pouvez rapidement générer du code et l’adapter à vos besoins. Ils sont également bien adaptés pour découvrir des données en un seul clic.

Pour l’intégration Spark 2, utilisez l’instruction de l’article [Interroger le magasin analytique Azure Cosmos DB à l’aide de Spark 2](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md).

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Interroger le magasin analytique à l’aide d’un pool SQL serverless dans Azure Synapse Analytics

Le pool SQL serverless vous permet d’interroger et d’analyser les données de vos conteneurs Azure Cosmos DB compatibles avec Azure Synapse Link. Vous pouvez analyser les données en quasi-temps réel sans affecter les performances de vos charges de travail transactionnelles. Il offre une syntaxe T-SQL familière pour interroger les données du magasin analytique et la connectivité intégrée à un large éventail d’outils décisionnels et d’interrogation ad hoc via l’interface T-SQL. Pour plus d’informations, consultez l’article [Interrogation du magasin analytique avec le pool SQL serverless](../synapse-analytics/sql/query-cosmos-db-analytical-store.md).

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Analyse et visualisation des données dans Power BI avec le pool SQL serverless

Il est possible de créer une base de données de pool SQL serverless et des vues sur Synapse Link pour Azure Cosmos DB. Par la suite, vous pouvez interroger les conteneurs Azure Cosmos, puis créer un modèle avec Power BI sur ces vues pour refléter cette requête. Il n’y a aucun impact sur les performances ou les coûts de vos charges de travail transactionnelles, ni de complexité liée à la gestion des pipelines ETL. Vous pouvez utiliser les modes [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) ou [Import](/power-bi/connect-data/service-dataset-modes-understand#import-mode). Pour plus d’informations, consultez l’article [Guide pratique d’utilisation du pool SQL serverless pour analyser les données Azure Cosmos DB avec Synapse Link](synapse-link-power-bi.md).

## <a name="configure-custom-partitioning"></a>Configurer le partitionnement personnalisé

Le partitionnement personnalisé vous permet de partitionner des données de magasin analytique sur des champs couramment utilisés comme filtres dans des requêtes analytiques, ce qui améliore les performances des requêtes.Pour plus d’informations, consultez les articles [Présentation du partitionnement personnalisé](custom-partitioning-analytical-store.md) et [Comment configurer un partitionnement personnalisé](configure-custom-partitioning.md).

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager

Le [modèle Azure Resource Manager](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) crée un compte Azure Cosmos DB dans lequel est activé Synapse Link pour l’API SQL. Ce modèle crée un compte d’API Core (SQL) dans une région avec un conteneur configuré avec une durée de vie analytique activée et une option d’utilisation de débit manuel ou de mise à l’échelle automatique. Pour déployer ce modèle, cliquez sur **Déployer sur Azure** dans la page lisezmoi (readme).

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Bien démarrer avec Azure Synapse Link - Exemples

Vous trouverez des exemples de prise en main d’Azure Synapse Link sur [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Ils mettent en avant des solutions de bout en bout pour des scénarios IoT et de vente au détail. Vous pouvez également trouver les exemples correspondant à l’API Azure Cosmos DB pour MongoDB dans le même référentiel sous le dossier [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples). 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les documents suivants :

* Consultez le module d’apprentissage sur la façon de [configurer Azure Synapse Link pour Azure Cosmos DB](/learn/modules/configure-azure-synapse-link-with-azure-cosmos-db/).

* [Vue d’ensemble du magasin analytique Azure Cosmos DB.](analytical-store-introduction.md)

* [Forum aux questions sur Azure Synapse Link pour Azure Cosmos DB.](synapse-link-frequently-asked-questions.yml)

* [Apache Spark dans Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Prise en charge du runtime de pool SQL serverless dans Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)
