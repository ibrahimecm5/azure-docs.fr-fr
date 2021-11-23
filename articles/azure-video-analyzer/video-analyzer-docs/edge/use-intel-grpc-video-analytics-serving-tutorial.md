---
title: Analyser des vidéos en direct avec l’extension Intel OpenVINO™ DL Streamer – Edge AI via gRPC avec Azure Video Analyzer
description: Ce tutoriel montre comment utiliser l’extension Intel OpenVINO™ DL Streamer – Edge AI pour analyser le flux vidéo en direct d’une caméra IP (simulée).
ms.topic: tutorial
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a3436dced8129aa984d30a0f2b4d6bcfecb5913
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484224"
---
# <a name="tutorial-analyze-live-video-with-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutoriel : Analyser une vidéo en direct avec l’extension Intel OpenVINO™ DL Streamer – Edge AI 

[!INCLUDE [header](includes/edge-env.md)]

Ce tutoriel montre comment utiliser Intel OpenVINO™ DL Streamer – Edge AI Extension pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée). Vous allez voir comment ce serveur d’inférence vous donne accès à différents modèles pour détecter des objets (une personne, un véhicule ou un vélo), classifier des objets (attributions de véhicule) et un modèle pour suivre des objets (personne, véhicule et vélo). L’intégration au module gRPC vous permet d’envoyer des images vidéo au serveur d’inférence IA. Les résultats sont alors envoyés au hub IoT Edge. Quand vous exécutez ce service d’inférence sur le même nœud de calcul qu’Azure Video Analyzer, vous pouvez tirer parti de l’envoi des données vidéo via la mémoire partagée. De cette façon, vous pouvez exécuter une inférence avec la fréquence d’images du flux vidéo en direct (par exemple, 30 images/s). 

