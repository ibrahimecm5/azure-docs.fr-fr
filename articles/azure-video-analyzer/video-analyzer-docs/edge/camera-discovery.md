---
title: Détection des caméras compatibles ONVIF dans le sous-réseau local
description: Ce guide pratique vous montre comment utiliser le module périphérique Video Analyzer pour découvrir des caméras compatibles ONVIF dans votre sous-réseau local.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 04d46daedd88ceef8b91ce4f0135d80b2d1c9045
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097162"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>Détection des caméras compatibles ONVIF dans le sous-réseau local

[!INCLUDE [header](includes/edge-env.md)]

Ce guide pratique vous guide tout au long de l’utilisation du module périphérique Video Analyzer pour découvrir les caméras compatibles ONVIF sur le même sous-réseau que l’appareil IoT Edge. La norme ONVIF (Open Network Video interface Forum) est une norme ouverte dans laquelle des appareils physiques basés sur IP discrets, comme les caméras de surveillance, peuvent communiquer avec des appareils et des logiciels en réseau supplémentaires. Pour plus d’informations sur la norme ONVIF, consultez le site web [ONVIF](https://www.onvif.org/about/mission/).

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez :

- Avoir un abonnement Azure actif.
- Installer [l’extension de l’interface Azure CLI pour IoT](https://github.com/Azure/azure-iot-cli-extension#installation)
- Effectuez l'une des opérations suivantes :
  - [Démarrage rapide : Bien démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md)
  - [Démarrage rapide : démarrage avec Azure Video Analyzer dans le Portail Azure](get-started-detect-motion-emit-events-portal.md)
- Le module périphérique Video Analyzer version 1.1 (ou ultérieure) doit être déployé sur votre appareil IoT Edge
- Si vous utilisez une machine virtuelle [Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11)) ou [EFLOW](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true), un [commutateur externe](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) est requis pour effectuer l’appel de méthode directe onvifDeviceDiscover. Cette condition est due à un appel [multidiffusion](https://en.wikipedia.org/wiki/Multicast) utilisé pour la découverte d’appareils ONVIF.

La fonctionnalité ONVIF du module périphérique Video Analyzer nécessite des options de création de conteneur spécifiques, comme décrit à la section [Activer la fonctionnalité de découverte ONVIF](#enable-onvif-discovery-feature). Cette fonctionnalité de découverte ONVIF nécessite que le port 3702 soit disponible.
  
> [!NOTE]
> Si vous avez un nouveau déploiement de la version 1.1 du module périphérique Video Analyzer, vous pouvez passer à la section [Utiliser des appels de méthode directe](#use-direct-method-calls). Vous pouvez également suivre les sections ci-dessous pour mettre à niveau votre module périphérique Video Analyzer existant afin d’activer la fonctionnalité de découverte ONVIF.  

## <a name="check-the-version"></a>Vérifier la version

À partir d’une invite de commande, exécutez les commandes suivantes :

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

Si le résultat de la commande ci-dessus est **Azure Video Analyzer : 1.0.1**, procédez comme suit pour mettre à jour le module périphérique Video Analyzer vers la version 1.1.

1. Dans le portail Azure, accéder à IoT Hub (`<IOT_HUB_NAME>` ci-dessus) qui est utilisé avec votre déploiement du module périphérique Video Analyzer
1. Cliquer sur **IoT Edge** sous Gestion automatique des appareils et sélectionner l’appareil IoT Edge (`<IOT_EDGE_DEVICE_NAME>` ci-dessus) sur lequel le module périphérique Video Analyzer a été déployé
1. Cliquer sur **Définir les modules**, puis sur **Vérifier + créer**
1. Cliquez sur **Créer**
1. Après quelques minutes, le module périphérique Video Analyzer est mis à jour et vous pouvez exécuter de nouveau la commande ci-dessus pour vérifier

### <a name="enable-onvif-discovery-feature"></a>Activer la fonctionnalité de découverte ONVIF

Ensuite, ajoutez des options supplémentaires dans les options de création de conteneur pour le module périphérique Video Analyzer. Cela lui permet de communiquer via le réseau hôte pour découvrir les caméras compatibles ONVIF sur le même sous-réseau.

1. Dans le portail Azure, accéder à IoT Hub
1. Cliquer sur **IoT Edge** sous Gestion automatique des appareils et sélectionner l’appareil IoT Edge sur lequel le module périphérique Video Analyzer a été déployé
1. Cliquer sur **Définir des modules** et sélectionner le module périphérique Video Analyzer
1. Accédez à l’onglet **Options de création de conteneur** et ajoutez les entrées JSON suivantes aux sections respectives :

    - Dans la zone d’entrée JSON, après le premier `{`, entrez ce qui suit :
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - Dans l’objet JSON `HostConfig` :

        ```JSON
        "NetworkMode": "host",
        ```
1. Cliquer sur **Mettre à jour** en bas
1. Cliquez sur **Vérifier + créer**.
1. Cliquez sur **Créer**

## <a name="use-direct-method-calls"></a>Utiliser des appels de méthode directe

Le module périphérique Video Analyzer fournit des appels de méthode directs pour la découverte ONVIF des caméras en réseau sur le même sous-réseau. Les étapes suivantes s’appliquent à la fois aux sections `onvifDeviceDiscover` et `onvifDeviceGet` ci-dessous :

1. Dans le portail Azure, accéder à IoT Hub
1. Cliquer sur **IoT Edge** sous Gestion automatique des appareils et sélectionner l’appareil IoT Edge sur lequel le module périphérique Video Analyzer a été déployé
1. Cliquez sur le module périphérique Video Analyzer, puis sur **Méthode directe** dans la barre de menus en haut

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

Cette méthode directe répertorie l’ensemble des appareils ONVIF détectables sur le même sous-réseau que le module périphérique Video Analyzer.


1. Pour le nom de la méthode, entrez :

    ```JSON
    onvifDeviceDiscover
    ```
1. Pour la charge utile, entrez :

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   La durée de la détection correspond à la durée pendant laquelle le module périphérique Video Analyzer attend de recevoir des réponses des appareils ONVIF détectables. Il peut être nécessaire d’ajuster cette valeur dans un environnement de grande taille.

1. Au bout de quelques secondes, vous devez voir un résultat `result` tel que :

    ```JSON
    {
        "status": 200,
        "payload": {
            "value": [
                {
                    "serviceIdentifier": "{urn:uuid}",
                    "remoteIPAddress": "{IP_ADDRESS}",
                    "scopes": [
                        "onvif://www.onvif.org/type/Network_Video_Transmitter",
                        "onvif://www.onvif.org/name/{CAMERA_MANUFACTURE}",
                        "onvif://www.onvif.org/location/",
                        "onvif://www.onvif.org/hardware/{CAMERA_MODEL}",
                        "onvif://www.onvif.org/Profile/Streaming",
                        "onvif://www.onvif.org/Profile/G",
                        "onvif://www.onvif.org/Profile/T"
                    ],
                    "endpoints": [
                        "http://<IP_ADDRESS>/onvif/device_service",
                        "https://<IP_ADDRESS>/onvif/device_service"
                    ],
                    
                }
            ]
        }
    }
    ```
    L’état de retour 200 indique que l’appel de méthode directe a été traité avec succès.

### <a name="onvifdeviceget"></a>onvifDeviceGet

L’appel onvifDeviceGet prend en charge à la fois les points de terminaison non sécurisés et compatibles TLS. Cet appel de méthode directe récupère des informations détaillées sur un appareil ONVIF spécifique.

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> Quand l’appel onvifDeviceGet est effectué sur un point de terminaison non sécurisé sur une caméra activée pour la norme ONVIF, vous devez définir le jumeau de module périphérique vidéo `"allowUnsecuredEndpoints"` sur `true`. Pour plus d’informations, consultez l’article [Propriétés de jumeau de module](./module-twin-configuration-schema.md).

1. Pour le nom de la méthode, entrez :

    ```JSON
    onvifDeviceGet
    ```
1. Pour la charge utile, entrez :

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "http://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }
    ```

# <a name="tlsendpoint"></a>[TlsEndpoint](#tab/tlsendpoint)

1. Pour le nom de la méthode, entrez :

    ```JSON
    onvifDeviceGet
    ```
1. Pour la charge utile, entrez :

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.TlsEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "https://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }

---

In the above payload:

- `url` is the IP address of the ONVIF device from which you wish to get additional details
- `username` is the user name that is used to authenticate with the device, which has permissions to the ONVIF features. Some devices require specific accounts to be created for this purpose.
- `password` is the password for that user name

    Within a few seconds you should see a result such as:

    ```JSON
    {
        "status": 200,
        "payload": {
            "hostname": {
                "fromDhcp": true,
                "hostname": "{NAME_OF_THE_ONVIF_DEVICE}"
            },
            "systemDateTime": {
                "type": "ntp",
                "time": "2021-09-28T03:05:05.000Z",
                "timeZone": "GMT"
            },
            "dns": {
                "fromDhcp": true,
                "ipv4Address": [
                    "{IP_ADDRESS}"
                ],
                "ipv6Address": []
            },
            "mediaProfiles": [
                {
                    "name": "{Profile1}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 3840,
                            "height": 2160
                        },
                        "rateControl": {
                            "bitRateLimit": 15600,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                },
                {
                    "name": "{Profile2}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 1280,
                            "height": 720
                        },
                        "rateControl": {
                            "bitRateLimit": 1900,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                }
            ]
        }
    }
    ```

### <a name="return-status-of-onvifdeviceget"></a>État de retour de onvifDeviceGet

| Statut | Code      | Signification/solution                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Succès   | L’appel de méthode directe s’est terminé avec succès.                                                                                                                                                                                |
| 400    | Demande incorrecte  | La requête était malformée   |
| 403    | Interdit | L’appel de méthode directe n’a pas pu récupérer avec succès les informations requises à partir de l’appareil ONVIF en raison d’un échec d’authentification. Vérifiez que le nom d’utilisateur et/ou le mot de passe dans le corps du message sont corrects. |
| 500 | Erreur | Une erreur inconnue s'est produite.  |
| 502    | Passerelle incorrecte | Réception d’une réponse non valide d’un service en amont.  |
| 504    | Délai d'expiration   | L’appel de méthode directe a expiré avant la réception de la réponse de l’appareil ONVIF.  |

## <a name="troubleshooting"></a>Dépannage

Cette section traite certaines étapes de dépannage :

- Dans le portail Azure, sur le panneau de méthode directe de module IoT Edge, si vous recevez l’erreur « Une erreur a empêché l’exécution de l’opération. La requête a échoué avec le code d’état 504. » (Voir l’image ci-dessous) :

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="Capture d’écran montrant l’erreur 504.":::

    Vérifiez que le paramètre pour `"discoveryDuration":"PT8S"` dans l’appel de méthode directe ci-dessus est inférieur aux valeurs `Connection Timeout` ou `Method Timeout`.

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="Capture d’écran montrant la correction de l’erreur 504.":::

- Si le champ de méthode directe `Results` affiche "{"status":200,"payload":{"value":[]}}, la découverte peut prendre plus de temps

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="Le message retourné est affiché dans le champ « Résultats » de méthode directe":::

    Ajustez la valeur de temps (x) `"discoveryDuration":"PTxS"` à un nombre plus élevé. Ajustez également les valeurs `Connection Timeout` et/ou `Method Timeout` en conséquence.
- L’appel de méthode directe `onvifDeviceGet` n’affiche aucun profil de média pour les flux de médias encodés H.265.
- L’état 403 peut être retourné dans le cas où le compte d’utilisateur utilisé pour se connecter à l’appareil ONVIF ne dispose pas des autorisations pour les fonctionnalités de caméra ONVIF. Certains appareils compatibles ONVIF nécessitent qu’un utilisateur soit ajouté aux paramètres de sécurité ONVIF pour récupérer les informations de l’appareil ONVIF.
- Actuellement, le module périphérique Video Analyzer retourne jusqu’à 200 caméras compatibles ONVIF qui sont accessibles sur le même sous-réseau via la multidiffusion. Il est également nécessaire que le port 3702 soit disponible.
- Si onvifDeviceGet est appelé avec un point de terminaison TLS, le nom et l’adresse IP de l’appareil ONVIF doivent être ajoutés au fichier hosts (/etc/hosts) sur le périphérique.  Si un certificat auto-signé est utilisé pour le chiffrement TLS, le certificat doit également être ajouté au magasin de certificats de l’appareil IoT Edge. 
- Pour l’appel de méthode directe onvifDeviceDiscover à partir d’une machine virtuelle EFLOW, procédez comme suit :
  - Se connecter à la machine virtuelle EFLOW et exécutez `sudo iptables -I INPUT -p udp -j ACCEPT`

## <a name="next-steps"></a>Étapes suivantes

- Essayer le [guide de démarrage rapide pour analyser les vidéos en direct](analyze-live-video-use-your-model-http.md) avec l’appareil ONVIF détecté
