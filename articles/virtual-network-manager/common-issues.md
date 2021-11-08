---
title: Problèmes courants rencontrés avec le gestionnaire Azure Virtual Network Manager (version préliminaire)
description: En savoir plus sur les problèmes courants rencontrés lors de l’utilisation du gestionnaire Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bcf66883ba766189215fd4ce267391c9358b10f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096357"
---
# <a name="common-issues-seen-with-azure-virtual-network-manager-preview"></a>Problèmes courants rencontrés avec le gestionnaire Azure Virtual Network Manager (version préliminaire)

Dans cet article, nous allons aborder les problèmes courants que vous pouvez rencontrer lors de l’utilisation du gestionnaire Azure Virtual Network Manager et fournir des solutions possibles.

## <a name="why-isnt-my-configuration-getting-applied"></a>Pourquoi la configuration n’est-elle pas appliquée ? 

**Raisons courantes pour lesquelles les configurations ne sont pas appliquées :** 

* La configuration n’est pas déployée dans les régions où se trouvent les réseaux virtuels. 

* Vous n’avez pas encore déployé la configuration. Vous devez déployer la configuration pour qu’elle prenne effet. 

* La configuration n’a pas eu suffisamment de temps pour prendre effet. Le temps nécessaire à l’application de la configuration après la validation de la configuration est d’environ 15-20 minutes. En cas de mise à jour de votre appartenance à un groupe réseau, il faut environ 10 minutes pour que les modifications prennent effet. 

## <a name="i-updated-my-configuration-but-the-changes-arent-reflected-in-my-environment"></a>J’ai mis à jour ma configuration, mais les modifications ne sont pas reflétées dans mon environnement. 

Vous devez déployer la nouvelle configuration une fois la configuration modifiée. 

## <a name="why-is-my-connectivity-configuration-not-working"></a>Pourquoi ma configuration de connectivité ne fonctionne-t-elle pas ? 

**Vous devriez peut-être tenir compte des éléments suivants :** 

* Dans une topologie Hub and Spoke, si vous activez l’option permettant d’*utiliser le hub en tant que passerelle*, vous devez disposer d’une passerelle dans le réseau virtuel Hub. Dans le cas contraire, la création de l’appairage de réseaux virtuels entre le hub et les réseaux virtuels Spoke échouera. 

* Si vous souhaitez que des membres du groupe de réseaux communiquent entre eux dans des régions dans une configuration de topologie Hub and Spoke, vous devez activer l’option de maillage global. 

## <a name="next-steps"></a>Étapes suivantes

Consultez la s[FAQ sur le gestionnaire Azure Virtual Network Manager](faq.md) pour obtenir des réponses aux questions fréquemment posées.
