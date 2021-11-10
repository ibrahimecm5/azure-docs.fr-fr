---
title: Guide des performances pour le Service Azure Web PubSub
description: Vue d’ensemble des performances et de la référence du Service Azure Web PubSub. Métriques clés à prendre en compte lors de la planification de la capacité.
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: biqian
author: bjqian
ms.openlocfilehash: bfdba230408a25d7e463cf7c70ab84d0d5f8cf0f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997549"
---
# <a name="performance-guide-for-azure-web-pubsub-service"></a>Guide des performances pour le Service Azure Web PubSub

L’un des principaux avantages de l’utilisation du Service Azure Web PubSub est la facilité de la mise à l’échelle des applications en amont de PubSub Web. Dans un scénario à grande échelle, les performances sont un facteur important. 

Dans ce guide, nous allons présenter les facteurs qui affectent les performances des applications en amont d’Azure Web PubSub. Nous allons décrire les performances typiques dans différents scénarios de cas d’usage. 

## <a name="term-definitions"></a>Définitions des termes

*Entrant* : message entrant à destination du Service Azure Web PubSub.

*Sortant* : message sortant en provenance du Service Azure Web PubSub.

*Bande passante* : taille totale de tous les messages en 1 seconde.

## <a name="overview"></a>Vue d'ensemble

Le Service Azure Web PubSub définit sept niveaux standard pour les différentes capacités de performances. Ce guide répond aux questions suivantes :

- Quelles sont les performances typiques du Service Azure Web PubSub pour chaque niveau ?

- Le Service Azure Web PubSub répond-il à mes exigences de débit de message (par exemple, l’envoi de 100 000 messages par seconde) ?

- Quel est le niveau qui convient pour mon scénario ? Ou comment puis-je sélectionner le niveau approprié ?

Pour répondre à ces questions, ce guide fournit tout d’abord une explication générale des facteurs qui affectent les performances. Il illustre ensuite le nombre maximal de messages entrants et sortants pour chaque niveau de cas d’usage classique : **envoi aux groupes via le sous-protocole Web PubSub**, **en amont** et via l’**API rest**.

Ce guide ne peut pas couvrir tous les scénarios (ni différents cas d’usage, tailles de message, modèles d’envoi de messages, etc.). Mais il fournit des informations de base pour comprendre la limitation des performances.

## <a name="performance-insight"></a>Insight des performances

Cette section décrit les méthodes d’évaluation des performances, puis liste tous les facteurs qui affectent celles-ci. Enfin, elle fournit des méthodes pour vous aider à évaluer les exigences de performance.

### <a name="methodology"></a>Méthodologie

Le *débit* et la *latence* sont deux aspects typiques de la vérification des performances. Pour le Service Azure Web PubSub, chaque niveau de référence SKU a sa propre stratégie de limitation de débit. La stratégie définit le *débit maximal autorisé (bande passante entrante et sortante)* comme étant le débit maximal atteint quand 99 % des messages ont une latence inférieure à 1 seconde.

### <a name="performance-factors"></a>Facteurs de performances

En théorie, la capacité du Service Azure Web PubSub est limitée par les ressources de calcul : processeur, mémoire et réseau. Par exemple, plus il y a de connexions au Service Azure Web PubSub, plus le service utilise de mémoire. Pour le trafic de messages plus volumineux (par exemple, chaque message est supérieur à 2 048 octets), le Service Azure Web PubSub doit dépenser plus de cycles de processeur pour traiter le trafic.

Le coût de routage des messages limite également les performances. Le Service Azure Web PubSub joue le rôle de répartiteur de message, lequel achemine le message entre un ensemble de clients. Un scénario ou API différent nécessite une stratégie de routage différente. 

Pour **echo**, le client envoie un message en amont, et le service en amont renvoie ce message au client. Ce modèle présente le coût de routage le plus bas. Par contre, pour la **diffusion**, l’**envoi au groupe** et l’**envoi à la connexion**, le Service Azure Web PubSub doit rechercher les connexions cibles par le biais de la structure de données distribuée interne. Ce traitement supplémentaire utilise davantage de processeur, de mémoire et de bande passante réseau. Ainsi, les performances sont plus lentes.

En résumé, les facteurs suivants affectent les capacités entrante et sortante :

-   Niveau de référence SKU (processeur/mémoire)

-   Nombre de connexions

-   Taille des messages

-   Débit d’envoi des messages

-   Scénario d’utilisation (coût de routage)


### <a name="finding-a-proper-sku"></a>Recherche d’une référence SKU appropriée

Comment pouvez-vous évaluer la capacité entrante/sortante ou trouver le niveau approprié pour un cas d’usage spécifique ?

Chaque niveau a ses propres bandes passantes entrante et sortante maximales. Une fois que le trafic entrant ou sortant dépasse la limite, la fluidité de l’expérience utilisateur n’est pas garantie. 

