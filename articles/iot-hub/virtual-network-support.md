---
title: Prise en charge d’Azure IoT Hub pour les réseaux virtuels
description: Guide pratique pour utiliser le modèle de connectivité des réseaux virtuels avec IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: jlian
ms.openlocfilehash: bd5a88bb8fc47e5f4815aea57544d6093281999b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258840"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Prise en charge par IoT Hub des réseaux virtuels avec Private Link et Managed Identity

Par défaut, les noms d'hôtes d’IoT Hub sont mappés à un point de terminaison public avec une adresse IP de routage public sur Internet. Différents clients partagent ce point de terminaison public IoT Hub, et tous les appareils IoT qui se trouvent sur des réseaux étendus et sur des réseaux locaux peuvent y accéder.

![Point de terminaison public IoT Hub](./media/virtual-network-support/public-endpoint.png)

Les fonctionnalités IoT Hub, notamment le [routage de messages](./iot-hub-devguide-messages-d2c.md), le [chargement de fichiers](./iot-hub-devguide-file-upload.md) et [l'importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md) ont également besoin d’une connectivité IoT Hub vers une ressource Azure appartenant au client via son point de terminaison public. L’ensemble de ces chemins de connectivité constitue le trafic de sortie entre IoT Hub et les ressources du client.

Plusieurs raisons peuvent vous pousser à restreindre la connectivité aux ressources Azure (y compris IoT Hub) via votre propre réseau virtuel. Les raisons sont les suivantes :

* Introduire un isolement réseau pour votre hub IoT en empêchant l’exposition de la connectivité à l’Internet public.

* Permettre une expérience de connectivité privée à partir de vos ressources réseau sur site en garantissant que vos données et votre trafic sont transmis directement au réseau principal Azure.

* Empêcher les attaques par exfiltration à partir de réseaux locaux sensibles. 

* Suivre des modèles de connectivité établis à l'échelle d’Azure en utilisant des [points de terminaison privés](../private-link/private-endpoint-overview.md).

Cet article explique comment atteindre ces objectifs en utilisant [Azure Private Link](../private-link/private-link-overview.md) pour la connectivité d'entrée vers IoT Hub et l'exception des services Microsoft approuvés pour la connectivité de sortie d’IoT Hub vers d'autres ressources Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Connectivité d’entrée vers IoT Hub avec Azure Private Link

Un point de terminaison privé est une adresse IP privée attribuée à l'intérieur d'un réseau virtuel appartenant au client, qui permet l’accès à une ressource Azure. Avec Azure Private Link, vous avez la possibilité de configurer un point de terminaison privé grâce auquel votre hub IoT pourra autoriser les services situés à l'intérieur de votre réseau virtuel à atteindre IoT Hub sans avoir besoin d'envoyer du trafic au point de terminaison public d’IoT Hub. De même, vos appareils locaux peuvent utiliser un [réseau privé virtuel (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou un peering [ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour obtenir une connectivité à votre réseau virtuel et à votre hub IoT (via son point de terminaison privé). Il vous est ainsi possible de restreindre ou de bloquer entièrement la connectivité aux points de terminaison publics de votre hub IoT à l’aide du [filtre IP IoT Hub](./iot-hub-ip-filtering.md) ou du [bouton bascule d’accès au réseau public](iot-hub-public-network-access.md). Cette approche permet de maintenir la connectivité des appareils à votre hub à l’aide du point de terminaison privé. Cette configuration s’adresse principalement aux appareils situés à l'intérieur d'un réseau local. Cette configuration n'est pas conseillée pour les appareils déployés sur un réseau étendu.

![Entrée du réseau virtuel IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Avant de commencer, assurez-vous que les conditions préalables suivantes sont remplies :

* Vous avez [créé un réseau virtuel Azure](../virtual-network/quick-create-portal.md) avec un sous-réseau dans lequel sera créé le point de terminaison privé.

* Pour les appareils qui fonctionnent sur des réseaux locaux, configurez un [réseau privé virtuel (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou un peering privé [ExpressRoute](https://azure.microsoft.com/services/expressroute/) dans votre réseau virtuel Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurer un point de terminaison privé pour l'entrée IoT Hub

Un point de terminaison privé fonctionne pour des API d’appareil IoT Hub (comme des messages appareil-à-cloud), ainsi que des API de service (comme la création et la mise à jour d’appareils).

1. Dans le portail Azure, sélectionnez **Mise en réseau**, **Accès privé**, puis cliquez sur l’option **+ Créer un point de terminaison privé**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Capture d’écran montrant où ajouter un point de terminaison privé pour IoT Hub" border="true":::

1. Indiquez l’abonnement, le groupe de ressources, le nom et la région dans laquelle sera créé le nouveau point de terminaison privé. Idéalement, il doit s’agir de la même région que celle de votre hub.

1. Cliquez sur **Suivant : Ressource**, précisez l’abonnement de votre ressource IoT Hub, puis sélectionnez le type de ressource **Microsoft.Devices/IotHubs**, le nom de votre hub IoT comme **ressource** et la sous-ressource cible **iotHub**.

1. Cliquez sur **Suivant : Configuration** et indiquez le réseau virtuel et le sous-réseau où le point de terminaison privé sera créé. Sélectionnez l'option d'intégration dans zone DNS privée Azure, si vous le souhaitez.

1. Cliquez sur **Suivant : Balises**, et spécifiez si nécessaire les balises de votre ressource.

1. Cliquez sur **Vérifier + Créer** pour créer votre ressource de liaison privée.

### <a name="built-in-event-hub-compatible-endpoint"></a>Point de terminaison intégré compatible avec Event Hubs 

Le [point de terminaison intégré compatible avec Event Hubs](iot-hub-devguide-messages-read-builtin.md) est également accessible via un point de terminaison privé. Lorsque la liaison privée est configurée, vous devez voir une connexion de point de terminaison privé supplémentaire pour le point de terminaison intégré. C’est celle qui inclut `servicebus.windows.net` dans le nom de domaine complet.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Image présentant deux points de terminaison privés en fonction de chaque liaison privée IoT Hub":::

Le filtre IP [d’IoT Hub](iot-hub-ip-filtering.md) peut contrôler l’accès public au point de terminaison intégré. 

Pour bloquer complètement l’accès réseau public à votre IoT Hub, [désactivez l’accès au réseau public](iot-hub-public-network-access.md) ou utilisez le filtre IP pour bloquer toutes les adresses IP, puis sélectionnez l’option permettant d’appliquer des règles au point de terminaison intégré.

### <a name="pricing-for-private-link"></a>Tarifs de la liaison privée

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connectivité entrante d’IoT Hub vers d'autres ressources Azure

IoT Hub peut se connecter à vos ressources Stockage Blob Azure, Event Hubs et Service Bus pour le [routage des messages](./iot-hub-devguide-messages-d2c.md), le [chargement des fichiers](./iot-hub-devguide-file-upload.md) et [l'importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md), via le point de terminaison public des ressources. Le fait de lier une ressource à un réseau virtuel bloque par défaut la connectivité à la ressource. Par conséquent, cette configuration empêche IoT Hub d’envoyer des données à vos ressources. Pour corriger ce problème, activez la connectivité entre votre ressource IoT Hub et vos ressources de compte de stockage, Event Hubs ou Service Bus avec l'option du **service Microsoft approuvé**. 

Votre hub IoT doit utiliser l’identité managée pour que d’autres services puissent le trouver en tant que service Microsoft approuvé. Une fois qu’une identité managée est approvisionnée, vous devez accorder l’autorisation Azure RBAC à l’identité managée de votre hub pour accéder à votre point de terminaison personnalisé. Suivez l’article [Prise en charge des identités managées dans IoT Hub](./iot-hub-managed-identity.md) pour approvisionner une identité managée avec l’autorisation Azure RBAC et ajouter le point de terminaison personnalisé à votre hub IoT. Si les configurations du pare-feu sont en place, veillez à activer l’exception des premiers tiers Microsoft approuvés pour autoriser l’accès de votre hub IoT au point de terminaison personnalisé.

### <a name="pricing-for-trusted-microsoft-service-option"></a>Tarification de l’option de service Microsoft approuvé
La fonctionnalité d'exception des services internes Microsoft approuvés est gratuite. Les frais pour les comptes de stockage provisionnés, les hubs d'événements ou les ressources Service Bus s'appliquent séparément.
## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens ci-dessous pour en savoir plus sur les fonctionnalités IoT Hub :

* [Routage de messages](./iot-hub-devguide-messages-d2c.md)
* [Chargement de fichiers](./iot-hub-devguide-file-upload.md)
* [Importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md)
