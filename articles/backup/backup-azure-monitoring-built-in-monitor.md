---
title: Surveiller les charges de travail protégées de Sauvegarde Azure
description: Dans cet article, découvrez les fonctionnalités de surveillance et de notification des charges de travail de Sauvegarde Azure à l’aide du Portail Azure.
ms.topic: conceptual
ms.date: 11/02/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: afc23090ce15621542ec6035c67d36b85a3414c1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439768"
---
# <a name="monitoring-azure-backup-workloads"></a>Surveillance des charges de travail de Sauvegarde Azure

Le service Sauvegarde Azure fournit plusieurs solutions de sauvegarde basées sur l’exigence en matière de sauvegarde et la topologie d’infrastructure (locale ou Azure). N’importe quel administrateur ou utilisateur de sauvegarde doit voir ce qu’il se passe au niveau de toutes les solutions et s’attendre à être informé dans les scénarios importants. Cet article décrit en détail les fonctionnalités de surveillance et de notification fournies par le service Sauvegarde Azure.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Éléments de sauvegarde dans le coffre Recovery Services

Vous pouvez surveiller tous vos éléments de sauvegarde via un coffre Recovery Services. L’accès à la section **Instances de sauvegarde** dans le **Centre de sauvegarde** a pour effet d’afficher une liste détaillée de tous les éléments de sauvegarde du type de charge de travail donné, avec des informations sur le dernier état de sauvegarde pour chaque élément, le dernier point de restauration disponible, etc.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-instances-inline.png" alt-text="Capture d’écran affichant les éléments de sauvegarde du coffre Recovery Services." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-instances-expanded.png":::

>[!NOTE]
>Pour les éléments sauvegardés dans Azure à l’aide de DPM, la liste affiche toutes les sources de données protégées (à la fois sur disque et en ligne) à l’aide du serveur DPM. Si la protection est arrêtée pour la source de données avec conservation des données de sauvegarde, la source de données sera toujours répertoriée dans le portail. Vous pouvez accéder aux détails de la source de données pour voir si les points de récupération sont présents sur le disque, en ligne ou les deux. En outre, les sources de données pour lesquelles la protection en ligne est arrêtée, mais les données sont conservées, la facturation des points de récupération en ligne continue jusqu’à ce que les données soient entièrement supprimées.
>
> La version de DPM doit être DPM 1807 (5.1.378.0) ou DPM 2019 (version 10.19.58.0 ou ultérieure) pour que les éléments de sauvegarde soient visibles dans le portail du coffre Recovery Services.

## <a name="backup-jobs-in-backup-center"></a>Travaux de sauvegarde dans le Centre de sauvegarde

Le service Sauvegarde Azure offre des fonctionnalités de surveillance et d’alerte intégrées pour les charges de travail protégées par Sauvegarde Azure. L’accès au volet **Travaux de sauvegarde** dans le **Centre de sauvegarde** vous permet de voir les travaux de sauvegarde et de restauration récents dans vos coffres.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-inline.png" alt-text="Capture d’écran montrant la surveillance intégrée du coffre Recovery Services." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-expanded.png":::

Les travaux sont générés lorsque des opérations telles que la configuration de la sauvegarde, la sauvegarde, la restauration, la suppression de la sauvegarde, etc. sont effectuées.

Les travaux provenant des solutions Sauvegarde Azure suivantes sont indiqués ici :

- Sauvegarde des machines virtuelles Azure
- Sauvegarde de fichiers Azure
- Sauvegarde de charge de travail Azure comme SQL et SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

Les travaux provenant de System Center Data Protection Manager (SC-DPM) et du Serveur Sauvegarde Microsoft Azure ne sont pas affichés.

