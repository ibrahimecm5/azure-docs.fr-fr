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
ms.openlocfilehash: 8560ea04e57553f556122cfa1986cabb03442a4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399026"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API de point de terminaison V2 pour la synchronisation d’Azure AD Connect 
Microsoft a déployé un nouveau point de terminaison (API) pour Azure AD Connect qui améliore les performances des opérations des services de synchronisation pour Azure Active Directory. En utilisant le nouveau point de terminaison v2, vous constatez des gains de performances perceptibles lors de l’exportation et de l’importation vers Azure AD. Ce nouveau point de terminaison prend en charge les éléments suivants :
    
 - Synchronisation des groupes avec 250 000 membres maximum.
 - Gains de performances lors de l’exportation et de l’importation Azure AD.
 
> [!NOTE]
> Actuellement, le nouveau point de terminaison n’a pas de limite de taille de groupe configurée pour les groupes Microsoft 365 réécrits. Cela peut avoir un effet sur vos latences Active Directory et du cycle de synchronisation. Il est recommandé d’augmenter la taille des groupes de façon incrémentielle.  

>[!NOTE]
> L’API de point de terminaison Azure AD Connect Sync V2 est actuellement uniquement disponible dans les environnements Azure suivants :
> - Azure Commercial
> - Le cloud Azure Chine
> - Le cloud US Government Azure. Il ne sera pas disponible dans le cloud Azure Allemagne.

## <a name="prerequisites"></a>Prérequis  
Pour utiliser le nouveau point de terminaison V2, vous devez utiliser Azure AD Connect v2.0. Lorsque vous déployez AADConnect V2.0, le point de terminaison v2 est automatiquement activé.
Il existe un problème connu où la mise à niveau vers la dernière version 1.6 réinitialise la limite d’appartenance de groupe à 50 000. Lorsqu’un serveur est mis à niveau vers AADConnect 1.6, le client doit réappliquer les modifications apportées à la règle lorsqu’il a initialement augmenté la limite d’appartenance de groupe à 250 000 avant d’activer la synchronisation pour le serveur. 

## <a name="frequently-asked-questions"></a>Forum aux questions  
 
**Quand le nouveau point de terminaison deviendra-t-il le point par défaut pour les mises à niveau et les nouvelles installations ?**   Le point de terminaison V2 est le paramètre par défaut pour AADConnect V2.0 et nous conseillons aux clients de mettre à niveau vers AADConnect V2.0 pour tirer parti des avantages de ce point de terminaison.
Il y a un problème où les clients qui ont le point de terminaison V2 s’exécutant avec une version antérieure et tentent d’effectuer une mise à niveau vers une version 1.6 plus récente verront que la limite de 50k sur l’appartenance au groupe est rétablie. Lorsqu’un serveur est mis à niveau vers AADConnect 1.6, le client doit réappliquer les modifications apportées à la règle lorsqu’il a initialement augmenté la limite d’appartenance de groupe à 250 000 avant d’activer la synchronisation pour le serveur. 

## <a name="next-steps"></a>Étapes suivantes

* [Synchronisation Azure AD Connect : Comprendre et personnaliser la synchronisation](how-to-connect-sync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)
