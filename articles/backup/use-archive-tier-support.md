---
title: Utilisation du niveau archive
description: Découvrez comment utiliser le niveau archive pour la Sauvegarde Azure.
ms.topic: conceptual
ms.date: 10/23/2021
ms.custom: devx-track-azurepowershell-azurecli
zone_pivot_groups: backup-client-powershelltier-clitier-portaltier
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 9b35cd9269ef75f7c97d576c85004a4fe0be6a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096389"
---
# <a name="use-archive-tier-support"></a>Utilisation de la prise en charge du niveau archive


::: zone pivot="client-powershelltier"

Cet article donne la procédure à suivre pour sauvegarder des points de rétention à long terme dans le niveau archive, ainsi que des instantanés et le niveau standard avec PowerShell.

## <a name="supported-workloads"></a>Charges de travail prises en charge

| Charges de travail | Operations |
| --- | --- |
| Machines virtuelles Azure (préversion)   <br><br>  SQL Server dans des machines virtuelles Azure   | <ul><li>Afficher les points de récupération archivables    </li><li>Afficher les points de récupération recommandés (uniquement pour les machines virtuelles)  </li><li>Déplacer les points de récupération archivables   </li><li>Déplacer les points de récupération recommandés (uniquement pour les machines virtuelles Azure)   </li><li>Afficher les points de récupération archivés   </li><li>Effectuer une restauration à partir de points de récupération archivés   </li></ul> |

## <a name="get-started"></a>Bien démarrer

