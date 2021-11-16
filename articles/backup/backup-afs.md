---
title: Sauvegarder des partages de fichiers Azure dans le portail Azure
description: Découvrir comment utiliser le portail Azure pour sauvegarder des partages de fichiers Azure sauvegardés dans le coffre Recovery Services
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 46068722385e9cf89c5c85d37a72a0528479ab8a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849727"
---
# <a name="back-up-azure-file-shares"></a>Sauvegarder des partages de fichiers Azure

Cet article explique comment sauvegarder les [partages de fichiers Azure](../storage/files/storage-files-introduction.md) à partir du portail Azure.

Dans cet article, vous allez apprendre à :

* Créez un coffre Recovery Services.
* Configurer la sauvegarde à partir du coffre Recovery Services
* Configurer la sauvegarde à partir du volet de partage de fichiers
* Exécuter un travail de sauvegarde à la demande pour créer un point de restauration

## <a name="prerequisites"></a>Prérequis

* [Découvrez](azure-file-share-backup-overview.md) la solution de sauvegarde de partage de fichiers Azure basée sur une capture instantanée de partage.
* Assurez-vous que le partage de fichiers est présent dans l’un des [types de comptes de stockage pris en charge](azure-file-share-support-matrix.md).
* Identifiez ou créez un [coffre Recovery Services](#create-a-recovery-services-vault) dans la même région et le même abonnement que le compte de stockage hébergeant le partage de fichiers.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Configurer la sauvegarde à partir du coffre Recovery Services

Pour configurer la sauvegarde de plusieurs partages de fichiers à partir du volet du coffre Recovery Services, procédez comme suit :

1. Sur le [portail Azure](https://portal.azure.com/), accédez à **Centre de sauvegarde**, puis cliquez sur **+Sauvegarde**.

   :::image type="content" source="./media/backup-afs/backup-center-configure-inline.png" alt-text="Capture d’écran montrant la configuration de la Sauvegarde pour Azure Files." lightbox="./media/backup-afs/backup-center-configure-expanded.png":::

1. Sélectionnez **Azure Files (Stockage Azure)** comme type de source de données, choisissez le coffre à l’aide duquel vous souhaitez protéger le partage de fichiers, puis cliquez sur **Continuer**.

   :::image type="content" source="./media/backup-afs/azure-file-share-select-vault.png" alt-text="Capture d’écran montrant la sélection d’Azure Files.":::

1. Cliquez sur **Sélectionner** pour sélectionner le compte de stockage contenant le partage de fichiers à sauvegarder.

   Le volet **Sélectionner un compte de stockage** qui s’ouvre à droite répertorie l’ensemble de comptes de stockage pris en charge qui ont été détectés. Ceux-ci sont associés à ce coffre ou présents dans la même région que le coffre, mais pas encore associés à un coffre Recovery Services.

   :::image type="content" source="./media/backup-afs/azure-file-share-select-storage-account-inline.png" alt-text="Capture d’écran montrant la sélection d’un compte de stockage." lightbox="./media/backup-afs/azure-file-share-select-storage-account-expanded.png":::

1. Dans la liste des comptes de stockage détectés, sélectionnez un compte, puis **OK**.

   :::image type="content" source="./media/backup-afs/azure-file-share-confirm-storage-account-inline.png" alt-text="Capture d’écran montrant la sélection de l’un des comptes de stockage découverts." lightbox="./media/backup-afs/azure-file-share-confirm-storage-account-expanded.png":::
   
   >[!NOTE]
   >Si un compte de stockage est présent dans une région différente de celle du coffre, il ne figurera pas dans la liste des comptes de stockage détectés.

1. L’étape suivante consiste à sélectionner les partages de fichiers que vous souhaitez sauvegarder. Sélectionnez le bouton **Ajouter** dans la section **Partages de fichiers à sauvegarder**.

   :::image type="content" source="./media/backup-afs/azure-select-file-share-inline.png" alt-text="Capture d’écran montrant la sélection des partages de fichiers à sauvegarder." lightbox="./media/backup-afs/azure-select-file-share-expanded.png":::

1. Le volet contextuel **Sélectionner les partages de fichiers** s’ouvre à droite. Azure recherche dans le compte de stockage les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et ne les voyez pas dans la liste, patientez un peu jusqu’à ce qu’ils s’affichent.

1. Dans la liste **Sélectionner les partages de fichiers**, sélectionnez un ou plusieurs partages de fichiers à sauvegarder. Sélectionnez **OK**.

1. Pour choisir une stratégie de sauvegarde pour votre partage de fichiers, vous avez trois options :

   * Choisissez la stratégie par défaut.<br>
   Cette option vous permet d’activer des sauvegardes quotidiennes qui seront conservées pendant 30 jours. Si vous n’avez pas de stratégie de sauvegarde existante dans le coffre, le volet de sauvegarde s’ouvre avec les paramètres de stratégie par défaut. Si vous voulez choisir les paramètres par défaut, vous pouvez sélectionner directement **Activer la sauvegarde**.

   * Créer une nouvelle stratégie <br>

      1. Pour créer une stratégie de sauvegarde pour votre partage de fichiers, sélectionnez le texte du lien en dessous de la liste déroulante dans la section **Stratégie de sauvegarde**.<br>

         :::image type="content" source="./media/backup-afs/azure-file-share-edit-policy-inline.png" alt-text="Capture d’écran montrant la création d’une stratégie." lightbox="./media/backup-afs/azure-file-share-edit-policy-expanded.png":::

      1. Effectuez les étapes 3-7 de la section [Créer une stratégie](manage-afs-backup.md#create-a-new-policy).

      1. Après avoir défini tous les attributs de la stratégie, cliquez sur **OK**.

         :::image type="content" source="./media/backup-afs/azure-file-share-policy-parameters-inline.png" alt-text="Capture d’écran montrant la fourniture d’un nom de stratégie et de valeurs de rétention." lightbox="./media/backup-afs/azure-file-share-policy-parameters-expanded.png":::

   * Choisir l’une des stratégies de sauvegarde existantes <br>

      Pour choisir l’une des stratégies de sauvegarde existantes pour la configuration de la protection, sélectionnez la stratégie de votre choix dans la liste déroulante **Stratégie de sauvegarde**.<br>

      ![Choisir une stratégie existante](./media/backup-afs/choose-existing-policy.png)

1. Sélectionnez **Activer la sauvegarde** pour commencer à protéger le partage de fichiers.

   ![Choisir Activer la sauvegarde](./media/backup-afs/enable-backup.png)

Une fois que vous avez défini une stratégie de sauvegarde, un instantané des partages de fichiers est pris à l’heure planifiée. Le point de récupération est également conservé pendant la période choisie.


## <a name="configure-backup-from-the-file-share-pane"></a>Configurer la sauvegarde à partir du volet de partage de fichiers

Les étapes suivantes expliquent comment configurer la sauvegarde de partages de fichiers individuels à partir de leur volet de partage de fichiers respectif :

1. Sur le [portail Azure](https://portal.azure.com/), ouvrez le compte de stockage hébergeant le partage de fichiers à sauvegarder.

1. Une fois dans le compte de stockage, sélectionnez la vignette intitulée **Partages de fichiers**. Vous pouvez aussi accéder à **Partages de fichiers** via la table des matières du compte de stockage.

   ![Compte de stockage](./media/backup-afs/storage-account.png)

1. Dans la liste des partages de fichiers doivent figurer tous les partages de fichiers présents dans le compte de stockage. Sélectionnez le partage de fichiers que vous voulez sauvegarder.

   ![Liste des partages de fichiers](./media/backup-afs/file-shares-list.png)

1. Sélectionnez **Sauvegarde** sous la section **Opérations** du volet de partage de fichiers. Le volet **Configurer la sauvegarde** se charge à droite.

   ![Volet Configurer la sauvegarde](./media/backup-afs/configure-backup.png)

1. Pour la sélection du coffre Recovery Services, procédez de l’une des façons suivantes :

    * Si vous disposez déjà d’un coffre, sélectionnez la case d’option **Sélectionner** pour les coffres Recovery Services, puis choisissez l’un des coffres existants dans le menu déroulant **Nom du coffre**.

       ![Sélectionner un coffre existant](./media/backup-afs/select-existing-vault.png)

    * Si vous n’avez pas de coffre, sélectionnez la case d’option **Créer nouveau** pour les coffres Recovery Services. Nommez le coffre. Il est créé dans la même région que le partage de fichiers. Par défaut, le coffre est créé dans le même groupe de ressources que le partage de fichiers. Si vous voulez choisir un autre groupe de ressources, sélectionnez le lien **Créer nouveau** sous la liste déroulante **Type de ressource**, puis attribuez un nom au groupe de ressources. Sélectionnez **OK** pour continuer.

       ![Créer un coffre](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Si le compte de stockage est inscrit auprès d’un coffre ou qu’il y a peu de partages protégés dans le compte de stockage hébergeant le partage de fichiers que vous tentez de protéger, le nom du coffre Recovery Services est prérempli et vous n’êtes pas autorisé à le modifier. [Apprenez-en davantage ici](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. Pour sélectionner une **Stratégie de sauvegarde**, procédez de l’une des façons suivantes :

    * Conservez la stratégie par défaut. Elle planifiera les sauvegardes quotidiennes avec une période de conservation de 30 jours.

    * Sélectionnez une stratégie de sauvegarde existante, si vous en avez une, dans le menu déroulant **Stratégie de sauvegarde**.

       ![Choisir une stratégie de sauvegarde](./media/backup-afs/choose-backup-policy.png)

    * Créez une stratégie avec une conservation quotidienne/hebdomadaire/mensuelle/annuelle, selon vos besoins.  

         1. Sélectionnez le texte du lien **Créer une stratégie**.

         2. Effectuez les étapes 3-7 de la section [Créer une stratégie](manage-afs-backup.md#create-a-new-policy).

         3. Après avoir défini tous les attributs de la stratégie, cliquez sur **OK**.

            ![Créer une stratégie de sauvegarde](./media/backup-afs/create-new-backup-policy.png)

1. Sélectionnez **Activer la sauvegarde** pour commencer à protéger le partage de fichiers.

   ![Sélectionner Activer la sauvegarde](./media/backup-afs/select-enable-backup.png)

1. Vous pouvez suivre la progression de la configuration dans les notifications du portail ou en supervisant les travaux de sauvegarde relevant du coffre dont vous vous servez pour protéger le partage de fichiers.

   ![Notifications du portail](./media/backup-afs/portal-notifications.png)

1. Une fois l’opération de configuration de la sauvegarde terminée, sélectionnez **Sauvegarde** sous la section **Opérations** du volet de partage de fichiers. Le volet contextuel listant les **éléments essentiels du coffre** se charge à droite. De là, vous pouvez déclencher des opérations de sauvegarde et de restauration à la demande.

   ![Éléments essentiels du coffre](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Exécuter un travail de sauvegarde à la demande

Vous pouvez occasionnellement générer un instantané de sauvegarde ou un point de récupération en dehors des heures planifiées dans la stratégie de sauvegarde. Souvent que la génération d'une sauvegarde à la demande intervient immédiatement après que vous avez configuré la stratégie de sauvegarde. Selon la planification définie dans la stratégie de sauvegarde, des heures ou des jours peuvent s’écouler avant la prise d’un instantané. Pour protéger vos données en attendant que la stratégie de sauvegarde génère un instantané, effectuez une sauvegarde à la demande. La création d’une sauvegarde à la demande est souvent nécessaire avant d’apporter des modifications prévues à vos partages de fichiers.

### <a name="from-backup-center"></a>À partir du Centre de sauvegarde

1. Accédez au **Centre de sauvegarde**, puis, dans le menu, cliquez sur **Instances de sauvegarde**.

   Filtrez sur **Azure Files (Stockage Azure)** en tant que type de source de données.

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-instances-inline.png" alt-text="Capture d’écran montrant la sélection d’Instances de sauvegarde." lightbox="./media/backup-afs/azure-file-share-backup-instances-expanded.png":::

1. Sélectionnez l’élément pour lequel vous souhaitez exécuter un travail de sauvegarde à la demande.

1. Dans le menu **Élément de sauvegarde**, sélectionnez **Sauvegarder maintenant**. Comme il s’agit d’un travail de sauvegarde à la demande, aucune stratégie de rétention n’est associée au point de récupération.

   :::image type="content" source="./media/backup-afs/azure-file-share-backup-now-inline.png" alt-text="Capture d’écran montrant la sélection de l’option Sauvegarder maintenant." lightbox="./media/backup-afs/azure-file-share-backup-now-expanded.png":::

1. Le volet **Sauvegarder maintenant** s’ouvre. Spécifiez le dernier jour que vous souhaitez conserver dans le point de récupération. La conservation maximale d'une sauvegarde à la demande est de 10 ans.

   :::image type="content" source="./media/backup-afs/azure-file-share-on-demand-backup-retention.png" alt-text="Capture d’écran montrant le choix de la date de rétention.":::

1. Cliquez sur **OK** pour confirmer l’exécution du travail de sauvegarde à la demande.

1. Surveillez les notifications du portail pour conserver une trace de la bonne exécution des tâches de sauvegarde.

   Pour surveiller la progression du travail dans le tableau de bord du **Centre de sauvegarde**, sélectionnez **Centre de sauvegarde** -> **Travaux de sauvegarde** -> **En cours**.

### <a name="from-the-file-share-pane"></a>À partir du volet de partage de fichiers

1. Ouvrez le volet **Vue d’ensemble**  du partage de fichiers pour lequel vous voulez effectuer une sauvegarde à la demande.

1. Sélectionnez **Sauvegarde** sous la section **Opération**. Le volet contextuel listant les **éléments essentiels du coffre** se charge à droite. Sélectionnez **Sauvegarder maintenant** pour effectuer une sauvegarde à la demande.

   ![Sélectionner Sauvegarder maintenant](./media/backup-afs/select-backup-now.png)

1. Le volet **Sauvegarder maintenant** s’ouvre. Spécifiez la période de conservation du point de récupération. La conservation maximale d'une sauvegarde à la demande est de 10 ans.

   ![Conserver la date de sauvegarde](./media/backup-afs/retain-backup-date.png)

1. Sélectionnez **OK** pour confirmer.

>[!NOTE]
>Sauvegarde Azure verrouille le compte de stockage lorsque vous configurez la protection pour tout partage de fichiers dans le compte correspondant. Cette méthode offre une protection contre la suppression accidentelle d'un compte de stockage avec des partages de fichiers sauvegardés.

## <a name="best-practices"></a>Meilleures pratiques

* Ne supprimez pas d’instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration.

* Ne supprimez pas le verrou effectué par Sauvegarde Azure au niveau du compte de stockage. Si vous supprimez le verrou, votre compte de stockage risque d’être supprimé accidentellement et, dans ce cas, vous perdrez vos instantanés ou vos sauvegardes.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

* [Restaurer des partages de fichiers Azure](restore-afs.md)
* [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)
