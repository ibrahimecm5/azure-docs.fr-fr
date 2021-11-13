---
title: Quota de capacité régionale pour Azure NetApp Files | Microsoft Docs
description: Explique le quota de capacité régionale d’Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: b-juche
ms.openlocfilehash: 1ae89ff8b8593d96740b0140a1570ac19deacea3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270937"
---
# <a name="regional-capacity-quota-for-azure-netapp-files"></a>Quota de capacité régionale pour Azure NetApp Files

Cet article explique le quota de capacité régionale d’Azure NetApp Files.

## <a name="display-regional-capacity-quota"></a>Afficher le quota de capacité régionale

vous pouvez cliquer sur **Quota** sous Paramètres de Azure NetApp Files pour afficher les tailles de quota actuelles et par défaut pour la région. 

Par exemple : 

![Capture d’écran montrant comment afficher des informations de quota.](../media/azure-netapp-files/quota-display.png) 

## <a name="request-regional-capacity-quota-increase"></a>Demander une augmentation du quota de capacité régionale

Vous pouvez [envoyer une demande de support](azure-netapp-files-resource-limits.md#request-limit-increase) pour une augmentation du quota de capacité régionale sans frais supplémentaires. La demande de support que vous envoyez sera envoyée à l’équipe de gestion de la capacité Azure pour traitement. Vous recevrez une réponse généralement dans un délai de 2 jours ouvrables. L’équipe de gestion de la capacité Azure peut vous contacter pour la gestion des requêtes volumineuses.  

Une augmentation du quota de capacité régional n’entraîne pas d’augmentation de la facturation. La facturation est toujours basée sur les pools de capacité approvisionnés.
Par exemple, si vous disposez actuellement de 25 Tio de capacité approvisionnée, vous pouvez demander une augmentation de quota à 35 Tio.  Dans les deux jours ouvrables, votre augmentation du quota sera appliquée à la région demandée. Une fois l’augmentation du quota appliquée, vous payez toujours uniquement la capacité approvisionnée actuelle (25 Tio). Mais lorsque vous approvisionnez en fait les 10 Tio supplémentaires, vous êtes facturé pour 35 Tio.

Les [limites de ressources](azure-netapp-files-resource-limits.md#resource-limits) actuelles pour Azure NetApp Files ne sont pas modifiées. Vous serez toujours en mesure d’approvisionner un pool de capacité de 500-Tio. Mais avant cela, le quota de capacité régionale doit être augmenté à 500 Tio.

## <a name="next-steps"></a>Étapes suivantes  

- [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Comprendre la hiérarchie de stockage d’Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modèle de coût pour Azure NetApp Files](azure-netapp-files-cost-model.md)
