---
title: Problèmes connus avec NFS 3.0 dans le Stockage Blob Azure
description: Découvrez les limitations et les problèmes connus de la prise en charge du protocole NFS 3.0 dans le Stockage Blob Azure.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 13808d38a4ddb7e00af64b02df390eee1e1f9469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421966"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Problèmes connus de la prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure

Cet article décrit les limitations et les problèmes connus de la prise en charge du protocole NFS 3.0 dans le Stockage Blob Azure.

> [!IMPORTANT]
> Étant donné que vous devez activer la fonctionnalité d’espace de noms hiérarchique de votre compte pour utiliser NFS 3.0, tous les problèmes connus décrits dans l’article [Problèmes connus avec Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md) sont également valables pour votre compte.

## <a name="nfs-30-support"></a>Prise en charge NFS 3.0

- La prise en charge NFS 3.0 ne peut pas être activée sur les comptes de stockage existants.

- Une fois activée, la prise en charge NFS 3.0 ne peut pas être désactivée dans un compte de stockage.

- Les options de redondance GRS, GZRS et RA-GRS ne sont pas prises en charge lorsque vous créez un compte de stockage NFS 3.0.

## <a name="nfs-30-features"></a>Fonctionnalités NFS 3.0

Les fonctionnalités NFS 3.0 suivantes ne sont pas encore prises en charge.

- NFS 3.0 sur UDP. Seul NFS 3.0 sur TCP est pris en charge.

- Verrouillage des fichiers avec NLM (Network Lock Manager). Les commandes de montage doivent inclure le paramètre `-o nolock`.

- Montage des sous-répertoires. Vous pouvez monter uniquement le répertoire racine (conteneur).

- Répertorier les montages (par exemple : à l’aide de la commande `showmount -a`)

- Répertorier les exportations (par exemple : à l’aide de la commande `showmount -e`)

- Lien physique

- Exportation d’un conteneur en lecture seule

## <a name="nfs-30-clients"></a>Clients NFS 3.0

Le client Windows pour NFS n’est pas encore pris en charge

## <a name="blob-storage-features"></a>Fonctionnalités du Stockage Blob

Lorsque vous activez la prise en charge du protocole NFS 3.0, certaines fonctionnalités du Stockage Blob sont entièrement prises en charge, mais d’autres peuvent ne l’être qu’au niveau de préversion ou ne pas être prises en charge du tout.

Pour voir comment chaque fonctionnalité de Stockage Blob est prise en charge dans des comptes dont le protocole NFS 3.0 est activé, consultez [Prise en charge des fonctionnalités de Stockage Blob dans les comptes Stockage Azure](storage-feature-support-in-storage-accounts.md).

## <a name="see-also"></a>Voir aussi

- [Prise en charge du protocole NFS 3.0 dans le service Stockage Blob Azure](network-file-system-protocol-support.md)
- [Monter le stockage Blob à l’aide du protocole NFS (Network File System) 3.0](network-file-system-protocol-support-how-to.md)
