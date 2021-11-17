---
title: Détecter les mouvements et enregistrer des vidéos sur des périphériques – Azure
description: Utilisez Azure Video Analyzer pour analyser le flux vidéo en direct provenant d’une caméra IP (simulée). Il montre comment détecter la présence d’un mouvement et, le cas échéant, enregistrer un clip vidéo MP4 sur le système de fichiers local de l’appareil de périphérie. Le guide de démarrage rapide utilise une machine virtuelle Azure comme appareil IoT Edge ainsi qu’un flux vidéo en direct simulé.
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 84dbe38e69b9dea259c570ef229927b75aefbe23
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493417"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Démarrage rapide : Détecter les mouvements et enregistrer des vidéos sur des appareils de périphérie

[!INCLUDE [header](includes/edge-env.md)]

Ce guide de démarrage rapide vous montre comment utiliser Azure Video Analyzer pour analyser le flux vidéo en direct provenant d’une caméra IP (simulée). Il montre comment détecter la présence d’un mouvement et, le cas échéant, enregistrer un clip vidéo MP4 sur le système de fichiers local de l’appareil de périphérie. Le guide de démarrage rapide utilise une machine virtuelle Azure comme appareil IoT Edge ainsi qu’un flux vidéo en direct simulé.

## <a name="prerequisites"></a>Prérequis

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Quand vous configurez les ressources Azure pour ce guide de démarrage rapide, une courte vidéo d’une aire de stationnement est copiée dans Azure sur la machine virtuelle Linux qui est utilisée comme appareil IoT Edge. Ce fichier vidéo permet de simuler un flux en direct pour ce tutoriel.

