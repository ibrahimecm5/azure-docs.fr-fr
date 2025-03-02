---
title: Effectuer des actions à l’aide du Centre de sauvegarde
description: Cet article explique comment effectuer des actions à l’aide du Centre de sauvegarde.
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: e8b357c19caeb20c2a50d9a05f9d8d583bd8f640
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083617"
---
# <a name="perform-actions-using-backup-center"></a>Effectuer des actions à l’aide du Centre de sauvegarde

Le Centre de sauvegarde vous permet d’effectuer des actions de sauvegarde de clés à partir d’une interface centralisée, sans devoir accéder à un coffre en particulier. Voici quelques actions que vous pouvez effectuer à partir du Centre de sauvegarde :

* Configurer la sauvegarde pour vos sources de données
* Restaurer une instance de sauvegarde
* Créer un coffre
* Créer une nouvelle stratégie de sauvegarde
* Déclencher une sauvegarde à la demande pour une instance de sauvegarde
* Arrêter la sauvegarde d’une instance de sauvegarde

## <a name="supported-scenarios"></a>Scénarios pris en charge

* Le Centre de sauvegarde est actuellement pris en charge pour la sauvegarde des machines virtuelles Azure, la sauvegarde des machines virtuelles SQL dans Azure, la sauvegarde des machines virtuelles SAP HANA dans Azure, la sauvegarde Azure Files et la sauvegarde des serveurs Azure Database pour PostgreSQL.
* Reportez-vous à la [matrice de prise en charge ](backup-center-support-matrix.md) pour obtenir une liste détaillée des scénarios pris en charge et non pris en charge.

## <a name="configure-backup"></a>Configurer une sauvegarde

Si vous sauvegardez des machines virtuelles Azure, des machines virtuelles SQL dans Azure, des machines virtuelles SAP HANA dans Azure ou Azure Files, vous devez utiliser un coffre Recovery Services. Si vous sauvegardez des bases de données Azure pour serveur PostgreSQL, vous devez utiliser un coffre de sauvegarde. 

Selon le type de source de données que vous souhaitez sauvegarder, suivez les instructions appropriées comme indiqué ci-dessous.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Configurer la sauvegarde dans un coffre Recovery Services

