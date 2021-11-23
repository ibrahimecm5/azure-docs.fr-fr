---
title: Créer des tâches de réplication pour les ressources Azure
description: Répliquez des ressources Azure à l’aide de modèles de tâche de réplication basés sur des workflows dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42364c1c1fc0f9d2d220b4b9156a4a7ab4a410fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337351"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Créer des tâches de réplication pour les ressources Azure à l’aide d’Azure Logic Apps (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est en préversion et est soumise aux [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) des préversions de Microsoft Azure.

Bien que la disponibilité et la fiabilité maximales soient les principales priorités opérationnelles des services Azure, les communications peuvent s’arrêter pour de nombreuses raisons à cause de problèmes de mise en réseau ou de résolution de noms, d’erreurs ou de l’absence temporaire de réponse. De telles conditions ne sont pas « désastreuses » au point d’abandonner complètement le déploiement régional, comme vous pourriez le faire en cas de récupération d’urgence. Toutefois, le scénario d’entreprise pour certaines applications peut être affecté par les événements de disponibilité qui ne durent pas plus de quelques minutes, voire quelques secondes.

Pour réduire l’effet que des événements imprévisibles peuvent avoir sur vos ressources Azure dans une région Azure, vous pouvez répliquer le contenu de ces ressources d’une région vers une autre, afin de pouvoir assurer la continuité des activités. Dans Azure, vous pouvez créer une [*tâche de réplication*](#replication-task) qui déplace les données, les événements ou les messages d’une source d’une région vers une cible d’une autre région. De cette façon, vous pouvez disposer de la cible immédiatement disponible si la source est mise hors connexion et que la cible doit être replacée.

> [!NOTE]
> Vous pouvez également utiliser des tâches de réplication pour déplacer du contenu entre des entités dans la même région, mais si l’ensemble de la région devient indisponible ou subit une interruption, la source et la cible sont affectées.

Cet article fournit une vue d’ensemble des tâches de réplication alimentées par Azure Logic Apps et montre comment créer un exemple de tâche de réplication pour les files d’attente Azure Service Bus. Si vous débutez avec les applications logiques et les workflows, consultez les pages [Présentation d’Azure Logic Apps](logic-apps-overview.md) et [Locataire unique ou locataires multiples et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md).

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>Qu’est-ce qu’une tâche de réplication ?

En règle générale, une tâche de réplication reçoit des données, des événements ou des messages d’une source, déplace ce contenu vers une cible, puis supprime ce contenu de la source, sauf lorsque la source est une entité Event Hubs. En règle générale, la tâche de réplication déplace le contenu sans apporter de modifications, mais les tâches de réplication alimentées par Azure Logic Apps ajoutent également des [propriétés de réplication](#replication-properties). Si les protocoles source et cible diffèrent, ces tâches effectuent également des mappages entre les structures de métadonnées. Les tâches de réplication sont sans état. Cela signifie qu’elles ne partagent pas les états ou d’autres effets secondaires sur les exécutions séquentielles ou parallèles d’une tâche.

Lorsque vous utilisez les modèles de tâche de réplication disponibles, chaque tâche de réplication que vous créez a un [workflow sans état](single-tenant-overview-compare.md#stateful-stateless) sous-jacent dans une ressource **Application logique (Standard)** , qui peut inclure plusieurs workflows pour les tâches de réplication. Cette ressource est hébergée dans une instance Azure Logic Apps à locataire unique, qui est un environnement d’exécution fiable et évolutif pour la configuration et l’exécution d’applications serverless, y compris les tâches de réplication et de fédération. Le runtime Azure Logic Apps à locataire unique utilise également le [modèle d’extensibilité d’Azure Functions](../azure-functions/functions-bindings-register.md) et est hébergé en tant qu’extension sur le runtime d’Azure Functions. Cette conception offre une portabilité, une flexibilité et des performances accrues pour les workflows d’application logique, ainsi que d’autres fonctionnalités et avantages hérités de la plateforme Azure Functions et de l’écosystème Azure App Service.

Pour plus d’informations sur la réplication et la fédération, consultez la documentation suivante :

- [Fédération multi-site et multi-région Event Hub](../event-hubs/event-hubs-federation-overview.md)
- [Modèles de tâches de réplication des événements](../event-hubs/event-hubs-federation-patterns.md)
- [Réplication des messages Service Bus et fédération interrégionale](../service-bus-messaging/service-bus-federation-overview.md)
- [Modèles de tâches de réplication des messages](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>Modèles de tâches de réplication

Actuellement, les modèles de tâche de réplication sont disponibles pour [Azure Event Hubs](../event-hubs/event-hubs-about.md) et [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Le tableau suivant répertorie les modèles de tâches de réplication actuellement disponibles dans cette préversion :

| Type de ressource | Source et cible de la réplication |
|---------------|-------------------------------|
| Espace de noms Azure Event Hubs | - D’instance Event Hubs instance à instance Event Hubs <br>- D’instance Event Hubs instance à file d’attente Service Bus <br>- D’instance Event Hubs instance à rubrique Service Bus |
| Espace de noms Azure Service Bus | - De file d’attente Service Bus à file d’attente Service Bus <br>- De file d’attente Service Bus à rubrique Service Bus <br>- De rubrique Service Bus à rubrique Service Bus <br>- De file d’attente Service Bus à instance Event Hubs <br>- De rubrique Service Bus à file d’attente Service Bus <br>- De rubrique Service Bus à instance Event Hubs |
|||

### <a name="replication-topology-and-workflow"></a>Topologie et workflow de réplication

Pour vous aider à visualiser le fonctionnement d’une tâche de réplication avec Azure Logic Apps (Standard), les diagrammes suivants montrent la structure et le workflow de la tâche de réplication pour les instances Event Hubs et les files d’attente Service Bus.

#### <a name="replication-topology-for-event-hubs"></a>Topologie de réplication pour Event Hubs

Le diagramme suivant montre le workflow de la tâche de topologie et de réplication entre les instances Event Hubs :

![Diagramme conceptuel montrant la topologie de la tâche de réplication alimentée par un workflow « Application logique (Standard) » entre des instances Event Hubs.](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Pour plus d’informations sur la réplication et la fédération dans Azure Event Hubs, consultez la documentation suivante :

- [Fédération multi-site et multi-région Event Hub](../event-hubs/event-hubs-federation-overview.md)
- [Modèles de tâches de réplication des événements](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Topologie de réplication pour Service Bus

Le diagramme suivant montre le workflow de la tâche de topologie et de réplication entre les files d’attente Event Hubs :

![Diagramme conceptuel montrant la topologie de la tâche de réplication alimentée par un workflow « Application logique (Standard) » entre des files d’attente Service Bus.](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Pour plus d’informations sur la réplication et la fédération dans Azure Service Bus, consultez la documentation suivante :

- [Réplication des messages Service Bus et fédération interrégionale](../service-bus-messaging/service-bus-federation-overview.md)
- [Modèles de tâches de réplication des messages](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>Mappages de propriétés et métadonnées

Pour Event Hubs, les éléments suivants obtenus à partir de l’espace de noms Event Hubs source sont remplacés par les nouvelles valeurs affectées par le service dans l’espace de noms Event Hubs cible : les métadonnées affectées par le service d’un événement, le temps de mise en file d’attente d’origine, le numéro de séquence et l’offset. Toutefois, pour les [fonctions d’assistance](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) et les tâches de réplication dans les exemples fournis par Azure, les valeurs d’origine sont conservées dans les propriétés de l’utilisateur : `repl-enqueue-time` (chaîne ISO8601), `repl-sequence` et `repl-offset`. Ces propriétés ont le type `string` et contiennent la valeur convertie en chaîne des propriétés d’origine respectives. Si l’événement est transféré plusieurs fois, les métadonnées attribuées par le service de la source immédiate sont ajoutées aux propriétés déjà existantes, avec des valeurs séparées par des points-virgules. Pour plus d’informations, consultez [Métadonnées attribuées par le service - Modèles de tâches de réplication des événements](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata).

Pour Service Bus, les éléments suivants obtenus à partir de la file d’attente ou de la rubrique source Service Bus sont remplacés par les nouvelles valeurs affectées par le service dans la file d’attente ou la rubrique cible Service Bus : les métadonnées affectées au service d’un message, le temps de mise en file d’attente d’origine et le numéro de séquence. Toutefois, pour les tâches de réplication par défaut dans les exemples fournis par Azure, les valeurs d’origine sont conservées dans les propriétés de l’utilisateur : `repl-enqueue-time` (chaîne ISO8601) et `repl-sequence`. Ces propriétés ont le type `string` et contiennent la valeur convertie en chaîne des propriétés d’origine respectives. Si le message est transféré plusieurs fois, les métadonnées attribuées par le service de la source immédiate sont ajoutées aux propriétés existantes, avec des valeurs séparées par des points-virgules. Pour plus d’informations, consultez [Métadonnées attribuées par le service - Modèles de tâches de réplication des messages](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata).

Lorsqu’une tâche est répliquée de Service Bus à Event Hubs, la tâche mappe uniquement la propriété `User Properties` à la propriété `Properties`. Toutefois, lorsque la tâche est répliquée de Event Hubs à Service Bus, la tâche mappe les propriétés suivantes :

| À partir d’Event Hubs | Vers Service Bus |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| PartitionKey | PartitionKey SessionId |
| Propriétés | Propriétés de l’utilisateur |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| Objet | Étiquette |
| À | À |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>Conservation de l’ordre

Pour Event Hubs, la réplication entre le même nombre de [partitions](../event-hubs/event-hubs-features.md#partitions) crée des clones 1:1 sans aucune modification dans les événements, mais peut également inclure des doublons. Toutefois, pour la réplication entre différents nombres de partitions, seul l’ordre relatif des événements est conservé en fonction de la clé de partition, mais peut également inclure des doublons. Pour plus d’informations, consultez [Flux et conservation de l’ordre](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation).

Pour Service Bus, vous devez activer les sessions afin que les séquences de messages avec le même ID de session extrait de la source soient soumises à la file d’attente ou à la rubrique cible sous la forme d’un lot dans la séquence d’origine et avec le même ID de session. Pour plus d’informations, consultez [Séquences et conservation de l’ordre](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation).

Pour en savoir plus sur la fédération multisite et multirégion pour les services Azure où vous pouvez créer des tâches de réplication, consultez la documentation suivante :

- [Fédération multi-site et multi-région Event Hub](../event-hubs/event-hubs-federation-overview.md)
- [Modèles de tâches de réplication des événements](../event-hubs/event-hubs-federation-patterns.md)
- [Réplication des messages Service Bus et fédération interrégionale](../service-bus-messaging/service-bus-federation-overview.md)
- [Modèles de tâches de réplication des messages](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>Tarifs

Dessous, une tâche de réplication est alimentée par un workflow sans état dans une ressource **Application logique (Standard)** qui est hébergée dans une instance Azure Logic Apps à un seul locataire. Lorsque vous créez cette tâche de réplication, la facturation de frais commence immédiatement. L’utilisation, la mesure, la facturation et le modèle de tarification suivent le [plan d’hébergement Standard](logic-apps-pricing.md#standard-pricing) et les [niveaux tarifaires de plan Standard](logic-apps-pricing.md#standard-pricing-tiers).

<a name="scale-up"></a>

En fonction du nombre d’événements que Event Hubs reçoit ou des messages que Service Bus gère, votre plan d’hébergement peut être mis à l’échelle pour maintenir l’utilisation minimale du processeur virtuel et une faible latence lors de la réplication active. Ce comportement nécessite que lorsque vous créez une ressource d’application logique à utiliser pour votre tâche de réplication, vous devez [choisir le niveau de tarification de plan Standard approprié](#scale-out) pour qu’Azure Logic Apps ne limite pas ou ne commence pas à plafonner l’utilisation de l’UC et puisse toujours garantir une vitesse de réplication rapide.

> [!NOTE]
> Si votre application démarre avec une instance du plan WS1 et effectue ensuite un scale-out vers deux instances, le coût est le double du coût de WS1, en supposant que les plans s’exécutent toute la journée. Si vous effectuez un scale-up de votre application vers le plan WS2 et que vous utilisez une instance, le coût est le même que pour deux instances de plan WS1. De même, si vous effectuez un scale-up de votre application vers le plan WS3 et que vous utilisez une instance, le coût est le même que pour deux instances de plan WS2 ou quatre instances de plan WS1.

<a name="scale-out"></a>

Les exemples suivants illustrent les options de configuration et de niveau tarifaire du plan d’hébergement qui offrent le meilleur débit et un coût optimal pour des scénarios de tâche de réplication spécifiques, selon que le scénario est Event Hubs ou Service Bus et différentes valeurs de configuration.

> [!NOTE]
> Les exemples des sections suivantes utilisent 800 comme valeur par défaut pour le nombre de prérécupérations, la taille maximale du lot d’événements pour Event Hubs et le nombre maximal de messages pour Service Bus, en supposant que la taille de l’événement ou du message est de 1 Ko. En fonction de la taille de vos événements, vous pouvez ajuster le nombre de prérécupérations, la taille maximale du lot d’événements ou le nombre maximal de messages. Par exemple, si la taille de votre événement ou message est supérieure à 1 Ko, vous souhaiterez peut-être réduire les valeurs du nombre de prérécupérations, la taille maximale du lot d’événements ou le nombre de messages.

### <a name="event-hubs-scale-out"></a>Scale-out Event Hubs

Les exemples suivants illustrent les options de configuration et de niveau tarifaire du plan d’hébergement pour une tâche de réplication entre deux espaces de noms Event Hubs *dans la même région*, en fonction du nombre de [partitions](../event-hubs/event-hubs-features.md#partitions), du nombre d’événements par seconde et d’autres valeurs de configuration.

Les exemples de cette section utilisent 800 comme valeur par défaut pour le nombre de prérécupérations et la taille maximale du lot d’événements, en supposant que la taille de l’événement est de 1 Ko. En fonction de la taille de vos événements, vous pouvez ajuster le nombre de prérécupérations et la taille maximale du lot d’événements. Par exemple, si la taille de votre événement est supérieure à 1 Ko, vous souhaiterez peut-être réduire les valeurs du nombre de prérécupérations et la taille maximale du lot d’événements.

| Niveau tarifaire | Nombre de partitions | Événements par seconde | Rafales maximales* | Instances toujours prêtes* | Nombre de prérécupérations* | Taille maximale du lot d’événements* |
|--------------|-----------------|-------------------|----------------|-------------------------|-----------------|-----------------|
| **WS1** | 1 | 1 000 | 1 | 1 | 800 | 800 |
| **WS1** | 2 | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 4 | 4000 | 2 | 1 | 800 | 800 |
| **WS2** | 8 | 8000 | 2 | 1 | 800 | 800 |
| **WS3** | 16 | 16000 | 2 | 1 | 800 | 800 |
| **WS3** | 32 | 32000 | 3 | 1 | 800 | 800 |
||||||||

\* Pour plus d’informations sur les valeurs que vous pouvez modifier pour chaque niveau tarifaire, consultez le tableau suivant :

| Valeur | Description |
|-------|-------------|
| **Rafales maximales** | Nombre *maximal* de Workers élastiques sur lesquels effectuer un scale-out sous une charge. Si votre application sous-jacente exige un nombre d’instances supérieur au nombre d’*instances toujours prêtes*, dans la ligne de tableau suivante, votre application peut continuer d’effectuer un scale-out tant que le nombre d’instances n’a pas atteint la limite maximale en rafale. Pour modifier cette valeur, consultez [Modifier les paramètres de scale-out du plan d’hébergement](#edit-plan-scale-out-settings) plus loin dans cet article. <p>**Remarque** : toute instance au-delà de la taille de votre plan est facturée *uniquement* quand elle est en cours d’exécution et vous est allouée à la seconde. La plateforme s’efforce d’assurer le scale-out de votre application jusqu’à la limite maximale définie. <p>**Conseil** : en guise de recommandation, sélectionnez une valeur maximale supérieure à celle dont vous pouvez avoir besoin pour que la plateforme puisse effectuer un scale-out afin de gérer une charge plus importante, si nécessaire, car les instances inutilisées ne sont pas facturées. <p>Pour plus d’informations, consultez la documentation suivante car le plan Workflow Standard partage certains aspects en commun avec le plan Azure Functions Premium : <p>- [Paramètres de plan et de référence SKU - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [Qu’est-ce que le cloud bursting](https://azure.microsoft.com/overview/what-is-cloud-bursting/) ? |
| **Instances toujours prêtes** | Nombre minimal d’instances qui sont toujours prêtes et chaudes pour l’hébergement de votre application. Le nombre minimal est toujours 1. Pour modifier cette valeur, consultez [Modifier les paramètres de scale-out du plan d’hébergement](#edit-plan-scale-out-settings) plus loin dans cet article. <p>**Remarque** : toute instance au-delà de la taille de votre plan est facturée *qu’elle soit ou pas* en cours d’exécution lorsqu’elle vous est allouée. <p>Pour plus d’informations, consultez la documentation suivante car le plan Workflow Standard partage certains aspects en commun avec le plan Azure Functions Premium : [Instances toujours prêtes - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Nombre de prérécupérations** | La valeur par défaut pour le paramètre d’application `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` dans votre ressource d’application logique qui détermine le nombre de prérécupérations utilisé par la classe sous-jacente `EventProcessorHost`. Pour ajouter ou spécifier une valeur différente pour ce paramètre d’application, consultez [Gérer les paramètres d’application - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), par exemple : <p>- **Nom** : `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Valeur** : `800` (aucune limite maximale) <p>Pour plus d’informations sur la propriété `prefetchCount`, consultez la documentation suivante : <p>- [host.json settings - Déclencheur Azure Event Hubs et liaisons pour Azure Functions](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [Propriété EventProcessorOptions.PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount) <br>- [Équilibrer la charge de partition sur plusieurs instances de votre application](../event-hubs/event-processor-balance-partition-load.md). <br>- [Hôte du processeur d’événements](../event-hubs/event-hubs-event-processor-host.md) <br>- [Classe EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) |
| **Taille maximale du lot d’événements** | La valeur par défaut pour le paramètre d’application `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` dans votre ressource d’application logique qui détermine le nombre maximal d’événements reçu par chaque boucle de réception. Pour ajouter ou spécifier une valeur différente pour ce paramètre d’application, consultez [Gérer les paramètres d’application - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), par exemple : <p>- **Nom** : `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` <br>- **Valeur** : `800` (aucune limite maximale) <p>Pour plus d’informations sur la propriété `maxBatchSize`, consultez la documentation suivante : <p>- [host.json settings - Déclencheur Azure Event Hubs et liaisons pour Azure Functions](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [Propriété EventProcessorOptions.MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) <br>- [Hôte du processeur d’événements](../event-hubs/event-hubs-event-processor-host.md) |
|||

### <a name="service-bus-scale-out"></a>Scale-out de Service Bus

Les exemples suivants illustrent les options de configuration et de niveau tarifaire du plan d’hébergement pour une tâche de réplication entre deux espaces de noms Service Bus *dans la même région*, en fonction du nombre de messages par seconde et d’autres valeurs de configuration.

Les exemples de cette section utilisent 800 comme valeur par défaut pour le nombre de prérécupérations et le nombre maximal de messages, en supposant que la taille de message est de 1 Ko. En fonction de la taille de vos messages, vous pouvez ajuster le nombre de prérécupérations et le nombre maximal de messages. Par exemple, si la taille de votre message est supérieure à 1 Ko, vous souhaiterez peut-être réduire les valeurs du nombre de prérécupérations et le nombre maximal de messages.

| Niveau tarifaire | Messages par seconde | Rafales maximales* | Instances toujours prêtes* | Nombre de prérécupérations* | Nombre maximal de messages* |
|--------------|---------------------|-----------------|-------------------------|-----------------|------------------------|
| **WS1** | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 2 500 | 1 | 1 | 800 | 800 |
| **WS3** | 3 500 | 1 | 1 | 800 | 800 |
|||||||

\* Pour plus d’informations sur les valeurs que vous pouvez modifier pour chaque niveau tarifaire, consultez le tableau suivant :

| Valeur | Description |
|-------|-------------|
| **Rafales maximales** | Nombre *maximal* de Workers élastiques sur lesquels effectuer un scale-out sous une charge. Si votre application sous-jacente exige un nombre d’instances supérieur au nombre d’*instances toujours prêtes*, dans la ligne de tableau suivante, votre application peut continuer d’effectuer un scale-out tant que le nombre d’instances n’a pas atteint la limite maximale en rafale. Pour modifier cette valeur, consultez [Modifier les paramètres de scale-out du plan d’hébergement](#edit-plan-scale-out-settings) plus loin dans cet article. <p>**Remarque** : toute instance au-delà de la taille de votre plan est facturée *uniquement* quand elle est en cours d’exécution et vous est allouée à la seconde. La plateforme s’efforce d’assurer le scale-out de votre application jusqu’à la limite maximale définie. <p>**Conseil** : en guise de recommandation, sélectionnez une valeur maximale supérieure à celle dont vous pouvez avoir besoin pour que la plateforme puisse effectuer un scale-out afin de gérer une charge plus importante, si nécessaire, car les instances inutilisées ne sont pas facturées. <p>Pour plus d’informations, consultez la documentation suivante car le plan Workflow Standard partage certains aspects en commun avec le plan Azure Functions Premium : <p>- [Paramètres de plan et de référence SKU - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [Qu’est-ce que le cloud bursting](https://azure.microsoft.com/overview/what-is-cloud-bursting/) ? |
| **Instances toujours prêtes** | Nombre minimal d’instances qui sont toujours prêtes et chaudes pour l’hébergement de votre application. Le nombre minimal est toujours 1. Pour modifier cette valeur, consultez [Modifier les paramètres de scale-out du plan d’hébergement](#edit-plan-scale-out-settings) plus loin dans cet article. <p>**Remarque** : toute instance au-delà de la taille de votre plan est facturée *qu’elle soit ou pas* en cours d’exécution lorsqu’elle vous est allouée. <p>Pour plus d’informations, consultez la documentation suivante car le plan Workflow Standard partage certains aspects en commun avec le plan Azure Functions Premium : [Instances toujours prêtes - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#always-ready-instances). |
| **Nombre de prérécupérations** | La valeur par défaut pour le paramètre d’application `AzureFunctionsJobHost__extensions__serviceBus__prefetchCount` dans votre ressource d’application logique qui détermine le nombre de prérécupérations utilisé par la classe sous-jacente `ServiceBusProcessor`. Pour ajouter ou spécifier une valeur différente pour ce paramètre d’application, consultez [Gérer les paramètres d’application - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), par exemple : <p>- **Nom** : `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **Valeur** : `800` (aucune limite maximale) <p>Pour plus d’informations sur la propriété `prefetchCount`, consultez la documentation suivante : <p>- [host.json settings - Liaisons Azure Service Bus pour Azure Functions](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) <br>- [Propriété ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount) <br>- [Classe ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) |
| **Nombre maximal de messages** | La valeur par défaut pour le paramètre d’application `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` dans votre ressource d’application logique qui détermine le nombre maximal de messages à envoyer lors du déclenchement. Pour ajouter ou spécifier une valeur différente pour ce paramètre d’application, consultez [Gérer les paramètres d’application - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings), par exemple : <p>- **Nom** : `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` <br>- **Valeur** : `800` (aucune limite maximale) <p>Pour plus d’informations sur la propriété `maxMessageCount`, consultez la documentation suivante : \[host.json settings - Liaisons Azure Event Hubs pour Azure Functions](../azure-functions/functions-bindings-service-bus.md#hostjson-settings).|
|||

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Les ressources ou entités source et cible, qui doivent exister dans différentes régions Azure afin que vous puissiez tester le scénario de basculement de géo-reprise d’activité après sinistre. Ces entités peuvent varier en fonction du modèle de tâche que vous souhaitez utiliser. L’exemple de cet article utilise deux files d’attente Service Bus, qui se trouvent dans différents espaces de noms et régions Azure.

- Ressource **Application logique (Standard)** que vous pouvez réutiliser lors de la création de la tâche de réplication. De cette façon, vous pouvez personnaliser cette ressource spécifiquement pour votre tâche de réplication, par exemple en [choisissant le plan d’hébergement et le niveau tarifaire](#pricing) en fonction des besoins de votre scénario de réplication, tels que la capacité, le débit et la mise à l’échelle. Bien que vous puissiez créer cette ressource lorsque vous créez la tâche de réplication, vous ne pouvez pas modifier la région, le plan d’hébergement et le niveau tarifaire. La liste suivante fournit d’autres raisons et meilleures pratiques pour une ressource d’application logique précédemment créée :

  - Vous pouvez créer cette ressource d’application logique dans une région qui diffère des entités source et cible dans votre tâche de réplication.

    Actuellement, cette recommandation est fournie en raison de l’intégration native de la tâche de réplication dans les ressources Azure. Lorsque vous créez une tâche de réplication entre des entités et que vous choisissez de créer une nouvelle ressource d’application logique au lieu d’utiliser une ressource existante, la *nouvelle application logique est créée dans la même région que l’entité source*. Si la région source devient indisponible, la tâche de réplication ne peut pas non plus fonctionner. Dans un scénario de basculement, la tâche ne peut pas non plus commencer à lire des données à partir de la nouvelle source, anciennement l’entité cible, qui est ce que le [modèle de réplication actif/passif](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication) tente d’atteindre.

  - Vous pouvez personnaliser cette ressource d’application logique à l’avance en choisissant le plan d’hébergement et le niveau tarifaire, plutôt que d’utiliser les attributs par défaut. De cette façon, votre tâche de réplication peut traiter plus d’événements ou de messages par seconde pour une réplication plus rapide. Si vous créez cette ressource lors de la création de la tâche de réplication, ces attributs par défaut sont fixes.

  - Vous pouvez vous assurer que cette ressource d’application logique contient *uniquement des workflows de tâche de réplication*, en particulier si vous souhaitez suivre le modèle de réplication actif/passif. Lorsque vous utilisez une application logique existante pour créer votre tâche de réplication, cette option ajoute la tâche (workflow sans état) à cette ressource d’application logique.

  Pour plus d’informations, consultez [Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md).

- Facultatif : Chaîne de connexion pour l’espace de noms cible. Cette option permet de faire en sorte que la cible existe dans un autre abonnement, afin que vous puissiez configurer la réplication entre abonnements.

   Pour rechercher la chaîne de connexion de l’entité cible, procédez comme suit :

   1. Dans le [portail Azure](https://portal.azure.com), accédez à l’espace de noms cible.

   1. Dans le menu de navigation de l’espace de noms, sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**.

   1. Dans le volet **Stratégies d’accès partagé** qui s’ouvre, sélectionnez **RootManageSharedAccessKey** sous **Stratégie**.

   1. Dans le volet **Stratégie SAS : RootManageSharedAccessKey** qui s’ouvre, copiez la valeur de la **chaîne de connexion principale**.

   1. Enregistrez la chaîne de connexion pour pouvoir l’utiliser ultérieurement pour vous connecter à l’espace de noms cible.

<a name="naming"></a>

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Faites attention à la stratégie de dénomination que vous utilisez pour vos tâches ou entités de réplication, si vous ne les avez pas encore créées. Assurez-vous que les noms sont facilement identifiables et différenciés. Par exemple, si vous utilisez un espace de noms Event Hubs, la tâche de réplication effectue la réplication à partir de chaque instance Event Hubs dans l’espace de noms source. Si vous utilisez des files d’attente Service Bus, le tableau suivant fournit un exemple pour nommer les entités et la tâche de réplication :

| Nom de la source | Exemple | Application de réplication | Exemple | Nom de la cible | Exemple |
|-------------|---------|-----------------|---------|-------------|---------|
| Espace de noms : `<name>-sb-<region>` | `fabrikam-sb-weu` | Application logique : `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | Espace de noms : `<name>-sb-<region>` | `fabrikam-sb-wus` |
| File d’attente : `<name>` | `jobs-transfer` | Workflow : `<name>` | `jobs-transfer-workflow` | File d’attente : `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>Créer une tâche de réplication

Cet exemple montre comment créer une tâche de réplication pour des files d’attente Service Bus.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’espace de noms Service Bus que vous souhaitez utiliser comme source.

1. Dans le menu de navigation de l’espace de noms, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

   ![Capture d’écran montrant le portail Azure et le menu de l’espace de noms Azure Service Bus avec « Tâches (préversion) » sélectionnée.](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. Dans le volet **Tâches**, sélectionnez **Ajouter une tâche** pour pouvoir sélectionner un modèle de tâche.

   ![Capture d’écran montrant le volet « Tâches (préversion) » avec « Ajouter une tâche » sélectionné.](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. Dans le volet **Ajouter une tâche**, sous **Sélectionner un modèle**, dans le modèle de la tâche de réplication que vous voulez créer, sélectionnez **Sélectionner**. Si la page suivante ne s’affiche pas, sélectionnez **Suivant : Authentifier**.

   Cet exemple se poursuit en sélectionnant le modèle de tâche **Répliquer à partir d’une file d’attente Service Bus vers une file d’attente**, qui réplique le contenu entre des files d’attente Service Bus.

   ![Capture d’écran montrant le volet « Ajouter une tâche » avec le modèle « Répliquer à partir d’une file d’attente Service Bus vers une file d’attente » sélectionné.](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. Sous l’onglet **Authentifier**, dans la section **Connexions**, sélectionnez **Créer** pour chacune des connexions qui apparaissent dans la tâche afin de pouvoir fournir toutes les informations d’authentification. Les types de connexions varient en fonction de la tâche.

   Cet exemple montre l’invite de création de la connexion à l’espace de noms cible Service Bus où se trouve la file d’attente cible. La connexion existe déjà pour l’espace de noms Service Bus source.

   ![Capture d’écran montrant l’option « Créer » sélectionnée pour la connexion à l’espace de noms Service Bus cible.](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. Fournissez les informations nécessaires sur la cible, puis sélectionnez **Créer**.

   Pour cet exemple, indiquez un nom d’affichage pour la connexion, puis sélectionnez l’espace de noms Service Bus dans lequel se trouve la file d’attente cible.

   ![Capture d’écran montrant le volet « Se connecter » avec le nom d’affichage de la connexion spécifié et l’espace de noms Service Bus sélectionné.](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > Vous pouvez également créer la connexion avec une chaîne de connexion à la place. Cette option permet de faire en sorte que la cible existe dans un autre abonnement, afin que vous puissiez configurer la réplication entre abonnements. La cible, ou la source en fonction de l’emplacement où vous avez commencé la création de la tâche de réplication, est configurée dynamiquement pour que vous n’ayez à connecter que la cible. Pour utiliser une chaîne de connexion, procédez comme suit :
   >
   > 1. Dans le volet **Se connecter** , sélectionnez **Se connecter via une chaîne de connexion**.
   >
   > 2. Dans la zone **Chaîne de connexion**, entrez la chaîne de connexion pour l’espace de noms cible.

   L’exemple suivant montre la connexion créée avec succès :

   ![Capture d’écran montrant le volet « Ajouter une tâche » avec une connexion terminée à l’espace de noms Service Bus.](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. Une fois toutes les connexions terminées, sélectionnez **Suivant : Configurer**.

1. Sous l’onglet **Configurer**, donnez un nom à la tâche et renseignez les autres informations nécessaires.

   > [!NOTE]
   > Une fois créé, le nom de la tâche ne peut pas être modifié. Choisissez donc un nom qui restera pertinent si jamais vous [modifiez le workflow sous-jacent](#edit-task-workflow). Les modifications que vous apportez au workflow sous-jacent s'appliquent uniquement à la tâche que vous avez créée, et non au modèle de tâche.
   >
   > Par exemple, si vous nommez votre tâche `fabrikam-rep-weu-wus` et que vous modifiez ensuite le workflow sous-jacent pour un objectif différent, vous ne pourrez pas remplacer le nom de votre tâche en conséquence.

   1. Pour ajouter le workflow de tâche à une ressource **Application logique (Standard)** existante, dans la liste des **applications logiques**, sélectionnez l’application logique existante. Pour créer une ressource **Application logique (Standard)** à la place, sous la liste d’**applications logiques**, sélectionnez **Créer** et indiquez le nom à utiliser pour la nouvelle application logique.

      > [!NOTE]
      > Si vous créez une ressource d’application logique lors de la création de tâches de réplication, l’application logique est créée dans la *même région que l’entité source*, ce qui pose problème si la région source devient indisponible et ne fonctionne pas dans un scénario de basculement. La meilleure pratique consiste à créer une ressource **Application logique (Standard)** dans une région différente de celle de votre source. Lorsque vous créez la tâche de réplication, sélectionnez plutôt l’application logique existante et ajoutez le workflow sans état sous-jacent à l’application logique existante. Pour plus d’informations, consultez les [Prérequis](#prerequisites).

   1. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

   ![Capture d’écran montrant le volet « Ajouter une tâche » avec des informations sur les tâches de réplication, telles que le nom de la tâche, les noms de files d’attente source et cible et le nom à utiliser pour la ressource d’application logique.](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. Sous l’onglet **Vérifier + créer**, vérifiez les ressources Azure dont la tâche de réplication a besoin pour fonctionner.

   - Si vous avez choisi de créer une ressource d’application logique pour la tâche de réplication, le volet affiche les ressources Azure requises que la tâche de réplication va créer pour fonctionner. Par exemple, ces ressources incluent un compte Stockage Azure qui contient des informations de configuration pour la ressource d’application logique, le workflow et d’autres opérations d’exécution. Par exemple, avec Event Hubs, ce compte de stockage contient des informations sur le point de contrôle et la position ou le *décalage* dans le flux où l’entité source s’arrête si la région source est interrompue ou n’est plus disponible.

     L’exemple suivant montre l’onglet **Vérifier + créer** si vous avez choisi de créer une application logique :

     ![Capture d’écran montrant le volet « Vérifier + créer » avec des informations sur les ressources lors de la création d’une application logique.](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - Si vous avez choisi de réutiliser une ressource d’application logique existante pour la tâche de réplication, le volet affiche les ressources Azure que la réplication va réutiliser pour fonctionner.

     L’exemple suivant montre l’onglet **Vérifier + créer** si vous avez choisi de réutiliser une application logique existante :

     ![Capture d’écran montrant le volet « Vérifier + créer » avec des informations sur les ressources lors de la réutilisation d’une application logique existante.](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > Si votre source, votre cible ou les deux se trouvent derrière un réseau virtuel, vous devez définir des autorisations et un accès après avoir créé la tâche. Dans ce scénario, les autorisations et l’accès sont requis afin que le workflow de l’application logique puisse exécuter la tâche de réplication.

1. Quand vous êtes prêt, sélectionnez **Créer**.

   La tâche que vous avez créée, qui est automatiquement active et en cours d’exécution, apparaît désormais dans la liste **Tâches**.

   > [!TIP]
   > Si la tâche n'apparaît pas immédiatement, essayez d'actualiser la liste ou patientez un peu. Sur la barre d'outils, sélectionnez **Actualiser**.

   ![Capture d’écran montrant le volet « Tâches » avec la tâche de réplication créée.](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. Si vos ressources se trouvent derrière un réseau virtuel, n’oubliez pas de définir les autorisations de la ressource et du workflow de l’application logique pour accéder à ces ressources.

## <a name="set-up-retry-policy"></a>Configurer une stratégie de nouvelle tentative

Pour éviter de perdre des données pendant l’événement de disponibilité de part et d’autre d’une relation de réplication, vous devez configurer la stratégie de nouvelle tentative de façon à ce qu’elle soit robuste. Pour configurer la stratégie de nouvelle tentative pour une tâche de réplication, consultez la [documentation sur les stratégies de nouvelle tentative dans Azure Logic Apps](logic-apps-exception-handling.md#retry-policies) et les étapes de [modification du workflow sous-jacent](#edit-task-workflow).

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Examiner l'historique des tâches

Cet exemple montre comment afficher l’historique d’une tâche de workflow, ainsi que les états, les entrées, les sorties et d’autres informations, et continue à utiliser l’exemple pour une tâche de réplication de file d’attente Service Bus.

1. Dans le [portail Azure](https://portal.azure.com), recherchez la ressource Azure contenant l'historique de la tâche que vous souhaitez examiner.

   Pour cet exemple, cette ressource est un espace de noms Service Bus.

1. Dans le menu de navigation de la ressource, sous **Paramètres**, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

1. Dans le volet **Tâches**, recherchez la tâche que vous souhaitez examiner. Dans la colonne **Exécutions** de cette tâche, sélectionnez **Afficher**.

   ![Capture d’écran montrant le volet « Tâches », une tâche de réplication et l’option « Affichage » sélectionnée.](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   Cette étape ouvre le volet **Vue d’ensemble** du workflow sans état sous-jacent, qui est inclus dans une ressource d’application logique standard.

1. Pour afficher l’historique des exécutions d’un workflow sans état, dans la barre d’outils du volet **Vue d’ensemble**, sélectionnez **Activer le mode débogage**.

   L’onglet **Historique des exécutions** affiche les exécutions précédentes, en cours et en attente de la tâche, ainsi que leurs identificateurs, états, heures de début et durées d’exécution.

   ![Capture d'écran présentant les exécutions d'une tâche, leur état et d'autres informations](./media/create-replication-tasks-azure-resources/run-history-list.png)

   Le tableau suivant décrit les états possibles d’une exécution :

   | Étiquette État | Description |
   |--------------|-------------|
   | **Annulé** | La tâche a été annulée pendant son exécution. |
   | **Échec** | Au moins une action de la tâche a échoué, et aucune action ultérieure n'a permis de gérer l'échec. |
   | **Exécution** | La tâche est en cours d'exécution. |
   | **Réussi** | Toutes les actions ont réussi. Une tâche peut encore se terminer avec succès après l'échec d'une action, à condition qu'une action ultérieure permette de gérer cet échec. |
   | **En attente** | L'exécution n'a pas encore été lancée car une instance antérieure de la tâche est toujours en cours d'exécution. |
   |||

1. Pour afficher l'état et d'autres informations sur chacune des étapes d'une exécution, sélectionnez cette exécution.

   Le volet de détails de l’exécution s'ouvre et affiche le workflow sous-jacent exécuté.

   - Un workflow démarre toujours avec un [*déclencheur*](../connectors/apis-list.md#triggers). Pour cette tâche, le workflow démarre avec un déclencheur Service Bus qui attend que les messages arrivent dans la file d’attente Service Bus source.

   - Chaque étape indique son état et sa durée d'exécution. Une durée de 0 seconde indique que l'exécution de l'étape a nécessité moins d'une seconde.

   ![Capture d’écran montrant chaque étape de l’exécution, de l’état et de la durée d’exécution dans le workflow.](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. Pour passer en revue les entrées et les sorties de chaque étape, sélectionnez l’étape, qui ouvre un volet qui affiche les détails des entrées, des sorties et des propriétés pour cette étape.

   Cet exemple montre les entrées du déclencheur Service Bus.

   ![Capture d’écran montrant les entrées, sorties et propriétés du déclencheur.](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

Pour savoir comment vous pouvez créer vos propres workflows automatisés afin de pouvoir intégrer des applications, des données, des services et des systèmes en dehors du contexte des tâches de réplication pour les ressources Azure, consultez [Créer un workflow d’intégration avec un seul locataire Azure Logic Apps (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md).

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>Surveiller les tâches de réplication

Pour vérifier les performances et l’intégrité de votre tâche de réplication, ou du workflow d’application logique sous-jacent, vous pouvez utiliser [Application Insights](../azure-monitor/app/app-insights-overview.md), qui est une fonctionnalité Azure Monitor. [Application Insights Application Map](../azure-monitor/app/app-map.md) est un outil visuel utile que vous pouvez utiliser pour surveiller les tâches de réplication. Ce mappage est généré automatiquement à partir des informations de surveillance capturées, ce qui vous permet d’explorer les performances et la fiabilité des transferts source et cible de la tâche de réplication. Pour obtenir des informations de diagnostic immédiates et une visualisation à faible latence des détails du journal, vous pouvez utiliser l’outil de portail [Métriques en temps réel](../azure-monitor/app/live-stream.md), également une fonctionnalité Azure Monitor.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Modifier la tâche

Pour modifier une tâche, vous disposez des options suivantes :

- [Modifier la tâche « inline »](#edit-task-inline) qui vous permet de modifier les propriétés de la tâche, comme les informations de connexion ou de configuration.

- [Modifier le workflow sous-jacent de la tâche](#edit-task-workflow) dans le concepteur.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Modifier la tâche inline

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource contenant la tâche que vous souhaitez mettre à jour.

1. Dans le menu de navigation de la ressource, dans la section **Automatisation**, sélectionnez **Tâches (préversion)** .

1. Dans la liste des tâches, recherchez la tâche que vous souhaitez mettre à jour. Ouvrez le menu **...** de la tâche, puis sélectionnez **Modifier inline**.

   ![Capture d'écran sur laquelle le menu « ... » est ouvert et l'option « Modifier inline » est sélectionnée.](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   Par défaut, l’onglet **Authentifier** s’affiche et montre les connexions existantes.

1. Pour ajouter de nouvelles informations d'authentification ou sélectionner des informations d'authentification existantes, ouvrez le menu **...** de la connexion, puis choisissez **Ajouter une nouvelle connexion** ou sélectionnez les informations d'authentification existantes, le cas échéant.

   > [!NOTE]
   > Vous ne pouvez modifier que la connexion cible, pas la connexion source.

   ![Capture d’écran de l’onglet Authentification contenant la liste des connexions existantes et dans lequel le menu « ... » est sélectionné.](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. Pour mettre à jour d’autres propriétés de la tâche, sélectionnez **Suivant : Configurer**.

   Pour la tâche de cet exemple, vous pouvez spécifier différentes files d’attente source et cible. Toutefois, le nom de la tâche et l’application logique sous-jacente et le workflow restent les mêmes.

   ![Capture d’écran montrant l’onglet « Configurer » et les propriétés disponibles pour la modification.](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. Quand vous avez terminé, sélectionnez **Enregistrer**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Modifier le workflow sous-jacent de la tâche

Vous pouvez modifier le workflow sous-jacent derrière une tâche de réplication, ce qui modifie la configuration d’origine de la tâche que vous avez créée, mais pas le modèle de tâche lui-même. Une fois vos modifications effectuées et enregistrées, votre tâche modifiée n’effectue plus la même fonction que la tâche d’origine. Si vous souhaitez une tâche qui exécute les fonctionnalités d’origine, vous devrez peut-être créer une nouvelle tâche avec le même modèle. Si vous ne souhaitez pas recréer la tâche d’origine, évitez de modifier le workflow associé à la tâche à l’aide du concepteur. Au lieu de cela, créez un workflow sans état **Application logique (Standard)** pour répondre à vos besoins d’intégration. Pour plus d’informations, consultez [Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md).

1. Sur le [portail Azure](https://portal.azure.com), recherchez la ressource contenant la tâche que vous souhaitez mettre à jour.

1. Dans le menu de navigation de la ressource, dans la section **Automatisation**, sélectionnez **Tâches**.

1. Dans la liste des tâches, recherchez la tâche que vous souhaitez mettre à jour. Ouvrez le menu **...** de la tâche et sélectionnez **Ouvrir dans Logic Apps**.

   ![Capture d'écran sur laquelle le menu « ... » est ouvert et l'option « Ouvrir dans Logic Apps » est sélectionnée.](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Le portail Azure modifie le contexte dans le concepteur, où vous pouvez maintenant modifier le workflow.

   ![Capture d’écran montrant le concepteur et le workflow sous-jacent.](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   Vous pouvez maintenant modifier le déclencheur et les actions du workflow, ainsi que les propriétés du déclencheur et des actions.

1. Pour afficher les propriétés du déclencheur ou d'une action, sélectionnez ce déclencheur ou cette action.

   ![Capture d’écran montrant le volet Propriétés du déclencheur Service Bus.](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   Pour cet exemple, la propriété **IsSessionsEnabled** du déclencheur est définie sur **Oui**.

1. Pour enregistrer vos modifications, sélectionnez **Enregistrer** sur la barre d'outils du concepteur.

   ![Capture d'écran de la barre d'outils du concepteur sur laquelle la commande « Enregistrer » est sélectionnée.](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. Pour tester et exécuter le workflow mis à jour, ouvrez la ressource d’application logique qui contient le workflow mis à jour. Dans le menu de navigation du workflow, sélectionnez **Vue d’ensemble** > **Exécuter le déclencheur** > **Exécuter**.

   Au terme de l'exécution du workflow, le concepteur affiche les détails de celle-ci. Pour passer en revue les entrées et les sorties de chaque étape, sélectionnez l’étape, qui ouvre un volet qui affiche les détails des entrées, des sorties et des propriétés pour cette étape.

   Cet exemple montre les entrées, sorties et propriétés du déclencheur Service Bus sélectionné, ainsi que la valeur de la propriété de déclencheur mise à jour.

   ![Capture d’écran montrant les détails de l’exécution du workflow avec les entrées, sorties et propriétés du déclencheur.](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. Pour désactiver le workflow afin que la tâche ne continue pas à s’exécuter, dans la barre d’outils **Vue d’ensemble**, sélectionnez **Désactiver**. Pour plus d’informations, consultez [Désactiver ou activer les workflows à locataire unique](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows).

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Configurer le basculement pour Azure Event Hubs

Pour la réplication Azure Event Hubs entre les mêmes types d’entités, la géo-reprise d’activité après sinistre requiert un basculement de l’entité source vers l’entité cible, puis indique à tous les consommateurs et producteurs d’événements affectés d’utiliser le point de terminaison pour l’entité cible, qui devient la nouvelle source. Ainsi, si un incident se produit et que l’entité source bascule, les consommateurs et les producteurs, y compris votre tâche de réplication, sont redirigés vers la nouvelle source. Le compte de stockage créé par votre tâche de réplication contient des informations sur le point de contrôle et la position ou le décalage dans le flux où l’entité source s’arrête si la région source est interrompue ou n’est plus disponible.

Pour vous assurer que le compte de stockage ne contient pas d’informations héritées de la source d’origine et que votre tâche de réplication commence à lire et à répliquer les événements à partir du début du nouveau flux source, vous devez nettoyer manuellement les informations héritées de la source d’origine et reconfigurer la tâche de réplication.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource d’application logique ou le workflow sous-jacent derrière la tâche de réplication.

   > [!NOTE]
   > La ressource d’application logique doit contenir uniquement des workflows de tâche de réplication.

1. Dans le menu de navigation de la ressource ou du workflow, sélectionnez **Vue d’ensemble**. Dans la barre d’outils **Vue d’ensemble**, sélectionnez **Désactiver** pour le workflow ou **Arrêter** pour la ressource d’application logique.

1. Pour rechercher le compte de stockage utilisé par la ressource d’application logique sous-jacente de la tâche de réplication afin de stocker les informations sur le point de contrôle et le décalage du flux à partir de l’entité source, procédez comme suit :

   1. Dans le menu de votre ressource d’application logique, sous **Paramètres**, sélectionnez **Configuration**.

   1. Dans le volet **Configuration**, sous l’onglet **Paramètres de l’application**, sélectionnez le paramètre d’application **AzureWebJobsStorage**.

      Ce paramètre spécifie la chaîne de connexion et le compte de stockage utilisés par la ressource d’application logique.

      > [!NOTE]
      > Si le paramètre d’application ne s’affiche pas dans la liste, sélectionnez **Afficher les valeurs**.

   1. Sélectionnez le paramètre d’application **AzureWebJobsStorage** afin de pouvoir afficher le nom du compte de stockage.

   Cet exemple montre comment rechercher le nom de ce compte de stockage, à savoir `storagefabrikamreplb0c` :

   ![Capture d’écran montrant le volet « Configuration » de la ressource d’application logique sous-jacente avec le paramètre d’application « AzureWebJobsStorage » et la chaîne de connexion avec le nom du compte de stockage.](./media/create-replication-tasks-azure-resources/find-storage-account-name.png)

   1. Pour confirmer que la ressource du compte de stockage existe, dans la zone de recherche du portail Azure, entrez le nom, puis sélectionnez le compte de stockage, par exemple :

   ![Capture d’écran montrant la zone de recherche du portail Azure avec le nom du compte de stockage saisi.](./media/create-replication-tasks-azure-resources/find-storage-account.png)

1. Maintenant, supprimez le dossier qui contient les informations sur le point de contrôle et le décalage de l’entité source en procédant comme suit :

   1. Téléchargez, installez et ouvrez la dernière version du [client bureau Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/), si vous n’avez pas encore la version la plus récente.

      > [!NOTE]
      > Pour la tâche de nettoyage de suppression, vous devez utiliser le client Explorateur Stockage Azure, et *non* l’explorateur de stockage, le navigateur, l’éditeur ou l’expérience de gestion dans le portail Azure.
      >
      > Bien que vous puissiez supprimer des dossiers de conteneur à l’aide de la [commande `Remove-AzStorageDirectory`](/powershell/module/az.storage/remove-azstoragedirectory) PowerShell, cette commande ne fonctionne que sur les dossiers *vides*.

   1. Si ce n’est déjà fait, connectez-vous avec votre compte Azure et assurez-vous que votre abonnement Azure pour votre ressource de compte de stockage est sélectionné. Pour plus d’informations, consultez [Prise en main de l’Explorateur Stockage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

   1. Dans la fenêtre de l’explorateur, sous le nom de votre abonnement Azure, accédez à **Comptes de stockage** >  **{*nom de votre compte de stockage*}**  > **Conteneurs Blob** > **azure-webjobs-eventhub**.

      > [!NOTE]
      > Si le dossier **azure-webjobs-eventhub** n’existe pas, la tâche de réplication n’a pas encore été exécutée. Le dossier s’affiche uniquement une fois que la tâche de réplication s’est exécutée au moins une fois.

      ![Capture d’écran montrant l’Explorateur Stockage Azure avec le compte de stockage et le conteneur blob ouverts pour afficher le dossier « azure-webjobs-eventhub » sélectionné.](./media/create-replication-tasks-azure-resources/azure-webjobs-eventhub-storage-explorer.png)

   1. Dans le volet **azure-webjobs-eventhub** qui s’ouvre, sélectionnez le dossier d’espace de noms Event Hubs, dont le nom est au format suivant : `<source-Event-Hubs-namespace-name>.servicebus.windows.net`.

   1. Une fois le dossier d’espace de noms ouvert, dans le volet **azure-webjobs-eventhub**, sélectionnez le dossier <*nom de l’entité source préalable*>. Dans le menu contextuel de la barre d’outils ou du dossier, sélectionnez **Supprimer**, par exemple :

      ![Capture d’écran montrant l’ancien dossier d’entité Event Hubs source sélectionné avec le bouton « Supprimer » également sélectionné.](./media/create-replication-tasks-azure-resources/delete-former-source-entity-folder-storage-explorer.png)

   1. Confirmez que vous souhaitez supprimer le dossier.

1. Revenez à la ressource ou au workflow de l’application logique derrière la tâche de réplication. Redémarrez l’application logique ou réactivez le workflow.

Pour permettre aux producteurs et aux consommateurs d’utiliser le nouveau point de terminaison source, vous devez faire en sorte que les informations sur la nouvelle entité source soient disponibles et se trouvent dans un emplacement facile à atteindre et à mettre à jour. Si des producteurs ou des consommateurs rencontrent des erreurs fréquentes ou persistantes, ils doivent consulter cet emplacement et ajuster leur configuration. Il existe de nombreuses façons de partager cette configuration, par exemple DNS et les partages de fichiers.

Pour plus d’informations sur la géo-reprise d’activité après sinistre, consultez la documentation suivante :

- [Azure Event Hubs - Géorécupération d’urgence](../event-hubs/event-hubs-geo-dr.md)
- [Azure Service Bus - Géo-reprise d’activité après sinistre](../service-bus-messaging/service-bus-geo-dr.md)

<a name="edit-plan-scale-out-settings"></a>

## <a name="edit-hosting-plan-scale-out-settings"></a>Modifier les paramètres de scale-out du plan d’hébergement

### <a name="portal"></a>[Portail](#tab/portal)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource d’application logique sous-jacente pour votre tâche de réplication.

1. Dans le menu de la ressource d’application logique, sous **Paramètres**, sélectionnez **Scale-out (plan App Service)** .

   ![Capture d’écran montrant les paramètres du plan d’hébergement pour les rafales maximales, les instances minimales, les instances toujours prêtes et l’application de la limite de scale-out.](./media/create-replication-tasks-azure-resources/edit-app-service-plan-settings.png)

1. En fonction des besoins de votre scénario, sous **Scale-out du plan** et **Scale-out de l’application**, modifiez les valeurs de rafales maximales et d’instances toujours prêtes, respectivement.

1. Lorsque c’est chose faite, dans la barre d’outils du volet **Scale-out (plan App Service)** , sélectionnez **Enregistrer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Vous pouvez aussi configurer des instances toujours prêtes pour une application avec Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <logic-app-app-name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

---

Pour plus d’informations, consultez la documentation suivante car le plan Workflow Standard partage certains aspects en commun avec le plan Azure Functions Premium :

- [Paramètres de plan et de référence SKU - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#plan-and-sku-settings)
- [Qu’est-ce que le cloud bursting](https://azure.microsoft.com/overview/what-is-cloud-bursting/) ?
- [Instances toujours prêtes - Plan Azure Functions Premium](../azure-functions/functions-premium-plan.md#always-ready-instances)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>Problèmes de réplication et échecs

Cette section décrit les différentes façons dont la réplication peut échouer ou cesser de fonctionner :

- Limites de taille des messages

  Veillez à envoyer des messages d’une taille inférieure à 1 Mo, car la tâche de réplication ajoute des [propriétés de réplication](#replication-properties). Dans le cas contraire, si la taille du message est supérieure à la taille des événements qui peuvent être envoyés à une entité Event Hubs une fois que la tâche a ajouté des [propriétés de réplication](#replication-properties), le processus de réplication échoue.

  Par exemple, supposons que la taille du message est de 1 Mo. Une fois que la tâche a ajouté des propriétés de réplication, la taille du message est supérieure à 1 Mo. L’appel sortant qui tente d’envoyer le message échoue.

- Clés de partition

  Si des clés de partition existent dans les événements, la réplication entre les instances Event Hubs échoue si ces instances ont le même nombre de partitions.

## <a name="next-steps"></a>Étapes suivantes

- [À propos du concepteur de workflow dans Azure Logic Apps monolocataire](designer-overview.md)
- [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans un service Azure Logic Apps à un seul locataire](edit-app-settings-host-settings.md)
- [Créer des paramètres à utiliser dans les flux de travail entre environnements dans les environnements Azure Logic Apps monolocataires](parameterize-workflow-app.md)
