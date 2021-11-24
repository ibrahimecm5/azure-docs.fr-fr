---
title: Géorécupération d’urgence Azure Spring Cloud | Microsoft Docs
description: Découvrez comment protéger votre application Spring Cloud contre les pannes régionales
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 33ff5a9e2dcf0d4e8f62d38bc36811548f9d3b3c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484833"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Récupération d’urgence Azure Spring Cloud

**Cet article s'applique à :** ✔️ Java ✔️ C#

Cet article explique certaines stratégies que vous pouvez utiliser pour protéger vos applications dans Azure Spring Cloud contre les temps d’arrêt.  Chaque région ou centre de données peut subir un temps d’arrêt causé par des catastrophes régionales, mais une planification soigneuse peut atténuer l’impact sur vos clients.

## <a name="plan-your-application-deployment"></a>Planifier le déploiement de votre application

Les applications dans Azure Spring Cloud s’exécutent dans une région spécifique.  Azure fonctionne dans plusieurs zones géographiques à travers le monde. Une zone géographique Azure est une zone définie du monde contenant au moins une région Azure. Une région Azure est une zone géographique contenant un ou plusieurs centres de données.  Chaque région Azure est associée à une autre région au sein de la même région géographique, constituant ainsi des paires régionales. Azure sérialise les mises à jour de plateforme (maintenance planifiée) à travers les paires régionales, garantissant ainsi qu’une seule région de chaque paire est mise à jour à la fois. En cas de panne affectant plusieurs régions, au moins l’une des régions de chaque paire est prioritaire pour la récupération.

Pour garantir la haute disponibilité et la protection contre les sinistres, vous devez déployer vos applications Spring Cloud dans plusieurs régions.  Azure fournit une liste de [régions jumelées](../best-practices-availability-paired-regions.md) pour vous permettre de planifier vos déploiements Spring Cloud sur des paires régionales.  Nous vous recommandons de prendre en compte trois facteurs clés lors de la conception de votre architecture de micro-service : la disponibilité des régions, les régions jumelées Azure et la disponibilité du service.

* Disponibilité dans les régions :  Choisissez une zone géographique proche de vos utilisateurs pour réduire la latence réseau et la durée de transmission.
* Régions jumelées Azure :  Choisissez des régions jumelées au sein de votre zone géographique choisie pour garantir des mises à jour de plateforme coordonnées et des efforts de récupération hiérarchisés si nécessaire.
* Disponibilité du service :   Décidez si vos régions appairées doivent avoir un niveau de disponibilité chaud/chaud, chaud/tiède ou chaud/froid.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utiliser Azure Traffic Manager pour router le trafic

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) fournit un équilibreur de charge de trafic DNS qui peut répartir le trafic réseau entre plusieurs régions.  Utilisez Azure Traffic Manager pour diriger les clients vers l’instance de service Azure Spring Cloud la plus proche.  Pour des performances et une redondance optimales, dirigez tout le trafic d’application via Azure Traffic Manager avant de l’envoyer à votre service Azure Spring Cloud.

Si vous avez des applications dans Azure Spring Cloud qui s’exécutent dans plusieurs régions, utilisez Azure Traffic Manager pour contrôler le flux du trafic vers vos applications dans chaque région.  Définissez un point de terminaison Azure Traffic Manager pour chaque service à l’aide de l’adresse IP du service. Les clients doivent se connecter à un nom DNS Azure Traffic Manager qui pointe vers le service Azure Spring Cloud.  Azure Traffic Manager équilibre la charge du trafic sur les points de terminaison définis.  Si un incident survient dans un centre de données, Azure Traffic Manager dirige le trafic de cette région vers sa paire, garantissant ainsi la continuité du service.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Créer le service Azure Traffic Manager pour le service Azure Spring Cloud

1. Créez un service Azure Spring Cloud dans deux régions différentes.
Vous aurez besoin de deux instances du service Azure Spring Cloud déployées dans deux régions différentes (USA Est et Europe Ouest). Lancez une application existante dans Azure Spring Cloud à l’aide du Portail Azure pour créer deux instances du service. Chacune servira de point de terminaison principal et de point de terminaison de basculement pour le trafic.

**Informations sur les deux instances de service :**

| Nom du service | Emplacement | Application |
|--|--|--|
| service-sample-a | USA Est | passerelle / service d’authentification / service de compte |
| service-sample-b | Europe Ouest | passerelle / service d’authentification / service de compte |

2. Configurez un domaine personnalisé pour le suivi du service [document de domaine personnalisé](./tutorial-custom-domain.md) afin de configurer un domaine personnalisé pour ces deux instances de service existantes. Une fois la configuration réussie, les deux instances de service sont liées au domaine personnalisé : bcdr-test.contoso.com

3. Créez un gestionnaire de trafic et deux points de terminaison : [Créez un profil de gestionnaire de trafic à l’aide du portail Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Voici le profil du gestionnaire de trafic :
* Nom DNS du gestionnaire de trafic : `http://asc-bcdr.trafficmanager.net`
* Profils de point de terminaison :

| Profil | Type | Cible | Priority | Paramètres d’en-têtes personnalisés |
|--|--|--|--|--|
| Profil A de point de terminaison | Point de terminaison externe | service-sample-a.asc-test.net | 1 | hôte : bcdr-test.contoso.com |
| Profil B de point de terminaison | Point de terminaison externe | service-sample-b.asc-test.net | 2 | hôte : bcdr-test.contoso.com |

4. Créez un enregistrement CNAME dans la zone DNS : bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net.

5. L’environnement est maintenant entièrement configuré. Les clients doivent pouvoir accéder à l’application via : bcdr-test.contoso.com

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Déployer votre première application Spring Boot dans Azure Spring Cloud](./quickstart.md)