Ouvrez une application comme le [lecteur multimédia VLC](https://www.videolan.org/vlc/), sélectionnez Ctrl+N, puis collez [ce lien](https://avamedia.blob.core.windows.net/public/lots_015.mkv) dans la vidéo de l’aire de stationnement pour commencer la lecture. Au repère d’approximativement 5 secondes, une voiture blanche traverse l’aire de stationnement.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Effectuez les étapes suivantes pour utiliser Video Analyzer afin de détecter le mouvement de la voiture et enregistrer un clip vidéo commençant aux alentours du repère des 5 secondes.

## <a name="examine-and-edit-the-sample-files"></a>Examiner et modifier les exemples de fichiers

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Exécuter l'exemple de programme

1. Dans Visual Studio Code, ouvrez l’onglet Extensions (ou appuyez sur Ctrl+Maj+X), puis recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez Paramètres d’extension.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text= "Extension settings":::

1. Recherchez et activez « Afficher le message détaillé ».

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text= "Show Verbose Message":::

1.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. Le code _operations.json_ appelle les méthodes directes `pipelineTopologyList` et `livePipelineList`. Si vous avez nettoyé les ressources après les guides de démarrage rapide précédents, ce processus retourne des listes vides, puis s’interrompt. Appuyez sur la touche Entrée.

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

   La fenêtre TERMINAL affiche le jeu d’appels de méthode directe suivant :

   - Un appel à `pipelineTopologySet` qui utilise l’élément pipelineTopologyUrl
   - Un appel à `livePipelineSet` qui utilise le corps suivant :

     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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

   - Un appel à `livePipelineActivate` qui démarre le pipeline en direct et le flux vidéo.
   - Un deuxième appel à `livePipelineList` qui indique que le pipeline en direct est à l’état En cours d’exécution.

1. La sortie de la fenêtre TERMINAL s’interrompt à Appuyez sur Entrée pour continuer. Ne sélectionnez pas encore Entrée. Faites défiler vers le haut pour voir les charges utiles de réponse JSON pour les méthodes directes que vous avez appelées.
1. Basculez vers la fenêtre SORTIE de Visual Studio Code. Vous voyez des messages indiquant que le module périphérique Video Analyzer effectue des envois au hub IoT. La section suivante de ce guide de démarrage rapide décrit ces messages.
1. La topologie de pipeline continue de s’exécuter et affiche des résultats. Le simulateur RTSP continue de boucler la vidéo source. Pour arrêter la topologie de pipeline, retournez à la fenêtre TERMINAL, puis sélectionnez Entrée.

La série d’appels suivante nettoie les ressources :

- Un appel à `livePipelineDeactivate` désactive le pipeline en direct.
- Un appel à `livePipelineDelete` supprime le pipeline en direct.
- Un appel à `pipelineTopologyDelete` supprime la topologie.
- Un dernier appel à `pipelineTopologyList` indique que la liste est maintenant vide.

## <a name="interpret-results"></a>Interpréter les résultats

Quand vous exécutez la topologie de pipeline, les résultats du nœud processeur de détection de mouvement passe par le nœud récepteur IoT Hub pour atteindre le hub IoT. Les messages qui s’affichent dans la fenêtre SORTIE de Visual Studio Code contiennent une section body et une section applicationProperties. Pour plus d’informations, consultez [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).

Dans les messages suivants, le module périphérique Video Analyzer définit les propriétés de l’application et le contenu du corps.

### <a name="mediasessionestablished-event"></a>Événement MediaSessionEstablished

Quand une topologie de pipeline est instanciée, le nœud source RTSP tente de se connecter au serveur RTSP qui s’exécute sur le conteneur rtspsim-live555. Si la connexion réussit, l’événement suivant est affiché.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
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

Dans la sortie précédente :

- Le message est un événement de diagnostic, MediaSessionEstablished. Il indique que le nœud source RTSP (l’objet) a établi une connexion au simulateur RTSP et a commencé à recevoir un flux en direct (simulé).
- Dans applicationProperties, subject fait référence au nœud de la topologie de pipeline à partir de laquelle le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
- Dans applicationProperties, « eventType » indique qu’il s’agit d’un événement de diagnostic.
- La valeur eventTime correspond à l’heure à laquelle l’événement s’est produit.
- La section body contient des données relatives à l’événement de diagnostic. Dans ce cas, les données comprennent les détails du protocole SDP.

### <a name="recordingstarted-event"></a>Événement RecordingStarted

Quand un mouvement est détecté, le nœud processeur de porte de signal est activé et le nœud récepteur de fichiers de la topologie de pipeline commence à écrire un fichier MP4. Le nœud récepteur de fichiers envoie un événement opérationnel. Le type est défini sur motion pour indiquer qu’il s’agit d’un résultat du processeur de détection de mouvement. La valeur eventTime est l’heure UTC à laquelle le mouvement s’est produit. Pour plus d’informations sur ce processus, consultez la section [Vue d’ensemble](detect-motion-record-video-edge-devices.md#overview) de ce guide de démarrage rapide.

Voici un exemple de ce message :

```
[IoTHubMonitor] [05:37:27 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20210511T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-11T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Dans le message précédent :

- Dans applicationProperties, subject fait référence au nœud du pipeline à partir duquel le message a été généré. Dans ce cas, le message provient du nœud récepteur de fichiers.
- Dans applicationProperties, eventType indique que cet événement est opérationnel.
- La valeur eventTime correspond à l’heure à laquelle l’événement s’est produit. Cette heure est postérieure de 5 à 6 secondes à MediaSessionEstablished et au début du flux vidéo. Cette heure correspond au repère des 5 à 6 secondes quand la [voiture a commencé à se déplacer](#review-the-sample-video) dans l’aire de stationnement.
- La section body contient des données sur l’événement opérationnel. Dans ce cas, les données comprennent outputType et outputLocation.
- La variable outputType indique que ces informations portent sur le chemin du fichier.
- La valeur outputLocation est l’emplacement du fichier MP4 dans le module périphérique.

### <a name="recordingstopped-and-recordingavailable-events"></a>Événements RecordingStopped et RecordingAvailable

Si vous examinez les propriétés du nœud processeur de porte de signal dans la [topologie de pipeline](../pipeline.md), vous remarquez que les durées d’activation sont définies sur 5 secondes. Ainsi, 5 secondes environ après la réception de l’événement **RecordingStarted**, vous obtenez :

- Un événement **RecordingStopped**, indiquant que l’enregistrement a été arrêté.
- Un événement **RecordingAvailable**, indiquant que le fichier MP4 peut maintenant être visualisé.

Les deux événements sont généralement émis à quelques secondes d’intervalle.

## <a name="play-the-mp4-clip"></a>Lire le clip MP4

Les fichiers MP4 sont écrits dans un répertoire sur l’appareil de périphérie que vous avez configuré dans le fichier .env à l’aide de la clé VIDEO_OUTPUT_FOLDER_ON_DEVICE. Si vous avez utilisé la valeur par défaut, les résultats doivent se trouver dans le dossier /var/media/ .

Pour lire le clip MP4 :

1. Accédez à votre groupe de ressources, recherchez la machine virtuelle, puis connectez-vous à celle-ci.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/detect-motion-record-video-edge-devices/sample-iot-edge-device.png" alt-text= "Edge device":::

1. Connectez-vous à l’aide des informations d’identification qui ont été générées quand vous avez configuré vos ressources Azure.
1. À l’invite de commandes, accédez au répertoire approprié. L'emplacement par défaut est /var/media. Vous devez voir les fichiers MP4 présents dans le répertoire.

1. Utilisez [Secure Copy (SCP)](../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) pour copier les fichiers sur votre ordinateur local.
1. Lisez les fichiers en utilisant le [lecteur multimédia VLC](https://www.videolan.org/vlc/) ou tout autre lecteur MP4.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer les autres guides de démarrage rapide, conservez les ressources que vous avez créées. Dans le cas contraire, dans le portail Azure, accédez à vos groupes de ressources, sélectionnez le groupe de ressources où vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

- Suivez le guide de démarrage rapide [Exécuter Azure Video Analyzer avec votre propre modèle](analyze-live-video-use-your-model-http.md) pour appliquer l’intelligence artificielle à des flux vidéo en direct.
- Passez en revue les défis supplémentaires pour les utilisateurs expérimentés :

  - Utilisez une [caméra IP](https://en.wikipedia.org/wiki/IP_camera) qui prend en charge RTSP au lieu d’utiliser le simulateur RTSP. Vous trouverez les caméras IP qui prennent RTSP en charge dans la page des [produits conformes ONVIF](https://www.onvif.org/conformant-products/). Recherchez les appareils conformes aux profils G, S ou T.
  - Utilisez un appareil Linux AMD64 ou x64 au lieu d’utiliser une machine virtuelle Linux dans Azure. Cet appareil doit se trouver dans le même réseau que la caméra IP. Suivez les instructions données dans [Installer le runtime Azure IoT Edge sur Linux](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true). Suivez ensuite les instructions dans [Déployer votre premier module IoT Edge sur un appareil virtuel Linux](../../../iot-edge/quickstart-linux.md?view=iotedge-2020-11&preserve-view=true) pour inscrire l’appareil auprès d’Azure IoT Hub.