> [!NOTE]
> Les charges de travail Azure comme les sauvegardes SQL et SAP HANA au sein de machines virtuelles Azure ont un très grand nombre de travaux de sauvegarde. Par exemple, les sauvegardes de fichier journal peuvent s’exécuter toutes les 15 minutes. Par conséquent, pour ces charges de travail de base de données, seules les opérations déclenchées par l’utilisateur sont affichées. Les opérations de sauvegarde planifiées ne sont pas affichées.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertes de sauvegarde dans le coffre Recovery Services

Les alertes sont principalement des scénarios dans le cadre desquels les utilisateurs reçoivent des notifications leur permettant de prendre les mesures nécessaires. La section **Alertes de sauvegarde** affiche les alertes générées par le service Sauvegarde Azure. Ces alertes sont définies par le service et l’utilisateur ne peut pas créer d’alerte de façon personnalisée.

### <a name="alert-scenarios"></a>Scénarios d’alerte

Les scénarios suivants sont définis par le service en tant que scénarios pouvant donner lieu à des alertes.

- Échecs de sauvegarde/restauration
- Sauvegarde réussie avec des avertissements pour l’agent MARS (Microsoft Azure Recovery Services)
- Arrêt de la protection avec données conservées/Arrêt de la protection avec données supprimées
- Fonctionnalité de suppression réversible désactivée pour le coffre
- Type de sauvegarde non pris en charge pour les charges de travail de base de données

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Les alertes provenant des solutions Sauvegarde Azure suivantes sont indiquées ici

- Sauvegardes de machines virtuelles Azure
- Sauvegardes de fichiers Azure
- Sauvegardes de charge de travail Azure comme SQL, SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

> [!NOTE]
> Les alertes provenant de System Center Data Protection Manager (SC-DPM) et du Serveur Sauvegarde Microsoft Azure ne sont pas affichées ici.

### <a name="consolidated-alerts"></a>Alertes consolidées

Pour les solutions de sauvegarde de charge de travail Azure comme SQL et SAP HANA, les sauvegardes de fichier journal peuvent être générées très souvent (toutes les 15 minutes en fonction de la stratégie). Vous pouvez donc aussi avoir très souvent des échecs de sauvegarde de fichier journal (toutes les 15 minutes maximum). Dans ce scénario, l’utilisateur final est surchargé si une alerte est générée à chaque fois qu’il y a un échec. Par conséquent, une alerte est envoyée pour la première occurrence et, si les échecs suivants sont liés à la même cause racine, aucune autre alerte n’est générée. La première alerte est mise à jour avec le nombre d’échecs. Toutefois, si l’alerte est désactivée par l’utilisateur, l’occurrence suivante déclenche une autre alerte qui est traitée comme une première alerte pour cette occurrence. C’est de cette façon que la sauvegarde Azure effectue la consolidation des alertes pour les sauvegardes SQL et SAP HANA. Les travaux de sauvegarde à la demande ne sont pas consolidés.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Cas exceptionnels dans lesquels une alerte n’est pas déclenchée

Dans quelques rares exceptions, une alerte n’est pas déclenchée en cas d’échec. Il s'agit de :

