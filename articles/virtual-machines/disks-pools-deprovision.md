---
title: Annuler l’approvisionnement d’un pool de disques Azure (préversion)
description: Découvrez comment annuler l’approvisionnement d’un pool de disques Azure, l’arrêter et le supprimer.
author: roygara
ms.date: 11/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 36c301cb5575d7627a6179520aef79c01f8cd837
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022129"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Annuler l’approvisionnement d’un pool de disques Azure (préversion)

Cet article décrit le processus de suppression d’un pool de disques Azure (préversion) et explique comment désactiver la prise en charge iSCSI.

## <a name="stop-a-disk-pool"></a>Arrêter un pool de disques

Vous pouvez arrêter un pool de disques pour réduire les coûts et conserver toutes les configurations. Lorsqu’un pool de disques est arrêté, vous ne pouvez plus vous y connecter via iSCSI. Les ressources managées déployées pour prendre en charge le pool de disques ne seront pas supprimées. Vous devez d’abord déconnecter tous les clients ayant des connexions iSCSI au pool de disques avant d’arrêter un pool de disques. Vous pouvez démarrer un pool de disques à tout moment. Cette opération réactivera la cible iSCSI exposée sur ce pool de disques.
# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre pool de disques, puis sélectionnez **Arrêter**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name '<yourDiskPool>' -ResourceGroupName '<yourResourceGroup>'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool stop --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```
---

## <a name="disable-iscsi-support"></a>Désactiver la prise en charge iSCSI

Si vous désactivez la prise en charge iSCSI sur un pool de disques, vous ne pouvez plus utiliser ce pool.

Lorsque vous activez pour la première fois la prise en charge iSCSI sur un pool de disques, une cible iSCSI est créée en tant que point de terminaison de la connexion iSCSI. Vous pouvez désactiver la prise en charge iSCSI sur le pool de disques en supprimant la cible iSCSI. Chaque pool de disques ne peut avoir qu’une seule cible iSCSI configurée.

Vous pouvez réactiver la prise en charge iSCSI sur un pool de disques existant. La prise en charge iSCSI ne peut pas être désactivée sur le pool de disques si des connexions iSCSI au pool de disques sont en attente.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Recherchez **Pool de disques** et sélectionnez votre pool de disques.
1. Sous **Paramètres**, sélectionnez **iSCSI**.
1. Décochez la case **Activer iSCSI** et sélectionnez **Enregistrer**.    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "<yourDiskpoolName>" -Name "<youriSCSITargetName>" -ResourceGroupName "yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "<yourDiskPool>" --name "<yourIscsiTarget>" --resource-group "<yourResourceGroup>"
```
---

## <a name="delete-a-disk-pool"></a>Supprimer un pool de disques

Quand vous supprimez un pool de disques, toutes les ressources du groupe de ressources managé sont également supprimées. Si des connexions iSCSI au pool de disques sont en attente, vous ne pouvez pas supprimer le pool de disques. Vous devez d’abord déconnecter tous les clients ayant des connexions iSCSI au pool de disques avant d’arrêter un pool de disques. Les disques qui ont été ajoutés au pool de disques ne seront pas supprimés.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez et sélectionnez **Pool de disques**, puis sélectionnez le pool de disques à supprimer.
1. En haut de la page, sélectionnez **Supprimer**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>"

Remove-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>
Remove-AzDiskPool -Name "<yourDiskpoolName>" -ResourceGroupName "<yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool delete --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```

---

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [disques gérés Azure](managed-disks-overview.md).