1. Téléchargez la [dernière](https://github.com/PowerShell/PowerShell/releases) version de PowerShell à partir de GitHub.

1. Exécutez la cmdlet suivante dans PowerShell :
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. Connectez-vous à Azure à l’aide la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Connectez-vous à votre abonnement :

   ```azurepowershell
   Set-AzContext -Subscription "SubscriptionName"
   ```

1. Obtenez le coffre :

    ```azurepowershell
    $vault = Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"
    ```

1. Obtenez la liste des éléments de sauvegarde :

   - **Pour les Machines virtuelles Azure :**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"
       ```

   - **Pour SQL Server dans les Machines virtuelles Azure :**

       ```azurepowershell
       $BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"
       ```

1. Obtenez l’élément de sauvegarde.

   - **Pour les Machines virtuelles Azure :**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}
     ```

   - **Pour SQL Server dans les Machines virtuelles Azure :**

     ```azurepowershell
     $bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}
     ```

1. (Facultatif) Ajoutez la plage de dates pour laquelle vous souhaitez afficher les points de récupération. Par exemple, si vous voulez voir ceux des 120 derniers jours, utilisez la cmdlet suivante :

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-120)
    $endDate = (Get-Date).AddDays(0) 
   ```
   >[!NOTE]
   >Si vous voulez afficher les points de récupération pour un autre intervalle de temps, modifiez les dates de début et de fin en conséquence. <br><br> Il s’agit par défaut des 30 derniers jours.

## <a name="check-the-archivable-status-of-all-recovery-points"></a>Vérification de la capacité d’archivage de tous les points de récupération

Vous pouvez maintenant vérifier la capacité d’archivage de tous les points de récupération d’un élément de sauvegarde avec la cmdlet suivante :

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

## <a name="check-archivable-recovery-points"></a>Vérifier les points de récupération archivables

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

Cette cmdlet répertorie tous les points de récupération associés à un élément de sauvegarde spécifique qui sont prêts à être déplacés vers le niveau archive (entre la date de début et la date de fin). Vous pouvez également modifier les dates de début et de fin.

## <a name="check-why-a-recovery-point-cant-be-moved-to-archive"></a>Vérification des raisons pour lesquelles un point de récupération n’est pas déplaçable vers le niveau archive

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Où `$rp[0]` est le point de récupération dont vous souhaitez savoir pourquoi il n’est pas archivable.

**Exemple de sortie**

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Vérifier l’ensemble recommandé de points archivables (uniquement pour les machines virtuelles Azure)

Les points de récupération associés à une machine virtuelle sont incrémentiels. Quand vous déplacez un point de récupération vers le niveau archive, il est au préalable converti en sauvegarde complète. Les économies associées au déplacement vers le niveau archive dépendent donc de l’attrition de la source de données.

Par conséquent, la Sauvegarde Azure fournit un ensemble recommandé de points de récupération qui peuvent réduire les coûts, s’ils sont déplacés conjointement.

>[!NOTE]
>- Les économies dépendent de divers facteurs et ne sont pas forcément les mêmes pour toutes les instances.
>- Les économies ne sont assurées que lorsque tous les points de récupération contenus dans l’ensemble de recommandations sont déplacés vers le niveau archive du coffre.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

## <a name="move-to-archive"></a>Déplacer une archive

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Où `$rp[0]` est le premier point de récupération dans la liste. Si vous souhaitez déplacer d’autres points de récupération, utilisez les commandes `$rp[1]`, `$rp[2]`, et ainsi de suite.

Cette cmdlet déplace un point de récupération archivable vers le niveau archive. Elle retourne un travail utilisable pour effectuer le suivi de l’opération de déplacement, que ce soit sur le portail ou avec PowerShell.

## <a name="view-archived-recovery-points"></a>Afficher les points de récupération archivés

Cette cmdlet retourne tous les points de récupération archivés.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-with-powershell"></a>Restaurer avec PowerShell

Pour les points de récupération dans l’archive, le service Sauvegarde Azure fournit une méthodologie de restauration intégrée.
La restauration intégrée est un processus en deux étapes.

1. Elle implique de réactiver les points de récupération stockés dans le niveau archive.
2. Stockez-le temporairement dans le niveau standard du coffre pour une durée (également appelée durée de réactivation) pouvant aller de 10 à 30 jours. La durée par défaut est de 15 jours. Il existe deux priorités de réalimentation : Standard et Haute. Apprenez-rn davantage sur la [priorité de réalimentation](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority).

>[!NOTE]
>
>- Une fois sélectionnée, la durée de réactivation n’est pas modifiable. Les points de récupération réactivés restent dans le niveau standard pendant toute la durée de la réactivation.
>- L’étape supplémentaire de réalimentation entraîne un coût.

Pour plus d’informations sur les différentes méthodes de restauration pour les machines virtuelles Azure, consultez [Restaurer une machine virtuelle Azure avec PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Pour restaurer SQL Server, procédez [comme suit](backup-azure-sql-automation.md#restore-sql-dbs). La cmdlet `Restore-AzRecoveryServicesBackupItem` exige deux paramètres supplémentaires, `RehydrationDuration` et `RehydrationPriority`.

## <a name="view-jobs"></a>Afficher les travaux

Pour afficher les travaux de déplacement et de restauration, utilisez la cmdlet PowerShell suivante :

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="move-recovery-points-to-archive-tier-at-scale"></a>Déplacer des points de récupération vers le niveau archive à grande échelle

Vous pouvez maintenant utiliser des exemples de scripts pour effectuer des opérations à l’échelle. [En savoir plus](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md) sur la procédure d’exécution des exemples de scripts. Vous pouvez télécharger les scripts [ici](https://github.com/hiaga/Az.RecoveryServices).

Vous pouvez effectuer les opérations suivantes à l’aide des exemples de scripts fournis par Sauvegarde Azure :

- Déplacez tous les points de récupération admissibles pour une base de données spécifique ou pour toutes les bases de données d’un serveur SQL dans une machine virtuelle Azure vers le niveau archive.
- Déplacez tous les points de récupération recommandés pour une machine virtuelle Azure particulière vers le niveau archive.
 
Vous pouvez également écrire un script en fonction de vos besoins ou modifier les exemples de scripts ci-dessus pour récupérer (fetch) les éléments de sauvegarde requis.

::: zone-end



::: zone pivot="client-clitier"

Cet article donne la procédure à suivre pour sauvegarder des points de rétention à long terme dans le niveau archive, ainsi que des instantanés et le niveau standard avec l’interface de ligne de commande (CLI, Command-Line Interface).

## <a name="supported-workloads"></a>Charges de travail prises en charge

| Charges de travail | Operations |
| --- | --- |
| Machines virtuelles Azure (préversion)   <br><br>  SQL Server dans des machines virtuelles Azure   <br><br>   SAP HANA dans les Machines virtuelles Azure | <ul><li> Afficher les points de récupération archivables       </li><li>Afficher les points de récupération recommandés (uniquement pour les machines virtuelles)    </li><li>Déplacer les points de récupération archivables    </li><li>Déplacer les points de récupération recommandés (uniquement pour les machines virtuelles Azure)    </li><li>Afficher les points de récupération archivés    </li><li>Effectuer une restauration à partir de points de récupération archivés </li></ul> |


## <a name="get-started"></a>Bien démarrer

1. Téléchargez/mettez à niveau AZ CLI vers la version 2.26.0 ou une version ultérieure. 

   1. Pour installer l’interface CLI la première fois, suivez les [instructions](/cli/azure/install-azure-cli).
   1. Pour mettre à niveau une version déjà installée, exécutez az --upgrade.

2. Connectez-vous avec la commande suivante :

   ```azurecli
   az login
   ```

3. Définissez le contexte de l’abonnement :

   ```azurecli
   az account set –s <subscriptionId>
   ```
## <a name="view-archivable-recovery-points"></a>Affichage des points de récupération archivables

Vous pouvez déplacer les points de récupération archivables vers le niveau archive du coffre avec les commandes suivantes. [En savoir plus](archive-tier-support.md#supported-workloads) sur les critères d’admissibilité.

- **Pour les Machines virtuelles Azure :**

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureIaasVM} --workload-type {VM}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Pour SQL Server dans les Machines virtuelles Azure :

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

- Pour SAP HANA dans les Machines virtuelles Azure :

  ```azurecli
  az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA}  --target-tier {VaultArchive} --is-ready-for-move {True}
  ```

## <a name="check-why-a-recovery-point-isnt-archivable"></a>Vérification des raisons pour lesquelles un point de récupération n’est pas archivable

Exécutez la commande suivante :

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload / AzureIaasVM} --workload-type {MSSQL / SAPHANA / VM}  --query [].{Name:name,move_ready:properties.recoveryPointMoveReadinessInfo.ArchivedRP.isReadyForMove,additional_details: properties.recoveryPointMoveReadinessInfo.ArchivedRP.additionalInfo
```

