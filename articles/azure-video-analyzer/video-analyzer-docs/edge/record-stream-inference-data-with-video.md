---
title: Enregistrer et streamer des métadonnées d’inférence avec une vidéo – Azure Video Analyzer
description: Dans ce tutoriel, vous allez apprendre à utiliser Azure Video Analyzer pour enregistrer des métadonnées de vidéo et d’inférence dans le cloud et lire l’enregistrement avec les métadonnées d’inférence visuelle.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 67331e8b39a7c059da31215dd971988009a0ee36
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491978"
---
# <a name="tutorial-record-and-stream-inference-metadata-with-video"></a>Tutoriel : Enregistrer et streamer des métadonnées d’inférence avec une vidéo

[!INCLUDE [header](includes/edge-env.md)]

Dans ce tutoriel, vous allez apprendre à utiliser Azure Video Analyzer pour enregistrer des métadonnées de vidéo en direct et d’inférence dans le cloud et lire cet enregistrement avec les métadonnées d’inférence visuelle. Dans ce cas d’usage, vous allez enregistrer la vidéo en continu tout en utilisant un modèle personnalisé pour la détection d’objets **(yoloV3)** et un processeur Video Analyzer **(traceur d’objets)** pour le suivi d’objets. La vidéo étant enregistrée en continu, les métadonnées d’inférence des objets sont détectées et suivies. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Lisez ces articles avant de commencer :

