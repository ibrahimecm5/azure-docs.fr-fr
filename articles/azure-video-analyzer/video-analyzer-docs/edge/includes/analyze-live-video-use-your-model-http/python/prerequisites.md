---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7d745c4e2dfd364e9cb0d62225abf74bd1741a5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861169"
---
* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > Vous aurez besoin d’un abonnement Azure avec au moins un rôle Contributeur. Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre compte pour qu’il vous les accorde.
    * [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
        * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/) (3.6.9 ou version ultérieure), [Pip 3](https://pip.pypa.io/en/stable/installing/) et éventuellement [venv](https://docs.python.org/3/library/venv.html).
* Lisez le guide de démarrage rapide [Détecter les mouvements et émettre des événements](../../../detect-motion-emit-events-quickstart.md)
## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce guide de démarrage rapide, vous allez utiliser Video Analyzer pour détecter des objets comme des véhicules et des personnes. Vous allez publier les événements d’inférence associés sur IoT Edge Hub.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-http/overview.png" alt-text="Publier des événements d’inférence associés sur le hub IoT Edge":::

Le diagramme ci-dessus montre comment les signaux circulent dans ce guide de démarrage rapide. Un [module périphérique](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un [nœud source RTSP](./../../../../pipeline.md#rtsp-source) extrait le flux vidéo de ce serveur et envoie des images vidéo au [nœud du processeur d’extension HTTP](./../../../../pipeline.md#http-extension-processor).

Le nœud d’extension HTTP joue le rôle d’un proxy. Il échantillonne les images vidéo entrantes définies par le champ samplingOptions et convertit les images vidéo en un type d’image spécifié. Ensuite, il relaie les images sur REST vers un autre module périphérique qui exécute un modèle IA derrière un point de terminaison HTTP. Dans cet exemple, le module de périphérie est généré à l’aide du modèle YOLOv3, qui peut détecter de nombreux types d’objets. Le nœud du processeur d’extension HTTP collecte les résultats de la détection et publie les événements sur le [nœud récepteur de messages IoT Hub](./../../../../pipeline.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2018-06&preserve-view=true#iot-edge-hub).

Dans ce guide de démarrage rapide, vous allez :

* Créer et déployer le livePipeline.
* interpréter les résultats ;
* Supprimer des ressources.
## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
