---
title: Gérer les bases de données SAP HANA sauvegardées sur des machines virtuelles Azure
description: Dans cet article, découvrez les tâches courantes de gestion et de supervision des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 03f7609b02461a89519ba19f3f70dd14e6922a2a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431332"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gérer et superviser des bases de données SAP HANA sauvegardées

Cet article décrit les tâches courantes de gestion et de supervision des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure et sauvegardées dans un coffre Recovery Services par le service [Sauvegarde Azure](./backup-overview.md). Vous apprendrez à superviser des travaux et des alertes, déclencher une sauvegarde à la demande, modifier des stratégies, arrêter et reprendre la protection de la base de données, ainsi que désinscrire une machine virtuelle des sauvegardes.

Si vous n’avez pas encore configuré de sauvegardes pour vos bases de données SAP HANA, consultez [Sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Surveiller les travaux de sauvegarde manuelles sur le portail

Le service Sauvegarde Azure affiche tous les travaux déclenchés manuellement dans la section **Travaux de sauvegarde** du **Centre de sauvegarde**.

:::image type="content" source="./media/sap-hana-db-manage/backup-center-jobs-list-inline.png" alt-text="Capture d’écran montrant la section Travaux de sauvegarde." lightbox="./media/sap-hana-db-manage/backup-center-jobs-list-expanded.png":::

Les travaux que vous voyez sur ce portail incluent les opérations de découverte, d’inscription, de sauvegarde et de restauration de base de données. Les travaux planifiés, notamment les sauvegardes de fichiers journaux, n’apparaissent pas dans cette section. Les sauvegardes déclenchées manuellement à partir des clients natifs SAP HANA (Studio/Cockpit/DBA Cockpit) n’apparaissent pas non plus ici.

:::image type="content" source="./media/sap-hana-db-manage/hana-view-jobs-inline.png" alt-text="Capture d’écran montrant la liste Travaux de sauvegarde." lightbox="./media/sap-hana-db-manage/hana-view-jobs-expanded.png":::

Pour en savoir plus sur la supervision, consultez [Supervision dans le Portail Azure](./backup-azure-monitoring-built-in-monitor.md) et [Supervision à l’aide d’Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Les alertes sont un moyen simple de superviser les sauvegardes des bases de données SAP HANA. Elles vous aident à vous concentrer sur les événements qui vous intéressent le plus sans vous perdre dans la multitude d’événements qu'une sauvegarde génère. Sauvegarde Azure vous permet de définir des alertes, et ces dernières peuvent être supervisées comme suit :

* Connectez-vous au [portail Azure](https://portal.azure.com/).
* Dans le tableau de bord du coffre, sélectionnez **Alertes de sauvegarde**.

  ![Alertes de sauvegarde sur le tableau de bord du coffre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Vous pourrez voir les alertes :

  ![Liste des alertes de sauvegarde](./media/sap-hana-db-manage/backup-alerts-list.png)

* Sélectionnez les alertes pour afficher plus de détails :

  ![Détails de l’alerte](./media/sap-hana-db-manage/alert-details.png)

Aujourd’hui, Sauvegarde Azure permet d’envoyer des alertes par e-mail. Ces alertes sont les suivantes :

* Déclenchées pour tous les échecs de sauvegarde.
* Consolidées au niveau de la base de données par code d’erreur.
* Envoyées uniquement pour le premier échec de sauvegarde d’une base de données.

Pour en savoir plus sur la supervision, consultez [Supervision dans le Portail Azure](./backup-azure-monitoring-built-in-monitor.md) et [Supervision à l’aide d’Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Opérations de gestion

Sauvegarde Azure simplifie la gestion d’une base de données SAP HANA sauvegardée grâce à une multitude d’opérations de gestion prises en charge. Ces opérations sont décrites plus en détail dans les sections suivantes.

### <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Les sauvegardes s’exécutent conformément à la planification de la stratégie. Vous pouvez exécuter une sauvegarde à la demande en procédant comme suit :

1. Dans le menu du **Centre de sauvegarde**, sélectionnez **Instances de sauvegarde**.
2. Sélectionnez **SAP HANA dans une machine virtuelle Azure** comme type de source de données, sélectionnez la machine virtuelle exécutant la base de données SAP HANA, puis cliquez sur **Sauvegarder maintenant**.
3. Dans **Sauvegarder maintenant**, choisissez le type de sauvegarde que vous souhaitez effectuer. Sélectionnez ensuite **OK**. Cette sauvegarde sera conservée en fonction de la stratégie associée à cet élément de sauvegarde.
4. Surveiller les notifications du portail. Pour surveiller la progression du travail, accédez à **Centre de sauvegarde** -> **Travaux de sauvegarde**, puis filtrez les travaux dont l’état est **En cours**. Selon la taille de votre base de données, la création de la sauvegarde initiale peut prendre un certain temps.

Par défaut, la période de rétention des sauvegardes à la demande est de 45 jours.

### <a name="hana-native-client-integration"></a>Intégration du client natif HANA

#### <a name="backup"></a>Sauvegarde

Les sauvegardes à la demande déclenchées à partir d’un des clients natifs HANA (pour **Backint**) s’affichent dans la liste de sauvegardes sur la page **Instances de sauvegarde**.

![Dernières sauvegardes exécutées](./media/sap-hana-db-manage/last-backups.png)

Vous pouvez également [surveiller ces sauvegardes](#monitor-manual-backup-jobs-in-the-portal) dans la page **Travaux de sauvegarde**.

Ces sauvegardes complètes figurent aussi dans la liste des points de restauration pour restauration.

![Liste des points de restauration](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restaurer

Les restaurations déclenchées à partir de clients HANA natifs (à l’aide de **Backint**) pour restaurer sur **la même machine** peuvent être [surveillées](#monitor-manual-backup-jobs-in-the-portal) à partir de la page **Travaux de sauvegarde**.
Les restaurations déclenchées à partir de clients natifs HANA à restaurer sur un autre ordinateur ne sont pas autorisées. Cela est dû au fait que le service Sauvegarde Azure ne peut pas authentifier le serveur cible, en fonction des règles du contrôle d’accès en fonction du rôle Azure, pour la restauration.

#### <a name="delete"></a>DELETE

L’opération de suppression native dans HANA **n’est pas** prise en charge par Sauvegarde Azure, car la stratégie de sauvegarde détermine le cycle de vie des sauvegardes dans le coffre Azure Recovery Services.

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Exécuter une sauvegarde de client natif SAP HANA sur le disque local d’une base de données avec Sauvegarde Azure activé

Si vous souhaitez sauvegarder localement (à l’aide de HANA Studio/Cockpit) une base de données qui est sauvegardée par Sauvegarde Azure, procédez comme suit :

1. Attendez la fin de toute sauvegarde complète ou de fichier journal de la base de données. Vérifiez l’état dans SAP HANA Studio/Cockpit.
2. Pour la base de données appropriée :
    1. Annulez les paramètres backint. Pour ce faire, double-cliquez sur **systemdb** > **Configuration (Configuration)**  > **Select Database (Sélectionner la base de données)**  > **Filter (Log) (Filtrer [journal])** .
        * enable_auto_log_backup : Non
        * log_backup_using_backint : False
        * catalog_backup_using_backint:False
3. Effectuez une sauvegarde complète à la demande de la base de données.
4. Inversez ensuite les étapes. Pour la même base de données pertinente mentionnée ci-dessus,
    1. Réactivez les paramètres backint :
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint : True
        1. enable_auto_log_backup : Oui

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Gérer ou nettoyer le catalogue HANA pour une base de données avec Sauvegarde Azure activé

Si vous souhaitez modifier ou nettoyer le catalogue de sauvegarde, procédez comme suit :

1. Attendez la fin de toute sauvegarde complète ou de fichier journal de la base de données. Vérifiez l’état dans SAP HANA Studio/Cockpit.
2. Pour la base de données appropriée :
    1. Annulez les paramètres backint. Pour ce faire, double-cliquez sur **systemdb** > **Configuration (Configuration)**  > **Select Database (Sélectionner la base de données)**  > **Filter (Log) (Filtrer [journal])** .
        * enable_auto_log_backup : Non
        * log_backup_using_backint : False
        * catalog_backup_using_backint:False
3. Modifiez le catalogue et supprimez les entrées les plus anciennes.
4. Inversez ensuite les étapes. Pour la même base de données pertinente mentionnée ci-dessus,
    1. Réactivez les paramètres backint
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint : True
        1. enable_auto_log_backup : Oui

### <a name="change-policy"></a>Changer la stratégie

Vous pouvez changer la stratégie sous-jacente d’un élément de sauvegarde SAP HANA.

Dans le tableau de bord du **Centre de sauvegarde**, accédez à **Instances de sauvegarde** :

* Choisissez **SAP Hana dans une machine virtuelle Azure** comme type de source de source de données.

  :::image type="content" source="./media/sap-hana-db-manage/hana-backup-instances-inline.png" alt-text="Capture d’écran montrant le choix de SAP HANA dans une machine virtuelle Azure." lightbox="./media/sap-hana-db-manage/hana-backup-instances-expanded.png":::

* Choisissez l’élément de sauvegarde dont vous souhaitez modifier la stratégie sous-jacente.
* Sélectionnez la stratégie de sauvegarde existante.

  ![Sélectionnez une stratégie de sauvegarde existante.](./media/sap-hana-db-manage/existing-backup-policy.png)

* Changez la stratégie en choisissant dans la liste. [Créez une nouvelle stratégie de sauvegarde](./backup-azure-sap-hana-database.md#create-a-backup-policy) si nécessaire.

  ![Choisir la stratégie dans la liste déroulante](./media/sap-hana-db-manage/choose-backup-policy.png)

* Enregistrez les modifications.

  ![Enregistrez les modifications](./media/sap-hana-db-manage/save-changes.png)

* La modification de la stratégie aura un impact sur tous les éléments de sauvegarde associés et déclenchera les tâches de **configuration de la protection** correspondantes.

>[!NOTE]
> Toute modification de la période de rétention sera appliquée rétroactivement à tous les anciens points de récupération ainsi qu’aux nouveaux.

### <a name="modify-policy"></a>Modifier la stratégie

Modifiez la stratégie de façon à changer les types de sauvegarde, leur fréquence et leur durée de rétention.

>[!NOTE]
>Toute modification apportée à la période de rétention s’applique rétroactivement à tous les anciens points de récupération, en plus des nouveaux.

1. Dans le tableau de bord **Centre de sauvegarde**, accédez à **Stratégies de sauvegarde**, puis choisissez la stratégie à modifier.

   :::image type="content" source="./media/sap-hana-db-manage/backup-center-policies-inline.png" alt-text="Capture d’écran montrant le choix de la stratégie à modifier." lightbox="./media/sap-hana-db-manage/backup-center-policies-expanded.png":::

1. Sélectionnez **Modifier**.

   ![Sélectionner Modifier](./media/sap-hana-db-manage/modify-policy.png)

1. Choisissez la fréquence pour les types de sauvegarde.

   ![Choisir la fréquence de sauvegarde](./media/sap-hana-db-manage/choose-frequency.png)

La modification de la stratégie a un impact sur tous les éléments de sauvegarde associés et déclenche les tâches de **configuration de la protection** correspondantes.

### <a name="inconsistent-policy"></a>Stratégie incohérente

Parfois, une opération de modification de la stratégie peut produire une version de stratégie **incohérente** pour certains éléments de sauvegarde. Cela se produit lorsque la tâche de **configuration de la protection** correspondante échoue pour l’élément de sauvegarde après le déclenchement d’une opération de modification de la stratégie. Elle se présente comme suit dans l’affichage des éléments de sauvegarde :

![Stratégie incohérente](./media/sap-hana-db-manage/inconsistent-policy.png)

Vous pouvez corriger la version de la stratégie pour tous les éléments concernés en un seul clic :

![Corriger la version de la stratégie](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Arrêter la protection d’une base de données SAP HANA

Vous pouvez arrêter la protection d’une base de données SAP HANA de deux manières :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêter tous les travaux de sauvegarde à venir, mais conserver les points de récupération.

Si vous choisissez de conserver les points de récupération, gardez à l’esprit les considérations suivantes :

* Tous les points de récupération resteront intacts et toutes les opérations de nettoyage s’arrêteront à l’arrêt de la protection avec données conservées.
* Vous serez facturé pour l’instance protégée et le stockage utilisé. Pour plus d’informations, voir [Tarification Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).
* Si vous supprimez une source de données sans arrêter les sauvegardes, les nouvelles sauvegardes échouent.

Pour arrêter la protection de la base de données :

1. Dans le tableau de bord du **Centre de sauvegarde**, sélectionnez **Instances de sauvegarde**.
1. Sélectionnez **SAP Hana dans une machine virtuelle Azure** comme type de source de source de données.

   :::image type="content" source="./media/sap-hana-db-manage/hana-backup-instances-inline.png" alt-text="Capture d’écran montrant la sélection de SAP HANA dans une machine virtuelle Azure." lightbox="./media/sap-hana-db-manage/hana-backup-instances-expanded.png":::

1. Sélectionnez la base de données dont vous souhaitez arrêter la protection.

1. Dans le menu de la base de données, sélectionnez **Arrêter la sauvegarde**.

   :::image type="content" source="./media/sap-hana-db-manage/stop-backup.png" alt-text="Capture d’écran montrant la sélection de l’option Arrêter la sauvegarde.":::

1. Dans le menu **Arrêter la sauvegarde**, indiquez si vous souhaitez conserver ou supprimer les données. Vous pouvez également indiquer une raison ou formuler un commentaire.

   :::image type="content" source="./media/sap-hana-db-manage/retain-backup-data.png" alt-text="Capture d’écran montrant le choix de conserver ou de supprimer des données.":::

1. Sélectionnez **Arrêter la sauvegarde**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Reprendre la protection d’une base de données SAP HANA

Lorsque vous arrêtez la protection de la base de données SAP HANA, si vous sélectionnez l’option **Conserver les données de sauvegarde**, vous pourrez reprendre la protection ultérieurement. Si vous ne conservez pas les données de sauvegarde, vous ne pourrez pas réactiver la protection.

Pour reprendre la protection d’une base de données SAP HANA :

* Ouvrez l’élément de la sauvegarde, puis sélectionnez **Reprendre la sauvegarde**.

   ![Sélectionnez Reprendre la sauvegarde](./media/sap-hana-db-manage/resume-backup.png)

* Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

### <a name="upgrading-from-sdc-to-mdc"></a>Mise à niveau de SDC vers MDC

Découvrez comment continuer la sauvegarde d’une base de données SAP HANA [après la mise à niveau de SDC vers MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Mise à niveau de SDC à MDC sans modification du SID

Découvrez comment continuer la sauvegarde d’une base de données SAP HANA dont [le SID n’a pas été modifié après la mise à niveau de SDC vers MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Mise à niveau vers une nouvelle version dans SDC ou MDC

Découvrez comment continuer la sauvegarde d’une base de données SAP HANA dont [la version est mise à niveau](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Désinscrire une instance SAP HANA

Désinscrivez une instance SAP HANA après avoir désactivé la protection, mais avant de supprimer le coffre :

* Sur le tableau de bord du coffre, sous **Gestion**, sélectionnez **Infrastructure de sauvegarde**.

   ![Choisir Infrastructure de sauvegarde](./media/sap-hana-db-manage/backup-infrastructure.png)

* Sélectionnez le **Type de gestion des sauvegardes** comme **Charge de travail dans une machine virtuelle Azure**

   ![Sélectionnez le Type de gestion des sauvegardes comme Charge de travail dans une machine virtuelle Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Dans **Serveurs protégés**, sélectionnez l’instance à désinscrire. Pour supprimer le coffre, vous devez désinscrire tous les serveurs et toutes les instances.

* Cliquez avec le bouton droit sur l’instance protégée, puis sélectionnez **Désinscrire**.

   ![Sélectionnez désinscrire](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Réinscrire une extension sur la machine virtuelle du serveur SAP HANA

Parfois, l’extension de charge de travail sur la machine virtuelle peut être affectée pour une raison ou une autre. Dans ce cas, toutes les opérations déclenchées sur la machine virtuelle commencent à échouer. Vous devrez alors réinscrire l’extension sur la machine virtuelle. L’opération de réinscription a pour effet de réinstaller l’extension de sauvegarde de charge de travail sur la machine virtuelle pour que les opérations se poursuivent.

Utilisez cette option avec prudence, car une fois déclenchée sur une machine virtuelle avec une extension déjà saine, cette opération provoque le redémarrage de l’extension. Cela peut provoquer l’échec de tous les travaux en cours. Vérifiez la présence d’un ou plusieurs de ces [symptômes](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) avant de déclencher l’opération de réinscription.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [résoudre les problèmes courants lors de la sauvegarde de bases de données SAP HANA](./backup-azure-sap-hana-database-troubleshoot.md).