* [Vue d’ensemble d’Azure Video Analyzer sur IoT Edge](../overview.md)
* [Terminologie d’Azure Video Analyzer sur IoT Edge](../terminology.md)
* [Concepts relatifs aux pipelines Video Analyzer](../pipeline.md) 
* [Enregistrement vidéo en continu](../continuous-video-recording.md)
* [Démarrage rapide : Analyser une vidéo en direct avec votre propre modèle (HTTP)](analyze-live-video-use-your-model-http.md)
* [Démarrage rapide : Suivre des objets dans une vidéo en direct](track-objects-live-video.md)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants : [!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Effectuer le déploiement dans Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/record-stream-inference-data-with-video/overview.svg" alt-text="Pipeline":::

Le diagramme est une représentation graphique d’un [pipeline](../pipeline.md) et des modules supplémentaires qui effectuent le scénario souhaité. Trois modules IoT Edge sont utilisés :
* Module Video Analyzer.
* Un module de périphérie exécutant un modèle d’intelligence artificielle derrière un point de terminaison HTTP. Ce module d’intelligence artificielle utilise le modèle [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), qui peut détecter de nombreux types d’objets.
* Un [module de simulateur RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) pour simuler une caméra RTSP.

Comme le montre le diagramme, vous allez utiliser un nœud de [source RTSP](../pipeline.md#rtsp-source) dans le pipeline afin de capturer la simulation de vidéo en direct du trafic sur l’autoroute, puis envoyer cette vidéo vers deux chemins :

* Le premier chemin dirige vers un nœud d’extension HTTP. Le nœud d’extension HTTP joue le rôle d’un proxy. Il convertit chaque dixième image vidéo en type d’image spécifié. Ensuite, il relaie l’image sur HTTP vers un autre module périphérique qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, le module de périphérie est généré à l’aide du modèle YOLOv3, qui peut détecter de nombreux types d’objets. Le nœud processeur d’extension HTTP collecte les résultats de la détection et envoie ces résultats et toutes les images vidéo (pas seulement la dixième) au nœud traceur d’objets. Le nœud traceur d’objets utilise des techniques de flux optique pour suivre l’objet dans les 9 images sur lesquelles le modèle IA n’a pas été appliqué. Le nœud traceur publie ses résultats sur le nœud récepteur vidéo et le nœud récepteur IoT Hub. Le nœud [récepteur vidéo](../pipeline.md#video-sink) utilise les métadonnées d’inférence du nœud traceur d’objets pour être lues avec la vidéo enregistrée. Le nœud [récepteur de messages IoT Hub](../pipeline.md#iot-hub-message-sink) envoie ensuite ces événements au [hub IoT Edge](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

* Le deuxième chemin va directement de la source RTSP vers le nœud récepteur vidéo pour effectuer l’enregistrement vidéo continu. La vidéo utilisée dans ce tutoriel est un [exemple de vidéo d’intersection d’autoroute](https://avamedia.blob.core.windows.net/public/camera-300s.mkv).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Ce didacticiel présente les procédures suivantes :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

Dans Visual Studio Code, accédez à src/edge. Vous allez y voir le fichier .env que vous avez créé et quelques fichiers de modèle de déploiement. Ce modèle définit les modules de périphérie que vous allez déployer sur le périphérique (la machine virtuelle Linux Azure). Le fichier .env contient des valeurs pour les variables utilisées dans ces modèles, telles que les informations d’identification Video Analyzer.

Ouvrez src/edge/deployment.yolov3.template.json. Il existe trois entrées sous la section **modules** qui correspondent aux éléments listés dans la section « Vue d’ensemble » précédente

* **avaedge** : Il s’agit du module Video Analyzer sur IoT Edge.
* **yolov3** : Il s’agit du module d’intelligence artificielle créé à l’aide du modèle YOLO v3.
* **rtspsim** : Il s’agit du simulateur RTSP.


Ensuite, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* **c2d-console-app.csproj** : Fichier projet pour Visual Studio Code.
* **operations.json** : Ce fichier liste les différentes opérations que vous allez exécuter.
* **Program.cs** : Exemple de code de programme :
    * Il charge les paramètres de l’application.

    * Il appelle les méthodes directes exposées par le module Video Analyzer sur IoT Edge. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en appelant ses [méthodes directes](direct-methods.md).

    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et les événements générés par le module dans la fenêtre **SORTIE**.
    * Il appelle des méthodes directes pour nettoyer des ressources.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge 

1. Cliquez avec le bouton droit sur le fichier _src/edge/deployment.yolov3.template.json_, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Capture d’écran de l’option Générer un manifeste de déploiement IoT Edge":::

    * Le fichier de manifeste _deployment.yolov3.amd64.json_ est créé dans le dossier _src/edge/config_.
1. Cliquez avec le bouton droit sur _src/edge/config/deployment.yolov3.amd64.json_, puis sélectionnez **Créer un déploiement pour un seul appareil**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **avasample-iot-edge-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

   - Le module **avaedge**, qui est le module Video Analyzer.
   - Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct.
   - Le module **yolov3**, qui est le modèle de détection d’objets YoloV3 qui applique la vision par ordinateur aux images et retourne plusieurs classes de types d’objets

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::
     
## <a name="create-and-deploy-the-live-pipeline"></a>Créer et déployer le pipeline en direct

### <a name="edit-the-sample-files"></a>Modifier les exemples de fichiers

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Paramètres d’extension":::
1. Recherchez et activez « Afficher le message détaillé ».

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Afficher le message détaillé":::
1. Accédez à src/cloud-to-device-console-app/operations.json.
1. Sous le nœud **pipelineTopologySet**, modifiez ce qui suit
 
    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json" `
    
1. Ensuite, sous les nœuds **livePipelineSet** et **pipelineTopologyDelete**, vérifiez que la valeur de **topologyName** correspond à celle de la propriété **name** dans la topologie de pipeline précédente :

    `"pipelineTopologyName" : "CVRHttpExtensionObjectTracking"`
1. Ouvrez la [topologie de pipeline](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json) dans un navigateur, puis examinez la valeur de videoName ; vous verrez qu’elle est codée en dur dans `sample-cvr-with-inference-metadata`. C’est acceptable dans un tutoriel. En production, vous devrez faire en sorte que chaque caméra RTSP soit enregistrée dans une ressource vidéo sous un nom unique.  

1. Examinez les paramètres du nœud d’extension HTTP.

  ```=
     "samplingOptions":{
         "skipSamplesWithoutAnnotation":"false",
         "maximumSamplesPerSecond":"2"
    }
  ```

Ici, `skipSamplesWithoutAnnotation` est défini sur `false`, car le nœud d’extension doit parcourir toutes les images, qu’elles aient ou non des résultats d’inférence, jusqu’au nœud traceur d’objets en aval. Le traceur d’objets peut assurer le suivi d’objets sur environ 15 images. Votre modèle AI a une valeur maximale d’FPS pour le traitement, qui est la valeur la plus élevée à laquelle `maximumSamplesPerSecond` doit être définie.


## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

1. Démarrez une session de débogage en appuyant sur F5. Quelques messages s’affichent dans la fenêtre **TERMINAL**.
1. Le fichier operations.json commence par appeler pipelineTopologyList et livePipelineList. Si vous avez nettoyé des ressources à l’issue de démarrages rapides ou de tutoriels, cette action renvoie des listes vides et marque des pauses pour vous permettre de sélectionner **Entrée**, comme illustré ci-dessous :
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
1. Une fois que vous avez sélectionné **Entrée** dans la fenêtre **TERMINAL**, l’ensemble suivant d’appels de méthode directe est effectué :
   * Un appel à pipelineTopologySet en utilisant l’élément pipelinetopologyUrl précédent
   * Un appel à livePipelineSet en utilisant le corps suivant
     
        ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CVRHttpExtensionObjectTracking",
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
   * Un appel à livePipelineActivate qui démarre le pipeline en direct et le flux vidéo.
   * Un deuxième appel à livePipelineList qui indique que le pipeline en direct est à l’état En cours d’exécution.
1. La sortie de la fenêtre TERMINAL s’interrompt avec l’invite Appuyez sur Entrée pour continuer. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre SORTIE de Visual Studio Code. Vous voyez des messages indiquant que le module Video Analyzer sur IoT Edge envoie des données au hub IoT.
1. Le pipeline continue de s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline en direct, retournez à la fenêtre **TERMINAL** et sélectionnez Entrée.
1. La série d’appels suivante nettoie les ressources :

    * Un appel à livePipelineDeactivate désactive le pipeline en direct.
    * Un appel à livePipelineDelete supprime le pipeline en direct.
    * Un appel à pipelineTopologyDelete supprime la topologie de pipeline.
    * Un dernier appel à pipelineTopologyList indique que la liste est vide.       

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, les résultats du nœud processeur d’extension HTTP sont transférés au hub IoT via le nœud traceur d’objets et le nœud récepteur IoT Hub. Les messages que vous voyez dans la fenêtre **SORTIE** contiennent une section « body » et une section « applicationProperties ». Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit les propriétés de l’application et le contenu du corps.

## <a name="diagnostics-events"></a>Événements de diagnostic
### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline en direct est activé, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché. Le type d’événement est Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Dans ce message, notez les informations suivantes :

* Le message est un événement de diagnostic. L’événement MediaSessionEstablished indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans applicationProperties, subject indique que le message a été généré à partir du nœud source RTSP dans le pipeline en direct.
* Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Événements opérationnels

### <a name="object-tracking-events"></a>Événements de suivi d’objets

Le nœud processeur d’extension HTTP envoie les images 0, 15, 30 et ainsi de suite au module yolov3 et reçoit les résultats de l’inférence. Il envoie ensuite ces résultats et toutes les images vidéo au nœud traceur d’objets. Supposons qu’un objet a été détecté sur l’image 0 : le traceur d’objets affecte un sequenceId unique à cet objet. Ensuite, dans les images 1, 2,..., 14, s’il peut suivre cet objet, il génère un résultat avec le même sequenceId. Dans les extraits de code suivants des résultats, vous pouvez constater que `sequenceId` est répété, mais que l’emplacement du cadre englobant change, car l’objet se déplace.

Depuis l’image M :

```json
  {
    "type": "entity",
    "subtype": "objectDetection",
    "inferenceId": "4d325fc4dc7a43b2a781bf7d6bdb3ff0",
    "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
    "entity": {
      "tag": {
        "value": "car",
        "confidence": 0.95237225
      },
      "box": {
        "l": 0.0025893003,
        "t": 0.550063,
        "w": 0.1086607,
        "h": 0.12116724
      }
    }
  },
```

Depuis l’image N :

```json
{
  "type": "entity",
  "subtype": "objectDetection",
  "inferenceId": "317aafdab7e940388be1e4c4cc58c366",
  "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
  "entity": {
    "tag": {
      "value": "car",
      "confidence": 0.95237225
    },
    "box": {
      "l": 0.0027777778,
      "t": 0.54901963,
      "w": 0.108333334,
      "h": 0.12009804
    }
  }
},
```

### <a name="recordingstarted-event"></a>Événement RecordingStarted
Quand le nœud récepteur vidéo commence à enregistrer le média, il émet cet événement de type **Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted** :

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

La section subject dans applicationProperties fait référence au nœud récepteur vidéo dans le pipeline en direct qui a généré ce message.

La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingavailable-event"></a>Événement RecordingAvailable

Comme son nom l’indique, l’événement RecordingStarted est envoyé lorsque l’enregistrement démarre. Il est cependant possible que les données du média ne soient pas encore chargées dans la ressource vidéo. Lorsque le nœud récepteur vidéo a chargé une vidéo, il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** :

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Cet événement indique que suffisamment de données ont été écrites dans la ressource vidéo pour que les lecteurs ou les clients commencent la lecture de la vidéo.

La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message.

La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingstopped-event"></a>Événement RecordingStopped

Quand vous désactivez le pipeline en direct, le nœud récepteur vidéo cesse d’enregistrer le média. Il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** :

```
[IoTHubMonitor] [11:33:31 PM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Cet événement indique que l’enregistrement s’est arrêté.

La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message.

La section body contient des informations sur l’emplacement de sortie, qui est dans ce cas le nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

## <a name="streaming-the-recording-with-visual-inference-metadata"></a>Streaming de l’enregistrement avec les métadonnées d’inférence visuelle

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources qui figurent dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la liste **Video Analyzer**.
1. Vous trouverez une vidéo portant le nom de `sample-cvr-with-inference-metadata`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. Dans la page d’informations de la vidéo, cliquez sur l’icône de **lecture**
1. Pour afficher les métadonnées d’inférence sous forme de cadres englobants sur la vidéo, cliquez sur l’icône de **cadre englobant** (dans le cercle rouge)
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/video-playback.png" alt-text="Capture d’écran de la lecture de vidéo":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge dans la [page de produits conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Suivez les instructions données dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md). Suivez ensuite les instructions données dans le démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](../../../iot-edge/quickstart-linux.md) pour inscrire l’appareil auprès d’Azure IoT Hub.
 
