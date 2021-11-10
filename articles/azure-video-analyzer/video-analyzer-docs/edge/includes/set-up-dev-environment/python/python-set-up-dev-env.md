---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: 50bc0dc30b14685747e24afc7bda2964e8fd2d37
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860768"
---
### <a name="get-the-sample-code"></a>Obtenir l’exemple de code

1. Clonez le [dépôt d’exemples Python AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-python).
1. Démarrez Visual Studio Code et ouvrez le dossier où le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app et créez un fichier nommé **appsettings.json**. Ce fichier contient les paramètres nécessaires à l’exécution du programme.
1. Accédez au partage de fichiers dans le compte de stockage créé à l’étape de configuration précédente et localisez le fichier **appsettings.json** sous le partage de fichiers « deployment-output ». Cliquez sur le fichier, puis appuyez sur le bouton « Télécharger ». Le contenu doit s’ouvrir sous un nouvel onglet de navigateur, qui ressemble à celui-ci :

   ```json
   {
     "IoThubConnectionString": "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
     "deviceId": "avasample-iot-edge-device",
     "moduleId": "avaedge"
   }
   ```

   La chaîne de connexion IoT Hub vous permet d’utiliser Visual Studio Code pour envoyer des commandes aux modules de périphérie par le biais d’Azure IoT Hub. Copiez le JSON ci-dessus dans le fichier **src/cloud-to-device-console-app/appsettings.json**.
1. Ensuite, accédez au dossier src/edge et créez un fichier nommé **.env**. Ce fichier contient les propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil périphérique.
1. Accédez au partage de fichiers dans le compte de stockage créé à l’étape de configuration précédente et localisez le fichier **env.txt** sous le partage de fichiers « deployment-output ». Cliquez sur le fichier, puis appuyez sur le bouton « Télécharger ». Le contenu doit s’ouvrir sous un nouvel onglet de navigateur, qui ressemble à celui-ci :

   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

   Copiez le JSON de votre fichier **env.txt** dans le fichier **src/edge/.env**.

### <a name="connect-to-the-iot-hub"></a>Se connecter au hub IoT

1.  Dans Visual Studio Code, définissez la chaîne de connexion IoT Hub en sélectionnant l’icône **Plus d’actions** à côté du volet **AZURE IOT HUB** en bas à gauche. Copiez la chaîne à partir du fichier src/cloud-to-device-console-app/appsettings.json.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
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

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
