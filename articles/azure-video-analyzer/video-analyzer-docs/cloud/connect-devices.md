---
title: Connecter des appareils à Azure Video Analyzer
description: Cet article explique comment connecter des appareils à Azure Video Analyzer
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70ad7876b56b12c6a9d6fd1654e12dc0a764aa00
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339969"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Connecter des appareils à Azure Video Analyzer

[!INCLUDE [header](includes/cloud-env.md)]

Pour capturer et enregistrer des vidéos à partir d’un appareil, le service Azure Video Analyzer doit établir une connexion [RTSP](../terminology.md#rtsp) à celui-ci. Si l’appareil se trouve derrière un pare-feu, ces connexions sont bloquées et il n’est pas toujours possible de créer des règles pour autoriser les connexions entrantes à partir d’Azure. Pour prendre en charge ces appareils, vous pouvez créer et installer une implémentation de l’appareil [Azure IoT Plug and Play](../../../iot-develop/overview-iot-plug-and-play.md), qui écoute les commandes envoyées via IoT Hub à partir de Video Analyzer, puis ouvre un tunnel WebSocket sécurisé vers le service. Une fois ce tunnel établi, Video Analyzer peut alors se connecter au serveur RTSP.

## <a name="overview"></a>Vue d’ensemble 

Cet article fournit des concepts généraux sur la création d’une implémentation d’appareil Azure IoT PnP qui peut permettre à Video Analyzer de capturer et d’enregistrer des vidéos à partir d’un appareil. 

L’application doit : 

1. S’exécuter en tant qu’appareil IoT 
1. Implémenter l’interface [IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md) avec une commande spécifique (`tunnelOpen`) 
1. Lors de la réception d’une telle commande : 
   * Valider les arguments reçus 
   * Ouvrir une connexion WebSocket sécurisée à l’URL indiquée à l’aide du jeton fourni
   * Transférer les octets WebSocket vers la connexion TCP du serveur RTSP de l’appareil photo

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="Connecter des appareils au cloud":::

## <a name="run-as-an-iot-device"></a>S’exécuter en tant qu’appareil IoT 

L’application Video Analyzer sera déployée en tant que plug-in PnP Video Analyzer. Cela nécessite l’utilisation de l’un des kits [Azure IoT device SDK](../../../iot-develop/libraries-sdks.md#device-sdks) pour créer votre implémentation d’appareil IoT PnP. Inscrivez l’appareil IoT auprès de votre hub IoT pour obtenir l’ID de l’appareil IoT Hub et la chaîne de connexion de l’appareil.

### <a name="iot-device-clientconfiguration"></a>Configuration du client de l’appareil IoT

* Définissez OPTION_MODEL_ID sur `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”` pour prendre en charge les requêtes PnP  
* Vérifiez que votre appareil utilise le protocole MQTT ou MQTT sur WebSockets pour se connecter à Azure IoT Hub 
    * Connectez-vous à IoT Hub via un proxy HTTPS s’il est configuré sur l’appareil IoT  
* Inscrivez un rappel pour la méthode directe `tunnelOpen`  

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>Implémenter l’interface IoT PnP pour Video Analyzer

Le modèle [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) suivant décrit un appareil qui peut se connecter à Video Analyzer.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:azure:videoanalyzer:WebSocketTunneling;1",
  "@type": "Interface",
  "displayName": "Azure Video Analyzer Web Socket Tunneling",
  "description": "This interface enables media publishing to Azure Video Analyzer service from a RTSP compatible device which is located behind a firewall or NAT device.",
  "contents": [
    {
      "@type": "Command",
      "displayName": "Tunnel Open",
      "name": "tunnelOpen",
      "request": {
        "@type": "CommandPayload",
        "displayName": "Parameters",
        "name": "parameters",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "displayName": "Remote Endpoint",
              "description": "The remote endpoint for the web socket tunnel.",
              "name": "remoteEndpoint",
              "schema": "string"
            },
            {
              "displayName": "Remote Authorization Token",
              "description": "The bearer token for the web socket authentication.",
              "name": "remoteAuthorizationToken",
              "schema": "string"
            },
            {
              "displayName": "Local Port",
              "description": "The local port where web socket data should be tunneled to.",
              "name": "localPort",
              "schema": "integer"
            }
          ]
        }
      }
    }
  ]
}
```

L’appareil IoT inscrit une méthode directe `tunnelOpen`, où le corps de la demande aura les paramètres `remoteEndpoint`, `remoteAuthorizationToken` et `localPort` comme indiqué ci-dessus.

## <a name="implement-the-direct-method-tunnelopen"></a>Implémentez la méthode directe `tunnelOpen`
Lorsque la méthode directe `tunnelOpen` est appelée par le service Video Analyzer, l’application doit effectuer les opérations suivantes :

1. Obtenir le ou les ports RTSP disponibles de l’appareil
1. Comparer la valeur `localPort` spécifiée dans l’appel de méthode directe aux ports disponibles
   * Retourner **BadRequest** si aucune correspondance n’est trouvée (voir la section Réponses d’erreur ci-dessous)
1. Ouvrir une connexion TCP à « (IP ou nom d’hôte de l’appareil photo) :`localPort` »
   * Retourner **BadRequest** si la connexion échoue
   * REMARQUE : Le nom d’hôte est généralement **localhost**
1. Ouvrez une connexion de socket web au `remoteEndpoint` (via un proxy s’il est configuré sur l’appareil)
   * Définissez l’en-tête HTTP « Authorization » sur « Bearer (remoteAuthorizationToken) »
   * Définissez l’en-tête « TunnelConnectionSource » avec la valeur « PnpDevice »
   * Définissez User-Agent sur une valeur appropriée qui vous aidera à identifier votre implémentation. 
      * Par exemple, vous pouvez capturer l’architecture de l’UC, le système d’exploitation, le modèle/la marque de l’appareil.
   * Retournez 200 OK si la connexion de socket web a réussi, sinon retournez le code d’erreur approprié
1. Retournez une réponse (pas de blocage)
1. L’implémentation d’un appareil IoT PnP commence à envoyer des données TCP de manière bidirectionnelle entre le WebSocket et la connexion TCP du serveur RTSP

Le service Video Analyzer retente les demandes `tunnelOpen` en cas d’échec, si bien que les nouvelles tentatives ne sont pas nécessaires dans l’application.

### <a name="error-responses"></a>Réponses d’erreur
Si la demande `tunnelOpen` échoue, le corps de la réponse doit se présenter comme suit

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
Voici quelques exemples de ces réponses d’erreur :

* Le port local n’est pas disponible en tant que port RTSP ou RTSPS {« code » : « 400 », « cible » : « (IP ou nom d’hôte de l’appareil photo) : {localPort} », « message » : « Le port local n’est pas disponible »}
* Délai d’attente/Impossible de se connecter au point de terminaison RTSP {« code » : « 400 », « cible » : « (IP ou nom d’hôte de l’appareil photo) : {localPort} », « message » : « Impossible de se connecter au point de terminaison RTSP »}
*   Délai d’attente/Réponse d’erreur de la tentative de connexion de socket web {« code » : « {code de réponse WebSocket} », « cible » : « {remoteEndpoint} », « message » : « {message d’erreur de réponse de socket web} »}


## <a name="ingestion-to-video-analyzer"></a>Ingestion dans Video Analyzer
Pour capturer et enregistrer des vidéos sur Video Analyzer, une topologie de pipeline avec activation du tunneling doit être créée. À partir de cette topologie, un pipeline en direct doit être créé et activé. [Les instructions pour ce processus sont décrites ici.](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>Exemple d'implémentation
Contactez videoanalyzerhelp@microsoft.com si vous souhaitez implémenter une application sur votre appareil pour la connecter à Video Analyzer.

## <a name="see-also"></a>Voir aussi 

[Qu’est ce qu’IoT Plug-and-Play ?](../../../iot-develop/overview-iot-plug-and-play.md)
