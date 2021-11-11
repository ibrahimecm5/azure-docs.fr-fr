---
title: Modèle de facturation du service Azure Web PubSub
description: Vue d’ensemble des concepts clés relatifs au modèle de facturation du service Azure Web PubSub.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 7f937e833a909f15cdc86af35c27bf5240abcfd8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426826"
---
# <a name="billing-model-of-azure-web-pubsub-service"></a>Modèle de facturation du service Azure Web PubSub

Le modèle de facturation du service Azure Web PubSub est basé sur le nombre d’unités et la taille du trafic sortant (nombre de messages). Cet article explique la façon dont les unités et le trafic sortant (nombre de messages) sont définis et comptabilisés pour la facturation.

## <a name="how-units-are-counted-with-billing-model"></a>Comptage des unités par le modèle de facturation

### <a name="what-is-the-connection"></a>Qu’est-ce que la connexion ?

La connexion se fait entre le client et le service. Vous pouvez surveiller le nombre de connexions dans Portail Azure. La vue « Connexion (Max) » affiche le nombre maximal de connexions au cours d’une période spécifique. 

### <a name="what-is-the-unit"></a>Qu’est-ce que l’unité ?

L’unité est un concept abstrait de la capacité du service Azure Web PubSub. Chaque unité prend en charge 1 000 connexions simultanées au maximum. Chaque instance du service Azure Web PubSub peut avoir 1, 2, 5, 10, 20, 50 ou 100 unités. Le nombre d’unité indique donc le nombre de connexions que votre instance de service Web PubSub peut accepter.

###  <a name="how-units-are-counted-with-billing-model"></a>Comptage des unités par le modèle de facturation

Les unités sont comptées sur la base du nombre d’unités et du temps d’utilisation (en secondes) des unités et sont facturés quotidiennement. 

Par exemple, imaginez que vous disposez d’une instance du service Azure Web PubSub avec 5 unités, que vous effectuez un scale-up à 10 unités de 10 h à 16 h, puis que vous repassez à 5 unités après 16 h. Il en résulte 5 unités pendant 18 heures et 10 unités pendant 6 heures pour un jour spécifique.

> Utilisation des unités pour la facturation = (5 unités*18 heures + 10 unités*6 heures)/24 heures = 6,25 unités/jour

## <a name="how-outbound-traffic-is-counted-with-billing-model"></a>Comment le trafic sortant est-il comptabilisé avec le modèle de facturation ?

### <a name="what-is-inboundoutbound-traffic"></a>Qu’est-ce que le trafic entrant/sortant ? 

Le trafic sortant correspond aux messages envoyés à partir du service Azure Web PubSub. Vous pouvez surveiller le trafic sortant par le biais de Portail Azure. La vue « Trafic sortant (total) » affiche la taille agrégée des messages sortants (en octets) au cours d’une période spécifique.

- Les messages diffusés par le service aux récepteurs.
- Les messages envoyés par le service aux webhooks en amont.
- Les journaux de ressources avec l’[outil Live Trace](./howto-troubleshoot-resource-logs.md#capture-resource-logs-with-live-trace-tool). 

Le trafic entrant correspond aux messages envoyés au service Azure Web PubSub. 

- Les messages envoyés par les clients au service.
- Les messages envoyés par le serveur ou les fonctions au service.

### <a name="what-is-message-count"></a>Qu’est-ce que le nombre de messages ?

Aux fins de facturation, le nombre de messages est un concept abstrait défini comme la taille du trafic sortant (en octets) divisée en blocs de 2 ko. Par exemple, un trafic de 100 ko est compté comme 50 messages.  

### <a name="how-traffic-is-counted-with-billing-model"></a>Comment le trafic est-il comptabilisé avec le modèle de facturation ?

Pour la facturation, seul le trafic sortant est compté. 

Par exemple, imaginez que vous disposez d’une application avec le service Azure Web PubSub et Azure Functions. Un utilisateur diffuse 4 ko de données à 10 connexions dans un groupe. Il en résulte 4 ko en amont du service à la fonction et 40 ko du service diffusé aux 10 connexions.

> Trafic sortant pour la facturation = 4 Ko (trafic en amont) + 4 Ko * 10 (diffusion du service vers les clients) = 44 Ko

> Équivalence en nombre de messages = 44 ko/2 ko = 22

Le service Azure Web PubSub offre également un quota gratuit quotidien de trafic sortant (nombre de messages) basé sur l’utilisation des unités. Le trafic sortant (nombre de messages) au-delà du quota gratuit correspond au trafic sortant supplémentaire (messages supplémentaires). Si l’on prend le niveau standard comme exemple, le quota gratuit est de 2 000 000 ko de trafic sortant (1 million de messages) par unité/jour.

Si on reprend l’exemple précédent d’utilisation d’unités, l’application utilise 6,25 unités par jour, ce qui assure un quota gratuit quotidien de 12 500 000 ko de trafic sortant (6,25 millions de messages). Si le trafic sortant quotidien est de 30 000 000 ko (15 millions de messages), les messages supplémentaires seront de 17 500 000 ko de trafic sortant (8,75 millions de messages). Par conséquent, 6,25 unités standard et 8,75 unités de message supplémentaires vous seront facturées pour la journée.

## <a name="pricing"></a>Tarifs 

Le service Azure Web PubSub offre plusieurs niveaux à des prix différents. Une fois que vous comprenez la façon dont le nombre d’unités et la taille du trafic sortant (nombre de messages) sont comptabilisés dans le modèle de facturation, vous pouvez obtenir plus de détails sur la tarification sur la page [Tarification du service Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub).





