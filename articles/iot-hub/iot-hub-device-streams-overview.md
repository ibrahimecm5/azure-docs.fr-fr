---
title: Flux d’appareils Azure IoT Hub | Microsoft Docs
description: Vue d’ensemble des flux d’appareils Azure IoT Hub, qui facilitent la création de tunnels TCP bidirectionnels sécurisés pour divers scénarios de communication cloud-à-appareil.
author: eross-msft
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: lizross
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 2bedbb4e14886e1e16d765f91ad86f7b20989a34
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555999"
---
# <a name="iot-hub-device-streams-preview"></a>Flux d'appareils IoT Hub (préversion)

Les *flux d’appareils* Azure IoT Hub facilitent la création de tunnels TCP bidirectionnels sécurisés pour divers scénarios de communication cloud-à-appareil. Un flux d'appareil est régi par un *point de terminaison de streaming* IoT Hub qui fait office de proxy entre votre appareil et les points terminaison de service. Illustrée dans le diagramme ci-dessous, cette configuration est particulièrement utile quand les appareils sont situés derrière un pare-feu réseau ou à l’intérieur d’un réseau privé. Dès lors, les flux d’appareils IoT Hub permettent aux clients d'accéder aux appareils IoT de manière sécurisée via un pare-feu, sans devoir trop ouvrir les ports de pare-feu de réseau entrants ou sortants.

