---
title: Gérer l’accès au réseau public pour Azure IoT Hub Device Provisioning (DPS)
description: Documentation sur la manière d’activer et de désactiver l’accès au réseau public pour Azure IoT Hub Device Provisioning (DPS)
ms.author: v-stharr
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 74c22d802c022d51a1ef8b4aa231cc92661d582b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858630"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>Gérer l’accès au réseau public pour votre service d’approvisionnement d’appareils IoT

Pour limiter l’accès à [un point de terminaison privé pour DPS dans votre réseau virtuel](virtual-network-support.md), désactivez l’accès au réseau public. Pour ce faire, utilisez le portail Azure ou l’API `publicNetworkAccess`. Vous pouvez également autoriser l’accès public à l’aide du portail ou de l’API `publicNetworkAccess`.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Désactiver l’accès au réseau public à l’aide du portail Azure

Pour désactiver l’accès au réseau public :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.
3. Sélectionnez votre service Device Provisioning.
4. Dans le menu **Paramètres** à gauche, sélectionnez *Mise en réseau*.
5. Sous **Accès au réseau public**, sélectionnez *Désactivé*.
6. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="Image qui montre le portail Azure où désactiver l’accès au réseau public" :::

Pour activer l’accès au réseau public :

1. Sélectionnez **Tous les réseaux**.
2. Sélectionnez **Enregistrer**.

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>Accéder au DPS après la désactivation de l’accès au réseau public

Une fois l’accès au réseau public désactivé, l’instance DPS n’est accessible que via [son point de terminaison privé de réseau virtuel à l’aide d’une liaison privée Azure](virtual-network-support.md). Cette restriction inclut l’accès via le portail Azure.

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Point de terminaison DPS, adresse IP et ports après la désactivation de l’accès au réseau public

DPS est une plateforme PaaS (Platform as a service) mutualisée via laquelle différents clients partagent le même pool de ressources matérielles de calcul, de réseau et de stockage. Les noms d’hôtes de DPS sont mappés à un point de terminaison public avec une adresse IP de routage public sur Internet. Différents clients partagent ce point de terminaison public de DPS, et tous les appareils IoT qui se trouvent sur des réseaux étendus et sur des réseaux locaux peuvent y accéder. 

La désactivation de l’accès au réseau public est appliquée à une ressource de DPS spécifique, garantissant ainsi l’isolation. Afin que le service demeure actif pour d’autres ressources client à l’aide du chemin public, son point de terminaison public peut toujours être résolu, les adresses IP sont détectables et les ports restent ouverts. Cela n’est pas une source de préoccupation, car Microsoft intègre plusieurs couches de sécurité pour garantir un isolement complet entre les locataires. Pour plus d’informations, consultez [Isolation dans le cloud public Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtre IP

Si l’accès au réseau public est désactivé, toutes les règles de [filtre IP](../iot-dps/iot-dps-ip-filtering.md) sont ignorées. Cela est dû au fait que toutes les adresses IP de l’Internet public sont bloquées. Pour utiliser le filtre IP, utilisez l’option **Plage d’adresses IP sélectionnées**.

### <a name="turn-on-all-network-ranges"></a>Activer toutes les plages réseau

Pour activer toutes les plages réseau :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.
3. Sélectionnez votre service Device Provisioning.
4. Dans le menu **Paramètres** à gauche, sélectionnez *Mise en réseau*.
5. Sous **Accès au réseau public**, sélectionnez *Tous les réseaux*.
6. Sélectionnez **Enregistrer**.
