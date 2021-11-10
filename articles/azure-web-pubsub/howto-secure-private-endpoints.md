---
title: Comment sécuriser le trafic entre un réseau virtuel et le service Azure Web PubSub via des points de terminaison privés Azure
description: Vue d’ensemble des points de terminaison privés pour un accès sécurisé au service Azure Web PubSub à partir de réseaux virtuels.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: c05112ec35197f8a17220b31e5032ae5d2469cf8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995045"
---
# <a name="use-private-endpoints-for-azure-web-pubsub-service"></a>Utiliser des points de terminaison privés pour le service Azure Web PubSub

Vous pouvez utiliser des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour votre service Azure Web PubSub afin de permettre aux clients d’un réseau virtuel (VNet) d’accéder en toute sécurité aux données via une [liaison privée](../private-link/private-link-overview.md). Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre service Azure Web PubSub. Le trafic entre les clients du réseau virtuel et le service Azure Web PubSub passe par une liaison privée du réseau principal Microsoft, ce qui élimine l’exposition à l’Internet public.

L’utilisation de points de terminaison privés pour votre service Azure Web PubSub vous permet de :

- Sécuriser votre service Azure Web PubSub à l’aide du contrôle d’accès réseau afin de bloquer toutes les connexions sur le point de terminaison public pour le service Azure Web PubSub.
- Améliorez la sécurité du réseau virtuel en vous permettant de bloquer l’exfiltration des données à partir du réseau virtuel.
- Vous connecter en toute sécurité au service Azure Web PubSub à partir de réseaux locaux qui se connectent au réseau virtuel à l’aide d’un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou de connexions [ExpressRoute](../expressroute/expressroute-locations.md) avec un Peering privé.

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle

:::image type="content" source="./media/howto-secure-private-endpoints/private-endpoint-overview.png" alt-text="Vue d’ensemble des points de terminaison privés pour le service Azure Web PubSub.":::

Un point de terminaison privé est une interface réseau spéciale pour un service Azure dans votre [réseau virtuel](../virtual-network/virtual-networks-overview.md). Lorsque vous créez un point de terminaison privé pour votre service Azure Web PubSub, il offre une connectivité sécurisée entre les clients de votre réseau virtuel et votre service. Une adresse IP est attribuée au point de terminaison privé à partir de la plage d’adresses IP de votre réseau virtuel. La connexion entre le point de terminaison privé et le service Azure Web PubSub utilise une liaison privée.

Les applications du réseau virtuel peuvent se connecter sans difficulté au service Azure Web PubSub sur le point de terminaison privé **en utilisant les chaînes de connexion et mécanismes d’autorisation habituels**. Les points de terminaison privés peuvent être utilisés avec tous les protocoles pris en charge par le service Azure Web PubSub, notamment l’API REST.

Lorsque vous créez un point de terminaison privé pour un service Azure Web PubSub de votre réseau virtuel, une demande de consentement est envoyée pour approbation au propriétaire du service Azure Web PubSub. Si l’utilisateur qui demande la création du point de terminaison privé est également propriétaire du service Azure Web PubSub, cette demande de consentement est automatiquement approuvée.

