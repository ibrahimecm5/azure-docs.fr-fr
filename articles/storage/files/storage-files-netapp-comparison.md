---
title: Comparaison entre Azure Files et Azure NetApp Files | Microsoft Docs
description: Comparaison entre Azure Files et Azure NetApp Files.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: jeffpatt
ms.openlocfilehash: 06c1c1e32c8e31f1762f3fbdc932612036c5a327
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519284"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Comparaison entre Azure Files et Azure NetApp Files

Cet article fournit une comparaison entre Azure Files et Azure NetApp Files. 

La plupart des charges de travail qui requièrent le stockage de fichiers cloud fonctionnent bien sur Azure Files ou Azure NetApp Files. Pour vous aider à déterminer la solution la mieux adaptée à votre charge de travail, consultez les informations fournies dans cet article. Pour plus d’informations, consultez la documentation [Fichiers Azure](./index.yml) et [Fichiers NetApp Azure](../../azure-netapp-files/index.yml), ainsi que la session [Stockage partagé pour toutes les charges de travail de fichiers d’entreprise](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s) laquelle aborde le choix entre les Fichiers Azure et les Fichiers NetApp Azure.

## <a name="features"></a>Fonctionnalités

| Category | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Description | [Azure Files](https://azure.microsoft.com/services/storage/files/) est un service complètement managé et hautement disponible de niveau entreprise qui est optimisé pour les charges de travail d’accès aléatoire avec des mises à jour de données sur place.<br><br> Azure Files repose sur la même plateforme de stockage Azure que d’autres services comme les Blobs Azure. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) est un service NAS d’entreprise complètement managé et hautement disponible, qui peut gérer les charges de travail les plus exigeantes, hautes performances et à faible latence nécessitant des fonctionnalités de gestion de données avancées. Il permet la migration des charges de travail, qui sont considérées comme « non migrables » sans.<br><br>  ANF s’appuie sur le matériel nu de NetApp avec le système d’exploitation de stockage ONTAP s’exécutant dans le centre de données Azure pour une expérience Azure cohérente et des performances locales. |
| Protocoles | Premium<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>NFS 4.1</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>REST</li></ul><br> Pour en savoir plus, consultez les [protocoles de partage de fichiers disponibles](./storage-files-planning.md#available-protocols). | Tous les niveaux<br><ul><li>SMB 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>Accès au protocole double (NFSv3/SMB)</li></ul><br> Pour plus d’informations, consultez comment créer des volumes [NFS](../../azure-netapp-files/azure-netapp-files-create-volumes.md), [SMB](../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)ou [double protocole](../../azure-netapp-files/create-volumes-dual-protocol.md). |
| Disponibilité dans les régions | Premium<br><ul><li>30 régions et plus</li></ul><br>Standard<br><ul><li>Toutes les régions</li></ul><br> Pour plus d’informations, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Tous les niveaux<br><ul><li>28 régions et plus</li></ul><br> Pour plus d’informations, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redondance | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Pour en savoir plus, consultez [redondance](./storage-files-planning.md#redundancy). | Tous les niveaux<br><ul><li>Haute disponibilité locale intégrée</li><li>[Réplication entre régions](../../azure-netapp-files/cross-region-replication-introduction.md)</li></ul> |
| Contrat de niveau de service (SLA)<br><br> Notez que les contrats SLA pour Azure Files et Azure NetApp Files sont calculés différemment. | [Contrat SLA pour Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [Contrat SLA pour Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Authentification et autorisation basées sur l’identité | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Notez que l’authentification basée sur l’identification est prise en charge uniquement lors de l’utilisation du protocole SMB. Pour en savoir plus, consultez [FAQ](./storage-files-faq.md#security-authentication-and-access-control). | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Protocole double NFS/SMB<ul><li>Intégration ADDS/LDAP</li></ul><br>NFSv3/NFSv4.1<ul><li>Ajout/intégration LDAP avec les groupes étendus NFS [(préversion)](../../azure-netapp-files/configure-ldap-extended-groups.md)</li></ul><br> Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur NFS Azure NetApp Files](../../azure-netapp-files/faq-nfs.md) et [Questions fréquentes (FAQ) sur SMB Azure NetApp Files](../../azure-netapp-files/faq-smb.md). |
| Chiffrement | Tous les protocoles<br><ul><li>Chiffrement au repos (AES 256) avec clés managées par le client ou par Microsoft</li></ul><br>SMB<br><ul><li>Chiffrement Kerberos utilisant AES 256 ou RC4-HMAC</li><li>Chiffrement en transit</li></ul><br>REST<br><ul><li>Chiffrement en transit</li></ul><br> Pour en savoir plus, consultez [Sécurité et mise en réseau](files-nfs-protocol.md#security-and-networking). | Tous les protocoles<br><ul><li>Chiffrement au repos (AES 256) avec clés managées par Microsoft </li></ul><br>SMB<ul><li>Chiffrement en transit à l’aide de l’algorithme AES-CCM (SMB 3.0) et AES-GCM (SMB 3.1.1)</li></ul><br>NFS 4.1<ul><li>Chiffrement en transit à l’aide de Kerberos avec AES 256</li></ul><br> Pour plus d’informations, consultez [FAQ de sécurité](../../azure-netapp-files/faq-security.md). |
| Options d’accès | <ul><li>Internet</li><li>Accès au réseau virtuel sécurisé</li><li>Passerelle VPN</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Pour plus d’informations, consultez [Considérations réseau](./storage-files-networking-overview.md). | <ul><li>Accès au réseau virtuel sécurisé</li><li>Passerelle VPN</li><li>ExpressRoute</li><li>[Cache de fichiers globaux](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](../../hpc-cache/hpc-cache-overview.md)</li></ul><br> Pour plus d’informations, consultez [Considérations réseau](../../azure-netapp-files/azure-netapp-files-network-topologies.md). |
| Protection des données  | <ul><li>Captures instantanées incrémentielles</li><li>Restauration automatique des utilisateurs de fichiers/répertoires</li><li>Restaurer vers un nouvel emplacement</li><li>Rétablir sur place</li><li>Suppression logicielle au niveau du partage</li><li>Intégration à la Sauvegarde Azure</li></ul><br> Pour plus d’informations, consultez [Azure Files améliore les fonctionnalités de protection des données](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Captures instantanées (255/volume)</li><li>Restauration automatique des utilisateurs de fichiers/répertoires</li><li>Restaurer sur un nouveau volume</li><li>Rétablir sur place</li><li>[Réplication interrégion](../../azure-netapp-files/cross-region-replication-introduction.md) </li></ul><br> Pour plus d’informations, consultez [Fonctionnement des captures instantanées Azure NetApp Files](../../azure-netapp-files/snapshots-introduction.md). |
| Outils de migration  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Service de migration de stockage</li><li>AzCopy</li><li>Robocopy</li></ul><br> Pour plus d’informations [Migrer vers des partages de fichiers Azure](./storage-files-migration-overview.md). | <ul><li>[Cache de fichiers globaux](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Service de migration de stockage</li><li>AzCopy</li><li>Robocopy</li><li>Basée sur l’application (par exemple, HSR, Data Guard, AOAG)</li></ul> |
| Niveaux | <ul><li>Premium</li><li>Transaction optimisée</li><li>Chaud</li><li>Froid</li></ul><br> Pour plus d’informations, consultez [Niveau de stockage](./storage-files-planning.md#storage-tiers). | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Tous les niveaux fournissent une latence minimale de sous-MS.<br><br> Pour plus d’informations, consultez [Niveaux de service](../../azure-netapp-files/azure-netapp-files-service-levels.md) et [Considérations sur les performances](../../azure-netapp-files/azure-netapp-files-performance-considerations.md). |
| Tarifs | [Tarifs Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) | [Tarifs Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Scalabilité et performance

| Category | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Taille minimale du partage/volume | Premium<br><ul><li>100 Gio</li></ul><br>Standard<br><ul><li>Pas de minimum.</li></ul> | Tous les niveaux<br><ul><li>100 Gio (taille minimale de la capacité du pol) : 4 Tio)</li></ul> |
| Taille maximale du partage/volume | 100 Tio | Tous les niveaux<br><ul><li>100 Tio (limite du pool de capacité de 500 Tio)</li></ul><br>Jusqu’à 12,5 PiB par compte Azure NetApp. |
| IOPS maximum du partage/volume | Premium<br><ul><li>Jusqu’à 100 000</li></ul><br>Standard<br><ul><li>Jusqu’à 20 000</li></ul> | Ultra et Premium<br><ul><li>Jusqu’à 450 000 </li></ul><br>Standard<br><ul><li>Jusqu’à 320 000</li></ul> |
| Débit maximal de partage/volume | Premium<br><ul><li>Jusqu’à 10 Gio/s</li></ul><br>Standard<br><ul><li>Jusqu’à 300 Mio/s</li></ul> | Ultra et Premium<br><ul><li>Jusqu’à 4,5 Gio/s</li></ul><br>Standard<br><ul><li>Jusqu’à 3,2 Gio/s</li></ul> |
| Taille maximale du fichier | 4 Tio | 16 Tio |
| IOPS maximum par fichier | Premium<br><ul><li>Jusqu’à 8 000</li></ul><br>Standard<br><ul><li>1 000</li></ul> | Tous les niveaux<br><ul><li>Jusqu’à la limite du volume</li></ul> |
| Débit maximal par fichier | Premium<br><ul><li>300 Mio/s (jusqu’à 1 Gio/s avec la version multichannel de SMB)</li></ul><br>Standard<br><ul><li>60 Mio/s</li></ul> | Tous les niveaux<br><ul><li>Jusqu’à la limite du volume</li></ul> |
| SMB Multichannel | Oui | Oui |
| Latence | Latence minimale d’une milliseconde unique (2 ms à 3 ms pour les petites E/S) | Latence minimale sous-milliseconde (>1 ms par E/S aléatoire)<br><br>Pour en savoir plus, consultez [Point de référence du niveau de performance](../../azure-netapp-files/performance-benchmarks-linux.md). |

Pour plus d’informations sur les cibles de scalabilité et de niveau de performance, consultez [Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets) et [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md).

## <a name="next-steps"></a>Étapes suivantes
* [Documentation Azure Files](./index.yml)
* [Documentation Azure NetApp Files](../../azure-netapp-files/index.yml)
* [Session Stockage partagé pour toutes les charges de travail de fichiers d’entreprise](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)
