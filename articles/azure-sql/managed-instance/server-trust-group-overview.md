---
title: Groupe d’approbations de serveurs
titleSuffix: Azure SQL Managed Instance
description: Découvrez comment gérer l’approbation entre les instances à l’aide d’un groupe d’approbations de serveurs dans Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ad3d7c45fb621d3f12146548bdb9e5ac5693a622
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071991"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>Configurer l’approbation entre les instances avec le groupe d’approbations de serveurs (Azure SQL Managed Instance)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Le groupe d’approbations de serveurs (également appelé groupe d’approbations SQL) est un concept utilisé pour gérer l’approbation entre les instances dans Azure SQL Managed Instance. En créant un groupe, une approbation basée sur un certificat est établie entre ses membres. Cette approbation peut être utilisée pour différents scénarios inter-instances. La suppression de serveurs du groupe ou la suppression du groupe supprime l’approbation entre les serveurs. Pour créer ou supprimer un groupe d’approbations de serveurs, l’utilisateur doit disposer d’autorisations en écriture sur l’instance managée.
[Groupe d’approbations de serveurs](/azure/templates/microsoft.sql/allversions) est un objet Azure Resource Manager qui a été étiqueté comme **Groupe d’approbations SQL** dans le portail Azure.


## <a name="set-up-group"></a>Configurer un groupe

Le groupe d’approbations de serveurs peut être configuré via [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup) ou [Azure CLI](/cli/azure/sql/stg). 

Pour créer un groupe d’approbations de serveurs à l’aide du portail Azure, procédez comme suit : 

1. Accédez au [portail Azure](https://portal.azure.com/).

2. Accédez à l’instance Azure SQL Managed Instance que vous prévoyez d’ajouter à un groupe d’approbations de serveurs.

3. Dans la page de paramètres **Sécurité**, sélectionnez l’onglet **Groupes d’approbations SQL**.

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="Groupes d’approbations SQL":::

4. Dans la page de configuration **Groupes d’approbations SQL**, sélectionnez l’icône **Nouveau groupe**.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="Nouveau groupe":::

5. Dans le panneau **Groupe d’approbations SQL**, définissez le **Nom du groupe**. Il doit être unique dans l’abonnement, le groupe de ressources et la région du groupe. L’**étendue d’approbation** définit le type de scénario entre les instances qui est activé avec le groupe d’approbations de serveurs. L’étendue d’approbation est fixe : toutes les fonctionnalités disponibles sont présélectionnées et ne peuvent pas être modifiées. Sélectionnez **Abonnement** et **Groupe de ressources** pour choisir les instances managées qui seront membres du groupe.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="Panneau Créer un groupe d’approbations SQL":::

6. Une fois tous les champs obligatoires remplis, sélectionnez **Enregistrer**.

## <a name="edit-group"></a>Modifier un groupe 

Pour modifier un groupe d’approbations de serveurs, procédez comme suit : 

1. Accédez au portail Azure.
1. Accédez à une instance managée qui appartient au groupe d’approbations.
1. Dans les paramètres de **Sécurité**, sélectionnez l’onglet **Groupes d’approbations SQL**.
1. Sélectionnez le groupe d’approbations que vous souhaitez modifier.
1. Cliquez sur **Configurer le groupe**.

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="Configurer le groupe d’approbations SQL":::

1. Ajoutez ou supprimez des instances managées dans le groupe.
1. Cliquez sur **Enregistrer** pour confirmer la sélection ou sur **Annuler** pour abandonner les modifications.

## <a name="delete-group"></a>Supprimer un groupe

Pour supprimer un groupe d’approbations de serveurs, procédez comme suit : 

1. Accédez au portail Azure.
1. Accédez à une instance managée qui appartient au groupe d’approbations SQL.
1. Dans les paramètres de **Sécurité**, sélectionnez l’onglet **Groupes d’approbations SQL**.
1. Sélectionnez le groupe d’approbations que vous souhaitez supprimer.

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="Sélectionner un groupe d’approbations SQL":::

1. Sélectionnez **Supprimer un groupe**.

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="Supprimer le groupe d’approbations SQL"::: 

1. Entrez le nom du groupe d’approbations SQL pour confirmer la suppression, puis sélectionnez **Supprimer**.

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="Confirmer la suppression du groupe d’approbations SQL":::

> [!NOTE]
> La suppression du groupe d’approbations SQL risque de ne pas supprimer immédiatement l’approbation entre les deux instances managées. La suppression de l’approbation peut être appliquée en appelant un [basculement](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) d’instances managées. Consultez les [Problèmes connus](../managed-instance/doc-changes-updates-known-issues.md) pour obtenir les dernières mises à jour à ce sujet.

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent aux groupes d’approbations de serveurs : 

 * Le groupe peut contenir uniquement des instances d’Azure SQL Managed Instance.
 * Impossible de modifier l’étendue d’approbation lors de la création ou de la modification d’un groupe.
 * Le nom du groupe d’approbations de serveurs doit être unique pour son abonnement, son groupe de ressources et sa région.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les transactions distribuées dans Azure SQL Managed Instance, consultez [Transactions distribuées](../database/elastic-transactions-overview.md).
* Pour obtenir les mises à jour de version et l’état des problèmes connus, consultez [Nouveautés](doc-changes-updates-release-notes-whats-new.md).
* Si vous avez des demandes de fonctionnalités, ajoutez-les au [forum SQL Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance).
