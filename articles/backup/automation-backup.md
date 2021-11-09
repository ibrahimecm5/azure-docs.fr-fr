---
title: Automatisation dans Sauvegarde Azure
description: Fournit un résumé des capacités d’automatisation offertes par Sauvegarde Azure.
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: ba817714fffb63e1c2b989c43e4a72c1d0f91235
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097643"
---
# <a name="automation-in-azure-backup"></a>Automatisation dans Sauvegarde Azure

À mesure que vous gérez vos opérations dans Azure, votre patrimoine de sauvegarde peut s’étendre en incluant de nouvelles charges de travail diverses ou en effectuant un scale-up du volume.

À petite échelle, les actions suivantes peuvent être manuelles ou effectuées facilement : identifier les machines à sauvegarder, configurer la sauvegarde, surveiller leur état, extraire des données, etc. Mais à grande échelle, ces actions peuvent être difficiles, complexes et sujettes à des erreurs.

Sauvegarde Azure vous permet d’automatiser la plupart des tâches de sauvegarde à l’aide de méthodes programmatiques. Ce document présente les différents clients d’automatisation pris en charge par Sauvegarde Azure. Il vous guide également à travers certains scénarios d’automatisation de bout en bout qui sont couramment utilisés dans les déploiements de sauvegarde à l’échelle de l’entreprise.

## <a name="automation-methods"></a>Méthodes d’automatisation

Pour accéder aux fonctionnalités de Sauvegarde Azure, vous pouvez utiliser les méthodes d’automatisation standard suivantes prises en charge par Azure :

- PowerShell
- Interface de ligne de commande
- API REST
- Kit de développement logiciel (SDK) Python
- Kit de développement logiciel (SDK) Go
- Terraform
- Modèles ARM
- Bicep

Vous pouvez également utiliser Sauvegarde Azure en association avec d’autres services Azure, tels que Logic Apps, Runbooks et Groupes d’actions, pour configurer des flux de travail d’automatisation de bout en bout.