1. Accédez au Centre de sauvegarde et sélectionnez **+ Sauvegarde** en haut de l’onglet **Vue d’ensemble**.

    ![Vue d’ensemble du Centre de sauvegarde](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Sélectionnez le type de source de source que vous souhaitez sauvegarder.

    ![Sélectionner une source de données pour configurer la sauvegarde de machine virtuelle](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Choisissez un coffre Recovery Services, puis sélectionnez **Continuer**. Vous accédez alors à l’expérience de configuration de la sauvegarde qui est identique à celle accessible à partir d’un coffre Recovery Services. [En savoir plus sur la configuration de la sauvegarde pour les machines virtuelles Azure avec un coffre Recovery Services](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Configurer la sauvegarde dans un coffre de sauvegarde

1. Accédez au Centre de sauvegarde et sélectionnez **+ Sauvegarde** en haut de l’onglet **Vue d’ensemble**.
2. Sélectionnez le type de source de données que vous souhaitez sauvegarder (serveur Azure Database pour PostgreSQL dans le cas présent).

    ![Sélectionner une source de données pour configurer la sauvegarde du serveur Azure Database pour PostgreSQL](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. Sélectionnez **Continuer**. Vous accédez alors à l’expérience de configuration de la sauvegarde qui est identique à celle accessible à partir d’un coffre Sauvegarde. [En savoir plus sur la configuration de la sauvegarde pour un serveur Azure Database pour PostgreSQL avec un coffre Sauvegarde](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Restaurer une instance de sauvegarde

Selon le type de source de données que vous souhaitez restaurer, suivez les instructions appropriées comme indiqué ci-dessous.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Si vous effectuez une restauration à partir d’un coffre Recovery Services

1. Accédez au Centre de sauvegarde et sélectionnez **Restaurer** en haut de l’onglet **Vue d’ensemble**.

    ![Vue d’ensemble du Centre de sauvegarde pour restaurer une machine virtuelle](./media/backup-center-actions/backup-center-overview-restore.png)

2. Sélectionnez le type de source de source que vous souhaitez restaurer.

    ![Sélectionner la source de données pour la restauration de la machine virtuelle](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Choisissez une instance de sauvegarde et sélectionnez **Continuer**. Vous accédez alors à l’expérience de paramètres de restauration qui est identique à celle accessible à partir d’un coffre Recovery Services. [En savoir plus sur la restauration d’une machine virtuelle Azure avec un coffre Recovery Services](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Si vous effectuez une restauration à partir d’un coffre de sauvegarde

1. Accédez au Centre de sauvegarde et sélectionnez **Restaurer** en haut de l’onglet **Vue d’ensemble**.
2. Sélectionnez le type de source de données que vous souhaitez restaurer (serveur Azure Database pour PostgreSQL dans le cas présent).

    ![Sélectionner une source de données pour restaurer le serveur Azure Database pour PostgreSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Choisissez une instance de sauvegarde et sélectionnez **Continuer**. Vous accédez alors à l’expérience de paramètres de restauration qui est identique à celle accessible à partir d’un coffre Recovery Services. [En savoir plus sur la restauration d’un serveur Azure Database pour PostgreSQL avec un coffre Sauvegarde](restore-azure-database-postgresql.md).

## <a name="create-a-new-vault"></a>Créer un coffre

Vous pouvez créer un coffre en accédant au Centre de sauvegarde et en sélectionnant **+ Coffre** en haut de l’onglet **Vue d’ensemble**.

![Create vault](./media/backup-center-actions/backup-center-create-vault.png)

* [En savoir plus sur la création d’un coffre Recovery Services](backup-create-rs-vault.md)
* [En savoir plus sur la création d’un coffre Sauvegarde](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Créer une nouvelle stratégie de sauvegarde

Selon le type de source de données que vous souhaitez sauvegarder, suivez les instructions appropriées comme indiqué ci-dessous.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Si vous effectuez une sauvegarde vers un coffre Recovery Services

1. Accédez au Centre de sauvegarde et sélectionnez **+ Stratégie** en haut de l’onglet **Vue d’ensemble**.

    ![Vue d’ensemble du Centre de sauvegarde pour la stratégie de sauvegarde](./media/backup-center-actions/backup-center-overview-policy.png)

2. Sélectionnez le type de source de source que vous souhaitez sauvegarder.

    ![Sélectionner la source de données pour la sauvegarde de la machine virtuelle](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Choisissez un coffre Recovery Services, puis sélectionnez **Continuer**. Vous accédez alors à l’expérience de configuration de la stratégie qui est identique à celle accessible à partir d’un coffre Recovery Services. [En savoir plus sur la création d’une stratégie de sauvegarde pour une machine virtuelle Azure avec un coffre Recovery Services](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Si vous effectuez une sauvegarde vers un coffre de sauvegarde

1. Accédez au Centre de sauvegarde et sélectionnez **+ Stratégie** en haut de l’onglet **Vue d’ensemble**.
2. Sélectionnez le type de source de données que vous souhaitez sauvegarder (serveur Azure Database pour PostgreSQL dans le cas présent).

    ![Sélectionner une source de données pour la stratégie de sauvegarde du serveur Azure Database pour PostgreSQL](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. Sélectionnez **Continuer**. Vous accédez alors à l’expérience de création de la stratégie qui est identique à celle accessible à partir d’un coffre Sauvegarde. [En savoir plus sur la création d’une stratégie de sauvegarde avec un coffre Sauvegarde](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Exécuter une sauvegarde à la demande pour une instance de sauvegarde

Le Centre de sauvegarde vous permet de rechercher des instances de sauvegarde au sein de votre espace de sauvegarde et d’exécuter des opérations de sauvegarde à la demande.

Pour arrêter la protection, accédez au centre de sauvegarde et sélectionnez l’option de menu **Instances de sauvegarde**. La sélection de cette option vous permet d’afficher les détails relatifs à toutes les instances de sauvegarde auxquelles vous avez accès. Vous pouvez rechercher l’instance de sauvegarde que vous souhaitez sauvegarder. Cliquez avec le bouton droit sur un élément de la grille pour ouvrir une liste des actions disponibles. Sélectionnez l’option **Sauvegarder maintenant** pour exécuter une sauvegarde à la demande.

![Sauvegarde à la demande](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Apprenez-en davantage sur l’exécution de sauvegardes à la demande pour les machines virtuelles Azure](backup-azure-manage-vms.md#run-an-on-demand-backup).

[Apprenez-en davantage sur l’exécution de sauvegardes à la demande pour le serveurs Azure Database pour PostgreSQL](backup-azure-database-postgresql.md).

## <a name="stop-backup-for-a-backup-instance"></a>Arrêter la sauvegarde d’une instance de sauvegarde

Dans certains scénarios, vous pouvez être amené à arrêter la sauvegarde d’une instance de sauvegarde, par exemple lorsque la ressource sous-jacente en cours de sauvegarde n’existe plus.

Pour arrêter la protection, accédez au centre de sauvegarde et sélectionnez l’option de menu **Instances de sauvegarde**. Ce faisant, tous les détails relatifs aux instances de sauvegarde auxquelles vous avez accès s’affichent. Vous pouvez rechercher l’instance de sauvegarde que vous souhaitez sauvegarder. Cliquez avec le bouton droit sur un élément de la grille pour ouvrir une liste des actions disponibles. Sélectionnez l’option **Arrêter la sauvegarde** pour arrêter la sauvegarde de l’instance de sauvegarde.

![Arrêter la protection](./media/backup-center-actions/backup-center-stop-protection.png)

- [Apprenez-en davantage](backup-azure-manage-vms.md#stop-protecting-a-vm) sur l’arrêt de la sauvegarde des machines virtuelles Azure.
- [Apprenez-en davantage](manage-azure-managed-disks.md#stop-protection-preview) sur l’arrêt de la sauvegarde d’un disque.
- [Apprenez-en davantage](manage-azure-database-postgresql.md#stop-protection-preview) sur l’arrêt de la sauvegarde du serveur Azure Database pour PostgreSQL.

## <a name="resume-backup"></a>Reprendre la sauvegarde

Vous pouvez reprendre la sauvegarde de vos charges de travail si vous avez conservé les données pendant l’**arrêt de la protection**.

- [Apprenez-en davantage](manage-azure-database-postgresql.md#resume-protection) sur la reprise de la protection de votre disque.
- [Apprenez-en davantage](manage-azure-managed-disks.md#resume-protection) sur la reprise de la protection pour le serveur Azure Database pour PostgreSQL.

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller et exécuter des sauvegardes](backup-center-monitor-operate.md)
* [Gérer votre espace de sauvegarde](backup-center-govern-environment.md)
* [Obtenir des insights sur vos sauvegardes](backup-center-obtain-insights.md)
