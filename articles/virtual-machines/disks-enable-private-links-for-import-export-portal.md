---
title: Portail Azure - Restreindre l’accès à l’importation/exportation de disques managés
description: Activez Private Link pour vos disques managés avec le portail Azure. Cela vous permet d’exporter et d’importer des disques de manière sécurisée dans votre réseau virtuel.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/03/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 57eef48a188f07847efcda31b4cd6a6043f2ae2a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491010"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>Restreindre l’accès à l’importation/exportation de disques managés à l’aide d’Azure Private Link

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Vous pouvez utiliser des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour restreindre l’exportation et l’importation de disques managés, et accéder de manière plus sécurisée aux données via une [liaison privée](../private-link/private-link-overview.md) à partir des clients de votre réseau virtuel Azure. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour vos disques managés. Le trafic réseau entre clients sur leurs réseau virtuel et disques managés transite uniquement sur le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, éliminant ainsi toute exposition à partir de l’Internet public.

Pour utiliser Private Link afin d’exporter et d’importer des disques managés, vous devez d’abord créer une ressource d’accès au disque et la lier à un réseau virtuel dans le même abonnement en créant un point de terminaison privé. Associez ensuite un disque ou une capture instantanée à une instance d’accès au disque.

## <a name="limitations"></a>Limites

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>Créer une ressource d’accès au disque

1. Connectez-vous au portail Azure, puis accédez à **Accès de disque** via [ce lien](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Vous devez utiliser le [lien fourni](https://aka.ms/disksprivatelinks) pour accéder au volet Accès de disque. Celui-ci n’est pas actuellement visible dans le portail public.

1. Sélectionnez **+ Créer** pour créer une ressource d’accès au disque.
1. Dans le volet **Créer un accès de disque**, sélectionnez votre abonnement et un groupe de ressources. Sous **Détails de l’instance**, entrez un nom, puis sélectionnez une région.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Capture d’écran du volet de création d’accès au disque. Indiquez le nom souhaité, sélectionnez une région, sélectionnez un groupe de ressources, puis continuez":::

1. Sélectionnez **Revoir + créer**.
1. Une fois votre ressource créée, accédez directement à celle-ci.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Capture d’écran du bouton Accéder à la ressource dans le portail":::

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Vous devez ensuite créer un point de terminaison privé et le configurer pour l’accès au disque.

1. Dans votre ressource d’accès au disque, sous **Paramètres**, sélectionnez **Connexions de point de terminaison privé**.
1. Sélectionnez **+ Point de terminaison privé**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Capture d’écran du volet de vue d’ensemble de la ressource d’accès au disque. Les connexions de point de terminaison privé sont mises en évidence.":::

1. Dans le volet **Créer un point de terminaison privé**, sélectionnez un groupe de ressources.
1. Indiquez un nom, puis sélectionnez la même région que celle dans laquelle votre ressource d’accès au disque a été créée.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Capture d’écran du workflow de création de point de terminaison privé, premier volet. Si vous ne sélectionnez pas la région appropriée, vous risquez de rencontrer des problèmes plus tard.":::

1. Sélectionnez **Suivant : Ressource**.
1. Dans le volet **Ressource**, sélectionnez **Se connecter à une ressource Azure dans mon annuaire**.
1. Pour **Type de ressource**, sélectionnez **Microsoft.Compute/diskAccesses**.
1. Pour **Ressource**, sélectionnez la ressource d’accès au disque que vous avez créée.
1. Pour **Sous-ressource cible**, laissez **disques**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Capture d’écran du workflow de création d’un point de terminaison privé, deuxième volet. Toutes les valeurs sont mises en évidence (Type de ressource, Ressource, Sous-ressource cible)":::

1. Sélectionnez **Suivant : Configuration**.
1. Sélectionnez le réseau virtuel pour lequel vous souhaitez limiter l’importation et l’exportation de disques. Cela empêche l’importation et l’exportation de votre disque vers d’autres réseaux virtuels.

    > [!NOTE]
    > Si un groupe de sécurité réseau est activé pour le sous-réseau sélectionné, il est désactivé pour les points de terminaison privés sur ce sous-réseau uniquement. Les autres ressources de ce sous-réseau conservent l’application du groupe de sécurité réseau.

1. Sélectionnez le sous-réseau approprié.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Capture d’écran du workflow de création d’un point de terminaison privé, troisième volet. Les options Réseau virtuel et Sous-réseau sont mises en évidence.":::

1. Sélectionnez **Revoir + créer**.

## <a name="enable-private-endpoint-on-your-disk"></a>Activer un point de terminaison privé sur votre disque

1. Accédez au disque que vous souhaitez configurer.
1. Sous **Paramètres**, sélectionnez **Mise en réseau**.
1. Sélectionnez **Point de terminaison privé (via l’accès de disque)** et sélectionnez l’accès au disque créé précédemment.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Capture d’écran du volet Réseau pour l’accès au disque managé. La sélection du point de terminaison privé et de l’accès au disque sont mises en évidence. Enregistrez pour configurer votre disque avec cet accès.":::

1. Sélectionnez **Enregistrer**.

Vous avez à présent configuré une liaison privée qui vous permet d’importer et d’exporter votre disque managé.

## <a name="next-steps"></a>Étapes suivantes

- Charger un disque dur virtuel dans Azure ou copier un disque managé dans une autre région - Module [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) ou [Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- Télécharger un disque dur virtuel - [Windows](windows/download-vhd.md) ou [Linux](linux/download-vhd.md)
- [Questions fréquentes (FAQ) sur les liaisons privées et les disques managés](./faq-for-disks.yml)
- [Exporter/copier des instantanés managés en tant que disque dur virtuel vers un compte de stockage dans une région différente avec PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)