- L’utilisateur a explicitement annulé le travail en cours d’exécution.
- Le travail échoue, car un autre travail de sauvegarde est en cours d’exécution (aucune intervention n’est nécessaire dans ce cas, dans la mesure où il nous faut simplement attendre que la tâche précédente se termine).
- Le travail de sauvegarde de machine virtuelle échoue, car la machine virtuelle Azure sauvegardée n’existe plus.
- [Alertes consolidées](#consolidated-alerts)

Dans le cadre des exceptions ci-dessus, il est entendu que le résultat de ces opérations (principalement déclenchées par l’utilisateur) s’affiche immédiatement sur le portail et dans les clients PS et CLI. Par conséquent, l’utilisateur en est informé immédiatement et n’a pas besoin de notification.

### <a name="alert-types"></a>Types d’alertes

Les alertes peuvent être définies selon trois types, en fonction de leur gravité :

- **Critique** : En principe, tout échec de sauvegarde ou de récupération (planifiée ou déclenchée par l’utilisateur) conduit à la génération d’une alerte et s’affiche en tant qu’alerte critique, de même que les opérations destructrices telles que la suppression de sauvegarde.
- **Avertissement** : Si l’opération de sauvegarde réussit, mais avec quelques avertissements, ces derniers sont répertoriés sous forme d’alertes d’avertissement. Les alertes d’avertissement sont disponibles uniquement pour les sauvegardes d’Agent Sauvegarde Azure.
- **Informations** : À ce jour, aucune alerte d’information n’est générée par le service Sauvegarde Azure.

## <a name="notification-for-backup-alerts"></a>Notification pour les alertes de sauvegarde

> [!NOTE]
> La configuration de la notification peut être effectuée uniquement via le portail Azure. La prise en charge de l’interface CLI/de PS/de l’API REST/du modèle Azure Resource Manager n’est pas assurée.

Lorsqu’une alerte est déclenchée, les utilisateurs en sont informés. Le service Sauvegarde Azure fournit un mécanisme intégré de notification par e-mail. Vous pouvez spécifier des adresses e-mail individuelles ou des listes de distribution pour être informé de la génération d’une alerte. Vous pouvez également choisir d’être informé à chaque alerte ou de les grouper dans une synthèse horaire avant d’être informé.

![Capture d’écran de la notification par e-mail intégrée du coffre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Lorsque la notification est configurée, vous recevez un e-mail de bienvenue ou d’introduction. Il confirme que le service Sauvegarde Azure peut envoyer des e-mails à ces adresses lorsqu’une alerte est déclenchée.<br>

Si la fréquence était définie sur une synthèse horaire et qu’une alerte a été déclenchée et résolue dans l’heure, elle ne fera pas partie de la synthèse horaire à venir.

> [!NOTE]
>
> - Si une opération destructive comme l’**arrêt de la protection avec suppression des données** est effectuée, une alerte est déclenchée et un e-mail est envoyé aux administrateurs, coadministrateurs et propriétaires de l’abonnement, même si les notifications ne sont pas configurées pour le coffre Recovery Services.
> - Pour configurer la notification pour les travaux réussis, utilisez [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Désactivation des alertes

Pour désactiver/résoudre une alerte active, vous pouvez sélectionner l’élément de liste correspondant à l’alerte que vous souhaitez désactiver. Cette opération ouvre un écran qui affiche des informations détaillées sur l’alerte, avec un bouton **Désactiver** en haut. Le fait de sélectionner ce bouton change l’état de l’alerte en **Inactif**. Vous pouvez également désactiver une alerte en cliquant avec le bouton droit sur l’élément de liste correspondant à cette alerte et en sélectionnant **Désactiver**.

![Capture d’écran de l’inactivation des alertes Centre de sauvegarde](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Alertes d’Azure Monitor pour Sauvegarde Azure (préversion)

Le service Sauvegarde Azure fournit également des alertes via Azure Monitor, afin de permettre aux utilisateurs de bénéficier d’une expérience cohérente de gestion des alertes dans différents services Azure, donc Sauvegarde. Vous pouvez router les alertes d’Azure Monitor vers n’importe quel canal de notification pris en charge par Azure Monitor, par exemple, e-mail, ITSM, webhook, application logique, etc.

Actuellement, Sauvegarde Azure a mis à disposition deux principaux types d’alertes intégrées :

* **Alertes de sécurité** : pour des scénarios tels que la suppression de données de sauvegarde ou la désactivation de la fonctionnalité de suppression réversible pour un coffre, des alertes de sécurité (de gravité Sev 0) sont déclenchées et affichées dans le portail Azure ou consommées via d’autres clients (PowerShell, CLI et API REST). Les alertes de sécurité sont générées par défaut et ne peuvent pas être désactivées. Toutefois, vous pouvez contrôler les scénarios pour lesquels les notifications (par exemple, les e-mails) doivent être déclenchées. Pour plus d’informations sur la configuration des notifications, consultez [Règles d’action](../azure-monitor/alerts/alerts-action-rules.md).
* **Alertes d’échec de tâche** : pour des scénarios tels que l’échec de la sauvegarde et de la restauration, Sauvegarde Azure fournit des alertes intégrées par le biais d’Azure Monitor (gravité Sev 1). Contrairement aux alertes de sécurité, vous pouvez choisir de désactiver les alertes Azure Monitor pour les scénarios d’échec de la tâche. Par exemple, si vous avez déjà configuré des règles d’alerte personnalisées pour les échecs de tâche via Log Analytics, et que vous n’avez pas besoin que des alertes intégrées soient déclenchées pour chaque échec de tâche. Par défaut, les alertes pour les échecs de tâche sont désactivées. Pour plus d’informations, reportez-vous à la [section sur l’activation des alertes pour ces scénarios](#turning-on-azure-monitor-alerts-for-job-failure-scenarios).
 
Le tableau suivant récapitule les différentes alertes de sauvegarde actuellement disponibles (en préversion) via Azure Monitor et les types de coffres/charges de travail pris en charge :

| **Catégorie d’alerte** | **Nom de l’alerte** | **Types de charges de travail / types de coffres pris en charge** | **Description** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| Sécurité | Supprimer des données de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> SQL dans les machines virtuelles Azure (scénarios non AG) <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Agent Azure Backup <br><br> <li> DPM <br><br> <li> Azure Backup Server <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> Objets blob Azure <br><br> <li> Azure Disques managés  | Cette alerte est déclenchée lorsqu’un utilisateur arrête la sauvegarde et supprime les données de sauvegarde (Remarque : Si la fonctionnalité de suppression réversible est désactivée pour le coffre, l’alerte Supprimer des données de sauvegarde n’est pas reçue) |
| Sécurité | Suppression définitive à venir | <li> Machine virtuelle Azure <br><br> <li> SQL dans les machines virtuelles Azure (scénarios non AG) <br><br> <li> SAP HANA sur machine virtuelle Azure | Pour toutes les charges de travail qui prennent en charge la suppression réversible, cette alerte est déclenchée lorsque les données de sauvegarde d’un élément sont à deux jours d’être supprimées définitivement par le service Sauvegarde Azure | 
| Sécurité | Suppression définitive terminée | <li> Machine virtuelle Azure <br><br> <li> SQL dans les machines virtuelles Azure (scénarios non AG) <br><br> <li> SAP HANA sur machine virtuelle Azure | Supprimer des données de sauvegarde |
| Sécurité | Suppression réversible désactivée pour le coffre | Coffres Recovery Services | Cette alerte est déclenchée lorsque les données de sauvegarde d’un élément supprimées de manière réversible ont été définitivement supprimées par le service Sauvegarde Azure | 
| travaux | Échec de la sauvegarde | <li> Machine virtuelle Azure <br><br> <li> SQL dans les machines virtuelles Azure (scénarios non AG) <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Agent Azure Backup <br><br> <li> Azure Files <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> Disques managés Azure | Cette alerte est déclenchée lorsqu’un échec de tâche de sauvegarde s’est produit. Par défaut, les alertes pour les échecs de sauvegarde sont désactivées. Pour plus d’informations, reportez-vous à la [section sur l’activation des alertes pour ce scénario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 
| travaux | Échec de la restauration | <li> Machine virtuelle Azure <br><br> <li> SQL dans les machines virtuelles Azure (scénarios non AG) <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Agent Azure Backup <br><br> <li> Azure Files <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> Objets blob Azure <br><br> <li> Azure Disques managés| Cette alerte est déclenchée lorsqu’un échec de tâche de restauration s’est produit. Par défaut, les alertes pour les échecs de restauration sont désactivées. Pour plus d’informations, reportez-vous à la [section sur l’activation des alertes pour ce scénario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>Activation des alertes Azure Monitor pour les scénarios d’échec de tâche

Pour activer les alertes Azure Monitor pour les scénarios d’échec de sauvegarde et de restauration, suivez les étapes ci-dessous :

1. Accédez au portail Azure et recherchez **Fonctionnalités d’évaluation**.

    ![Capture d’écran de l’affichage des fonctionnalités d’évaluation dans le portail](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. Vous pouvez afficher la liste de toutes les fonctionnalités d’évaluation auxquelles vous pouvez adhérer.

    * Si vous souhaitez recevoir des alertes d’échec de tâche pour les charges de travail sauvegardées dans les coffres Recovery Services, sélectionnez l’indicateur nommé **EnableAzureBackupJobFailureAlertsToAzureMonitor** correspondant au fournisseur Microsoft.RecoveryServices (colonne 3).
    * Si vous souhaitez recevoir des alertes d’échec de tâche pour les charges de travail sauvegardées dans les coffres Sauvegarde, sélectionnez l’indicateur nommé **EnableAzureBackupJobFailureAlertsToAzureMonitor** correspondant au fournisseur Microsoft.DataProtection (colonne 3).

    ![Capture d’écran de l’inscription aux alertes des fonctionnalités d’évaluation](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. Cliquez sur **Enregistrer** pour activer cette fonctionnalité pour votre abonnement.
    > [!NOTE]
    > L’inscription peut prendre jusqu’à 24 heures pour prendre effet. Pour activer cette fonctionnalité pour plusieurs abonnements, répétez le processus ci-dessus en sélectionnant l’abonnement concerné en haut de l’écran. Nous vous recommandons également de réinscrire l’indicateur d’évaluation si une nouvelle ressource a été créée dans l’abonnement après l’inscription initiale afin de continuer à recevoir des alertes.

4. Nous vous recommandons également d’inscrire le fournisseur de ressources pour vous assurer que les informations d’inscription de la fonctionnalité sont synchronisées avec le service de Sauvegarde Azure comme prévu. Pour inscrire le fournisseur de ressources, exécutez la commande PowerShell suivante dans l’abonnement pour lequel vous avez enregistré l’indicateur de fonctionnalité.

```powershell
Register-AzResourceProvider -ProviderNamespace <ProviderNamespace>
```

Pour recevoir des alertes pour les coffres des Services de récupération, utilisez la valeur _Microsoft.RecoveryServices_ pour le paramètre _ProviderNamespace_. Pour recevoir des alertes pour les coffres de Sauvegarde, utilisez la valeur _Microsoft.DataProtection_.

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>Affichage des alertes déclenchées dans le portail Azure 

Une fois qu’une alerte a été déclenchée pour un coffre, vous pouvez l’afficher sur le portail Azure en accédant à Centre de sauvegarde. Dans l’onglet **Vue d’ensemble**, vous pouvez voir un résumé des alertes actives réparties par gravité. Deux types d’alertes s’affichent :

* **Alertes de source de données** : les alertes liées à une source de données spécifique en cours de sauvegarde (par exemple, échec de la sauvegarde ou de la restauration d’une machine virtuelle, suppression des données de sauvegarde d’une base de données, etc.) apparaissent sous la section **Alertes de source de données**.
* **Alertes globales** : les alertes qui ne sont pas liées à une source de données spécifique (par exemple, la désactivation de la fonctionnalité de suppression réversible pour un coffre) apparaissent sous la section **Alertes globales**.

Chacun des types d’alertes ci-dessus est subdivisé en alertes **Sécurité** et **Configuré**. Actuellement, les alertes Sécurité incluent les scénarios de suppression des données de sauvegarde de désactivation de la suppression réversible pour le coffre (pour les charges de travail pertinentes, comme indiqué dans la section ci-dessus). Les alertes Configuré comprennent les échecs de sauvegarde et de restauration, car ces alertes ne sont déclenchées qu’après l’inscription de la fonctionnalité dans le portail d’évaluation.

![Capture d’écran de l’affichage des alertes dans Centre de sauvegarde](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

En cliquant sur l’un des numéros (ou sur l’élément de menu **Alertes**), vous obtenez une liste de toutes les alertes actives déclenchées avec les filtres correspondants appliqués. Vous pouvez filtrer sur une série de propriétés, telles que l’abonnement, le groupe de ressources, le coffre, la gravité, l’état, etc. Vous pouvez cliquer sur l’une des alertes pour obtenir plus de détails sur l’alerte, notamment la source de données concernée, la description de l’alerte et l’action recommandée, etc.

![Capture d’écran de l’affichage des détails de l’alerte](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

Vous pouvez changer l’état d’une alerte en **Réceptionné** ou **Fermé** en cliquant sur **Changer l’état de l’alerte**.

![Capture d’écran de la modification de l’état de l’alerte](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - Dans Centre de sauvegarde, seules les alertes pour les charges de travail Azure s’affichent actuellement. Pour afficher les alertes pour les ressources locales, accédez coffre Recovery Services et cliquez sur l’élément de menu **Alertes**.
> - Seules les alertes Azure Monitor sont affichées dans Centre de sauvegarde. Les alertes générées par l’ancienne solution d’alerte (accessible via l’onglet [Alertes Sauvegarde](#backup-alerts-in-recovery-services-vault) dans le coffre Recovery Services) ne sont pas affichées dans Centre de sauvegarde.
Pour plus d’informations concernant les alertes d’Azure Monitor, consultez [Vue d’ensemble des alertes dans Azure](../azure-monitor/alerts/alerts-overview.md).

### <a name="configuring-notifications-for-alerts"></a>Configuration des notifications pour les alertes

Pour configurer les notifications pour les alertes Azure Monitor, vous devez créer une règle d’action. Les étapes suivantes montrent comment créer une règle d’action pour envoyer des notifications par e-mail à une adresse e-mail donnée. Des instructions similaires s’appliquent pour acheminer ces alertes vers d’autres canaux de notification, comme ITSM, webhook, application logique, etc.

1. Accédez à **Centre de sauvegarde** dans le portail Azure. Cliquez sur l’élément de menu **Alertes** et sélectionnez **Gérer les actions**.

    ![Capture d’écran de Gérer les actions dans Centre de sauvegarde](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. Accédez à l’onglet **Règles d’action (préversion)** et cliquez sur **Nouvelle règle d’action**.

    ![Capture d’écran de la création d’une nouvelle règle d’action](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. Sélectionnez l’étendue pour laquelle la règle d’action doit être appliquée. Vous pouvez appliquer la règle d’action à toutes les ressources d’un abonnement. Si vous le souhaitez, vous pouvez également appliquer des filtres sur les alertes, par exemple, pour générer des notifications uniquement pour les alertes d’une certaine gravité.

    ![Capture d’écran de la définition de l’étendue de la règle d’action](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. Créez un groupe d’actions. Un groupe d’actions est la destination à laquelle la notification d’une alerte doit être envoyée, par exemple, une adresse e-mail.
 
    ![Capture d’écran de la création d’un nouveau groupe d’actions](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. Dans l’onglet **Informations de base**, sélectionnez le nom du groupe d’actions et l’abonnement et le groupe de ressources sous lesquels il doit être créé.

    ![Capture d’écran des propriétés de base d’un groupe d’actions](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. Dans l’onglet **Notifications**, sélectionnez **E-mail/SMS/Push/Voix**, puis entrez l’adresse e-mail du destinataire.

    ![Capture d’écran de la définition des propriétés de notification](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour déployer le groupe d’actions.

8. Enfin, enregistrez la règle d’action.

[En savoir plus sur les règles d’action dans Azure Monitor](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>Étapes suivantes

[Superviser les charges de travail Sauvegarde Azure à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
