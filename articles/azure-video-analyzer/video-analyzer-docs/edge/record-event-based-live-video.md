---
title: 'Tutoriel : Enregistrement vidéo basé sur les événements et lecture vidéo dans le cloud - Azure'
description: Dans ce tutoriel, vous allez voir comment utiliser Azure Video Analyzer pour effectuer un enregistrement vidéo basé sur les événements dans le cloud et pour le lire dans le cloud.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1edec79cd7baa20819e0599b039fbdfe20f35d92
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484817"
---
# <a name="tutorial-event-based-video-recording-and-playback"></a>Tutoriel : Enregistrement vidéo basé sur les événements et lecture des enregistrements

[!INCLUDE [header](includes/edge-env.md)]

Dans ce tutoriel, vous allez voir comment utiliser Azure Video Analyzer pour enregistrer certaines parties d’une source vidéo en direct dans Video Analyzer, dans le cloud. Ici, nous désignerons ce cas d’usage par [enregistrement vidéo basé sur les événements](../event-based-video-recording-concept.md) (EVR, Event-based Video Recording). Pour enregistrer certaines parties d’une vidéo live, vous allez utiliser un modèle d’intelligence artificielle de détection d’objets afin de rechercher des objets dans la vidéo et d’enregistrer des clips vidéo uniquement lorsqu’un certain type d’objet est détecté. Vous allez également voir comment lire les clips vidéo enregistrés à l’aide de Video Analyzer. Cette possibilité s’avère utile pour un grand nombre de scénarios dans lesquels il est nécessaire de conserver une archive des clips vidéo d’intérêt. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Lisez ces articles avant de commencer :

