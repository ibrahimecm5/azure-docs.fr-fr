---
title: Protections supplémentaires contre les menaces de Microsoft Defender pour le cloud
description: En savoir plus sur les protections contre les menaces qui sont fournies par Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: e240294c82e283e74f0b8c0ed24432be3ffa8506
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460581"
---
# <a name="additional-threat-protections-in-microsoft-defender-for-cloud"></a>Protections supplémentaires contre les menaces de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En plus des [plans de protection avancés qui sont intégrés à Microsoft Defender](defender-for-cloud-introduction.md), Microsoft Defender pour le cloud offre les fonctionnalités de protection suivantes.

> [!TIP]
> Si vous souhaitez activer les fonctionnalités de protection contre les menaces de Defender pour le cloud, vous devez activer les fonctionnalités de sécurité améliorée dans l’abonnement qui contient les charges de travail applicables.

## <a name="threat-protection-for-azure-network-layer"></a>Protection contre les menaces pour la couche réseau Azure <a name="network-layer"></a>
L’analytique de la couche réseau de Defender pour le cloud est basée sur des exemples de [données IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), qui sont des en-têtes de paquets collectés par des routeurs de base Azure. En fonction de ce flux de données, les modèles Machine Learning de Defender pour le cloud identifient et signalent les activités de trafic malveillantes. Defender pour le cloud utilise également la base de données Microsoft Threat Intelligence pour enrichir les adresses IP.

Certaines configurations réseau empêchent Defender pour le cloud de générer des alertes en cas d’activité réseau suspecte. Pour que Defender pour le cloud génère des alertes réseau, faites en sorte que :
- Votre machine virtuelle a une adresse IP publique (ou se trouve sur un équilibreur de charge avec une adresse IP publique).
- Le trafic de sortie réseau de votre machine virtuelle n’est pas bloqué par une solution IDS externe.

Pour obtenir la liste des alertes de la couche réseau Azure, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Protection contre les menaces pour Azure Cosmos DB (préversion)<a name="cosmos-db"></a>

Les alertes Azure Cosmos DB sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB.

Pour plus d'informations, consultez les pages suivantes :

* [Protection avancée contre les menaces pour Azure Cosmos DB (préversion)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Liste des alertes de protection contre les menaces pour Azure Cosmos DB (préversion)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-defender-for-cloud-recommendations-in-microsoft-defender-for-cloud-apps"></a>Afficher les recommandations de Defender pour le cloud dans Microsoft Defender pour les applications cloud <a name="azure-mcas"></a>

Microsoft Defender pour applications cloud (anciennement Microsoft Cloud App Security) est un répartiteur CASB qui prend en charge différents modes de déploiement, y compris la collecte de journaux, les connecteurs API et le proxy inverse. Il offre une grande visibilité, un contrôle des déplacements des données, et des analyses sophistiquées pour identifier et combattre les cybermenaces sur l'ensemble de vos services cloud Microsoft et tiers.

Si vous avez activé Microsoft Defender pour les applications cloud et sélectionné l’intégration dans les paramètres de Defender pour le cloud, les recommandations en matière de durcissement de la sécurité de Defender pour le cloud s’afficheront dans Microsoft Defender pour les applications cloud sans que vous ayez à effectuer une configuration supplémentaire.

> [!NOTE]
> Defender pour le cloud stocke les données client relatives à la sécurité dans la même zone géographique que la ressource. Si Microsoft n’a pas encore déployé Defender pour le cloud dans la zone géographique de la ressource, il stocke les données dans la région États-Unis. Quand Microsoft Defender pour les applications cloud est activé, ces informations sont stockées conformément aux règles d’emplacement géographique de Microsoft Defender pour les applications cloud. Pour plus d’informations, consultez [Stockage des données pour les services non régionaux](https://azuredatacentermap.azurewebsites.net/).


## <a name="stream-security-alerts-from-other-microsoft-services"></a>Envoyer en streaming des alertes de sécurité à partir d’autres services Microsoft <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-defender-for-cloud"></a>Afficher les alertes Azure WAF dans Defender pour le cloud <a name="azure-waf"></a>

Azure Application Gateway propose un pare-feu d’applications web (WAF, Web Application Firewall) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploitations courantes.

Les applications web sont de plus en plus visées par des attaques malveillantes qui exploitent des vulnérabilités connues. Le pare-feu d’applications web d’Application Gateway suit l’ensemble des règles de base 3.0 ou 2.2.9 d’Open Web Application Security Project. La solution WAF est mise à jour automatiquement pour offrir une protection contre les nouvelles vulnérabilités. 

Si vous avez une licence pour Azure WAF, vos alertes WAF seront envoyées en streaming vers Defender pour le cloud sans configuration supplémentaire. Pour plus d’informations sur les alertes générées par le pare-feu d’applications web, consultez [Règles et groupes de règles CRS de pare-feu d’applications web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="display-azure-ddos-protection-alerts-in-defender-for-cloud"></a>Afficher les alertes Azure DDoS Protection dans Defender pour le cloud <a name="azure-ddos"></a>

Les attaques DDoS (déni de service distribué) sont connues pour être faciles à exécuter. Elles sont devenues un problème de sécurité majeur, en particulier si vous déplacez vos applications vers le cloud. Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent cibler n’importe quel point de terminaison accessible sur Internet.

Pour vous défendre contre les attaques DDoS, achetez une licence Azure DDoS Protection, puis veillez à suivre les bonnes pratiques de conception d’applications. DDoS Protection fournit différents niveaux de service. Pour plus d’informations, consultez [Vue d’ensemble du service Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md).

Si Azure DDoS Protection est activé, vos alertes DDoS seront envoyées en streaming vers Defender pour le cloud sans aucune configuration supplémentaire. Pour plus d’informations sur les alertes générées par le service Protection DDoS, consultez le [Tableau de référence des alertes](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les alertes de sécurité provenant de ces fonctionnalités de protection contre les menaces, consultez les articles suivants :

* [Table de référence de toutes les alertes Defender pour le cloud](alerts-reference.md)
* [Alertes de sécurité dans Defender pour le cloud](alerts-overview.md)
* [Gérer les alertes de sécurité et y répondre dans Defender pour le cloud](managing-and-responding-alerts.md)
* [Exportation continue des données Defender pour le cloud](continuous-export.md)
