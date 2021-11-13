---
title: Hiérarchie de stockage d’Azure NetApp Files | Microsoft Docs
description: Décrit la hiérarchie de stockage, y compris les comptes, les pools de capacité et les volumes Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 49ad214c8851546cb2340d7ad413f9369e9b8a01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219941"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Hiérarchie de stockage d’Azure NetApp Files

Avant de créer un volume dans Azure NetApp Files, vous devez acheter et configurer un pool de capacité disponible.  Pour configurer un pool de capacité, vous devez disposer d’un compte NetApp. Comprendre la hiérarchie de stockage s’avère utile pour configurer et gérer vos ressources Azure NetApp Files.

> [!IMPORTANT] 
> Actuellement, Azure NetApp Files ne prend pas en charge la migration des ressources entre les abonnements.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Comptes NetApp

- Un compte NetApp est un groupement administratif de pools de capacité.  
- Un compte de NetApp diffère de votre compte de stockage Azure général. 
- Un compte NetApp a une portée régionale.   
- Vous pouvez avoir plusieurs comptes NetApp dans une région, mais chaque compte NetApp est lié à une seule région.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Pools de capacités

Avoir une bonne compréhension du fonctionnement des pools de capacités vous aide à sélectionner les types de pools de capacités appropriés pour vos besoins de stockage. 

### <a name="general-rules-of-capacity-pools"></a>Règles générales des pools de capacités

- Un pool de capacité se mesure par sa capacité allouée.   
    Pour plus d’informations, consultez [Types de QoS](#qos_types).  
- La capacité est approvisionnée via les références SKU fixes que vous avez achetées (par exemple, une capacité de 4 Tio).
- Un pool de capacité ne peut avoir qu’un seul niveau de service.  
- Chaque pool de capacité peut appartenir à un seul compte NetApp. Toutefois, vous pouvez avoir plusieurs pools de capacité au sein d’un compte NetApp.  
- Un pool de capacité ne peut pas être déplacé entre les comptes NetApp.   
  Par exemple, dans le [diagramme conceptuel de la hiérarchie de stockage](#conceptual_diagram_of_storage_hierarchy) ci-dessous, le Pool de capacité 1 ne peut pas être déplacé du compte NetApp USA Est au compte NetApp USA Ouest 2.  
- Impossible de supprimer un pool de capacités tant que tous les volumes qu’il contient n’ont pas été supprimés.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Types de QoS (Qualité de service) pour les pools de capacités

Le type de QoS est un attribut d’un pool de capacités. Azure NetApp Files fournit deux types QoS de pools de capacités : *automatique (par défaut)* et *manuel*. 

#### <a name="automatic-or-auto-qos-type"></a>Type de QoS *Automatique (ou Auto)*  

Quand vous créez un pool de capacités, le type de QoS par défaut est Auto.

Dans un pool de capacités de QoS automatique, le débit est affecté automatiquement aux volumes dans le pool, proportionnellement au quota de taille alloué aux volumes. 

Le débit maximal alloué à un volume dépend du niveau de service du pool de capacités et du quota de taille du volume. Pour obtenir un exemple de calcul, consultez [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md).

Pour plus d’informations concernant les performances des types QoS, consultez [Considérations sur les performances pour Azure NetApp Files](azure-netapp-files-performance-considerations.md).

#### <a name="manual-qos-type"></a>Type de QoS *manuel*  

Lorsque vous [créez un pool de capacités](azure-netapp-files-set-up-capacity-pool.md), vous pouvez spécifier d’utiliser le type QoS manuelle pour le pool de capacités. Vous pouvez également [modifier un pool de capacités existant](manage-manual-qos-capacity-pool.md#change-to-qos) pour utiliser le type QoS manuelle. *La définition du type de capacité sur la QoS manuelle est une modification permanente.* Vous ne pouvez pas convertir un outil de capacité de type QoS manuelle en pool de capacités avec QoS automatique. 

Au sein d’un pool de capacités QoS manuel, vous pouvez affecter la capacité et le débit d’un volume de manière indépendante. Pour les niveaux de débit minimaux et maximaux, consultez [Limites de ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits). Le débit total de tous les volumes créés avec un pool de capacités de QoS manuel est limité par le débit total du pool.  Il est déterminé par la combinaison de la taille du pool et du débit au niveau du service.  Par exemple, un pool de capacités de 4 Tio avec le niveau de service Ultra a une capacité de débit totale de 512 Mio/s (4 Tio x 128 Mio/s/Tio) disponible pour les volumes.

##### <a name="example-of-using-manual-qos"></a>Exemple d’utilisation de la QoS manuelle

Lorsque vous utilisez un pool de capacités avec QoS manuelle avec, par exemple, un système SAP HANA, une base de données Oracle ou d’autres charges de travail nécessitant plusieurs volumes, le pool de capacités peut être utilisé pour créer ces volumes d’application.  Chaque volume peut fournir la taille et le débit individuels pour répondre aux besoins de l’application.  Pour plus d’informations sur les avantages, consultez [Exemples de limite de débit de volumes dans un pool de capacités avec QoS manuelle](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool).  

## <a name="volumes"></a><a name="volumes"></a>Volumes

- Un volume se mesure par la consommation de capacité logique et est évolutif. 
- La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.
- La consommation de débit d’un volume est comptée par rapport au débit disponible de son pool. Consultez [Type de QoS manuel](#manual-qos-type).
- Chaque volume appartient à un seul pool, mais un pool peut contenir plusieurs volumes. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagramme conceptuel de la hiérarchie de stockage 
L’exemple suivant montre les relations entre l’abonnement Azure, les comptes NetApp, les pools de capacité et les volumes.   

![Diagramme conceptuel de la hiérarchie de stockage](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Étapes suivantes

- [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Considérations sur les performances pour Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Créer un pool de capacités](azure-netapp-files-set-up-capacity-pool.md)
- [Gérer un pool de capacités de QoS manuel](manage-manual-qos-capacity-pool.md)
