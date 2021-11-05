---
title: Détecter des mouvements et enregistrer des vidéos avec Azure Video Analyzer
description: Ce guide de démarrage rapide montre comment utiliser le module de périphérie Azure Video Analyzer pour appliquer la détection de mouvement à un stream vidéo en direct et enregistrer une vidéo dans le compte Video Analyzer.
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a62af8ee0ac3859a6d8f7e7d81d7115877afda35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029176"
---
# <a name="quickstart-detect-motion-record-video-to-video-analyzer"></a>Démarrage rapide : Détecter des mouvements et enregistrer une vidéo dans Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

Cet article vous explique comment utiliser le module de périphérie Azure Video Analyzer pour un [enregistrement basé sur les événements](../event-based-video-recording-concept.md). Il utilise une machine virtuelle Linux comme appareil IoT Edge et un flux vidéo en direct simulé. Ce flux vidéo est analysé en vue de détecter la présence d’objets en mouvement. Quand un mouvement est détecté, les événements sont envoyés à Azure IoT Hub, et la partie appropriée du stream vidéo est enregistrée en tant que [ressource vidéo](../terminology.md#video) dans votre compte Video Analyzer.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) gratuitement.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

Le processus de déploiement prend environ **20 minutes**. À la fin de l’opération, certaines ressources Azure sont déployées dans l’abonnement Azure, notamment :
1. **Compte Video Analyzer** : ce [service cloud](../overview.md) est utilisé pour inscrire le module de périphérie Video Analyzer ainsi que pour la lecture des vidéos enregistrées et l’analytique vidéo.
1. **Compte de stockage** : pour stocker les vidéos enregistrées et l’analytique vidéo.
1. **Identité managée** : il s’agit de l’[identité managée]../../active-directory/managed-identities-azure-resources/overview.md) affectée par l’utilisateur, qui permet de gérer l’accès au compte de stockage ci-dessus.
1. **Machine virtuelle** : il s’agit d’une machine virtuelle qui sert d’appareil de périphérie simulé.
1. **IoT Hub** : fait office de hub de messagerie centralisé pour la communication bidirectionnelle entre votre application IoT, les modules IoT Edge et les appareils qu'il gère.

