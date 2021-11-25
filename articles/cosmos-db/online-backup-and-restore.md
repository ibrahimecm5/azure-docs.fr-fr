---
title: Sauvegarde en ligne et restauration de données à la demande dans Azure Cosmos DB
description: Cet article décrit le fonctionnement de la sauvegarde automatique et de la restauration des données à la demande. Il explique également la différence entre les modes de sauvegarde continue et périodique.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/15/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a87371e697d39b73a647e7dea1dbbceb1e028d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523217"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Sauvegarde en ligne et restauration de données à la demande dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB sauvegarde automatiquement vos données à intervalles réguliers. Les sauvegardes automatiques sont effectuées sans affecter les performances ou la disponibilité des opérations de base de données. Toutes les sauvegardes sont stockées séparément dans un service de stockage. Les sauvegardes automatiques sont utiles dans les scénarios où vous supprimez ou mettez à jour accidentellement votre compte, base de données ou conteneur Azure Cosmos et où vous avez besoin ultérieurement de récupérer les données. Les sauvegardes d’Azure Cosmos DB sont chiffrées à l’aide de clés de service géré Microsoft. Ces sauvegardes sont transférées sur un réseau sécurisé non public. Cela signifie que les données de sauvegarde restent chiffrées pendant leur transfert et au repos. Les sauvegardes d’un compte dans une région donnée sont chargées sur des comptes de stockage dans la même région.

## <a name="backup-modes"></a>Modes de sauvegarde

Il existe deux modes de sauvegarde :

* **Mode de sauvegarde périodique** : Ce mode est le mode de sauvegarde par défaut pour tous les comptes existants. Dans ce mode, la sauvegarde est effectuée à intervalles réguliers et les données sont restaurées en adressant une demande au support technique. Dans ce mode, vous configurez un intervalle de sauvegarde et une rétention pour votre compte. La période de rétention maximale s’étend jusqu’à un mois. L’intervalle minimal de sauvegarde peut être d’une heure.  Pour plus d’informations, consultez l’article [Mode de sauvegarde périodique](configure-periodic-backup-restore.md).

* **Mode de sauvegarde continue** : Vous choisissez ce mode lors de la création du compte Azure Cosmos DB. Ce mode vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours. Pour plus d’informations, consultez les articles [Introduction au mode de sauvegarde continue](continuous-backup-restore-introduction.md), provisionner la sauvegarde continue avec le [portail Azure](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli) ou [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).

  > [!NOTE]
  > Si vous configurez un nouveau compte avec une sauvegarde continue, vous pouvez effectuer une restauration en libre-service à l’aide de Portail Azure, de PowerShell ou de l’interface CLI. Si votre compte est configuré en mode continu, vous ne pouvez pas revenir en mode périodique.

Pour les comptes avec Azure Synapse Link, les données du magasin analytique ne sont pas incluses dans les sauvegardes et les restaurations. Lorsque Synapse Link est activé, Azure Cosmos DB continue à effectuer des sauvegardes automatiques de vos données dans le magasin transactionnel à un intervalle de sauvegarde planifié. La sauvegarde et la restauration automatiques de vos données dans le magasin analytique ne sont pas prises en charge pour le moment.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="can-i-restore-from-an-account-a-in-subscription-s1-to-account-b-in-a-subscription-s2"></a>Puis-je effectuer une restauration à partir d’un compte A de l’abonnement S1 vers un compte B de l’abonnement S2 ?

Non. Vous pouvez uniquement effectuer une restauration entre des comptes au sein du même abonnement.

### <a name="can-i-restore-into-an-account-that-has-fewer-partitions-or-low-provisioned-throughput-than-the-source-account"></a>Puis-je effectuer une restauration dans un compte qui a moins de partitions ou un débit d’approvisionnement plus faible que le compte source ?

Non. Vous ne pouvez pas effectuer de restauration dans un compte ayant un débit de RU/s plus faible ou moins de partitions.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite découvrir comment configurer et gérer les modes de sauvegarde périodique et continue pour votre compte :

* [Configurer et gérer la stratégie de sauvegarde périodique](configure-periodic-backup-restore.md).
* Qu’est-ce que le mode de [sauvegarde continue](continuous-backup-restore-introduction.md) ?
* Provisionnez la sauvegarde continue avec le [portail Azure](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), l’interface [CLI](provision-account-continuous-backup.md#provision-cli) ou [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaurez le compte de sauvegarde continue avec le [Portail Microsoft Azure](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), l’interface [CLI](restore-account-continuous-backup.md#restore-account-cli) ou [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Migrer vers un compte à partir d’une sauvegarde périodique vers une sauvegarde continue](migrate-continuous-backup.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).
