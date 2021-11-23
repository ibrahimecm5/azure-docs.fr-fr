---
title: Détecter quand des objets franchissent une ligne virtuelle dans une vidéo en direct avec Azure Video Analyzer
description: Ce guide de démarrage rapide vous montre comment utiliser Azure Video Analyzer pour détecter quand des objets franchissent une ligne dans un flux vidéo en direct d’une caméra IP (simulée).
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 163dcc488de80a81b8ab9defdbdb7b84dfdbd302
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494593"
---
# <a name="tutorial-detect-when-objects-cross-a-virtual-line-in-a-live-video"></a>Tutoriel : Détecter quand des objets franchissent une ligne virtuelle dans une vidéo en direct

[!INCLUDE [header](includes/edge-env.md)]

Ce tutoriel vous montre comment utiliser Azure Video Analyzer pour détecter quand des objets franchissent une ligne virtuelle dans un flux vidéo en direct d’une caméra IP (simulée). Vous voyez comment appliquer un modèle de vision par ordinateur pour détecter des objets dans un sous-ensemble d’images du flux vidéo en direct. Vous pouvez ensuite utiliser un nœud de suivi d’objets pour suivre ces objets dans les autres images et envoyer les résultats à un nœud de franchissement de ligne.

Le nœud de franchissement de ligne vous permet de détecter quand les objets franchissent la ligne virtuelle. Les événements contiennent la direction (dans le sens des aiguilles d’une montre ou inversement) et un compteur total par direction.  

