---
title: Restaurer un serveur flexible Azure Database pour MySQL avec le portail Azure
description: Cet article explique comment effectuer des opérations de restauration dans un serveur flexible Azure Database pour MySQL à l’aide du portail Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: cce31cc3d83be03da462c9345c1b8d3d86ef6228
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467906"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Restauration à un instant dans le passé d’un serveur flexible Azure Database pour MySQL à l’aide du portail Azure

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Cet article indique la procédure pas-à-pas à suivre pour effectuer des restaurations à un instant dans le passé sur un serveur flexible à l’aide de sauvegardes.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Vous devez disposer d’un serveur flexible Azure Database pour MySQL.

## <a name="restore-to-the-latest-restore-point"></a>Restaurer au dernier point de restauration

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2. Cliquez sur **Vue d’ensemble** dans le panneau gauche.

3. Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

4. La page Restaurer s’affiche et vous permet de choisir entre **Dernier point de restauration** et Point de restauration personnalisé.

5. Sélectionnez **Dernier point de restauration**.

6. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="Heure de restauration la plus ancienne":::

7. Cliquez sur **OK**.

8. Une notification indique que l’opération de restauration est lancée.


## <a name="restore-to-a-fastest-restore-point"></a>Restaurer sur un point de restauration plus rapide

Procédez comme suit pour restaurer votre serveur flexible à l’aide d’une sauvegarde complète existante comme point de restauration le plus rapide. 

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde. 

2. Cliquez sur **Vue d’ensemble** dans le panneau gauche. 

3. Dans la page de vue d’ensemble, cliquez sur **Restaurer**. 

4. La page Restaurer s’affiche et vous permet de choisir entre Dernier point de restauration, Point de restauration personnalisé et Point de restauration le plus rapide. 

5. Sélectionnez l’option **Sélectionner le point de restauration le plus rapide (restauration à l’aide de la sauvegarde complète)** . 

6. Sélectionnez la sauvegarde complète souhaitée dans la liste déroulante **Point de restauration le plus rapide (UTC)** . 
 
    :::image type="content" source="./media/how-to-restore-server-portal/fastest-restore-point.png" alt-text="Point de restauration le plus rapide":::

7. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

8. Cliquez sur **Vérifier + créer**. 

9. Après avoir cliqué sur **Créer**, une notification indique que l’opération de restauration est lancée.  

## <a name="restore-from-a-full-backup-through-the-backup-and-restore-blade"></a>Restaurer à partir d’une sauvegarde complète via le panneau Sauvegarde et restauration

Suivez ces étapes pour restaurer votre serveur flexible à l’aide d’une sauvegarde complète existante. 

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde. 

2. Dans le volet gauche, cliquez sur **Sauvegarde et restauration**. 

3. La page Afficher les sauvegardes disponibles s’affiche avec l’option permettant de restaurer toutes les sauvegardes automatisées complètes effectuées pour le serveur pendant la période de rétention.  

4. Sélectionnez la sauvegarde complète souhaitée dans la liste en cliquant sur l’action de **Restauration** correspondante. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/view-available-backups.png" alt-text="Afficher les sauvegardes disponibles":::

5. La page Restaurer s’affiche avec l’option Point de restauration le plus rapide sélectionnée par défaut et le timestamp de sauvegarde complète souhaité sélectionné sur la page Afficher les sauvegardes disponibles. 

6. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

7. Cliquez sur **Vérifier + créer**. 

8. Après avoir cliqué sur **Créer**, une notification indique que l’opération de restauration est lancée.  


## <a name="geo-restore-to-latest-restore-point"></a>Géo-restaurer au dernier point de restauration

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2. Cliquez sur **Vue d’ensemble** dans le panneau gauche.

3. Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

