---
title: Procédure pas à pas de peering Internet Azure pour services de communication
titleSuffix: Azure
description: Procédure pas à pas de peering Internet Azure pour services de communication
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: d17ae08067568098fffcdd443ba196c0023fbd42
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519342"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Procédure pas à pas de peering Internet Azure pour services de communication

Cette section explique les étapes qu’un fournisseur de services de communication doit suivre pour établir une interconnexion directe avec Microsoft.

**Fournisseurs de services de communication :** les fournisseurs de services de communication sont des organisations qui offrent des services de communication (communications, messagerie, conférence, etc.) et cherchent à intégrer leur infrastructure de services de communication (passerelle SBC/SIP, etc.) avec Azure Communication Services et Microsoft Teams. 

Le peering Internet Azure prend en charge les fournisseurs de services de communication pour établir une interconnexion directe avec Microsoft sur l’un de ses sites périphériques (emplacements de point de présence). La liste de tous les sites périphériques publics est disponible dans [PeeringDB](https://www.peeringdb.com/net/694).

Le peering Internet Azure offre une interconnexion hautement fiable et avec Qualité de service (QoS) pour les services de communication afin de garantir des services orientés performances et qualité.

## <a name="technical-requirements"></a>Exigences techniques
Les exigences techniques liées à l’établissement d’une interconnexion directe pour Azure Communication Services sont les suivantes :
-   L’homologue DOIT fournir son propre numéro de système autonome (ASN) qui DOIT être public.
-   L’homologue DOIT avoir une interconnexion redondante (PNI) à chaque emplacement d’interconnexion pour garantir la redondance locale.
-   L’homologue DOIT avoir une géoredondance en place pour garantir le basculement en cas de défaillance de site dans une région ou zone urbaine.
-   L’homologue DOIT avoir des sessions BGP de type Active-Active pour garantir une haute disponibilité et une convergence plus rapide, qui ne doivent pas être configurées en tant Principale et Secondaire.
-   L’homologue DOIT maintenir un ratio 1:1 pour appairer les routeurs de peering aux circuits de peering, et aucune limitation de débit n’est appliquée.
-   L’homologue DOIT fournir et publier son propre espace d’adressage IPv4 routable publiquement, utilisé par ses points de terminaison de service de communication (par exemple, SBC). 
-   L’homologue DOIT fournir des détails sur la classe de trafic et les points de terminaison hébergés dans chaque sous-réseau publié. 
-   L’homologue DOIT exécuter le protocole BGP sur le protocole BFD (Bidirectional Forwarding Detection) pour faciliter une convergence d’itinéraire en une fraction de seconde.
-   Tous les préfixes d’infrastructure de communication sont inscrits auprès du portail Azure et publiés avec la chaîne de communauté 8075:8007.
-   L’homologue Ne Doit Pas mettre fin au peering sur un appareil exécutant un pare-feu avec état. 
-   Microsoft configurera tous les liens d’interconnexion en tant que LAG (groupement de liaisons) par défaut. Ainsi, l’homologue doit prendre en charge le protocole LACP (Link Aggregation Control Protocol) sur les liaisons d’interconnexion.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Établissement d’une interconnexion directe avec Microsoft pour les services de communication.

Pour établir une interconnexion directe à l’aide d’un peering Internet Azure, suivez les étapes ci-dessous :

**1. Associez l’ASN public de l’homologue à l’abonnement Azure :**

Si l’homologue a déjà associé son ASN public à un abonnement Azure, ignorez cette étape.

[Associer l’ASN public de l’homologue à l’abonnement Azure à l’aide du portail – Azure | Microsoft Docs](./howto-subscription-association-portal.md)

L’étape suivante consiste à créer une connexion de peering directe pour le Peering Service.

> [!NOTE]
> Une fois l’association d’ASN approuvée, envoyez-nous un e-mail à l’adresse peeringservice@microsoft.com avec votre ASN et votre ID d’abonnement pour associer votre abonnement aux services de communication. 

**2. Créez une connexion de peering direct pour Peering Service :**

Suivez les instructions pour [Créer ou modifier un peering direct en utilisant le portail](./howto-direct-portal.md)

Veillez à ce qu’il réponde aux exigences de haute disponibilité.

Veillez à sélectionner les options suivantes sur la page « Créer un peering » :

Type de peering : **Direct**

Réseau Microsoft  : **8075 (avec Voice)**

Référence (SKU) : **Premium gratuit**


Sous « Direct Peering Connection Page », sélectionnez les options suivantes :

Fournisseur d’adresse de session : **Microsoft**

Utilisation pour les services de peering : **Activé**

> [!NOTE] 
> Ignorez le message suivant lors de la sélection de l’activation pour les services de peering.
> *N’activez que si vous avez contacté peering@microsoft.com pour devenir un fournisseur MAPS.*


  **2a. Utilisez une connexion de peering direct existante pour les services de peering**

Si vous disposez d’un peering direct que vous souhaitez utiliser pour prendre en charge Peering Service, vous pouvez l’activer sur le portail Azure.
1.  Suivez les instructions pour [Convertir un Peering direct existant en ressource Azure en utilisant le portail](./howto-legacy-direct-portal.md).
Si nécessaire, commandez des circuits supplémentaires pour répondre à l’exigence de haute disponibilité.

2.  Suivez les étapes pour [Activer Peering Service](./howto-peering-service-portal.md) sur un peering direct à l’aide du portail.




**3. Inscrivez vos préfixes pour un routage optimisé**

Pour optimiser le routage de vos préfixes d’infrastructure de services de communication, vous devez inscrire tous vos préfixes auprès de vos interconnexions de peering.
[Inscrire Azure Peering Service – Portail Azure | Microsoft Docs](../peering-service/azure-portal.md)

La clé préfixe étant automatiquement remplie pour les partenaires de service de communication, le partenaire n’a pas besoin d’utiliser une clé préfixe pour s’inscrire. 

Vérifiez que les préfixes en cours d’inscription sont annoncés sur les interconnexions directes établies pour la région.


## <a name="faqs"></a>Questions fréquentes (FAQ) :

**Q.**  J’ai des sous-réseaux plus petits (</24) pour mes services de communication. Est-il possible de les router également ?

**A.**  Oui, Microsoft Azure Peering Service prend également en charge le routage de préfixe plus petit. Veillez à inscrire les préfixes plus petits pour le routage. Ils sont annoncés sur les interconnexions.

**Q.**  Quels itinéraires Microsoft recevrons-nous sur ces interconnexions ?

**A.** Microsoft annonce tous ses préfixes de service publics sur ces interconnexions. Cela garantit que non seulement les communications, mais aussi d’autres services cloud, soient accessibles à partir de la même interconnexion.

**Q.**  Je dois définir la limite du préfixes. Combien d’itinéraires Microsoft annonce-t-il ?

**A.** Microsoft annonce environ 280 préfixes sur Internet et pourrait augmenter ce nombre de 10 à 15 % à l’avenir. Par conséquent, il peut être judicieux de définit une limite sûre de 400-500 comme nombre maximal de préfixes

**Q.** Est-ce que Microsoft republiera les préfixes d’homologue sur Internet ?

**A.** Non.

**Q.** Ce service est-il payant ?

**A.** Non, mais l’homologue est supposé assumer les coûts d’interconnexion de site.

**Q.** Quelle est la vitesse de liaison minimale d’une interconnexion ?

**A.** 10 Gbits/s.

**Q.** L’homologue est-il lié à un contrat SLA ?

**A.** Oui, quand l’utilisation atteint 40 %, un processus d’augmentation de LAG de 45 à 60 jours doit commencer.

**Q.** Quel est l’avantage de ce service par rapport au peering direct actuel ou à ExpressRoute ?

**A.** Le chemin gratuit et entier est optimisé pour le trafic vocal sur WAN Microsoft, et la convergence est réglée pour opérer en une fraction de seconde avec le protocole BFD.

**Q.** Combien de temps le processus d’intégration nécessite-t-il ?

**A.** Le temps varie en fonction du nombre et de l’emplacement des sites, et selon que l’homologue migre des peerings privés existants ou établit un nouveau câblage. L’opérateur doit prévoir plus de 3 semaines.

**Q.** Pouvez-vous utiliser des API pour l’intégration ?

**A.** Il n’existe actuellement aucune prise en charge de l’API et la configuration doit être effectuée via le portail web.
