---
title: Fichier include
description: Fichier include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 10/29/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0f5bafd3bfa25691ee531c5e8aa60b4c70937815
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520928"
---
| Ressource | Limite |
| --- | --- |
| Débit de données |30 Gbits/s|
|Limites de règle|10 000 sources/destinations uniques dans les règles de réseau et d’application|
|Taille totale des règles au sein d’un groupe de collection de règles unique| 2 Mo|
|Nombre de groupes de collection de règles dans une stratégie de pare-feu|50|
|Nombre maximum de règles DNAT|298 (pour les pare-feu configurés avec une adresse IP publique unique)<br><br> La limitation DNAT est due à la plateforme sous-jacente. Le nombre maximal de règles DNAT est de 298. Cependant, toutes les adresses IP publiques supplémentaires réduisent le nombre de règles DNAT disponibles. Par exemple, deux adresses IP publiques autorisent 297 règles DNAT. Si le protocole d’une règle est configuré à la fois pour TCP et pour UDP, elle compte pour deux règles.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|1 - 65535|
|Adresses IP publiques|250 maximum. Toutes les adresses IP publiques peuvent être utilisées dans des règles DNAT et toutes contribuent aux ports SNAT disponibles.|
|Adresses IP dans les groupes IP|Maximum de 100 groupes d’adresses IP par pare-feu.<br>Maximum de 5 000 adresses IP individuelles ou préfixes IP pour chaque groupe IP.
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous autoriserons votre abonnement afin de garantir le maintien de la connectivité Internet du pare-feu.|
|Noms FQDN dans les règles réseau|Pour de bonnes performances, ne dépassez pas plus de 1 000 noms FQDN dans toutes les règles réseau par pare-feu.|