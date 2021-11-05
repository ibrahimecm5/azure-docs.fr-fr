---
title: Tutoriel sur l’enregistrement et la lecture vidéo en continu - Azure Video Analyzer
description: Dans ce tutoriel, vous allez voir comment utiliser Azure Video Analyzer pour enregistrer des vidéos en continu dans le cloud et comment lire les enregistrements.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6a86dac6b242139d406dd6513a70846c57fa1c05
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559349"
---
# <a name="tutorial-continuous-video-recording-and-playback"></a>Tutoriel : Enregistrement et lecture vidéo en continu

[!INCLUDE [header](includes/edge-env.md)]

Dans ce tutoriel, vous allez voir comment utiliser Azure Video Analyzer pour réaliser des [enregistrements vidéo en continu](../continuous-video-recording.md) dans le cloud et comment lire ces enregistrements. Cette fonctionnalité est utile dans des scénarios tels que la sécurité et la conformité où il est nécessaire de conserver une archive des images d’une caméra pendant des jours, des semaines, des mois, voire des années. Vous pouvez également spécifier la durée de conservation de la vidéo en cours d’enregistrement. La stratégie de conservation définit le nombre de jours pendant lesquels la vidéo doit être stockée (par exemple, les 7 derniers jours). Pour en savoir plus, consultez l’article [Gérer la stratégie de conservation](../manage-retention-policy.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer les ressources appropriées.
> * Examiner le code qui effectue l’enregistrement CVR.
> * Exécuter l’exemple de code.
> * Examiner les résultats et regarder la vidéo.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée  

Lisez ces articles avant de commencer :

* [Vue d’ensemble de Video Analyzer](../overview.md)
* [Terminologie de Video Analyzer](../terminology.md)
* [Concepts relatifs aux pipelines Azure Video Analyzer](../pipeline.md) 
* [Scénarios d’enregistrement vidéo en continu](../continuous-video-recording.md)

## <a name="prerequisites"></a>Prérequis

Les prérequis pour ce tutoriel sont les suivants : [!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Effectuer le déploiement dans Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="concepts"></a>Concepts

Comme l’explique [cet article](../pipeline.md), un pipeline Video Analyzer permet de définir les éléments suivants :

- Emplacement à partir duquel les médias doivent être capturés.
- Mode de traitement des médias.
- Emplacement auquel remettre les résultats. 
 
 Pour effectuer un enregistrement vidéo en continu, vous devez capturer la vidéo à partir d’une caméra compatible RTSP et l’enregistrer en continu dans une [ressource vidéo](../terminology.md#video). Ce diagramme montre une représentation graphique de ce pipeline. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="../media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Pipeline Azure Video Analyzer pour l’enregistrement vidéo en continu":::
<!-- ./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg -->

Dans ce tutoriel, vous allez utiliser un module périphérique créé avec le [serveur multimédia Live555](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) pour simuler une caméra RTSP. Dans le pipeline, vous allez utiliser un nœud [source RTSP](../pipeline.md#rtsp-source) pour obtenir le flux en direct, puis envoyer cette vidéo au [nœud récepteur vidéo](../pipeline.md#video-sink) qui enregistrera la vidéo dans votre compte Video Analyzer. La vidéo utilisée dans ce tutoriel est un [exemple de vidéo d’intersection d’autoroute](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Ce didacticiel présente les procédures suivantes :

1. Configurer votre environnement de développement.
1. Déployer les modules périphériques nécessaires.
1. Créer et déployer le pipeline en direct.
1. interpréter les résultats ;
1. Supprimer des ressources.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement


### <a name="get-the-sample-code"></a>Obtenir l’exemple de code

1. Clonez le [dépôt des exemples C# AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp).
1. Démarrez Visual Studio Code et ouvrez le dossier où a été téléchargé le dépôt.
1. Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app et créez un fichier nommé **appsettings.json**. Ce fichier contient les paramètres nécessaires à l’exécution du programme.
1. Accédez au partage de fichiers dans le compte de stockage créé à l’étape de configuration précédente et localisez le fichier **appsettings.json** sous le partage de fichiers « deployment-output ». Cliquez sur le fichier, puis appuyez sur le bouton « Télécharger ». Le contenu doit s’ouvrir sous un nouvel onglet de navigateur, qui ressemble à celui-ci :

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   La chaîne de connexion IoT Hub vous permet d’utiliser Visual Studio Code pour envoyer des commandes aux modules de périphérie par le biais d’Azure IoT Hub. Copiez le JSON ci-dessus dans le fichier **src/cloud-to-device-console-app/appsettings.json**.

### <a name="connect-to-the-iot-hub"></a>Se connecter au hub IoT

1. Dans Visual Studio Code, définissez la chaîne de connexion IoT Hub en sélectionnant l’icône **Plus d’actions** à côté du volet **AZURE IOT HUB** en bas à gauche. Copiez la chaîne à partir du fichier src/cloud-to-device-console-app/appsettings.json.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]
1. Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche. Vous devez voir l’appareil de périphérie `avasample-iot-edge-device`, dont les modules suivants sont normalement déployés :
    - Hub Edge (nom de module **edgeHub**)
    - Agent Edge (nom de module **edgeAgent**)
    - Video Analyzer (nom de module **avaedge**)
    - Simulateur RTSP (nom du module **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules

Quand vous exécutez ce guide de démarrage rapide ou ce tutoriel, les événements sont envoyés au hub IoT. Pour voir ces événements, effectuez les étapes suivantes :

1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez **Azure IoT Hub** dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton droit sur `avasample-iot-edge-device`, puis sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**.

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="examine-the-sample-files"></a>Examiner les exemples de fichiers

Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

* **c2d-console-app.csproj** : Fichier projet pour Visual Studio Code.
* **operations.json** : Ce fichier liste les différentes opérations que vous allez exécuter.
* **Program.cs** : Exemple de code de programme :
    * Il charge les paramètres de l’application.
    * Appelle des méthodes directes exposées par le module périphérique Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en appelant ses [méthodes directes](direct-methods.md).
    * Il s’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et les événements générés par le module dans la fenêtre **SORTIE**.
    * Il appelle des méthodes directes pour nettoyer des ressources.

## <a name="run-the-program"></a>Exécuter le programme 

1. Dans Visual Studio Code, accédez à src/cloud-to-device-console-app/operations.json.
1. Sous le nœud **pipelineTopologySet**, modifiez les points suivants :

    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json" `
1. Ensuite, sous les nœuds **livePipelineSet** et **pipelineTopologyDelete**, vérifiez que la valeur de **topologyName** correspond à celle de la propriété **name** dans la topologie de pipeline précédente :

    `"topologyName" : "CVRToVideoSink"`  
1. Ouvrez la [topologie de pipeline](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json) dans un navigateur, puis examinez la valeur de videoName ; vous verrez qu’elle est codée en dur dans `sample-cvr-video`. C’est acceptable dans un tutoriel. En production, vous devrez faire en sorte que chaque caméra RTSP soit enregistrée dans une ressource vidéo sous un nom unique.
1. Démarrez une session de débogage en appuyant sur F5. Quelques messages s’affichent dans la fenêtre **TERMINAL**.
1. Le fichier operations.json commence par appeler `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé des ressources à l’issue de démarrages rapides ou de tutoriels, cette action renvoie des listes vides, comme illustré ci-dessous :

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

    ```

1. Ensuite, l’ensemble suivant d’appels de méthode directe est passé :
   * Un appel à `pipelineTopologySet` à l’aide du `topologyUrl` précédent
   * Un appel à `livePipelineSet` à l’aide du corps suivant
     
     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "CVRToVideoSink",
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
   * Un appel à `livePipelineActivate` pour démarrer le pipeline en direct et démarrer le flux de la vidéo, puis une pause qui vous permet de sélectionner **Entrée** dans la fenêtre **Terminal**
1. La sortie de la fenêtre **TERMINAL** s’interrompt maintenant à l’invite **Appuyez sur Entrée pour continuer**. Ne sélectionnez pas **Entrée** pour l’instant. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez invoquées.
1. Si vous basculez maintenant vers la fenêtre **OUTPUT** de Visual Studio Code, vous allez voir les messages qui sont envoyés à IoT Hub par le module périphérique Video Analyzer. Ces messages sont abordés dans la section qui suit.
1. Le pipeline en direct continue à s’exécuter et à enregistrer la vidéo. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter l’enregistrement, revenez à la fenêtre **TERMINAL** et sélectionnez **Entrée**. La série d’appels suivante permet de nettoyer les ressources en utilisant :

   * Un appel à `livePipelineDeactivate` pour désactiver le pipeline en direct.
   * Un appel à `livePipelineDelete` pour supprimer le pipeline en direct.
   * Un deuxième appel à `livePipelineList` pour montrer que le pipeline en direct est en cours d’exécution.
   * Un appel à `pipelineTopologyDelete` pour supprimer la topologie.
   * Un dernier appel à `pipelineTopologyList` pour montrer que la liste est maintenant vide.

## <a name="interpret-the-results"></a>Interpréter les résultats

Quand vous exécutez le pipeline en direct, le module périphérique Video Analyzer envoie certains événements opérationnels et de diagnostic au hub IoT Edge. Ces événements sont les messages que vous voyez dans la fenêtre **SORTIE** de Visual Studio Code. Ils contiennent une section `body` et une section `applicationProperties`. Pour comprendre ce que ces sections représentent, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, les propriétés de l’application et le contenu du corps sont définis par le module périphérique Video Analyzer.


## <a name="diagnostics-events"></a>Événements de diagnostic 

### <a name="mediasession-established-event"></a>Événement MediaSession Established

Lorsque le pipeline est activé, le nœud source RTSP tente de se connecter au serveur RTSP exécuté dans le module rtspsim. En cas de réussite, il imprime cet événement :

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

* Le message est un événement de diagnostic (**Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished**). Il indique que le nœud source RTSP (l’objet) a établi une connexion au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
* La section subject (l’objet) dans applicationProperties référence le nœud dans la topologie de pipeline à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* La section eventType dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* La section eventTime indique l’heure à laquelle l’événement s’est produit.
* La section body contient des données relatives à l’événement de diagnostic, qui, dans ce cas, correspondent aux détails [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Événements opérationnels 

### <a name="recordingstarted-event"></a>Événement RecordingStarted

Lorsque le nœud récepteur vidéo commence à enregistrer la vidéo, il émet cet événement de type **Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted** :

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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

La section subject dans applicationProperties référence le nœud récepteur vidéo dans le pipeline en direct qui a généré ce message.

La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingavailable-event"></a>Événement RecordingAvailable

Comme son nom l’indique, l’événement **RecordingStarted** est envoyé lorsque l’enregistrement démarre. Il est cependant possible que les données vidéo ne soient pas encore chargées dans la ressource vidéo. Lorsque le nœud récepteur vidéo a chargé une vidéo, il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** :

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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

La section subject dans applicationProperties fait référence au nœud récepteur vidéo dans le pipeline en direct qui a généré ce message.

La section body contient des informations sur l’emplacement de sortie. Dans ce cas, il s’agit du nom de la ressource Video Analyzer dans laquelle la vidéo est enregistrée.

### <a name="recordingstopped-event"></a>Événement RecordingStopped

Quand vous désactivez le pipeline en direct, le nœud récepteur vidéo cesse d’enregistrer le média. Il émet un événement de type **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** :

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
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

La section body contient des informations sur l’emplacement de sortie, qui est dans ce cas le nom de la ressource Video Analyzer dans lequel la vidéo est enregistrée.

## <a name="playing-back-the-recording"></a>Lecture de l’enregistrement

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources que vous avez dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la section **Video Analyzer**.
1. Vous trouverez une vidéo portant le nom de `sample-cvr-video`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. La page de détails de la vidéo s’ouvre et la lecture démarre automatiquement.

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) avec prise en charge de RTSP au lieu d’utiliser le simulateur RTSP. Vous pouvez consulter les caméras IP prenant RTSP en charge dans la [page de produits conformes ONVIF](https://www.onvif.org/conformant-products/) en recherchant les appareils conformes aux profils G, S ou T.
* Utilisez un périphérique Linux AMD64 ou x64 (au lieu d’utiliser une machine virtuelle Linux Azure). Cet appareil doit se trouver dans le même réseau que la caméra IP. Suivez les instructions données dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md). Suivez ensuite les instructions données dans le démarrage rapide [Déployer votre premier module IoT Edge sur un appareil Linux virtuel](../../../iot-edge/quickstart-linux.md) pour inscrire l’appareil auprès d’Azure IoT Hub.
