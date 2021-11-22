---
title: Vue d’ensemble du service Protection DDos Standard Azure
description: Découvrez comment la norme Azure DDoS Protection, combinée avec les meilleures pratiques de conception d’application, fournit une protection contre les attaques DDoS.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 3d473dd2c2e587d3c9563ca7171f7105311fa59a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306393"
---
# <a name="azure-ddos-protection-standard-overview"></a>Vue d’ensemble du service Protection DDos Standard Azure

Les attaques par déni de service distribué (DDoS) représentent certains des problèmes de disponibilité et de sécurité majeurs auxquels sont confrontés les clients qui déplacent leurs applications vers le cloud. Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet.

Chaque propriété dans Azure est protégée par la protection DDoS (de base) d’Azure sans frais supplémentaires. De par son échelle et sa capacité, le réseau Azure déployé à l’échelle mondiale assure une défense contre les attaques courantes de la couche réseau. Cette défense est par ailleurs renforcée par le monitoring continu du trafic et l’atténuation en temps réel. DDoS Protection Basic ne nécessite aucun changement de la part de l’utilisateur au niveau de la configuration ou de l’application. DDoS Protection Basic permet de protéger tous les services Azure, notamment les services PaaS comme Azure DNS.

Azure DDoS Protection Standard, combiné aux meilleures pratiques de conception d’application, offre des fonctions d’atténuation DDoS améliorée contre les attaques DDoS. Cette solution s’adapte automatiquement pour protéger vos ressources Azure spécifiques dans un réseau virtuel. La protection est simple à activer sur n’importe quel réseau virtuel, nouveau ou existant, et ne nécessite aucun changement au niveau de l’application ou des ressources. Elle offre plusieurs avantages par rapport au service de base, notamment la journalisation, la création d’alertes et la télémétrie. 

![Comparaison du service Azure DDos Protection](./media/ddos-protection-overview/ddos-comparison.png)

La protection DDoS d’Azure ne stocke pas les données client.

## <a name="features"></a>Fonctionnalités

- **Intégration de la plateforme native :** Intégré en natif dans Azure. Inclut la configuration par le biais du portail Azure. Le service Protection DDos Standard comprend vos ressources et leur configuration.
- **Protection clé en main :** La configuration simplifiée protège immédiatement toutes les ressources situées sur un réseau virtuel dès que DDoS Protection Standard est activé. Aucune définition ou intervention de l’utilisateur n’est nécessaire. 
- **Surveillance permanente du trafic :** Vos modèles de trafic d’application sont surveillés 24h/24 et 7j/7, à la recherche d’indicateurs d’attaques DDoS. Le service Protection DDoS Standard atténue de façon instantanée et automatique l’attaque une fois que celle-ci est détectée.
- **Optimisation adaptative :** Le profilage intelligent du trafic étudie le trafic de votre application au fil du temps pour sélectionner et mettre à jour le profil le plus adapté pour votre service. Le profil s’ajuste en fonction des modifications du trafic au fil du temps.
- **Protection multi-couches :** lorsqu’elle est déployée avec un pare-feu d’applications web (WAF), la Protection DDoS Standard offre une protection à la fois au niveau de la couche réseau (couche 3 et 4, assurée par Azure DDoS Protection Standard) et au niveau de la couche Application (couche 7, assurée par un pare-feu WAF). Les offres WAF incluent [la référence SKU du pare-feu d’applications web Azure Application Gateway](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ainsi que des offres tierces de pare-feu d’applications web disponibles sur [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Échelle de prévention étendue :** Plus de 60 types d’attaques différents peuvent être contrées, avec une protection globale contre les attaques DDoS les plus connues.
- **Analytique des attaques :** Recevez des rapports détaillés toutes les cinq minutes pendant une attaque, et un résumé complet une fois l’attaque terminée. Transmettez en continu les journaux de flux de prévention des attaques à [Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection) ou un système hors ligne de gestion des informations et des événements de sécurité (SIEM) pour une supervision en temps quasi-réel pendant une attaque.
- **Métriques des attaques :** Des métriques récapitulatives de chaque attaque sont accessibles via Azure Monitor.
- **Alerte d’attaque :** Vous pouvez configurer des alertes pour le début et la fin d’une attaque, ainsi que pendant qu’elle se produit, avec des métriques d’attaque intégrées. Les alertes s’intègrent à vos logiciels opérationnels, comme les journaux d’activité Microsoft Azure Monitor, Splunk, Stockage Azure, votre messagerie électronique et le portail Azure.
- **DDoS Rapid Response** : Faites appel à l’équipe de réponse rapide de la protection DDoS (DRR) pour faciliter l’étude et l’analyse des attaques. Pour en savoir plus, consultez [DDoS Rapid Response](ddos-rapid-response.md).
- **Maîtrise des coûts :** Recevez un crédit de service de transfert de données et de mise à l’échelle des applications pour les coûts de ressources résultant d’attaques DDoS documentées.

## <a name="pricing"></a>Tarifs

Les plans de protection DDoS présentent un coût mensuel fixe de 2 944 $ par mois, qui couvre jusqu’à 100 adresses IP publiques. Peuvent s’y ajouter 30 $ par ressource supplémentaire et par mois.

Sous un locataire, un même plan de protection DDoS peut être utilisé sur plusieurs abonnements. Il n’est donc pas nécessaire de créer plusieurs plans de protection DDoS.

Pour en savoir plus sur la tarification d’Azure DDoS Protection standard, consultez [Tarification d’Azure DDoS Protection Standard](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Architectures de référence

Le service DDoS Protection Standard est conçu [pour les services déployés dans un réseau virtuel](../virtual-network/virtual-network-for-azure-services.md). Pour les autres services, le service DDoS Protection Basic par défaut s’applique. Pour en savoir plus sur les architectures prises en charge, consultez [Architectures de référence de la protection DDoS](./ddos-protection-reference-architectures.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un plan de protection DDoS](manage-ddos-protection.md)
* [Module Learn : Présentation d’Azure DDoS Protection](/learn/modules/introduction-azure-ddos-protection/)
