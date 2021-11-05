---
title: Restaurer Azure Database pour PostgreSQL
description: Découvrez comment restaurer les sauvegardes d’Azure Database pour PostgreSQL.
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2eb0d8bd462c50a0da09b4da059d8c0d185dbd3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020518"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>Restaurer les sauvegardes d’Azure Database pour PostgreSQL (préversion)

Cet article explique comment restaurer une base de données sur un serveur Azure PostgreSQL sauvegardé par la Sauvegarde Azure.

Vous pouvez restaurer une base de données sur n’importe quel serveur Azure PostgreSQL au sein du même abonnement, si le service dispose de l’[ensemble d’autorisations](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) approprié sur le serveur cible.

## <a name="restore-azure-postgresql-database"></a>Restaurer la base de données Azure PostgreSQL

1. Accédez à **Coffre de sauvegarde** -> **Instances de sauvegarde**. Sélectionnez une base de données et cliquez sur **Restaurer**.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="Capture d’écran montrant le processus de sélection et de restauration d’une base de données." lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   Vous pouvez également accéder à cette page à partir du [Centre de sauvegarde](./backup-center-overview.md).    
  
1. Sur la page **Sélectionner un point de restauration**, sélectionnez un point de récupération dans la liste de toutes les sauvegardes complètes disponibles pour l’instance de sauvegarde sélectionnée. Le dernier point de récupération est sélectionné par défaut.

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="Capture d’écran montrant le processus de sélection d’un point de récupération." lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   Si le point de restauration se trouve dans le niveau archive, vous devez réhydrater le point de récupération avant la restauration. Fournissez les paramètres supplémentaires suivants requis pour la réhyratation :

   - **Priorité de réhydratation** : la valeur par défaut est **Standard**.
   - **Durée de réhydratation** : la durée maximale de réhydratation est de 30 jours, et la durée de réhydratation minimale est de 10 jours. La valeur par défaut est **15 jours**. Le point de récupération est stocké dans le **Magasin de données de sauvegarde** pendant cette durée.

1. Sur la page **Paramètres de restauration**, sélectionnez l’un des types de ressource suivants : **Restaurer en tant que base de données** ou **Restaurer en tant que fichiers**.

   - **Restaurer en tant que base de données**

     Le serveur cible peut être le même que le serveur source. Cependant, le remplacement de la base de données d’origine n’est pas pris en charge. Vous pouvez choisir le serveur parmi tous les abonnements, mais dans la même région que celle du coffre.

     1. Dans la liste déroulante **Sélectionner le coffre de clés et le secret**, sélectionnez un coffre qui stocke les informations de connexion au serveur cible.

     1. Sélectionnez **Vérifier + restaurer** pour déclencher la validation afin de vérifier si le service dispose des [autorisations de restauration sur le serveur cible](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore). Ces autorisations doivent être [accordées manuellement](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="Capture d’écran montrant le type de restauration sélectionné, Restaurer en tant que base de données." lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - **Restaurer en tant que fichiers : placer une copie des fichiers de sauvegarde dans le compte de stockage cible (objets blob).**

     Vous pouvez choisir un compte de stockage parmi tous les abonnements, mais dans la même région que celle du coffre.     

     1. Dans la liste déroulante **Sélectionner le conteneur cible**, sélectionnez l’un des conteneurs filtrés pour le compte de stockage sélectionné.
     1. Sélectionnez **Vérifier + restaurer** pour déclencher la validation afin de vérifier si le service de sauvegarde dispose des [autorisations de restauration sur le compte de stockage cible](#restore-permissions-on-the-target-storage-account).

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="Capture d’écran montrant le type de restauration sélectionné, Restaurer en tant que fichiers." lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. Soumettez l’opération de restauration et suivez la tâche déclenchée sous **Travaux de sauvegarde**.
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="Capture d’écran montrant le suivi de la tâche déclenchée sous Travaux de sauvegarde." lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>La prise en charge de l’archivage pour Azure Database pour PostgreSQL est en préversion publique limitée.

## <a name="restore-permissions-on-the-target-storage-account"></a>Autorisations de restauration sur le compte de stockage cible

Attribuez à l’identité MSI du coffre de sauvegarde l’autorisation d’accéder aux conteneurs du compte de stockage en utilisant le portail Azure.

1. Accédez à **Compte de stockage** -> **Contrôle d’accès** -> **Ajouter une attribution de rôle**.

1. Sélectionnez le rôle **Contributeur aux données blob du stockage** dans la liste déroulante **Rôle** pour le coffre de sauvegarde MSI.

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="Capture d’écran montrant le processus d’octroi à l’identité MSI du coffre de sauvegarde l’autorisation d’accéder aux conteneurs du compte de stockage en utilisant le portail Azure." lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

Vous pouvez aussi accorder des autorisations précises sur le conteneur spécifique vers lequel vous effectuez la restauration en utilisant la commande Azure CLI [az role assignment create](/cli/azure/role/assignment).

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
Remplacez le paramètre du destinataire de l’affectation par l’_ID d’application_ de l’identité MSI du coffre et le paramètre d’étendue de façon à référencer votre conteneur spécifique. Pour obtenir l’**ID d’application** de l’identité MSI du coffre, sélectionnez **Toutes les applications** sous **Type d’application**. Recherchez le nom du coffre et copiez l’ID d’application.

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="Capture d’écran montrant le processus d’obtention de l’ID d’application de l’identité MSI du coffre." lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="Capture d’écran montrant le processus de copie de l’ID d’application du coffre." lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>Étapes suivantes

[Résoudre les problèmes de sauvegarde de base de données PostgreSQL avec Sauvegarde Azure](backup-azure-database-postgresql-troubleshoot.md)