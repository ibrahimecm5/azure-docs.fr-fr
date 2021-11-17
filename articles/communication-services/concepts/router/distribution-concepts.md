---
title: Concepts relatifs à la distribution des travaux pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts relatifs à la distribution de Job Router d’Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdc64b5d789d017e6fcb8aab2cd48103235535ec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319241"
---
# <a name="job-distribution-concepts"></a>Concepts relatifs à la distribution des travaux

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Job Router d’Azure Communication Services suit un processus de distribution flexible, qui implique l’utilisation d’une stratégie et du cycle de vie d’une offre de travail pour affecter les collaborateurs. Cet article décrit les différentes façons dont un travail peut être distribué, le cycle de vie de l’offre de travail et l’effet de ce processus sur les collaborateurs.

## <a name="job-distribution-overview"></a>Vue d’ensemble de la distribution des travaux

Le choix de la distribution des travaux aux collaborateurs constitue une fonctionnalité clé de Job Router. Le kit de développement logiciel (SDK, Software Development Kit) offre un modèle extensible et flexible qui vous permet de personnaliser votre environnement. Comme le décrit le guide des [concepts de classification](classification-concepts.md), une fois qu’un travail a été classé, Job Router recherche un collaborateur approprié en fonction des caractéristiques du travail et de la stratégie de distribution. Si les collaborateurs sont occupés, il recherche un travail adapté dès qu’un collaborateur est disponible. L’adéquation du collaborateur est déterminée selon trois caractéristiques : un [canal disponible](#channel-configurations), ses [capacités](#worker-abilities) et son [état](#worker-status). Une fois qu’un collaborateur approprié a été trouvé, un contrôle est effectué pour vérifier qu’il dispose d’un canal ouvert auquel le travail peut être attribué.

Ces deux approches constituent des concepts clés dans la manière dont Job Router lance la découverte des travaux ou des collaborateurs.

### <a name="finding-workers-for-a-job"></a>Recherche de collaborateurs pour un travail

Une fois qu’un travail a suivi le [processus de classification](classification-concepts.md), Job Router applique la stratégie de distribution configurée dans la file d’attente afin de sélectionner un ou plusieurs collaborateurs qui remplissent les conditions des sélecteurs de collaborateurs sur le travail et de générer des offres pour que ces collaborateurs effectuent le travail. 

### <a name="finding-a-job-for-a-worker"></a>Recherche d’un travail pour un collaborateur

Il existe plusieurs scénarios qui déclenchent dans Job Router la recherche d’un travail pour un collaborateur :

- Un collaborateur s’inscrit auprès de Job Router
- Un travail est fermé, et le canal libéré
- Une offre de travail est refusée ou révoquée

Le processus de distribution est identique à la recherche de collaborateurs pour un travail. Lorsqu’un collaborateur est trouvé, une [offre](#job-offer-overview) est générée.

## <a name="worker-overview"></a>Vue d'ensemble des collaborateurs

Les collaborateurs **s’inscrivent** auprès de Job Router à l’aide du kit SDK en fournissant les informations de base suivantes :

- ID et nom d’un collaborateur
- ID de files d’attente
- Capacité totale (nombre)
- Liste des **configurations de canaux**
- Ensemble d’étiquettes 

Job Router contient toujours une référence à chaque collaborateur inscrit, même s’il est **désinscrit** manuellement ou automatiquement.

### <a name="channel-configurations"></a>Configurations de canaux

Chaque travail exige une propriété ID de canal représentant un canal Job Router préconfiguré ou un canal personnalisé. Une configuration de canal se compose d’une chaîne `channelId` et d’un nombre `capacityCostPerJob`. Ensemble, ils représentent un mode de communication abstrait et le coût de ce mode. Par exemple, la plupart des gens ne peuvent passer qu’un seul appel téléphonique à la fois. Par conséquent, un canal `Voice` peut avoir un coût élevé de `100`. Certaines charges de travail comme la conversation instantanée, en revanche, peuvent afficher une concurrence plus élevée, donc un coût inférieur. On peut considérer les configurations de canaux comme des emplacements ouverts auxquels un travail peut être attribué ou attaché. L’exemple suivant illustre ce point :

```csharp
await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new[] { "XBOX_Queue", "XBOX_Escalation_Queue" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "Edmonton" },
        { "XBOX_Hardware", 7 },
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100
        ),
        new (
            channelId: ManagedChannels.AcsChatChannel,
            capacityCostPerJob: 33
        )
    }
);
```

Le collaborateur ci-dessus est inscrit avec deux configurations de canaux, chacune présentant des coûts uniques par canal. Le résultat effectif est que `EdmontonWorker` peut gérer trois travaux `ManagedChannels.AcsChatChannel` simultanés ou un travail `ManagedChannels.AcsVoiceChannel`.

Job Router met à votre disposition les ID de canaux préconfigurés suivants :

- ManagedChannels.AcsChatChannel
- ManagedChannels.AcsVoiceChannel
- ManagedChannels.AcsSMSChannel

De nouveaux canaux abstraits peuvent être créés à l’aide du kit SDK Job Router :

```csharp
await client.SetChannelAsync(
    id: "MakePizza",
    name: "Make a pizza"
);

await client.SetChannelAsync(
    id: "MakeDonairs",
    name: "Make a donair"
);

await client.SetChannelAsync(
    id: "MakeBurgers",
    name: "Make a burger"
);
```

Vous pouvez ensuite utiliser le canal lors de l’inscription du collaborateur pour représenter sa capacité à effectuer un travail correspondant à cet ID de canal :

```csharp
await client.RegisterWorkerAsync(
    id: "PizzaCook",
    queueIds: new[] { "PizzaOrders", "DonairOrders", "BurgerOrders" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: MakePizza,
            capacityCostPerJob: 50
        ),
        new (
            channelId: MakeDonair,
            capacityCostPerJob: 33
        ),
        new (
            channelId: MakeBurger,
            capacityCostPerJob: 25        
        )
    }
);
```

L’exemple ci-dessus illustre trois canaux abstraits, chacun présentant son propre coût par travail. Par conséquent, les exemples de concurrence de travaux suivants sont possibles pour le collaborateur `PizzaCook` :

| MakePizza | MakeDonair | MakeBurger | Score |
|--|--|--|--|
| 2         |            |            | 100   |
|           | 3          |            | 99    |
| 1         | 1          |            | 83    |
|           | 2          | 1          | 91    |
|           |            | 4          | 100   |
|           | 1          | 2          | 83    |

### <a name="worker-abilities"></a>Capacités des collaborateurs

En dehors des canaux disponibles d’un collaborateur, le processus de distribution utilise la collection d’étiquettes du collaborateur inscrit pour déterminer son adéquation à un travail. Dans l’exemple de préparation de pizzas ci-dessus, le collaborateur possède une collection d’étiquettes qui se compose des éléments suivants :

```csharp
new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    }
```

Lorsqu’un travail est envoyé, les **sélecteurs de collaborateurs** sont utilisés pour définir les exigences de cette unité de travail particulière. Si un travail exige une personne anglophone sachant faire des kebabs (« donair »), l’appel du kit SDK est le suivant :

```csharp
await client.CreateJobAsync(
    channelId: "MakeDonair",
    channelReference: "ReceiptNumber_555123",
    queueId: "DonairOrders",
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "DonairMaker",
            @operator: LabelOperator.GreaterThanEqual,
            value: 8),
        new (
            key: "English",
            @operator: LabelOperator.GreaterThan,
            value: 5)
    });
```

### <a name="worker-status"></a>État du collaborateur

Étant donné que Job Router peut gérer des travaux simultanés pour un collaborateur en fonction de ses configurations de canaux, le concept de disponibilité est représenté par trois états :

**Actif** : un collaborateur est inscrit auprès de Job Router et est disposé à accepter un travail.

**Épuisé** : un collaborateur a été désinscrit de Job Router, mais un ou plusieurs travaux actifs lui sont actuellement affectés.

**Inactif** : un collaborateur a été désinscrit de Job Router et ne possède pas de travaux actifs.

## <a name="job-offer-overview"></a>Vue d’ensemble des offres de travail

Lorsque le processus de distribution localise un collaborateur approprié disposant d’un canal ouvert et de l’état souhaité, une offre de travail est générée, et un événement envoyé. La stratégie de distribution contient les propriétés configurables suivantes pour l’offre :

**OfferTTL** : durée de vie de chaque offre générée

**Mode** : **modes de distribution** qui contiennent à la fois la propriété `minConcurrentOffers` et la propriété `maxConcurrentOffers`.

> [!Important]
> Lorsqu’une offre de travail est générée pour un collaborateur, elle consomme l’une des configurations de canaux correspondant à l’ID de canal du travail. De ce fait, le collaborateur ne reçoit pas d’autre offre, sauf si une capacité supplémentaire pour ce canal est disponible sur le collaborateur. Si le collaborateur refuse l’offre ou que celle-ci expire, le canal est libéré.

### <a name="job-offer-lifecycle"></a>Cycle de vie de l’offre de travail

Les événements suivants du cycle de vie de l’offre de travail peuvent être observés par le biais d’un abonnement Event Grid :

- RouterWorkerOfferIssued
- RouterWorkerOfferAccepted
- RouterWorkerOfferDeclined
- RouterWorkerOfferExpired
- RouterWorkerOfferRevoked

> [!NOTE]
> Une offre peut être acceptée ou refusée par un collaborateur à l’aide du kit SDK, tandis que tous les autres événements sont générés en interne.

## <a name="distribution-modes"></a>Modes de distribution

Job Router comprend les modes de distribution suivants :

**LongestIdleMode** : génère une offre pour le collaborateur le plus longtemps inactif d’une file d’attente.

**RoundRobinMode** : étant donné une collection de collaborateurs, choisit le collaborateur suivant le dernier sélectionné, par ordre d’ID.

**BestWorkerMode** : utilise le moteur [RuleEngine](router-rule-concepts.md) de Job Router pour choisir un collaborateur en fonction de ses étiquettes.

## <a name="distribution-summary"></a>Résumé de la distribution

Plusieurs facteurs tels que l’état d’un collaborateur, la configuration et la capacité des canaux, le mode de la stratégie de distribution et la concurrence de l’offre peuvent influencer la façon dont les offres de travail sont générées. Il est recommandé de commencer par une implémentation simple et d’ajouter de la complexité en fonction des besoins.
