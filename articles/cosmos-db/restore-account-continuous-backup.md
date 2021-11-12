---
title: Restaurez un compte Azure Cosmos DB qui utilise le mode de sauvegarde continue.
description: Découvrez comment identifier l’heure de restauration et restaurer un compte Azure Cosmos DB actif ou supprimé. Vous verrez comment utiliser le flux d’événements pour identifier l’heure de restauration et restaurer le compte à l’aide du portail Azure, de PowerShell, de l’interface CLI ou d’un modèle Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3161971323ebda6b55ec0fb423089d3115cd9c01
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562121"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>Restaurer un compte Azure Cosmos DB qui utilise le mode de sauvegarde continue
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé, ou de restaurer dans n’importe quelle région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment identifier l’heure de restauration et restaurer un compte Azure Cosmos DB actif ou supprimé. Vous verrez comment restaurer le compte en utilisant le [portail Azure](#restore-account-portal), [PowerShell](#restore-account-powershell), [CLI](#restore-account-cli), ou un modèle [Resource Manager](#restore-arm-template).

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal"></a>Restaurer un compte à l’aide de portail Azure

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Restaurer un compte actif à la suite d’une modification accidentelle

Vous pouvez utiliser Portail Azure pour restaurer un compte actif entier ou certaines bases de données et certains conteneurs sous celui-ci. Pour restaurer vos données, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte Azure Cosmos DB et ouvrez le panneau **Restauration à un instant dans le passé**.

   > [!NOTE]
   > Le panneau de restauration du portail Azure n’est rempli que si vous disposez de l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Pour en savoir plus sur la définition de cette autorisation, consultez l’article [Autorisations de sauvegarde et de restauration](continuous-backup-restore-permissions.md).

1. Renseignez les détails suivants pour la restauration :

   * **Point de restauration (UTC)**  : Un timestamp au cours des 30 derniers jours. Le compte doit exister à cette date. Vous pouvez spécifier le point de restauration en heure UTC. Il peut être aussi proche de la seconde quand vous souhaitez le restaurer. Sélectionnez le lien **Cliquez ici** pour obtenir de l’aide sur l’[identification du point de restauration](#event-feed).

   * **Emplacement** : Région de destination où le compte est restauré. Le compte doit exister dans cette région à l’horodatage donné (par exemple, USA Ouest ou USA Est). Un compte ne peut être restauré que dans les régions où le compte source existait.

   * **Restaurer une ressource** : Vous pouvez choisir soit **Intégralité du compte**, soit **une base de données ou un conteneur** à restaurer. Les bases de données et les conteneurs doivent exister à l’heure donnée. En fonction du point de restauration et de l’emplacement sélectionnés, les ressources de restauration sont renseignées, ce qui permet à l’utilisateur de sélectionner des bases de données ou des conteneurs spécifiques qui doivent être restaurés.

   * **Groupe de ressources** : Groupe de ressources sous lequel le compte cible sera créé et restauré. Le groupe de ressources doit déjà exister.

   * **Restaurer le compte cible** : Nom du compte cible. Le nom du compte cible doit respecter les mêmes instructions que lorsque vous créez un nouveau compte. Ce compte sera créé par le processus de restauration dans la même région que celle où se trouve votre compte source.
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="Restaurer un compte actif à la suite d’une modification accidentelle sur le portail Azure." border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. Après avoir sélectionné les paramètres ci-dessus, sélectionnez le bouton **Envoyer** pour lancer une restauration. Le coût de restauration est une charge unique, qui est basée sur la taille des données et le coût du stockage de sauvegarde dans la région sélectionnée. Pour plus d’informations, consultez la section [Tarification](continuous-backup-restore-introduction.md#continuous-backup-pricing).

La suppression du compte source alors qu’une restauration est en cours peut entraîner l’échec de la restauration.

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Utiliser un flux d’événements pour identifier l’heure de la restauration

Lorsque vous renseignez l’heure de l’instant dans le passé dans le portail Azure, si vous avez besoin d’aide pour identifier le point de restauration, sélectionnez le lien **Cliquez ici** pour accéder au panneau du flux d’événements. Le flux d’événements fournit une liste complète des événements de création, de remplacement et de suppression sur les bases de données et les conteneurs du compte source. 

Par exemple, si vous souhaitez effectuer une restauration au point précédant la suppression ou la mise à jour d’un conteneur donné, consultez ce flux d’événements. Les événements sont affichés dans l’ordre chronologique décroissant du moment où ils se sont produits, les événements récents se trouvant en haut de la liste. Vous pouvez parcourir les résultats et sélectionner l’heure avant ou après l’événement pour affiner encore davantage l’heure de votre choix.

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="Utiliser un flux d’événements pour identifier l’heure du point de restauration." border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> Le flux d’événements n’affiche pas les modifications apportées aux ressources de l’élément. Vous pouvez toujours spécifier manuellement un timestamp au cours des 30 derniers jours (à condition que le compte existe à ce moment-là) pour la restauration.

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Restaurer un compte supprimé

Vous pouvez utiliser Portail Azure pour restaurer complètement un compte supprimé dans les 30 jours suivant sa suppression. Pour restaurer un compte supprimé, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez les ressources *Azure Cosmos DB* dans la barre de recherche globale. Elle répertorie tous vos comptes existants.
1. Sélectionnez ensuite le bouton **Restaurer**. Le panneau Restaurer affiche la liste des comptes supprimés qui peuvent être restaurés pendant la période de rétention, qui est de 30 jours à partir de la date de suppression.
1. Choisissez le compte que vous souhaitez restaurer.

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="Restaurer un compte supprimé à partir de Portail Azure." border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > Le panneau de restauration du portail Azure n’est rempli que si vous disposez de l’autorisation `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`. Pour en savoir plus sur la définition de cette autorisation, consultez l’article [Autorisations de sauvegarde et de restauration](continuous-backup-restore-permissions.md).

1. Pour restaurer un compte supprimé, sélectionnez un compte à restaurer et entrez les informations suivantes :

   * **Point de restauration (UTC)**  : Un timestamp au cours des 30 derniers jours. Le compte doit exister à cette date. Spécifiez le point de restauration en heure UTC. Il peut être aussi proche de la seconde quand vous souhaitez le restaurer.

   * **Emplacement** : Région de destination où le compte doit être restauré. Le compte source doit exister dans cette région à l’heure donnée. Par exemple : USA Ouest ou USA Est.  

   * **Groupe de ressources** : Groupe de ressources sous lequel le compte cible sera créé et restauré. Le groupe de ressources doit déjà exister.

   * **Restaurer le compte cible** : Le nom du compte cible doit respecter les mêmes instructions que lorsque vous créez un nouveau compte. Ce compte sera créé par le processus de restauration dans la même région que celle où se trouve votre compte source.

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Suivre l’état de l’opération de restauration

Après avoir lancé une opération de restauration, sélectionnez l’icône **Notification** en forme de cloche dans le coin supérieur droit du portail. Elle donne un lien qui affiche l’état du compte en cours de restauration. Pendant que la restauration est en cours, l’état du compte est *En cours de création* ; une fois l’opération de restauration terminée, l’état du compte devient *En ligne*.

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="L’état du compte restauré passe d’En cours de création à En ligne lorsque l’opération est terminée." border="true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::

### <a name="get-the-restore-details-from-the-restored-account"></a>Récupérer les détails de la restauration à partir du compte restauré

Une fois l’opération de restauration terminée, vous souhaiterez peut-être connaître les détails du compte source à partir duquel vous avez restauré, ou l’heure de la restauration.

Procédez comme suit pour récupérer les détails de la restauration à partir du portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez au compte restauré.

1. Accédez au volet **Exporter le modèle**. Il ouvre un modèle JSON correspondant au compte restauré.

1. L’objet **resources** > **properties** > **restoreParameters** contient les détails de la restauration. **restoreTimestampInUtc** vous donne l’heure à laquelle le compte a été restauré et **databasesToRestore** affiche la base de données et le conteneur spécifiques à partir desquels le compte a été restauré.

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell"></a>Restaurer un compte à l’aide d’Azure PowerShell

Avant de restaurer le compte, installez la [dernière version d’Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) ou une version supérieure à 6.2.0. Connectez-vous ensuite à votre compte Azure et sélectionnez l’abonnement requis avec les commandes suivantes :

1. Connectez-vous à Azure en utilisant la commande suivante :

   ```azurepowershell
   Connect-AzAccount
   ```

1. Sélectionnez un abonnement spécifique avec la commande suivante :

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**Exemple 1 :** Restauration de l'intégralité du compte :

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Exemple 2 :** Restauration de collections et de bases de données spécifiques. Cet exemple restaure les collections *MyCol1*, *MyCol2* à partir de *MyDB1* et la totalité de la base de données *MyDB2*, qui comprend tous les conteneurs sous celui-ci.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="get-the-restore-details-from-the-restored-account"></a>Récupérer les détails de la restauration à partir du compte restauré

Importez le module `Az.CosmosDB` et exécutez la commande suivante pour récupérer les détails de la restauration. Le restoreTimestamp se trouve sous l’objet restoreParameters :

```azurepowershell
Get-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount 
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Énumérer les ressources restaurables pour l’API SQL

Les cmdlets d’énumération vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel

Exécutez la commande PowerShell `Get-AzCosmosDBRestorableDatabaseAccount` pour répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel.

La réponse comprend tous les comptes de base de données (à la fois actifs et supprimés) qui peuvent être restaurés et les régions à partir desquelles ils peuvent être restaurés.

```json
{
    "accountName": "SampleAccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

À l’instar de l’heure `CreationTime` ou `DeletionTime` pour le compte, il y a également une heure `CreationTime` ou `DeletionTime` pour la région. Ces heures vous permettent de choisir la bonne région et une plage horaire valide pour restaurer dans cette région.

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>Répertorier toutes les versions des bases de données SQL dans un compte de base de données actif

La liste de toutes les versions de bases de données vous permet de choisir la base de données appropriée dans un scénario où le moment réel de l'existence de la base de données est inconnu.

Exécutez la commande PowerShell suivante pour répertorier toutes les versions des bases de données. Cette commande fonctionne uniquement avec des comptes actifs. Les paramètres `DatabaseAccountInstanceId` et `Location` sont obtenus à partir des propriétés `name` et `location` dans la réponse de l’applet de commande `Get-AzCosmosDBRestorableDatabaseAccount`. L’attribut `DatabaseAccountInstanceId` fait référence à la propriété `instanceId` du compte de base de données source en cours de restauration :

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Répertorier toutes les versions des conteneurs SQL d’une base de données dans un compte de base de données actif

Utilisez la commande suivante pour répertorier toutes les versions des conteneurs SQL. Cette commande fonctionne uniquement avec des comptes actifs. Le paramètre `DatabaseRId` est le `ResourceId` de la base de données que vous souhaitez restaurer. Il s’agit de la valeur de l’attribut `ownerResourceid` trouvé dans la réponse de l’applet de commande `Get-AzCosmosdbSqlRestorableDatabase`. La réponse inclut également la liste des opérations effectuées sur tous les conteneurs de cette base de données.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Rechercher des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée

Utilisez la commande suivante pour récupérer la liste des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée. Cette commande fonctionne uniquement avec des comptes actifs.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Énumérer les ressources restaurables dans l’API pour MongoDB

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables. Ces commandes ne fonctionnent que pour les comptes actifs et sont similaires aux commandes de l’API SQL, mais avec `MongoDB` dans le nom de la commande au lieu de `sql`.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Répertorier toutes les versions des bases de données MongoDB dans un compte de base de données actif

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Répertorier toutes les versions des collections mongodb d’une base de données dans un compte de base de données actif

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Répertorier toutes les ressources d’un compte de base de données mongodb qui peuvent être restaurées à une heure et dans une région données

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>Restaurer un compte à l’aide d’Azure CLI

Avant de restaurer le compte, installez Azure CLI en procédant comme suit :

1. Installez la dernière version d’Azure CLI.

   * Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli) ou une version supérieure à 2.26.0
   * Si vous avez déjà installé l’interface CLI, exécutez la commande `az upgrade` pour effectuer la mise à jour vers la dernière version. Cette commande ne fonctionnera qu’avec une version de CLI supérieure à la version 2.11. Si vous disposez d’une version antérieure, utilisez le lien ci-dessus pour installer la dernière version.

1. Se connecter et sélectionner votre abonnement

   * Connectez-vous à votre compte Azure à l’aide de la commande `az login`.
   * Sélectionnez l’abonnement requis à l’aide de la commande `az account set -s <subscriptionguid>`.

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>Déclencher une opération de restauration avec l’interface CLI

La façon la plus simple de déclencher une restauration consiste à lancer la commande de restauration avec le nom du compte cible, le compte source, l’emplacement, le groupe de ressources, le timestamp (UTC) et éventuellement les noms de la base de données et du conteneur. Voici quelques exemples pour déclencher l’opération de restauration :

1. Créez un compte Azure Cosmos DB en le restaurant à partir d’un compte existant.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Créez un compte Azure Cosmos DB en restaurant uniquement les bases de données et les conteneurs sélectionnés à partir d’un compte de base de données existant.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="get-the-restore-details-from-the-restored-account"></a>Récupérer les détails de la restauration à partir du compte restauré

Exécutez la commande suivante pour récupérer les détails de la restauration. Le restoreTimestamp se trouve sous l’objet restoreParameters :

```azurecli-interactive
az cosmosdb show --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Énumérer les ressources restaurables pour l’API SQL

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables.

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>Répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel

Exécutez la commande CLI suivante pour répertorier tous les comptes qui peuvent être restaurés dans l’abonnement actuel.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

La réponse comprend tous les comptes de base de données (à la fois actifs et supprimés) qui peuvent être restaurés et les régions à partir desquelles ils peuvent être restaurés :

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

À l’instar de l’heure `CreationTime` ou `DeletionTime` pour le compte, il y a également une heure `CreationTime` ou `DeletionTime` pour la région. Ces heures vous permettent de choisir la bonne région et une plage horaire valide pour restaurer dans cette région.

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>Répertorier toutes les versions des bases de données dans un compte de base de données actif

L’énumération de toutes les versions de bases de données vous permet de choisir la base de données appropriée dans un scénario où le moment réel de l’existence de la base de données est inconnu.

Exécutez la commande CLI suivante pour répertorier toutes les versions des bases de données. Cette commande fonctionne uniquement avec des comptes actifs. Les paramètres `instance-id` et `location` sont obtenus à partir des propriétés `name` et `location` dans la réponse de la commande `az cosmosdb restorable-database-account list`. L’attribut instanceId est également une propriété du compte de base de données source en cours de restauration :

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Cette sortie de commande indique désormais quand une base de données a été créée et supprimée.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>Répertorier toutes les versions des conteneurs SQL d’une base de données dans un compte de base de données actif

Utilisez la commande suivante pour répertorier toutes les versions des conteneurs SQL. Cette commande fonctionne uniquement avec des comptes actifs. Le paramètre `database-rid` est le `ResourceId` de la base de données que vous souhaitez restaurer. Il s’agit de la valeur de l’attribut `ownerResourceid` trouvé dans la réponse de la commande `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Cette sortie de commande affiche la liste des opérations effectuées sur tous les conteneurs de cette base de données :

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>Rechercher des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée

Utilisez la commande suivante pour récupérer la liste des bases de données ou des conteneurs qui peuvent être restaurés à une heure donnée. Cette commande fonctionne uniquement avec des comptes actifs.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Énumérer les ressources restaurables pour le compte d’API MongoDB

Les commandes d’énumération décrites ci-dessous vous aident à découvrir les ressources disponibles pour la restauration à différents moments. En outre, elles fournissent également un flux d’événements clés sur les ressources du compte, des bases de données et des conteneurs restaurables. Ces commandes fonctionnent uniquement pour les comptes actifs.

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>Répertorier toutes les versions des bases de données mongodb dans un compte de base de données actif

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>Répertorier toutes les versions des collections mongodb d’une base de données dans un compte de base de données actif

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>Répertorier toutes les ressources d’un compte de base de données mongodb qui peuvent être restaurées à une heure et dans une région données

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"></a>Restaurer à l’aide du modèle Resource Manager

Vous pouvez également restaurer un compte à l’aide du modèle Resource Manager. Lorsque vous définissez le modèle, incluez les paramètres suivants :

* Définissez le paramètre `createMode` sur *Restore*.
* Définissez le paramètre `restoreParameters`, et notez que la valeur de `restoreSource` est extraite de la sortie de la commande `az cosmosdb restorable-database-account list` pour votre compte source. L’attribut ID d’instance de votre nom de compte est utilisé pour effectuer la restauration.
* Définissez le paramètre `restoreMode` sur *PointInTime* et configurez la valeur `restoreTimestampInUtc`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Ensuite, déployez le modèle à l’aide d’Azure PowerShell ou de l’interface CLI. L’exemple suivant montre comment déployer le modèle avec une commande CLI :  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Étapes suivantes

* Approvisionnez la sauvegarde continue à l’aide du [portail Azure](provision-account-continuous-backup.md#provision-portal), de [PowerShell](provision-account-continuous-backup.md#provision-powershell), de l’interface [CLI](provision-account-continuous-backup.md#provision-cli) ou d’[Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* [Comment migrer vers un compte à partir d’une sauvegarde périodique vers une sauvegarde continue](migrate-continuous-backup.md).
* [Modèle de ressource du mode de sauvegarde continue.](continuous-backup-restore-resource-model.md)
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
