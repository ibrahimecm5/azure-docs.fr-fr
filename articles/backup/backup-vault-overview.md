---
title: Vue d’ensemble des coffres de sauvegarde
description: Une vue d’ensemble des coffres de sauvegarde.
ms.topic: conceptual
ms.date: 10/31/2021
ms.custom: references_regions
ms.openlocfilehash: 1a3909e525adadc2e5983e2792e407301a5b321d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473639"
---
# <a name="backup-vaults-overview"></a>Vue d’ensemble des coffres de sauvegarde

Cet article décrit les fonctionnalités d’un coffre de sauvegarde. Un coffre de sauvegarde est une entité de stockage dans Azure qui héberge des données de sauvegarde pour certaines charges de travail plus récentes prises en charge par Sauvegarde Azure. Vous pouvez utiliser des coffres de sauvegarde afin de stocker des données de sauvegarde pour différents services Azure, comme des serveurs Azure Database pour PostgreSQL et des charges de travail plus récentes qui seront prises en charge par Sauvegarde Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres de sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités comme :

- **Fonctionnalités enrichies pour sécuriser les données de sauvegarde** : Avec les coffres de sauvegarde, Sauvegarde Azure fournit des fonctionnalités de sécurité pour protéger les sauvegardes cloud. Ces fonctionnalités de sécurité vous garantissent de pouvoir sécuriser vos sauvegardes et récupérer en toute sécurité des données même si des serveurs de production et de sauvegarde sont compromis. [En savoir plus](backup-azure-security-feature.md)

