---
title: Gérer les sauvegardes de partage de fichiers Azure
description: Cet article décrit les tâches courantes de gestion et de supervision des partages de fichiers Azure sauvegardés par le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 021a24cbe5c1e2ba9f504ca99c149b135bc40fc4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844721"
---
# <a name="manage-azure-file-share-backups"></a>Gérer les sauvegardes de partage de fichiers Azure

Cet article décrit les tâches courantes de gestion et de supervision des partages de fichiers Azure sauvegardés par le service [Sauvegarde Azure](./backup-overview.md). Vous allez apprendre à effectuer des tâches de gestion dans le **Centre de sauvegarde**.

## <a name="monitor-jobs"></a>Surveiller des travaux

Lorsque vous déclenchez une opération de sauvegarde ou de restauration, le service de sauvegarde crée une tâche de suivi. Vous pouvez surveiller la progression de tous les travaux sur la page **Travaux de sauvegarde**.

Pour ouvrir la page **Travaux de sauvegarde** :

1. Accédez au **Centre de sauvegarde**, puis, dans la section **Surveillance**, sélectionnez **Travaux de sauvegarde**.

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-list-inline.png" alt-text="Capture d’écran montrant les travaux de sauvegarde dans la section Surveillance." lightbox="./media/manage-afs-backup/backup-center-jobs-list-expanded.png":::

   Le volet **Travaux de sauvegarde** indique l’état de tous les travaux.

1. Sélectionnez **Azure Files (Stockage Azure)** comme type de source de données et sélectionnez une ligne pour afficher les détails du travail en question.

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-inline.png" alt-text="Capture d’écran montrant la liste des travaux." lightbox="./media/manage-afs-backup/backup-center-jobs-expanded.png":::

## <a name="monitor-using-azure-backup-reports"></a>Surveiller à l’aide des rapports de Sauvegarde Azure

