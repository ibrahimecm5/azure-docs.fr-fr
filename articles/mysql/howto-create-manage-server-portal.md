---
title: Gérer un serveur - Portail Azure - Azure Database pour MySQL
description: Découvrez comment gérer un serveur Azure Database pour MySQL à partir du portail Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 5e6f21b2ae49539a8728a307eb33bfdba33f9688
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633048"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Gérer un serveur Azure Database pour MySQL à l’aide du portail Azure

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Cet article vous explique comment gérer vos serveurs Azure Database pour MySQL. Les tâches de gestion incluent notamment la mise à l’échelle du calcul et du stockage, la réinitialisation de mot de passe et l’affichage des informations relatives au serveur.

> [!NOTE]
> Cet article contient des références au terme *esclave*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.
>

## <a name="sign-in"></a>Se connecter

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Créer un serveur

Consultez le [guide de démarrage rapide](quickstart-create-mysql-server-database-using-azure-portal.md) pour savoir comment créer et prendre en main un serveur Azure Database pour MySQL.

## <a name="scale-compute-and-storage"></a>Mettre à l’échelle le calcul et le stockage

Une fois le serveur créé, vous pouvez mettre à l’échelle entre les niveaux Usage général et Mémoire optimisée en fonction de vos besoins. Vous pouvez également mettre à l’échelle le calcul et la mémoire en augmentant ou en diminuant le nombre de vCores. Le stockage peut être monté en puissance (mais pas descendu en puissance).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Mettre à l’échelle entre les niveaux Usage général et Mémoire optimisée

Vous pouvez passer du niveau Usage général au niveau Mémoire optimisée et inversement. Le passage au niveau De base ou son remplacement par un autre niveau de service n’est pas pris en charge après la création du serveur.

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Sélectionnez **Usage général** ou **Mémoire optimisée**, selon le niveau souhaité.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Capture d’écran du Portail Azure pour choisir un niveau De base, Usage général ou À mémoire optimisée dans Azure Database pour MySQL":::

   > [!NOTE]
   > Le changement de niveau entraîne un redémarrage du serveur.

3. Sélectionnez **OK** pour enregistrer les modifications.

### <a name="scale-vcores-up-or-down"></a>Scale-up ou scale-down des vCores

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Modifiez le paramètre **vCore** en déplaçant le curseur vers la valeur souhaitée.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Capture d’écran du Portail Azure pour choisir l’option vCore dans Azure Database pour MySQL":::

    > [!NOTE]
    > La mise à l’échelle des vCores entraîne un redémarrage du serveur.

3. Sélectionnez **OK** pour enregistrer les modifications.

### <a name="scale-storage-up"></a>Scale-up du stockage

1. Dans le Portail Azure, sélectionnez votre serveur. Sélectionnez **Niveau tarifaire**, dans la section **Paramètres**.

2. Modifiez le paramètre **Stockage** en déplaçant le curseur vers la valeur souhaitée.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Capture d’écran du Portail Azure pour choisir le scale-in de stockage dans Azure Database pour MySQL":::

   > [!NOTE]
   > Le stockage ne peut pas faire l’objet d’un scale-down.

3. Sélectionnez **OK** pour enregistrer les modifications.

## <a name="update-admin-password"></a>Mettre à jour le mot de passe administrateur

Vous pouvez modifier le mot de passe du rôle d’administrateur à l’aide du Portail Azure.

1. Dans le Portail Azure, sélectionnez votre serveur. Dans la fenêtre **Vue d’ensemble**, sélectionnez **Réinitialiser le mot de passe**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Capture d’écran du Portail Azure pour réinitialiser le mot de passe dans Azure Database pour MySQL":::

2. Entrez un nouveau mot de passe et confirmez-le. La zone de texte vous indique les exigences en matière de complexité du mot de passe.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Capture d’écran du Portail Azure pour réinitialiser votre mot de passe et enregistrer dans Azure Database pour MySQL":::

3. Sélectionnez **OK** pour enregistrer le nouveau mot de passe.
 

> [!IMPORTANT]
> La réinitialisation du mot de passe de l’administrateur du serveur réinitialise automatiquement les privilèges de l’administrateur du serveur sur les valeurs par défaut. Envisagez de réinitialiser votre mot de passe de l’administrateur du serveur si vous avez accidentellement révoqué un ou plusieurs des privilèges de l’administrateur du serveur.
   
> [!NOTE]
> Par défaut, l’utilisateur administrateur du serveur dispose des privilèges suivants : SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

## <a name="delete-a-server"></a>Supprimer un serveur

Vous pouvez supprimer votre serveur si vous n’en avez plus besoin.

1. Dans le Portail Azure, sélectionnez votre serveur. Dans la fenêtre **Vue d’ensemble**, sélectionnez **Supprimer**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Capture d’écran du Portail Azure pour supprimer le serveur dans Azure Database pour MySQL":::

2. Entrez le nom du serveur dans la zone d’entrée pour confirmer qu’il s’agit du serveur à supprimer.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Capture d’écran du Portail Azure pour confirmer la suppression du serveur dans Azure Database pour MySQL":::

   > [!NOTE]
   > La suppression d’un serveur est irréversible.

3. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [sauvegardes et les restaurations de serveur](howto-restore-server-portal.md)
- En savoir plus sur les [options d’optimisation et de surveillance dans Azure Database pour MySQL](concepts-monitoring.md)
