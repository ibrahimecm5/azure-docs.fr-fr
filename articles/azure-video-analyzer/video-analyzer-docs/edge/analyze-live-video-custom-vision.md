---
title: Commencer avec Azure Video Analyzer
description: Ce tutoriel vous guide dans les étapes d’analyse d’une vidéo en direct avec Azure Video Analyzer sur IoT Edge et Azure Custom Vision.
ms.topic: tutorial
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6999b30816f0750657d8c5990291892d3148cbd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097428"
---
# <a name="tutorial-analyze-live-video-with-azure-video-analyzer-on-iot-edge-and-azure-custom-vision"></a>Tutoriel : Analyser une vidéo en direct avec Azure Video Analyzer sur IoT Edge et Azure Custom Vision

[!INCLUDE [header](includes/edge-env.md)]

Dans ce tutoriel, vous apprenez à utiliser Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) pour créer un modèle conteneurisé capable de détecter un camion jouet, et à utiliser la [fonctionnalité d’extensibilité de l’IA](../analyze-live-video-without-recording.md#analyzing-video-using-a-custom-vision-model) d’Azure Video Analyzer sur Azure IoT Edge pour déployer le modèle à la périphérie et détecter des camions jouets dans un flux vidéo en direct.

Nous allons vous montrer comment tirer parti de la puissance de Custom Vision en vue de créer et d’entraîner un modèle de vision par ordinateur en chargeant et en étiquetant quelques images. Vous n’avez pas besoin d’avoir des connaissances en science des données, machine learning ou intelligence artificielle. Vous découvrez aussi les fonctionnalités de Video Analyzer, et comment déployer facilement un modèle personnalisé sous forme de conteneur à la périphérie et analyser un flux vidéo en direct simulé.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/python/header.md)]
::: zone-end

Ce didacticiel explique les procédures suivantes :

- Configurer les ressources appropriées.
- Créer un modèle Custom Vision dans le cloud pour détecter les camions jouets, et le déployer en périphérie
- Créer et déployer un pipeline avec une extension HTTP sur un modèle Custom Vision.
- Exécuter l’exemple de code.
- Examinez et interprétez les résultats.

