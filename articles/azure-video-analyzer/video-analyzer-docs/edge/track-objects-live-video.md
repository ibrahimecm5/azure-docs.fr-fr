---
title: Suivre des objets dans une vidéo en direct avec Azure Video Analyzer
description: Ce guide de démarrage rapide vous montre comment utiliser le module périphérique Azure Video Analyzer pour suivre des objets dans un flux vidéo en direct provenant d’une caméra IP (simulée). Vous allez voir comment appliquer un modèle de vision par ordinateur pour détecter des objets dans un sous-ensemble d’images dans le flux vidéo en direct. Vous pouvez ensuite utiliser un nœud de suivi d’objets pour suivre ces objets dans les autres images.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b9286dc6dba80de841969af285ab57e587f560a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319260"
---
# <a name="quickstart-track-objects-in-a-live-video"></a>Démarrage rapide : Suivre des objets dans une vidéo en direct

[!INCLUDE [header](includes/edge-env.md)]

Ce guide de démarrage rapide vous montre comment utiliser le module périphérique Azure Video Analyzer pour suivre des objets dans un flux vidéo en direct provenant d’une caméra IP (simulée). Vous allez voir comment appliquer un modèle de vision par ordinateur pour détecter des objets dans un sous-ensemble d’images dans le flux vidéo en direct. Vous pouvez ensuite utiliser un nœud de suivi d’objets pour suivre ces objets dans les autres images.