Pour plus d’informations sur les différents scénarios pris en charge par les clients d’automatisation et les références de document correspondantes, consultez la section [Solutions d’automatisation prises en charge pour Sauvegarde Azure](#supported-automation-methods-by-operation-type).

## <a name="sample-automation-scenarios"></a>Exemples de scénarios d’automatisation

Cette section présente quelques cas d’usage courants de l’automatisation que vous pouvez rencontrer en tant qu’administrateur de sauvegarde. Elle fournit également une aide relative à la prise en main.

### <a name="configure-backups"></a>Configurer des sauvegardes

En tant qu’administrateur de sauvegarde, vous devez gérer les nouvelles infrastructures qui sont ajoutées régulièrement et vous assurer qu’elles sont protégées conformément aux exigences convenues. Les clients d’automatisation, tels que PowerShell/CLI, permettent d’extraire tous les détails des machines virtuelles, de vérifier l’état de la sauvegarde de chacune d’entre elles, puis de prendre les mesures appropriées pour les machines virtuelles non protégées.

Toutefois, cela doit être performant à grande échelle. En outre, vous devez les planifier périodiquement et surveiller chaque exécution. Pour faciliter les opérations d’automatisation, Sauvegarde Azure utilise désormais Azure Policy et fournit des [stratégies Azure intégrées spécifiques à la sauvegarde](backup-center-govern-environment.md#azure-policies-for-backup) pour régir le patrimoine de sauvegarde.

Une fois que vous avez affecté une stratégie Azure à une étendue, toutes les machines virtuelles qui répondent à vos critères sont sauvegardées automatiquement, et les machines virtuelles plus récentes sont analysées et protégées régulièrement par la stratégie Azure. Vous pouvez également consulter un rapport de conformité qui vous alerte en cas de ressources non conformes.

[En savoir plus sur les stratégies Azure intégrées pour la sauvegarde](backup-azure-auto-enable-backup.md).

La vidéo suivante illustre le fonctionnement de la stratégie Azure pour la sauvegarde :  <br><br>

> [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Configure-backups-at-scale-using-Azure-Policy/player]

### <a name="export-backup-operational-data"></a>Exporter les données opérationnelles de la sauvegarde

Vous devrez peut-être extraire les données opérationnelles de sauvegarde pour l’ensemble de votre patrimoine et les importer régulièrement dans vos systèmes d’analyse ou tableaux de bord. À grande échelle, les données doivent être récupérées rapidement (lorsque vous interrogez d’énormes enregistrements). Vous devez effectuer des requêtes sur les ressources, les abonnements et les locataires. Vous devez également effectuer des requêtes à partir d’un client (Portail Azure/PowerShell/CLI/n’importe quel Kit de développement logiciel [SDK]/API REST). Le format de sortie doit également être flexible (table ou tableau).

Azure Resource Graph (ARG) vous permet d’effectuer ces opérations et d’effectuer des requêtes à grande échelle. Sauvegarde Azure utilise ARG comme un moyen optimisé d’extraire les données requises avec un minimum de requêtes (une requête pour un scénario). Par exemple, une seule requête permet de récupérer tous les travaux ayant échoué dans tous les coffres de tous les abonnements et de tous les locataires. En outre, les requêtes sont conformes au contrôle d’accès en fonction du rôle (RBAC Azure).

Voir les [exemples de requêtes ARG](query-backups-using-azure-resource-graph.md#sample-queries).

### <a name="automate-responsesactions"></a>Automatiser les réponses/actions

L’automatisation des réponses aux échecs temporaires des travaux de sauvegarde vous permet de vous assurer que vous disposez du nombre approprié de sauvegardes fiables à partir desquelles effectuer la restauration. Cela permet également d’éviter les violations involontaires de votre [objectif de point de récupération](azure-backup-glossary.md#rpo-recovery-point-objective).

Vous pouvez configurer un flux de travail pour retenter les travaux de sauvegarde en utilisant une combinaison de runbooks Azure Automation, de PowerShell et d’Azure Resource Graph. Cela aide dans les scénarios où les travaux de sauvegarde ont échoué en raison d’une erreur temporaire ou d’une panne planifiée/non planifiée.

Pour commencer, suivez ces instructions :

1. Créez un compte d’automatisation et un nouveau runbook PowerShell dans le compte Automation. [Plus d’informations](/azure/automation/learn/powershell-runbook-managed-identity)

2. Insérez le script suivant dans le corps du runbook. 

   Le script exécute une requête ARG pour extraire la liste de toutes les machines virtuelles dont les travaux ont récemment échoué (vous pouvez ajouter un filtre sur _startTime_ à la requête), puis déclenche une sauvegarde à la demande pour chaque machine virtuelle. Vous pouvez créer des requêtes similaires pour extraire la liste de toutes les bases de données SQL et HANA, de tous les fichiers Azure et des autres charges de travail Azure sauvegardées.

    ```azurepowershell
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    $connectionResult = Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $connection.TenantID `
    -ApplicationId $connection.ApplicationID `
    -CertificateThumbprint $connection.CertificateThumbprint
    "Login successful.."

    $query = "RecoveryServicesResources
    | where type in~ ('microsoft.recoveryservices/vaults/backupjobs')
    | extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
    | extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
    | extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
    | extend protectedItemName = split(split(properties.backupInstanceId, 'protectedItems')[1],'/')[1]
    | extend vaultId = tostring(split(id, '/backupJobs')[0])
    | extend vaultSub = tostring( split(id, '/')[2])
    | extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'), startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration 
    | where (dataSourceType in~ ('AzureIaasVM'))
    | where jobStatus=='Failed'
    | where operation == 'backup' or operation == 'adhocBackup'
    | project vaultSub, vaultId, protectedItemName, startTime, endTime, jobStatus, operation
    | sort by vaultSub"

    $subscriptions = Get-AzSubscription | foreach {$_.SubscriptionId}
    $result = Search-AzGraph -Subscription $subscriptions -Query $query -First 5
    $result = $result.data
    $prevsub = ""
    foreach($jobresponse in $result)
    {
                if($jobresponse.vaultSub -ne $prevsub)
                {
                            Set-AzContext -SubscriptionId $jobresponse.vaultSub
                            $prevsub = $jobresponse.vaultSub
                }

                $item = Get-AzRecoveryServicesBackupItem -VaultId $jobresponse.vaultId -BackupManagementType AzureVM -WorkloadType AzureVM -Name $jobresponse.protectedItemName

                Backup-AzRecoveryServicesBackupItem -ExpiryDateTimeUTC (get-date).AddDays(10) -Item $item -VaultId $jobresponse.vaultId
    }
    ```

3. Importez les modules suivants dans le runbook pour vous assurer que le script s’exécute correctement : `Az.Accounts`, `Az.RecoveryServices`, `Az.Graph`.

   [Découvrez comment importer des modules dans un runbook](/azure/automation/shared-resources/modules).

4. [Liez le runbook à une planification](/azure/automation/shared-resources/schedules) pour configurer l’exécution automatique du script à intervalles réguliers.

   Pour une présentation pas à pas et de bout en bout du scénario, reportez-vous à la vidéo suivante : <br><br>

   > [!VIDEO https://channel9.msdn.com/Shows/IT-Ops-Talk/Automatically-retry-failed-backup-jobs-using-Azure-Resource-Graph-and-Azure-Automation-Runbooks/player]

## <a name="supported-automation-methods-by-operation-type"></a>Méthodes d’automatisation prises en charge par type d’opération

| **Charge de travail** | **Catégorie** | **Opération** | **PowerShell** | **INTERFACE DE LIGNE DE COMMANDE** | **REST API** | **Azure Policy** | **Modèle ARM** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure VM | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#back-up-azure-vms) | Prise en charge <br><br> [Voir les exemples](/azure/backup/quick-backup-vm-cli#enable-backup-for-an-azure-vm) | Prise en charge  <br><br>  [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/backup-azure-auto-enable-backup) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/backup-rm-template-samples) |
| Azure VM | Sauvegarde | Sauvegarde sélective des disques | Prise en charge  <br><br> [Voir les exemples](/azure/backup/selective-disk-backup-restore#using-powershell) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/selective-disk-backup-restore#using-azure-cli) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms#excluding-disks-in-azure-vm-backup) | N/A | N/A |
| Azure VM | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge   <br><br> [Voir les exemples](/azure/backup/quick-backup-vm-powershell#start-a-backup-job) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/quick-backup-vm-cli#start-a-backup-job) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms#trigger-an-on-demand-backup-for-a-protected-azure-vm) | N/A | N/A |
| Azure VM | Restaurer | Restaurer des disques dans la région primaire | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#restore-an-azure-vm) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/tutorial-restore-disk#restore-a-vm-disk) | Prise en charge  <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-restoreazurevms) | N/A | N/A |
| Azure VM | Restaurer | Effectuer une restauration interrégion | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#restore-disks-to-a-secondary-region) | Prise en charge   <br><br> [Voir les exemples](/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks&preserve-view=true) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#cross-region-restore) | N/A | N/A |
| Azure VM | Restaurer | Restaurer des disques de manière sélective | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#restore-selective-disks) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/selective-disk-backup-restore#restore-disks-with-azure-cli) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks-selectively) | N/A | N/A |
| Azure VM | Restaurer | Créer une machine virtuelle à partir de disques restaurés | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#using-managed-identity-to-restore-disks) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/tutorial-restore-disk#using-managed-identity-to-restore-disks) | Prise en charge   <br><br> [Voir les exemples](/rest/api/backup/restores/trigge) | N/A | N/A |
| Azure VM | Restaurer | Restaurer des fichiers | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/tutorial-restore-disk#create-a-vm-from-the-restored-disk) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-disks) | N/A | N/A |
| Azure VM | Gérer | Surveiller des travaux | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/tutorial-restore-files) | N/A | N/A |
| Azure VM | Gérer | Modifier la stratégie de sauvegarde | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#monitoring-a-backup-job) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/quick-backup-vm-cli#monitor-the-backup-job) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job) | N/A | N/A |
| Azure VM | Gérer | Arrêter la protection et conserver les données de sauvegarde | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#retain-data) | Prise en charge   <br><br> [Voir les exemples](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-but-retain-existing-data) | N/A | N/A |
| Azure VM | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#delete-backup-data) | Prise en charge   <br><br> [Voir les exemples](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_disable&preserve-view=true) | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms#stop-protection-and-delete-data) | N/A | N/A |
| Azure VM | Gérer | Reprendre la protection | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#resume-backup)    | Prise en charge    <br><br> [Voir les exemples](/cli/azure/backup/protection?view=azure-cli-latest#az_backup_protection_resume&preserve-view=true) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-backupazurevms#undo-the-deletion) | N/A | N/A |
| SQL dans une machine virtuelle Azure | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#configure-a-backup-policy) | Prise en charge | Prise en charge | Pas pour l’instant | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-rm-template-samples) |
| SQL dans une machine virtuelle Azure | Sauvegarde | Activer la protection automatique | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#enable-autoprotection) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#on-demand-backup) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Restaurer | Restaurer sur un point de récupération complet/différentiel distinct | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#original-restore-with-distinct-recovery-point) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Restaurer | Restaurer à un point dans le temps | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#original-restore-with-log-point-in-time) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Restaurer | Effectuer une restauration interrégion | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#alternate-workload-restore-to-a-vault-in-secondary-region) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Surveiller des travaux | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#track-azure-backup-jobs) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Gérer les alertes d’Azure Monitor (préversion) | Prise en charge    <br><br> [Voir les exemples](/azure/azure-monitor/powershell-samples) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Gérer les métriques d’Azure Monitor (préversion) | Prise en charge    <br><br> [Voir les exemples](/azure/azure-monitor/powershell-samples) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Modifier la stratégie de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#change-policy-for-backup-items) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Arrêter la protection et conserver les données de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#retain-data) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#delete-backup-data) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Désinscrire une instance | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#unregister-sql-vm) | Prise en charge | Prise en charge | N/A | N/A |
| SQL dans une machine virtuelle Azure | Gérer | Réinscrire une instance | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-sql-automation#re-register-sql-vms) | Prise en charge | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-backup-cli#register-and-protect-the-sap-hana-instance) | Prise en charge | Actuellement non pris en charge | N/A |
| SAP HANA sur machine virtuelle Azure | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-backup-cli#trigger-an-on-demand-backup) | Prise en charge | N/A | Prise en charge – Exemples   <br><br> [Voir les exemples](/azure/backup/backup-rm-template-samples) |
| SAP HANA sur machine virtuelle Azure | Restaurer | Restaurer sur un point de récupération complet/différentiel/incrémentiel distinct | Prise en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | Prise en charge | N/A |    |
| SAP HANA sur machine virtuelle Azure | Restaurer | Restaurer à un point dans le temps | Prise en charge  | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-restore-cli#restore-a-database) | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Restaurer | Effectuer une restauration interrégion | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Surveiller des travaux | Prise en charge  | Prise en charge | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Modifier la stratégie de sauvegarde | Prise en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-manage-cli#change-policy) | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Arrêter la protection et conserver les données de sauvegarde | Prise en charge  | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-with-retain-data) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge  | Prise en charge    <br><br> [Voir les exemples](/azure/backup/tutorial-sap-hana-manage-cli#stop-protection-without-retain-data) | Prise en charge    <br><br> [Voir les exemples](/rest/api/backup/protected-items/delete) | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Désinscrire une instance | Prise en charge  | Prise en charge | Prise en charge | N/A | N/A |
| SAP HANA sur machine virtuelle Azure | Gérer | Réinscrire une instance | Prise en charge  | Prise en charge | Prise en charge | N/A | NON APPLICABLE |
| Azure Files | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-afs-automation#configure-a-backup-policy) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-afs-cli#enable-backup-for-azure-file-shares) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-file-share-rest-api#configure-backup-for-an-unprotected-azure-file-share-using-rest-api) | Actuellement non pris en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-rm-template-samples) |
| Azure Files | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-afs-automation#trigger-an-on-demand-backup) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-afs-cli#trigger-an-on-demand-backup-for-file-share) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-file-share-rest-api#trigger-an-on-demand-backup-for-file-share) | N/A | NON APPLICABLE |
| Azure Files | Restaurer | Restaurer à l’emplacement d’origine ou à un autre emplacement | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-afs-powershell) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-afs-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-azure-file-share-rest-api) | N/A | NON APPLICABLE |
| Azure Files | Gérer | Surveiller des travaux | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-powershell#track-backup-and-restore-jobs) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-backup-cli#monitor-jobs) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-azure-file-share-rest-api#monitor-jobs) | N/A | NON APPLICABLE |
| Azure Files | Gérer | Modifier la stratégie de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-powershell#modify-the-protection-policy) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-backup-cli#modify-policy) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-azure-file-share-rest-api#modify-policy) | N/A | NON APPLICABLE |
| Azure Files | Gérer | Arrêter la protection et conserver les données de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-powershell#stop-protection-and-retain-recovery-points) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-backup-cli#stop-protection-and-retain-recovery-points) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-azure-file-share-rest-api#stop-protection-but-retain-existing-data) | N/A | NON APPLICABLE |
| Azure Files | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-powershell#stop-protection-without-retaining-recovery-points) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-afs-backup-cli#stop-protection-without-retaining-recovery-points) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/manage-azure-file-share-rest-api#stop-protection-and-delete-data) | N/A | N/A |
| Objets blob Azure | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-blobs-storage-account-ps) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-blobs-storage-account-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-blobs) | Actuellement non pris en charge | Prise en charge |
| Objets blob Azure | Restaurer | Restaurer des objets blob | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-blobs-storage-account-ps) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-blobs-storage-account-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs) | N/A | N/A |
| Objets blob Azure | Gérer | Surveiller des travaux | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-blobs-storage-account-ps#tracking-job) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-blobs-storage-account-cli#tracking-job) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-blobs#tracking-jobs) | N/A | N/A |
| Objets blob Azure | Gérer | Modifier la stratégie de sauvegarde | Actuellement non pris en charge | Actuellement non pris en charge | Actuellement non pris en charge | N/A | N/A |
| Objets blob Azure | Gérer | Arrêter la protection et conserver les données de sauvegarde | Actuellement non pris en charge | Actuellement non pris en charge | Actuellement non pris en charge | N/A | N/A |
| Objets blob Azure | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Objets blob Azure | Gérer | Reprendre la protection | Actuellement non pris en charge | Actuellement non pris en charge | Actuellement non pris en charge | N/A | N/A |
| Disques Azure | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge   <br><br> [Voir les exemples](/azure/backup/backup-managed-disks-ps) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-managed-disks-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-backup-disks) | Actuellement non pris en charge | Prise en charge |
| Disques Azure | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-managed-disks-ps#run-an-on-demand-backup) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-managed-disks-cli#run-an-on-demand-backup) |  N/A | N/A |
| Disques Azure | Restaurer | Restaurer sur un nouveau disque | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-managed-disks-ps) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-managed-disks-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks) | N/A | N/A |
| Disques Azure | Gérer | Surveiller des travaux | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-managed-disks-ps#tracking-job) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/restore-managed-disks-cli#tracking-job) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-restore-disks#track-jobs) | N/A | N/A |
| Disques Azure | Gérer | Modifier la stratégie de sauvegarde | Actuellement non pris en charge | Actuellement non pris en charge | Actuellement non pris en charge | N/A | N/A |
| Disques Azure | Gérer | Arrêter la protection et conserver les données de sauvegarde | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Disques Azure | Gérer | Arrêter la protection et supprimer les données de sauvegarde | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Disques Azure | Gérer | Reprendre la protection | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Sauvegarde | Créer une stratégie de sauvegarde et configurer la sauvegarde | Prise en charge | Prise en charge | Prise en charge | Pas pour l’instant | Prise en charge |
| Serveur Azure Database pour PostgreSQL | Sauvegarde | Exécuter une sauvegarde à la demande | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Restaurer | Restaurer une base de données sur le compte de stockage cible | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Gérer | Modifier la stratégie de sauvegarde | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Gérer | Arrêter la protection et supprimer les données | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Gérer | Arrêter la protection et conserver les données | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Serveur Azure Database pour PostgreSQL | Gérer | Reprendre la protection | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Configurations de niveau coffre | Gérer | Créer un coffre Recovery Services | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-vms-automation#create-a-recovery-services-vault) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/quick-backup-vm-cli#create-a-recovery-services-vault) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-arm-userestapi-createorupdatevault) | N/A | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-rm-template-samples) |
| Configurations de niveau coffre | Gérer | Créer un coffre de sauvegarde | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-blobs-storage-account-ps#create-a-backup-vault) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-blobs-storage-account-cli#create-a-backup-vault) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-dataprotection-use-rest-api-create-update-backup-vault) | N/A | Prise en charge |
| Configurations de niveau coffre | Gérer | Déplacer le coffre Recovery Services | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-move-recovery-services-vault#use-powershell-to-move-recovery-services-vault) | Prise en charge | N/A | N/A |
| Configurations de niveau coffre | Gérer | Déplacer un coffre de sauvegarde | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Configurations de niveau coffre | Gérer | Supprimer un coffre Recovery Services | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-powershell) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-cli) | Prise en charge    <br><br> [Voir les exemples](/azure/backup/backup-azure-delete-vault#delete-the-recovery-services-vault-by-using-azure-resource-manager) | N/A | N/A |
| Configurations de niveau coffre | Gérer | Supprimer un coffre de sauvegarde | Prise en charge | Here | Here | N/A | N/A |
| Configurations de niveau coffre | Gérer | Configurer les paramètres de diagnostic | Prise en charge | Prise en charge | Prise en charge | Prise en charge    <br><br> [Voir les exemples](/azure/backup/azure-policy-configure-diagnostics) | Prise en charge |
| Configurations de niveau coffre | Gérer | Gérer les alertes d’Azure Monitor (préversion) | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Configurations de niveau coffre | Gérer | Gérer les métriques d’Azure Monitor (préversion) | Prise en charge | Prise en charge | Prise en charge | N/A | N/A |
| Configurations de niveau coffre | Sécurité | Activer des points de terminaison privés pour le coffre Recovery Services | Prise en charge | Prise en charge | Prise en charge | Seule la stratégie d’audit est actuellement prise en charge | Prise en charge |
| Configurations de niveau coffre | Sécurité | Activer des clés gérées par le client pour le coffre Recovery Services | Prise en charge | Prise en charge | Prise en charge | Seule la stratégie d’audit est actuellement prise en charge | Prise en charge |
| Configurations de niveau coffre | Sécurité | Activer la suppression réversible pour le coffre Recovery Services | Prise en charge | Prise en charge | Prise en charge | Actuellement non pris en charge | Prise en charge |
| Configurations de niveau coffre | Résilience | Activer la restauration interrégion pour le coffre Recovery Services | Prise en charge | Prise en charge | Prise en charge | Actuellement non pris en charge | Prise en charge |