Ce tutoriel utilise une machine virtuelle Azure comme appareil IoT Edge, ainsi qu’un flux vidéo en direct simulé.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-tracker-topology.png" alt-text="Détectez quand des objets franchissent une ligne virtuelle dans une vidéo en direct.":::

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension HTTP](../pipeline.md#http-extension-processor).

Le nœud d’extension HTTP joue le rôle d’un proxy. Il convertit chaque dixième image vidéo en type d’image spécifié. Ensuite, il relaie l’image sur HTTP vers un autre module périphérique qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, le module de périphérie est généré à l’aide du modèle [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), qui peut détecter de nombreux types d’objets. Le nœud de processeur d’extension HTTP collecte les résultats de la détection et envoie ces résultats et toutes les images vidéo (pas seulement la dixième image) au nœud de suivi d’objets. Le nœud de suivi d’objets utilise des techniques de flux optique pour suivre l’objet dans les 9 images sur lesquelles le modèle IA n’a pas été appliqué. Le nœud de suivi publie ses résultats sur le nœud récepteur de messages IoT Hub. Ce nœud [récepteur de messages IoT Hub](../pipeline.md#iot-hub-message-sink) envoie ensuite ces événements au [hub IoT Edge](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

Le nœud de franchissement de ligne reçoit les résultats du nœud de suivi d’objets en amont. La sortie du nœud de suivi d’objets contient les coordonnées des objets détectés. Ces coordonnées sont comparées par le nœud de franchissement de ligne aux coordonnées de la ligne. Quand des objets franchissent la ligne, le nœud de franchissement de ligne émet un événement. Les événements sont envoyés au récepteur de messages du hub IoT Edge. 

Ce didacticiel présente les procédures suivantes :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Comprendre comment calculer des coordonnées.
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
    * Le module **avaextension**, qui est le modèle de détection d’objets YOLOv3 qui applique la vision par ordinateur aux images et retourne plusieurs classes de types d’objet

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::


## <a name="create-and-deploy-the-live-pipeline"></a>Créer et déployer le pipeline en direct

### <a name="review-sample-video"></a>Passer en revue l’exemple de code

Quand vous configurez les ressources Azure, une courte vidéo du trafic d’une autoroute est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce tutoriel utilise le fichier vidéo pour simuler un stream en direct.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [l’exemple de vidéo d’intersection d’autoroute](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) pour démarrer la lecture. Vous voyez la séquence vidéo de nombreux véhicules se déplaçant dans le trafic d’une autoroute.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

### <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* **c2d-console-app.csproj** : Fichier projet pour Visual Studio Code.
* **operations.json** : Ce fichier liste les différentes opérations que vous allez exécuter.
* **Program.cs** : Exemple de code de programme :
    * Il charge les paramètres de l’application.
    * Appelle des méthodes directes exposées par le module périphérique Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en appelant ses [méthodes directes](direct-methods.md).
    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et les événements générés par le module dans la fenêtre **SORTIE**.
    * Invoque des méthodes directes pour nettoyer des ressources.

1. Modifiez le fichier operations.json :
    
    * Changez le lien de la topologie de pipeline :
    * `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/line-crossing/topology.json"`
    * Sous `livePipelineSet`, modifiez le nom de la topologie de manière à ce qu’il corresponde à la valeur figurant dans le lien ci-dessus précédent :
    * `"topologyName" : "LineCrossingWithHttpExtension"`
    * Sous `pipelineTopologyDelete`, modifiez le nom :
    * `"name" : "LineCrossingWithHttpExtension"`
    
Ouvrez l’URL de la topologie de pipeline dans un navigateur et examinez les paramètres du nœud d’extension HTTP.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

Ici, `skipSamplesWithoutAnnotation` est défini sur `false`, car le nœud d’extension doit parcourir toutes les images, qu’elles aient ou non des résultats d’inférence, jusqu’au nœud de suivi d’objets en aval. Le traceur d’objets peut assurer le suivi d’objets sur environ 15 images. Votre modèle AI a une valeur maximale d’FPS pour le traitement, qui est la valeur la plus élevée à laquelle `maximumSamplesPerSecond` doit être définie.

Examinez également les espaces réservés `linecrossingName` et `lineCoordinates` des paramètres du nœud de franchissement de ligne. Nous avons fourni des valeurs par défaut pour ces paramètres, mais vous les remplacez avec le fichier operations.json. Regardez comment nous passons les autres paramètres du fichier operations.json à une topologie (par ex., URL RTSP).  

 
## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre **TERMINAL**.
1. Le code operations.json commence par appeler les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide/tutoriels précédents, ce processus retourne des listes vides, puis se met en pause. Pour continuer, sélectionnez la touche Entrée.
    
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
      "topologyName": "LineCrossingWithHttpExtension",
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
    * Un appel à `livePipelineActivate` qui démarre le pipeline en direct et le flux vidéo.
    * Un deuxième appel à `livePipelineList` qui indique que le pipeline en direct est dans l’état En cours d’exécution.
1. La sortie de la fenêtre TERMINAL s’interrompt avec l’invite Appuyez sur Entrée pour continuer. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre SORTIE de Visual Studio Code. Vous voyez des messages indiquant que le module Video Analyzer envoie des données au hub IoT. La section suivante de ce tutoriel décrit ces messages.
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
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafetaria-01.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T17:33:09.554Z",
    "dataVersion": "1.0"
  },
  }
}
```

Dans ce message, notez les informations suivantes :

* Le message est un événement de diagnostic. **MediaSessionEstablished** indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* Dans `applicationProperties`, « subject » indique que le message a été généré à partir du nœud source RTSP dans le pipeline en direct.
* Dans `applicationProperties`, « eventType » indique que cet événement est un événement de diagnostic.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="line-crossing-events"></a>Événements de franchissement de ligne

Le nœud de processeur d’extension HTTP envoie les images 0, 15, 30,... etc. au module avaextension, et reçoit les résultats de l’inférence. Il envoie ensuite ces résultats et toutes les images vidéo au nœud de suivi d’objets. Le suivi d’objets génère des résultats d’inférence pour toutes les images (0, 1, 2,...) qui sont ensuite comparés par le nœud de franchissement de ligne aux coordonnées de la ligne spécifiées dans la topologie. Quand les objets franchissent ces coordonnées, un événement est déclenché. L’événement ressemble à celui-ci :
```
{
  "body": {
    "timestamp": 145865319410261,
    "inferences": [
      {
        "type": "event",
        "subtype": "lineCrossing",
        "inferenceId": "8f4f7b25d6654536908bcfe34374a15e",
        "relatedInferences": [
          "c9ea5decdd6a487089ded249c748cf5b"
        ],
        "event": {
          "name": "LineCrossing1",
          "properties": {
            "counterclockwiseTotal": "35",
            "direction": "counterclockwise",
            "total": "38",
            "clockwiseTotal": "3"
          }
        }
      }
    ]
  },
```
Dans ce message, notez les informations suivantes :
* le type est `event` avec un sous-type `lineCrossing`.
* L’événement contient le `name` spécifié dans la topologie de la ligne qui a été franchie.
* Le nombre `total` de franchissements de ligne dans n’importe quelle direction.
* Le nombre de franchissements `clockwiseTotal`.
* Le nombre de franchissements `counterclockwiseTotal`.
* `direction` contient la direction de cet événement.

> [!NOTE] 
> Si vous avez déployé des ressources Azure à l’aide du déploiement en un clic pour ce tutoriel, une machine virtuelle Standard DS1 est créée. Toutefois, pour obtenir des résultats précis à partir de modèles AI consommant beaucoup de ressources comme YOLO, il peut être nécessaire d’augmenter la taille de la machine virtuelle. [Redimensionnez la machine virtuelle](../../../virtual-machines/windows/resize-vm.md) pour augmenter le nombre de processeurs virtuels et la mémoire en fonction de vos besoins. Réactivez ensuite le pipeline en direct pour afficher les inférences.

## <a name="customize-for-your-own-environment"></a>Personnaliser pour votre propre environnement

Ce tutoriel fonctionne avec l’exemple de vidéo fourni pour lequel nous avons calculé les coordonnées appropriées de la ligne. Quand vous examinez le fichier de topologie, vous voyez que le paramètre `lineCoordinates` contient la valeur suivante : `[[0.5,0.1], [0.5,0.9]]`

Que signifie cette valeur ? Pour dessiner une ligne sur une image 2D, vous avez besoin de deux points, A et B, entre lesquels vous avez une ligne imaginaire. Chaque point a ses propres coordonnées x et y pour déterminer où il se trouve par rapport à la résolution d’image complète. Dans ce cas, le point A est `[0.5,0.1]` et le point B est `[0.5,0.9]`. Une représentation visuelle de cette ligne ressemble à ceci :
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-visual-example.png" alt-text="Exemple visuel montrant un franchissement de ligne sur une image.":::

Dans cette image, vous voyez la ligne entre le point A et le point B. Tout objet qui franchit la ligne crée un événement avec des propriétés dont la direction, comme nous l’avons vu plus haut dans ce tutoriel. Notez également les axes x et y en bas à gauche. Cette illustration nous permet simplement d’expliquer comment nous normalisons les coordonnées afin d’obtenir les valeurs attendues pour le nœud de franchissement de ligne. 

Voici un exemple de calcul : supposons que la résolution vidéo est 1920 x 1080. 1920 et 1080 étant respectivement le nombre de pixels le long des axes x et y.
Créez une image à partir d’une image de la vidéo que vous prévoyez d’utiliser. Ouvrez maintenant cette image dans un programme d’éditeur d’images (par exemple, MSPaint). Déplacez le curseur là où vous voulez spécifier le point A. En bas à gauche, vous voyez les coordonnées x et y de cette position de curseur.
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-mspaint-coordinates.png" alt-text="Exemple visuel de franchissement de ligne dans MSPaint.":::

Notez ces valeurs et répétez la même opération pour le point B. Notez également les valeurs. À ce stade, vous devez avoir les valeurs x et y du point A, et les valeurs x et y du point B. Par exemple : point A : x=1024, y=96 et point B : x=1024, y=960. Ces valeurs ne sont pas prises en charge par le nœud de franchissement de ligne puisqu’il accepte seulement des nombres compris entre 0 et 1. Pour les calculer, vous appliquez la formule suivante :

`x coordinate / image resolution along x axis`, soit `1024/1920 = 0.5` dans notre exemple. De la même façon, y : `96/1080=0.1`. Il s’agit des coordonnées normalisées du point A. Répétez cette opération pour le point B. Vous obtenez un tableau de valeurs comprises entre 0 et 1 `[[0.5,0.1], [0.5,0.9]]`, comme indiqué plus haut dans ce tutoriel.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Essayez d’exécuter différentes vidéos dans le pipeline en direct.
