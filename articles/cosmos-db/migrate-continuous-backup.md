---
title: Migrer un compte Azure Cosmos DB du mode de sauvegarde périodique vers le mode de sauvegarde continue
description: Azure Cosmos DB prend actuellement en charge une migration unidirectionnelle du mode périodique en mode continu et il est irréversible. Après la migration du mode périodique vers le mode continu, vous pouvez tirer parti des avantages du mode continu.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 3ac1ae98dfa920a4b4060905e99b7378fcdb3414
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476297"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>Migrer un compte Azure Cosmos DB du mode de sauvegarde périodique vers le mode de sauvegarde continue
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Les comptes Azure CosmosDB avec une stratégie de sauvegarde en mode périodique peuvent être migrés vers le mode continu à l’aide du [portail Azure](#portal), de l’[interface CLI](#cli), de [PowerShell](#powershell) ou des [modèles Resource Manager](#ARM-template). La migration du mode périodique vers le mode continu est une migration unidirectionnelle et n’est pas réversible. Après la migration du mode périodique vers le mode continu, vous pouvez tirer parti des avantages du mode continu.

Voici les principales raisons de migrer en mode continu :

* La possibilité d’effectuer une restauration libre-service à l’aide du portail Azure, de l’interface CLI ou de PowerShell.
* La possibilité de revenir à la granularité temporelle du deuxième dans la dernière fenêtre de 30 jours.
* La possibilité de s’assurer que la sauvegarde est cohérente entre les plages clés d’étendues ou de partitions au cours d’une période.
* La possibilité de restaurer le conteneur, la base de données ou le compte complet lorsqu’il est supprimé ou modifié.
* La possibilité de choisir les événements sur le conteneur, la base de données ou le compte et de décider quand initier la restauration.

> [!NOTE]
> La fonctionnalité de migration est unidirectionnelle uniquement et il s’agit d’une action irréversible. Cela signifie qu’une fois que vous avez effectué la migration du mode périodique vers le mode continu, vous ne pouvez pas revenir au mode périodique.
>
> Vous pouvez migrer un compte en mode de sauvegarde continue uniquement si les conditions suivantes sont remplies. Vérifiez également les [limites de restauration dans le temps](continuous-backup-restore-introduction.md#current-limitations) avant la migration de votre compte :
>
> * Si le compte est de type API SQL ou API pour MongoDB.
> * Si le compte a une seule région d’écriture.
> * Si le compte n’est pas activé avec des clés gérées par le client (CMK).
> * Si le compte n’est pas activé avec le magasin analytique.

## <a name="permissions"></a>Autorisations

Pour effectuer la migration, vous avez besoin d’autorisation `Microsoft.DocumentDB/databaseAccounts/write` pour le compte en cours de migration.  

## <a name="pricing-after-migration"></a>Tarification après la migration

Après la migration de votre compte en mode de sauvegarde continue, le coût associé à ce mode est différent par rapport au mode de sauvegarde périodique. Le coût de la sauvegarde en mode continu est beaucoup moins cher que le mode périodique. Pour en savoir plus, consultez l’exemple de [tarification en mode de sauvegarde continue](continuous-backup-restore-introduction.md#continuous-backup-pricing).

## <a name="migrate-using-portal"></a><a id="portal"></a> Migrer à l’aide du portail

Procédez comme suit pour migrer votre compte de la sauvegarde périodique vers le mode de sauvegarde continue :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre compte Azure Cosmos DB et ouvrez le volet **Nouvelles fonctionnalités**. Sélectionnez **Sauvegarde continue** et sélectionnez **Activez**.

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="Migrer vers le mode continu à l’aide du portail Azure" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. Lorsque la migration est en cours, l’état indique **En cours.** Une fois l’opération terminée, l’état passe à **Activé.** Le moment de la migration dépend de la taille des données de votre compte.

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="Vérifier l’état de la migration à partir du portail Azure" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>Migrer à l’aide de PowerShell

Installez la [dernière version d’Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) ou une version supérieure à 6.2.0. Ensuite, effectuez les étapes suivantes :

1. Connectez-vous à votre compte Azure :

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. Migrez votre compte du mode de sauvegarde périodique vers le mode continu :

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

### <a name="check-the-migration-status"></a>Vérifier l’état de la migration

Exécutez la commande suivante et vérifiez les propriétés **status** et **targetType** de l’objet **backupPolicy**. L’état indique « en cours » après le démarrage de la migration :

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-started-powershell.png" alt-text="Vérifier l’état de la migration à l’aide de la commande PowerShell":::

Une fois la migration terminée, le type de sauvegarde passe à **Continue**. Exécutez à nouveau la même commande pour vérifier l’état :

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-complete-powershell.png" alt-text="Le type de sauvegarde devient Continue une fois la migration terminée":::

## <a name="migrate-using-cli"></a><a id="cli"></a>Migrer en utilisant l’interface CLI

1. Installez la dernière version d’Azure CLI :

   * Si vous n’avez pas d’interface CLI, [installez](/cli/azure/) la dernière version d’Azure CLI ou une version supérieure à 2.26.0.
   * Si Azure CLI est déjà installé, utilisez la commande `az upgrade` pour effectuer une mise à niveau vers la version la plus récente.
   * L’utilisateur peut également utiliser Cloud Shell à partir du portail Azure.

1. Connectez-vous à votre compte Azure et exécutez la commande suivante pour migrer votre compte en mode continu :

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. Une fois la migration terminée, la sortie indique que la propriété type de l’objet backupPolicy a la valeur Continuous.

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a> Migrer à l’aide d’un modèle Resource Manager

Pour migrer vers le mode de sauvegarde continue à l’aide d’un modèle ARM, recherchez la section backupPolicy de votre modèle et mettez à jour la propriété `type`. Par exemple, si votre modèle existant possède une stratégie de sauvegarde telle que l’objet JSON suivant :

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

Remplacez-la par l’objet JSON suivant :

```json
"backupPolicy": {
   "type": "Continuous"
},
```

Ensuite, déployez le modèle à l’aide d’Azure PowerShell ou de l’interface CLI. L’exemple suivant montre comment déployer le modèle avec une commande CLI :

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>Que se passe-t-il pendant et après la migration ?

Lors de la migration du mode périodique en mode continu, vous ne pouvez pas exécuter les opérations du plan de contrôle qui effectuent des mises à jour ou des suppressions au niveau du compte. Par exemple, les opérations telles que l’ajout ou la suppression de régions, le basculement de compte, la mise à jour de la stratégie de sauvegarde, etc., ne peuvent pas être exécutées pendant la migration. Le temps de la migration dépend de la taille des données et du nombre de régions de votre compte. L’action de restauration sur les comptes migrés ne réussit qu’à partir du moment où la migration se termine correctement.

Vous pouvez restaurer votre compte une fois la migration terminée. Si la migration se termine à 13 h 00 PST, vous pouvez effectuer une restauration dans le temps à partir de 13 h 00 PST.

## <a name="frequently-asked-questions"></a>Forum aux questions

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>La migration se produit-elle uniquement au niveau du compte ?
Oui.

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>Quels comptes peuvent être ciblés pour la migration de sauvegarde ?
Actuellement, l’API SQL et l’API pour les comptes MongoDB avec une seule région d’écriture, qui ont partagé, provisionné ou provisionné par mise à l’échelle automatique une migration de prise en charge de débit.

Les comptes activés avec le stockage analytique, les régions à plusieurs écritures et les clés gérées par le client (CMK) ne sont pas pris en charge pour la migration.

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>La migration prend-elle du temps ? Quelle est la durée habituelle ?
Le temps de la migration dépend de la taille des données et du nombre de régions de votre compte. Vous pouvez obtenir l’état de la migration à l’aide d’Azure CLI ou de commandes PowerShell. Pour les comptes volumineux avec des dizaines de téraoctets de données, la migration peut prendre jusqu’à quelques jours.

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>La migration entraîne-t-elle un impact sur la disponibilité ou un temps d’arrêt ?
Non, l’opération de migration a lieu en arrière-plan, de sorte que les requêtes des clients ne sont pas affectées. Toutefois, nous devons effectuer des opérations de serveur principal pendant la migration, et cela peut prendre un certain temps si le compte est soumis à une charge importante.

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>Que se passe-t-il si la migration échoue ? Vais-je continuer à effectuer les sauvegardes périodiques ou à récupérer les sauvegardes continues ?
Une fois le processus de migration démarré, le compte commence à devenir un mode continu.  Si la migration échoue, vous devez initier la migration jusqu’à ce qu’elle réussisse.

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>Comment faire une restauration à un horodatage avant/pendant/après la migration ?
Supposons que vous avez démarré la migration à t1 et que vous avez terminé à t5, vous ne pouvez pas utiliser un horodateur de restauration entre t1 et t5.

Pour restaurer à une heure après t5 parce que votre compte est maintenant en mode continu, vous pouvez effectuer la restauration à l’aide du portail Azure, de l’interface CLI ou de PowerShell comme vous le feriez normalement avec un compte continu. Cette requête de restauration libre-service ne peut être effectuée qu’une fois la migration terminée.

Pour effectuer une restauration jusqu’à une heure antérieure à t1, vous pouvez ouvrir un ticket de support comme vous le feriez normalement avec le compte de sauvegarde périodique. Après la migration, vous avez jusqu’à 30 jours pour effectuer la restauration périodique.  Pendant ces 30 jours, vous pouvez restaurer en fonction de la rétention/l’intervalle de sauvegarde de votre compte avant la migration.  Par exemple, si la configuration de sauvegarde devait conserver 24 copies par intervalle de 1 heure, vous pouvez restaurer à tout moment entre [t1 – 24 heures] et [t1].

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>Quelles opérations du plan de contrôle au niveau du compte sont bloquées pendant la migration ?
Les opérations telles que l'ajout/la suppression d'une région, le basculement, la modification de la politique de sauvegarde, les changements de débit entraînant un déplacement des données sont bloquées pendant la migration.

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>Si la migration échoue pour un problème sous-jacent, est-ce qu’elle bloque toujours l’opération de plan de contrôle jusqu’à ce qu’elle soit retentée et terminée correctement ?
L’échec de la migration ne bloque pas les opérations du plan de contrôle. Si la migration échoue, il est recommandé de réessayer jusqu’à ce qu’elle réussisse avant d’effectuer d’autres opérations de plan de contrôle.

#### <a name="is-it-possible-to-cancel-the-migration"></a>Est-il possible d’annuler la migration ?
Il n’est pas possible d’annuler la migration, car il ne s’agit pas d’une opération réversible.

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>Existe-t-il un outil qui peut aider à estimer la durée de la migration en fonction de l’utilisation des données et du nombre de régions ?
Il n’existe pas d’outil pour estimer le temps. Toutefois, nos série de mise à l’échelle indiquent qu’une seule région avec 1 To de données prend environ une heure et demie.

Pour les comptes à plusieurs régions, calculez la taille totale des données en tant que `Number_of_regions * Data_in_single_region`.

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>Étant donné que le mode de sauvegarde continue est désormais GA, recommanderiez-vous quand même de restaurer une copie de votre compte et d’essayer la migration sur la copie avant de décider de migrer le compte de production ?
Il est recommandé de tester la fonctionnalité de mode de sauvegarde continue pour voir qu’elle fonctionne comme prévu avant de migrer les comptes de production. Parce que la migration est une opération unidirectionnelle et qu’elle n’est pas réversible.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le mode de sauvegarde continue, consultez les articles suivants :

* [Présentation du mode de sauvegarde continue avec une limite de restauration dans le temps.](continuous-backup-restore-introduction.md)

* [Modèle de ressource du mode de sauvegarde continue.](continuous-backup-restore-resource-model.md)

* Restaurez un compte à l’aide du [portail Azure](restore-account-continuous-backup.md#restore-account-portal), de [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), de l’interface [CLI](restore-account-continuous-backup.md#restore-account-cli) ou d’[Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ?
   * Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
   * Si vous connaissez les taux de requêtes types de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