Vous pouvez obtenir plus de détails [ici](https://github.com/Azure/video-analyzer/tree/main/setup).

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

Sur la machine virtuelle créée par le déploiement ci-dessus se trouvent plusieurs fichiers MKV.  L’un de ces fichiers s’appelle `lots_015.mkv`. Dans les étapes suivantes, nous allons utiliser ce fichier vidéo afin de simuler un stream en direct pour ce tutoriel.

Vous pouvez utiliser une application, telle que [VLC Player](https://www.videolan.org/vlc/), la lancer, appuyer sur `Ctrl+N` et coller le lien de l’[exemple de vidéo d’aire de stationnement](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) pour commencer la lecture. Au repère d’approximativement 5 secondes, une voiture blanche traverse l’aire de stationnement.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Quand vous aurez effectué les étapes ci-dessous, vous aurez utilisé le module de périphérie Video Analyzer pour détecter le mouvement de la voiture, et enregistrer un clip vidéo commençant au repère situé à environ 5 secondes. 

Le diagramme ci-dessous est la représentation visuelle du flux global.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/detect-motion-record-video-clips-cloud/topology.svg" alt-text="Enregistrement vidéo basé sur les événements dans une ressource vidéo basée sur les événements de détection de mouvement":::

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

### <a name="obtain-your-iot-hub-connection-string"></a>Obtenir votre chaîne de connexion IoT Hub

1. Dans le portail Azure, accédez au IoT Hub que vous avez créé dans le cadre de l’étape de configuration ci-dessus
1. Recherchez l’option **Stratégies d’accès partagé** dans le volet de navigation de gauche, puis cliquez dessus.
1. Cliquez sur la stratégie nommée **iothubowner**
1. Copiez la **chaîne de connexion principale**. Elle ressemble à `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`

### <a name="connect-to-the-iot-hub"></a>Se connecter au hub IoT

1. Ouvrez Visual Studio Code, sélectionnez **Afficher** > **Explorateur**. Vous pouvez aussi sélectionner Ctrl+Maj+E.
1. Dans l’angle en bas à gauche de l’onglet **Explorateur**, sélectionnez **Azure IoT Hub**.
1. Sélectionnez l’icône **Plus d’options** pour voir le menu contextuel. Sélectionnez ensuite **Définir la chaîne de connexion IoT Hub**.
1. Quand une zone d’entrée s’affiche, entrez votre chaîne de connexion IoT Hub.
1. Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche. Vous devez voir l’appareil de périphérie `avasample-iot-edge-device`, dont les modules suivants sont normalement déployés :
    * Module de périphérie Video Analyzer (nom du module : **avaedge**)
    * Simulateur RTSP (nom du module **rtspsim**)


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Développez le nœud Modules":::

> [!TIP]
> Si vous [avez déployé manuellement Video Analyzer](deploy-iot-edge-device.md) sur un appareil de périphérie (par exemple un appareil ARM64), vous voyez le module s’afficher sous cet appareil, sous le hub Azure IoT Hub. Vous pouvez sélectionner ce module et suivre le reste des étapes ci-dessous.

### <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules 

Si vous suivez ce guide de démarrage rapide, les événements sont envoyés au hub IoT. Pour voir ces événements, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X), puis recherchez **Azure IoT Hub**.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Sélectionnez Paramètres d’extension":::
1. Recherchez et activez Afficher le message détaillé.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Afficher le message détaillé":::
1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez **Azure IoT Hub** dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton droit sur `avasample-iot-edge-device`, puis sélectionnez **Démarrer la supervision du point de terminaison d’événement intégré**.

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="use-direct-method-calls-to-analyze-live-video"></a>Utiliser des appels de méthode directe pour analyser la vidéo en direct

Vous pouvez désormais analyser les streams vidéo en direct en appelant les méthodes directes exposées par le module de périphérie Video Analyzer. Lisez [Méthodes directes de Video Analyzer](direct-methods.md) pour examiner toutes les méthodes directes fournies par le module.

### <a name="enumerate-pipeline-topologies"></a>Énumérer les topologies de pipeline

Cette étape énumère toutes les [topologies de pipeline](../pipeline.md) du module.

1. Cliquez avec le bouton droit sur le module « avaedge », puis sélectionnez **Invoquer la méthode directe du module** dans le menu contextuel.
1. Vous verrez une zone de modification apparaître dans la partie centrale supérieure de la fenêtre Visual Studio Code. Entrez « pipelineTopologyList » dans la zone d’édition, puis appuyez sur Entrée.
1. Ensuite, copiez et collez la charge utile JSON ci-dessous dans la zone de modification, puis appuyez sur Entrée.
   
```json
{
    "@apiVersion" : "1.1"
}
```

En quelques secondes, vous verrez la réponse suivante dans la fenêtre SORTIE :
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

La réponse ci-dessus est attendue, car aucune topologie de pipeline n’a été créée.

### <a name="set-a-pipeline-topology"></a>Définir une topologie de pipeline

En suivant les mêmes étapes que ci-dessus, vous pouvez appeler `pipelineTopologySet` pour définir une topologie de pipeline à l’aide du JSON suivant en tant que charge utile. Vous allez créer une topologie de pipeline nommée « EVRtoVideoSinkOnMotionDetection ».

> [!NOTE]
> Dans la charge utile ci-dessous, la propriété `videoName` a la valeur « sample-motion-video-camera001 », qui représente le nom de la ressource vidéo créée dans votre compte Video Analyzer. Ce nom de ressource doit être unique pour chaque source vidéo en direct que vous enregistrez. Vous devez modifier la propriété `videoName` ci-dessous selon les besoins pour garantir l’unicité.

```
{
  "@apiVersion": "1.1",
  "name": "EVRtoVideoSinkOnMotionDetection",
  "properties": {
    "description": "Event-based video recording to Video Sink based on motion events",
    "parameters": [
      {
        "name": "rtspUserName",
        "type": "String",
        "description": "rtsp source user name.",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "String",
        "description": "rtsp source password.",
        "default&quot;: &quot;dummyPassword"
      },
      {
        "name": "rtspUrl",
        "type": "String",
        "description&quot;: &quot;rtsp Url"
      },
      {
        "name": "motionSensitivity",
        "type": "String",
        "description": "motion detection sensitivity",
        "default&quot;: &quot;medium"
      },
      {
        "name": "hubSinkOutputName",
        "type": "String",
        "description": "hub sink output name",
        "default&quot;: &quot;inferenceOutput"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "url": "${rtspUrl}",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          }
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "name": "motionDetection",
        "sensitivity": "${motionSensitivity}",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ]
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
        "name": "signalGateProcessor",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          },
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "activationEvaluationWindow": "PT1S",
        "activationSignalOffset": "PT0S",
        "minimumActivationTime": "PT30S",
        "maximumActivationTime&quot;: &quot;PT30S"
      }
    ],
    "sinks": [
      {
        "@type": "#Microsoft.VideoAnalyzer.VideoSink",
        "name": "videoSink",
        "videoName": "sample-motion-video-camera001",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "videoCreationProperties": {
          "title": "sample-motion-video-camera001",
          "description": "Motion-detection based recording of clips to a video resource",
          "segmentLength&quot;: &quot;PT30S"
        },
        "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
        "localMediaCacheMaximumSizeMiB&quot;: &quot;2048"
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.IoTHubMessageSink",
        "name": "hubSink",
        "hubOutputName": "${hubSinkOutputName}",
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

La charge utile JSON ci-dessus entraîne la création d’une topologie de pipeline qui définit cinq paramètres (quatre d’entre eux ont des valeurs par défaut). La topologie comporte un nœud source ([source RTSP](../pipeline.md#rtsp-source)), deux nœuds processeurs ([processeur de détection de mouvement](../pipeline.md#motion-detection-processor) et [processeur de porte de signal](../pipeline.md#signal-gate-processor)) ainsi que deux nœuds récepteurs (récepteur IoT Hub et [récepteur vidéo](../pipeline.md#video-sink)). La représentation visuelle de la topologie est indiquée ci-dessus.

En quelques secondes, vous verrez la réponse suivante dans la fenêtre **SORTIE**.

```
[DirectMethod] Invoking Direct Method [pipelineTopologySet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
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
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
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



L’état retourné est 201, ce qui indique qu’une topologie de pipeline a été créée. Essayez les méthodes directes suivantes en exécutant la procédure suivante :

* Appelez à nouveau `pipelineTopologySet` et vérifiez que le code d’état retourné est 200. Le code d’état 200 indique qu’une topologie de pipeline existante a été correctement mise à jour.
* Appelez à nouveau `pipelineTopologySet`, mais changez la chaîne de description. Vérifiez que le code d’état dans la réponse est 200 et que la description est mise à jour avec la nouvelle valeur.
* Appelez `pipelineTopologyList`, comme indiqué à l’étape précédente, et vérifiez que vous pouvez désormais voir la topologie « EVRtoVideoSinkOnMotionDetection » listée dans la charge utile retournée.

### <a name="read-the-pipeline-topology"></a>Lire la topologie de pipeline

Appelez à présent `pipelineTopologyGet` avec la charge utile suivante
```

{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre Sortie

```
[DirectMethod] Invoking Direct Method [pipelineTopologyGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
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
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

Remarquez les propriétés suivantes dans la charge utile de réponse :

* Le code d’état est 200, ce qui indique une réussite.
* La charge utile comprend l’horodatage `createdAt` et l’horodatage `lastModifiedAt`.

### <a name="create-a-live-pipeline-using-the-topology"></a>Créer un pipeline en direct à l’aide de la topologie

Créez ensuite un pipeline en direct qui référence la topologie de pipeline ci-dessus. Appelez la méthode directe `livePipelineSet` avec la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1",
    "properties" : {
        "topologyName" : "EVRtoVideoSinkOnMotionDetection",
        "description" : "Sample pipeline description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Notez les points suivants :

* La charge utile ci-dessus spécifie la topologie (« EVRtoVideoSinkOnMotionDetection ») à utiliser par le pipeline en direct.
* La charge utile contient la valeur de paramètre pour `rtspUrl`, qui n’avait pas de valeur par défaut dans la charge utile de la topologie.

En quelques secondes, vous verrez la réponse suivante dans la fenêtre Sortie :

```
[DirectMethod] Invoking Direct Method [livePipelineSet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:20:29.023Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:20:29.023Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Remarquez les propriétés suivantes dans la charge utile de réponse :

* Le code d’état est 201, ce qui indique qu’un pipeline en direct a été créé.
* L’état est « Inactif », ce qui indique que le pipeline en direct a été créé mais qu’il n’est pas activé. Pour plus d’informations, consultez [États du pipeline](../pipeline.md#pipeline-states).

Essayez les méthodes directes suivantes en exécutant la procédure suivante :

* Appelez à nouveau `livePipelineSet` avec la même charge utile. Vous noterez que le code d’état retourné est à présent 200.
* Appelez à nouveau `livePipelineSet` mais avec une autre description, et notez la description mise à jour dans la charge utile de réponse. Elle indique que le pipeline en direct a été correctement mis à jour.

    > [!NOTE]
    > Comme indiqué dans [Topologies de pipeline](../pipeline.md#pipeline-topologies), vous pouvez créer plusieurs pipelines en direct pour analyser les streams vidéo en direct de plusieurs caméras à l’aide de la même topologie de pipeline. Toutefois, cette topologie particulière code en dur la valeur de `videoName`. Dans la mesure où une seule source vidéo en direct doit être enregistrée dans une ressource vidéo Video Analyzer, vous ne devez pas créer de pipelines en direct supplémentaires avec cette topologie particulière.

### <a name="activate-the-live-pipeline"></a>Activer le pipeline en direct

Vous pouvez ensuite activer le pipeline en direct, ce qui entraîne le démarrage du flux de la vidéo en direct (simulée) à travers le pipeline. Appelez la méthode directe `livePipelineActivate` avec la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE

```
[DirectMethod] Invoking Direct Method [livePipelineActivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 dans la charge utile de réponse indique que le pipeline en direct a été correctement activé.

### <a name="check-the-state-of-the-live-pipeline"></a>Vérifiez l’état du pipeline en direct

Appelez à présent la méthode directe `livePipelineGet` avec la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE

```
[DirectMethod] Invoking Direct Method [livePipelineGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T14:21:21.750Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T14:21:21.750Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Remarquez les propriétés suivantes dans la charge utile de réponse :

* Le code d’état est 200, ce qui indique une réussite.
* L’état est « Actif », ce qui indique que le pipeline en direct est maintenant à l’état « Actif ».

## <a name="observe-results"></a>Observer les résultats

Le pipeline en direct que vous avez créé et activé ci-dessus utilise le nœud processeur de détection de mouvement pour détecter les mouvements dans le stream vidéo en direct entrant, puis envoie les événements au récepteur IoT Hub. Ces événements sont ensuite relayés vers le hub IoT sous forme de messages, qui sont désormais consultables. Les messages suivants s’affichent dans la fenêtre SORTIE

```
[IoTHubMonitor] [1:22:53 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620066173760872 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCJoQAAAMABAAAAwDwPGDGWA==,aOvhEsiw\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-03T18:22:53.761Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066173816,
    "iothub-message-source": "Telemetry",
    "messageId": "c2de6a40-1e0a-45ef-9449-599fc5680d05",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
[IoTHubMonitor] [1:22:59 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "timestamp": 145805956115743,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/motionDetection",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-03T18:22:59.063Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066179359,
    "iothub-message-source": "Telemetry",
    "messageId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}

```

Notez les propriétés suivantes dans les messages ci-dessus :

* Chaque message contient une section `body` et une section `properties`. Pour comprendre ce que ces sections représentent, lisez l’article [Créer et lire des messages IoT Hub](../../../iot-hub/iot-hub-devguide-messages-construct.md).
* Le premier message, **MediaSessionEstablished**, indique que le nœud source RTSP (sujet) a pu établir une connexion avec le simulateur RTSP, et qu’il commence à recevoir un flux en direct (simulé).
* `subject` référence le nœud situé dans le pipeline en direct à partir duquel le message a été généré. Dans ce cas, le message provient du nœud source RTSP.
* `eventType` indique qu’il s’agit d’un événement Diagnostics.
* `eventTime` indique l’heure à laquelle l’événement s’est produit.
* `body` contient les données relatives à l’événement. Il s’agit du message [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).
* Le deuxième message est un événement **Inference**. Vous pouvez constater qu’il est envoyé environ 5 secondes après le message **MediaSessionEstablished**, ce qui correspond au délai entre le début de la vidéo et le moment où la voiture traverse le parking.
* `subject` référence le nœud processeur de détection de mouvement dans le pipeline, qui a généré ce message
* Dans la mesure où il s’agit d’un événement Analytics, `body` contient des données `timestamp` et `inferences`.
* La section `inferences` indique que le `type` est « motion » et qu’il contient des données supplémentaires relatives à l’événement « motion ».

Notez que la section `messageId` dans le corps correspond à « 9ccfab80-2993-42c7-9452-92e21df96413 ». Elle apparaît dans l’événement opérationnel suivant :

```
[IoTHubMonitor] [1:23:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion-video-camera001"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-03T18:23:31.319Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066211373,
    "iothub-message-source": "Telemetry",
    "messageId": "c7cbb363-7cc7-4169-936f-55de5fae111c",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
« Ce message **RecordingStarted** indique que l’enregistrement vidéo a démarré. Notez que la valeur de `correlationId`, « 9ccfab80-2993-42c7-9452-92e21df96413 », correspond au `messageId` du message **Inference**, ce qui vous permet de suivre l’événement qui a déclenché l’enregistrement. Le prochain événement opérationnel est le suivant :

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingAvailable",
    "eventTime": "2021-05-03T18:24:00.686Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240741,
    "iothub-message-source": "Telemetry",
    "messageId": "5b26aa88-e037-4834-af34-a6a4df3c42c2",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
```

Cet événement **RecordingAvailable** indique que les données multimédias sont désormais enregistrées dans la ressource vidéo. Notez que `correlationId` est identique : « 9ccfab80-2993-42c7-9452-92e21df96413 ». Le dernier événement opérationnel pour cette chaîne de messages (avec le même `correlationId`) est le suivant :

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStopped",
    "eventTime": "2021-05-03T18:24:00.710Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240766,
    "iothub-message-source": "Telemetry",
    "messageId": "f3dbd5d5-3176-4d5b-80d8-c67de85bc619",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
Cet événement **RecordingStopped** indique que la porte de signal s’est fermée, et que la partie appropriée de la vidéo en direct entrante a été enregistrée. Notez que `correlationId` est identique : « 9ccfab80-2993-42c7-9452-92e21df96413 ».

Dans la topologie, le nœud processeur de la porte de signal a été configuré avec des délais d’activation de 30 secondes, ce qui signifie que la topologie de pipeline enregistre environ 30 secondes de vidéo.  Pendant l’enregistrement de la vidéo, le nœud processeur de détection de mouvement continue à émettre des événements **Inference**, lesquels vont s’afficher dans la fenêtre SORTIE entre les événements **RecordingAvailable** et **RecordingStopped**.

Si vous laissez le pipeline en direct continuer à s’exécuter, le simulateur RTSP atteint la fin du fichier vidéo, puis s’arrête/se déconnecte. Le nœud source RTSP se reconnecte ensuite au simulateur et le processus se répète.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Invoquer des appels de méthodes directes supplémentaires pour nettoyer

Vous pouvez appeler ensuite des méthodes directes pour désactiver et supprimer le pipeline en direct (dans cet ordre).

### <a name="deactivate-the-live-pipeline"></a>Désactiver le pipeline en direct

Appelez la méthode directe `livePipelineDeactivate` avec la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE.

```
[DirectMethod] Invoking Direct Method [livePipelineDeactivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été correctement désactivé.


### <a name="delete-the-live-pipeline"></a>Supprimer le pipeline en direct

Appelez la méthode directe `livePipelineDelete` avec la charge utile suivante

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [livePipelineDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 dans la réponse indique que le pipeline en direct a été correctement supprimé.


### <a name="delete-the-pipeline-topology"></a>Supprimer la topologie de pipeline

Appelez la méthode directe `pipelineTopologyDelete` avec la charge utile suivante :

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE

```
[DirectMethod] Invoking Direct Method [pipelineTopologyDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que la topologie de pipeline a été correctement supprimée.

## <a name="playing-back-the-recording"></a>Lecture de l’enregistrement

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.
1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources que vous avez dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la liste **Video Analyzer**.
1. Vous trouverez une vidéo portant le nom de `sample-motion-video-camera001`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. La page de détails de la vidéo s’ouvre et la lecture démarre automatiquement.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->


[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]    

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [lire des enregistrements vidéo](../playback-recordings-how-to.md)
* Essayer le [guide de démarrage rapide pour analyser les vidéo en direct](analyze-live-video-use-your-model-http.md)
