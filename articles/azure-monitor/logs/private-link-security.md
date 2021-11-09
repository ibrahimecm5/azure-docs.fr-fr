---
title: Utiliser Azure Private Link pour connecter des réseaux à Azure Monitor
description: Configurez une étendue de liaison privée Azure Monitor pour connecter en toute sécurité des réseaux aux Azure Monitor.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 872a9c1f58974f1394286a6dd8f2a1c35892930c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078177"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Utiliser Azure Private Link pour connecter des réseaux à Azure Monitor

Avec [Azure Private Link](../../private-link/private-link-overview.md), vous pouvez lier de manière sécurisée les ressources de la plateforme Azure en tant que service (PAA) à votre réseau virtuel en utilisant des points de terminaison privés. Azure Monitor est une constellation de différents services interconnectés qui fonctionnent ensemble pour surveiller vos charges de travail. Une liaison privée Azure Monitor connecte un point de terminaison privé à un ensemble de ressources Azure Monitor, définissant ainsi les limites de votre réseau de surveillance. Cet ensemble est appelé étendue de liaison privée Azure Monitor (Azure Monitor Private Link Scope, AMPLS).


## <a name="advantages"></a>Avantages

Grâce à Azure Private Link, vous pouvez :

- Vous connecter en privé à Azure Monitor sans ouvrir d’accès au réseau public
- Vérifier que vos données de surveillance sont accessibles uniquement par le biais de réseaux privés autorisés
- Empêcher l’exfiltration de données à partir de vos réseaux privés en définissant les ressources Azure Monitor spécifiques qui se connectent via votre point de terminaison privé
- Connecter en toute sécurité votre réseau local privé à Azure Monitor à l’aide d’ExpressRoute et d’Azure Private Link
- Conserver tout le trafic au sein du réseau principal Microsoft Azure

