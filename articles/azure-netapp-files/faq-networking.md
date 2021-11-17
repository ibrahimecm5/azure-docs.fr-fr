---
title: FAQ sur les réseaux pour Azure NetApp Files | Microsoft Docs
description: Réponses aux questions fréquemment posées sur les réseaux Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 11/08/2021
ms.openlocfilehash: 9539cf9cc327cb954a852c183cf8c94ee4d56ca1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054787"
---
# <a name="networking-faqs-for-azure-netapp-files"></a>FAQ sur les réseaux pour Azure NetApp Files

Cet article contient les réponses à certaines questions fréquemment posées sur les réseaux Azure NetApp Files. 

## <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Le chemin d’accès aux données pour NFS ou SMB passe-t-il par Internet ?  

Non. Le chemin d’accès aux données pour NFS ou SMB ne passe pas par Internet. Azure NetApp Files est un service natif Azure qui est déployé dans le réseau virtuel Azure (VNet) dans lequel le service est disponible. Azure NetApp Files utilise un sous-réseau délégué et configure une interface réseau directement sur le réseau virtuel. 

Voir [Consignes pour planifier un réseau Azure NetApp Files](./azure-netapp-files-network-topologies.md) pour plus de détails.  

## <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Puis-je connecter un réseau virtuel que j’ai déjà créé pour le service Azure NetApp Files ?

Oui, vous pouvez connecter des réseaux virtuels que vous avez créés pour le service. 

Voir [Consignes pour planifier un réseau Azure NetApp Files](./azure-netapp-files-network-topologies.md) pour plus de détails.  

## <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Puis-je monter un volume NFS d’Azure NetApp Files avec le nom FQDN du DNS ?

Oui, vous pouvez le faire si vous créez les entrées DNS requises. Azure NetApp Files fournit l’adresse IP de service pour le volume configuré. 

> [!NOTE] 
> Azure NetApp Files peut déployer des adresses IP supplémentaires pour le service en fonction des besoins.  Les entrées DNS peuvent avoir à être mises à jour régulièrement.

## <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Puis-je définir ou sélectionner ma propre adresse IP pour un volume Azure NetApp Files ?  

Non. L’attribution d’adresses IP aux volumes Azure NetApp Files est dynamique. L’attribution d’adresses IP statiques n’est pas prise en charge. 

## <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files prend-il en charge le réseau virtuel à double pile (IPv4 et IPv6) ?

Non, Azure NetApp Files ne prend pas actuellement en charge le déploiement de volumes dans un sous-réseau délégué à double pile (IPv4 et IPv6). Le sous-réseau délégué pour le service Azure NetApp Files doit être un sous-réseau IPv4 uniquement. Toutefois, Azure NetApp Files est accessible sur IPv4 via un sous-réseau ou un réseau virtuel (appairé) à double pile.

## <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>Le nombre d'adresses IP à l'aide d'Azure VMware Solution pour les montages de système d'exploitation invités est-il limité à 1 000 ?

Non. Azure VMware Solution se trouve derrière une passerelle ER qui se comporte comme sur des systèmes locaux. Le nombre d’« hôtes » et d’« invités » AVS n’est pas visible pour Azure NetApp Files, et la [limite de 1000 adresses IP](azure-netapp-files-resource-limits.md#resource-limits) ne s’applique pas.
 
## <a name="next-steps"></a>Étapes suivantes  

- [Questions fréquentes (FAQ) sur Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Questions fréquentes (FAQ) sur les réseaux virtuels Microsoft Azure](../virtual-network/virtual-networks-faq.md)
- [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Forum aux questions sur la sécurité](faq-security.md)
- [Questions fréquentes (FAQ) relatives aux performances](faq-performance.md)
- [FAQ relatifs au NFS](faq-nfs.md)
- [Questions fréquentes sur SMB](faq-smb.md)
- [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
- [Questions fréquentes sur la migration et la protection des données](faq-data-migration-protection.md)
- [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)
- [FAQ relatives à la résilience des applications](faq-application-resilience.md)
- [Forum aux questions sur l’intégration](faq-integration.md)