Ce tutoriel utilise une machine virtuelle Azure comme appareil IoT Edge simulé ainsi qu’un flux vidéo en direct simulé. Il est basé sur l’exemple de code écrit en C# et s’appuie sur le guide de démarrage rapide [Détecter les événements de mouvement et d’émission](detect-motion-emit-events-quickstart.md).

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Quand vous configurez les ressources Azure, une courte vidéo d’un parking est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce tutoriel utilise le fichier vidéo pour simuler un stream en direct.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/). Sélectionnez Ctrl+N, puis collez un lien vers [la vidéo](https://avamedia.blob.core.windows.net/public/lots_015.mkv) pour démarrer la lecture. Vous voyez la séquence vidéo des véhicules sur le parking, la plupart d’entre eux garés et l’un d’eux en déplacement.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Dans ce tutoriel, vous utilisez Video Analyzer avec l’extension Intel OpenVINO™ DL Streamer – Edge AI pour détecter des objets comme des véhicules, des personnes ou des vélos, afin de les classifier ou de les tracer. Vous publierez les événements d’inférence associés sur un hub IoT Edge.

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.png" alt-text="Vue d’ensemble du pipeline Azure Video Analyzer avec le module Intel DL Streamer Edge AI.":::

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension gRPC](../pipeline.md#grpc-extension-processor). 

Le nœud processeur d’extension gRPC prend les images vidéo décodées comme entrée et les relaie à un point de terminaison [gRPC](../terminology.md#grpc) exposé par un serveur gRPC. Le nœud prend en charge le transfert de données à l’aide de la [mémoire partagée](https://en.wikipedia.org/wiki/Shared_memory) ou l’incorporation directe du contenu dans le corps des messages gRPC. De plus, le nœud dispose d’un formateur d’image intégré pour la mise à l’échelle et l’encodage des images vidéo avant qu’elles soient relayées au point de terminaison gRPC. Le processus de mise à l’échelle propose des options permettant de préserver les proportions de l’image, de la remplir ou de l’étirer. L’encodeur d’image prend en charge les formats JPEG, PNG ou BMP. Découvrez-en plus sur le processeur [ici](../pipeline.md#grpc-extension-processor).

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>À propos du module Intel OpenVINO™ DL Streamer – Edge AI Extension


Le module OpenVINO™ DL Streamer - Edge AI Extension est un microservice basé sur Video Analytics Serving (VA Serving) d’Intel qui sert des pipelines d’analytique vidéo créés avec OpenVINO™ DL Streamer. Les développeurs peuvent envoyer des images vidéo décodées au module d’extension IA qui effectue la détection, la classification ou le suivi avant de retourner les résultats. Le module d’extension IA expose des API gRPC compatibles avec les plateformes d’analytique vidéo comme Azure Video Analyzer de Microsoft.

Pour créer des solutions d’analytique vidéo complexes hautes performances, le module Azure Video Analyzer doit être associé à un moteur d’inférence puissant capable d’optimiser l’échelle à la périphérie. Dans ce tutoriel, les demandes d’inférence sont envoyées à l’extension Intel OpenVINO™ DL Streamer – Edge AI, module périphérique conçu pour fonctionner avec Azure Video Analyzer.

Dans la version initiale de ce serveur d’inférence, vous avez accès aux [modèles](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options) suivants :

- object_detection for person_vehicle_bike_detection ![détection d’objet pour véhicule](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification for vehicle_attributes_recognition ![classification d’objet pour véhicule](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking for person_vehicle_bike_tracking ![suivi d’objet pour personne ou véhicule](./media/use-intel-openvino-tutorial/object-tracking.png)

Ce modèle utilise les pipelines de détection d’objet préchargé, de classification d’objet et de suivi d’objet pour démarrer rapidement. De plus, il est fourni avec les modèles préchargés [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) et [vehicle-attributes-recognition-barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md).

> [!NOTE]
> En téléchargeant et en utilisant le module Edge OpenVINO™ DL Streamer – Edge AI Extension d’Intel et le logiciel inclus, vous acceptez les termes et conditions du [contrat de licence](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel s’engage à respecter les droits de l’homme et à éviter toute complicité en matière d’abus des droits de l’homme. Consultez les [principes généraux d’Intel en matière de droits de l’homme](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Les produits et logiciels Intel sont destinés uniquement à être utilisés dans des applications qui n’entraînent pas ou ne contribuent pas à une violation d’un droit humain reconnu au niveau international.

Vous pouvez utiliser la flexibilité des différents pipelines pour votre cas d’usage spécifique en modifiant simplement les variables d’environnement de pipeline dans votre modèle de déploiement. De cette façon, vous pouvez changer rapidement le modèle de pipeline et, si vous l’associez à Azure Video Analyzer, changer en quelques secondes le pipeline de média et le modèle d’inférence.

Ce didacticiel présente les procédures suivantes :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>Déployer les modules nécessaires

1. Dans Visual Studio Code, cliquez avec le bouton droit sur le fichier *src/edge/deployment.openvino.grpc.template.json*, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Générer un manifeste de déploiement IoT Edge":::
1. Le fichier manifeste *deployment.openvino.grpc.amd64.json* est créé dans le dossier *src/edge/config*.
1. Cliquez avec le bouton droit sur *src/edge/config/deployment.openvino.grpc.amd64.json* et sélectionnez **Créer un déploiement pour un seul appareil**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for a Single Device":::
1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **avasample-iot-edge-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :

    * Le module périphérique Video Analyzer, nommé **avaedge**.
    * Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct. 
    * Le module **avaextension**, c’est-à-dire le module Intel OpenVINO DL Streamer avec la détection, la classification et le modèle de suivi des objets qui applique la vision par ordinateur aux images et retourne plusieurs classes de types d’objet.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "OpenVINO object detection model":::

> [!NOTE]
> Nous avons également ajouté un modèle *deployment.openvino.grpc.xpu.template.json* qui permet de prendre en charge le processeur, le processeur virtuel et le processeur graphique (si disponibles dans votre appareil), pour le module d’extension Intel OpenVINO DL Streamer – Edge AI. Ces modèles pointent vers l’image du hub Docker d’Intel.

### <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Cliquez avec le bouton droit sur l’appareil Azure Video Analyzer et sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**. Vous devez effectuer cette étape pour superviser les événements IoT Hub dans la fenêtre **SORTIE** de Visual Studio Code.

![Démarrer la surveillance](./media/quickstarts/start-monitoring-iot-hub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Exécuter l’exemple de programme pour détecter des véhicules, des personnes ou un vélo
Si vous ouvrez la [topologie de pipeline](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) de ce tutoriel dans un navigateur, vous voyez que la valeur de `grpcExtensionAddress` a été définie sur `tcp://avaExtension:5001`. Comme dans le tutoriel *httpExtensionOpenVINO*, vous n’avez pas besoin de remplacer l’URL par celle du serveur gRPC. À la place, vous indiquez au module à l’aide de la section `extensionConfiguration` du fichier operations d’exécuter un pipeline spécifique. Dans le cas contraire, la valeur par défaut est « object_detection » pour « person_vehicle_bike_detection ». Vous pouvez faire des essais avec d’autres pipelines pris en charge.

1. Modifiez le fichier *operations.json* :
    * Changez le lien de la topologie du pipeline en direct :

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"`

    * Sous `pipelineTopologySet`, remplacez le nom de la topologie du pipeline en direct par la valeur du lien précédent :

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Sous `pipelineTopologyDelete`, modifiez le nom :

      `"name": "InferencingWithOpenVINOgRPC"`
    
    * Sous `rtspUrl`, définissez l’URL sur : `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

Ajoutez la section extensionConfiguration suivante après les paramètres RTSP :

```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
}
```


Votre `operations.json` doit ressembler à ceci :

```
{
  "apiVersion": "1.1,
  "operations": [
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue."
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the activating steps."
          }
      },
      {
          "opName": "pipelineTopologySet",
          "opParams": {
              "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"
          }
      },
      {
          "opName": "livePipelineSet",
          "opParams": {
              "name": "Sample-Pipeline-1",
              "properties": {
                  "topologyName": "InferencingWithOpenVINOgRPC",
                  "description": "Sample pipeline description",
                  "parameters": [
                      {
                          "name": "rtspUrl",
                          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
                      },
                      {
                          "name": "rtspUserName",
                          "value": "testuser"
                      },
                      {
                          "name": "rtspPassword",
                          "value": "testpassword"
                      },
                      {
                        "name": "extensionConfiguration",
                        "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
                      }
                  ]
              }
          }
      },
      {
          "opName": "livePipelineActivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the deactivating steps."
          }
      },
      {
          "opName": "livePipelineDeactivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineDelete",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "pipelineTopologyDelete",
          "opParams": {
              "name": "InferencingWithOpenVINOgRPC"
          }
      },
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      }
  ]
}
```

L’utilisation de la section `extensionConfiguration` vous permet de passer une chaîne de données au module Intel DL Streamer - Edge AI. Avec les données appropriées, vous pouvez indiquer au module Edge AI d’utiliser un modèle spécifique pour chaque pipeline. Par exemple, vous pouvez remplacer le modèle « object_detection » utilisé ci-dessus par d’autres modèles pris en charge, comme la classification ou le suivi. Pour ce faire, utilisez les exemples suivants :

Classification :
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_classification\",\"version\":\"vehicle_attributes_recognition\"}}"
}
```

Suivi :
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_tracking\",\"version\":\"person_vehicle_bike_tracking\"}}"
}
```

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Paramètres d’extension":::
1. Recherchez et activez « Afficher le message détaillé ».

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Afficher le message détaillé":::
1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre **TERMINAL**.
1. Le code *operations.json* commence par appeler les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide/tutoriels précédents, ce processus retourne des listes vides, puis se met en pause. Pour continuer, sélectionnez la touche Entrée.

    La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

     * Un appel à `pipelineTopologySet` qui utilise le `topologyUrl` précédent
     * Un appel à `livePipelineSet` qui utilise le corps suivant :

         ```
         {
           "@apiVersion": "1.1",
           "name": "Sample-Pipeline-1",
           "properties": {
             "topologyName": "InferencingWithGrpcExtension",
             "description": "Sample pipeline description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               },
               {
                 "name": "grpcExtensionAddress",
                 "value": "tcp://avaextension:5001"
               },
               {
                 "name": "extensionConfiguration",
                 "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
               }
             ]
           }
         }
         ```

     * Un appel à `livePipelineActivate` qui démarre le pipeline et le flux vidéo
1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau d’une invite `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Vous voyez des messages indiquant que le module Video Analyzer envoie des données au hub IoT. La section suivante de ce tutoriel décrit ces messages.
1. Le pipeline en direct continue à s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline en direct, retournez à la fenêtre **TERMINAL** et sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :
      * Un appel à `livePipelineDeactivate` désactive le pipeline.
      * Un appel à `livePipelineDelete` supprime le pipeline.
      * Un appel à `pipelineTopologyDelete` supprime la topologie.
      * Un dernier appel à `pipelineTopologyList` indique que la liste est vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, les résultats du nœud de processeur d’extension HTTP sont passés au hub IoT par le nœud récepteur des messages IoT Hub. Les messages que vous voyez dans la fenêtre **SORTIE** contiennent une section `body`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit le contenu du corps. 

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline en direct est activé, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
}
```

Dans la sortie précédente : 

* Le message est un événement de diagnostic, `MediaSessionEstablished`. Il indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* La section `sdp` contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud processeur d’extension gRPC reçoit les résultats d’inférence en provenance d’Intel OpenVINO™ DL Streamer – Edge AI Extension. Il envoie ensuite les résultats via le nœud récepteur de messages IoT Hub sous forme d’événements d’inférence. 

Dans ces événements, le type est défini sur `entity` pour indiquer qu’il s’agit d’une entité, comme une voiture ou un camion. 

Dans l’exemple suivant, un véhicule a été détecté, avec des valeurs de confiance supérieures à 0,9.

```
[IoTHubMonitor] [3:10:23 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145819820073974,
  "inferences": [
    {
      "type": "entity",
      "subtype": "vehicleDetection",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9147264
        },
        "box": {
          "l": 0.6853116,
          "t": 0.5035262,
          "w": 0.04322505,
          "h": 0.03426218
        }
      }
    }
```

Dans le message, notez les informations suivantes :

* La section `body` contient des données relatives à l’événement d’analytique. Dans ce cas, l’événement est un événement d’inférence. Par conséquent, le corps contient des données `inferences`.
* La section `inferences` indique que le `type` est `entity`. Cette section inclut des données supplémentaires sur l’entité.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, puis à vos groupes de ressources. Sélectionnez le groupe de ressources où vous avez exécuté ce tutoriel, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
* Utilisez un appareil Linux x64 Intel plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md). Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../../iot-edge/quickstart-linux.md).
