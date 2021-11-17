---
title: Niveaux de service Premium et Standard d’Azure Service Bus
description: Cet article décrit les niveaux de service Standard et Premium d’Azure Service Bus. Compare ces niveaux de service et indique les différences techniques.
ms.topic: conceptual
ms.date: 11/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0734e6d7d54966617e66a5ac5876df4a1da9264f
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028778"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Couches messagerie Service Bus Premium et Standard

La messagerie Service Bus, qui comprend des entités telles que les files d’attente et les rubriques, associe des fonctionnalités de messagerie d’entreprise à une sémantique riche de publication et d’abonnement à l’échelle du cloud. La messagerie Service Bus sert d’infrastructure de communication à de nombreuses solutions cloud sophistiquées.

La couche *Premium* de la messagerie Service Bus répond aux demandes couramment formulées par les clients concernant la mise à l’échelle, les performances et la disponibilité de leurs applications critiques. Le niveau Premium est recommandé pour les scénarios de production. Bien qu’offrant des ensembles de fonctionnalités quasiment identiques, les deux couches de messagerie Service Bus sont conçues pour différents cas de figure.

Les principales différences sont répertoriées dans le tableau suivant.

| Premium | standard |
| --- | --- |
| Débit élevé |Débit variable |
| Performances prévisibles |Latence variable |
| Prix fixe |Tarification à l’utilisation variable |
| Possibilité de faire évoluer la charge de travail |N/A |
| Taille de message maximale de 100 Mo. Pour plus d’informations, consultez [Prise en charge des messages de grande taille](#large-messages-support). |Taille de message maximale de 256 Ko |

La **messagerie Service Bus Premium** isole les ressources au niveau processeur et mémoire, ce qui permet d’exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter 1, 2, 4, 8 ou 16 unités de messagerie pour chaque espace de noms Service Bus Premium. Une entité ou une charge de travail unique peut couvrir plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. Au final, les performances de votre solution Service Bus sont prévisibles et répétables.

Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, mais aussi supérieures. La messagerie Premium offre des performances de pointe bien supérieures à celles de la couche Standard.

## <a name="premium-messaging-technical-differences"></a>Différences techniques de la messagerie Premium

Les sections suivantes décrivent les quelques différences entre les couches de messagerie Standard et Premium.

### <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées

La messagerie Premium-Messaging ne prend pas en charge les files d’attente et rubriques partitionnées. Pour plus d’informations sur le partitionnement, voir [Files d’attentes et rubriques partitionnées](service-bus-partitioning.md).

### <a name="express-entities"></a>Entités Express

La messagerie Premium-Messaging s’exécutant dans un environnement d’exécution isolé, les entités express ne sont pas prises en charge dans les espaces de noms Premium. Une entité Express conserve temporairement un message en mémoire avant de l’écrire dans un stockage persistant. Si du code s’exécute sous la messagerie Standard et que vous souhaitez le porter au niveau Premium, assurez-vous que la fonctionnalité d’entité Express est désactivée.

## <a name="premium-messaging-resource-usage"></a>Utilisation des ressources Premium Messaging
En règle générale, toute opération sur une entité peut entraîner l’utilisation du processeur et de la mémoire. Voici quelques-unes de ces opérations : 

- Les opérations de gestion comme les opérations CRUD (Create, Retrieve, Update et Delete) sur les files d’attente, les sujets et les abonnements.
- Les opérations d’exécution (envoi et réception de messages)
- Les opérations de surveillance et d’alerte

L’utilisation supplémentaire de mémoire et de processeur n’est cependant pas facturée. Pour la messagerie Premium Messaging, il existe un prix unique par unité de message.

L’utilisation du processeur et de la mémoire est suivie et affichée pour les raisons suivantes : 

- Transparence sur le fonctionnement interne du système
- Capacité des ressources achetées.
- Planification de la capacité vous décider de monter en puissance ou non.

## <a name="messaging-unit---how-many-are-needed"></a>Unité de messagerie – Combien sont nécessaires ?

Lors du provisionnement d’un espace de noms Azure Service Bus Premium, le nombre d’unités de messagerie allouées doit être spécifié. Ces unités de messagerie sont des ressources dédiées qui sont allouées à l’espace de noms.

Le nombre d’unités de messagerie allouées à l’espace de noms Service Bus Premium peut être **ajusté dynamiquement** pour prendre en compte la variation (augmentation ou diminution) des charges de travail.

Certains facteurs doivent être pris en compte pour déterminer le nombre d’unités de messagerie pour votre architecture :

- Commencez par ***1 ou 2 unités de messagerie*** allouées à votre espace de noms.
- Étudiez les métriques d’utilisation du processeur dans les [métriques d’utilisation des ressources](monitor-service-bus-reference.md#resource-usage-metrics) pour votre espace de noms.
    - Si l’utilisation du processeur est **inférieure à 20 %** , vous pourrez peut-être *_réduire_* le nombre d’unités de messagerie allouées à votre espace de noms.
    - Si l’utilisation du processeur est **supérieure à 70 %** , votre application profitera d’une *_augmentation_* du nombre d’unités de messagerie allouées à votre espace de noms.

Pour configurer un espace de noms Service Bus afin qu'il se mette automatiquement à l'échelle (augmentation ou diminution des unités de messagerie), consultez [Mettre automatiquement à jour les unités de messagerie](automate-update-messaging-units.md).

> [!NOTE]
> La **mise à l’échelle** des ressources allouées à l’espace de noms peut être préemptive ou réactive.
>
>  * **Préemptive** : si une charge de travail supplémentaire est attendue (en raison de la saisonnalité ou des tendances), vous pouvez allouer plus d’unités de messagerie à l’espace de noms avant la hausse des charges de travail.
>
>  * **Réactive** : si des charges de travail supplémentaires sont identifiées en examinant les métriques d’utilisation des ressources, des ressources supplémentaires peuvent être allouées à l’espace de noms pour incorporer la hausse de la demande.
>
> Les compteurs de facturation pour Service Bus sont horaires. Dans le cas d’un scale-up, vous payez uniquement pour les ressources supplémentaires pendant les heures où celles-ci sont utilisées.
>

## <a name="get-started-with-premium-messaging"></a>Prise en main de Premium Messaging

La prise en main de Premium Messaging est immédiate ; le processus est similaire à celui de la messagerie Standard. Commencez par [créer un espace de noms](service-bus-create-namespace-portal.md) dans le [portail Azure](https://portal.azure.com). Veillez à sélectionner **Premium** sous **Niveau de tarification**. Cliquez sur **Afficher tous les détails de la tarification** pour voir plus d’informations sur chaque niveau.

:::image type="content" source="./media/service-bus-premium-messaging/select-premium-tier.png" alt-text="Capture d’écran montrant la sélection du niveau premium lors de la création d’un espace de noms.":::

Vous pouvez également créer des [espaces de noms Premium à l’aide de modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/).

## <a name="large-messages-support"></a>Prise en charge des messages volumineux
Les espaces de noms Azure Service Bus de niveau premium prennent en charge la possibilité d’envoyer des charges utiles de messages volumineux jusqu’à 100 Mo. Cette fonctionnalité est principalement ciblée vers des charges de travail héritées qui ont utilisé des charges utiles de messages plus importantes sur d’autres répartiteurs de messagerie d’entreprise et cherchent à migrer de façon fluide vers Azure Service Bus.

Voici quelques points à prendre en compte lors de l’envoi de messages volumineux sur Azure Service Bus :
   * Prise en charge sur les espaces de noms Azure Service Bus niveau premium uniquement.
   * Prise en charge uniquement lors de l’utilisation du protocole AMQP. Pas de prise en charge lors de l’utilisation du protocole SBMP.
   * Prise en charge lors de l’utilisation du [SDK client Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) et de SDK clients d’autres langages.
   * L’envoi de messages volumineux entraîne une réduction du débit et une augmentation de la latence.
   * Bien que les charges utiles de messages de 100 Mo soient prises en charge, il est recommandé de les maintenir le plus faibles possible pour garantir des performances fiables à partir de l’espace de noms Service Bus.
   * La taille maximale de message est appliquée uniquement pour les messages envoyés à la file d’attente ou à la rubrique. La limite de taille n’est pas appliquée pour l’opération de réception. Cela vous permet de mettre à jour la taille maximale des messages pour une file d’attente (ou rubrique) donnée.

### <a name="enabling-large-messages-support-for-a-new-queue-or-topic"></a>Activation de la prise en charge des messages volumineux pour une nouvelle file d’attente (ou rubrique)

Pour activer la prise en charge des messages volumineux, définissez la taille maximale de message lors de la création d’une nouvelle file d’attente (ou rubrique) comme indiqué ci-dessous. 

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview.png" alt-text="Capture d’écran montrant comment activer la prise en charge des messages volumineux pour une file d’attente existante.":::

### <a name="enabling-large-messages-support-for-an-existing-queue-or-topic"></a>Activation de la prise en charge des messages volumineux pour une file d’attente (ou rubrique) existante

Vous pouvez aussi activer la prise en charge des messages volumineux pour les files d’attente (ou rubriques) existantes, en mettant à jour la **Taille de message maximale** dans la **_Vue d’ensemble_** de cette file d’attente (ou rubrique) spécifique, comme indiqué ci-dessous.

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview-update.png" alt-text="Capture d’écran de la page Créer une file d’attente avec la prise en charge de messages volumineux activée.":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les liens suivants :

- [Mettre automatiquement à jour les unités de messagerie](automate-update-messaging-units.md)
- [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (billet de blog))](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (Channel9))](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