![« Vue d’ensemble des flux d’appareils IoT Hub »](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Grâce aux flux d’appareils IoT Hub, les appareils restent sécurisés et les connexions TCP sortantes vers le point de terminaison de streaming IoT Hub interviennent exclusivement sur le port 443. Une fois le flux établi, les applications côté service et côté appareil bénéficient chacune d’un accès programmatique à un objet client WebSocket afin d’échanger entre elles des octets bruts. Ce tunnel offre des garanties de fiabilité à égalité avec TCP.

## <a name="benefits"></a>Avantages

Les flux d'appareils IoT Hub offrent les avantages suivants :

* **Connectivité sécurisée via un pare-feu :** Les appareils IoT sont accessibles depuis les points de terminaison de service, sans ouverture du port du pare-feu entrant au niveau de l'appareil ou du réseau (seule une connectivité sortante vers IoT Hub est requise sur le port 443).

* **Authentication (Authentification) :** Le côté appareil et le côté service du tunnel doivent s'authentifier auprès de IoT Hub à l'aide des informations d'identification correspondantes.

* **Chiffrement :** Par défaut, les flux d'appareils IoT Hub utilisent des connexions TSL. Ainsi, le trafic est chiffré, indépendamment du fait que l'application utilise ou non le chiffrement.

* **Simplicité de la connectivité :** Dans de nombreux cas, grâce aux flux d’appareils, la connectivité aux appareils IoT ne requiert plus de configuration complexe des réseaux privés virtuels.

* **Compatibilité avec la pile TCP/IP :** Les flux d'appareils IoT Hub peuvent prendre en charge le trafic des applications TCP/IP. Ainsi, un large éventail de protocoles propriétaires et de protocoles basés sur des normes peuvent exploiter cette fonctionnalité.

* **Facilité d'utilisation dans des configurations de réseau privé :** Le service peut communiquer avec un appareil en référençant son ID d’appareil plutôt que son adresse IP. Cela s'avère particulièrement utile lorsqu'un appareil est situé à l'intérieur d’un réseau privé et dispose d'une adresse IP privée, ou que son adresse IP est attribuée de manière dynamique et n'est pas connue côté service.

## <a name="device-stream-workflows"></a>Workflows des flux d’appareils

Un flux d'appareil est initié lorsque le service demande à se connecter à un appareil en fournissant son ID d'appareil. Ce workflow s’adapte particulièrement au modèle de communication client/serveur, dont SSH et RDP, quand un utilisateur envisage de se connecter à distance au serveur SSH ou RDP en cours d’exécution sur l’appareil à l’aide d’un programme client SSH ou RDP.

Le processus de création de flux d'appareils implique une négociation entre l’appareil, le service, ainsi que le point de terminaison principal et le point de terminaison de streaming du hub IoT. Le point de terminaison principal du hub IoT orchestre la création d’un flux d'appareil et le point de terminaison de streaming gère le trafic entre le service et l'appareil.

### <a name="device-stream-creation-flow"></a>Flux de création de flux d'appareils

La création programmatique d'un flux d'appareil à l’aide du SDK implique les étapes suivantes, également représentées dans la figure ci-dessous :

![« Processus de liaison de flux d’appareils »](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. L’application de l'appareil enregistre à l'avance un rappel pour être informé chaque fois qu'un nouveau flux d'appareil est initié vers l'appareil. Cette étape intervient généralement lorsque l'appareil démarre et se connecte à IoT Hub.

2. Si besoin, le programme côté service initie un flux d'appareil en fournissant l'ID d'appareil (_pas_ l’adresse IP).

3. IoT Hub informe le programme côté client en appelant le rappel enregistré à l’étape 1. L’appareil peut accepter ou rejeter la requête d’initiation de flux. Cette logique peut être spécifique à votre scénario d’application. Si la requête de flux est rejetée par l’appareil, IoT Hub en informe le service. Si elle est acceptée, les étapes ci-dessous s'ensuivent.

4. L’appareil crée une connexion TCP sortante sécurisée vers le point de terminaison de streaming sur le port 443, et met à niveau la connexion vers un WebSocket. L’URL du point de terminaison de streaming ainsi que les informations d’identification à utiliser à des fins d'authentification sont fournies à l'appareil par IoT Hub dans le cadre de la requête envoyée à l’étape 3.

5. Si l’appareil accepte le flux, le service en est informé et crée son propre client WebSocket vers le point de terminaison de streaming. De même, il reçoit les informations d’authentification et l'URL du point de terminaison de streaming de IoT Hub.

Dans le processus de liaison ci-dessus :

* Le processus de liaison doit se terminer sous 60 secondes (étapes 2 à 5), à défaut de quoi la liaison échoue et le service en est informé.

* Une fois le flux de création ci-dessus terminé, le point de terminaison de streaming fait office de proxy et transfert le trafic entre le service et l’appareil via leurs WebSockets respectifs.

* L'appareil et le service doivent disposer d'une connectivité sortante vers le point de terminaison principal IoT Hub, ainsi que vers le point de terminaison de streaming sur le port 443. L’URL de ces points de terminaison est disponible sous l’onglet *Vue d’ensemble* du portail IoT Hub.

* Un flux établi offre des garanties de fiabilité à égalité avec TCP.

* Toutes les connexions vers IoT Hub et le point de terminaison de streaming utilisent TLS et sont chiffrées.

### <a name="termination-flow"></a>Flux de fin

Un flux établi prend fin lorsqu'une des connexions TCP vers la passerelle est déconnectée (par le service ou l'appareil). Cela peut intervenir de manière volontaire, en fermant le WebSocket sur les programmes de l'appareil ou du service, ou de manière involontaire, en cas d'expiration du délai de connectivité réseau ou d'échec du processus. Lorsqu'une connexion de l'appareil ou du service vers le point de terminaison de streaming prend fin, l'autre connexion TCP est également interrompue (de manière forcée), et l'appareil et le service doivent recréer le flux, si besoin.

## <a name="connectivity-requirements"></a>Connectivité requise

Les côtés appareil et service d'un flux d'appareil doivent tous deux pouvoir établir des connexions TLS à IoT Hub et son point de terminaison de streaming. Cela nécessite une connectivité sortante sur le port 443 vers ces points de terminaison. Le nom d'hôte associé à ces points de terminaison est disponible sous l'onglet *Vue d’ensemble* de IoT Hub, comme illustré dans la figure ci-dessous :

![« Points de terminaison des flux d'appareils »](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Vous pouvez aussi récupérer les informations relatives aux points de terminaison en utilisant Azure CLI sous la section des propriétés du hub, dans les clés `property.hostname` et `property.deviceStreams`.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

La sortie est un objet JSON de tous les points de terminaison auxquels l’appareil et le service de votre concentrateur doivent se connecter afin d’établir un flux d’appareils.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Assurez-vous d’avoir installé Azure CLI version 2.0.57 ou ultérieure. Vous pouvez télécharger la dernière version à partir de la page [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Autoriser la connectivité sortante aux points de terminaison de flux d’appareils

Comme indiqué au début de cet article, votre appareil crée une connexion sortante vers le point de terminaison de streaming IoT Hub lors du processus d’initiation des flux d’appareils. Les pare-feu de l’appareil ou de son réseau doivent autoriser les connexions sortantes vers la passerelle de streaming sur le port 443 (notez que cette communication s’effectue via une connexion WebSocket chiffrée à l’aide de TLS).

Le nom d'hôte du point de terminaison de streaming de l'appareil se trouve sous l'onglet Vue d'ensemble du portail Azure IoT Hub. ![« Points de terminaison des flux d’appareils »](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Ces informations sont également disponibles en utilisant Azure CLI :

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Assurez-vous d’avoir installé Azure CLI version 2.0.57 ou ultérieure. Vous pouvez télécharger la dernière version à partir de la page [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Détecter un problème via les journaux de ressources des flux d’appareils

Vous pouvez configurer Azure Monitor de manière à collecter les [journaux de ressources des flux d’appareils](monitor-iot-hub-reference.md#device-streams-preview) émis par votre hub IoT. Ce journal peut s'avérer particulièrement utile à des fins de résolution des problèmes.

Pour créer un paramètre de diagnostic et envoyer les journaux de flux d’appareils pour votre hub IoT vers les journaux Azure Monitor :

1. Dans le portail Azure, accédez à votre hub IoT. Dans le volet de gauche, sous **Supervision**, sélectionnez **Paramètres de diagnostic**. Sélectionnez ensuite **Ajouter un paramètre de diagnostic**.

2. Donnez un nom à votre paramètre de diagnostic et sélectionnez **DeviceStreams** dans la liste des journaux. Sélectionnez ensuite **Envoyer à Log Analytics**. Vous serez invité à sélectionner un espace de travail Log Analytics existant ou à en créer un.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Activer les journaux d’activité des flux d’appareils":::

3. Après avoir créé un paramètre de diagnostic pour envoyer les journaux de flux d’appareils à un espace de travail Log Analytics, vous pouvez accéder aux journaux en sélectionnant **Journaux** sous **Supervision** dans le volet de gauche de votre hub IoT sur le portail Azure. Les journaux des flux d’appareils apparaissent dans la table `AzureDiagnostics` et ont `Category=DeviceStreams`. Notez que l’affichage des journaux dans la table peut prendre plusieurs minutes.

   Comme indiqué ci-dessous, l’identité de l’appareil cible ainsi que le résultat de l’opération sont également disponibles dans les journaux d’activité.

   ![« Accéder aux journaux d’activité des flux d’appareils »](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Pour en savoir plus sur d’Azure Monitor avec IoT Hub, consultez [Superviser avec IoT Hub](monitor-iot-hub.md). Pour plus d’informations sur les journaux de ressources, métriques et tables disponibles pour IoT Hub, consultez [Informations de référence sur l’analyse des données Azure IoT Hub](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Disponibilité régionale

Pendant la phase de préversion publique, les flux d’appareils IoT Hub sont disponibles dans les régions USA Centre, EUAP USA Centre, Europe Nord et Asie Sud-Est. Veillez à créer votre hub dans l’une de ces régions.

## <a name="sdk-availability"></a>Disponibilité du kit de développement logiciel (SDK)

Deux côtés de chaque flux (côté appareil et côté service) utilisent le SDK IoT Hub pour établir le tunnel. Dans le cadre de la préversion publique, les clients peuvent sélectionner les langages de SDK suivants :

* Les SDK C et C# prennent en charge les flux d'appareils côté appareil.

* Les SDK NodeJS et C# prennent en charge les flux d'appareils côté service.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens ci-dessous pour en savoir plus sur les flux d’appareils.

> [!div class="nextstepaction"]
> [Émission Flux d’appareils sur IoT (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
