---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: 17f32b1500f5218a1a6668a875c63bfd64de83b9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860350"
---
Quand vous exécutez le pipeline en direct, les résultats du nœud du processeur de détecteur de mouvements passe par le nœud récepteur des messages IoT Hub pour aller dans le hub IoT. Les messages que vous voyez dans la fenêtre **SORTIE** de Visual Studio Code contiennent une section **body** et une section **applicationProperties**. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module périphérique Video Analyzer définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline en direct est activé, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
  {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ava-sample-deployment/providers/Microsoft.Media/videoAnalyzers/avasample",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T00:58:58.602Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-message-source": "Telemetry",
    "messageId": "459c3255-7c86-4ff5-a1e5-7ce3fcb07627",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
```

Dans la sortie précédente :

- Le message est un événement de diagnostic, **MediaSessionEstablished**. Il indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
- La section sdp contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="motiondetection-event"></a>Événement MotionDetection

Quand un mouvement est détecté, le module Video Analyzer envoie un événement d’inférence. Le \* **\*type** est défini sur **motion** pour indiquer qu’il s’agit d’un résultat du processeur de détection de mouvement.

Voici un exemple de ce message :

```json
{
  "body": {
    "timestamp": 145818422564951,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.322176,
            "t": 0.574627,
            "w": 0.525,
            "h": 0.088889
          }
        }
      }
    ]
  },
  "properties": { … },
  "systemProperties": { … }
}
```

Dans cet exemple :

- La valeur de **body** correspond à des données sur l’événement d’analytique. En l’occurrence, l’événement est un événement d’inférence : le corps contient donc des données **timestamp** et **inferences**.
- Les données **inferences** indiquent que le **type** est **motion**. Elles contiennent des données supplémentaires sur cet événement **motion**.
- La section **box** contient les coordonnées d’un cadre englobant autour de l’objet en mouvement. Les valeurs sont normalisées par la largeur et la hauteur de la vidéo, en pixels. Par exemple, pour obtenir les coordonnées de pixel d’origine, vous devez multiplier les dimensions horizontales par 1 920 et les dimensions verticales par 1 080.

  ```
  l - distance from left of image
  t - distance from top of image
  w - width of bounding box
  h - height of bounding box
  ```
