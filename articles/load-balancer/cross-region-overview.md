---
title: Équilibreur de charge inter-région (préversion)
titleSuffix: Azure Load Balancer
description: Vue d'ensemble du niveau Équilibreur de charge inter-région d'Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: d8b8cebfe18b52de7b904989907d587a4519c8e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254624"
---
# <a name="cross-region-load-balancer-preview"></a>Équilibreur de charge inter-région (préversion)

Azure Standard Load Balancer prend en charge l'équilibrage de charge inter-région, ce qui ouvre notamment la voie aux scénarios de haute disponibilité géo-redondants suivants :

* Trafic entrant en provenance de plusieurs régions.
* [Basculement global instantané](#regional-redundancy) vers le prochain déploiement régional optimal.
* Distribution de la charge des différentes régions vers la région Azure la plus proche avec [la très faible latence](#ultra-low-latency).
* Possibilité de [scale-up/scale-down](#ability-to-scale-updown-behind-a-single-endpoint) derrière un point de terminaison unique.
* Adresse IP globale anycast statique
* [Conservation de l'adresse IP du client](#client-ip-preservation)
* [Tirer parti de la solution d'équilibrage de charge existante](#build-cross-region-solution-on-existing-azure-load-balancer) sans aucun besoin d'apprentissage

> [!IMPORTANT]
> L’équilibreur de charge entre les régions est actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La configuration IP frontale de votre équilibreur de charge inter-région est statique et publiée dans [la plupart des régions Azure](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagramme de l'équilibreur de charge inter-région" border="true":::

> [!NOTE]
> Le port principal de votre règle d’équilibrage de charge sur l’équilibreur de charge inter-région doit correspondre au port front-end de la règle d’équilibrage de charge/règle NAT de trafic entrant sur l’équilibreur de charge standard régional. 

### <a name="regional-redundancy"></a>Redondance régionale

Configurez la redondance régionale en ajoutant une adresse IP publique frontale globale à vos équilibreurs de charge existants. 

En cas de défaillance d'une région, le trafic est acheminé vers l'équilibreur de charge régional sain le plus proche.  

La sonde d'intégrité de l'équilibreur de charge inter-région collecte les informations relatives à la disponibilité toutes les 20 secondes. Si la disponibilité d'un équilibreur de charge régional passe à 0, l'équilibreur de charge inter-région détecte la défaillance. L'équilibreur de charge régional est alors retiré de la rotation. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagramme de la vue globale du trafic régional" border="true":::

### <a name="ultra-low-latency"></a>Latence ultra faible

L'algorithme d'équilibrage de charge de géo-proximité est basé sur l'emplacement géographique de vos utilisateurs et de vos déploiements régionaux. 

Le trafic initié par un client atteindra la région participante la plus proche et passera par le réseau global principal de Microsoft pour arriver au déploiement régional le plus proche. 

Par exemple, vous disposez d'un équilibreur de charge inter-région avec des équilibreurs de charge standard dans les régions Azure suivantes :

* USA Ouest
* Europe Nord

Si un flux est initié depuis Seattle, le trafic entre dans la région USA Ouest. Il s'agit de la région participante la plus proche de Seattle. Le trafic est acheminé vers l'équilibreur de charge de la région la plus proche, à savoir USA Ouest.

L'équilibreur de charge inter-région Azure utilise un algorithme d'équilibrage de charge de géo-proximité pour la décision de routage. 

Le mode de distribution de la charge configuré pour les équilibreurs de charge régionaux est utilisé lors de la décision de routage finale si plusieurs équilibreurs de charge régionaux sont utilisés pour la géo-proximité.

Pour plus d’informations, consultez [Configuration du mode de distribution pour Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Possibilité de scale-up/scale-down derrière un point de terminaison unique

Lorsque vous exposez le point de terminaison global d’un équilibreur de charge inter-région aux clients, vous pouvez ajouter ou supprimer des déploiements régionaux derrière le point de terminaison global sans interruption. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-anycast-global-ip-address"></a>Adresse IP globale anycast statique
L'équilibreur de charge inter-région est fourni avec une adresse IP publique statique, ce qui garantit que l'adresse IP restera la même. Pour en savoir plus sur les adresses IP statiques, cliquez [ici](../virtual-network/ip-services/public-ip-addresses.md#ip-address-assignment).

### <a name="client-ip-preservation"></a>Conservation de l'adresse IP du client
L'équilibreur de charge inter-région est un équilibreur de charge réseau « Pass-through » de type Couche 4. Ce « Pass-through » conserve l'adresse IP d'origine du paquet.  L'adresse IP d'origine est accessible au code exécuté sur la machine virtuelle. Cette conservation vous permet d'appliquer une logique spécifique à une adresse IP.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Créer une solution inter-région sur une instance existante d'Azure Load Balancer
Le pool principal de l'équilibreur de charge inter-région contient un ou plusieurs équilibreurs de charge régionaux. 

Ajoutez vos déploiements d'équilibreurs de charge existants à un équilibreur de charge inter-région pour bénéficier d'un déploiement inter-région hautement disponible.

La **région d’accueil** est celle où est déployé l’équilibreur de charge inter-région ou l’adresse IP publique du niveau global. Cette région ne détermine en rien la façon dont le trafic sera acheminé. Si une région d’hébergement tombe en panne, le flux de trafic n’est pas affecté.

### <a name="home-regions"></a>Régions d'accueil
* USA Est 2
* USA Ouest
* Europe Ouest
* Asie Sud-Est
* USA Centre
* Europe Nord
* Asie Est

> [!NOTE]
> Vous ne pouvez déployer votre équilibreur de charge inter-région que dans l'une des 7 régions ci-dessus.

Une **région participante** est celle où l'adresse IP publique globale de l'équilibreur de charge est annoncée.

Le trafic initié par l'utilisateur sera acheminé vers la région participante la plus proche via le réseau Microsoft principal. 

L'équilibreur de charge inter-région achemine le trafic vers l'équilibreur de charge régional approprié.

### <a name="participating-regions"></a>Régions participantes
* USA Est 
* Europe Ouest 
* USA Centre 
* USA Est 2 
* USA Ouest 
* Europe Nord 
* États-Unis - partie centrale méridionale 
* USA Ouest 2 
* Sud du Royaume-Uni 
* Asie Sud-Est 
* Centre-Nord des États-Unis 
* Japon Est 
* Asie Est 
* Centre-USA Ouest 
* Sud-Australie Est 
* Australie Est 
* Inde centrale 

## <a name="limitations"></a>Limites

* Les configurations IP frontales inter-région sont uniquement publiques. Les serveurs frontaux internes ne sont actuellement pas pris en charge.

* Aucun équilibreur de charge privé ou interne ne peut pas être ajouté au pool principal de l’équilibreur de charge inter-région 

* Les configurations IP frontales IPv6 inter-région ne sont pas prises en charge. 

* Le trafic UDP n’est pas pris en charge sur l’équilibreur de charge inter-régions. 

* Il est actuellement impossible de configurer une sonde d'intégrité. Une sonde d'intégrité par défaut collecte automatiquement les informations de disponibilité relatives à l'équilibreur de charge régional toutes les 20 secondes. 

* L’intégration au service Azure Kubernetes (AKS) est actuellement indisponible. Une perte de connectivité se produit lors du déploiement d’un équilibreur de charge entre les régions avec l’équilibreur de charge standard quand le cluster AKS est déployé dans le serveur principal.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA
Équilibreur de charge inter-région, partage le [contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) de l'équilibreur de charge standard.

 
## <a name="next-steps"></a>Étapes suivantes

- Voir le [tutoriel : Créer un équilibreur de charge interrégional à l’aide du portail Azure](tutorial-cross-region-portal.md) pour créer un équilibreur interrégional.
- En savoir plus sur l’[équilibreur de charge interrégional](https://www.youtube.com/watch?v=3awUwUIv950).
- En savoir plus sur [Azure Load Balancer](load-balancer-overview.md).