* [Vue d’ensemble d’Azure Video Analyzer](../overview.md)
* [Terminologie d’Azure Video Analyzer](../terminology.md)
* [Concepts relatifs aux pipelines Azure Video Analyzer](../pipeline.md) 
* [Enregistrement de vidéo basé sur les événements](../event-based-video-recording-concept.md)
* [Tutoriel : Développement d’un module IoT Edge](../../../iot-edge/tutorial-develop-for-linux.md)
* [Modification de deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Section expliquant [comment déclarer des routes dans le manifeste de déploiement IoT Edge](../../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants :
* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) gratuitement.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Installation de Docker](https://docs.docker.com/desktop/#download-and-install) sur votre ordinateur.
* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Kit SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Effectuer le déploiement dans Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble

Un enregistrement vidéo basé sur les événements est un enregistrement vidéo qui a été déclenché par un événement. Cet événement peut être généré à partir des éléments suivants :
- Traitement du signal vidéo lui-même, par exemple, lors de la détection d’un objet qui se déplace dans la vidéo.
- Source indépendante, par exemple, ouverture d’une porte. 

Vous pouvez également déclencher l’enregistrement uniquement lorsqu’un service d’inférence détecte qu’un événement particulier s’est produit. Dans ce tutoriel, vous allez utiliser une vidéo de véhicules qui roulent sur une autoroute et enregistrer des clips vidéo chaque fois qu’un camion est détecté.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/overview.svg" alt-text="Pipeline":::

Le diagramme est une représentation graphique d’un [pipeline](../pipeline.md) et des modules supplémentaires qui accomplissent le scénario souhaité. Quatre modules IoT Edge sont impliqués :

* Video Analyzer sur un module IoT Edge.
* Un module de périphérie exécutant un modèle d’intelligence artificielle derrière un point de terminaison HTTP. Ce module d’intelligence artificielle utilise le modèle [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), qui peut détecter de nombreux types d’objets.
* Un module personnalisé pour compter et filtrer les objets, appelé compteur d’objets dans le diagramme. Vous allez créer un compteur d’objets, puis le déployer au cours de ce tutoriel.
* Un [module de simulateur RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) pour simuler une caméra RTSP.
    
Comme le montre le diagramme, vous allez utiliser un nœud de [source RTSP](../pipeline.md#rtsp-source) dans le pipeline afin de capturer la simulation de vidéo en direct du trafic sur l’autoroute, puis envoyer cette vidéo vers deux chemins :

* Le premier chemin dirige vers un nœud d’extension HTTP. Le nœud échantillonne les trames vidéo en une valeur que vous définissez à l’aide du champ `samplingOptions`, puis relaie les trames, en tant qu’images, vers le module d’IA YOLOv3, qui est un détecteur d’objets. Le nœud reçoit les résultats, qui correspondent aux objets (véhicules dans le trafic) détectés par le modèle. Le nœud d’extension HTTP publie ensuite les résultats par le biais du nœud récepteur de messages IoT Hub dans le hub IoT Edge.

* Le module objectCounter est configuré pour recevoir des messages du hub IoT Edge, notamment les résultats de la détection des objets (véhicules dans le trafic). Le module vérifie ces messages et recherche des objets d’un certain type, qui ont été configurés par le biais d’un paramètre. Quand un tel objet est trouvé, ce module envoie un message au hub IoT Edge. Les messages « object found » (objet trouvé) sont ensuite routés vers le nœud source IoT Hub du pipeline. Lors de la réception d’un tel message, le nœud source IoT Hub dans le pipeline déclenche le nœud [processeur de porte de signal](../pipeline.md#signal-gate-processor). Le nœud processeur de porte de signal est alors ouvert pour une durée configurée. Pendant cette période, la vidéo passe par la porte pour aller au nœud récepteur vidéo. Cette partie du stream en direct est ensuite enregistrée via le nœud [récepteur vidéo](../pipeline.md#video-sink) dans une [vidéo](../terminology.md#video) de votre compte Video Analyzer. La vidéo utilisée dans ce tutoriel est un [exemple de vidéo d’intersection d’autoroute](https://avamedia.blob.core.windows.net/public/camera-300s.mkv).

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

Ouvrez src/edge/deployment.objectCounter.template.json. Il existe quatre entrées sous la section **modules** qui correspondent aux éléments listés dans la section de vue d’ensemble précédente.

* **avaedge** : Il s’agit du module Video Analyzer.
* **yolov3** : Il s’agit du module d’intelligence artificielle créé à l’aide du modèle YOLO v3.
* **rtspsim** : Il s’agit du simulateur RTSP.
* **objectCounter** : Il s’agit du module qui recherche des objets spécifiques dans les résultats de yolov3.

Pour le module objectCounter, consultez la chaîne (${MODULES.objectCounter}) utilisée pour la valeur « image ». Celle-ci se base sur le [tutoriel](../../../iot-edge/tutorial-develop-for-linux.md) sur le développement d’un module IoT Edge. Visual Studio Code reconnaît automatiquement que le code du module objectCounter se trouve sous src/edge/modules/objectCounter. 

Lisez [cette section](../../../iot-edge/module-composition.md#declare-routes) sur la manière de déclarer des routes dans le manifeste de déploiement IoT Edge. Examinez ensuite les routes dans le fichier JSON du modèle. Remarque :

> [!NOTE]
> Vérifiez les propriétés souhaitées pour le module objectCounter, qui sont configurées pour rechercher les objets portant l’étiquette « truck » (camion), avec un niveau de confiance d’au moins 50 %.

Ensuite, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* **c2d-console-app.csproj** : Fichier projet pour Visual Studio Code.
* **operations.json** : Ce fichier liste les différentes opérations que vous allez exécuter.
* **Program.cs** : Exemple de code de programme :
    * Il charge les paramètres de l’application.

    * Appelle des méthodes directes exposées par le module Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en appelant ses [méthodes directes](direct-methods.md).

    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et les événements générés par le module dans la fenêtre **SORTIE**.
    * Il appelle des méthodes directes pour nettoyer des ressources.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge 

Le manifeste de déploiement définit les modules qui sont déployés sur un périphérique et les paramètres de configuration de ces modules. Effectuez les étapes suivantes pour générer un manifeste à partir du fichier de modèle, puis déployez-le sur le périphérique.

À l’aide de Visual Studio Code, suivez [ces instructions](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) pour vous connecter à Docker. Sélectionnez ensuite **Créer et envoyer (push) la solution IoT Edge**. Utilisez src/edge/deployment.objectCounter.template.json pour cette étape.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/build-push.png" alt-text="Créer et envoyer (push) la solution IoT Edge":::

Cette action permet de créer le module objectCounter pour le comptage des objets et d’envoyer (push) l’image à Azure Container Registry.

* Vérifiez que les variables d’environnement CONTAINER_REGISTRY_USERNAME_myacr et CONTAINER_REGISTRY_PASSWORD_myacr sont définies dans le fichier .env.

Cette étape permet de créer le manifeste de déploiement IoT Edge dans src/edge/config/deployment.objectCounter.amd64.json. Cliquez avec le bouton droit sur ce fichier, puis sélectionnez **Créer un déploiement pour un seul appareil**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/create-deployment-single-device.png" alt-text="Créer un déploiement pour un seul appareil":::

S’il s’agit de votre premier tutoriel avec Video Analyzer, Visual Studio Code vous invite à entrer la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier appsettings.json.

[!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

Visual Studio Code vous demande ensuite de sélectionner un appareil IoT Hub. Sélectionnez votre appareil IoT Edge, à savoir avasample-iot-edge-device.

À ce stade, le déploiement des modules de périphérie sur votre appareil IoT Edge a démarré.
Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche de Visual Studio Code. Vous devez y voir six modules déployés nommés $edgeAgent, $edgeHub avaedge, rtspsim, yolov3 et objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/modules.png" alt-text="Quatre modules déployés":::

## <a name="run-the-program"></a>Exécuter le programme

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Paramètres d’extension":::
1. Recherchez et activez « Afficher le message détaillé ».

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Afficher le message détaillé":::
1. Accédez à src/cloud-to-device-console-app/operations.json.
1. Sous le nœud **pipelineTopologySet**, modifiez les points suivants :


    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json" `
    
1. Ensuite, sous les nœuds **livePipelineSet** et **pipelineTopologyDelete**, vérifiez que la valeur de **topologyName** correspond à celle de la propriété **name** dans la topologie de pipeline précédente :

    `"pipelineTopologyName" : "EVRtoVideoSinkOnObjDetect"`
1. Ouvrez la [topologie de pipeline](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) dans un navigateur, puis examinez la valeur de videoName ; vous verrez qu’elle est codée en dur dans `sample-evr-video`. C’est acceptable dans un tutoriel. En production, vous devrez faire en sorte que chaque caméra RTSP soit enregistrée dans une ressource vidéo sous un nom unique.
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
   * Un appel à livePipelineSet à l’aide du corps suivant
     
        ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "EVRtoVideoSinkOnObjDetect",
            "description": "Sample topology description",
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
    
   * Un appel à livePipelineActivate pour démarrer le pipeline en direct et le flux vidéo
   * Un deuxième appel à livePipelineList qui indique que le pipeline en direct est en cours d’exécution 
     
1. La sortie de la fenêtre **TERMINAL** s’interrompt maintenant à l’invite **Appuyez sur Entrée pour continuer**. Ne sélectionnez pas **Entrée** pour l’instant. Faites défiler vers le haut pour voir les charges utiles de réponse JSON des méthodes directes que vous avez appelées.
1. Si vous basculez maintenant vers la fenêtre **OUTPUT** de Visual Studio Code, vous allez voir les messages qui sont envoyés à IoT Hub par le module Video Analyzer.

   Ces messages sont abordés dans la section qui suit.
1. Le pipeline en direct continue à s’exécuter et à enregistrer la vidéo. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter l’enregistrement, revenez à la fenêtre **TERMINAL** et sélectionnez **Entrée**. La série d’appels suivante permet de nettoyer les ressources en utilisant :

   * Un appel à livePipelineDeactivate pour désactiver le pipeline en direct.
   * Un appel à livePipelineDelete pour supprimer le pipeline en direct.
   * Un appel à pipelineTopologyDelete pour supprimer la topologie.
   * Un dernier appel à pipelineTopologyList pour indiquer que la liste est maintenant vide.

## <a name="interpret-the-results"></a>Interpréter les résultats 

Quand vous exécutez le pipeline en direct, le module Video Analyzer envoie certains événements opérationnels et de diagnostic au hub IoT Edge. Ces événements sont les messages que vous voyez dans la fenêtre **SORTIE** de Visual Studio Code. Ils contiennent une section body et une section applicationProperties. Pour comprendre ce que ces sections représentent, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, les propriétés de l’application et le contenu du corps sont définis par le module Video Analyzer.

## <a name="diagnostics-events"></a>Événements de diagnostic

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished 

Lorsque le pipeline est activé, le nœud source RTSP tente de se connecter au serveur RTSP exécuté dans le conteneur du simulateur RTSP. En cas de réussite, il imprime cet événement :

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```


* Le message est un événement de diagnostic (MediaSessionEstablished). Il indique que le nœud source RTSP (l’objet) a établi une connexion au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* La section subject (l’objet) dans applicationProperties référence le nœud dans la topologie de pipeline à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* La section eventType dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* La section eventTime indique l’heure à laquelle l’événement s’est produit.
* La section body contient des données relatives à l’événement de diagnostic, qui, dans ce cas, correspondent aux détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Événements opérationnels

Une fois que le pipeline se sera exécuté pendant un certain temps, vous recevrez un événement du module objectCounter. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [avasample-iot-edge-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

La section applicationProperties contient l’heure de l’événement. Il s’agit de l’heure à laquelle le module objectCounter a constaté que les résultats du module yolov3 contenaient des objets d’intérêt (camions).

Vous pouvez constater que de plus en plus de ces événements apparaissent à mesure que d’autres camions sont détectés dans la vidéo.

### <a name="recordingstarted-event"></a>Événement RecordingStarted

Presque immédiatement après l’envoi de l’événement par le compteur d’objets, vous verrez un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** :

```
[IoTHubMonitor] [5:53:46 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message. La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource vidéo Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingavailable-event"></a>Événement RecordingAvailable

Comme son nom l’indique, l’événement RecordingStarted est envoyé lorsque l’enregistrement démarre. Il est cependant possible que les données du média ne soient pas encore chargées dans la ressource vidéo. Lorsque le nœud récepteur vidéo a chargé une vidéo, il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** :

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Cet événement indique que suffisamment de données ont été écrites dans la ressource vidéo pour que les lecteurs ou les clients commencent la lecture de la vidéo. La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message. La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingstopped-event"></a>Événement RecordingStopped

Quand vous désactivez le pipeline en direct, le nœud récepteur vidéo cesse d’enregistrer le média. Il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** :

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Cet événement indique que l’enregistrement s’est arrêté. La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message. La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

## <a name="playing-back-the-recording"></a>Lecture de l’enregistrement

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.
1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources que vous avez dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la liste **Video Analyzer**.
1. Vous trouverez une vidéo portant le nom de `sample-evr-video`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. La page de détails de la vidéo s’ouvre et la lecture démarre automatiquement.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [clean-up-resources](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge dans la [page de produits conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Suivez les instructions données dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md). Suivez ensuite les instructions données dans le démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](../../../iot-edge/quickstart-linux.md) pour inscrire l’appareil auprès d’Azure IoT Hub.