```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections* : nombre de connexions envoyant le message.
- *outboundConnections* : nombre de connexions recevant le message.
- *messageSize* : taille d’un message unique (valeur moyenne). Un petit message inférieur à 1 024 octets a un impact sur les performances similaire à un message de 1 024 octets.
- *sendInterval*: intervalle d’envoi des messages. Par exemple, 1 seconde signifie l’envoi d’un message toutes les secondes. Un intervalle plus petit signifie l’envoi de davantage de messages au cours d’une période. Par exemple, 0,5 seconde signifie l’envoi de deux messages par seconde.
- *Connexions* : seuil maximal validé pour le Service Azure Web PubSub pour chaque niveau. Les connexions qui dépassent le seuil sont limitées.

Supposons que le service en amont est suffisamment puissant et qu’il n’est pas le goulot d’étranglement des performances. Vous pouvez alors vérifier les bandes passantes entrante et sortante maximales pour chaque niveau.

## <a name="case-study"></a>Étude de cas

Les sections suivantes abordent trois cas d’utilisation classiques : **l’envoi aux groupes via le sous-protocole Azure Web PubSub**, **le déclenchement de CloudEvent**, **l’appel de l’api rest**. Pour chaque scénario, la section liste les capacités entrantes et sortantes actuelles pour le Service Azure Web PubSub. Elle explique également les principaux facteurs qui affectent les performances.

Dans tous les cas d’usage, la taille de message par défaut est de 2 048 octets, tandis que l’intervalle d’envoi des messages est de 1 seconde.

### <a name="send-to-groups-through-web-pubsub-subprotocol"></a>Envoyer à des groupes via le sous-protocole Azure Web PubSub
Le service prend en charge un sous-protocole spécifique appelé `json.webpubsub.azure.v1`, qui permet aux clients de publier/s’abonner directement à la place d’un aller-retour au serveur en amont. Ce scénario est efficace, car aucun serveur n’est impliqué et tout le trafic transite par la connexion WebSocket du service client.

![Diagramme montrant le flux de travail de l’envoi au groupe.](./media/concept-performance/group.png)

Le nombre de membres par groupe et de groupes sont deux facteurs qui affectent les performances. Pour simplifier l’analyse, nous définissons deux types de groupes :

- **Big group** : le nombre de groupes est toujours de 10. Le nombre de membres par groupe est égal à (nombre de connexions maximal) / 10. Par exemple, pour l’unité 1, si le nombre de connexions s’élève à 1 000, chaque groupe a 1000 / 10 = 100 membres.
- **Small group** : chaque groupe comporte 10 connexions. Le nombre de groupes est égal à (nombre de connexions maximal) / 10. Par exemple, pour l’unité 1, si le nombre de connexions s’élève à 1 000, nous avons 1 000 / 10 = 100 groupes.

L’**envoi au groupe** implique un coût de routage pour le Service Azure Web PubSub, car il doit rechercher les connexions cibles par le biais d’une structure de données distribuée. Plus le nombre de connexions d’envoi augmente, plus le coût s’accroît.

##### <a name="big-group"></a>Grand groupe

Pour le cas d’usage **envoi à un grand groupe**, la bande passante sortante devient le goulot d’étranglement avant d’atteindre la limite du coût de routage. Le tableau suivant liste la bande passante sortante maximum.

|   Envoi à un grand groupe       | Unité 1 | Unité 2 | Unité 5  | Unité 10 | Unité 20  | Unité 50 | Unité 100 |
|---------------------------|-------|-------|--------|--------|------- |--------|---------|
| Connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000|
| Nombre de membres par groupe        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 |
| Nombre de groupes               | 10    | 10    | 10     | 10     | 10     | 10      | 10|
| Messages entrants par seconde  | 30      | 30      | 30      | 30    | 30    | 30   | 30     |
| Bande passante entrante  | 60 Kbits/s | 60 Kbits/s  | 60 Kbits/s   | 60 Kbits/s  | 60 Kbits/s   | 60 Kbits/s    | 60 Kbits/s    |
| Messages sortants par seconde | 3 000 | 6 000 / 750 | 15,000 | 30,000 | 60 000 | 150 000 | 300 000 |
| Bande passante sortante | **6 Mbits/s** | **12 Mbits/s** | **30 Mbits/s** | **60 Mbits/s** | **120 Mbits/s** | **300 Mbits/s** | **600 Mbits/s** |

##### <a name="small-group"></a>Petit groupe

Le coût de routage est important pour l’envoi de message à de nombreux petits groupes. L’implémentation du Service Azure Web PubSub atteint la limite du coût de routage à l’unité 50. L’ajout de ressources en processeur et en mémoire n’étant pas efficace, l’unité 100 ne peut pas en soi apporter d’amélioration. Si vous avez besoin de davantage de bande passante, contactez le support client.

|   Envoi à un petit groupe     | Unité 1 | Unité 2 | Unité 5  | Unité 10 | Unité 20  | Unité 50 | Unité 100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000 |
| Nombre de membres par groupe        | 10    | 10    | 10     | 10     | 10     | 10     | 10 |
| Nombre de groupes               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 |
| Messages entrants par seconde  | 400 | 800  | 2 000 | 4 000 | 8,000 | 15,000 | 15,000 |
| Bande passante entrante  | 800 Kbits/s | 1,6 Mbits/s | 4 Mbits/s    | 8 Mbits/s    | 16 Mbits/s   | 30 Mbits/s  | 30 Mbits/s   |
| Messages sortants par seconde | 4 000 | 8,000 | 20 000 | 40 000 | 80 000 | 150 000 | 150 000 |
| Bande passante sortante | **8 Mbits/s** | **16 Mbits/s** | **40 Mbits/s** | **80 Mbits/s** | **160 Mbits/s** | **300 Mbits/s** | **300 Mbits/s** |

### <a name="triggering-cloud-event"></a>Déclenchement de l’événement Cloud 
Le service fournit des événements client au webhook en amont à l’aide du [protocole http CloudEvents](./reference-cloud-events.md).

![Webhook en amont](./media/concept-performance/upstream.png)

Pour chaque événement, il formule une requête HTTP POST au service en amont enregistré et attend une réponse HTTP. 

> [!NOTE]
> Le Web PubSub prend également en charge le protocole HTTP 2.0 pour la remise des événements en amont. Le résultat ci-dessous est testé à l’aide de HTTP 1.1. Si votre serveur d’applications prend en charge HTTP 2.0, les performances seront meilleures.

#### <a name="echo"></a>Écho

Dans ce cas, le serveur d’applications réécrit le message d’origine dans la réponse http. Le comportement du cas d’usage **écho** détermine que la bande passante entrante maximale est égale à la bande passante sortante maximale. Pour plus de détails, consultez le tableau suivant.

|       Écho                        | Unité 1 | Unité 2 | Unité 5 | Unité 10 | Unité 20 | Unité 50 | Unité 100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Connexions                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Messages entrants/sortants par seconde | 500 | 1 000 | 2 500 | 5 000 | 10 000 | 25 000 | 50 000 |
| Bande passante entrante/sortante | **1 Mbits/s** | **2 Mbits/s** | **5 Mbits/s** | **10 Mbits/s** | **20 Mbits/s** | **50 Mbits/s** | **100 Mbits/s** |



### <a name="rest-api"></a>API REST

Le Service Azure Web PubSub fournit des [API](/rest/api/webpubsub/) puissantes pour gérer les clients et remettre des messages en temps réel.

![Diagramme montrant le flux de travail global du service Azure Web PubSub à l’aide des API REST.](./media/concept-performance/rest-api.png)

#### <a name="send-to-user-through-rest-api"></a>Envoi à l’utilisateur par le biais de l’API REST
Le point de référence attribue des noms d’utilisateur à tous les clients avant qu’ils ne commencent à se connecter au Service Azure Web PubSub. 

|   Envoi à l’utilisateur par le biais de l’API REST | Unité 1 | Unité 2 | Unité 5  | Unité 10 | Unité 20 | Unité 50  | Unité 100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Messages entrants/sortants par seconde | 180 | 360 | 900    | 1 800 | 3,600 | 9 000 | 18 000  |
| Bande passante entrante/sortante           | **360 Kbits/s** | **720 Kbits/s** | **1,8Mbits/s** | **3,6 Mbits/s** | **7,2 Mbits/s** | **18 Mbits/s** | **36 Mbits/s** |

#### <a name="broadcast-through-rest-api"></a>Diffusion par le biais de l’API REST
La limite de bande passante est la même que pour l’**envoi à un grand groupe**.

|   Diffusion par le biais de l’API REST     | Unité 1 | Unité 2 | Unité 5  | Unité 10 | Unité 20 | Unité 50  | Unité 100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Connexions               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Messages entrants par seconde  | 3    | 3    | 3     | 3     | 3     | 3      | 3      |
| Messages sortants par seconde | 3 000 | 6 000 / 750 | 15,000 | 30,000 | 60 000 | 150 000 | 300 000 |
| Bande passante entrante  | 6 Kbits/s   | 6 Kbits/s   | 6 Kbits/s    | 6 Kbits/s    | 6 Kbits/s    | 6 Kbits/s     | 6 Kbits/s     |
| Bande passante sortante | **6 Mbits/s** | **12 Mbits/s** | **30 Mbits/s** | **60 Mbits/s** | **120 Mbits/s** | **300 Mbits/s** | **600 Mbits/s** |

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]