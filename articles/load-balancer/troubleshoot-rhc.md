---
title: Résoudre les problèmes d’intégrité des ressources, de disponibilité de front-end et de back-end Azure Load Balancer Resource
description: Servez-vous des métriques disponibles pour diagnostiquer votre équilibreur de charge Azure Standard Load Balancer Azure à l’état dégradé ou indisponible.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: kumud
ms.openlocfilehash: f3f0e1d714c5edef75f0f042edce1749b8f01bcf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056506"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Résoudre les problèmes d’intégrité des ressources et de disponibilité entrante 

Cet article est destiné à vous guider dans l’investigation des problèmes impactant la disponibilité de l’adresse IP du front-end et des ressources back-end de votre équilibreur de charge. 

La vérification Resource Health (RHC) pour Load Balancer est utilisée pour déterminer l’intégrité de votre équilibreur de charge. Elle analyse la métrique Disponibilité du chemin des données sur un intervalle de **deux minutes** pour déterminer si les points de terminaison d’équilibrage de charge, l’adresse IP du front-end et les combinaisons de ports frontaux avec règles d’équilibrage de charge sont disponibles.

Le tableau ci-dessous décrit la logique RHC utilisée pour déterminer l’état d’intégrité de votre équilibreur de charge.

| État d’intégrité des ressources | Description |
| --- | --- |
| Disponible | Votre ressource d’équilibreur de charge standard est intègre et disponible. |
| Détérioré | Votre équilibreur de charge standard présente des événements lancés par la plateforme ou l’utilisateur qui nuisent aux performances. La métrique Disponibilité du chemin de données a fait état d’une intégrité inférieure à 90 %, mais supérieure à 25 % pendant au moins deux minutes. L’impact sur les performances que vous allez subir sera de niveau modéré à grave. 
| Non disponible | Votre ressource d’équilibreur de charge standard n’est pas intègre. La métrique Disponibilité du chemin des données a fait état d’une intégrité inférieure à 25 % pendant au moins deux minutes. Vous allez subir un impact sur les performances significatif ou un défaut de disponibilité pour la connectivité entrante. Des événements utilisateur ou plateforme peuvent être à l’origine de l’indisponibilité. |
| Unknown | L’état d’intégrité de votre ressource d’équilibrage de charge standard n’a pas encore été mis à jour ou n’a pas encore reçu d’informations de disponibilité du chemin de données au cours des 10 dernières minutes. Cet état devrait être transitoire et passer à un état correct dès que des données seront reçues. |


## <a name="about-the-metrics-well-use"></a>À propos des métriques que nous utilisons
Les deux métriques à utiliser sont *Disponibilité du chemin des données* et *État de la sonde d’intégrité* , et il est important d’en comprendre la signification pour obtenir des insights justes. 

## <a name="data-path-availability"></a>Disponibilité du chemin des données
La métrique de disponibilité du chemin de données est générée par un test ping TCP toutes les 25 secondes sur tous les ports front-end sur lesquels des règles NAT de trafic entrant et d’équilibrage de charge sont configurées. Ce test ping TCP est ensuite routé vers une des instances back-end (sondées) saines. Si le service reçoit une réponse au test ping, il est considéré comme ayant réussi et la somme de la métrique est itérée une fois. En cas d’échec, elle n’est pas itérée. Le décompte de cette métrique est de 1/100 du nombre total de tests ping TCP par période d’échantillonnage. Par conséquent, il est préférable de prendre en compte la moyenne, qui indique la somme/le décompte moyen de la période. La métrique de disponibilité du chemin de données agrégée par moyenne donne un pourcentage du taux de réussite des tests ping TCP sur votre adresse IP front-end:port pour chacune de vos règles NAT de trafic entrant et d’équilibrage de charge.

## <a name="health-probe-status"></a>État de la sonde d’intégrité
La métrique d’état de la sonde d’intégrité est générée par un test ping du protocole défini dans la sonde d’intégrité. Ce test ping est envoyé à chaque instance dans le pool back-end et sur le port défini dans la sonde d’intégrité. Pour les sondes HTTP et HTTPS, un test ping réussi passe par une réponse HTTP 200 OK, tandis qu’avec les sondes TCP, toutes les réponses sont considérées réussies. Les réussites ou échecs consécutifs de chaque sonde déterminent alors si une instance back-end est saine et en mesure de recevoir du trafic pour les règles d’équilibrage de charge auxquelles le pool back-end est affecté. Comme pour la disponibilité du chemin des données, nous utilisons l’agrégation moyenne, qui indique la moyenne des tests ping réussis/totaux pendant l’intervalle d’échantillonnage. Cette valeur d’état de la sonde d’intégrité indique l’intégrité du back-end isolé de l’équilibreur de charge en sondant les instances back-end sans envoyer de trafic via le front-end.

