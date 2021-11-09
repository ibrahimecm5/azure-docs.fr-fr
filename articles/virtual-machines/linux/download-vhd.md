---
title: Télécharger un disque VHD Linux à partir d’Azure
description: Télécharger un disque VHD Linux à l’aide de l’interface de ligne de commande Azure et du portail Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 393706ca463d8a3cc6edaede2689feae3e867bfa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074495"
---
# <a name="download-a-linux-vhd-from-azure"></a>Télécharger un disque VHD Linux à partir d’Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Dans cet article, vous apprendrez à télécharger un fichier de disque dur virtuel (VHD) Linux à partir d’Azure à l’aide du Portail Azure. 

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un disque VHD associé à une machine virtuelle en cours d’exécution à partir d’Azure. Si vous souhaitez conserver la machine virtuelle en cours d’exécution, vous pouvez [créer un instantané, puis le télécharger](#alternative-snapshot-the-vm-disk).

Pour arrêter la machine virtuelle :

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).
2.  Dans le menu de gauche, sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle dans la liste.
4.  Sur la page de la machine virtuelle, sélectionnez **Arrêter**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Affiche le bouton de menu permettant d’arrêter la machine virtuelle.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternative : Prendre un instantané du disque de machine virtuelle

Prenez un instantané du disque à télécharger.

1. Sélectionnez la machine virtuelle dans le [portail](https://portal.azure.com).
2. Sélectionnez **Disques** dans le menu de gauche, puis sélectionnez le disque dont vous voulez prendre un instantané. Les détails du disque s’affichent.  
3. Sélectionnez **Créer une capture instantanée** dans le menu en haut de la page. La page **Créer une capture instantanée** s’ouvre.
4. Dans **Nom**, saisissez le nom de l’instantané. 
5. Pour **Type d’instantané**, sélectionnez **Complet** ou **Incrémentiel**.
6. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

Votre instantané sera créé sous peu, et pourra ensuite être utilisé pour télécharger ou créer une autre machine virtuelle.

> [!NOTE]
> Si vous n’arrêtez pas d’abord la machine virtuelle, l’instantané ne sera pas propre. L’instantané sera dans le même état que si la machine virtuelle avait été mise hors tension ou s’était bloquée au moment où l’instantané a été réalisé. Bien que cette méthode soit généralement sans danger, elle peut causer des problèmes si les applications en cours d’exécution à ce moment-là ne sont pas résistantes aux pannes.
>  
> Cette méthode est recommandée uniquement pour les machines virtuelles avec un seul disque de système d’exploitation. Les machines virtuelles avec un ou plusieurs disques de données doivent être arrêtées avant le téléchargement ou avant la création d’un instantané du disque du système d’exploitation et de chaque disque de données.

## <a name="generate-sas-url"></a>Générer une URL de SAP

Pour télécharger le fichier VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1. Dans le menu du page pour la machine virtuelle, sélectionnez **Disques**.
2. Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis **Exportation de disque**.
1. Si nécessaire, mettez à jour la valeur de **URL expire dans (secondes)** pour vous fournir suffisamment de temps pour terminer le téléchargement. La valeur par défaut est 3 600 secondes (une heure).
3. Sélectionnez **Générer une URL**.
 
      
## <a name="download-vhd"></a>Télécharger un VHD

1.  Sous l’URL générée, sélectionnez **Télécharger le fichier de disque dur virtuel**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Affiche le bouton permettant de télécharger le disque dur virtuel.":::

2.  Vous devrez peut-être sélectionner **Enregistrer** dans le navigateur pour commencer le téléchargement. Le nom par défaut du fichier VHD est *abcd*.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger et créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI](upload-vhd.md). 
- [Gestion des disques Azure avec l’interface de ligne de commande Azure](tutorial-manage-disks.md).
