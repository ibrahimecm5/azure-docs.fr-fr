---
title: Connecter la caméra vers le cloud à l’aide d’un adaptateur de périphérique distant
description: Cet article explique comment connecter une caméra au service Azure Video Analyzer à l’aide d’un adaptateur de périphérique distant
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74734444b33c6963097d4e23e3859aedbc9a91e7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563204"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>Connecter des caméras au cloud à l’aide d’un adaptateur de périphérique distant

[!INCLUDE [header](includes/cloud-env.md)]

Le service Azure Video Analyzer permet aux utilisateurs de capturer et d’enregistrer des vidéos à partir de caméras RTSP qui sont connectées au cloud. Cela requiert que ces caméras soient accessibles sur Internet, ce qui n’est pas autorisé dans de nombreux cas. Au lieu de cela, vous pouvez déployer le module de périphérie de Video Analyzer sur un appareil de périphérie léger sur le même réseau (privé) que les caméras RTSP et connecter l’appareil de périphérie à Internet. Le module de périphérie peut maintenant être configuré comme un *adaptateur* qui permet au service Video Analyzer de se connecter aux *appareils distants* (caméras). Le module de périphérie agit comme une [passerelle transparente](../../../iot-edge/iot-edge-as-gateway.md) pour le trafic vidéo entre les caméras RTSP et le service Video Analyzer.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="Connecter des caméras au cloud avec un adaptateur de périphérique distant":::

## <a name="pre-reading"></a>Prélecture

* [Connecter des caméras au cloud](connect-cameras-to-cloud.md)
* [Démarrage rapide : Bien démarrer avec les pipelines en direct de Video Analyzer sur le Portail Azure](get-started-livepipelines-portal.md)