Ce suivi d’objets s’avère utile quand vous devez détecter des objets dans chaque image, mais que l’appareil périphérique n’a pas la puissance de calcul nécessaire pour pouvoir appliquer le modèle de vision sur chaque image. Si le flux vidéo en direct a par exemple 30 images par seconde et que vous ne pouvez exécuter votre modèle de vision par ordinateur que toutes les 15 images, le suivi d’objets peut prendre les résultats d’une de ces images, puis utiliser les techniques de [flux optique](https://en.wikipedia.org/wiki/Optical_flow) afin de générer des résultats pour la 2e, la 3e…, la 14e image, jusqu’à ce que le modèle soit réappliqué à l’image suivante.

Ce guide de démarrage rapide utilise une machine virtuelle Azure comme appareil IoT Edge, ainsi qu’un flux vidéo en direct simulé.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/object-tracker-topology.svg" alt-text="Suivre des objets dans une vidéo en direct":::

Ce diagramme montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension HTTP](../pipeline.md#http-extension-processor).

Le nœud d’extension HTTP joue le rôle d’un proxy. Il convertit chaque 15e image vidéo dans le type d’image spécifié. Ensuite, il relaie l’image sur HTTP vers un autre module périphérique qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, le module périphérique utilise le modèle [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), qui peut détecter de nombreux types d’objets. Le nœud du processeur d’extension HTTP collecte les résultats de la détection et envoie ces résultats ainsi que toutes les images vidéo (pas seulement la 15e image) au nœud de [suivi d’objets](../pipeline.md#object-tracker-processor). Le nœud de suivi d’objets utilise des techniques de flux optique pour suivre l’objet dans les 14 images sur lesquelles le modèle IA n’a pas été appliqué. Le nœud de suivi publie ses résultats sur le nœud récepteur de messages IoT Hub. Ce nœud [récepteur de messages IoT Hub](../pipeline.md#iot-hub-message-sink) envoie ensuite ces événements au [hub IoT Edge](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

> [!NOTE]
> Il est recommandé de passer en revue la discussion sur le compromis entre la justesse et la puissance de traitement avec le nœud de [suivi d’objets](../pipeline.md#object-tracker-processor).

Dans ce guide de démarrage rapide, vous allez :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>Déployer les modules nécessaires

1. Dans Visual Studio Code, cliquez avec le bouton droit sur le fichier *src/edge/deployment.yolov3.template.json*, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Générer un manifeste de déploiement IoT Edge":::
1. Le fichier de manifeste *deployment.yolov3.amd64.json* est créé dans le dossier *src/edge/config*.
1. Cliquez avec le bouton droit sur *src/edge/config/deployment.yolov3.amd64.json*, puis sélectionnez **Créer un déploiement pour un seul appareil**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::
1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **avasample-iot-edge-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

    * Le module périphérique Video Analyzer, nommé **avaedge**.
    * Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct. 
    * Le module **yolov3**, qui utilise le modèle YOLOV3 pour détecter divers objets

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/track-objects-live-video/object-tracker-modules.png" alt-text= "List of deployed IoT Edge modules":::


## <a name="create-and-deploy-the-live-pipeline"></a>Créer et déployer le pipeline en direct

### <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* **c2d-console-app.csproj** : Fichier projet pour Visual Studio Code.
* **operations.json** : Ce fichier liste les différentes opérations que vous allez exécuter.
* **Program.cs** : Exemple de code de programme :
    * Il charge les paramètres de l’application.
    * Appelle des méthodes directes exposées par le module Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en appelant ses [méthodes directes](direct-methods.md).
    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et les événements générés par le module dans la fenêtre **SORTIE**.
    * Invoque des méthodes directes pour nettoyer des ressources.

1. Modifiez le fichier operations.json :
    
    * Changez le lien de la topologie de pipeline :
    * « pipelineTopologyUrl » : « https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/object-tracking/topology.json  »
    * Sous livePipelineSet, modifiez le nom de la topologie de pipeline pour le faire correspondre à la valeur du lien précédent :
    * « topologyName » : « ObjectTrackingWithHttpExtension »
    * Sous pipelineTopologyDelete, modifiez le nom :
    * « name » : « ObjectTrackingWithHttpExtension »
    
Ouvrez l’URL de la topologie de pipeline dans un navigateur et examinez les paramètres du nœud d’extension HTTP.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

Ici, `skipSamplesWithoutAnnotation` est défini sur `false`, car le nœud d’extension doit parcourir toutes les images, qu’elles aient ou non des résultats d’inférence, jusqu’au nœud de suivi d’objets en aval. Le traceur d’objets peut assurer le suivi d’objets sur environ 15 images. Votre modèle AI a une valeur maximale d’FPS pour le traitement, qui est la valeur la plus élevée à laquelle `maximumSamplesPerSecond` doit être définie.
    
## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre **TERMINAL**.
1. Le code operations.json commence par appeler les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche Entrée.
    
    ```
    -------------------------------Executing operation pipelineTopologyList-----------------------  
    Request: pipelineTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.1"
    }
    ---------------  
    Response: pipelineTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
    
    La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :
    
    * Un appel à `pipelineTopologySet` qui utilise le contenu de `pipelineTopologyUrl`
    * Un appel à `livePipelineSet` qui utilise le corps suivant :
        
    ```json
    {
      "@apiVersion": "1.1",
      "name": "Sample-Pipeline-1",
      "properties": {
        "topologyName": "ObjectTrackingWithHttpExtension",
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
    * Un appel à `livePipelineActivate` qui active le pipeline en direct et le flux vidéo.
    * Un deuxième appel à `livePipelineList` qui indique que le pipeline en direct est dans l’état En cours d’exécution.
1. La sortie de la fenêtre TERMINAL s’interrompt avec l’invite Appuyez sur Entrée pour continuer. N’appuyez pas encore sur Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre SORTIE de Visual Studio Code. Vous voyez des messages indiquant que le module périphérique Video Analyzer effectue des envois au hub IoT. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le pipeline en direct continue à s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline en direct, retournez à la fenêtre **TERMINAL** et sélectionnez Entrée.
1. La série d’appels suivante nettoie les ressources :

    * Un appel à `livePipelineDeactivate` désactive le pipeline en direct.
    * Un appel à `livePipelineDelete` supprime le pipeline en direct.
    * Un appel à `pipelineTopologyDelete` supprime la topologie de pipeline.
    * Un dernier appel à `pipelineTopologyList` indique que la liste est vide.
    
## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, les résultats du nœud du processeur d’extension HTTP passe par le nœud récepteur des messages IoT Hub pour aller dans le hub IoT. Les messages qui s’affichent dans la fenêtre **SORTIE** contiennent une section `body` et une section `applicationProperties`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline en direct est activé, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché. Le type d’événement est **MediaSessionEstablished**.

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

* Le message est un événement de diagnostic. L’événement **MediaSessionEstablished** indique que le nœud source RTSP (le sujet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans applicationProperties, « subject » indique que le message a été généré à partir du nœud source RTSP dans le pipeline en direct.
* Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="object-tracking-events"></a>Événements de suivi d’objet

Le nœud processeur d’extension HTTP envoie les images 0, 15, 30 et ainsi de suite au module yolov3 et reçoit les résultats de l’inférence. Il envoie ensuite ces résultats et toutes les images vidéo au nœud de suivi d’objets. Supposons qu’un objet a été détecté sur l’image 0 : le suivi d’objets va affecter un `sequenceId` unique à cet objet. Ensuite, dans les images 1, 2,..., 14, s’il peut suivre cet objet, il va générer un résultat avec le même `sequenceId`. Dans les extraits de code suivants des résultats, notez comment `sequenceId` est répété, mais l’emplacement du cadre englobant a changé, car l’objet se déplace.

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

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Essayer de [détecter quand des objets franchissent une ligne virtuelle dans une vidéo en direct](use-line-crossing.md).
