---
title: Analyser une vidéo en direct avec votre propre modèle - HTTP
description: Ce guide de démarrage rapide explique comment analyser une vidéo en direct en utilisant votre propre modèle (HTTP) avec Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 11/04/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: c7770c61ce6d768f3fdd3216da94c87466310e74
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484232"
---
# <a name="quickstart-analyze-live-video-with-your-own-model---http"></a>Démarrage rapide : Analyser une vidéo en direct avec votre propre modèle (HTTP)

[!INCLUDE [header](includes/edge-env.md)]

Ce guide de démarrage rapide vous montre comment utiliser Azure Video Analyzer pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée). Vous allez voir comment appliquer un modèle de vision par ordinateur pour détecter des objets. Un sous-ensemble d’images du flux vidéo en direct est envoyé à un service d’inférence. Les résultats sont envoyés à IoT Edge Hub.

Ce guide de démarrage rapide utilise une machine virtuelle Azure comme appareil IoT Edge ainsi qu’un flux vidéo en direct simulé. Il s’appuie sur le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md).

## <a name="prerequisites"></a>Prérequis

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Quand vous configurez les ressources Azure, une courte vidéo du trafic d’une autoroute est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce guide de démarrage rapide utilise le fichier vidéo pour simuler un flux en direct.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [l’exemple de vidéo d’intersection d’autoroute](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) pour démarrer la lecture. Vous voyez la séquence vidéo de nombreux véhicules se déplaçant dans le trafic d’une autoroute.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

## <a name="create-and-deploy-the-livepipeline"></a>Créer et déployer le livePipeline

### <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-http/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge

1. Cliquez avec le bouton droit sur le fichier _src/edge/deployment.yolov3.template.json_, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Capture d’écran de l’option Générer un manifeste de déploiement IoT Edge":::

1. Le fichier de manifeste _deployment.yolov3.amd64.json_ est créé dans le dossier _src/edge/config_.
1. Cliquez avec le bouton droit sur _src/edge/config/deployment.yolov3.amd64.json_, puis sélectionnez **Créer un déploiement pour un seul appareil**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **ava-sample-iot-edge-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

   - Le module Video Analyzer, nommé **avaedge**.
   - Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct.
   - Le module **avaextension**, modèle de détection d’objets YoloV3, qui applique la vision par ordinateur aux images et retourne plusieurs classes de types d’objets

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

1. ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. Le code operations.json commence par appeler les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.

   ```
   --------------------------------------------------------------------------
   Executing operation pipelineTopologyList
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.1"
   }
   ---------------  Response: pipelineTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput

   Press Enter to continue
   ```

1. La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

   - Un appel à `pipelineTopologySet` qui utilise l’élément pipelineTopologyUrl précédent.
   - Un appel à `livePipelineSet` qui utilise le corps suivant :

     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample pipeline description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```

   - Un appel à livePipelineActivate qui démarre le pipeline en direct et le flux vidéo.
   - Un deuxième appel à `livePipelineList` qui indique que le pipeline en direct est dans l’état En cours d’exécution.

1. La sortie de la fenêtre **TERMINAL** s’interrompt avec l’invite **Appuyez sur Entrée pour continuer**. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Vous voyez des messages indiquant que le module Video Analyzer envoie des données au hub IoT. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le pipeline continue à s’exécuter et affiche des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline, retournez à la fenêtre **TERMINAL**, puis sélectionnez Entrée.

   La série d’appels suivante nettoie les ressources :

   - Un appel à `livePipelineDeactivate` désactive le pipeline en direct.
   - Un appel à `livePipelineDelete` supprime le pipeline en direct.
   - Un appel à `pipelineTopologyDelete` supprime la topologie.
   - Un dernier appel à `pipelineTopologyList` indique que la liste est vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, les résultats du nœud du processeur d’extension HTTP passe par le nœud récepteur des messages IoT Hub pour aller dans le hub IoT. Les messages que vous voyez dans la fenêtre **SORTIE** contiennent une section « body » et une section « applicationProperties ». Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit les propriétés de l’application et le contenu du corps.

**Événement MediaSessionEstablished**

Quand un pipeline est instancié, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché. Le type d’événement est **Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasampleiot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

Dans ce message, notez les informations suivantes :

- Le message est un événement de diagnostic. L’événement MediaSessionEstablished indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
- Dans applicationProperties, « subject » indique que le message a été généré à partir du nœud source RTSP dans le pipeline.
- Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement de diagnostic.
- « eventTime » indique l’heure à laquelle l’événement s’est produit.
- Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud processeur d’extension HTTP reçoit les résultats d’inférence à partir du module yolov3. Il émet ensuite les résultats via le nœud récepteur de messages IoT Hub sous la forme d’événements d’inférence.

Dans ces événements, le type est défini sur « entity » pour indiquer qu’il s’agit d’une entité, comme une voiture ou un camion. La valeur eventTime est l’heure UTC à laquelle l’objet a été détecté.

Dans l’exemple suivant, deux voitures ont été détectées dans la même image vidéo, avec des niveaux de confiance différents.

```
[IoTHubMonitor] [1:48:04 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145589011404622,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.97052866
        },
        "box": {
          "l": 0.40896654,
          "t": 0.60390747,
          "w": 0.045092657,
          "h": 0.029998193
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.9547283
        },
        "box": {
          "l": 0.20050547,
          "t": 0.6094412,
          "w": 0.043425046,
          "h": 0.037724357
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.94567955
        },
        "box": {
          "l": 0.55363107,
          "t": 0.5320657,
          "w": 0.037418623,
          "h": 0.027014252
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8916893
        },
        "box": {
          "l": 0.6642384,
          "t": 0.581689,
          "w": 0.034349587,
          "h": 0.027812533
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8547814
        },
        "box": {
          "l": 0.584758,
          "t": 0.60079926,
          "w": 0.07082855,
          "h": 0.034121
        }
      }
    }
  ]
}
```

Dans le message, notez les informations suivantes :

- La section « body » contient des données relatives à l’événement d’analytique. Dans ce cas, l’événement est un événement d’inférence : le corps contient donc des données sur les inférences.
- La section « inferences » indique que le type est « entity ». Cette section inclut des données supplémentaires sur l’entité.
- La valeur timestamp indique l’heure à laquelle l’événement a été reçu.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

- Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
- Utilisez un appareil Linux AMD64 ou x64 plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2018-06&preserve-view=true). Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../../iot-edge/quickstart-linux.md?view=iotedge-2018-06&preserve-view=true).