Pour plus d’informations, consultez [Principaux avantages d’Azure Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Fonctionnement

### <a name="overview"></a>Vue d’ensemble
Une étendue de liaison privée Azure Monitor connecte des points de terminaison privés (et les réseaux virtuels dans lesquels ils se trouvent) à une ou plusieurs ressources Azure Monitor telles que des espaces de travail Log Analytics et des composants Application Insights.

![Schéma de la topologie de base des ressources](./media/private-link-security/private-link-basic-topology.png)

* Une liaison privée Azure Monitor connecte un point de terminaison privé à un ensemble de ressources Azure Monitor (espaces de travail Log Analytics et ressources Application Insights). Cet ensemble est appelé étendue de liaison privée Azure Monitor (Azure Monitor Private Link Scope, AMPLS).
* Le point de terminaison privé de votre réseau virtuel lui permet d’atteindre des points de terminaison Azure Monitor par le biais d’adresses IP privées à partir du pool de votre réseau, au lieu d’utiliser les adresses IP publiques de ces points de terminaison. Cela vous permet de continuer à utiliser vos ressources Azure Monitor sans ouvrir votre réseau virtuel à un trafic sortant non requis. 
* Le trafic du point de terminaison privé vers vos ressources Azure Monitor passe par le réseau principal Microsoft Azure et n’est pas acheminé vers des réseaux publics.
* Vous pouvez configurer votre étendue de liaison privée Azure Monitor (ou des réseaux spécifiques s’y connectant) pour utiliser le mode d’accès préféré, c’est-à-dire soit autoriser le trafic uniquement vers des ressources Private Link, soit autoriser le trafic vers des ressources Private Link et des ressources non-Private Link (ressources extérieures à l’étendue de liaison privée Azure Monitor)
* Vous pouvez configurer chacun de vos espaces de travail ou composants afin d’autoriser ou de refuser l’ingestion et les requêtes provenant de réseaux publics. Vous bénéficiez ainsi d’une protection au niveau des ressources, ce qui vous permet de contrôler le trafic vers des ressources spécifiques.

> [!NOTE]
> Un réseau virtuel ne peut se connecter qu’à une seule étendue de liaison privée Azure Monitor, qui répertorie jusqu’à 50 ressources accessibles par le biais d’une liaison privée.

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Azure Monitor Private Link s’appuie sur votre DNS
Quand vous configurez une connexion Private Link, vos zones DNS sont définies pour mapper des points de terminaison Azure Monitor à des adresses IP privées, afin d’envoyer le trafic via la liaison privée. Azure Monitor utilise à la fois des points de terminaison spécifiques de la ressource et des points de terminaison régionaux ou globaux qui gèrent le trafic vers plusieurs espaces de travail/composants. En ce qui concerne les points de terminaison régionaux et globaux, la configuration d’une liaison privée (même pour une seule ressource) affecte le mappage DNS qui contrôle le trafic vers **toutes** les ressources. En d’autres termes, le trafic vers tous les espaces de travail ou composants peut être affecté par une simple configuration de liaison privée.

#### <a name="global-endpoints"></a>Points de terminaison globaux
Plus important encore, le trafic vers les points de terminaison globaux ci-dessous est envoyé via la liaison privée :
* Tous les points de terminaison Application Insights (les points de terminaison gèrent l’ingestion, les métriques en temps réel, le profileur, le débogueur, etc.) vers des points de terminaison Application Insights sont globaux.
* Point de terminaison de requête : le point de terminaison qui gère les requêtes pour Application Insights et Log Analytics est global.

Cela signifie que tout le trafic Application Insights est envoyé à la liaison privée, et que toutes les requêtes vers les ressources Application Insights et Log Analytics sont envoyées à la liaison privée.

Le trafic vers une ressource Application Insights non ajoutée à votre AMPLS ne réussira pas la validation de liaison privée et échouera.

#### <a name="resource-specific-endpoints"></a>Points de terminaison propres aux ressources
Tous les points de terminaison Log Analytics, à l’exception du point de terminaison de requête, sont spécifiques à l’espace de travail. Par conséquent, la création d’une liaison privée à un espace de travail Log Analytics spécifique n’affecte pas le trafic d’ingestion d’autres espaces de travail qui continuent à utiliser les points de terminaison publics.


> [!NOTE]
> Créez une seule étendue de liaison privée Azure Monitor pour tous les réseaux qui partagent le même DNS. La création de plusieurs ressources d’étendue de liaison privée Azure Monitor a pour effet que les zones DNS Azure Monitor se substituent les unes aux autres, et dégradent des environnements existants.

### <a name="private-link-access-modes-private-only-vs-open"></a>Modes d’accès Private Link : uniquement privé ou ouvert
Comme expliqué dans la section [Azure Monitor Private Link s’appuie sur votre DNS](#azure-monitor-private-link-relies-on-your-dns), une seule ressource d’étendue de liaison privée Azure Monitor doit être créée pour tous les réseaux qui partagent le même DNS. Par conséquent, les organisations qui utilisent un seul DNS global ou régional disposent en fait d’une liaison privée unique pour gérer le trafic vers toutes les ressources Azure Monitor, dans l’ensemble des réseaux globaux ou régionaux.

Pour les liaisons privées créés avant septembre 2021, cela signifie ce qui suit : 
* L’ingestion de journaux fonctionne uniquement pour les ressources dans l’étendue de liaison privée Azure Monitor. L’ingestion de toutes les autres ressources est refusée (sur tous les réseaux qui partagent le même DNS), quel que soit l’abonnement ou le locataire.
* Les requêtes ont un comportement plus ouvert, ce qui permet aux demandes de requête d’atteindre même des ressources ne figurant pas dans l’étendue de liaison privée Azure Monitor. L’intention était ici d’éviter de dégrader les requêtes des clients aux ressources ne figurant pas dans l’étendue de liaison privée Azure Monitor, et d’autoriser les requêtes centrées sur des ressources à retourner le jeu de résultats complet.

Toutefois, ce comportement s’est avéré trop restrictif pour certains clients (car il dégrade l’ingestion des ressources ne figurant pas dans l’étendue de liaison privée Azure Monitor), trop permissif pour d’autres (parce qu’il permet d’interroger des ressources ne figurant pas dans l’étendue de liaison privée Azure Monitor), et constituer généralement une source de confusion.

Par conséquent, les liaisons privées créés à partir de septembre 2021 ont de nouveaux paramètres d’étendue de liaison privée Azure Monitor obligatoires, qui définissent explicitement la manière dont les liaisons privées doivent affecter le trafic réseau. Lors de la création d’une ressource d’étendue de liaison privée Azure Monitor, vous devez désormais sélectionner les modes d’accès souhaités pour l’ingestion et les requêtes séparément. 
* Mode privé uniquement : autorise le trafic uniquement vers des ressources de liaison privée
* Mode ouvert : utilise une liaison privée pour communiquer avec des ressources dans l’étendue de liaison privée Azure Monitor, mais autorise également la continuation du trafic vers d’autres ressources. Pour en savoir plus, consultez [Control how Private Links apply to your networks](./private-link-design.md#control-how-private-links-apply-to-your-networks) (Contrôler la façon dont les liaisons privées s’appliquent à vos réseaux).

> [!NOTE]
> L’ingestion Log Analytics utilise des points de terminaison spécifiques d’une ressource. Par conséquent, elle n’adhère pas aux modes d’accès AMPLS. **Pour s'assurer que les requêtes d'ingestion de Log Analytics ne peuvent pas accéder aux espaces de travail hors de l'AMPLS, configurez le pare-feu du réseau pour bloquer le trafic vers les points de terminaison publics, indépendamment des modes d'accès de l'AMPLS**.

## <a name="next-steps"></a>Étapes suivantes
- [Conception de votre configuration Private Link](private-link-design.md)
- Apprenez à [Configurer votre liaison privée](private-link-configure.md)
- En savoir plus sur le [stockage privé](private-storage.md) pour les journaux personnalisés et les clés gérées par le client (CMK)
<h3><a id="connect-to-a-private-endpoint"></a></h3>
