---
title: Migrer des données existantes vers un compte API Table dans Azure Cosmos DB
description: Apprenez à migrer ou importer des données locales ou cloud vers un compte d’API Table Azure dans Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: c47b92d416bd24554c6e4cecc1aadb81c331616e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061265"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrer vos données vers un compte d’API Table Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Ce didacticiel fournit des instructions sur l’importation de données à utiliser avec [l’API Table](introduction.md) Azure Cosmos DB. Si vous avez des données stockées dans Stockage Table Azure, vous pouvez utiliser l’outil de migration de données ou AzCopy pour importer vos données dans l’API Table Azure Cosmos DB. 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Importation de données avec l’outil de migration de données
> * Importation de données avec AzCopy

## <a name="prerequisites"></a>Prérequis

* **Augmenter le débit :** la durée de la migration de vos données dépend du débit que vous définissez pour un conteneur spécifique ou un ensemble de conteneurs. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts.

* **Créer des ressources Azure Cosmos DB :** avant de commencer à migrer les données, créez toutes vos tables à partir du portail Azure. Si vous migrez vers un compte Azure Cosmos DB qui a un débit de niveau base de données, assurez-vous de fournir une clé de partition quand vous créez des tables Azure Cosmos DB.

## <a name="data-migration-tool"></a>Outil de migration de données

Vous pouvez utiliser l’outil de migration de données en ligne de commande (dt.exe) dans Azure Cosmos DB pour importer vos données Stockage Table Azure existantes dans un compte API Table. 

Pour migrer des données de table :

1. Téléchargez l’outil de migration à partir de [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Exécutez `dt.exe` en utilisant les arguments de ligne de commande de votre scénario. `dt.exe` prend une commande au format suivant :

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Les options prises en charge pour cette commande sont les suivantes :

* **/ErrorLog :** facultatif. Nom du fichier CSV pour rediriger les échecs de transfert de données.
* **/OverwriteErrorLog :** facultatif. Remplacer le fichier journal des erreurs.
* **/ProgressUpdateInterval :** facultatif. La valeur par défaut est `00:00:01`. Intervalle de temps pour l’actualisation de la progression du transfert des données à l’écran.
* **/ErrorDetails :** facultatif. La valeur par défaut est `None`. Spécifie que des informations détaillées sur l’erreur doivent être affichées pour les erreurs suivantes : `None`, `Critical` ou `All`.
* **/EnableCosmosTableLog :** facultatif. Dirigez le journal vers un compte de table Azure Cosmos DB. Si cette option est définie, la chaîne de connexion du compte de destination est définie par défaut, sauf si `/CosmosTableLogConnectionString` est également fournie. C’est utile si plusieurs instances de l’outil sont exécutées simultanément.
* **/CosmosTableLogConnectionString :** facultatif. Chaîne de connexion pour diriger le journal vers un compte de table Azure Cosmos DB distante.

### <a name="command-line-source-settings"></a>Paramètres de la source de ligne de commande

Utilisez les options de source suivantes quand vous définissez Stockage Table Azure en tant que source de la migration.

* **/s:AzureTable :** Lit des données à partir de Stockage Table.
* **/s.ConnectionString :** chaîne de connexion pour le point de terminaison de table. Vous pouvez la récupérer à partir du portail Azure.
* **/s.LocationMode :** facultatif. La valeur par défaut est `PrimaryOnly`. Spécifie le mode d’emplacement à utiliser lors de la connexion à Stockage Table : `PrimaryOnly`, `PrimaryThenSecondary`, `SecondaryOnly`, `SecondaryThenPrimary`.
* **/s.Table :** Nom de la table Azure.
* **/s.InternalFields :** définir sur `All` pour la migration de table, car `RowKey` et `PartitionKey` sont obligatoires pour l’importation.
* **/s.Filter :** facultatif. Chaîne de filtrage à appliquer.
* **/s.Projection :** facultatif. Liste des colonnes à sélectionner.

Pour récupérer la chaîne de connexion source quand vous effectuez une importation à partir de Stockage Table, ouvrez le portail Azure. Sélectionnez **Comptes de stockage** > **Compte** > **Clés d’accès** et copiez la **chaîne de connexion**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Capture d’écran affichant les options Comptes de stockage > Compte > Clés d’accès avec la mise en évidence de l’icône Copier.":::

### <a name="command-line-target-settings"></a>Paramètres de la cible de la ligne de commande

Utilisez les options de cible suivantes quand vous définissez l’API Table Azure Cosmos DB en tant que cible de la migration.

* **/t : TableAPIBulk :** charge les données sur l’API Table Azure Cosmos DB par lots.
* **/t.ConnectionString :** chaîne de connexion pour le point de terminaison de table.
* **/t.TableName :** spécifie le nom de la table dans laquelle écrire.
* **/t.Overwrite :** facultatif. La valeur par défaut est `false`. Spécifie si les valeurs existantes doivent être remplacées.
* **/t.MaxInputBufferSize :** facultatif. La valeur par défaut est `1GB`. Estimation approximative des octets d’entrée à mettre en mémoire tampon avant de vider les données dans le récepteur.
* **/t.Throughput :** facultatif. Si aucune valeur n’est spécifiée, le service est utilisé par défaut. Spécifie le débit à configurer pour la table.
* **/t.MaxBatchSize :** facultatif. La valeur par défaut est `2MB`. Spécifier la taille du lot, en octets.

### <a name="sample-command-source-is-table-storage"></a>Exemple de commande (la source correspond à Stockage Table)

Voici un exemple de ligne de commande indiquant comment effectuer l’importation entre Stockage Table et l’API Table :

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmos.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrer des données à l’aide d’AzCopy

Vous pouvez également utiliser l’utilitaire de ligne de commande AzCopy pour migrer des données de Stockage Table vers l’API Table Azure Cosmos DB. Pour utiliser AzCopy, vous devez d’abord exporter vos données comme décrit dans [Exporter des données à partir de Stockage Table](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Ensuite, vous importez les données dans l’API Table Azure Cosmos DB avec la commande suivante. Vous pouvez également les importer dans [Stockage Table Azure](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Reportez-vous à l’exemple suivant quand vous effectuez une importation dans Azure Cosmos DB. Notez que la valeur `/Dest` utilise `cosmosdb`, et non `core`.

Exemple de commande d’importation :

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment interroger des données avec l’API Table Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données ?](tutorial-query-table.md)




