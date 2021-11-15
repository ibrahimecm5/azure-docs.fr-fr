---
title: Développer et déployer un serveur d’inférence gRPC - Azure Video Analyzer
description: Cet article fournit des conseils d’aide sur le développement et le déploiement d’un serveur d’inférence gRPC à utiliser avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3b5d2dbb4e686cfe03a8deefbcbbc6d08647f3de
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564059"
---
# <a name="develop-and-deploy-grpc-inference-server"></a>Développer et déployer un serveur d’inférence gRPC

[!INCLUDE [header](includes/edge-env.md)]

Cet article vous montre comment wrapper les modèles IA de votre choix dans un serveur d’inférence gRPC, pour les intégrer à Azure Video Analyzer (AVA) via l’extension de pipeline.

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

* Extensions de pipeline
* Protocole d’extension gRPC
* Schéma des métadonnées d’inférence
* [Présentation de gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Guide du langage proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Prérequis

* Un appareil x86-64 ou ARM64 exécutant l’un des [systèmes d’exploitation Linux pris en charge](../../../iot-edge/support.md#operating-systems) ou un ordinateur Windows.
* [Installation de Docker](https://docs.docker.com/desktop/#download-and-install) sur votre ordinateur.
* Installation d’un [runtime IoT Edge](../../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>Étapes d’implémentation gRPC

Pour créer un serveur d’inférence gRPC et l’implémenter en tant qu’extension avec Video Analyzer, vous devez effectuer les étapes suivantes :

### <a name="setup-video-analyzer-module"></a>Configurer le module Video Analyzer

Effectuez les étapes nécessaires pour déployer le module Video Analyzer, et le faire fonctionner sur un appareil IoT Edge.

### <a name="high-level-implementation-steps"></a>Étapes d’implémentation de haut niveau

1. Choisissez l’un des nombreux langages pris en charge par gRPC : C#, C++, Dart, Go, Java, Node, Objective-C, PHP, Python, Ruby.
1. Implémentez un serveur gRPC qui va communiquer avec Video Analyzer à l’aide des [fichiers proto3](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/inference-srv-container-process.svg" alt-text="Serveur gRPC qui va communiquer avec Video Analyzer à l’aide des fichiers proto3":::

    Au sein de ce service :
    1. Gérez l’échange de messages de description de session entre le serveur et le client.
    1. Gérez les [exemples de messages](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto) et retournez les résultats.

        1. Appelez votre moteur d’inférence qui utilise un modèle entraîné pour effectuer des inférences sur les messages entrants.
        1. Recevez les résultats d’inférence du moteur, packagez-les sous forme d’exemple de contenu multimédia, puis soumettez-les à nouveau à Video Analyzer à l’aide du fichier [inferencing.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/inferencing.proto).

            Vous pouvez également appeler une fonction de transformation multimédia sur l’exemple de fichier multimédia.
    1. Déployez l’implémentation du serveur gRPC. Pour cela, deux méthodes sont à votre disposition :

        1. Effectuer le déploiement sous forme de module IoT colocalisé avec le module Video Analyzer
        1. Effectuer le déploiement sous forme de module IoT sur un nœud accessible en réseau (localement ou dans le cloud), qui peut échanger des données avec le module Video Analyzer.
    1. Configurez une topologie de pipeline Video Analyzer avec le module Video Analyzer, puis faites-la pointer vers le serveur gRPC.

### <a name="recommendation"></a>Recommandation

En cas de colocalisation sur le même nœud, `shared memory` peut être utilisé pour obtenir des performances optimales. Vous devez pour cela vous servir des fonctionnalités de mémoire partagée Linux exposées par le langage/l’environnement de programmation.

1. Ouvrez le descripteur de la mémoire partagée Linux.
1. À la réception d’une trame, accédez au décalage d’adresse dans la mémoire partagée.
1. Accusez réception de l’achèvement du traitement des images afin que sa mémoire puisse être récupérée par Video Analyzer.

> [!NOTE]
> Lors de l’utilisation d’un module d’extension gRPC pour l’inférence avec la mémoire partagée, le module périphérique Video Analyzer et le module d’extension doivent tous deux être exécutés dans le même [groupe et utilisateur](https://docs.docker.com/engine/reference/builder/#user)

## <a name="create-a-grpc-inference-server"></a>Créer un serveur d’inférence gRPC

Vous allez à présent créer un module IoT Edge (IA externe) qui accepte les images vidéo de Video Analyzer à l’aide de messages [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) via la mémoire partagée, classifie les images selon qu’elles sont **sombres** ou **claires**, puis retourne les résultats d’inférence au récepteur de messages IoT Hub dans Video Analyzer en utilisant le schéma de métadonnées d’inférence.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/external-ai.png" alt-text="Créer un module de IoT Edge (IA externe)":::

Ce serveur d’inférence gRPC est une application console .NET Core conçue pour gérer les messages [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) envoyés entre Video Analyzer et votre IA personnalisée. Voici le flux de messages entre Video Analyzer et le serveur d’inférence gRPC :

1. Video Analyzer envoie un descripteur de stream multimédia (consultez [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)), qui définit les informations de stream multimédia à envoyer au serveur, suivies des images vidéo, en tant que message [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) via la session de stream gRPC.
1. Le serveur valide et accuse réception du descripteur de flux et définit la méthode de transfert de données souhaitée.
1. Video Analyzer commence alors à envoyer les fichiers MediaSample qui contiennent les images vidéo.
1. Le serveur analyse les images vidéo au fur et à mesure qu’il les reçoit, puis commence à les traiter à l’aide du processeur d’images que vous avez défini.
1. Le serveur retourne ensuite les résultats d’inférence en tant que messages [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) dès qu’ils sont disponibles.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/grpc-external-srv.png" alt-text="Créer un serveur d’inférence gRPC":::

Les images vidéo peuvent être transférées via la [mémoire partagée](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) ou peuvent être incorporées dans le message protobuf. Vous pouvez configurer le mode de transfert de données dans la topologie de pipeline AVA pour déterminer la façon dont les images doivent être transférées. Pour ce faire, vous devez configurer l’élément **dataTransfer** de la propriété `GrpcExtension`, comme indiqué ci-dessous :

Incorporé :

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Mémoire partagée :

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Lors de la communication sur une mémoire partagée, la valeur de IpcMode doit être définie sur **shareable** et dans le module du serveur gRPC, définissez la valeur de IpcMode sur **container:{NOM_CONTENEUR}** . Ces paramètres doivent être définis dans le fichier manifeste de déploiement utilisé pour déployer les modules dans IoT Hub Azure. Vous trouverez ci-dessous un exemple des options de conteneur à utiliser lors de la configuration des modules IoT Edge.

Module Azure Video Analyzer :

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

Module d’extension gRPC :

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:avaedge"
    }
}
```

> [!NOTE]
> Vérifiez que vous pouvez accéder à la zone de mémoire partagée de **container:avaedge** dans grpcExtension.

## <a name="sample-grpc-server"></a>Exemple de serveur gRPC

Pour comprendre la façon dont le serveur gRPC est développé, passons en revue notre exemple de code.

1. Clonez le dépôt à partir du lien GitHub [https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp).
1. Lancez VSCode et accédez au dossier /src/edge/modules/grpcExtension.
1. Nous allons parcourir rapidement les fichiers :

    1. **Program.cs**: il s’agit du point d’entrée de l’application. Il est responsable de l’initialisation et de la gestion du serveur gRPC, qui agira en tant qu’hôte. Dans notre exemple, le port à écouter pour les messages gRPC entrants en provenance d’un client gRPC (par exemple Azure Video Analyzer) est spécifié par l’élément de configuration grpcBindings dans le fichier appConfig.json.

        ```json
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\PipelineExtensionService.cs** : cette classe est chargée de gérer les messages [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc). Elle lit l’image dans le message, appelle le processeur d’images (ImageProcessor) et écrit les résultats de l’inférence.
      Maintenant que vous avez configuré et initialisé les connexions de port du serveur gRPC, voyons comment traiter les messages gRPC entrants.

        1. Une fois qu’une session gRPC est établie, le tout premier message que le serveur gRPC reçoit du client (Azure Video Analyzer) est un MediaStreamDescriptor défini dans le fichier [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto).

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        1. Dans notre implémentation de serveur, la méthode `ProcessMediaStreamDescriptor` valide la propriété MediaDescriptor de MediaStreamDescriptor pour un fichier vidéo, puis configure le mode de transfert de données (qui utilise la mémoire partagée ou le mode de transfert d’image incorporée) selon les valeurs que vous spécifiez dans la topologie et le fichier de modèle de déploiement utilisé.
        1. Lors de la réception du message et de la configuration du mode de transfert de données, le serveur gRPC renvoie le message MediaStreamDescriptor au client en tant qu’accusé de réception et établit ainsi une connexion entre le serveur et le client.
        1. Une fois que Video Analyzer a reçu l’accusé de réception, il commence à transférer le stream multimédia vers le serveur gRPC. Dans notre implémentation de serveur, la méthode `ProcessMediaStream` traitera le MediaStreamMessage entrant. Le MediaStreamMessage est également défini dans le fichier [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.


              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        1. Selon la valeur de `batchSize` dans *appconfig.json*, notre serveur continue à recevoir les messages et stocke les images vidéo dans une liste. Une fois la limite de batchSize atteinte, la fonction appelle la fonction ou le fichier qui traitera l’image. Dans notre cas, la méthode appelle un fichier nommé **BatchImageProcessor.cs**.
    1. **Processors\BatchImageProcessor.cs** : cette classe est chargée de traiter la ou les images. Nous avons utilisé un modèle de classification d’image dans cet exemple. Pour chaque image traitée, l’algorithme utilisé est le suivant :

        1. Convertit l’image en tableau d’octets pour traitement. Consultez la méthode : `GetBytes(Bitmap image)`

            L’exemple de processeur que nous utilisons ne prend en charge que les trames d’image encodées JPG et « None » comme format de pixel. Si votre processeur personnalisé prend en charge un encodage et/ou un format différents, mettez à jour la méthode `IsMediaFormatSupported` de la classe du processeur.
        1. À l’aide de la classe [ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1), convertissez l’image en nuances de gris. Consultez la méthode : `ToGrayScale(Image source)`
        1. Une fois que vous avez obtenu l’image en nuances de gris, calculez la moyenne des octets de nuances de gris.
        1. Si la valeur moyenne est inférieure à 127, classifiez l’image comme étant « sombre », sinon classifiez-le comme étant « claire » avec une valeur de confiance de 1.0. Consultez la méthode : `ProcessImage(List<Image> images)`

    Vous pouvez ajouter votre propre logique de processeur en modifiant cette classe ou en ajoutant une nouvelle classe et en implémentant la méthode :

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images)
    ```

    Une fois que vous avez ajouté la nouvelle classe, vous devez mettre à jour le fichier **PipelineExtensionService.cs** pour qu’il instancie la classe et appelle la méthode ProcessImage sur celle-ci afin d’exécuter la logique de traitement.

## <a name="connect-with-video-analyzer-module"></a>Se connecter avec le module Video Analyzer

Une fois le module d’extension gRPC créé, nous allons créer et déployer la topologie de pipeline.

1. À l’aide de Visual Studio Code, suivez [ces instructions](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) pour vous connecter à Docker.
1. Dans Visual Studio Code, accédez à *src/edge*. Votre fichier  *.env* et quelques fichiers de modèle de déploiement s’affichent.

    Le **modèle de déploiement** fait référence au manifeste de déploiement de l’appareil de périphérie. Il inclut des valeurs d’espace réservé. Le fichier .env inclut les valeurs de ces variables.

    Sélectionnez ensuite **Créer et envoyer (push) la solution IoT Edge**. Utilisez *src/edge/deployment.grpc.template.json* pour cette étape.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/build-push-iot-edge-solution.png" alt-text="Se connecter avec le module Video Analyzer":::

    Cette action permet de créer le module de serveur grpc et d’envoyer (via push) l’image à Azure Container Registry.
    Vérifiez que les variables d’environnement `CONTAINER_REGISTRY_USERNAME_myacr` et `CONTAINER_REGISTRY_PASSWORD_myacr` sont définies dans le fichier *.env*.
1. Accédez au dossier *src/cloud-to-device-console-app*. Ce dernier contient votre fichier *appsettings.json* et quelques autres fichiers :

    * **c2d-console-app.csproj** : fichier projet pour Visual Studio Code.
    * **operations.json** : liste des opérations que vous voulez que le programme exécute.
    * **Program.cs** : exemple de code de programme. Ce code :

        * Il charge les paramètres de l’application.
        * Appelle les méthodes directes exposées par le module Azure Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses méthodes directes.
        * S’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre TERMINAL et d’examiner les événements qui ont été générés par le module dans la fenêtre SORTIE.
        * Invoque des méthodes directes pour nettoyer des ressources.
1. Modifiez le fichier operations.json :

    * Changez le lien de la topologie de pipeline :

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json`
        * Sous `livePipelineSet`, modifiez le nom de la topologie de pipeline pour qu’il corresponde à la valeur du lien précédent :<br/>`"topologyName": "EVRtoVideoSinkByGrpcExtension"`
        * Sous `pipelineTopologyDelete`, modifiez le nom :<br/>`"name": "EVRtoVideoSinkByGrpcExtension"`

    La topologie doit définir une adresse d’extension. À titre d’exemple, vous pouvez créer un paramètre et l’utiliser dans le nœud GrpcExtension :
    * Paramètre d’adresse d’extension

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC AVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Configuration

        ```
        {
            "@apiVersion": "1.1",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "75"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.VideoAnalyzer.ImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Générez et déployez le manifeste de déploiement IoT Edge

Le manifeste de déploiement définit les modules qui sont déployés sur un périphérique et les paramètres de configuration de ces modules. Effectuez les étapes suivantes pour générer un manifeste à partir du fichier de modèle, puis déployez-le sur le périphérique.
1. Cette étape permet de créer le manifeste de déploiement IoT Edge dans *src/edge/config/deployment.grpc.amd64.json*. Cliquez avec le bouton droit sur ce fichier, puis sélectionnez **Créer un déploiement pour un seul appareil**.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/create-deployment-single-device.png" alt-text="Générer et déployer le manifeste de déploiement IoT Edge":::

1. Visual Studio Code vous demande ensuite de sélectionner un appareil IoT Hub. Sélectionnez votre appareil IoT Hub, `avasample-iot-edge-device`.
À ce stade, le déploiement des modules de périphérie sur votre appareil IoT Edge a démarré. Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche de Visual Studio Code. Vous devez voir qu’un nouveau module a été déployé. Il se nomme avaextension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/devices.png" alt-text="Un nouveau module nommé avaextension a été déployé":::

## <a name="next-steps"></a>Étapes suivantes

* Suivez les étapes de **préparation de l’analyse des événements** mentionnées dans le démarrage rapide Analyser une vidéo en direct avec votre modèle pour exécuter l’exemple et interpréter les résultats.
* Consultez également nos exemples de topologies gRPC : [gRPCExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json) et [EVROnMotionPlusGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json).
