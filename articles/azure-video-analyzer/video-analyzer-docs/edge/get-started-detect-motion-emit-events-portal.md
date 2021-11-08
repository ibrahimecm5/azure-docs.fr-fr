---
title: Bien démarrer avec Azure Video Analyzer en utilisant le portail Azure - Azure
description: Ce guide de démarrage rapide vous aide à commencer à utiliser Azure Video Analyzer étape par étape en utilisant le Portail Azure.
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1dd1a5cdf68c60d4f5da75f324c163bc78dffa8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029148"
---
# <a name="quickstart-get-started-with-video-analyzer-in-the-portal"></a>Démarrage rapide : Prise en main de Video Analyzer sur le portail

[!INCLUDE [header](includes/edge-env.md)]

Ce guide de démarrage rapide vous guide tout au long des étapes de prise en main d’Azure Video Analyzer. Vous allez créer un compte Azure Video Analyzer et les ressources qui l’accompagnent en utilisant le Portail Azure. Vous allez ensuite déployer le module en périphérie Video Analyzer et un module de simulateur de caméra RTSP (Real Time Streaming Protocol) sur votre périphérique Azure IoT Edge.

Une fois la procédure de configuration terminée, vous pourrez exécuter un flux vidéo en direct simulé par le biais d’un pipeline qui détecte et signale tout mouvement dans ce flux. Le diagramme suivant représente graphiquement ce pipeline.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Diagramme d’un pipeline qui détecte et signale le mouvement.":::

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]

- Un périphérique IoT Edge sur lequel vous disposez de privilèges d’administrateur :
  - [Déployer sur un appareil IoT Edge](deploy-iot-edge-device.md)
  - [Déployer sur IoT Edge pour Linux sur Windows](deploy-iot-edge-linux-on-windows.md)
