---
title: Monter un partage de fichiers Azure NFS (préversion) - Azure Files
description: Apprenez à monter un partage NFS (Network File System).
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 04d3174900ce5aa788f3793b39fa4d6c06222001
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036786"
---
# <a name="how-to-mount-an-nfs-file-share-preview"></a>Comment monter un partage de fichiers NFS (préversion)

[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l'aide du protocole SMB (Server Message Block) ou du protocole NFS (Network File System) (préversion). Cet article est consacré au montage avec NFS. Pour en savoir plus sur le montage avec SMB, consultez [Utiliser Azure Files avec Linux](storage-how-to-use-files-linux.md). Pour plus d'informations sur chacun des protocoles disponibles, consultez [Protocoles de partage de fichiers Azure](storage-files-planning.md#available-protocols).

## <a name="limitations"></a>Limites

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilité régionale

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prérequis

- [Créez un partage NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > Les partages NFS ne sont accessibles qu'à partir de réseaux approuvés. Les connexions à votre partage NFS doivent provenir de l'une des sources suivantes :

- Utilisez l'une des solutions de mise en réseau suivantes :
    - [Créez un point de terminaison privé](storage-files-networking-endpoints.md#create-a-private-endpoint) (recommandé) ou [restreignez l'accès à votre point de terminaison public](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Configurer un VPN site à site pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md).
    - Configurez [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Désactiver le transfert sécurisé

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez au compte de stockage contenant le partage NFS que vous avez créé.
1. Sélectionnez **Configuration**.
1. Sous **Transfert sécurisé requis**, sélectionnez **Désactivé**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="Capture de l'écran de configuration du compte de stockage, sur lequel le transfert sécurisé est désactivé. " lightbox="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png":::

## <a name="mount-an-nfs-share"></a>Monter un partage NFS

1. Une fois le partage de fichiers créé, sélectionnez-le et choisissez **Se connecter à partir de Linux**.
1. Entrez le chemin de montage que vous souhaitez utiliser, puis copiez le script.
1. Connectez-vous à votre client et utilisez le script de montage fourni.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Capture d’écran du panneau de connexion du partage de fichiers.":::

Votre partage NFS est désormais monté.

### <a name="validate-connectivity"></a>Valider la connectivité

En cas d’échec de votre montage, il est possible que votre point de terminaison privé n’ait pas été correctement configuré ou qu’il soit inaccessible. Pour plus d’informations sur la vérification de la connectivité, consultez la section [Vérifier la connectivité](storage-files-networking-endpoints.md#verify-connectivity) de l’article relatif aux points de terminaison de mise en réseau.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d'informations sur Azure Files, consultez notre article [Planifier un déploiement Azure Files](storage-files-planning.md).
- Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md).