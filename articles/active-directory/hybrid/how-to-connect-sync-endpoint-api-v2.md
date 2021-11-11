---
title: Point de terminaison V2 pour la synchronisation d’Azure AD Connect | Microsoft Docs
description: Ce document traite des mises à jour de l’API des points de terminaison v2 pour la synchronisation Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964829c0d775bd33ea0c44386820ae2ba921ce1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466045"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de point de terminaison V2 pour la synchronisation d’Azure AD Connect 
Microsoft a déployé un nouveau point de terminaison (API) pour Azure AD Connect qui améliore les performances des opérations des services de synchronisation pour Azure Active Directory. En utilisant le nouveau point de terminaison v2, vous constaterez des gains de performances perceptibles lors de l’exportation et de l’importation vers Azure AD. Ce nouveau point de terminaison prend en charge les éléments suivants :
    
 - synchronisation des groupes avec jusqu’à 250 000 membres
 - gains de performances lors de l’exportation et de l’importation vers Azure AD
 
> [!NOTE]
> Actuellement, le nouveau point de terminaison n’a pas de limite de taille de groupe configurée pour les groupes Microsoft 365 réécrits. Cela peut avoir un effet sur vos latences Active Directory et du cycle de synchronisation. Il est recommandé d’augmenter la taille des groupes de façon incrémentielle.  

>[!NOTE]
> L’API de point de terminaison Azure AD Connect Sync V2 est actuellement uniquement disponible dans les environnements Azure suivants :
> - Azure Commercial
> - Le cloud Azure Chine
> - Le cloud US Government Azure. Il ne sera pas disponible dans le cloud Azure Allemagne.

## <a name="prerequisites"></a>Prérequis  
Pour utiliser le nouveau point de terminaison V2, vous devez utiliser Azure AD Connect v2.0. Lorsque vous déployez AADConnect V2.0, le point de terminaison v2 est automatiquement activé.
Notez que la prise en charge du point de terminaison V2 n’est plus disponible pour les versions V1.x. Si vous devez synchroniser des groupes avec plus de 50 000 membres, vous devez effectuer une mise à niveau vers Azure AD Connect V2.0.

## <a name="frequently-asked-questions"></a>Forum aux questions  
 
**Quand le nouveau point de terminaison deviendra-t-il le point par défaut pour les mises à niveau et les nouvelles installations ?**   Le point de terminaison v2 est le paramètre par défaut pour AADConnect V2.0, et n’est pas pris en charge pour AADConnect v1.x

## <a name="next-steps"></a>Étapes suivantes

* [Synchronisation Azure AD Connect : Comprendre et personnaliser la synchronisation](how-to-connect-sync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)
