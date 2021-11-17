---
title: Analyse de la passerelle VPN Azure
description: Découvrez comment afficher les métriques de la passerelle VPN à l’aide de Azure Monitor.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/08/2021
ms.author: alzam
ms.openlocfilehash: 8ae6519881dd0e41cde8ed0fa4d7ffc64f35bdf2
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058786"
---
# <a name="monitoring-vpn-gateway"></a>Supervision de la passerelle VPN

Vous pouvez surveiller les passerelles VPN Azure à l’aide d’Azure Monitor. Cet article présente les métriques disponibles via le portail. Les métriques sont légères et peuvent prendre en charge des scénarios en quasi-temps réel, ce qui les rend utiles pour les alertes et la détection rapide de problèmes.


| **Mesure**                                 | **Unité**     | **Granularité**     | **Description**                                                                         |
| -------------------------------------------| ------------ | ------------------- | --------------------------------------------------------------------------------------- |
| **État du pair BGP**                        | Count        | 5 minutes           | État de connectivité BGP moyen par homologue et par instance.                              |
| **Itinéraires BGP publiés**                  | Count        | 5 minutes           | Nombre de routes publiées par pair et par instance.                                  |
| **Itinéraires BGP appris**                     | Count        | 5 minutes           | Nombre de routes apprises par pair et par instance.                                     |
| **Bande passante P2S de passerelle**                  | octets/s      | 1 minute            | Utilisation moyenne de la bande passante combinée pour toutes les connexions de point à site sur la passerelle. |
| **Bande passante S2S de passerelle**                  | octets/s      | 5 minutes           | Utilisation moyenne de la bande passante combinée pour toutes les connexions de site à site sur la passerelle.  |
| **Nombre de connexions P2S**                   | Count        | 1 minute            | Nombre de connexions point à site sur la passerelle.                                      |
| **Bande passante de tunnel**                       | octets/s      | 5 minutes           | Utilisation moyenne de la bande passante pour les tunnels créés sur la passerelle.                        |
| **Octets de sortie de tunnel**                    | Octets        | 5 minutes           | Nombre d’octets sortants d’un tunnel.                                                 |
| **Nombre d’annulations de paquets de sortie par tunnel**        | Count        | 5 minutes           | Nombre de paquets sortants abandonnés par un tunnel.                                         |
| **Paquets de sortie de tunnel**                  | Count        | 5 minutes           | Nombre de paquets sortants d’un tunnel.                                               |
| **Rejet de paquets TS de sortie de tunnel pour incompatibilité**  | Count        | 5 minutes           | Nombre de paquets sortants abandonnés par des tunnels en raison d’une incompatibilité de sélecteur de trafic.      |
| **Octets d’entrée de tunnel**                   | Octets        | 5 minutes           | Nombre d’octets entrants dans un tunnel.                                                   |
| **Nombre d’annulations de paquets d’entrée par tunnel**       | Count        | 5 minutes           | Nombre de paquets entrants abandonnés par un tunnel.                                         |
| **Paquets en entrée de tunnel**                 | Count        | 5 minutes           | Nombre de paquets entrants dans un tunnel.                                                 |
| **Rejet de paquets TS d’entrée de tunnel pour incompatibilité** | Count        | 5 minutes           | Nombre de paquets entrants abandonnés par des tunnels en raison d’une incompatibilité de sélecteur de trafic.      |
| **Nombre de tunnels MMSA**                      | Count        | 5 minutes           | Nombre d’associations de sécurité en mode principal présentes.                                      |
| **Pic PPS dans le tunnel**                        | Count        | 5 minutes           | Nombre maximal de paquets par seconde par tunnel.                                            |
| **Nombre de tunnels QMSA**                      | Count        | 5 minutes           | Nombre d’associations de sécurité en mode rapide présentes.                                     |
| **Nombre total de flux d’un tunnel**                | Count        | 5 minutes           | Nombre de flux distincts créés par tunnel.                                            |
| **Nombre d’itinéraires VPN utilisateur**                   | Count        | 5 minutes           | Nombre d’itinéraires VPN utilisateur configurés sur la passerelle VPN.                                |
| **Nombre de préfixes d’adresse de réseau virtuel**              | Count        | 5 minutes           | Nombre de préfixes d’adresse de réseau virtuel utilisés/publiés par la passerelle.                |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Les étapes suivantes vous aident à localiser et afficher des métriques

1. Dans le portail, accédez à la ressource de la passerelle de réseau virtuel.
2. Sélectionnez **Vue d’ensemble** pour afficher les métriques d’entrée et de sortie du tunnel total.

   ![Sélections pour la création d’une règle d’alerte](./media/monitor-vpn-gateway/overview.png "Affichage")

3. Pour afficher les autres métriques répertoriées ci-dessus. Cliquez sur la section **Métriques** sous votre ressource de passerelle de réseau virtuel, puis sélectionnez la métrique dans la liste déroulante.

   ![Bouton Sélectionner et passerelle VPN dans la liste des ressources](./media/monitor-vpn-gateway/metrics.png "Sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les métriques de tunnel, consultez [Configurer des alertes sur les métriques de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Pour configurer des alertes sur les journaux de ressources de tunnel, consultez [Configurer des alertes sur les journaux de ressources de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