Si vous n’avez pas [d’abonnement Azure](../../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

Lisez les articles suivants avant de commencer :

- [Vue d’ensemble de Video Analyzer sur IoT Edge](../overview.md)
- [Présentation d’Azure Custom Vision](../../../cognitive-services/custom-vision-service/overview.md)
- [Terminologie de Video Analyzer sur IoT Edge](../terminology.md)
- [Concept de pipeline](../pipeline.md)
- [Video Analyzer sans enregistrement vidéo](../analyze-live-video-without-recording.md)
- [Tutoriel : Développement d’un module IoT Edge](../../../iot-edge/tutorial-develop-for-linux.md)
- [Modification de deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Prérequis

* [Installation de Docker](https://docs.docker.com/desktop/#download-and-install) sur votre ordinateur.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/python/prerequisites.md)]
::: zone-end


## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Ce tutoriel utilise un fichier de [vidéo d’inférence du jouet](https://lvamedia.blob.core.windows.net/public/t2.mkv) pour simuler un stream en direct. Vous pouvez regarder la vidéo grâce à une application telle que [VLC Media Player](https://www.videolan.org/vlc/). Sélectionnez **Ctrl+N**, puis collez un lien vers la [vidéo d’inférence du jouet](https://lvamedia.blob.core.windows.net/public/t2.mkv) pour démarrer la lecture. Lorsque vous regarderez la vidéo, vous verrez qu’un camion jouet apparaît au bout de 36 secondes. Le modèle personnalisé a été entraîné à détecter ce jouet spécifique.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LPwK]

Dans ce tutoriel, vous utilisez Video Analyzer sur IoT Edge pour détecter ce type de camions jouets et publier des événements d’inférence associés sur le hub IoT Edge.

## <a name="overview"></a>Vue d'ensemble

![Diagramme montrant une vue d’ensemble de Custom Vision.](./media/custom-vision/topology-custom-vision.svg)

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud [processeur d’extension HTTP](../pipeline.md#http-extension-processor).

Le nœud d’extension HTTP joue le rôle d’un proxy. Il échantillonne les images vidéo entrantes définies par vous à l’aide du champ `samplingOptions` et convertit également les images vidéo vers le type d’image spécifié. Ensuite, il relaie l’image vers le modèle de détecteur de camion jouet créé à l’aide de Custom Vision. Le nœud de processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le nœud [récepteur de messages Azure IoT Hub](../pipeline.md#iot-hub-message-sink), qui envoie ensuite ces événements au [hub IoT Edge](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Créer et déployer un modèle de détection de jouet avec Custom Vision

Comme le nom Custom Vision le suggère, vous pouvez utiliser ce module afin de créer votre propre détecteur ou classifieur personnalisé d’objets dans le cloud. Il fournit une interface simple, facile à utiliser et intuitive pour créer des modèles Custom Vision qui peuvent être déployés dans le cloud ou en périphérie grâce à des conteneurs.

Pour créer un détecteur de camion jouet, suivez les étapes décrites dans [Démarrage rapide : Créer un détecteur d’objets avec le site web Custom Vision](../../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Ce module Custom Vision ne prend en charge que les architectures **Intel x86 et amd64**. Vérifiez l’architecture de votre appareil périphérique avant de continuer.

Informations complémentaires :

- Pour ce tutoriel, n’utilisez pas les exemples d’images fournis dans la [section Prérequis](../../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) du guide de démarrage rapide. Pour créer le modèle Custom Vision de détection des jouets, nous allons utiliser un autre ensemble d’images. Utilisez [ces images](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) quand vous êtes invité à [choisir vos images d’entraînement](../../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) dans le [guide de démarrage rapide](../../../cognitive-services/custom-vision-service/get-started-build-detector.md).
- Dans la section Étiquetage des images du guide de démarrage rapide, veillez à étiqueter le camion jouet vu dans l’image avec l’étiquette « delivery truck » (camion de livraison).
- Sélectionnez l’option Général (compact) pour les domaines pendant la création du projet Custom Vision

Une fois que vous avez terminé, vous pouvez exporter le modèle vers un conteneur Docker à l’aide du bouton **Exporter** sous l’onglet **Performances**. Vérifiez que vous avez choisi Linux comme type de plateforme de conteneur. Il s’agit de la plateforme sur laquelle le conteneur s’exécutera. L’ordinateur sur lequel vous téléchargez le conteneur peut fonctionner sous Windows ou sous Linux. Les instructions qui suivent se basent sur le fichier conteneur téléchargé sur un ordinateur Windows.

![Capture d’écran montrant le fichier Dockerfile sélectionné.](./media/custom-vision/docker-file.png)

1. Vous devez trouver un fichier zip nommé `<projectname>.DockerFile.Linux.zip` sur votre ordinateur local.
2. Vérifiez que Docker est installé. Si ce n’est pas le cas, installez [Docker](https://docs.docker.com/get-docker/) sur votre poste de travail Windows.
3. Décompressez le fichier téléchargé à l’emplacement de votre choix. Utilisez la ligne de commande pour accéder au répertoire du dossier décompressé. Vous devez voir les deux fichiers suivants : app\labels.txt et app\model.pb
4. Clonez le [dépôt Video Analyzer](https://github.com/Azure/video-analyzer) et utilisez la ligne de commande pour accéder au dossier edge-modules\extensions\customvision\avaextension
5. Copiez les fichiers labels.txt et model.pb de l’étape 3 dans le dossier edge-modules\extensions\customvision\avaextension. Dans le même dossier :

   Exécutez les commandes suivantes :

   1. `docker build -t cvtruck .`

      Cette commande télécharge de nombreux packages, génère l’image Docker, et l’étiquette ainsi : `cvtruck:latest`.

      > [!NOTE]
      > Si l’opération réussit, vous devez voir les messages suivants : `Successfully built <docker image id>` et `Successfully tagged cvtruck:latest`. Si la commande de génération échoue, réessayez. Il arrive que les packages de dépendances ne puissent pas être téléchargés la première fois.
   2. `docker image ls`

      Cette commande vérifie si la nouvelle image se trouve dans votre registre local.
   
## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

1. Dans Visual Studio Code, accédez à src/cloud-to-device-console-app/operations.json.
2. Sous `pipelineTopologySet`, vérifiez que ce qui suit est vrai :<br/>
   `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtension/topology.json"`
3. Sous `livePipelineSet`, vérifiez que ce qui suit est vrai :

   1. `"topologyName" : "InferencingWithHttpExtension"`
   2. Ajoutez ce qui suit en haut du tableau des paramètres : `{"name": "inferencingUrl","value": "http://cv/score"},`
   3. Remplacez la valeur du paramètre `rtspUrl` par la valeur `"rtsp://rtspsim:554/media/t2.mkv"`.
4. Sous `pipelineTopologyDelete`, vérifiez que `"name": "InferencingWithHttpExtension"` est vrai.
5. Cliquez avec le bouton droit sur le fichier deployment.customvision.template.json du dossier src/edge/, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**.

   ![Capture d’écran montrant l’option Générer un manifeste de déploiement IoT Edge](./media/custom-vision/deployment-template-json.png)

   Cette action doit créer un fichier manifeste nommé deployment.customvision.amd64.json sous src/edge/config.
6. Ouvrez le fichier deployment.customvision.template.json sous src/edge/, puis recherchez le bloc JSON `registryCredentials`. Dans ce bloc, vous trouverez l’adresse de votre registre de conteneurs Azure, ainsi que son nom d’utilisateur et son mot de passe.
7. Envoyez (push) le conteneur Custom Vision local dans votre instance Azure Container Registry en effectuant les étapes suivantes sur la ligne de commande :

   1. Connectez-vous au registre en exécutant la commande suivante :

      `docker login <address>`

      Entrez le nom d’utilisateur et le mot de passe lorsqu’ils vous sont demandés pour l’authentification.

      > [!NOTE]
      > Le mot de passe ne s’affiche pas sur la ligne de commande.
   2. Étiquetez votre image à l’aide de cette commande :<br/>
      `docker tag cvtruck <address>/cvtruck`.
   3. Envoyez (push) votre image à l’aide de cette commande :<br/>
      `docker push <address>/cvtruck`.

      Si l’envoi réussit, `Pushed` doit s’afficher sur la ligne de commande avec l’algorithme SHA pour l’image.
   4. Vous pouvez également vérifier que l’envoi a réussi en accédant à votre instance Azure Container Registry sur le portail Azure. À cet endroit, le nom du dépôt et l’étiquette s’afficheront.
8. Définissez la chaîne de connexion IoT Hub en sélectionnant l’icône **Autres actions** en regard du volet **AZURE IOT HUB** dans le coin inférieur gauche. Vous pouvez copier la chaîne à partir du fichier appsettings.json. (Pour être sûr que le bon hub IoT est configuré dans Visual Studio Code, vous pouvez également utiliser la [commande Sélectionner un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub))

   ![Capture d’écran montrant la chaîne de connexion Définir un hub IoT.](./media/custom-vision/connection-string.png)
9. Ensuite, cliquez avec le bouton de droite sur src/edge/config/deployment.customvision.amd64.json, puis sélectionnez **Créer un déploiement pour un seul appareil**.

   ![Capture d’écran montrant l’option Créer un déploiement pour un seul appareil.](./media/custom-vision/deployment-amd64-json.png)
10. Vous êtes ensuite invité à sélectionner un appareil IoT Hub. Sélectionnez **ava-sample-iot-edge-device** dans la liste déroulante.
11. Après environ 30 secondes, actualisez Azure IoT Hub dans la section en bas à gauche. Les modules suivants doivent être déployés sur l’appareil de périphérie :

    - Hub Edge (nom de module **edgeHub**)
    - Agent Edge (nom de module **edgeAgent**)
    - Video Analyzer (nom de module **avaedge**)
    - Simulateur RTSP (nom du module **rtspsim**, qui simule un serveur RTSP faisant office de source d’un flux vidéo en direct)
    - Custom Vision (module nommé **cv**, basé sur le modèle de détection de camion jouet)

À partir de ces étapes, le module Custom Vision a maintenant été ajouté. 

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

Si vous ouvrez la topologie de ce tutoriel dans un navigateur, vous voyez que la valeur de `inferencingUrl` a été définie sur `http://cv/score`. Ce paramètre signifie que le serveur d’inférence retourne les résultats après avoir détecté des camions jouets dans la vidéo en direct.

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou sélectionnez **Ctrl+Maj+X**) et recherchez Azure IoT Hub.
2. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

   ![Capture d’écran montrant les paramètres d’extension](./media/custom-vision/extensions-tab.png)
3. Recherchez et activez **Afficher le message détaillé**.

   ![Capture d’écran montrant l’option Afficher le message détaillé.](./media/custom-vision/show-verbose-message.png)
4.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end  

5. Le code operations.json commence par appeler les méthodes directes `livePipelineList` et `livePipelineList`. Si vous avez nettoyé les ressources après avoir suivi les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Pour continuer, sélectionnez la touche **Entrée**.

   La fenêtre **TERMINAL** affiche le jeu d’appels de méthode directe suivant :

   - Un appel à `pipelineTopologySet` qui utilise le `pipelineTopologyUrl` précédent.
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
                "name": "inferencingUrl",
                "value": "http://cv/score"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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

   - Un appel à `livePipelineActivate` qui active le pipeline et le flux vidéo.
   - Un deuxième appel à `livePipelineList` qui montre que le pipeline est actif.

6. La sortie de la fenêtre **TERMINAL** s’interrompt avec l’invite **Appuyez sur Entrée pour continuer**. Ne sélectionnez pas encore **Entrée**. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
7. Basculez vers la fenêtre **SORTIE** de Visual Studio Code. Vous voyez des messages indiquant que le module IoT Edge Video Analyzer envoie des données au hub IoT. La section suivante de ce tutoriel décrit ces messages.
8. Le pipeline continue à s’exécuter et imprime des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter le pipeline, retournez à la fenêtre **TERMINAL** et sélectionnez **Entrée**. La série d’appels suivante nettoie les ressources :

   - Un appel à `livePipelineDeactivate` désactive le pipeline.
   - Un appel à `livePipelineDelete` supprime le pipeline.
   - Un appel à `pipelineTopologyDelete` supprime la topologie.
   - Un dernier appel à `pipelineTopologyList` indique que la liste est vide.

## <a name="interpret-the-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline, les résultats du nœud de processeur d’extension HTTP sont passés au hub IoT par le nœud récepteur des messages IoT Hub. Les messages qui s’affichent dans la fenêtre **SORTIE** contiennent une section Body et une section `applicationProperties`. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module Video Analyzer définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand un pipeline est instancié, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché.


```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
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

- Le message est un événement de diagnostic. `MediaSessionEstablished` indique que le nœud source RTSP (l’objet) s’est connecté au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
- Dans `properties`, `subject` indique que le message a été généré à partir du nœud source RTSP dans le pipeline.
- Dans `properties`, le type d’événement indique que cet événement est un événement de diagnostic.
- L’heure de l’événement indique l’heure à laquelle l’événement s’est produit.
- Le corps contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du [protocole SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Événement d'inférence

Le nœud de processeur d’extension HTTP reçoit les résultats d’inférence du conteneur Custom Vision et les envoie via le nœud récepteur de messages IoT Hub sous forme d’événements d’inférence.


```
{
  "body": {
    "timestamp": 145892470449324,
    "inferences": [
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.20541823
          },
          "box": {
            "l": 0.6826309,
            "t": -0.01415127,
            "w": 0.3135161,
            "h": 0.94683206
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.14967085
          },
          "box": {
            "l": 0.33310884,
            "t": 0.03174839,
            "w": 0.13532706,
            "h": 0.54967254
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.1352181
          },
          "box": {
            "l": 0.48884687,
            "t": 0.44746214,
            "w": 0.025887,
            "h": 0.05414263
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/...",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/httpExtension",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-14T21:24:09.436Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637563926153483223",
    "iothub-enqueuedtime": 1621027452077,
    "iothub-message-source": "Telemetry",
    "messageId": "96f7f0b5-728d-4e3e-a7bb-4e3198c58726",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
```

Notez les informations suivantes dans les messages précédents :

- Dans `properties`, l’objet référence le nœud dans le pipeline à partir duquel le message a été généré. Dans ce cas, le message provient du processeur d’extension HTTP.
- Dans `properties`, le type d’événement indique qu’il s’agit d’un événement d’inférence d’analytique.
- L’heure de l’événement indique l’heure à laquelle l’événement s’est produit.
- Le corps contient des données relatives à l’événement d’analytique. Dans ce cas, l’événement est un événement d’inférence. Le corps contient donc un tableau d’inférences appelées « prédictions ».
- La section des inférences contient la liste des prédictions selon lesquelles un camion de livraison jouet (étiquette « delivery truck ») est trouvé dans l’image. Comme vous le savez, « delivery truck » (camion de livraison) est l’étiquette personnalisée que vous avez fournie à votre modèle entraîné personnalisé pour le camion jouet. Le modèle inférence et identifie le camion jouet dans la vidéo d’entrée avec différents scores de probabilité.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres tutoriels ou guides de démarrage rapide, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure et à vos groupes de ressources, sélectionnez le groupe de ressources sous lequel vous avez exécuté ce tutoriel, puis supprimez le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

- Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez rechercher les caméras IP qui prennent RTSP en charge dans la page des produits [conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
- Utilisez un appareil Linux AMD64 ou x64 plutôt qu’une machine virtuelle Linux Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Vous pouvez suivre les instructions mentionnées dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md).

Inscrivez ensuite l’appareil auprès d’Azure IoT Hub en suivant les instructions mentionnées dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../../iot-edge/quickstart-linux.md).  
