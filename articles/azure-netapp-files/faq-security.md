---
title: FAQ sur la sécurité dans Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur la sécurité dans Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.custom: references_regions
ms.openlocfilehash: d1794516cfd6c97411d27122749a4260bcbe8358
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270811"
---
# <a name="security-faqs-for-azure-netapp-files"></a>FAQ sur la sécurité dans Azure NetApp Files

Cet article contient les réponses à certaines questions fréquemment posées sur la sécurité dans Azure NetApp Files.

## <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Le trafic réseau entre la machine virtuelle Azure et le stockage peut-il être chiffré ?

Le trafic des données Azure NetApp Files est sécurisé par conception parce qu’il ne fournit pas de point de terminaison public et que le trafic de données reste au sein du réseau virtuel appartenant au client. Les données à la volée ne sont pas chiffrées par défaut. Toutefois, le trafic des données d’une machine virtuelle Azure (exécutant un client SMB ou NFS) vers Azure NetApp Files est aussi sécurisé que tout autre trafic entre machines virtuelles Azure. 

Le protocole NFSv3 ne prend pas en charge le chiffrement. Ces données ne peuvent donc pas être chiffrées. Toutefois, le chiffrement des données à la volée NFSv4.1 et SMB3 peut être activé. Le trafic de données entre les clients NFSv4.1 et les volumes Azure NetApp Files peut être chiffré à l’aide de Kerberos avec le chiffrement AES-256. Pour plus d’informations, consultez [Configurer le chiffrement Kerberos NFSv4.1 pour Azure NetApp Files](configure-kerberos-encryption.md). Le trafic de données entre les clients SMB3 et les volumes Azure NetApp Files peut être chiffré à l’aide de l’algorithme AES-CCM sur SMB 3.0 et de l’algorithme AES-GCM sur les connexions SMB 3.1.1. Pour plus d’informations, consultez [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md). 

## <a name="can-the-storage-be-encrypted-at-rest"></a>Le stockage peut-il être chiffré au repos ?

Tous les volumes Azure NetApp Files sont chiffrés en suivant la norme FIPS 140-2. Toutes les clés sont gérées par le service Azure NetApp Files. 

## <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Le trafic de réplication inter-région Azure NetApp Files est-il chiffré ?

La réplication inter-région Azure NetApp Files utilise le chiffrement TLS 1.2 AES-256 GCM pour chiffrer toutes les données transférées entre le volume source et le volume de destination. Ce chiffrement s’ajoute au [chiffrement Azure MACSec](../security/fundamentals/encryption-overview.md) qui est activé par défaut pour tout le trafic Azure, notamment la réplication inter-région Azure NetApp Files. 

## <a name="how-are-encryption-keys-managed"></a>Comment les clés de chiffrement sont-elles gérées ? 

La gestion des clés pour Azure NetApp Files est assurée par le service. Une clé de chiffrement de données XTS-AES-256 unique est générée pour chaque volume. Une hiérarchie de clés de chiffrement est utilisée pour chiffrer et protéger toutes les clés de volume. Ces clés de chiffrement ne sont jamais affichées ou indiquées dans un format non chiffré. Les clés de chiffrement sont supprimées en même temps qu'un volume.

La prise en charge des clés gérées par le client (Bring Your Own Key) à l’aide d’Azure Dedicated HSM est disponible de façon contrôlée dans les régions USA Est, USA Centre Sud, USA Ouest 2 et US Gov Virginie. Vous pouvez demander l’accès à [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Une fois la capacité disponible, les demandes sont approuvées.

## <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Puis-je configurer les règles de stratégie d’exportation NFS pour contrôler l’accès à la cible de montage du service Azure NetApp Files ?

Oui, vous pouvez configurer jusqu'à cinq règles dans une même stratégie d’exportation NFS.

## <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Puis-je utiliser Azure RBAC avec Azure NetApp Files ?

Oui, Azure NetApp Files prend en charge les fonctionnalités Azure RBAC. Avec les rôles Azure intégrés, vous pouvez [créer des rôles personnalisés](../role-based-access-control/custom-roles.md) pour Azure NetApp files. 

Pour obtenir la liste complète des autorisations Azure NetApp Files, consultez les opérations de fournisseur de ressources Azure pour [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp).

## <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Les journaux d’activité Azure sont-ils pris en charge sur Azure NetApp Files ?

Azure NetApp Files est un service natif Azure. Toutes les API PUT, POST et DELETE opérant sur Azure NetApp Files sont journalisées. Par exemple, les journaux présentent des activités indiquant, par exemple, qui a créé la capture instantanée, qui a modifié le volume, et ainsi de suite.

Pour obtenir la liste complète des opérations API, consultez [API REST Azure NetApp Files](/rest/api/netapp/).

## <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Puis-je utiliser des stratégies Azure avec Azure NetApp Files ?

Oui, vous pouvez créer des [stratégies Azure personnalisées](../governance/policy/tutorials/create-custom-policy-definition.md). 

Toutefois, vous ne pouvez pas créer de stratégies Azure (stratégies de nommage personnalisées) sur l’interface Azure NetApp Files. Consultez [Consignes pour planifier un réseau Azure NetApp Files](azure-netapp-files-network-topologies.md#considerations).

## <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Lorsque je supprime un volume Azure NetApp Files, les données sont-elles supprimées en toute sécurité ? 

La suppression d'un volume Azure NetApp Files est effectuée par programmation avec effet immédiat. L’opération de suppression comprend la suppression des clés utilisées pour chiffrer les données au repos. Il n’existe aucune option pour un scénario de récupération d’un volume supprimé une fois l’opération de suppression exécutée avec succès (via des interfaces telles que le portail Azure et l’API).


## <a name="next-steps"></a>Étapes suivantes  

- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [FAQ sur la mise en réseau](faq-networking.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)