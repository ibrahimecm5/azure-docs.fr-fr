---
title: Partages de fichiers NFS dans Azure Files
description: Découvrez les partages de fichiers hébergés dans Azure Files avec le protocole NFS (Network File System).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 31aec4e32178b2f0ca7905c6d584df298dd2059a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524338"
---
# <a name="nfs-file-shares-in-azure-files"></a>Partages de fichiers NFS dans Azure Files
Azure Files propose deux protocoles standard pour le montage d’un partage de fichiers Azure : le protocole [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) et le protocole [NFS](https://en.wikipedia.org/wiki/Network_File_System). Azure Files vous permet de choisir le protocole de système de fichiers le mieux adapté à votre charge de travail. Les partages de fichiers Azure ne prennent pas en charge l’accès à un partage de fichiers Azure individuel avec les protocoles SMB et NFS, même si vous pouvez créer des partages de fichiers SMB et NFS au sein du même compte de stockage. Pour tous les partages de fichiers, Azure Files propose des partages de fichiers d’entreprise qui peuvent être mis à l’échelle pour répondre à vos besoins de stockage et accessibles simultanément par des milliers de clients.

Cet article traite des partages de fichiers Azure NFS. Pour plus d’informations sur les partages de fichiers Azure SMB, consultez [Partages de fichiers SMB dans Azure Files](files-smb-protocol.md).

> [!IMPORTANT]
>  Pour obtenir la liste des partages de fichiers NFS pour la production, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md).

## <a name="common-scenarios"></a>Scénarios courants
Les partages de fichiers NFS sont souvent utilisés dans les scénarios suivants :

- Stockage d’applications Linux/UNIX, notamment des applications métier écrites à l’aide des API du système de fichiers Linux ou POSIX (même si elles n’ont pas besoin d’être compatibles avec POSIX).
- Des charges de travail qui requièrent des partages de fichiers compatibles POSIX, le respect de la casse ou des autorisations de style UNIX (UID/GID).
- Nouveau développement d’applications et de services, en particulier si cette application ou ce service est obligatoire pour les E/S aléatoires et le stockage hiérarchique. 

