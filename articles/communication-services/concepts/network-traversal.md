---
title: Documentation conceptuelle sur Azure Communication Services Azure - Network Traversal
titleSuffix: An Azure Communication Services Concept Document
description: Apprenez-en davantage sur les SDK et les API REST Network Traversal Azure Communication Services.
author: rahulva
manager: shahen
services: azure-communication-services
ms.author: rahulva
ms.date: 09/20/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 02f52069cc9dea270cc8e6a34096af5bd80159d7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894941"
---
#  <a name="network-traversal-concepts"></a>Concepts Network Traversal   

Les relais en temps réel résolvent le problème de la traversée NAT (traduction d’adresses réseau) pour les connexions d’homologue à homologue (P2P). Aujourd’hui, la plupart des appareils sur Internet ont une adresse IP utilisée pour le trafic LAN interne (réseau privé ou d’entreprise) ou une adresse visible de l’extérieur (routeur ou passerelle NAT). Pour connecter deux appareils sur Internet, l’adresse externe est requise, mais elle n’est généralement pas disponible pour un appareil situé derrière une passerelle NAT. Pour résoudre le problème de connectivité, les protocoles suivants sont utilisés :

  STUN (Session Traversal Utility for NAT) offre un protocole qui permet aux appareils d’échanger des adresses IP externes sur Internet. Si les clients peuvent se voir mutuellement, il n’y a généralement pas besoin d’un relais via un service TURN puisque la connexion peut être établie d’homologue à homologue. Le travail d’un serveur STUN doit répondre à la demande de l’adresse IP externe d’un appareil.
  
  TURN (Traversal Using Relays around NAT) est une extension du protocole STUN qui relaie également les données entre deux points de terminaison via un serveur visible mutuellement.
        
## <a name="acs-network-traversal-overview"></a>Présentation Network Traversal ACS   

WebRTC (Web Real-Time technologies) permet aux navigateurs web de diffuser du contenu audio, vidéo et des données entre des appareils sans avoir besoin d’une passerelle au milieu. Voici quelques cas d’usage courants : voix, vidéo, diffusion et partage d’écran. Pour connecter deux points de terminaison sur Internet, leur adresse IP externe est requise. L’adresse IP externe n’est généralement pas disponible pour les appareils se trouvant derrière un pare-feu d’entreprise. Les protocoles tels que STUN (Session Traversal Utilities for NAT) et TURN (Traversal Using Relays around NAT) sont utilisés pour aider les points de terminaison à communiquer.

Azure Communication Services fournit des connexions à bande passante élevée et à faible latence entre les homologues pour les scénarios de communication en temps réel. Le service Network Traversal ACS héberge les serveurs TURN pour une utilisation avec les scénarios NAT. Le service Azure Real-Time Relay expose l’infrastructure STUN/TURN existante en tant qu’offre Azure de plateforme en tant que service (PaaS). Le service fournit des services STUN et TURN de bas niveau.  Les utilisateurs sont ensuite facturés proportionnellement à la quantité de données relayées. 


## <a name="next-steps"></a>Étapes suivantes :

* Pour une présentation de l’authentification, consultez [S’authentifier auprès d’Azure Communication Services](./authentication.md).
* Pour une présentation de l’acquisition de candidats de relais, consultez [Créer et gérer des jetons d’accès](../quickstarts/relay-token.md).