Vous obtiendrez une liste de tous les points de récupération, leur capacité d’archivage et la raison pour laquelle ils ne sont pas archivables, le cas échéant.

## <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Vérifier l’ensemble recommandé de points archivables (uniquement pour les machines virtuelles Azure)

Exécutez la commande suivante :

```azurecli
az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --recommended-for-archive
```

[En savoir plus](archive-tier-support.md#archive-recommendations-only-for-azure-virtual-machines) sur l’ensemble de recommandations.

>[!Note]
>- Les économies dépendent de divers facteurs. Elles ne sont pas forcément les mêmes pour toutes les instances.
>- Les économies ne sont assurées que lorsque tous les points de récupération contenus dans l’ensemble de recommandations sont déplacés vers le niveau archive du coffre.

## <a name="move-to-archive"></a>Déplacer une archive

Vous pouvez déplacer les points de récupération archivables vers le niveau archive du coffre avec les commandes suivantes. Le paramètre name de la commande doit contenir le nom d’un point de récupération archivable.

- **Pour les Machines virtuelles Azure :**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type { AzureIaasVM} --workload-type {VM} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Pour SQL Server dans les Machines virtuelles Azure :**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```
- **Pour SAP HANA dans les Machines virtuelles Azure :**

  ```azurecli
  az backup recoverypoint move -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} --source-tier {VaultStandard} --destination-tier {VaultArchive} --name {rp}
  ```

## <a name="view-archived-recovery-points"></a>Afficher les points de récupération archivés

Utilisez les commandes suivantes :

- **Pour les Machines virtuelles Azure :**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload } --workload-type {VM} -- tier {VaultArchive}
    ```
- **Pour SQL Server dans les Machines virtuelles Azure :**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {MSSQL} -- tier {VaultArchive}
    ```
- **Pour SAP HANA dans les Machines virtuelles Azure :**

    ```azurecli
    az backup recoverypoint list -g {rg} -v {vault} -c {container} -i {item} --backup-management-type {AzureWorkload} --workload-type {SAPHANA} -- tier {VaultArchive}
    ```

## <a name="restore"></a>Restaurer 

Exécutez les commandes suivantes :

- **Pour les Machines virtuelles Azure :**

    ```azurecli
    az backup restore restore-disks -g {rg} -v {vault} -c {container} -i {item} --rp-name {rp} --storage-account {storage_account} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

- **Pour SQL Server dans les Machines virtuelles Azure/SAP HANA dans les Machines virtuelles Azure :**

    ```azurecli
    az backup recoveryconfig show --resource-group saphanaResourceGroup \
        --vault-name saphanaVault \
        --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
        --item-name saphanadatabase;hxe;hxe \
        --restore-mode AlternateWorkloadRestore \
        --rp-name 7660777527047692711 \
        --target-item-name restored_database \
        --target-server-name hxehost \
        --target-server-type HANAInstance \
        --workload-type SAPHANA \
        --output json


    az backup restore restore-azurewl -g {rg} -v {vault} --recovery-config {recov_config} --rehydration-priority {Standard / High} --rehydration-duration {rehyd_dur}
    ```

::: zone-end



::: zone pivot="client-portaltier"

Cet article donne la procédure à suivre pour sauvegarder des points de rétention à long terme dans le niveau archive, ainsi que des instantanés et le niveau standard avec le Portail Azure.

