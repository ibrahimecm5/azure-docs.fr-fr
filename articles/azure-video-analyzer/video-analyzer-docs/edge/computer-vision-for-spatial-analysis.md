---
title: Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale - Azure
description: Ce tutoriel explique comment utiliser Azure Video Analyzer avec la fonctionnalité IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services dans le but d’analyser un flux vidéo en direct provenant d’une caméra IP (simulée).
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 341c3df126d0ff949c3ba40e5d679bb2e3147ff6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488636"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Tutoriel : Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale (préversion)

[!INCLUDE [header](includes/edge-env.md)]

Ce tutoriel explique comment utiliser Azure Video Analyzer avec le [service IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée). Vous y verrez comment ce serveur d’inférence vous permet d’analyser du contenu vidéo diffusé en continu pour comprendre les relations spatiales entre les personnes et leur déplacement dans l’espace physique. Un sous-ensemble d’images du flux vidéo est envoyé à ce serveur d’inférence, après quoi les résultats sont envoyés à IoT Edge Hub. Lorsque certaines conditions sont remplies, les clips vidéo sont enregistrés et stockés sous forme de vidéos dans le compte Video Analyzer.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> - Configurer des ressources
> - Examiner le code
> - Exécuter l’exemple de code
> - Surveiller les événements

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

Lisez ces articles avant de commencer :

- [Vue d’ensemble de Video Analyzer](../overview.md)
- [Terminologie de Video Analyzer](../terminology.md)
- [Concepts relatifs aux pipelines](../pipeline.md)
- [Enregistrement de vidéo basé sur les événements](record-event-based-live-video.md)
- [Conteneur Vision par ordinateur d’Azure Cognitive Services](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) pour l’analyse spatiale.

## <a name="prerequisites"></a>Prérequis

Vous trouverez ci-dessous les prérequis nécessaires pour connecter le module d’analyse spatiale au module Azure Video Analyzer.

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

   > [!Note]
   > Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671. Cette configuration permet à Azure IoT Tools de communiquer avec Azure IoT Hub.

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