## <a name="prerequisites"></a>Prérequis

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Déployer Azure Video Analyzer sur un appareil IoT Edge](../edge/deploy-iot-edge-device.md)
* [IoT Hub doit être attaché au compte Video Analyzer](../create-video-analyzer-account.md#post-deployment-steps)
* [Caméras RTSP](../quotas-limitations.md#supported-cameras)
  * S’assurer que la ou les caméras se trouvent sur le même réseau que l’appareil de périphérie
  * Assurez-vous de pouvoir configurer l’appareil photo pour envoyer de la vidéo au niveau ou au-dessous d’une bande passante maximale (mesurée en Kbits/s/seconde)

## <a name="overview"></a>Vue d’ensemble
Pour connecter une caméra au service Video Analyzer à l’aide d’un adaptateur de périphérique distant, vous devez :

1. Créer un appareil IoT dans le IoT Hub pour représenter la caméra RTSP
1. Créer un adaptateur pour appareil mobile sur le module de périphérie de Video Analyzer pour agir en tant que passerelle transparente pour l’appareil ci-dessus
1. Utiliser l’appareil IoT et l’adaptateur pour appareil mobile lors de la création d’un pipeline en direct dans le service Video Analyzer pour capturer et enregistrer des vidéos à partir de la caméra



## <a name="create-an-iot-device"></a>Créer un appareil IoT

Créez un appareil IoT pour représenter chaque caméra RTSP qui doit être connectée au service Video Analyzer. Dans le portail Azure :

1. Accédez à l’IoT Hub
1. Sélectionnez le volet **Périphériques** sous **Gestion des périphériques**
1. Sélectionnez **Ajouter un périphérique** 
1. Entrez un **ID d’appareil** à l’aide d’une chaîne unique (ex : building404-camera1)
1. Le **type d’authentification** doit être une **Clé symétrique**
1. Toutes les autres propriétés peuvent être conservées par défaut
1. Sélectionnez **Enregistrer** pour créer l’appareil IoT
1. Sélectionnez l’appareil IoT et enregistrez la **clé primaire** ou **secondaire**, car vous en aurez besoin ci-dessous

## <a name="create-a-remote-device-adapter"></a>Créer un adaptateur pour appareil mobile distant

Pour permettre au module de périphérie de Video Analyzer d’agir en tant que passerelle transparente pour la vidéo entre la caméra et le service Video Analyzer, vous devez créer un adaptateur pour appareil mobile distant pour chaque caméra. Appelez la [**méthode directe remoteDeviceAdapterSet**](../edge/direct-methods.md) qui requiert les valeurs suivantes : 

* ID d’appareil pour l’appareil IoT
* Clé primaire de l’appareil IoT
* Adresse IP de la caméra

Dans le portail Azure :

1. Accédez à l’IoT Hub
1. Sélectionnez le volet **IoT Edge** sous **Gestion des appareils**.
1. Sélectionner l’appareil IoT Edge (tel que **ava-sample-device**) sur lequel le module de périphérie de Video Analyzer a été déployé
1. Sous modules, sélectionnez le module de périphérie Video Analyzer (par exemple, **avaedge**)
1. Sélectionnez **</> Méthode directe** 
1. Entrez **remoteDeviceAdapterSet** pour le nom de la méthode
1. Entrez l’expression suivante pour la **charge utile** :

```
 {
   "@apiVersion" : "1.1",
   "name": "<name of remote device adapter such as remoteDeviceAdapterCamera1>",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

En cas de réussite, vous recevrez une réponse avec un code statut 201.

Pour afficher la liste de tous les adaptateurs pour appareils distants définis, appelez la méthode directe **remoteDeviceAdapterList** avec la charge utile suivante :
```
 {
   "@apiVersion" : "1.1"
 }
```


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Créer une topologie de pipeline dans le service Video Analyzer

Lors de la création d’une topologie de pipeline Cloud pour une réception à partir d’une caméra derrière un pare-feu, le tunneling doit être activé sur le nœud source RTSP de la topologie de pipeline. Voir un exemple de cette [topologie de pipeline](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cloud-record-camera-behind-firewall).  


Les valeurs suivantes, qui correspondent à l’appareil IoT créé suivant les instructions précédentes, sont nécessaires pour activer le tunneling sur le nœud source RTSP :

* Nom du hub IoT
* ID de l’appareil IoT Hub

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

Assurez-vous que :
* `Transport` est défini sur `tcp`
* `Endpoint` est défini sur `UnsecuredEndpoint`
* `Tunnel` est défini sur `SecureIotDeviceRemoteTunnel`

Ce [guide de démarrage rapide](get-started-livepipelines-portal.md#deploy-a-live-pipeline) peut être utilisé comme référence, car il décrit la procédure de création d’une topologie de pipeline et d’un pipeline en direct sur le Portail Azure. Utilisez l’exemple de topologie `Live capture, record, and stream from RTSP camera behind firewall`. 

## <a name="create-and-activate-a-live-pipeline"></a>Créer et activer un pipeline en direct

Lors de la création du pipeline en direct, vous devez définir l’URL RTSP, le nom d’utilisateur RTSP, le mot de passe RTSP et l’ID de l’appareil IoT Hub. Vous trouverez ci-dessous un exemple de charge utile.

```
   {
    "name": "record-from-building404-camera1",
    "properties": {
        "topologyName": "record-camera-behind-firewall",
        "description": "Capture, record and stream video from building404-camera1 via a remote device adapter",
        "bitrateKbps": 1500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "<RTSP URL for building404-camera1 such as rtsp://localhost:554/media/video>"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "<User name for building404-camera1>"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "<Password for building404-camera1>"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "<IoT Hub Device ID such as building404-camera1>"
            },
            {
                "name": "videoName",
                "value": "video-from-building404-camera1"
            }
          ]
       }
   }
```
L’adresse IP de l’URL RTSP doit être **localhost**. Assurez-vous que la`bitrateKbps` valeur correspond au paramètre de débit maximal pour la vidéo à partir de la caméra RTSP.

Une fois le pipeline en direct créé, le pipeline peut être activé pour démarrer l’enregistrement sur la ressource vidéo Video Analyzer. [Le démarrage rapide](get-started-livepipelines-portal.md#deploy-a-live-pipeline) mentionné à l’étape précédente explique également comment activer un pipeline en direct dans le portail Azure.


### <a name="playback-recorded-video-in-the-azure-portal"></a>Lecture d’une vidéo enregistrée dans le portail Azure

1. Après l’activation du pipeline en direct, la ressource vidéo sera disponible dans le volet **Vidéos** du compte Video Analyzer dans le portail Azure. L’état indique que le pipeline est **Est en cours d’utilisation** lorsqu’il est actif et en train d’enregistrer.
1. Sélectionnez la ressource vidéo qui a été définie dans le pipeline en direct pour afficher la vidéo.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Capture d’écran de la vidéo en direct capturée par le pipeline en direct dans le Cloud.":::

Si vous rencontrez des erreurs lors de la tentative de lecture de la vidéo, suivez les étapes décrites dans [ce guide de dépannage](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline).

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant qu’il existe une vidéo dans votre compte Video Analyzer, vous pouvez exporter un clip de cette vidéo enregistrée au format MP4 à l’aide de [ce didacticiel](export-portion-of-video-as-mp4.md).