>[!IMPORTANT]
>L’état de la sonde d’intégrité est échantillonné toutes les minutes. Cela peut occasionner de légères fluctuations dans une valeur sinon stable. Par exemple, s’il existe deux instances back-end, une en service et l’autre hors service, le service de sonde d’intégrité peut capturer sept échantillons pour l’instance saine et six pour l’instance non saine. Ainsi, une valeur auparavant stable de 50 passe à 46,15 pendant une minute. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnostiquer les équilibrages de charge dégradés et indisponibles
Comme indiqué dans l[’article sur l’intégrité des ressources](load-balancer-standard-diagnostics.md#resource-health-status), un équilibreur de charge dégradé présente un taux de disponibilité du chemin des données compris entre 25 % et 90 % ; pour un équilibreur de charge non disponible, il est inférieur à 25 % sur une période de deux minutes. Vous pouvez suivre ces mêmes étapes pour étudier la défaillance mentionnée dans les alertes d’état de la sonde d’intégrité ou de disponibilité du chemin de données que vous avez configurées. Nous allons nous pencher sur le cas où la vérification de l’intégrité de nos ressources révèle que notre équilibreur de charge était indisponible avec une disponibilité du chemin de données de 0 % (service indisponible).

Pour commencer, accédons à la vue détaillée des métriques du panneau d’insights de l’équilibreur de charge. Pour cela, il convient de passer par le panneau de ressources de l’équilibreur de charge ou par le lien figurant dans le message d’intégrité des ressources.  Accédons ensuite à l’onglet Disponibilité front-end et back-end et analysons une fenêtre de trente minutes de la période où l’état détérioré ou indisponible est apparu. Si nous constatons que la disponibilité du chemin de données est de 0 %, nous savons qu’un problème empêche le trafic pour l’ensemble de nos règles NAT de trafic entrant et d’équilibrage de charge et nous pouvons déterminer la durée de cet impact. 

Le prochain élément auquel nous devons nous intéresser est la métrique d’état de la sonde d’intégrité afin de déterminer si le chemin des données est indisponible, car nous ne disposons d’aucune instance back-end saine pour gérer le trafic. S’il y a au moins une instance back-end saine pour l’ensemble de nos règles d’équilibrage de charge et de trafic entrant, nous savons que ce n’est pas notre configuration qui est à l’origine de l’indisponibilité de nos chemins de données. Ce scénario indique l’existence d’un rare problème sur la plateforme Azure. Ne vous inquiétez pas, car une alerte automatisée est envoyée à notre équipe pour résoudre rapidement tous les problèmes de plateforme.

## <a name="diagnose-health-probe-failures"></a>Diagnostiquer les échecs de la sonde d’intégrité
Supposons que nous vérifions l’état de la sonde d’intégrité et que nous découvrons que toutes les instances sont présentées comme étant non saines. Cette découverte explique la raison pour laquelle notre chemin de données est indisponible, car le trafic ne peut aller nulle part. Nous devons parcourir la liste de vérification suivante pour exclure les erreurs de configuration :
* Vérifier l’utilisation du processeur pour les ressources de façon à contrôler si en fait les instances sont saines
  * Vous pouvez vérifier cela 
* Si une sonde HTTP ou HTTPS est utilisée, vérifier si l’application est saine et réactive
  * Vérifiez qu’elle est opérationnelle en accédant directement aux applications via l’adresse IP privée ou l’adresse IP publique au niveau de l’instance associée à votre instance back-end
* Passer en revue les groupes de sécurité réseau appliqués aux ressources back-end. Vérifier qu’il n’existe pas de règles dont la priorité est supérieure à celle de AllowAzureLoadBalancerInBound et qui bloqueraient la sonde d’intégrité
  * Pour ce faire, vous pouvez accéder au panneau Réseau de vos groupes de machines virtuelles identiques ou machines virtuelles back-end
  * Si vous constatez que ce problème de groupe de sécurité réseau est bien le cause, déplacez la règle d’autorisation existante ou créez une règle de haute priorité pour autoriser le trafic AzureLoadBalancer
* Vérifier le système d’exploitation. Vérifier que les machines virtuelles écoutent le port de la sonde et examiner leurs règles de pare-feu du système d’exploitation pour s’assurer qu’elles ne bloquent pas le trafic de la sonde en provenance de l’adresse IP `168.63.129.16`
  * Vous pouvez vérifier les ports d’écoute en exécutant `netstat -a` à l’invite de commandes Windows ou `netstat -l` à partir d’un terminal Linux
* Ne pas placer une machine virtuelle d’appliance virtuelle réseau de pare-feu dans le pool back-end de l’équilibreur de charge, utiliser des [routes définies par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md#user-defined) pour router le trafic vers des instances back-end via le pare-feu
* Vérifier que vous utilisez le bon protocole ; si vous utilisez HTTP pour sonder un port à l’écoute d’une application non HTTP, la sonde échoue

Si, après avoir parcouru cette liste de vérification, vous continuez de trouver des échecs de sonde d’intégrité, il est possible que des problèmes de plateforme rares affectent le service de sondage de vos instances. Dans ce cas, vous pouvez compter sur Azure : une alerte automatisée est envoyée à notre équipe pour résoudre rapidement tous les problèmes de plateforme.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur la sondes d’intégrité Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [En savoir plus sur les métriques Azure Load Balancer](load-balancer-standard-diagnostics.md)
