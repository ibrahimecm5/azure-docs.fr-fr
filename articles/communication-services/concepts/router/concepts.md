---
title: Présentation de Job Router pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez Job Router d’Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 86902147b68272fb0ff2ef5873ad6cca80654843
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312397"
---
# <a name="job-router-concepts"></a>Concepts de Job Router

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Job Router d’Azure Communication Services résout le problème de la correspondance entre une offre abstraite et une demande abstraite sur un système. Intégré à Azure Event Grid, Job Router vous envoie des notifications en quasi-temps réel, ce qui vous permet de créer des applications réactives pour contrôler le comportement de votre instance Job Router.

## <a name="job-router-overview"></a>Présentation de Job Router

Les Kits de développement logiciel (SDK) Job Router peuvent être utilisés pour élaborer divers scénarios d’entreprise dans lesquels il est nécessaire de faire correspondre une unité de travail à une ressource particulière. Par exemple, le travail peut être défini comme une série d’appels téléphoniques avec de nombreux agents potentiels du centre de contact ou comme une demande de conversation web avec un agent en direct qui gère plusieurs sessions simultanées avec d’autres personnes. Le besoin d’acheminer une unité de travail abstraite vers une ressource disponible vous oblige à définir le travail, c’est-à-dire une [mission](#job), une [file d’attente](#queue), le [travailleur](#worker) et un ensemble de [stratégies](#policies), qui définissent les aspects comportementaux de l’interaction entre ces composants.

## <a name="job-router-architecture"></a>Architecture de Job Router

Job Router d’Azure Communication Services utilise des événements pour informer vos applications des actions au sein du service. Les diagrammes suivants illustrent un flux simplifié commun à Job Router : envoi d’une mission, inscription d’un travailleur, traitement de l’offre de mission.

### <a name="job-submission-flow"></a>Flux d’envoi d’une mission

1. L’application Contoso envoie une mission à Job Router dans l’instance Azure Communication Services.
2. La mission est classée et un événement, appelé **RouterJobClassified** et qui comprend toutes les informations sur la mission et la façon dont le processus de classification a pu modifier ses propriétés, est créé.
 
    :::image type="content" source="../media/router/acs-router-job-submission.png" alt-text="Diagramme montrant Job Router d’Azure Communication Services envoyant une mission.":::

### <a name="worker-registration-flow"></a>Flux d’inscription du travailleur

1. Lorsqu’un travailleur est prêt à accepter une mission, il s’inscrit auprès de Job Router via l’application de Contoso.
2. Job Router renvoie alors un **RouterWorkerRegistered**.

    :::image type="content" source="../media/router/acs-router-worker-registration.png" alt-text="Diagramme montrant l’inscription d’un travailleur dans Job Router d’Azure Communication Services.":::

### <a name="matching-and-accepting-a-job-flow"></a>Correspondance et acceptation d’un flux de mission

1. Lorsque Job Router trouve un travailleur correspondant à une mission, il propose la mission en envoyant un **RouterWorkerOfferIssued** que l’application Contoso reçoit et envoie un signal à l’utilisateur connecté à l’aide d’une plateforme telle qu’Azure SignalR Service.
2. Le travailleur accepte l’offre.
3. Job Router envoie un **RouterWorkerOfferAccepted** indiquant à l’application de Contoso que le travailleur est affecté à la mission.

    :::image type="content" source="../media/router/acs-router-accept-offer.png" alt-text="Diagramme montrant l’offre acceptée dans Job Router d’Azure Communication Services.":::

## <a name="real-time-notifications"></a>Notifications en temps réel

Azure Communication Services s’appuie sur la plateforme de messagerie d’Event Grid pour envoyer des notifications concernant les actions effectuées par Job Router sur la charge de travail que vous envoyez. Job Router envoie des messages sous la forme d’événements chaque fois qu’une action importante se produit, comme des événements du cycle de vie d’une mission, notamment la création d’une mission, l’achèvement, l’acceptation d’une offre et bien d’autres encore.

## <a name="job"></a>Travail

Une mission représente l’unité de travail qui doit être acheminée vers un travailleur disponible. Les missions sont définies à l’aide des Kits de développement logiciel (SDK) Job Router d’Azure Communication Services ou en envoyant une demande authentifiée à l’API REST. Les missions contiennent souvent une référence à un identificateur unique que vous pouvez avoir, tel qu’un numéro d’appel ou un numéro de ticket, ainsi que les caractéristiques du travail à effectuer.

## <a name="queue"></a>File d'attente

Lorsqu’une mission est créée, elle est affectée à une file d’attente, soit de manière statique au moment de l’envoi, soit de manière dynamique par l’application d’une stratégie de classification. Les missions sont regroupées en fonction de la file d’attente qui leur est attribuée et peuvent présenter des caractéristiques différentes selon la manière dont vous entendez distribuer la charge de travail. Les files d’attente nécessitent une **stratégie de distribution** pour déterminer la manière dont les missions sont proposées aux travailleurs qualifiés.

Les files d’attente dans Job Router peuvent également contenir des stratégies d’exception qui déterminent le comportement des missions dans certaines conditions. Par exemple, vous souhaiterez peut-être déplacer une mission vers une autre file d’attente, augmenter sa priorité ou les deux en fonction d’une minuterie ou d’une autre condition.

## <a name="worker"></a>Worker

Un travailleur représente l’offre disponible pour traiter une mission pour une file d’attente particulière. Chaque travailleur inscrit auprès de Job Router est accompagné d’un ensemble d’**étiquettes**, de **files d’attente** associées, de **configurations de canaux** et d’un **score total de capacité**. Job Router utilise ces facteurs pour déterminer quand et comment acheminer les missions vers un travailleur en temps réel.

Job Router d’Azure Communication Services gère et utilise l’état d’un travailleur à l’aide de simples états **Actif**, **Inactif** ou **Épuisé** pour déterminer le moment où les missions disponibles peuvent être associées à un travailleur. Grâce à l’état, à la configuration des canaux et au score total de capacité, Job Router calcule les travailleurs viables et émet des offres liées à la mission.

## <a name="policies"></a>Stratégies

Job Router d’Azure Communication Services applique des stratégies flexibles pour associer un comportement dynamique à divers aspects du système. Selon la stratégie, les étiquettes d’une mission peuvent être consommées et évaluées pour modifier la priorité d’une mission, la file d’attente dans laquelle elle doit se trouver, et bien plus encore. Certaines stratégies de Job Router offrent un traitement en ligne des fonctions à l’aide de PowerFx ou, pour des scénarios plus complexes, un rappel vers une fonction Azure.

**Stratégie de classification :** Une stratégie de classification aide Job Router à définir la file d’attente et la priorité et peut modifier les sélecteurs de travailleurs lorsque l’expéditeur ne peut pas ou ne sait pas quels sont ces paramètres au moment de l’envoi. Pour plus d’informations sur la classification, consultez la page des [concepts de classification](classification-concepts.md).

**Stratégie de distribution :** Lorsque Job Router reçoit une nouvelle mission, la stratégie de distribution est utilisée pour trouver un travailleur approprié et gérer les offres de mission. Les travailleurs sont sélectionnés selon différents **modes** et, en fonction de la stratégie, Job Router peut notifier un ou plusieurs travailleurs simultanément.

**Stratégie d’exception :** Une stratégie d’exception contrôle le comportement d’une mission en fonction d’un déclencheur et exécute une action souhaitée. La stratégie d’exception est attachée à une file d’attente afin qu’elle puisse contrôler le comportement des missions dans la file d’attente.

## <a name="next-steps"></a>Étapes suivantes

- [Concepts de règle de routage](router-rule-concepts.md)
- [Concepts de classification](classification-concepts.md)
- [Concepts de distribution](distribution-concepts.md)
- [Guide de démarrage rapide](../../quickstarts/router/get-started-router.md)
- [Gérer des files d’attente](../../how-tos/router-sdk/manage-queue.md)
- [Classification d’une mission](../../how-tos/router-sdk/job-classification.md)
- [Transférer une mission](../../how-tos/router-sdk/escalate-job.md)
- [S’abonner aux événements](../../how-tos/router-sdk/subscribe-events.md)