4. La page Restaurer s’affiche avec une option permettant de choisir la **Restauration géo-redondante**. Si vous avez configuré votre serveur pour les sauvegardes géographiquement redondantes, le serveur peut être restauré dans la région associée Azure correspondante et l’option de restauration géo-redondante peut être activée. L’option de restauration géo-redondante restaure le serveur au dernier timestamp UTC et, par conséquent, après la sélection de la restauration géo-redondante, les options de restauration à un instant dans le passé ne peuvent pas être sélectionnées simultanément.

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-flex.png" alt-text="Option de géo-restauration":::

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-enabled-flex.png" alt-text="Activation de la géo-restauration":::

5. Fournissez un nouveau nom de serveur dans le champ **Nom** de la section Détails du serveur.

6. Sélectionnez **Vérifier + Créer** pour passer en revue vos sélections. 

7. Une notification indique que l’opération de restauration est lancée. Cette opération peut prendre quelques minutes. 

Le serveur créé par la restauration géographique possède les mêmes ID de connexion administrateur de serveur et mot de passe qui étaient valides pour le serveur existant au moment où la restauration a été initiée. Le mot de passe peut être modifié sur la page Vue d’ensemble du nouveau serveur. En outre, au cours d’une géo-restauration, vous pouvez configurer des paramètres de **Mise en réseau**, tels que des paramètres de réseau virtuel et des règles de pare-feu, comme décrit dans la section ci-dessous.

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Utilisation de la restauration pour déplacer un serveur d’un accès public à un accès privé

Effectuez ces étapes pour restaurer votre serveur flexible à l’aide de la plus ancienne sauvegarde existante.

1. Dans le [portail Azure](https://portal.azure.com/), choisissez le serveur flexible à partir duquel vous voulez restaurer la sauvegarde.

2. Dans la page de vue d’ensemble, cliquez sur **Restaurer**.

3. La page Restaurer s’affiche avec une option permettant de choisir entre la géo-restauration ou la restauration à un instant dans le passé.

4. Choisissez l’option de **Géo-restauration** ou l’option de **Restauration à un instant dans le passé**.

5. Indiquez un nouveau nom de serveur dans le champ **Restaurer sur un nouveau serveur**.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="vue d’ensemble":::

6. Accédez à l’onglet **Réseau** pour configurer vos options réseau.

7. Dans **Méthode de connectivité**, sélectionnez **Accès privé (intégration au réseau virtuel)** . Accédez à la section **Réseau virtuel**. Vous pouvez sélectionner un *réseau virtuel* et un *sous-réseau* déjà existants qui sont délégués à *Microsoft. DBforMySQL/flexibleServers*, ou en créer un en cliquant sur le lien *Créer un réseau virtuel*.
    > [!Note]
    > Seuls les réseaux virtuels et les sous-réseaux de la même région et du même abonnement seront listés dans la liste déroulante. </br>
    > Le sous-réseau choisi sera délégué à *Microsoft.DBforMySQL/flexibleServers*. Cela signifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Configuration de réseau virtuel":::

8. Créez une **Zone DNS privée** ou sélectionnez-en une qui existe déjà.
    > [!NOTE]
    > Les noms des zones DNS privées doivent se terminer par `mysql.database.azure.com`. </br>
    > Si vous ne voyez pas l’option permettant de créer une zone DNS privée, entrez le nom du serveur sous l’onglet **De base**.</br>
    > Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un accès public (adresses IP autorisées).</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="Configuration de DNS":::
9. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.
10. Sélectionnez **Créer** pour approvisionner le serveur. L'approvisionnement peut prendre quelques minutes.

11. Une notification indique que l’opération de restauration est lancée.


## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Une fois la restauration terminée, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est censé remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur.
- Vérifiez que les règles de réseau virtuel appropriées sont en place pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
- Vérifiez que les connexions et les autorisations appropriées au niveau de la base de données sont en place.
- Configurez les alertes comme il convient pour le serveur nouvellement restauré.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [continuité de l’activité](concepts-business-continuity.md)