La Sauvegarde Azure offre une solution de création de rapports qui utilise les [journaux Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md) et les [classeurs Azure](../azure-monitor/visualize/workbooks-overview.md). Ces ressources vous aident à obtenir des insights enrichis dans vos sauvegardes. Vous pouvez tirer parti de ces rapports pour obtenir une visibilité sur les éléments de sauvegarde Azure Files, les travaux au niveau de l’élément et les détails des stratégies actives. La fonctionnalité Rapport de messagerie disponible dans Rapports de sauvegarde vous permet de créer des tâches automatisées pour recevoir des rapports périodiques par e-mail. [Découvrez](./configure-reports.md#get-started) comment configurer et afficher des rapports de Sauvegarde Azure.

## <a name="create-a-new-policy"></a>Créer une nouvelle stratégie

Vous pouvez créer une nouvelle stratégie pour sauvegarde des partages de fichiers Azure à partir de la section **Stratégies de sauvegarde** du **Centre de sauvegarde**. Toutes les stratégies créées lors de la configuration de la sauvegarde des partages de fichiers sont définies sur le **Type de stratégie** **Partage de fichiers Azure**.

Pour créer une stratégie de sauvegarde, procédez comme suit :

1. Dans le volet **Stratégies de sauvegarde** du **Centre de sauvegarde**, sélectionnez **+Ajouter**.

   :::image type="content" source="./media/manage-afs-backup/backup-center-add-policy-inline.png" alt-text="Capture d’écran montrant l’option permettant de démarrer la création d’une stratégie de sauvegarde." lightbox="./media/manage-afs-backup/backup-center-add-policy-expanded.png":::

1. Choisissez **Azure Files (Stockage Azure)** comme type de source de source, sélectionnez le coffre dans lequel la stratégie doit être créée, puis cliquez sur **Continuer**.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-select-vault-for-policy.png" alt-text="Capture d’écran montrant la sélection d’un partage de fichiers Azure comme type de stratégie.":::

1. Lorsque le volet **Stratégie de sauvegarde** du **Partage de fichiers Azure** s’ouvre, spécifiez le nom de stratégie.

1. Dans **Planification de sauvegarde**, sélectionnez une fréquence appropriée pour les sauvegardes : **Quotidienne** ou **Horaire**.

   :::image type="content" source="./media/manage-afs-backup/backup-frequency-types.png" alt-text="Capture d’écran montrant les types de fréquence pour les sauvegardes.":::

   - **Quotidienne** : déclenche une sauvegarde par jour. Pour la fréquence quotidienne, sélectionnez les valeurs appropriées pour :

     - **Heure** : timestamp indiquant à quel moment le travail de sauvegarde doit être déclenché.
     - **Fuseau horaire** : fuseau horaire correspondant au travail de sauvegarde.

   - **Toutes les heures** : déclenche plusieurs sauvegardes par jour. Pour la fréquence horaire, sélectionnez les valeurs appropriées pour :
   
     - **Planification** : intervalle de temps (en heures) entre les sauvegardes consécutives.
     - **Heure de début** : heure à laquelle le premier travail de sauvegarde du jour doit être déclenché.
     - **Durée** : représente la fenêtre de sauvegarde (en heures), c’est-à-dire l’intervalle de temps pendant lequel les travaux de sauvegarde doivent être déclenchés conformément à la planification sélectionnée.
     - **Fuseau horaire** : fuseau horaire correspondant au travail de sauvegarde.
     
     Par exemple, vous avez besoin d’un RPO (objectif de point de récupération) de 4 heures et vos heures de travail sont de 9 h 00 à 21 h 00. Pour répondre à ces exigences, la configuration de la planification de sauvegarde est la suivante :
    
     - Planification : Toutes les 4 heures
     - Heure de début : 9 h 
     - Durée : 12 heures 
     
     :::image type="content" source="./media/manage-afs-backup/hourly-backup-frequency-values-scenario.png" alt-text="Capture d’écran montrant un exemple de valeurs de fréquence de sauvegarde toutes les heures.":::

     En fonction de votre sélection, les détails du travail de sauvegarde (horodatage de déclenchement du travail de sauvegarde) s’affichent dans le panneau Stratégie de sauvegarde.

1. Dans **Durée de rétention**, spécifiez les valeurs de rétention appropriées pour les sauvegardes (quotidienne, hebdomadaire, mensuelle ou annuelle).

1. Après avoir défini tous les attributs de la stratégie, cliquez sur **Créer**.
  
### <a name="view-policy"></a>Afficher la stratégie

Pour afficher les stratégies de sauvegarde existantes :

1. Accédez au **Centre de sauvegarde**, puis, dans la section **Gérer**, sélectionnez **Stratégies de sauvegarde**.

   Toutes les stratégies de sauvegarde configurées dans votre coffre s’affichent.

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="Capture d’écran montrant toutes les stratégies de sauvegarde." lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. Pour afficher des stratégies spécifiques d’**Azure Files (Stockage Azure)** , sélectionnez **Partage de fichiers Azure** comme type de source de données.

## <a name="modify-policy"></a>Modifier la stratégie

Vous pouvez modifier une stratégie de sauvegarde pour modifier la fréquence de sauvegarde ou la durée de rétention.

Pour modifier une stratégie :

1. Accédez au **Centre de sauvegarde**, puis, dans la section **Gérer**, sélectionnez **Stratégies de sauvegarde**.

   Toutes les stratégies de sauvegarde configurées dans vos coffres s’affichent.

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="Capture d’écran montrant toutes les stratégies de sauvegarde dans le coffre." lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. Pour afficher des stratégies spécifiques d’un partage de fichiers Azure, sélectionnez **Azure Files (Stockage Azure)** comme type de source de données.

   Cliquez sur la stratégie que vous souhaitez mettre à jour.

1. Le volet **Planification** s'ouvre. Modifiez la **planification de la sauvegarde** et la **durée de conservation** en fonction de vos besoins, puis sélectionnez **Enregistrer**.

   Le message _Mise à jour en cours_ s’affiche dans le volet. Une fois la mise à jour de la stratégie effectuée, le message _Stratégie de sauvegarde mise à jour avec succès_ s’affiche.

   ![Enregistrer la stratégie modifiée](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Arrêter la protection sur un partage de fichiers

Il existe deux façons de suspendre la protection des partages de fichiers Azure :

* Arrêter tous les travaux de sauvegarde à venir et *supprimer tous les points de récupération*
* Arrêter tous les travaux de sauvegarde à venir mais *conserver les points de récupération*

Le fait de laisser des points de récupération dans l'espace de stockage peut avoir un coût car les instantanés sous-jacents créés par Sauvegarde Azure sont conservés. La conservation des points de récupération présente l'avantage de vous permettre de restaurer le partage de fichiers par la suite. Pour plus d’informations sur les coûts de conservation des points de récupération, voir les [détails de la tarification](https://azure.microsoft.com/pricing/details/backup/). Si vous décidez de supprimer tous les points de récupération, vous ne pouvez plus restaurer le partage de fichiers.

Pour arrêter la protection d’un partage de fichiers Azure :

1. Accédez au **Centre de sauvegarde**, sélectionnez **Instances de sauvegarde** dans le menu, puis sélectionnez **Azure Files (Stockage Azure)** comme type de source de données.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="Capture d’écran montrant la sélection d’Azure Files comme type de données." lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. Sélectionnez l’élément de sauvegarde dont vous souhaitez arrêter la protection.

1. Sélectionnez l'option **Arrêter la sauvegarde**.

   ![Sélectionner Arrêter la sauvegarde](./media/manage-afs-backup/stop-backup.png)

1. Dans le volet **Arrêter la sauvegarde**, sélectionnez **Conserver les données de sauvegarde** ou **Supprimer les données de sauvegarde**. Puis sélectionnez **Arrêter la sauvegarde**.

    ![Sélectionner Conserver les données de sauvegarde ou Supprimer les données de sauvegarde](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Reprendre la protection sur un partage de fichiers

Si vous avez sélectionné l'option **Conserver les données de sauvegarde** au moment de l'arrêt de la protection du partage de fichiers, vous avez la possibilité de restaurer la protection. Si vous avez sélectionné l'option **Supprimer les données de sauvegarde**, vous ne pourrez pas restaurer la protection du partage de fichiers.

Pour reprendre la protection d’un partage de fichiers Azure :

1. Accédez au **Centre de sauvegarde**, sélectionnez **Instances de sauvegarde** dans le menu, puis sélectionnez **Azure Files (Stockage Azure)** comme type de source de données.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="Capture d’écran montrant la sélection d’Azure Files comme type de source de données." lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. Sélectionnez l’élément de sauvegarde dont vous souhaitez reprendre la protection.

1. Sélectionnez l'option **Reprendre la sauvegarde**.

   ![Sélectionner Reprendre la sauvegarde](./media/manage-afs-backup/resume-backup.png)

1. Le volet **Stratégie de sauvegarde** s'ouvre. Sélectionnez la stratégie pour laquelle vous souhaitez reprendre la sauvegarde.

1. Après avoir sélectionné une stratégie de sauvegarde, sélectionnez **Enregistrer**.

   Le message _Mise à jour en cours_ s’affiche sur le portail. Une fois que la sauvegarde a repris, le message _Stratégie de sauvegarde mise à jour pour le partage de fichiers Azure protégé_ s’affiche.

   ![Stratégie de sauvegarde mise à jour avec succès](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Vous pouvez supprimer la sauvegarde d'un partage de fichiers pendant la tâche **Arrêter la sauvegarde** ou à tout moment après avoir mis fin à la protection. Il est préférable d'attendre quelques jours, voire quelques semaines, avant de supprimer les points de récupération. Lorsque vous supprimez des données de sauvegarde, vous ne pouvez pas choisir de points de récupération spécifiques à supprimer. Si vous décidez de supprimer vos données de sauvegarde, vous supprimerez tous les points de récupération associés au partage de fichiers.

La procédure suivante suppose que le partage de fichiers n'est plus protégé.

Pour supprimer les données de sauvegarde du partage de fichiers Azure :

1. Une fois la tâche de sauvegarde arrêtée, les options **Reprendre la sauvegarde** et **Supprimer les données de sauvegarde** sont accessibles dans le tableau de bord **Élément de sauvegarde**. Sélectionnez l'option **Supprimer les données de sauvegarde**.

   ![Supprimer les données de sauvegarde](./media/manage-afs-backup/delete-backup-data.png)

1. Le volet **Supprimer les données de sauvegarde** s'ouvre. Entrez le nom du partage de fichiers pour en confirmer la suppression. Vous pouvez également entrer des informations supplémentaires dans les zones **Motif** ou **Commentaires**. Si vous êtes bien sûr de vouloir supprimer les données de sauvegarde, sélectionnez **Supprimer**.

   ![Confirmer la suppression des données](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Annuler l’inscription d’un compte de stockage

Pour protéger vos partages de fichiers sur un compte de stockage particulier à l’aide d’un coffre Recovery Services distinct, commencez par [mettre fin à la protection de tous les partages de fichiers](#stop-protection-on-a-file-share) de ce compte de stockage. Désinscrivez ensuite le compte du coffre Recovery Services utilisé pour la protection.

La procédure suivante part du principe que la protection a été interrompue pour tous les partages de fichiers du compte de stockage dont vous souhaitez annuler l'inscription.

Pour annuler l’inscription du compte de stockage :

1. Ouvrez le coffre Recovery Services dans lequel votre compte de stockage est inscrit.
1. Dans le volet **Vue d'ensemble**, accédez à la section **Gestion** et sélectionnez l'option **Infrastructure de sauvegarde**.

   ![Choisir Infrastructure de sauvegarde](./media/manage-afs-backup/backup-infrastructure.png)

1. Le volet **Infrastructure de sauvegarde** s'ouvre. Sélectionnez **Comptes de stockage** dans la section **Comptes de stockage Azure**.

   ![Sélectionner Comptes de stockage](./media/manage-afs-backup/storage-accounts.png)

1. Une fois que vous avez sélectionné **Comptes de stockage**, la liste des comptes de stockage inscrits auprès du coffre apparaît.
1. Cliquez avec le bouton droit sur le compte de stockage dont vous souhaitez annuler l'inscription, puis sélectionnez **Annuler l'inscription**.

   ![Sélectionner Annuler l'inscription](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez [Résoudre les problèmes liés à la sauvegarde des partages de fichiers Azure](./troubleshoot-azure-files.md).