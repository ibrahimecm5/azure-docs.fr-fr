---
title: Configurer des alertes sur les métriques de passerelle VPN Azure
description: Découvrez comment utiliser la Portail Azure pour configurer des alertes Azure Monitor en fonction des mesures pour les passerelles VPN de réseau virtuel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e17f86d08b5892c7df0a761e53e1f16dd43f127a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158305"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurer des alertes sur les métriques de passerelle VPN

Cet article vous aide à configurer des alertes sur les métriques de passerelle VPN Azure. Azure Monitor permet de configurer des alertes pour les ressources Azure. Vous pouvez définir des alertes pour les passerelles de réseau virtuel de type « VPN ».

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

## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurer des alertes Azure Monitor en fonction de métriques à l’aide du portail Azure

L’exemple suivant montre comment créera une alerte sur une passerelle pour :

- **Mesure :** TunnelAverageBandwidth
- **Condition :** Bande passante > 10 octets/seconde
- **Fenêtre :** 5 minutes
- **Action d’alerte :** E-mail



1. Accédez à la ressource de passerelle de réseau virtuel et sélectionnez **Alertes** dans l’onglet **Surveillance**. Ensuite, créez une règle d’alerte ou modifiez une règle d’alerte existante.

   ![Sélections pour la création d’une règle d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Créer")

2. Sélectionnez votre passerelle VPN en tant que ressource.

   ![Bouton Sélectionner et passerelle VPN dans la liste des ressources](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Sélectionnez")

3. Sélectionnez une métrique à configurer pour l’alerte.

   ![Métrique sélectionnée dans la liste des métriques](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Sélectionnez")
4. Configurez la logique du signal. Il existe trois composants :

    a. **Dimensions** : si la métrique a des dimensions, vous pouvez sélectionner des valeurs de dimension spécifiques afin que l’alerte évalue uniquement les données de cette dimension. Cela est facultatif.

    b. **Condition** : il s’agit de l’opération visant à évaluer la valeur de métrique.

    c. **Time** : spécifiez la granularité des données de métrique et la période pour laquelle évaluer l’alerte.

   ![Détails de la configuration de logique de signal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Sélectionnez")

5. Pour afficher les règles configurées, sélectionnez **Gérer les règles d’alerte**.

   ![Bouton de gestion des règles d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les journaux de ressources de tunnel, consultez [Configurer des alertes sur les journaux de ressources de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
