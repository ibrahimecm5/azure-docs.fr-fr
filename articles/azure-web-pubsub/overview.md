---
title: Qu’est-ce que le service Azure Web PubSub ?
description: Approfondissez votre compréhension des cas d’usage classiques d’Azure Web PubSub et découvrez ses principaux avantages.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 11/08/2021
ms.openlocfilehash: 85ccd066fb51331575cc17cbeabbe3eb0c0ba4ec
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997397"
---
# <a name="what-is-azure-web-pubsub-service"></a>Qu’est-ce que le service Azure Web PubSub ? 

Le service Azure Web PubSub vous aide à créer facilement des applications web de messagerie en temps réel avec des WebSockets et le modèle publication-abonnement. Cette fonctionnalité en temps réel permet de publier des mises à jour de contenu entre un serveur et des clients connectés (par exemple, une application web ou mobile monopage). Les clients n’ont pas besoin d’interroger les dernières mises à jour ni d’envoyer de nouvelles requêtes HTTP pour obtenir les mises à jour.

Cet article offre une vue d’ensemble du service Azure Web PubSub.

## <a name="what-is-azure-web-pubsub-service-used-for"></a>À quoi sert le service Azure Web PubSub ?

Tout scénario qui nécessite une messagerie de type publication/abonnement en temps réel entre un serveur et des clients ou entre des clients peut avoir recours au service Azure Web PubSub. Les fonctionnalités en temps réel traditionnelles qui demandent souvent d’interroger un serveur ou d’envoyer des requêtes HTTP peuvent aussi utiliser le service Azure Web PubSub.

Le service Azure Web PubSub peut être utilisé pour tout type d’application nécessitant des mises à jour de contenu en temps réel. Voici quelques exemples d’utilisation du service Azure Web PubSub :

* **Mises à jour haute fréquence de données :** jeux, votes, sondages, enchères.
* **Tableaux de bord et supervision en direct :** tableau de bord d’entreprise, données des marchés financiers, mise à jour instantanée des ventes, tableaux multijoueurs et supervision IoT.
* **Conversation instantanée interplateforme :** visioconférence, bot conversationnel, support technique en ligne, assistant d’achat en temps réel, messagerie instantanée, conversation intégrée à un jeu, etc.
* **Localisation en temps réel sur une carte :** suivi logistique, suivi de l’état de livraison, mises à jour sur l’état de la circulation, applications GPS.
* **Publicités ciblées en temps réel :** publicités et offres push en temps réel personnalisées, publicités interactives.
* **Applications collaboratives :** rédaction conjointe, applications de tableau blanc et logiciels de réunion d’équipe.
* **Notifications instantanées de type Push :** réseau social, e-mail, jeux, alertes de voyage.
* **Diffusion en temps réel :** diffusion audio/vidéo en direct, sous-titrage en direct, traduction, diffusion d’événements/d’actualités.
* **IoT et appareils connectés :** mesures IoT en temps réel, contrôle à distance, statut en temps réel et suivi de localisation.
* **Automatisation :** déclenchement en temps réel à partir d’événements en amont.

## <a name="what-are-the-benefits-using-azure-web-pubsub-service"></a>Quels sont les avantages d’utiliser Azure Web PubSub ?

**Prise en charge intégrée des connexions client à grande échelle et des architectures hautement disponibles :**

Le service Azure Web PubSub est conçu pour les applications en temps réel à grande échelle. Il permet à plusieurs instances de travailler ensemble pour s’adapter à des millions de connexions client. En même temps, il prend aussi en charge plusieurs régions dans le monde pour le partitionnement, la haute disponibilité ou la reprise d’activité après sinistre.

**Prise en charge d’un large choix de SDK client et de langages de programmation :**

Le service Azure Web PubSub fonctionne avec un large choix de clients, comme les navigateurs web et mobiles, les applications de bureau, les applications mobiles, les processus serveur, les appareils IoT et les consoles de jeu. Étant donné que ce service prend en charge la connexion WebSocket standard avec le modèle publication-abonnement, il est facile d’utiliser un kit de développement logiciel (SDK) client WebSocket standard dans différents langages avec ce service. 

**Offrir des API riches pour des modèles de messagerie différents :**

Le service Azure Web PubSub est un service de messagerie bidirectionnelle qui autorise différents modèles de messagerie entre le serveur et les clients, par exemple :

* Le serveur envoie des messages à un client en particulier, à tous les clients ou un sous-ensemble de clients appartenant à un utilisateur spécifique ou qui ont été placées dans un groupe arbitraire. 
* Le client envoie des messages aux clients appartenant à un groupe arbitraire.
* Les clients envoient des messages au serveur.


## <a name="how-to-use-the-azure-web-pubsub-service"></a>Comment utiliser le service Azure Web PubSub ?

Il existe de nombreux modes de programmation avec Azure Web PubSub, notamment les exemples listés ici :

- **Générer des applications en temps réel serverless** : Utilisez l’intégration d’Azure Functions au service Azure Web PubSub pour générer des applications en temps réel serverless dans des langages comme JavaScript, C#, Java et Python. 
- **Utilisez le sous-protocole WebSocket pour n’appliquer PubSub que côté client** : le service Azure Web PubSub-fournit des sous-protocoles WebSocket pour permettre aux clients autorisés de publier sur d’autres clients de manière pratique.
- **Utilisez les kits de développement logiciel (SDK) pour gérer les connexions WebSocket dans des serveurs d’applications auto-hébergés** : le service Azure Web PubSub fournit des kits de développement logiciel (SDK) en C#, JavaScript, Java et Python pour gérer facilement les connexions WebSocket, y compris les messages de diffusion aux connexions, ajouter des connexions à certains groupes ou fermer les connexions, etc.
- **Envoyer des messages du serveur aux clients par le biais d’une API REST** : Le service Azure Web PubSub fournit une API REST pour permettre aux applications de publier des messages sur des clients connectés, dans n’importe quel langage de programmation compatible avec REST.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