Les propriétaires de services Azure Web PubSub peuvent gérer les demandes de consentement et les points de terminaison privés via l’onglet « *Points de terminaison privés* » du service Azure Web PubSub sur le [portail Azure](https://portal.azure.com).

> [!TIP]
> Si vous souhaitez restreindre l’accès à votre service Azure Web PubSub uniquement par le biais du point de terminaison privé, [configurez le contrôle d’accès réseau](howto-secure-network-access-control.md) de manière à refuser ou contrôler l’accès via le point de terminaison public.

### <a name="connecting-to-private-endpoints"></a>Connexion à des points de terminaison privés

Les clients d’un réseau virtuel qui utilisent le point de terminaison privé doivent utiliser la même chaîne de connexion pour le service Azure Web PubSub que les clients qui se connectent au point de terminaison public. Nous nous appuyons sur la résolution DNS pour acheminer automatiquement les connexions entre le réseau virtuel et le service Azure Web PubSub via une liaison privée.

> [!IMPORTANT]
> Utilisez la même chaîne de connexion pour vous connecter au service Azure Web PubSub avec des points de terminaison privés. Ne vous connectez pas au service Azure Web PubSub à l’aide de son URL de sous-domaine `privatelink`.

Nous créons une [zone DNS privée](../dns/private-dns-overview.md) attachée au réseau virtuel avec les mises à jour nécessaires pour les points de terminaison privés, par défaut. Toutefois, si vous utilisez votre propre serveur DNS, vous devrez peut-être apporter d’autres modifications à votre configuration DNS. La section sur les [modifications DNS](#dns-changes-for-private-endpoints) ci-dessous décrit les mises à jour requises pour les points de terminaison privés.

## <a name="dns-changes-for-private-endpoints"></a>Modifications DNS pour les points de terminaison privés

Lorsque vous créez un point de terminaison privé, l’enregistrement de ressource CNAME DNS de votre service Azure Web PubSub est remplacé dans un sous-domaine par un alias doté du préfixe `privatelink`. Par défaut, nous créons également une [zone DNS privée](../dns/private-dns-overview.md) correspondant au sous-domaine `privatelink`, avec les enregistrements de ressource DNS A pour les points de terminaison privés.

Lorsque vous résolvez le nom de domaine de votre service Azure Web PubSub à l’extérieur du réseau virtuel avec le point de terminaison privé, il correspond au point de terminaison public du service Azure Web PubSub. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, le nom de domaine correspond à l'adresse IP du point de terminaison privé.

Pour l’exemple illustré ci-dessus, les enregistrements de ressources DNS correspondant au « foobar » du service Azure Web PubSub, lorsqu’ils sont résolus à l’extérieur du réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Nom                                                  | Type  | Valeur                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | Un     | \<Azure Web PubSub service public IP address\>           |

Comme mentionné précédemment, vous pouvez refuser ou contrôler l'accès pour les clients situés en dehors du réseau virtuel via le point de terminaison public à l'aide du contrôle d'accès réseau.

Les enregistrements de ressources DNS correspondant à « foobar », lorsqu'ils sont résolus par un client du réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Nom                                                  | Type  | Valeur                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | Un     | 10.1.1.5                                              |

Cette approche permet d’accéder au service Azure Web PubSub **avec la même chaîne de connexion** pour les clients du réseau virtuel hébergeant les points de terminaison privés et les clients extérieurs au réseau virtuel.

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet du point de terminaison du service Azure Web PubSub sur l’adresse IP du point de terminaison privé. Vous devez configurer votre serveur DNS de manière à déléguer votre sous-domaine de liaison privée à la zone DNS privée du réseau virtuel, ou configurer les enregistrements A pour `foobar.privatelink.webpubsub.azure.com` avec l'adresse IP du point de terminaison privé.

> [!TIP]
> Lorsque vous utilisez un serveur DNS personnalisé ou local, vous devez configurer votre serveur DNS de manière à résoudre le nom du service Azure Web PubSub dans le sous-domaine `privatelink` sur l’adresse IP du point de terminaison privé. Pour ce faire, vous pouvez déléguer le sous-domaine `privatelink` à la zone DNS privée du réseau virtuel, ou configurer la zone DNS sur votre serveur DNS et ajouter les enregistrements A DNS.

Le nom de zone DNS recommandé pour les points de terminaison privés du service Azure Web PubSub est le suivant : `privatelink.webpubsub.azure.com`.

Pour plus d’informations sur la configuration de votre propre serveur DNS pour la prise en charge des points de terminaison privés, consultez les articles suivants :

- [Résolution de noms pour des ressources dans les réseaux virtuels Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuration DNS pour les points de terminaison privés](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

### <a name="create-a-private-endpoint-along-with-a-new-azure-web-pubsub-service-in-the-azure-portal"></a>Créer un point de terminaison privé avec un nouveau service Azure Web PubSub dans le portail Azure

1. Lors de la création d’un nouveau service Azure Web PubSub, sélectionnez l’onglet **Mise en réseau**. Choisissez **Point de terminaison privé** comme méthode de connectivité.

    :::image type="content" source="./media/howto-secure-private-endpoints/portal-create-blade-networking-tab.png" alt-text="Créer un service Azure Web PubSub, onglet Mise en réseau.":::

1. Sélectionnez **Ajouter**. Renseignez les champs Abonnement, Groupe de ressources, Emplacement et Nom du nouveau point de terminaison privé. Choisissez un réseau virtuel et un sous-réseau.

1. Sélectionnez **Revoir + créer**.

### <a name="create-a-private-endpoint-for-an-existing-azure-web-pubsub-service-in-the-azure-portal"></a>Créer un point de terminaison privé pour un service Azure Web PubSub existant dans le portail Azure

1. Accédez au service Azure Web PubSub.

1. Sélectionnez le menu de paramètres **Connexions des points de terminaison privés**.

1. Sélectionnez le bouton **+ Point de terminaison privé** en haut.

1. Renseignez les champs Abonnement, Groupe de ressources, Nom de la ressource et Région du nouveau point de terminaison privé.

1. Choisissez la ressource de service Azure Web PubSub cible.

1. Choisir le réseau virtuel cible

1. Sélectionnez **Revoir + créer**.

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problèmes connus

Gardez à l’esprit les problèmes connus suivants concernant les points de terminaison privés pour le service Azure Web PubSub.

### <a name="free-tier"></a>Niveau gratuit

L’instance de niveau gratuit du service Azure Web PubSub ne peut pas s’intégrer à un point de terminaison privé.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Contraintes d'accès pour les clients des réseaux virtuels dotés de points de terminaison privés

Les clients des réseaux virtuels dotés de points de terminaison privés sont soumis à des contraintes lors de l’accès à d’autres instances du service Azure Web PubSub qui disposent de points de terminaison privés. Par exemple, supposons qu’un réseau virtuel N1 possède un point de terminaison privé pour une instance de service Azure Web PubSub W1. Si le service Azure Web PubSub W2 possède un point de terminaison privé dans un réseau virtuel N2, les clients du réseau virtuel N1 doivent également accéder au service Azure Web PubSub W2 à l’aide d’un point de terminaison privé. Si le service Azure Web PubSub W2 ne possède pas de points de terminaison privés, les clients du réseau virtuel N1 peuvent accéder au service Azure Web PubSub de ce compte sans point de terminaison privé.

Cette contrainte résulte des modifications DNS effectuées lorsque le service Azure Web PubSub W2 crée un point de terminaison privé.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Règles de groupe de sécurité réseau pour les sous-réseaux avec des points de terminaison privés

Actuellement, vous ne pouvez pas configurer de règles de [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md) (NSG, Network Security Group) ni de routes définies par l’utilisateur pour des points de terminaison privés. Les règles NSG appliquées au sous-réseau hébergeant le point de terminaison privé sont appliquées au point de terminaison privé. Une solution de contournement limitée pour ce problème consiste à implémenter vos règles d’accès pour les points de terminaison privés sur les sous-réseaux sources, bien que cette approche puisse nécessiter une charge de gestion supérieure.

