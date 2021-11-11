---
title: Découvrez comment gérer les comptes de base de données dans Azure Cosmos DB
description: Apprenez à gérer les ressources Azure Cosmos DB à l’aide du portail Azure, de PowerShell, de l’interface CLI et des modèles Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 6046468281878f8ae6b9ac9f802bb16fd378c616
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434139"
---
# <a name="manage-an-azure-cosmos-account-using-the-azure-portal"></a>Gérer un compte Azure Cosmos en utilisant le portail Azure

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Cet article explique comment gérer différentes tâches sur un compte Azure Cosmos en utilisant le portail Azure. 

> [!TIP]
> Azure Cosmos DB peut aussi être géré avec d’autres clients de gestion Azure, notamment [Azure PowerShell](manage-with-powershell.md), [Azure CLI](sql/manage-with-cli.md), des [modèles Azure Resource Manager](./manage-with-templates.md) et [Bicep](sql/manage-with-bicep.md).

## <a name="create-an-account"></a>Créer un compte

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

## <a name="addremove-regions-from-your-database-account"></a>Ajouter/supprimer des régions à partir de votre compte de base de données

> [!TIP]
> Quand une nouvelle région est ajoutée, toutes les données doivent être entièrement répliquées et validées dans la nouvelle région pour que celle-ci soit marquée comme disponible. Le temps nécessaire à cette opération dépend de la quantité de données stockées dans le compte. Si une [opération de mise à l’échelle du débit asynchrone](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus) est en cours, l’opération de mise à l’échelle du débit est suspendue et reprend automatiquement une fois l’opération d’ajout ou de suppression de région terminée. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

1. Pour ajouter des régions, sélectionnez les hexagones sur la carte avec l’étiquette **+** correspondante. Sinon, pour ajouter une région, sélectionnez l’option **+ Ajouter une région**, puis choisissez une région dans le menu déroulant.

1. Pour supprimer des régions, désactivez une ou plusieurs régions à partir de la carte en sélectionnant les hexagones bleus avec des coches. Vous pouvez aussi sélectionner l’icône de la « Corbeille » (🗑) en regard de la région, sur le côté droit.

1. Pour enregistrer vos modifications, sélectionnez **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Ajouter ou supprimer le menu des régions":::

Dans un mode écriture dans une seule région, vous ne pouvez pas supprimer la région d’écriture. Vous devez basculer vers une autre région avant de pouvoir supprimer la région d’écriture actuelle.

Dans un mode d’écriture dans plusieurs régions, vous pouvez ajouter ou supprimer n’importe quelle région si vous en avez au moins une.

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configurer plusieurs régions d’écriture

Ouvrez l’onglet **Répliquer les données globalement** et sélectionnez **Activer** pour activer l’écriture dans plusieurs régions. Une fois que vous avez activé l’écriture dans plusieurs régions, toutes les régions de lecture indiquées sur votre compte deviennent des régions de lecture et d’écriture.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Capture d’écran de la configuration des écritures multirégions dans un compte Azure Cosmos":::

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Activer le basculement automatique pour votre compte Azure Cosmos

L’option de basculement automatique permet à Azure Cosmos DB de basculer vers la région ayant la priorité de basculement la plus élevée sans action de l’utilisateur au cas où une région devienne indisponible. Lorsque le basculement automatique est activé, la priorité des régions peut être modifiée. Le compte doit avoir au moins deux régions pour activer le basculement automatique.

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu pour répliquer les données globalement":::

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**. 

4. Sélectionnez **Enregistrer**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu du portail pour le basculement automatique":::

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Définir les priorités de basculement pour votre compte Azure Cosmos

Une fois qu’un compte Cosmos est configuré pour le basculement automatique, la priorité de basculement des régions peut être modifiée.

> [!IMPORTANT]
> Vous ne pouvez pas modifier la région d’écriture (priorité de basculement de zéro) lorsque le compte est configuré pour le basculement automatique. Pour changer la région d’écriture, vous devez désactiver le basculement automatique et effectuer un basculement manuel.

1. Dans votre compte Azure Cosmos, ouvrez le volet **Répliquer les données globalement**.

2. En haut du volet, sélectionnez **Basculement automatique**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu pour répliquer les données globalement":::

3. Dans le volet **Basculement automatique**, vérifiez que l’option **Activer le basculement automatique** est définie sur **ACTIVÉ**.

4. Pour modifier la priorité de basculement, faites glisser les régions de lecture via les trois points à gauche de la ligne qui s’affichent quand vous placez le curseur dessus.

5. Sélectionnez **Enregistrer**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu du portail pour le basculement automatique":::

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Effectuer un basculement manuel sur un compte Azure Cosmos

> [!IMPORTANT]
> Le compte Azure Cosmos doit être configuré pour le basculement manuel pour que l’opération fonctionne.

> [!NOTE]
> Si vous effectuez une opération de basculement manuel alors qu’une [opération de mise à l’échelle du débit asynchrone](scaling-provisioned-throughput-best-practices.md#background-on-scaling-rus) est en cours, l’opération de mise à l’échelle du débit est suspendue. Elle reprend automatiquement une fois l’opération de basculement terminée.

1. Accédez à votre compte Azure Cosmos, puis ouvrez le menu **Répliquer les données globalement**.

2. En haut du menu, sélectionnez **Basculement manuel**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu pour répliquer les données globalement":::

3. Dans le menu **Basculement manuel**, sélectionnez votre nouvelle région d’écriture. Sélectionnez la case à cocher pour indiquer que vous comprenez que cette option modifie votre région d’écriture.

4. Pour déclencher le basculement, sélectionnez **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menu du portail pour le basculement manuel":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et d’exemples de gestion du compte Azure Cosmos ainsi que des bases de données et conteneurs, consultez les articles suivants :

* [Gérer Azure Cosmos DB à l’aide d’Azure PowerShell](manage-with-powershell.md)
* [Gérer Azure Cosmos DB à l’aide d’Azure CLI](sql/manage-with-cli.md)
* [Gérer Azure Cosmos DB en utilisant des modèles Azure Resource Manager](./manage-with-templates.md)
* [Gérer Azure Cosmos DB en utilisant Bicep](sql/manage-with-bicep.md)
