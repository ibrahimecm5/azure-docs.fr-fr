---
title: Gestion de l’accès réseau public pour Azure IoT Hub
description: Documentation sur la désactivation et l’activation de l’accès au réseau public pour le hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: b77fa4d3e82114c4b52edca250aada9837157782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068274"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gestion de l’accès réseau public pour votre hub IoT

Pour limiter l’accès à un seul [point de terminaison privé pour un hub IoT dans votre réseau virtuel](virtual-network-support.md), désactivez l’accès au réseau public. Pour ce faire, utilisez le portail Azure ou l’API `publicNetworkAccess`. Vous pouvez également autoriser l’accès public à l’aide du portail ou de l’API `publicNetworkAccess`.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Désactiver l’accès au réseau public à l’aide du portail Azure

1. Accédez au [Portail Azure](https://portal.azure.com).
2. Accédez à votre hub IoT. Accédez à **Groupes de ressources**, choisissez le groupe approprié, puis sélectionnez votre IoT Hub.
3. Sélectionnez **Mise en réseau** dans le menu Place de marché.
4. Sous « Autoriser l’accès du réseau public à », sélectionnez **Désactivé**
5. Sélectionnez **Enregistrer**.

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Capture d’écran qui montre où désactiver l’accès au réseau public sur le Portail Azure.":::

Pour activer l’accès au réseau public, sélectionnez **Tous les réseaux**, puis **Enregistrer**.

### <a name="accessing-the-iot-hub-after-disabling-the-public-network-access"></a>Accès à IoT Hub après la désactivation de l’accès au réseau public

Une fois que l’accès au réseau public est désactivé, IoT Hub est uniquement accessible via [son point de terminaison privé de réseau virtuel à l’aide d’une liaison privée Azure](virtual-network-support.md). Cette restriction comprend l’accès par le biais du portail Azure, car les appels d’API au service IoT Hub sont effectués directement à l’aide de votre navigateur avec vos informations d’identification.

### <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Point de terminaison IoT Hub, adresse IP et ports après la désactivation de l’accès au réseau public

IoT Hub étant une plateforme PaaS (Platform as a service) multilocataire, différents clients partagent le même pool de ressources matérielles de calcul, de réseau et de stockage. Les noms d’hôtes d’IoT Hub sont mappés à un point de terminaison public avec une adresse IP de routage public sur Internet. Différents clients partagent ce point de terminaison public IoT Hub, et tous les appareils IoT qui se trouvent sur des réseaux étendus et sur des réseaux locaux peuvent y accéder. 

La désactivation de l’accès au réseau public est appliquée à une ressource IoT Hub spécifique, garantissant ainsi l’isolation. Afin que le service demeure actif pour d’autres ressources client à l’aide du chemin public, son point de terminaison public peut toujours être résolu, les adresses IP sont détectables et les ports restent ouverts. Cela n’est pas une source de préoccupation, car Microsoft intègre plusieurs couches de sécurité pour garantir un isolement complet entre les locataires. Pour plus d’informations, consultez [Isolation dans le cloud public Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

### <a name="ip-filter"></a>Filtre IP

Si l’accès au réseau public est désactivé, toutes les règles de [filtre IP](iot-hub-ip-filtering.md) sont ignorées. Cela est dû au fait que toutes les adresses IP de l’Internet public sont bloquées. Pour utiliser le filtre IP, utilisez l’option **Plage d’adresses IP sélectionnées**.

### <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Résolution de bogue via un point de terminaison compatible avec Event Hub intégré

Il existe un bogue dans IoT Hub où le [point de terminaison intégré compatible avec Event Hub](iot-hub-devguide-messages-read-builtin.md) continue d’être accessible via l’Internet public alors que l’accès du réseau public à IoT Hub est désactivé. Pour en savoir plus et nous contacter concernant ce bogue, consultez [Désactiver l’accès du réseau public pour IoT Hub désactive l’accès au point de terminaison Event Hub intégré](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).

## <a name="turn-on-network-access-using-azure-portal"></a>Activer l’accès au réseau public à l’aide du portail Azure

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez à votre hub IoT. Accédez à **Groupes de ressources**, choisissez le groupe approprié, puis sélectionnez votre Hub.
3. Sélectionnez **Mise en réseau** dans le menu Place de marché.
4. Sous « Autoriser l’accès du réseau public à », sélectionnez **Plages IP sélectionnées**.
5. Dans la boîte de dialogue **Filtre IP** qui s’affiche, sélectionnez **Ajouter votre adresse IP client** et entrez un nom et une plage d’adresses.
6. Sélectionnez **Enregistrer**. Si le bouton est grisé, vérifiez que l’adresse IP de votre client est déjà ajoutée en tant que filtre IP.

:::image type="content" source="./media/iot-hub-publicnetworkaccess/turn-on-public-network-access.png" alt-text="Capture d’écran qui montre où activer l’accès au réseau public sur le Portail Azure.":::

### <a name="turn-on-all-network-ranges"></a>Activer toutes les plages réseau

1. Accédez à votre hub IoT. Accédez à **Groupes de ressources**, choisissez le groupe approprié, puis sélectionnez votre Hub.
1. Sélectionnez **Mise en réseau** dans le menu Place de marché.
1. Sous Autoriser l’accès du réseau public à, sélectionnez **Tous les réseaux**.
1. Sélectionnez **Enregistrer**.

### <a name="check-iot-hub-access-using-cloud-shell"></a>Vérifier l’accès à IoT Hub à l’aide de Cloud Shell

Vous pouvez vérifier l’accès à IoT Hub à l’aide d’Azure Cloud Shell. Assurez-vous que vous avez activé toutes les plages réseau, puis exécutez les commandes suivantes. Remplacez « SubscriptionName » par le nom de votre abonnement et « MyIoTHub » par le nom de votre hub.

```azurecli
  az account set -s "SubscriptionName"
  az iot hub device-identity list --hub-name "MyIoTHub"
```

```azurepowershell
  Set-AzContext -Name "SubscriptionName"
  Get-AzIoTHubDevice -IotHubName "MyIoTHub"
```
### <a name="troubleshooting"></a>Résolution des problèmes

Si vous ne parvenez pas à accéder à votre IoT Hub, la configuration de votre réseau pose peut-être problème. Par exemple, si le message d’erreur suivant s’affiche lorsque vous tentez d’accéder à la page des appareils IoT, consultez la page **Mise en réseau** pour voir si l’accès au réseau public est désactivé ou limité aux plages d’adresses IP sélectionnées.

```
  Unable to retrieve devices. Please ensure that your network connection is online and network settings allow connections from your IP address.
```

Pour obtenir l’accès à IoT Hub, demandez à votre administrateur informatique d’ajouter votre adresse IP à la plage d’adresses IP ou d’autoriser l’accès au réseau public à tous les réseaux. Si le problème persiste, vérifiez les paramètres de votre réseau local ou contactez son administrateur pour résoudre les problèmes de connectivité à IoT Hub. Par exemple, parfois, un proxy dans le réseau local peut interférer avec l’accès à IoT Hub.

Si les commandes précédentes ne fonctionnent pas ou si vous ne pouvez pas activer toutes les plages réseau, contactez le support technique de Microsoft.
