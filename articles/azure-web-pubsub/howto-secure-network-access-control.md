---
title: Sécuriser et contrôler l'accès réseau au point de terminaison du service Azure Web PubSub
description: Vue d'ensemble de la configuration du contrôle d'accès réseau du service Azure Web PubSub
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 56e467b392a40a7d9e5389aa31f41468db7730c9
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997625"
---
# <a name="configure-network-access-control-for-azure-web-pubsub-service"></a>Configurer le contrôle d'accès réseau pour le service Azure Web PubSub

Le service Azure Web PubSub vous permet de sécuriser et de contrôler le niveau d'accès à votre point de terminaison de service, en fonction du type de requête et du sous-ensemble de réseaux utilisés. Lorsque des règles de réseau sont configurées, seules les applications demandant des données sur l'ensemble de réseaux spécifié peuvent accéder à votre service Azure Web PubSub.

Le service Azure Web PubSub dispose d'un point de terminaison public accessible via Internet. Vous pouvez également créer des [points de terminaison privés pour votre service Azure Web PubSub](howto-secure-private-endpoints.md). Le point de terminaison privé attribue une adresse IP privée de votre réseau virtuel au service Azure Web PubSub, et sécurise tout le trafic entre votre réseau virtuel et le service Azure Web PubSub via une liaison privée. Le contrôle d'accès réseau du service Azure Web PubSub fournit un contrôle d'accès pour le point de terminaison public et les points de terminaison privés.

Si vous le souhaitez, vous pouvez choisir d’autoriser ou de refuser certains types de requêtes pour le point de terminaison public et pour chaque point de terminaison privé. 

Une application qui accède à un service Azure Web PubSub lorsque des règles de contrôle d'accès réseau sont en vigueur requiert toujours une autorisation appropriée pour la requête.

## <a name="scenario-a---no-public-traffic"></a>Scénario A : aucun trafic public

Pour refuser complètement tout le trafic public, vous devez d’abord configurer la règle de réseau public pour n’autoriser aucun type de requête. Ensuite, vous devez configurer des règles qui autorisent l’accès au trafic en provenance de réseaux virtuels spécifiques. Cette configuration vous permet de créer une limite de réseau sécurisée pour vos applications.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Scénario B : uniquement les connexions client d’un réseau public

Dans ce scénario, vous pouvez configurer la règle de réseau public pour autoriser uniquement les connexions client du réseau public. Vous pouvez ensuite configurer des règles de réseau privé pour autoriser d’autres types de requêtes provenant d’un réseau virtuel spécifique. Cette configuration masque vos serveurs d'applications du réseau public et établit des connexions sécurisées entre vos serveurs d'applications et le service Azure Web PubSub.

## <a name="managing-network-access-control"></a>Gestion du contrôle d’accès réseau

Vous pouvez gérer le contrôle d'accès réseau du service Azure Web PubSub via le portail Azure.

### <a name="azure-portal"></a>Dans le portail Azure

1. Accédez au service Azure Web PubSub que vous souhaitez sécuriser.

1. Sélectionnez le menu de paramètres appelé **Contrôle d'accès réseau**.

    :::image type="content" source="./media/howto-secure-network-access-control/portal-network-access-control.png" alt-text="Contrôle d'accès réseau sur le portail Azure.":::

1. Pour modifier l’action par défaut, faites basculer le bouton **Autoriser/refuser**.

    > [!TIP]
    > L’action par défaut est l’action que nous entreprenons lorsqu’il n’existe aucune correspondance de règle avec la liste de contrôle d’accès (ACL, access-control list). Par exemple, si l’action par défaut est **Refuser**, les types de demandes non explicitement approuvés ci-dessous sont refusés.

1. Pour modifier une règle de réseau public, sélectionnez les types de requêtes autorisés sous **Réseau public**.

    :::image type="content" source="./media/howto-secure-network-access-control/portal-public-network.png" alt-text="Modifier l'ACL du réseau public sur le portail Azure.":::

1. Pour modifier des règles de réseau des points de terminaison privés, sélectionnez les types de requêtes autorisés dans chaque ligne sous **Connexions de points de terminaison privés**.

1. Sélectionnez **Enregistrer** pour enregistrer vos modifications.
