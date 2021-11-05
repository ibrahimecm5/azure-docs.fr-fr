---
title: Vue d’ensemble des connexions du point de terminaison privé
description: Cet article est une vue d’ensemble des connexions de point de terminaison privé avec Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 346630425cd6a77c7ebf18b6f16a1bbd1bcab705
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096699"
---
# <a name="private-endpoint-connections-overview"></a>Vue d’ensemble des connexions du point de terminaison privé

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article est une vue d’ensemble des connexions de point de terminaison privé avec Media Services.

## <a name="clients-using-vnet"></a>Clients utilisant un réseau virtuel

Les clients sur un réseau virtuel utilisant un point de terminaison privé doivent utiliser le même nom DNS pour se connecter à Media Services en tant que clients aux points de terminaison publics de Media Services. Media Services s’appuie sur la résolution DNS pour router automatiquement les connexions entre le réseau virtuel et les points de terminaison Media Services sur une liaison privée.

> [!IMPORTANT]
> Utilisez les mêmes noms DNS pour les points de terminaison Media Services lorsque vous vous servez de points de terminaison privés. Veuillez ne pas vous connecter aux points de terminaison Media Services à l’aide de son URL de sous-domaine privatelink.

Media Services crée une [zone DNS privée](/dns/private-dns-overview) attachée au réseau virtuel avec les mises à jour nécessaires pour les points de terminaison privés, par défaut. Toutefois, si vous utilisez votre propre serveur DNS, vous devrez peut-être apporter des modifications supplémentaires à votre configuration DNS. La section sur les modifications DNS ci-dessous décrit les mises à jour requises pour les points de terminaison privés.

## <a name="dns-changes-for-private-endpoints"></a>Modifications DNS pour les points de terminaison privés

Lorsque vous créez un point de terminaison privé, l’enregistrement de ressources **DNS CNAME** des points de terminaison Media Services est remplacé dans un sous-domaine par un alias doté du préfixe `privatelink`. Par défaut, nous créons également une zone DNS privée correspondant au sous-domaine `privatelink`, avec les enregistrements de ressource DNS A pour les points de terminaison privés.

Quand vous résolvez un nom DNS Media Services à l’extérieur du réseau virtuel avec le point de terminaison privé, il correspond au point de terminaison public de Media Services. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL de Media Services correspond à l’adresse IP du point de terminaison privé.

Par exemple, les enregistrements de ressources DNS pour un point de terminaison de streaming dans Media Services `MediaAccountA`, lorsqu’ils sont résolus à l’extérieur du réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Nom | Type | Value |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | Un | `<Streaming Endpoint public IP address>` |

Vous pouvez refuser ou restreindre l’accès internet public aux points de terminaison Media Services à l’aide d’une liste d’adresses IP autorisées ou en désactivant l’accès au réseau public pour toutes les ressources du compte.

Les enregistrements de ressources DNS pour l’exemple de point de terminaison de streaming, lorsqu’ils sont résolus par un client dans le réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | Un | `<Streaming Endpoint public endpoint>`, par example 10.0.0.9 |

Cette approche permet d’accéder au point de terminaison Media Services à l’aide du même nom DNS pour les clients au sein du réseau virtuel hébergeant les points de terminaison privés. Elle effectue la même opération pour les clients en dehors du réseau virtuel.

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients peuvent résoudre le nom de domaine complet du point de terminaison Media Services vers l’adresse IP du point de terminaison privé. Configurez votre serveur DNS pour déléguer votre sous-domaine de liaison privée à la zone DNS privée du réseau virtuel, ou configurer les enregistrements A pour  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` avec l’adresse IP du point de terminaison privé.

> [!TIP]
> Lorsque vous utilisez un serveur DNS personnalisé ou local, vous devez configurer votre serveur DNS pour résoudre le nom du point de terminaison Media Services dans le sous-domaine privatelink vers l’adresse IP du point de terminaison privé. Pour ce faire, vous pouvez déléguer le sous-domaine privatelink à la zone DNS privée du réseau virtuel ou configurer la zone DNS sur votre serveur DNS et ajouter les enregistrements A DNS.

Les noms de zone DNS recommandés pour les points de terminaison privés des services de stockage et les sous-ressources cibles de point de terminaison associées sont les suivants :

| Point de terminaison Media Services | ID de groupe de liaison privée | Nom de la zone DNS |
| ----------------------- | --------------------- | ------------- |
| Point de terminaison de streaming | streamingendpoint | privatelink.media.azure.net |
| Remise de clés | streamingendpoint | privatelink.media.azure.net |
| Événement en direct | liveevent | privatelink.media.azure.net |

Pour plus d’informations sur la configuration de votre propre serveur DNS pour la prise en charge des points de terminaison privés, reportez-vous aux articles suivants :

- [Résolution de noms pour des ressources dans les réseaux virtuels Azure](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configuration DNS pour les points de terminaison privés](/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>Indicateur d’accès au réseau public

L’indicateur `publicNetworkAccess` sur le compte Media Services peut être utilisé pour autoriser ou bloquer l’accès aux points de terminaison de Media Services à partir de l’Internet public. Lorsque `publicNetworkAccess` est désactivé, les requêtes adressées à un point de terminaison Media Services à partir de l’internet public sont bloquées ; les requêtes adressées aux points de terminaison privés sont toujours autorisées.  

## <a name="service-level-ip-allowlists"></a>Liste d’adresses IP autorisées de niveau de service

Lorsque `publicNetworkAccess` est activé, les requêtes provenant de l’Internet public sont autorisées, soumises à la liste d’adresses IP autorisées du niveau de service. Si `publicNetworkAccess` est désactivé, les requêtes émanant de l’Internet public sont bloquées, quels que soient les paramètres de la liste d’adresses IP autorisées. Les listes d’adresses IP autorisées s’appliquent uniquement aux requêtes provenant de l’Internet public ; les requêtes adressées à des points de terminaison privés ne sont pas filtrées par la liste d’adresses IP autorisées.
