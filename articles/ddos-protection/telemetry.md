---
title: Afficher et configurer la télémétrie de la protection DDoS pour Azure DDoS Protection Standard
description: Découvrez comment afficher et configurer la télémétrie de la protection DDoS pour Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 728943aa79e208eb2d6cd6dcdcfaf7876033e2f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347405"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Afficher et configurer la télémétrie de la protection DDoS

La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. La télémétrie enrichie est exposée via Azure Monitor et inclut des métriques détaillées pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut également être intégrée à [Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics et Stockage Azure pour l’analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Afficher la télémétrie de la protection DDoS
> * Voir les stratégies d’atténuation des risques liés à DDoS
> * Valider et tester la télémétrie de la protection DDoS

### <a name="metrics"></a>Mesures

Les noms des métriques présentent différents types de paquets et les octets/paquets, et les noms de balise sur chaque métrique sont essentiellement construits comme suit :

- **Nom de balise des paquets supprimés** (par exemple, **Paquets entrants ignorés DDoS) :** nombre de paquets supprimés/nettoyés par le système de protection DDoS.
- **Nom de balise des paquets transférés** (par exemple, **Paquets entrants transférés DDoS) :** nombre de paquets transférés par le système DDoS vers l’adresse IP virtuelle de destination (trafic qui n’a pas été filtré).
- **Nom de balise du nombre de paquets** (par exemple, **Paquets entrants DDoS**) : nombre total de paquets ayant atteint le système de nettoyage, représentant la somme des paquets supprimés et transférés.

> [!NOTE]
> Bien que plusieurs options pour **Agrégation** soient affichées sur le portail Azure, seuls les types d’agrégation répertoriés dans le tableau ci-dessous sont pris en charge pour chaque mesure. Nous vous prions de nous excuser pour cette confusion et nous travaillons à résoudre cette erreur.

Les [mesures](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) suivantes sont disponibles pour Azure DDoS Protection standard. Ces mesures sont également exportables via les paramètres de diagnostic (consultez [Afficher et configurer la journalisation des diagnostics DDoS](diagnostic-logging.md)).


| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Octets entrants supprimés DDoS | BytesPerSecond | Maximale | Octets entrants supprimés DDoS| 
| BytesForwardedDDoS | Octets entrants transférés DDoS | BytesPerSecond | Maximale | Octets entrants transférés DDoS |
| BytesInDDoS | Octets entrants DDoS | BytesPerSecond | Maximale | Octets entrants DDoS |
| DDoSTriggerSYNPackets | Paquets SYN entrants pour déclencher l’atténuation DDoS | CountPerSecond | Maximale | Paquets SYN entrants pour déclencher l’atténuation DDoS |
| DDoSTriggerTCPPackets | Paquets TCP entrants pour déclencher l’atténuation DDoS | CountPerSecond | Maximale | Paquets TCP entrants pour déclencher l’atténuation DDoS |
| DDoSTriggerUDPPackets | Paquets UDP entrants pour déclencher l’atténuation DDoS | CountPerSecond | Maximale | Paquets UDP entrants pour déclencher l’atténuation DDoS |
| IfUnderDDoSAttack | Sous attaque DDoS ou non | Count | Maximale | Sous attaque DDoS ou non |
| PacketsDroppedDDoS | Paquets entrants ignorés DDoS | CountPerSecond | Maximale | Paquets entrants ignorés DDoS |
| PacketsForwardedDDoS | Paquets entrants transférés DDoS | CountPerSecond | Maximale | Paquets entrants transférés DDoS |
| PacketsInDDoS | Paquets entrants DDoS | CountPerSecond | Maximale | Paquets entrants DDoS |
| TCPBytesDroppedDDoS | Octets TCP entrants supprimés DDoS | BytesPerSecond | Maximale | Octets TCP entrants supprimés DDoS |
| TCPBytesForwardedDDoS | Octets TCP entrants transférés DDoS | BytesPerSecond | Maximale | Octets TCP entrants transférés DDoS |
| TCPBytesInDDoS | Octets TCP entrants DDoS | BytesPerSecond | Maximale | Octets TCP entrants DDoS |
| TCPPacketsDroppedDDoS | Paquets TCP entrants TCP ignorés DDoS | CountPerSecond | Maximale | Paquets TCP entrants TCP ignorés DDoS |
| TCPPacketsForwardedDDoS | Paquets TCP entrants transférés DDoS | CountPerSecond | Maximale | Paquets TCP entrants transférés DDoS |
| TCPPacketsInDDoS | Paquets TCP entrants DDoS | CountPerSecond | Maximale | Paquets TCP entrants DDoS |
| UDPBytesDroppedDDoS | Octets UDP entrants supprimés DDoS | BytesPerSecond | Maximale | Octets UDP entrants supprimés DDoS |
| UDPBytesForwardedDDoS | Octets UDP entrants transférés DDoS | BytesPerSecond | Maximale | Octets UDP entrants transférés DDoS |
| UDPBytesInDDoS | Octets UDP entrants DDoS | BytesPerSecond | Maximale | Octets UDP entrants DDoS |
| UDPPacketsDroppedDDoS | Paquets UDP entrants ignorés DDoS | CountPerSecond | Maximale | Paquets UDP entrants ignorés DDoS |
| UDPPacketsForwardedDDoS | Paquets UDP entrants transférés DDoS | CountPerSecond | Maximale | Paquets UDP entrants transférés DDoS |
| UDPPacketsInDDoS | Paquets UDP entrants DDoS | CountPerSecond | Maximale | Paquets UDP entrants DDoS |

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de pouvoir effectuer les étapes de ce didacticiel, vous devez d’abord créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md), et la protection DDoS Standard doit être activée sur un réseau virtuel.
- Le service DDoS surveille les adresses IP publiques affectées aux ressources dans un réseau virtuel. Si aucune ressource dans le réseau virtuel ne possède une adresse IP publique, vous devez d’abord créer une ressource dotée d’une adresse IP publique. Vous pouvez surveiller l’IP publique de toutes les ressources déployées par le biais de Resource Manager (non Classic) qui figurent dans [Réseau virtuel pour services Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (y compris les équilibreurs de charge Azure pour lesquels les machines virtuelles principales se trouvent dans le réseau virtuel), à l’exception des instances Azure App Service Environment. Pour poursuivre ce tutoriel, vous pouvez créer rapidement une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-ddos-protection-telemetry"></a>Afficher la télémétrie de la protection DDoS

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Bien que les [déclencheurs d'atténuation](#view-ddos-mitigation-policies) pour TCP SYN, TCP & UDP soient disponibles pendant les périodes de repos, d’autres données de télémétrie sont uniquement disponibles quand une adresse IP publique a été atténuée. 

Vous pouvez afficher les données de télémétrie DDoS pour une adresse IP publique protégée par le biais de trois types de ressources différents : le plan de protection DDoS, le réseau virtuel et l’adresse IP publique.

### <a name="ddos-protection-plan"></a>Plan de protection DDoS
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre plan de protection DDoS.
2. Sous **Supervision**, sélectionnez **Métriques**.
3. Sélectionnez **Étendue**. Sélectionnez l’**Abonnement** qui contient l’adresse IP publique que vous souhaitez journaliser, sélectionnez **Adresse IP publique** comme **Type de ressource**, sélectionnez l’adresse IP publique spécifique pour laquelle vous souhaitez enregistrer les mesures, puis sélectionnez **Appliquer**.
4. Définissez le type **d’agrégation** sur **Max**. 

### <a name="virtual-network"></a>Réseau virtuel
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez au réseau virtulel sur lequel est activé votre plan de protection DDoS.
2. Sous **Supervision**, sélectionnez **Métriques**.
3. Sélectionnez **Étendue**. Sélectionnez l’**Abonnement** qui contient l’adresse IP publique que vous souhaitez journaliser, sélectionnez **Adresse IP publique** comme **Type de ressource**, sélectionnez l’adresse IP publique spécifique pour laquelle vous souhaitez enregistrer les mesures, puis sélectionnez **Appliquer**.
4. Définissez le type **d’agrégation** sur **Max**.
5. Sélectionnez **Ajouter un filtre**. Sous **Propriété**, sélectionnez **Adresse IP protégée**, et l’opérateur doit être défini sur **=** . Sous **Valeurs**, vous verrez une liste déroulante d’IP publiques, associées au réseau virtuel, qui sont protégées par la protection DDoS activée. 

![Paramètres de diagnostic DDoS](./media/ddos-attack-telemetry/vnet-ddos-metrics.png)

### <a name="public-ip-address"></a>Adresse IP publique
1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre adresse IP publique.
2. Sous **Surveillance**, sélectionnez **Métriques**.
3. Définissez le type **d’agrégation** sur **Max**.

## <a name="view-ddos-mitigation-policies"></a>Voir les stratégies d’atténuation des risques liés à DDoS

La Protection Standard DDoS applique trois stratégies de prévention réglées automatiquement (TCP SYN, TCP et UDP) pour chaque adresse IP publique de la ressource protégée, dans le réseau virtuel sur lequel la protection DDoS est activée. Vous pouvez voir les seuils de stratégie en sélectionnant les métriques **Paquets TCP entrants pour déclencher l’atténuation des risques liés à DDoS** et **Paquets UDP entrants pour déclencher l’atténuation des risques liés à DDoS** avec le type **d’agrégation** défini sur « Max », comme indiqué dans l’image suivante :

![Voir les stratégies d’atténuation des risques](./media/manage-ddos-protection/view-mitigation-policies.png)

## <a name="validate-and-test"></a>Valider et tester

Pour simuler une attaque DDoS afin de valider la télémétrie de la protection DDoS, consultez [validation de la détection DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

- Configurer des alertes pour les métriques du service de protection DDoS
- Afficher la télémétrie de la protection DDoS
- Voir les stratégies d’atténuation des risques liés à DDoS
- Valider et tester la télémétrie de la protection DDoS

Pour savoir comment configurer les rapports de prévention des attaques et les journaux de flux, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Afficher et configurer la journalisation des diagnostics DDoS](diagnostic-logging.md)
