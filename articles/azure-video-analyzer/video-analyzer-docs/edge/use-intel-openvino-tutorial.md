---
title: Analyser une vidéo en direct avec l’extension IA Intel OpenVINO™ Model Server
description: Dans ce tutoriel, vous utilisez un serveur de modèles IA fourni par Intel avec des modèles préentraînés pour analyser le flux vidéo en direct d’une caméra IP (simulée).
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
titleSuffix: Azure
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2e6e14849bfa00862b2fd87a0e42d88b315ab14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490702"
---
# <a name="tutorial-analyze-live-video-using-openvino-model-server--ai-extension-from-intel"></a>Tutoriel : Analyser une vidéo en direct avec l’extension IA Intel OpenVINO™ Model Server 

[!INCLUDE [header](includes/edge-env.md)]

Ce tutoriel montre comment utiliser l’[extension IA Intel OpenVINO™ Model Server](https://aka.ms/ava-intel-ovms) pour analyser le flux vidéo en direct d’une caméra IP (simulée). Vous verrez comment ce serveur d’inférence vous donne accès à des modèles pour la détection d’objets (une personne, un véhicule ou un vélo) et à un modèle pour la classification des véhicules. Un sous-ensemble d’images du flux vidéo en direct est envoyé à ce serveur d’inférence, et les résultats sont envoyés au hub IoT Edge.

Ce tutoriel utilise une machine virtuelle Azure comme appareil IoT Edge, ainsi qu’un flux vidéo en direct simulé. Il se base sur un exemple de code écrit en C#.

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>À propos de l’extension IA OpenVINO™ Model Server d’Intel

La distribution Intel® du [kit de ressources OpenVINO™](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (inférence visuelle ouverte et optimisation de réseau neuronal) est un kit logiciel gratuit qui permet aux développeurs et aux scientifiques des données d’accélérer les charges de travail de vision par ordinateur, de rationaliser l’inférence et les déploiements de deep learning, et de bénéficier d’exécutions simples et hétérogènes sur toutes les plateformes Intel®, de la périphérie au cloud. Elle comprend le kit de ressources de déploiement deep learning d’Intel® avec optimiseur de modèle et moteur d’inférence, ainsi que le référentiel [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) qui comprend plus de 40 modèles pré-entraînés et optimisés.

Pour créer des solutions d’analytique vidéo complexes hautes performances, le module Video Analyzer doit être associé à un moteur d’inférence puissant capable d’optimiser l’échelle à la périphérie. Dans ce tutoriel, les demandes d’inférence sont envoyées à l’[extension IA Intel OpenVINO™ Model Server](https://aka.ms/ava-intel-ovms), module périphérique conçu pour fonctionner avec Video Analyzer. Ce module de serveur d’inférence contient OpenVINO™ Model Server (OVMS), un serveur d’inférence tirant parti du kit de ressources OpenVINO™, hautement optimisé pour les charges de travail de vision par ordinateur et développé pour les architectures Intel®. Une extension a été ajoutée à OVMS pour faciliter l’échange d’images vidéo et de résultats d’inférence entre le serveur d’inférence et le module Video Analyzer, ce qui vous permet d’exécuter n’importe quel modèle de boîte à outils OpenVINO™ pris en charge (vous pouvez personnaliser le module de serveur d’inférence en modifiant le [code](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)). Vous pouvez de plus choisir parmi la large gamme de mécanismes d’accélération fournis par le matériel Intel®. notamment les processeurs (Atom, Core, Xeon), FPGA et VPU.

Dans la version initiale de ce serveur d’inférence, vous avez accès aux [modèles](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models) suivants :

- Détection de véhicule (URL d’inférence : http://{nom_module}:4000/vehicleDetection)
- Détection de personne/véhicule/vélo (URL d’inférence : http://{nom_module}:4000/personVehicleBikeDetection)
- Classification de véhicule (URL d’inférence : http://{nom_module}:4000/vehicleClassification)
- Détection de visage (URL d’inférence : http://{nom_module}:4000/faceDetection)

> [!NOTE]
> En téléchargeant et en utilisant le module Edge d’extension IA pour OpenVINO™ Model Server d’Intel et le logiciel inclus, vous acceptez les termes et conditions du [contrat de licence](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel s’engage à respecter les droits de l’homme et à éviter toute complicité en matière d’abus des droits de l’homme. Consultez les [principes généraux d’Intel en matière de droits de l’homme](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Les produits et logiciels Intel sont destinés uniquement à être utilisés dans des applications qui n’entraînent pas ou ne contribuent pas à une violation d’un droit humain reconnu au niveau international.

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.png" alt-text="Vue d'ensemble":::

Ce diagramme montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension HTTP](../pipeline-extension.md#http-extension-processor). 

Le nœud de processeur d’extension HTTP joue le rôle d’un proxy. Il sélectionne un sous-ensemble d’images vidéo entrantes et les convertit en images. Ensuite, il relaie l’image sur REST vers un autre module de périphérie qui exécute des modèles IA derrière un point de terminaison HTTP. Dans cet exemple, ce module de périphérie est l’extension IA OpenVINO™ Model Server d’Intel. Le nœud de processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le nœud [récepteur de messages IoT Hub](../pipeline.md#iot-hub-message-sink). Ce nœud envoie ensuite ces événements au [hub IoT Edge](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Ce didacticiel présente les procédures suivantes :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

### <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Quand vous configurez les ressources Azure, une courte [vidéo d’un parking](https://avamedia.blob.core.windows.net/public/lots_015.mkv) est copiée dans Azure sur la machine virtuelle Linux que vous utilisez comme appareil IoT Edge. Ce guide de démarrage rapide utilise le fichier vidéo pour simuler un flux en direct.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

## <a name="deploy-the-required-modules"></a>Déployer les modules nécessaires

Dans le cadre des prérequis, vous avez téléchargé l’exemple de code dans un dossier. Suivez ces étapes pour déployer les modules nécessaires.

1. Dans Visual Studio Code, cliquez avec le bouton droit sur le fichier *src/edge/deployment.openvino.template.json*, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.   
1. Le fichier manifeste *deployment.openvino.amd64.json* est créé dans le dossier *src/edge/config*.
1. Cliquez avec le bouton droit sur *src/edge/config/deployment.openvino.amd64.json*, puis sélectionnez **Créer un déploiement pour un seul appareil**.
1. Quand vous êtes invité à sélectionner un appareil IoT Hub, sélectionnez **avasample-iot-edge-device**.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :
    * Le module périphérique Video Analyzer, nommé **avaedge**.
    * Le module **rtspsim**, qui simule un serveur RTSP et fait office de source d’un flux vidéo en direct. 
    * Le module **openvino**, qui est le module de l’extension IA Intel OpenVINO™ Model Server.
 
## <a name="create-and-activate-the-live-pipeline"></a>Créer et activer le pipeline en direct

### <a name="edit-the-sample-code"></a>Modifier l’exemple de code
1. Dans Visual Studio Code, accédez à *src/cloud-to-device-console-app* et modifiez le fichier *operations.json* :
    * Changez le lien de la topologie du pipeline en direct :

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json"`

    * Sous `livePipelineSet`, remplacez le nom de la topologie du pipeline en direct par la valeur du lien précédent :

      `"topologyName" : "InferencingWithOpenVINO"`

    * Sous `pipelineTopologyDelete`, modifiez le nom :

      `"name": "InferencingWithOpenVINO"`

### <a name="run-the-sample-program-to-detect-vehicles"></a>Exécuter l’exemple de programme pour détecter les véhicules
Si vous ouvrez la topologie de pipeline (`pipelineTopologyUrl`) de ce tutoriel dans un navigateur, vous voyez que la valeur de `inferencingUrl` a été définie sur `http://openvino:4000/vehicleDetection`, ce qui signifie que le serveur d’inférence tente de détecter des véhicules dans la vidéo en direct.

1. Pour démarrer une session de débogage, appuyez sur la touche F5. Des messages s’affichent dans la fenêtre **TERMINAL**.
1. Le code *operations.json* commence par appeler les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides. La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

     * Un appel à `pipelineTopologySet` qui utilise le `pipelineTopologyUrl` précédent
     * Un appel à `livePipelineSet` qui utilise le corps suivant :

         ```json
            {
              "@apiVersion": "1.1",
              "name": "Sample-Pipeline-1",
              "properties": {
                "topologyName": "InferencingWithOpenVINO",
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

     * Un appel à `livePipelineActivate` qui démarre le pipeline et le flux vidéo
1. La sortie affichée dans la fenêtre **TERMINAL** fait une pause au niveau d’une invite `Press Enter to continue`. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Vous voyez des messages indiquant que le module Video Analyzer envoie des données au hub IoT. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. Le pipeline en direct continue à s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline en direct, retournez à la fenêtre **TERMINAL** et sélectionnez Entrée. 

    La série d’appels suivante nettoie les ressources :
      * Un appel à `livePipelineDeactivate` désactive le pipeline.
      * Un appel à `livePipelineDelete` supprime le pipeline.
      * Un appel à `pipelineTopologyDelete` supprime la topologie.
      * Un dernier appel à `pipelineTopologyList` indique que la liste est vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, les résultats du nœud de processeur d’extension HTTP sont passés au hub IoT par le nœud récepteur des messages IoT Hub. Les messages que vous voyez dans la fenêtre **SORTIE** contiennent une section `body`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit les propriétés de l’application et le contenu du corps. 

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline en direct est activé, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion est établie, un événement s’affiche dans la fenêtre **SORTIE** avec les éléments suivants.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

Dans la sortie précédente : 

* Le message est un événement de diagnostic, **MediaSessionEstablished**. Il indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* La section `sdp` contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud processeur d’extension HTTP reçoit les résultats d’inférence à partir du module d’extension IA pour OpenVINO™ Model Server. Il envoie ensuite les résultats via le nœud récepteur de messages IoT Hub sous forme d’événements d’inférence. 

Dans ces événements, le type est défini sur `entity` pour indiquer qu’il s’agit d’une entité, comme une voiture ou un camion. Dans l’exemple suivant de `body` pour ce type d’événement, un véhicule a été détecté, avec une valeur de confiance supérieure à 0,9.

```json
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

## <a name="run-the-sample-program-to-detect-persons-vehicles-or-bikes"></a>Exécuter l’exemple de programme pour détecter des personnes, des véhicules ou des vélos
Pour utiliser un modèle différent, vous devez modifier la topologie de pipeline ainsi que le fichier `operations.json`.

Copiez la topologie de pipeline (URL utilisée dans `pipelineTopologyUrl`) dans un fichier local, par exemple, `C:\TEMP\topology.json`. Ouvrez cette copie, puis affectez `http://openvino:4000/personVehicleBikeDetection` comme valeur de `inferencingUrl`.

Ensuite, dans Visual Studio Code, accédez au dossier *src/cloud-to-device-console-app* et ouvrez le fichier `operations.json`. Modifiez la ligne indiquant `pipelineTopologyUrl` comme suit :

```
      "pipelineTopologyFile" : "C:\\TEMP\\topology.json" 
```
Vous pouvez maintenant répéter les étapes ci-dessus pour réexécuter l’exemple de programme avec la nouvelle topologie. Les résultats de l’inférence seront similaires (dans le schéma) à ceux du modèle de détection de véhicule, mais avec `subtype` défini sur `personVehicleBikeDetection`.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Exécuter l’exemple de programme pour classifier les véhicules
Dans Visual Studio Code, ouvrez la copie locale du fichier `topology.json` de l’étape précédente, puis remplacez la valeur de `inferencingUrl` par `http://openvino:4000/vehicleClassification`. Si vous avez exécuté l’exemple précédent pour détecter des personnes, des véhicules ou des vélos, vous n’avez pas besoin de remodifier le fichier `operations.json`.

Vous pouvez maintenant répéter les étapes ci-dessus pour réexécuter l’exemple de programme avec la nouvelle topologie. Voici un exemple de résultat de la classification.

```json
{
  "timestamp": 145819896480527,
  "inferences": [
    {
      "type": "classification",
      "subtype": "color",
      "classification": {
        "tag": {
          "value": "gray",
          "confidence": 0.683415
        }
      }
    },
    {
      "type": "classification",
      "subtype": "type",
      "classification": {
        "tag": {
          "value": "truck",
          "confidence": 0.9978394
        }
      }
    }
  ]
}

```

## <a name="run-the-sample-program-to-detect-faces"></a>Exécuter l’exemple de programme pour détecter les visages
Dans Visual Studio Code, ouvrez la copie locale du fichier `topology.json` de l’étape précédente, puis remplacez la valeur de `inferencingUrl` par `http://openvino:4000/faceDetection`. Si vous avez exécuté l’exemple précédent pour détecter des personnes, des véhicules ou des vélos, vous n’avez pas besoin de remodifier le fichier `operations.json`.

Vous pouvez maintenant répéter les étapes ci-dessus pour réexécuter l’exemple de programme avec la nouvelle topologie. Vous trouverez ci-dessous un exemple de résultat de détection (Remarque : La vidéo du parking utilisée plus haut ne contient pas de visages détectables ; vous devez utiliser une autre vidéo pour tester ce modèle).

```json
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  }
```
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, puis à vos groupes de ressources. Sélectionnez le groupe de ressources où vous avez exécuté ce tutoriel, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
* Utilisez un appareil Linux x64 local plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md). Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../../iot-edge/quickstart-linux.md).