1. **Choisir un appareil de calcul**  

    Pour exécuter le conteneur d’analyse spatiale, vous avez besoin d’un appareil de calcul avec une [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Nous vous recommandons d’utiliser **[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)** avec l’accélération GPU. Cependant, le conteneur peut s’exécuter sur n’importe quel autre **ordinateur de bureau** hôte ou **machine virtuelle Azure** sur lequel est installé [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/).

   #### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)

   Azure Stack Edge est une solution matérielle en tant que service ainsi qu’un appareil de computing en périphérie basé sur l’intelligence artificielle. Il est doté de fonctionnalités de transfert de données via le réseau. Pour obtenir des instructions détaillées sur la préparation et la configuration, consultez la [documentation Azure Stack Edge](../../../databox-online/azure-stack-edge-deploy-prep.md).

   #### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises

   - RAM système de 4 Go
   - 4 Go de RAM GPU
   - Processeur 8 cœurs
   - 1 GPU NVIDIA Tesla T4
   - 20 GB d’espace HDD

   #### <a name="recommended-hardware"></a>Matériel recommandé

   - RAM système de 32 Go
   - 16 Go de RAM GPU
   - Processeur 8 cœurs
   - 2 GPU NVIDIA Tesla T4
   - 50 Go d’espace SSD

   #### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)

   Vous pouvez utiliser une [machine virtuelle de série NC](../../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée d’un GPU K80.
        
1. **Configurer l’appareil périphérique**

    #### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)
    [Configurer le calcul sur le portail Azure Stack Edge](../../../cognitive-services/computer-vision/spatial-analysis-container.md#configure-compute-on-the-azure-stack-edge-portal)
    #### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)
    [Suivez ces instructions si votre ordinateur hôte n’est pas un appareil Azure Stack Edge.](../../../cognitive-services/computer-vision/spatial-analysis-container.md#install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer)
    #### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)
    1. [Créer la machine virtuelle](../../../cognitive-services/computer-vision/spatial-analysis-container.md?tabs=virtual-machine#create-the-vm) et installer les ancrages nécessaires sur la machine virtuelle

        > [!Important]
        > Veuillez **ignorer l’étape du manifeste de déploiement IoT** mentionnée dans ce document. Vous utiliserez notre propre fichier **[manifeste de déploiement](#configure-deployment-template)** pour déployer les conteneurs requis.

    1. Connectez-vous à votre machine virtuelle, puis, dans le terminal, tapez la commande suivante :
    ```bash
    bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"
    ```
    Le module Azure Video Analyzer s’exécute sur l’appareil de périphérie avec des comptes d’utilisateurs locaux non privilégiés. Il a aussi besoin de certains dossiers locaux pour le stockage des données de configuration d’application. Enfin, pour ce guide pratique, vous allez utiliser un [simulateur RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) qui relaie un flux vidéo en temps réel vers le module AVA à des fins d’analyse. Ce simulateur prend comme entrée des fichiers vidéo préenregistrés à partir d’un répertoire d’entrée. 
    
    Le script de préparation d’appareil utilisé ci-dessus permet d’automatiser ces tâches, ce qui vous permet de créer en une seule commande tous les dossiers d’entrée et de configuration pertinents, les fichiers d’entrée vidéo et les comptes d’utilisateur avec privilèges. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre appareil Edge. 
    
      * `/home/localedgeuser/samples`
      * `/home/localedgeuser/samples/input`
      * `/var/lib/videoanalyzer`
      * `/var/media`
    
     Notez la présence des fichiers vidéo (*.mkv) dans le dossier /home/localedgeuser/samples/input, qui servent de fichiers d’entrée à analyser.  

1. Ensuite, déployez les autres ressources Azure.

   [![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > Le bouton ci-dessus crée et utilise la machine virtuelle par défaut qui n’est pas dotée d’un GPU NVIDIA. Utilisez l’option « Utiliser un appareil périphérique existant » lorsque cela vous est demandé dans le modèle Azure Resource Manager (ARM), et utilisez les informations relatives à IoT Hub et à l’appareil de l’étape précédente.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="Utiliser un appareil existant":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="Vue d’ensemble de l’analyse spatiale":::

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](../pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud `CognitiveServicesVisionProcessor`.

Le nœud `CognitiveServicesVisionProcessor` joue un rôle de proxy. Il convertit les images vidéo dans le type d’image spécifié. Il relaie ensuite l’image via une **mémoire partagée** vers un autre module de périphérie qui exécute des opérations IA derrière un point de terminaison gRPC. Dans cet exemple, ce module de périphérie est le module d’analyse spatiale. Le nœud `CognitiveServicesVisionProcessor` effectue deux opérations :

- Il rassemble les résultats et publie les événements sur le nœud [récepteur IoT Hub](../pipeline.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).
- Il capture également un clip vidéo de 30 secondes à partir de la source RTSP en utilisant un [processeur de signaux](../pipeline.md#signal-gate-processor), et le stocke en tant que récepteur vidéo.

## <a name="create-the-computer-vision-resource"></a>Créer la ressource Vision par ordinateur

Vous devez créer une ressource Azure de type [Vision par ordinateur](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) pour le **niveau standard S1** sur le [Portail Azure](../../../iot-edge/how-to-deploy-modules-portal.md) ou via Azure CLI. 

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="configure-deployment-template"></a>Configurer un modèle de déploiement
#### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)
Recherchez le fichier de déploiement dans **/src/edge/deployment.spatialAnalysis.ase.template.json**. À partir du modèle, il existe un module `avaedge`, un module `rtspsim` et notre module `spatialanalysis`. 

Dans votre fichier de modèle de déploiement :

1. Le manifeste de déploiement utilise le port 50051 pour la communication entre `avaedge` et le module `spatialanalysis`. Si le port est utilisé par une autre application, définissez la liaison de port dans le module `spatialanalysis` sur un port ouvert.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `IpcMode` dans `avaedge` et le module createOptions `spatialanalysis` doivent être identiques et être définis sur **host**.
1. Pour que le simulateur RTSP fonctionne, vérifiez que le paramètre Volume Bounds est bien configuré lorsque vous utilisez l’appareil Azure Stack Edge.

   1. [Connectez-vous au partage SMB](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) et copiez l’[exemple de fichier vidéo retail shop](https://avamedia.blob.core.windows.net/public/retailshop-15fps.mkv) sur le partage local.

      > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMIPP]

   1. Vérifiez que le module rtspsim présente la configuration suivante :
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
#### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)
Recherchez le fichier de déploiement dans **/src/edge/deployment.spatialAnalysis.generic.template.json**. À partir du modèle, il existe un module `avaedge`, un module `rtspsim` et notre module `spatialanalysis`. 

#### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)
Recherchez le fichier de déploiement dans **/src/edge/deployment.spatialAnalysis.generic.template.json**. À partir du modèle, il existe un module `avaedge`, un module `rtspsim` et notre module `spatialanalysis`.

---

Le tableau suivant présente les différentes variables d’environnement utilisées par le module IoT Edge. Vous pouvez également les définir dans le manifeste de déploiement lié ci-dessus, à l’aide de l’attribut `env` dans `spatialanalysis` :

| Nom du paramètre | Valeur | Description|
|---------|---------|---------|
| DISPLAY | :1 | Cette valeur doit être identique à la sortie de `echo $DISPLAY` sur l’ordinateur hôte. Les appareils Azure Stack Edge n’ont pas d’affichage. Ce paramètre n'est pas applicable|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | **Ne modifiez pas**|
| ARCHON_LOG_LEVEL | Info; Verbose | Niveau de journalisation, sélectionnez l’une des deux valeurs suivantes :|
| QT_X11_NO_MITSHM | 1 | **Ne modifiez pas**|
| OMP_WAIT_POLICY | PASSIVE | **Ne modifiez pas**|
| CLUF | accepter | Cette valeur doit être définie sur *accepter* pour que le conteneur s’exécute |
| ARCHON_TELEMETRY_IOTHUB | true | Définissez cette valeur sur True pour envoyer les événements de télémétrie à IoT Hub |
| FACTURATION | votre URI de point de terminaison| Récupérez cette valeur dans le portail Azure à partir de votre ressource Vision par ordinateur. Vous la trouverez dans la section **Clé et point de terminaison** de votre ressource.|
| APIKEY | votre clé API| Récupérez cette valeur dans le portail Azure à partir de votre ressource Vision par ordinateur. Vous la trouverez dans la section **Clé et point de terminaison** de votre ressource. |
| LAUNCHER_TYPE | avaBackend | **Ne modifiez pas** |
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Ajoutez cette variable d’environnement si votre GPU n’est **pas** T4 or NVIDIA 2080 Ti.|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas. 

### <a name="gathering-keys-and-endpoint-uri"></a>Collecte des clés et URI de point de terminaison

Une clé est utilisée pour démarrer le conteneur d’analyse spatiale ; elle est disponible dans la page `Keys and Endpoint` de la ressource Vision par ordinateur sur le portail Azure. Accédez à cette page et recherchez la clé et l’URI de point de terminaison requis par le conteneur `spatialAnalysis`.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="URI de point de terminaison":::

## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

Le manifeste de déploiement définit les modules qui sont déployés sur un appareil de périphérie. Il définit également les paramètres de configuration de ces modules.

Effectuez les étapes suivantes pour générer le manifeste à partir du fichier de modèle, puis déployez-le sur l’appareil de périphérie.

1. Ouvrez Visual Studio Code.
1. À côté du volet `AZURE IOT HUB`, sélectionnez l’icône Autres actions pour définir la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier `src/cloud-to-device-console-app/appsettings.json`.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="Analyse spatiale : chaîne de connexion":::

1. Dans votre Explorateur de dossiers, cliquez avec le bouton droit sur votre fichier de modèle de déploiement, puis sélectionnez Générer le manifeste de déploiement IoT Edge.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="Analyse spatiale : fichier json amd64 de déploiement":::

   Cette action doit créer un fichier manifeste nommé dans le dossier **src/edge/config**.

1. Cliquez avec le bouton droit sur le fichier manifeste généré, sélectionnez **Créer un déploiement pour un seul appareil**, puis sélectionnez le nom de votre appareil périphérique.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="Analyse spatiale : Déployer sur un seul appareil":::

1. En haut de la page, vous êtes invité à sélectionner un appareil IoT Hub et à choisir le nom de votre appareil périphérique dans le menu déroulant.
1. Après environ 30 secondes, en bas à gauche de la fenêtre, actualisez le volet **AZURE IOT HUB**. L’appareil de périphérie affiche maintenant les modules déployés suivants :

   - Azure Video Analyzer (nom de module **avaedge**).
   - Simulateur RTSP (Real-Time Streaming Protocol) (nom de module **rtspsim**).
   - Analyse spatiale (nom de module **spatialanalysis**).

Une fois le déploiement réussi, un message s’affiche dans la fenêtre de sortie :

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

Vous pouvez ensuite rechercher les modules `avaedge`, `spatialanalysis`et `rtspsim` sous Appareils/Modules. Leur état doit être **running** (en cours d’exécution).

## <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Pour voir ces événements, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="Paramètres d’extension":::

1. Recherchez et activez « Afficher le message détaillé ».

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="Afficher le message détaillé":::

1. Ouvrez le volet Explorateur et recherchez **AZURE IOT HUB** en bas à gauche. Cliquez dessus avec le bouton droit puis sélectionnez Démarrer la supervision du point de terminaison d’événement intégré.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="Analyse spatiale : démarrer l’analyse":::

## <a name="run-the-program"></a>Exécuter le programme

Il existe un fichier program.cs qui appelle les méthodes directes dans `src/cloud-to-device-console-app/operations.json`. Vous devrez modifier le fichier `operations.json` et mettre à jour l’URL de topologie du pipeline, le nom de la topologie, ainsi que l’URL RTSP.

Dans operations.json :

- Définissez la topologie du pipeline de la façon suivante :

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json"
      }
  },
  ```
* Sous `livePipelineSet`, modifiez le nom de la topologie de manière à ce qu’il corresponde à la valeur figurant dans le lien ci-dessus précédent :
    * `"topologyName" : "PersonZoneCrossingTopology"`
* Sous `pipelineTopologyDelete`, modifiez le nom :
    * `"name" : "PersonZoneCrossingTopology"`

> [!Important]
> La topologie utilisée ci-dessus a un nom codé en dur pour la ressource VideoSink `videoSink`. Si vous décidez de choisir une autre source vidéo, n’oubliez pas de modifier cette valeur.

- Créez un pipeline en direct comme celui-ci, puis définissez les paramètres dans la topologie du pipeline ici :

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "PersonZoneCrossingTopology",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": "rtsp://rtspsim:554/media/retailshop-15fps.mkv"
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
  },
  ```

Exécutez une session de débogage en sélectionnant F5, puis suivez les instructions du **TERMINAL**. Cette opération permet de définir la topologie du pipeline, le pipeline en direct, d’activer le pipeline en direct, et enfin de supprimer les ressources.

> [!Note]
> Le programme s’interrompt à l’étape d’activation du pipeline en direct. Ouvrez l’onglet Terminal et appuyez sur **Entrée** pour continuer et démarrer les étapes de désactivation et de suppression dans les ressources.

## <a name="interpret-results"></a>Interpréter les résultats

`spatialanalysis` est un conteneur de grande taille qui peut mettre jusqu’à 30 secondes pour démarrer. Une fois que le conteneur spatialanalysis est opérationnel, il commence à envoyer les événements d’inférences. Vous verrez des événements tels que :

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/retailshop-15fps.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}

```
> [!NOTE]
> Vous verrez les messages **« initializing »** . Ces messages s’affichent lors du démarrage du module spatialAnalysis et peuvent prendre jusqu’à 60 secondes pour passer à l’état « running » (en cours d’exécution). Vous devez attendre un peu avant de voir l’événement d’inférence.

Lors de l’instanciation de la topologie de pipeline, vous devez voir l’événement « MediaSessionEstablished ». Voici un [exemple d’événement MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Le module spatialanalysis enverra également des événements Insights IA à Azure Video Analyzer, puis à IoT Hub, ce qui s’affichera dans la fenêtre **OUTPUT**. Ces Insights IA sont enregistrés en même temps que la vidéo via le nœud récepteur vidéo. Vous pouvez utiliser Video Analyzer pour les afficher, comme indiqué ci-dessous.

## <a name="supported-spatial-analysis-operations"></a>Opérations d’analyse spatiale prises en charge

Voici les opérations que le module `spatialAnalysis` propose et prises en charge par Azure Video Analyzer :

- **personZoneCrossing**
- **personCrossingLine**
- **personDistance**
- **personCount**
- **customOperation**


Pour en savoir plus sur les différentes opérations et les propriétés prises en charge, consultez notre documentation de référence sur les **[opérations d’analyse spatiale](../spatial-analysis-operations.md)** **prises en charge**.

## <a name="playing-back-the-recording"></a>Lecture de l’enregistrement

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources qui figurent dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la liste **Video Analyzer**.
1. Vous trouverez une vidéo portant le nom de `personzonecrossing`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. Dans la page d’informations de la vidéo, cliquez sur l’icône de **lecture**

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="Capture d’écran de la lecture de vidéo":::
   
1. Pour afficher les métadonnées d’inférence sur la vidéo, cliquez sur l’icône de **Affichage des métadonnées**
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="Icône d’affichage des métadonnées":::

    Trois options s’affichent en tant que superposition sur la vidéo :  
      - **Rectangles englobants** : affiche une zone englobante autour de chaque personne ayant un ID unique
      - **Attributs** : affiche les attributs de personne tels que sa vitesse (en pieds/s) et son orientation (à l’aide d’une flèche), le cas échéant
      - **Chemin d’accès à l’objet** : affiche un résumé pour chaque déplacement de chaque personne, le cas échéant

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback-bounding-boxes.png" alt-text="Capture d’écran de la lecture vidéo avec des zones englobantes":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Étapes suivantes

Essayez les différentes opérations proposées par le module `spatialAnalysis`. Pour cela, consultez les topologies de pipeline suivantes :

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)