## <a name="supported-workloads"></a>Charges de travail prises en charge

| Charges de travail | Operations |
| --- | --- |
| Machine virtuelle Azure | <ul><li>Afficher les points de récupération archivés    </li><li>Restaurer les points de récupération archivés   </li><li>Afficher les travaux de déplacement vers le niveau archive et de restauration </li></ul> |
| SQL Server dans les Machines virtuelles Azure/ <br> SAP HANA dans les Machines virtuelles Azure | <ul><li>Afficher les points de récupération archivés    </li><li>Déplacer toute la récupération archivable vers le niveau archive   </li><li>Effectuer une restauration à partir de points de récupération archivés   </li><li>Afficher les travaux de déplacement vers le niveau archive et de restauration</li></ul> |

## <a name="view-archived-recovery-points"></a>Afficher les points de récupération archivés

Vous pouvez maintenant afficher tous les points de récupération déplacés vers le niveau archive.

:::image type="content" source="./media/use-archive-tier-support/view-recovery-points-list-inline.png" alt-text="Capture d’écran montrant la liste des points de récupération." lightbox="./media/use-archive-tier-support/view-recovery-points-list-expanded.png":::


## <a name="move-archivable-recovery-points-for-a-particular-sqlsap-hana-database"></a>Déplacement des points de récupération archivables d’une base de données SQL/SAP HANA en particulier

Vous pouvez maintenant déplacer en une seule fois tous les points de récupération d’une base de données SQL/SAP HANA en particulier.

Procédez comme suit :

1. Sélectionnez l’élément de sauvegarde (base de données dans SQL Server ou SAP HANA dans les Machines virtuelles Azure) dont vous souhaitez déplacer les points de récupération vers le niveau archive du coffre.

2. Sélectionnez **cliquez ici** pour afficher les points de récupération datant de plus de 7 jours.

   :::image type="content" source="./media/use-archive-tier-support/view-old-recovery-points-inline.png" alt-text="Capture d’écran montrant le processus d’affichage des points de récupération datant de plus de 7 jours." lightbox="./media/use-archive-tier-support/view-old-recovery-points-expanded.png":::

3. Pour afficher tous les points archivables (admissibles au déplacement vers le niveau archive), sélectionnez _Les points de rétention à long terme peuvent être déplacés vers le niveau archive. Pour déplacer « tous les points de récupération éligibles » vers le niveau archive, cliquez ici_.

   :::image type="content" source="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-inline.png" alt-text="Capture d’écran montrant le processus d’affichage de tous les points archivables (admissibles au déplacement vers le niveau archive)." lightbox="./media/use-archive-tier-support/view-all-eligible-archivable-points-for-move-expanded.png":::

   Tous les points de récupération archivables s’affichent.


   [En savoir plus](archive-tier-support.md#supported-workloads) sur les critères d’admissibilité.

3. Cliquez sur **Déplacer les points de récupération vers le niveau archive** pour déplacer tous les points de récupération vers le niveau archive du coffre.

   :::image type="content" source="./media/use-archive-tier-support/move-all-recovery-points-to-vault-inline.png" alt-text="Capture d’écran montrant l’option permettant de lancer le processus de déplacement de tous les points de récupération vers le niveau archive du coffre." lightbox="./media/use-archive-tier-support/move-all-recovery-points-to-vault-expanded.png":::

   >[!Note]
   >Cette option déplace tous les points de récupération archivables vers le niveau archive du coffre.

Vous pouvez suivre la progression dans les travaux de sauvegarde.

## <a name="restore"></a>Restaurer

Pour restaurer les points de récupération déplacés vers le niveau archive, vous devez ajouter les paramètres requis de durée et de priorité de réactivation.

:::image type="content" source="./media/use-archive-tier-support/restore-in-portal.png" alt-text="Capture d’écran montrant le processus de restauration des points de récupération sur le portail.":::

## <a name="view-jobs"></a>Afficher les travaux

:::image type="content" source="./media/use-archive-tier-support/view-jobs-portal.png" alt-text="Capture d’écran montrant le processus d’affichage des travaux sur le portail.":::

## <a name="view-archive-usage-in-vault-dashboard"></a>Affichage de l’utilisation de l’archive dans le tableau de bord du coffre

Vous pouvez également afficher l’utilisation de l’archive dans le tableau de bord du coffre.

:::image type="content" source="./media/use-archive-tier-support/view-archive-usage-in-vault-dashboard.png" alt-text="Capture d’écran montrant l’utilisation de l’archive dans le tableau de bord du coffre.":::


::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des erreurs du niveau archive](troubleshoot-archive-tier.md)