- **Contrôle d’accès en fonction du rôle Azure (Azure RBAC)**  : Azure RBAC offre un contrôle très précis de la gestion des accès dans Azure. [Azure offre différents rôles intégrés](../role-based-access-control/built-in-roles.md), et Sauvegarde Microsoft Azure comprend trois [rôles intégrés pour gérer les points de récupération](backup-rbac-rs-vault.md). Les coffres de sauvegarde sont compatibles avec Azure RBAC, qui restreint l’accès aux sauvegardes et restaurations à l’ensemble défini de rôles d’utilisateur. [En savoir plus](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Paramètres de stockage dans le coffre de sauvegarde

Un coffre de sauvegarde est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre de sauvegarde contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

- La Sauvegarde Azure gère automatiquement le stockage du coffre. Choisissez la redondance de stockage qui correspond aux besoins de votre entreprise lors de la création du coffre de sauvegarde.

- Pour en savoir plus sur la redondance du stockage, consultez les articles suivants sur la redondance [géographique](../storage/common/storage-redundancy.md#geo-redundant-storage) et la redondance [locale](../storage/common/storage-redundancy.md#locally-redundant-storage).

## <a name="encryption-settings-in-the-backup-vault"></a>Paramètres de chiffrement dans le coffre de sauvegarde

Cette section décrit les options disponibles pour le chiffrement de vos données de sauvegarde stockées dans le coffre de sauvegarde. Le service Sauvegarde Azure utilise l’application **Service de gestion des sauvegardes** pour accéder à Azure Key Vault, mais pas à l’identité managée du coffre de sauvegarde.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Chiffrement des données de sauvegarde à l’aide de clés gérées par la plateforme

Par défaut, toutes vos données sont chiffrées à l’aide de clés gérées par la plateforme. Vous n’avez aucune action explicite à effectuer pour activer ce chiffrement. Il s’applique à toutes les charges de travail sauvegardées dans votre coffre de sauvegarde.

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre de sauvegarde est une entité de gestion qui stocke les points de récupération créés au fil du temps et il fournit une interface permettant d’effectuer des opérations liées à la sauvegarde. Cela inclut l’exécution de sauvegardes à la demande, l’exécution de restaurations et la création de stratégies de sauvegarde.

Pour créer un coffre de sauvegarde, effectuez ces étapes.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Créer un coffre de sauvegarde

1. Tapez **Coffres de sauvegarde** dans la zone de recherche.
2. Sous **Services**, sélectionnez **Coffres de sauvegarde**.
3. Sur la page **Coffres de sauvegarde**, sélectionnez **Ajouter**.
4. Sur l’onglet **Général**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis choisissez **Créer un groupe de ressources**. Tapez *myResourceGroup* comme nom.

    ![Créer un groupe de ressources](./media/backup-vault-overview/new-resource-group.png)

5. Sous **Détails de l’instance**, tapez *myVault* pour **Nom du coffre de sauvegarde**, puis choisissez votre région, dans le cas présent *USA Est* pour **Région**.
6. Choisissez maintenant votre **Redondance du stockage**. La redondance du stockage ne peut pas être changée une fois que des éléments sont protégés dans le coffre.
7. Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, nous vous recommandons de continuer à utiliser le paramètre **Géoredondant** par défaut.
8. Si vous n’utilisez pas Azure comme point de terminaison de stockage de sauvegarde, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure. Informez-vous sur la redondance [géo](../storage/common/storage-redundancy.md#geo-redundant-storage) et [locale](../storage/common/storage-redundancy.md#locally-redundant-storage).

    ![Choisir la redondance du stockage](./media/backup-vault-overview/storage-redundancy.png)

9. Sélectionnez le bouton Vérifier + créer dans le bas de la page.

    ![Sélectionner Vérifier + créer](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Supprimer un coffre de sauvegarde

Cette section décrit comment supprimer un coffre de sauvegarde. Il indique comment supprimer des dépendances, puis un coffre.

### <a name="before-you-start"></a>Avant de commencer

Vous ne pouvez pas supprimer un coffre de sauvegarde ayant une des dépendances suivantes :

- Vous ne pouvez pas supprimer un coffre qui contient des sources de données protégées (par exemple des serveurs Azure Database pour PostgreSQL).
- Vous ne pouvez pas supprimer un coffre qui contient des données de sauvegarde.

Si vous essayez de supprimer le coffre sans supprimer les dépendances, vous obtenez les messages d’erreur suivants :

>Impossible de supprimer le coffre de sauvegarde, car des instances de sauvegarde ou des stratégies de sauvegarde existent dans le coffre. Supprimez toutes les instances de sauvegarde et toutes les stratégies de sauvegarde présentes dans le coffre, puis essayez de supprimer le coffre.

Veillez à passer en revue les options de filtre de **Type de sources de données** dans le **Centre de sauvegarde** pour ne pas manquer les instances ou stratégies de sauvegarde existantes qui doivent être supprimées avant de pouvoir supprimer le coffre de sauvegarde.

![Types de source de données](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>Méthode appropriée pour supprimer un coffre

>[!WARNING]
L’opération suivante est destructrice et ne peut pas être annulée. Toutes les données de sauvegarde et tous les éléments de sauvegarde associés au serveur protégé seront définitivement supprimés. Procédez avec prudence.

Pour supprimer correctement un coffre, vous devez suivre les étapes ci-après dans l’ordre indiqué :

- Vérifiez s’il existe des éléments protégés :
  - Accédez à **Instances de sauvegarde** dans la barre de navigation de gauche. Tous les éléments listés ici doivent d’abord être supprimés.

Une fois ces étapes terminées, vous pouvez poursuivre la suppression du coffre.

### <a name="delete-the-backup-vault"></a>Supprimer le coffre de sauvegarde

Lorsqu’il n’y a plus d’éléments dans le coffre, sélectionnez **Supprimer** dans le tableau de bord du coffre. Un texte de confirmation s’affiche, vous demandant si vous voulez supprimer le coffre.

![Supprimer un coffre](./media/backup-vault-overview/delete-vault.png)

1. Sélectionnez **Oui** pour confirmer la suppression du coffre. Le coffre est supprimé. Le portail revient au menu de service **Nouveau**.

## <a name="monitor-and-manage-the-backup-vault"></a>Superviser et gérer le coffre de sauvegarde

Cette section explique comment utiliser le tableau de bord **Vue d’ensemble** des coffres de sauvegarde pour superviser et gérer vos coffres de sauvegarde. Le volet Vue d’ensemble contient deux vignettes : **Travaux** et **Instances**.

![Vue d’ensemble du tableau de bord](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Gérer les instances de sauvegarde

Dans la vignette **Travaux**, vous voyez un récapitulatif de tous les travaux liés à la sauvegarde et à la restauration dans votre coffre de sauvegarde. La sélection de l’un des nombres dans cette vignette vous permet de voir des informations supplémentaires sur les travaux pour un type de source de données, un type d’opération et un état spécifiques.

![Instances de sauvegarde](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Gérer les travaux de sauvegarde

La vignette **Instances de sauvegarde** vous montre un récapitulatif de toutes les instances de sauvegarde dans votre coffre de sauvegarde. La sélection de l’un des nombres figurant dans cette vignette vous permet d’afficher des informations supplémentaires sur les instances de sauvegarde pour un type de source de données et un état de protection spécifiques.

![Travaux de sauvegarde](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>Déplacer un coffre de sauvegarde dans des abonnements/groupes de ressources Azure (Préversion publique)

Cette section explique comment utiliser le portail Azure pour déplacer un coffre de sauvegarde (configuré pour le service Sauvegarde Azure) entre des abonnements et des groupes de ressources Azure.

>[!Note]
>Vous pouvez également déplacer des coffres de sauvegarde vers un autre groupe de ressources ou un autre abonnement à l’aide de [PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) et de l’[interface de ligne de commande (CLI)](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move).

### <a name="supported-regions"></a>Régions prises en charge

Le déplacement du coffre entre les abonnements et les groupes de ressources est pris en charge dans toutes les régions publiques.

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>Utiliser le portail Azure pour déplacer un coffre de sauvegarde vers un autre groupe de ressources

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez la liste des coffres de sauvegarde, puis sélectionnez le coffre à déplacer.

   Le tableau de bord du coffre affiche les détails du coffre.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="Capture d’écran montrant le tableau de bord du coffre à déplacer vers un autre groupe de ressources." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. Dans le menu de **Vue d’ensemble** du coffre, cliquez sur **Déplacer**, puis sélectionnez **Déplacer vers un autre groupe de ressources**.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="Capture d’écran montrant l’option permettant de déplacer un coffre de sauvegarde vers un autre groupe de ressources." lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >Seul la personne disposant d’un abonnement Administrateur possède les autorisations requises pour déplacer un coffre.

1. Dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant, ou sélectionnez **Créer** pour créer un groupe de ressources.

   L’abonnement reste le même et est renseigné automatiquement.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="Capture d’écran de la page Déplacer des ressources où les options permettant de sélectionner un groupe de ressources existant ou de créer un groupe de ressources sont mises en évidence." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. Sous l’onglet **ressources à déplacer**, le coffre de sauvegarde qui doit être déplacé est soumis à une validation. Ce processus peut prendre quelques minutes. Veuillez attendre la fin de la validation.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="Capture d’écran montrant l’état de la validation du coffre de sauvegarde." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. Pour confirmer, cochez la case en regard de _Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource_, puis sélectionnez **Déplacer**.
 
   >[!Note]
   >Le déplacement du coffre entre des groupes de ressources ou des abonnements modifie le chemin d’accès de la ressource. Veillez à mettre à jour les outils et les scripts avec le nouveau chemin d’accès de la ressource une fois l’opération de déplacement terminée.

Attendez que l’opération de déplacement soit terminée pour effectuer d’autres opérations sur le coffre. Toutes les opérations effectuées sur le coffre de sauvegarde échouent si elles sont effectuées pendant le déplacement. Une fois le processus terminé, le coffre de sauvegarde doit apparaître dans le groupe de ressources cible.

>[!Important]
>Si vous rencontrez une erreur lors du déplacement du coffre, reportez-vous à la section [Codes d’erreur et résolution des problèmes](#error-codes-and-troubleshooting).  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>Utiliser le portail Azure pour déplacer un coffre de sauvegarde vers un autre abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez la liste des coffres de sauvegarde, puis sélectionnez le coffre à déplacer.
   
   Le tableau de bord du coffre affiche les détails du coffre.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="Capture d’écran montrant le tableau de bord du coffre à déplacer vers un autre abonnement Azure." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. Dans le menu **Vue d’ensemble** du coffre, cliquez sur **Déplacer**, puis sélectionnez **Déplacer vers un autre abonnement**.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="Capture d’écran montrant l’option permettant de déplacer un coffre de sauvegarde vers un autre Abonnement Azure." lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >Seul la personne disposant d’un abonnement Administrateur possède les autorisations requises pour déplacer un coffre.

1. Dans la liste déroulante **Abonnement**, sélectionnez un abonnement existant.

   Pour déplacer des coffres entre des abonnements, il faut que l’abonnement cible se trouve dans le même tenant (locataire) que l’abonnement source. Pour déplacer un coffre vers un autre tenant (locataire), consultez l’article [Transférer un abonnement Azure vers une autre instance Azure AD Directory](../role-based-access-control/transfer-subscription.md).

1. Dans la liste déroulante **Groupe de ressources**, sélectionnez un groupe de ressources existant, ou sélectionnez **Créer** pour créer un groupe de ressources.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="Capture d’écran de la page Déplacer des ressources où les options permettant de sélectionner un groupe de ressources existant ou de créer un groupe de ressources dans un autre abonnement Azure sont mises en évidence." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. Sous l’onglet **ressources à déplacer**, le coffre de sauvegarde qui doit être déplacé est soumis à une validation. Ce processus peut prendre quelques minutes. Veuillez attendre la fin de la validation.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="Capture d’écran montrant l’état de la validation du coffre de sauvegarde à déplacer vers un autre abonnement Azure." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. Pour confirmer, cochez la case en regard de _Je comprends que les outils et les scripts associés aux ressources déplacées ne fonctionnent pas tant que je ne les mets pas à jour pour utiliser de nouveaux ID de ressource_, puis sélectionnez **Déplacer**.
 
   >[!Note]
   >Le déplacement du coffre entre des groupes de ressources ou des abonnements modifie le chemin d’accès de la ressource. Veillez à mettre à jour les outils et les scripts avec le nouveau chemin d’accès de la ressource une fois l’opération de déplacement terminée.

Attendez que l’opération de déplacement soit terminée pour effectuer d’autres opérations sur le coffre. Toutes les opérations effectuées sur le coffre de sauvegarde échouent si elles sont effectuées pendant le déplacement. Une fois le processus terminé, le coffre de sauvegarde doit apparaître dans l’abonnement cible et le groupe de ressources.

>[!Important]
>Si vous rencontrez une erreur lors du déplacement du coffre, reportez-vous à la section [Codes d’erreur et résolution des problèmes](#error-codes-and-troubleshooting).

### <a name="error-codes-and-troubleshooting"></a>Codes d’erreur et résolution des problèmes

Voici diverses solutions pour vous aider à résoudre les principaux problèmes pouvant survenir lors du déplacement d’un coffre de sauvegarde :

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

**Cause** : cette erreur risque de survenir lorsque le déplacement du coffre de sauvegarde n’a pas complètement réussi.

**Recommandation** : ce problème doit être résolu automatiquement dans un délai de 36 heures. S’il persiste, contactez le support Microsoft.

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

**Cause** : cette erreur risque de survenir lorsque le déplacement du coffre de sauvegarde a échoué de manière critique. 

**Recommandation** : ce problème doit être résolu automatiquement dans un délai de 36 heures. S’il persiste, contactez le support Microsoft. 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

**Cause** : cette erreur risque de survenir si vous tentez d’effectuer des opérations sur le coffre de sauvegarde pendant son déplacement. 

**Recommandation** : attendez que l’opération de déplacement soit terminée, puis réessayez. 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

**Cause** : cette erreur risque de survenir si vous tentez de déplacer plusieurs coffres de sauvegarde en même temps. 

**Recommandation** : veuillez sélectionner un seul coffre de sauvegarde pour chaque opération de déplacement. 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

**Cause** : cette erreur risque de survenir si le coffre n’est pas encore approvisionné. 

**Recommandation** : veuillez réessayer l’opération un peu plus tard.

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

**Cause** : le déplacement de ressources pour le coffre de sauvegarde n’est pas pris en charge dans la région Azure que vous avez sélectionnée.

**Recommandation** : assurez-vous que vous avez sélectionné l’une des régions prises en charge pour déplacer des coffres de sauvegarde. Consultez [Régions prises en charge](#supported-regions).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la sauvegarde sur des bases de données Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
