---
title: Analyser des flux vidéo en direct avec plusieurs modèles IA à l’aide de la composition IA
description: Cet article fournit des conseils sur l’analyse de flux vidéo en direct avec plusieurs modèles IA à l’aide de la fonctionnalité de composition IA d’Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4d54fba6f3b59c0e32286333694fa460cdb07b3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487540"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>Analyser des flux vidéo en direct avec plusieurs modèles IA à l’aide de la composition IA

[!INCLUDE [header](includes/edge-env.md)]

Certains scénarios client nécessitent une analyse de vidéo avec plusieurs modèles IA. Ces modèles peuvent [s’augmenter mutuellement](../ai-composition-overview.md#sequential-ai-composition) ou [fonctionner en parallèle de façon indépendante](../ai-composition-overview.md#parallel-ai-composition) sur le [même flux vidéo ou une combinaison](../ai-composition-overview.md#combined-ai-composition) de ces modèles (augmentés et fonctionnant en parallèle de façon indépendante) peut agir sur le même flux vidéo pour en tirer des insights actionnables.

Azure Video Analyzer prend en charge ces scénarios grâce à une fonctionnalité appelée [composition IA](../ai-composition-overview.md). Ce guide vous montre comment appliquer plusieurs modèles de manière augmentée sur le même flux vidéo. Il utilise un modèle Tiny YOLO (léger) et un modèle YOLO classique en parallèle pour détecter un objet d’intérêt. Le modèle Tiny YOLO est plus léger en termes de calcul mais moins précis que le modèle YOLO. Il est appelé en premier. Si l’objet détecté passe un seuil de confiance spécifique, le modèle YOLO classique, fonctionnant par étapes séquentielles, n’est pas appelé. Les ressources sous-jacentes sont ainsi utilisées efficacement.

Après avoir effectué les étapes de ce guide, vous pourrez exécuter un flux vidéo en direct simulé par le biais d’un pipeline avec la capacité de composition d’intelligence artificielle et l’étendre à vos scénarios spécifiques. Le diagramme suivant représente graphiquement ce pipeline.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="Vue d’ensemble de la composition IA":::
 
## <a name="prerequisites"></a>Prérequis

* Un compte Azure disposant d’un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > Vous aurez besoin d’un abonnement Azure avec des autorisations pour créer des principaux de service (le rôle propriétaire permet d’effectuer cette opération). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de votre compte pour qu’il vous les accorde.
* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement. Vérifiez que vous disposez de l’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671 pour le trafic sortant. Cette configuration permet à Azure IoT Tools de communiquer avec Azure IoT Hub.
* Suivez le guide de démarrage rapide [Analyser une vidéo en direct à l’aide de votre propre modèle gRPC](analyze-live-video-use-your-model-grpc.md). Vous ne devez en aucun cas l’ignorer. Il s’agit d’une exigence stricte dans le cadre du guide pratique.

> [!TIP]
> Vous serez peut-être invité à installer Docker pendant l’installation de l’extension Azure IoT Tools. N’hésitez pas à ignorer cette invite.
>
> En cas de problèmes avec les ressources Azure créées, consultez notre [guide de dépannage](troubleshoot.md#common-error-resolutions) qui couvre les problèmes couramment rencontrés.

## <a name="review-the-video-sample"></a>Visionner l’exemple de vidéo

Étant donné que vous avez déjà suivi le guide de démarrage rapide indiqué dans la section Prérequis, un appareil périphérique est déjà créé. Le dossier d’entrée de cet appareil périphérique est /home/localedgeuser/samples/input. Il contient certains fichiers vidéo. Connectez-vous à l’appareil IoT Edge, accédez au répertoire /home/localedgeuser/samples/input/ et exécutez la commande suivante pour obtenir le fichier d’entrée que nous utiliserons pour ce guide pratique.

wget https://avamedia.blob.core.windows.net/public/co-final.mkv

De plus, si vous le souhaitez, sur la machine sur laquelle [VLC media player](https://www.videolan.org/vlc/) est installé, sélectionnez Ctrl+N, puis collez un lien vers l’[exemple de vidéo (.mkv)](https://avamedia.blob.core.windows.net/public/co-final.mkv) pour commencer la lecture. Vous voyez la séquence vidéo montrant des voitures sur une autoroute.

## <a name="create-and-deploy-the-pipeline"></a>Créer et déployer le pipeline

Vous pouvez effectuer les étapes suivantes de la même façon que celles du guide de démarrage rapide que vous avez déjà suivi (indiqué dans les prérequis) moyennant de légers ajustements.

1. Suivez les instructions de la section [Créer et déployer le pipeline](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline) du guide de démarrage rapide que vous venez de terminer. Veillez à effectuer les ajustements suivants à mesure que vous effectuez les étapes. Ces étapes permettent de vérifier que le corps approprié est utilisé pour les appels de méthode directe.
    
    Modifiez le fichier *operations.json* :
    
    * Changez le lien de la topologie de pipeline : `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
    * Sous `livePipelineSet`,  
       1. Vérifiez que `"topologyName" : "AIComposition"` et 
       2. Remplacez la valeur du paramètre `rtspUrl` par la valeur `"rtsp://rtspsim:554/media/co-final.mkv"`.
        
    * Sous `pipelineTopologyDelete`, modifiez le nom : `"name" : "AIComposition"`
2. Suivez les instructions de la section [Générer et déployer le manifeste de déploiement IoT Edge](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest), mais utilisez le manifeste de déploiement suivant : src/edge/deployment.composite.template.json
3. Suivez les instructions de la section [Exécuter l’exemple de programme](analyze-live-video-use-your-model-grpc.md#run-the-sample-program).
4. Pour plus d’informations sur les résultats, consultez la section [Interpréter les résultats](analyze-live-video-use-your-model-grpc.md#interpret-results). En plus des événements analytiques sur le hub et des événements de diagnostic, la topologie que vous avez utilisée crée également un clip vidéo correspondant dans le cloud, déclenché par l’activation de la porte de signal basée sur le signal IA. Ce clip est également accompagné d’[événements opérationnels](record-event-based-live-video.md#operational-events) sur le hub récupérables par les workflows en aval. Vous pouvez [examiner et lire](record-event-based-live-video.md#playing-back-the-recording) le clip vidéo en vous connectant au portail Azure.

## <a name="clean-up"></a>Nettoyer

Si vous ne comptez pas continuer à utiliser cette application, supprimez les ressources que vous avez créées dans ce guide de démarrage rapide.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur les [messages de diagnostic](monitor-log-edge.md).