- [Visual Studio Code](https://code.visualstudio.com/) avec l'extension [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="prepare-your-iot-edge-device"></a>Préparer votre périphérique IoT Edge

Le module Azure Video Analyzer doit être configuré pour s’exécuter sur le périphérique IoT Edge avec un compte d’utilisateur local non privilégié. Le module a aussi besoin de certains dossiers locaux pour stocker les données de configuration d’application. Le module simulateur de caméra RTSP a besoin de fichiers vidéo avec lesquels il peut synthétiser un flux vidéo en direct.

Exécutez les commandes suivantes sur votre appareil IoT Edge :

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

Le script de préparation du périphérique de cette commande automatise les tâches de création de dossiers d’entrée et de configuration, de téléchargement de fichiers d’entrée vidéo et de création de comptes d’utilisateur avec les privilèges appropriés. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre périphérique :

- _/home/localedgeuser/samples_
- _/home/localedgeuser/samples/input_
- _/var/lib/videoanalyzer_
- _var/media_

Notez les fichiers vidéo (_.mkv) dans le dossier _/home/localedgeuser/samples/input \* qui sont utilisés pour simuler la vidéo en direct.

## <a name="create-azure-resources"></a>Créer des ressources Azure

La prochaine étape consiste à créer les ressources Azure (compte Video Analyzer, compte de stockage et identité managée affectée par l'utilisateur). Vous pouvez ensuite créer un registre de conteneurs facultatif et inscrire un module en périphérie Video Analyzer avec le compte Video Analyzer.

Lorsque vous créez un compte Video Analyzer, vous devez y associer un compte de stockage Azure. Si vous utilisez Video Analyzer pour enregistrer la vidéo en direct à partir d’une caméra, ces données sont stockées en tant que blobs dans un conteneur dans le compte de stockage. Vous devez utiliser une identité managée pour accorder au compte Video Analyzer l’accès approprié au compte de stockage, comme suit.

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Créer un compte Video Analyzer dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la barre de recherche située en haut, entrez **Video Analyzer**.
1. Sélectionnez **Analyseurs vidéo** sous **Services**.
1. Sélectionnez **Ajouter**.
1. Dans la section **Créer un compte Video Analyzer**, entrez les valeurs requises :

   - **Abonnement** : choisissez l’abonnement sous lequel vous souhaitez créer le compte Video Analyzer.
   - **Groupe de ressources** : choisissez un groupe de ressources pour créer le compte Video Analyzer ou sélectionnez **Créer nouveau** pour créer un groupe de ressources.
   - **Nom du compte Video Analyzer** : entrez un nom de votre compte Video Analyzer. Le nom doit se composer de lettres en minuscules ou de chiffres, sans espaces et comprendre entre 3 et 24 caractères.
   - **Localisation** : choisissez une région pour déployer votre compte Video Analyzer (par exemple **USA Ouest 2**).
   - **compte de stockage** : créez un compte de stockage. Nous vous recommandons de sélectionner un compte de stockage [standard v2 à usage général](../../../storage/common/storage-account-overview.md#types-of-storage-accounts).
   - **Identité de l’utilisateur** : Créez et nommez une nouvelle identité managée affectée par l’utilisateur.

1. Sélectionnez **Vérifier + créer** en bas du formulaire.

### <a name="create-a-container-registry"></a>Créer un registre de conteneur

1. Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.
1. Sous l’onglet **De base**, entrez les valeurs pour **Groupe de ressources** et **Nom du registre**. Utilisez le même groupe de ressources que celui des étapes précédentes. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques.
1. Acceptez les valeurs par défaut pour les autres paramètres. Sélectionnez ensuite **Passer en revue + créer** . Après avoir révisé les paramètres, sélectionnez **Créer**.

## <a name="deploy-edge-modules"></a>Déployer des modules en périphérie

### <a name="deploy-the-video-analyzer-edge-module"></a>Déployer le module en périphérie Video Analyzer

1. Accédez à votre compte Video Analyzer.
1. Sélectionnez **Modules en périphérie** sous le panneau **Edge**.
1. Sélectionnez **Ajouter des modules en périphérie**, entrez **avaedge** en tant que nom du nouveau module en périphérie, puis sélectionnez **Ajouter**.
1. La page **Copier le jeton de provisionnement** s’affiche sur le côté droit de votre écran. Copiez l’extrait de code sous **Propriétés souhaitées recommandées pour le déploiement de module IoT**. Vous en aurez besoin dans une étape ultérieure.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Accédez à votre compte Azure IoT Hub.
1. Sélectionnez **IoT Edge** sous **Gestion automatique des périphériques**.
1. Sélectionnez l’**ID du périphérique** de votre périphérique IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Sélectionnez **Ajouter**, puis **Module IoT Edge** dans le menu déroulant.
1. Entrez **avaedge** pour le **Nom du module IoT Edge**.
1. Copiez et collez la ligne suivante dans le champ **URI de l’image** : `mcr.microsoft.com/media/video-analyzer:1`.
1. Sélectionnez **Variables d’environnement**.
1. Sous **NOM**, entrez **LOCAL_USER_ID**. Sous **VALEUR**, entrez **1010**.
1. Sur la seconde ligne, sous **NOM**, entrez **LOCAL_GROUP_ID**. Sous **VALEUR**, entrez **1010**.
1. Sélectionnez **Opérations de création de conteneur**, puis copiez et collez les lignes suivantes :
   ```json
   {
     "HostConfig": {
       "LogConfig": {
         "Type": "",
         "Config": {
           "max-size": "10m",
           "max-file": "10"
         }
       },
       "Binds": [
         "/var/media/:/var/media/",
         "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
       ],
       "IpcMode": "host",
       "ShmSize": 1536870912
     }
   }
   ```
1. Sélectionnez **Paramètres du jumeau de module** et collez l’extrait de code que vous avez copié précédemment dans la page **Copier le jeton de provisionnement** dans le compte Video Analyzer.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Sélectionnez **Ajouter** en bas de votre écran.
1. Sélectionnez **Itinéraires**.
1. Sous **NOM**, entrez **AVAToHub**. Sous **VALEUR**, entrez `FROM /messages/modules/avaedge/outputs/* INTO $upstream`.
1. Sélectionnez **Vérifier + créer**, puis **Créer** pour déployer votre module en périphérie **avaedge**.

### <a name="deploy-the-edge-module-for-the-rtsp-camera-simulator"></a>Déployer le module en périphérie pour le simulateur de caméra RTSP

1. Accédez à votre compte IoT Hub.
1. Sélectionnez **IoT Edge** sous **Gestion automatique des périphériques**.
1. Sélectionnez l’**ID du périphérique** de votre périphérique IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Sélectionnez **Ajouter**, puis **Module IoT Edge** dans le menu déroulant.
1. Entrez **rtspsim** pour le **Nom du module IoT Edge**.
1. Copiez et collez la ligne suivante dans le champ **URI de l’image** : `mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2`.
1. Sélectionnez **Opérations de création de conteneur**, puis copiez et collez les lignes suivantes :
   ```json
   {
     "HostConfig": {
       "Binds": ["/home/localedgeuser/samples/input:/live/mediaServer/media"]
     }
   }
   ```
1. Sélectionnez **Ajouter** en bas de votre écran.
1. Sélectionnez **Vérifier + créer**, puis **Créer** pour déployer votre module en périphérie **rtspsim**.

### <a name="verify-your-deployment"></a>Vérifier votre déploiement

Dans la page des détails de l’appareil, vérifiez que les modules **avaedge** et **rtspsim** sont listés à la fois comme **Spécifié dans le déploiement** et **Signalé par l’appareil**.

Le démarrage des modules sur l’appareil et leur signalement à IoT Hub peuvent prendre quelques instants. Actualisez la page pour afficher un état mis à jour. Le code d’état **200 –OK** signifie que le [runtime IoT Edge](../../../iot-edge/iot-edge-runtime.md) est sain et fonctionne correctement.

![La capture d’écran affiche une valeur d’état pour un runtime IoT Edge.](./media/deploy-iot-edge-device/status.png)

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

### <a name="obtain-your-iot-hub-connection-string"></a>Obtenir votre chaîne de connexion IoT Hub

1. Dans le Portail Azure, accédez à votre compte IoT Hub.
1. Recherchez **Stratégies d’accès partagé** dans le volet gauche et sélectionnez-le.
1. Cliquez sur la stratégie nommée **iothubowner**.
1. Copiez la valeur **Chaîne de connexion primaire**. Cela ressemble à ceci `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-to-iot-hub"></a>Connexion à IoT Hub

1. Ouvrez Visual Studio Code et sélectionnez **Afficher** > **Explorer**. Vous pouvez aussi sélectionner Ctrl+Maj+E.
1. Dans l’angle en bas à gauche de l’onglet **Explorateur**, sélectionnez **Azure IoT Hub**.
1. Sélectionnez l’icône **Plus d’options** pour voir le menu contextuel. Sélectionnez ensuite **Définir la chaîne de connexion IoT Hub**.
1. Quand une zone d’entrée s’affiche, entrez votre chaîne de connexion IoT Hub.
1. Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche. Vous devriez voir votre ID d’appareil, dont les modules suivants sont normalement déployés :

   - Module de périphérie Video Analyzer (nom du module : **avaedge**)
   - Simulateur RTSP (nom du module **rtspsim**)

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Capture d’écran qui montre le nœud modules étendus.":::

> [!TIP]
> Si vous avez [déployé manuellement Video Analyzer](deploy-iot-edge-device.md) sur un périphérique (par exemple un appareil ARM64), vous voyez le module s’afficher sous cet appareil, sous Azure IoT Hub. Vous pouvez sélectionner ce module et passer aux étapes suivantes.

### <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules

Si vous suivez ce guide de démarrage rapide, les événements sont envoyés à IoT Hub. Pour voir ces événements, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou sélectionnez Ctrl+Maj+X) et recherchez **Azure IoT Hub**.
1. Cliquez avec le bouton de droite sur l’extension IoT Hub et sélectionnez **Paramètres d’extension**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Capture d’écran qui montre la sélection de l’Extension Paramètres.":::

1. Recherchez et activez **Afficher le message détaillé**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Capture d’écran de l’option Afficher les messages détaillés activée.":::

1. Ouvrez le volet **Explorateur** dans Visual Studio Code, puis recherchez **Azure IoT Hub** dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton de droite sur votre ID d’appareil et sélectionnez **Démarrer l’analyse du point de terminaison d’événements intégré**.

   > [!NOTE]
   > Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour IoT Hub. Pour obtenir ces informations, dans le Portail Azure, accédez à votre IoT Hub et recherchez l’option **Points de terminaison intégrés** dans le volet de gauche. Sélectionnez-la et recherchez la section **Point de terminaison compatible avec Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :
   >
   > ```
   > Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
   > ```

## <a name="use-direct-method-calls"></a>Utiliser des appels de méthode directe

Vous pouvez désormais analyser les flux vidéo en direct en appelant les méthodes directes exposées par le module de périphérie Video Analyzer. Lisez [Méthodes directes de Video Analyzer](direct-methods.md) pour examiner toutes les méthodes directes fournies par le module. Le schéma des méthodes directes est disponible [ici](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json).

### <a name="enumerate-pipeline-topologies"></a>Énumérer les topologies de pipeline

Cette étape énumère toutes les [topologies de pipeline](../pipeline.md) du module.

1. Cliquez avec le bouton de droite sur le module **avaedge**, puis sélectionnez **Invoquer la méthode directe du module** dans le menu contextuel.
1. Entrez **pipelineTopologyList** dans la zone d’édition, puis appuyez sur Entrée.
1. Copiez la charge utile JSON suivante et collez-la dans la zone d’édition, puis sélectionnez la touche Entrée.

   ```json
   {
     "@apiVersion": "1.1"
   }
   ```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

Cette réponse est attendue, car aucune topologie de pipeline n’a été créée.

### <a name="set-a-pipeline-topology"></a>Définir une topologie de pipeline

En suivant les mêmes étapes que celles décrites ci-dessus, vous pouvez appeler `pipelineTopologySet` pour définir une topologie de pipeline à l’aide du JSON suivant en tant que charge utile. Vous allez créer une topologie de pipeline nommée _MotionDetection_.

```json
{
  "@apiVersion": "1.1",
  "name": "MotionDetection",
  "properties": {
    "description": "Analyzing live video to detect motion and emit events",
    "parameters": [
      {
        "name": "rtspUrl",
        "type": "string",
        "description&quot;: &quot;rtspUrl"
      },
      {
        "name": "rtspUserName",
        "type": "string",
        "description": "rtspUserName",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "string",
        "description": "rtspPassword",
        "default&quot;: &quot;dummypw"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "transport": "tcp",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          },
          "url&quot;: &quot;${rtspUrl}"
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "sensitivity": "medium",
        "name": "motionDetection",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": []
          }
        ]
      }
    ],
    "sinks": [
      {
        "hubOutputName": "inferenceOutput",
        "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
        "name": "iotHubSink",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

Cette charge utile JSON crée une topologie qui définit trois paramètres, dont deux ont des valeurs par défaut. La topologie comprend un nœud source ([source RTSP](../pipeline.md#rtsp-source)), un nœud processeur ([processeur de détection de mouvement](../pipeline.md#motion-detection-processor)) et un nœud récepteur ([récepteur de messages IoT Hub](../pipeline.md#iot-hub-message-sink)). La charge utile affiche la représentation visuelle de la topologie.

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

L’état retourné est 201. Cet état indique qu’une topologie a été créée.

Essayez les étapes suivantes :

- Invoquez à nouveau `pipelineTopologySet`. Le code d’état retourné est 200. Ce code indique qu’une topologie existante a été correctement mise à jour.
- Invoquez de nouveau `pipelineTopologySet`, mais changez la chaîne de description. Le code d’état retourné est 200 et la description est mise à jour avec la nouvelle valeur.
- Invoquez `pipelineTopologyList` comme indiqué dans la section précédente. Vous pouvez maintenant voir la topologie _MotionDetection_ dans la charge utile retournée.

### <a name="read-the-pipeline-topology"></a>Lire la topologie de pipeline

Invoquez `pipelineTopologyGet` à l’aide de la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "MotionDetection"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les informations suivantes :

- Le code d’état est 200, ce qui indique la réussite.
- La charge utile comprend l’horodatage `createdAt` et l’horodatage `lastModifiedAt`.

### <a name="create-a-live-pipeline-by-using-the-topology"></a>Créer un pipeline en direct à l’aide de la topologie

Créez ensuite un pipeline en direct qui référence la topologie de pipeline précédente. Invoquez la méthode directe `livePipelineSet` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1",
  "properties": {
    "topologyName": "MotionDetection",
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

Notez que cette charge utile :

- Spécifie la topologie (_MotionDetection_) que le pipeline actif utilisera.
- Elle contient la valeur de paramètre pour `rtspUrl`, qui n’avait pas de valeur par défaut dans la charge utile de la topologie. Cette valeur est un lien vers l’exemple de vidéo suivant :

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les points suivants :

- Le code d’état est 201, ce qui indique qu’un pipeline en direct a été créé.
- L’état est `Inactive`, ce qui indique que le pipeline en direct a été créé mais qu’il n’est pas activé. Pour plus d’informations, consultez [États du pipeline](../pipeline.md#pipeline-states).

Essayez les méthodes directes suivantes en exécutant la procédure suivante :

- Invoquez à nouveau `livePipelineSet` à l’aide de la même charge utile. Notez que le code d’état retourné est à présent 200.
- Invoquez à nouveau `livePipelineSet`, mais utilisez une description différente. Notez la description mise à jour dans la charge utile de réponse, indiquant que le pipeline en direct a été correctement mis à jour.
- Invoquez `livePipelineSet`, mais remplacez le nom par `mdpipeline2` et modifiez `rtspUrl` par `rtsp://rtspsim:554/media/lots_015.mkv`. Dans la charge utile de réponse, notez le pipeline en direct nouvellement créé (autrement dit, le code d’état 201).

  > [!NOTE]
  > Comme indiqué dans [Topologies de pipeline](../pipeline.md#pipeline-topologies), vous pouvez créer plusieurs pipelines en direct pour analyser les flux vidéo en direct de plusieurs caméras à l’aide de la même topologie de pipeline. Si vous créez d’autres pipelines en direct, veillez à les supprimer au cours de l’étape de nettoyage.

### <a name="activate-the-live-pipeline"></a>Activer le pipeline en direct

Vous pouvez activer le pipeline en direct pour démarrer le flux de la vidéo en direct (simulée) à travers le pipeline. Invoquez la méthode directe `livePipelineActivate` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été activé.

### <a name="check-the-state-of-the-live-pipeline"></a>Vérifier l’état du pipeline en direct

Invoquez la méthode directe `livePipelineGet` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les informations suivantes :

- Le code d’état est 200, ce qui indique la réussite.
- L’état est `Active`, ce qui indique que le pipeline en direct est désormais actif.

## <a name="observe-results"></a>Observer les résultats

Le pipeline en direct que vous avez créé et activé ci-dessus utilise le nœud processeur de détection de mouvement pour détecter les mouvements dans le stream vidéo en direct entrant, puis envoie les événements au récepteur IoT Hub. Ces événements sont ensuite relayés vers IoT Hub sous forme de messages, qui sont désormais consultables. Messages dans la fenêtre **SORTIE** avec ce qui suit dans la section « corps » :

```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

La section `inferences` indique que le type est mouvement. Elle fournit davantage de données sur l’événement de mouvement. Elle fournit également un cadre englobant pour la région du cadre vidéo (à l’horodatage indiqué) où le mouvement a été détecté.

## <a name="invoke-more-direct-method-calls-to-clean-up"></a>Invoquer des appels de méthodes directes supplémentaires pour nettoyer

Vous pouvez appeler ensuite des méthodes directes pour désactiver et supprimer le pipeline en direct (dans cet ordre).

### <a name="deactivate-the-live-pipeline"></a>Désactiver le pipeline en direct

Invoquez la méthode directe `livePipelineDeactivate` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été désactivé.

Ensuite, essayez d’invoquer `livePipelineGet` comme indiqué précédemment dans cet article. Notez la valeur de l’état.

### <a name="delete-the-live-pipeline"></a>Supprimer le pipeline en direct

Invoquez la méthode directe `livePipelineDelete` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été supprimé.

Si vous avez également créé le pipeline nommé _mdpipeline2_, vous ne pouvez pas supprimer la topologie de pipeline sans supprimer également ce pipeline supplémentaire. Invoquez à nouveau la méthode directe `livePipelineDelete` à l’aide de la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline2"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été supprimé.

Vous pouvez invoquer `livePipelineList` à l’aide de la même charge utile que `pipelineTopologyList`. Notez qu’aucun pipeline en direct n’est énuméré.

### <a name="delete-the-pipeline-topology"></a>Supprimer la topologie de pipeline

Une fois que tous les pipelines en direct ont été supprimés, vous pouvez appeler la méthode directe `pipelineTopologyDelete` avec la charge utile suivante :

```json
{
  "@apiVersion": "1.1",
  "name": "MotionDetection"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que la topologie a été supprimée.

Vous pouvez essayer d’invoquer `pipelineTopologyList` pour voir que le module ne contient pas de topologies.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

- Essayez le [guide de démarrage rapide pour enregistrer des vidéos dans le cloud quand des mouvements sont détectés](detect-motion-record-video-clips-cloud.md).
- Essayez le [guide de démarrage rapide pour analyser les vidéo en direct](analyze-live-video-use-your-model-http.md).
- Découvrez-en plus sur les [messages de diagnostic](monitor-log-edge.md).
