---
title: Comprendre l’accès aux machines virtuelles juste-à-temps dans Microsoft Defender pour le cloud
description: Ce document explique comment l’accès juste à temps aux machines virtuelles dans Microsoft Defender pour le cloud vous aide à contrôler l’accès à vos machines virtuelles Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 8e54419761d823396387c3b6106f694e4277702d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437844"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Fonctionnement de l’accès aux machines virtuelles juste-à-temps (JAT)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique les principes qui sous-tendent la fonctionnalité d’accès aux machines virtuelles juste-à-temps (JAT) de Microsoft Defender pour le cloud et la logique qui sous-tend la recommandation.

Pour savoir comment appliquer l’accès JAT à vos machines virtuelles sur le Portail Azure (Defender pour le cloud ou Machines virtuelles Azure) ou programmatiquement, consultez [Guide pratique pour sécuriser des ports de gestion avec l’accès JAT](just-in-time-access-usage.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Risque inhérent à des ports de gestion ouverts sur une machine virtuelle

Les acteurs des menaces repèrent activement les machines accessibles avec des ports de gestion ouverts, par exemple pour les protocoles RDP ou SSH. Toutes vos machines virtuelles sont des cibles potentielles pour une attaque. Lorsqu’une machine virtuelle est compromise, elle est utilisée comme point d’entrée pour attaquer d’autres ressources au sein de votre environnement.



## <a name="why-jit-vm-access-is-the-solution"></a>Raison pour laquelle l’accès aux machines virtuelles JAT est la solution 

Comme pour toutes les techniques de prévention en cybersécurité, votre objectif doit être de réduire la surface d’attaque. Dans ce cas, cela signifie avoir moins de ports ouverts, en particulier des ports de gestion.

Vos utilisateurs légitimes utilisent également ces ports, il n’est donc pas pratique de les garder fermés.

Pour résoudre ce dilemme, Microsoft Defender pour le cloud offre le JAT. Grâce à l’accès JAT, vous pouvez verrouiller le trafic entrant sur vos machines virtuelles, ce qui réduit l’exposition aux attaques tout en facilitant la connexion aux machines virtuelles le cas échéant.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Fonctionnement de l’accès JAT avec des groupes de sécurité réseau et Pare-feu Azure

Lorsque vous activez l’accès aux machines virtuelles juste-à-temps, vous pouvez sélectionner les ports de la machine virtuelle vers lesquels le trafic entrant sera bloqué. Defender pour le cloud garantit l’existence de règles de « refus de tout le trafic entrant » pour les ports sélectionnés dans les règles du [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) et de [Pare-feu Azure](../firewall/rule-processing.md). Ces règles restreignent l’accès aux ports de gestion de vos machines virtuelles Azure et les protègent contre les attaques. 

Si d’autres règles existent déjà pour les ports sélectionnés, celles-ci sont prioritaires sur les nouvelles règles de « refus de tout le trafic entrant ». S’il n’existe aucune règle sur les ports sélectionnés, alors les nouvelles règles sont prioritaires dans le groupe de sécurité réseau et Pare-feu Azure.

Quand un utilisateur demande l’accès à une machine virtuelle, Defender pour le cloud vérifie que cet utilisateur dispose des autorisations de [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) sur cette machine virtuelle. Si la requête est approuvée, Defender pour le cloud configure les NSG et Pare-feu Azure afin d’autoriser le trafic entrant vers les ports sélectionnés à partir de l’adresse IP (ou de la plage d’adresses IP) correspondante, pendant la durée spécifiée. Après expiration du délai, Defender pour le cloud restaure les groupes de sécurité réseau à leur état précédent. Les connexions déjà établies ne sont pas interrompues.

> [!NOTE]
> L’accès JAT ne prend pas en charge les machines virtuelles protégées par des pare-feu Azure contrôlés par [Azure Firewall Manager](../firewall-manager/overview.md).  Le pare-feu Azure doit être configuré avec des règles (classiques) et ne peut pas utiliser des stratégies de pare-feu.




## <a name="how-defender-for-cloud-identifies-which-vms-should-have-jit-applied"></a>Comment Defender pour le cloud identifie les machines virtuelles auxquelles le JAT doit être appliqué

Le diagramme ci-dessous montre la logique que Defender pour le cloud applique lorsqu’il décide de la manière de classer les machines virtuelles prises en charge : 

[![Flux logique de machine virtuelle juste-à-temps.](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Lorsque Defender pour le cloud trouve une machine qui peut tirer parti de l’accès JAT, il l’ajoute à l’onglet **Ressources non saines** de la recommandation. 

![Recommandation d’accès aux machines virtuelles juste-à-temps (JAT).](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---just-in-time-virtual-machine-access"></a>FAQ : Accès juste-à-temps aux machines virtuelles

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Quelles sont les autorisations nécessaires pour configurer et utiliser l’accès JAT ?

JIT requiert que [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) soit activé sur l’abonnement. 

Les rôles **Lecteur** et **SecurityReader** peuvent tous deux afficher l’état et les paramètres de l’accès JAT.

Si vous voulez créer des rôles personnalisés qui peuvent fonctionner avec l’accès JAT, vous aurez besoin des détails du tableau ci-dessous.

> [!TIP]
> Pour créer un rôle de moindre privilège pour les utilisateurs qui doivent demander un accès JAT à une machine virtuelle et n’exécuter aucune autre opération JAT, utilisez le script [Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role) à partir des pages de la communauté GitHub de Defender pour le cloud.

| Pour permettre à un utilisateur de : | Autorisations à définir|
| --- | --- |
|Configurer ou modifier une stratégie juste-à-temps pour une machine virtuelle | *Attribuez ces actions au rôle :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Dans le cadre d’un abonnement, d’un groupe de ressources de machine virtuelle : <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Demander l’accès JIT à une machine virtuelle | *Attribuez ces actions à l’utilisateur :*  <ul><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>Dans le cadre d’un abonnement ou d’un groupe de ressources qui est associé à la machine virtuelle :<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Dans le cadre d’un abonnement, d’un groupe de ressources ou d’une machine virtuelle :<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Lire les stratégies JIT| *Attribuez ces actions à l’utilisateur :*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Étapes suivantes

Cette page explique _pourquoi_ l’accès aux machines virtuelles juste-à-temps (JAT) doit être utilisé. Pour savoir _comment_ activer la fonctionnalité juste-à-temps et demander l’accès à vos machines virtuelles juste-à-temps, consultez ce qui suit :

> [!div class="nextstepaction"]
> [Comment sécuriser vos ports de gestion avec l’accès JAT](just-in-time-access-usage.md)