## <a name="features"></a>Fonctionnalités
- Système de fichiers entièrement compatible POSIX.
- Prise en charge des liens physiques.
- Prise en charge des liens symboliques.
- Les partages de fichiers NFS ne prennent actuellement en charge que la plupart des fonctionnalités de la [spécification de protocole 4.1](https://tools.ietf.org/html/rfc5661). Certaines fonctionnalités, comme les délégations et les rappels de toutes sortes, l’authentification Kerberos et le chiffrement en transit, ne sont pas prises en charge.


## <a name="security-and-networking"></a>Sécurité et mise en réseau
Toutes les données stockées dans Azure Files sont chiffrées au repos avec Azure Storage Service Encryption (SSE). Cette fonctionnalité fonctionne de la même façon que BitLocker sur Windows : les données sont chiffrées sous le niveau du système de fichiers. Comme les données sont chiffrées sous le système de fichiers du partage de fichiers Azure, car codées sur le disque, il n’est pas nécessaire d’avoir accès à la clé sous-jacente sur le client pour lire ou écrire sur le partage de fichiers Azure. Le chiffrement au repos s’applique aux protocoles SMB et NFS.

Pour un chiffrement en transit, Azure fournit une couche de chiffrement pour toutes les données en transit entre les centres de données Azure en utilisant [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). De ce fait, un chiffrement est appliqué lorsque les données sont transférées entre des centres de données Azure. Contrairement à Azure Files qui utilise le protocole SMB, les partages de fichiers utilisant le protocole NFS n’offrent pas l’authentification basée sur l’utilisateur. L’authentification pour les partages NFS est basée sur les règles de sécurité réseau configurées. Par conséquent, pour vous assurer que seules des connexions sécurisées sont établies avec votre partage NFS, vous devez utiliser des points de terminaison de service ou des points de terminaison privés. Pour accéder aux partages à partir d’un emplacement local, en plus d’un point de terminaison privé, vous devez configurer un réseau VPN ou ExpressRoute. Les demandes qui ne proviennent pas des sources suivantes sont rejetées :

- [Point de terminaison privé](storage-files-networking-overview.md#private-endpoints)
- [Passerelle VPN Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Réseau VPN de point à site (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [De site à site](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Point de terminaison public restreint](storage-files-networking-overview.md#storage-account-firewall-settings)

Pour plus d’informations sur les options réseau disponibles, consultez [Considérations réseau Azure Files](storage-files-networking-overview.md).

## <a name="support-for-azure-storage-features"></a>Prise en charge des fonctionnalités du Stockage Azure

Le tableau suivant indique le niveau actuel de prise en charge des fonctionnalités du Stockage Azure dans les comptes pour lesquels la fonctionnalité NFS 4.1 est activée. 

L’état des éléments figurant dans ce tableau peut évoluer au fil du temps, car la prise en charge continue de s’étendre.

| Fonctionnalité Stockage | Pris en charge pour les partages NFS |
|-----------------|---------|
| [API REST du plan de gestion de fichiers](/rest/api/storagerp/file-shares) | ✔️ |
| [API REST du plan de données de fichiers](/rest/api/storageservices/file-service-rest-api)| ⛔ |
| Chiffrement au repos|   ✔️ |
| Chiffrement en transit| ⛔ |
| [Types de redondance LRS ou ZRS](storage-files-planning.md#redundancy)|  ✔️ |
| [Points de terminaison privés](storage-files-networking-overview.md#private-endpoints) | ✔️  |
| Montages de sous-répertoires|  ✔️ |
| [Accorder l’accès réseau à des réseaux virtuels Azure spécifiques](storage-files-networking-endpoints.md#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)|  ✔️  |
| [Accorder l’accès réseau à des adresses IP spécifiques](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#grant-access-from-an-internet-ip-range)| ⛔ |
| [Niveau Premium](storage-files-planning.md#storage-tiers) |  ✔️  |
| [Niveaux standard (à chaud, froid et transactionnel optimisé)](storage-files-planning.md#storage-tiers)| ⛔ |
| [Autorisations POSIX](https://en.wikipedia.org/wiki/File-system_permissions#Notation_of_traditional_Unix_permissions)|  ✔️  |
| Écrasement de la racine|  ✔️  |
| [Authentification basée sur l’identité](storage-files-active-directory-overview.md) | ⛔ |
| [Suppression réversible de partage de fichiers Azure](storage-files-prevent-file-share-deletion.md) | ⛔  |
| [Azure File Sync](../file-sync/file-sync-introduction.md)| ⛔ |
| [Sauvegardes de partages de fichiers Azure](../../backup/azure-file-share-backup-overview.md)| ⛔ |
| [Instantanés de partage de fichiers Azure](storage-snapshots-files.md)| ⛔ |
| [Types de redondance GRS ou GZRS](storage-files-planning.md#redundancy)| ⛔ |
| [AZCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)| ⛔ |
| Explorateur Stockage Azure| ⛔ |
| Créer des partages NFS sur des comptes de stockage existants*| ⛔ |
| Prise en charge de plus de 16 groupes| ⛔ |

## <a name="regional-availability"></a>Disponibilité régionale

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="performance"></a>Performances
Les partages de fichiers Azure NFS sont uniquement proposés sur des partages de fichiers Premium, qui stockent les données sur des disques SSD. Les IOPS et le débit des partages NFS sont mis à l’échelle avec la capacité provisionnée. Consultez la section relative au [modèle provisionné](understanding-billing.md#provisioned-model) de l’article sur la présentation de la facturation pour comprendre les formules utilisées pour les IOPS, le bursting des E/S et le débit. Exprimées en millisecondes, es latences d’E/S moyennes sont à un seul chiffre dans le bas de la fourchette pour une petite taille d’E/S, tandis que les latences de métadonnées moyennes sont à un seul chiffre dans le haut de la fourchette. Les opérations lourdes en métadonnées comme la décompression et les charges de travail telles que WordPress peuvent faire face à des latences supplémentaires en raison du nombre élevé d’opérations d’ouverture et de fermeture.

## <a name="workloads"></a>Charges de travail
> [!IMPORTANT]
> Pour obtenir la liste des partages de fichiers NFS pour la production, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md).

NFS a été validé pour fonctionner correctement avec les charges de travail telles que la couche Application SAP, les sauvegardes de base de données, la réplication de base de données, les files d’attente de messages, les répertoires de base des serveurs de fichiers à usage général et les référentiels de contenu pour les charges de travail d’application.

Les charges de travail suivantes présentent des problèmes connus. Pour obtenir la liste des problèmes connus, consultez [Résoudre les problèmes liés aux partages de fichiers Azure NFS](storage-troubleshooting-files-nfs.md) :
- Oracle Database présente une incompatibilité avec sa fonctionnalité dNFS.


## <a name="next-steps"></a>Étapes suivantes
- [Créer un partage de fichiers NFS](storage-files-how-to-create-nfs-shares.md)
- [Comparer l’accès à Azure Files, Stockage Blob et Azure NetApp Files avec NFS](../common/nfs-comparison.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
