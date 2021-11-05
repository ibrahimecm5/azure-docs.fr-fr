---
title: Résoudre les problèmes liés à Azure Video Analyzer - Azure
description: Cet article décrit les étapes de résolution des problèmes liés au module périphérique Azure Video Analyzer.
ms.topic: troubleshooting
ms.date: 10/22/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89c14c4a8199f811359d76550b5e281c857a1469
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097704"
---
# <a name="troubleshoot-azure-video-analyzer"></a>Résoudre les problèmes liés à Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

Cet article décrit les étapes de résolution des problèmes liés au module périphérique Azure Video Analyzer.

## <a name="troubleshoot-deployment-issues"></a>Résoudre les problèmes de déploiement

### <a name="diagnostics"></a>Diagnostics

Dans le cadre de votre déploiement de Video Analyzer, vous allez configurer des ressources Azure telles qu’IoT Hub et des appareils IoT Edge. Pour commencer à diagnostiquer les problèmes, assurez-vous toujours que l’appareil Edge est correctement configuré en suivant les instructions ci-dessous :

1. [Exécutez la commande `check`](../../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Vérifiez votre version d’IoT Edge](../../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Vérifiez l’état du gestionnaire de sécurité IoT Edge et de ses journaux](../../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Affichez les messages acheminés via le hub IoT Edge](../../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Redémarrez les conteneurs](../../../iot-edge/troubleshoot.md#restart-containers).
1. [Vérifiez vos règles de configuration de pare-feu et de port](../../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="issues-when-using-arm-template"></a>Problèmes liés à l'utilisation de modèles ARM

Si vous rencontrez des problèmes pendant l’utilisation du modèle ARM (le bouton `Deploy to Azure`), suivez les étapes de ce [guide de démarrage rapide](get-started-detect-motion-emit-events-portal.md) pour déployer manuellement les ressources nécessaires. 

### <a name="pre-deployment-issues"></a>Problèmes liés au prédéploiement

Si l’infrastructure Edge est correcte, vous pouvez rechercher des problèmes dans le fichier du manifeste de déploiement. Pour déployer le module Video Analyzer sur l’appareil IoT Edge avec d’autres modules IoT, utilisez un manifeste de déploiement qui contient le hub IoT Edge, l’agent IoT Edge et d’autres modules avec leurs propriétés. Vous pouvez utiliser la commande suivante pour déployer le fichier manifeste :

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id avasample-iot-edge-device --content <path-to-deployment_manifest.json>
```

Si le code JSON n’est pas bien formé, vous pouvez recevoir l’erreur suivante : &nbsp;&nbsp;&nbsp; **Impossible d’analyser le code JSON du fichier : « \<deployment manifest.json\> » pour l’argument « content ». Exception : « Données supplémentaires : ligne 101 colonne 1 (char 5325) »**

Si vous rencontrez cette erreur, nous vous recommandons de vérifier dans le fichier JSON s’il manque des crochets ou si la structure du fichier présente d’autres problèmes. Pour valider la structure des fichiers, vous pouvez utiliser un client tel que [Notepad++ avec le plug-in JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou un outil en ligne tel que [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-iot-edge-runtime-response"></a>Pendant le déploiement : réponse du runtime IoT Edge

Une fois le module Video Analyzer déployé sur l’appareil IoT Edge, vous pouvez utiliser le portail Azure pour exécuter un diagnostic du [Runtime IoT Edge](../../../iot-edge/iot-edge-runtime.md).

1. Dans le portail Azure, accédez au hub IoT connecté à votre appareil IoT Edge.
1. Recherchez **Gestion automatique des appareils**, puis sélectionnez **IoT Edge**.
1. Dans la liste des appareils périphériques, sélectionnez celui que vous voulez diagnostiquer et ouvrez le panneau de gestion.

   ![Capture d’écran du portail Azure affichant une liste d’appareils Edge](./media/troubleshoot/ava-sample-device.png)

1. Vérifiez si le code de réponse du runtime IoT Edge est _200-OK_. Les autres codes de réponse possibles sont :

   - 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
   - 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
   - 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
   - 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
   - 500 - Une erreur s’est produite dans le runtime IoT Edge.

   > [!TIP]
   > Si vous rencontrez des problèmes lors de l’exécution de modules Azure IoT Edge dans votre environnement, utilisez l’article **[Étapes de diagnostic standard d’Azure IoT Edge](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** qui vous guidera pour la résolution des problèmes et les diagnostics.

### <a name="post-deployment-reported-properties-and-direct-methods"></a>Après le déploiement : propriétés rapportées et méthodes directes

Dans le portail Azure, sélectionnez et ouvrez le panneau de gestion du module périphérique Video Analyzer.
Commencez par vérifier les propriétés rapportées et souhaitées, qui peuvent vous aider à comprendre si les propriétés du module sont synchronisées avec le déploiement. Si ce n’est pas le cas, vous pouvez redémarrer votre appareil IoT Edge. Si les colonnes **Spécifié dans le déploiement** et **Rapporté par l’appareil** indiquent _Oui_, vous pouvez appeler des méthodes directes sur le module Video Analyzer en cliquant sur l’option de menu **Méthode directe**.

Vous pouvez utiliser le guide des [Méthodes directes](direct-methods.md) pour appeler un certain nombre de méthodes, notamment les méthodes simples comme `pipelineTopologyList`. Ce guide précise également les charges utiles de demande et de réponse attendues ainsi que les codes d’erreur. Si les méthodes directes simples ont réussi, le module périphérique Video Analyzer fonctionne correctement.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/troubleshoot/direct-method.png" alt-text="Capture d’écran du volet Méthode directe pour le module IoT Edge." lightbox="./media/troubleshoot/direct-method.png":::

Gardez à l’esprit les points suivants :
1. Si vous recevez un code de statut `501 code`, vérifiez que le nom de la méthode directe est exact. Si le nom de la méthode et la charge utile de la demande sont justes, vous devez obtenir des résultats avec le code de réussite 200.
1. Si la charge utile de demande est inexacte, vous obtenez un code de statut `400 code` et une charge utile de réponse indiquant un code d’erreur et un message utiles pour diagnostiquer le problème de l’appel de méthode directe.

### <a name="post-deployment-diagnostic-logs-for-issues"></a>Après le déploiement : journaux de diagnostic pour les problèmes

Les journaux de conteneur de votre module IoT Edge doivent contenir des informations de diagnostic pour vous aider à déboguer les problèmes que vous rencontrez pendant l’analyse de la vidéo en direct. Vous pouvez [consulter les journaux de conteneur pour voir s’ils indiquent des problèmes](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues) et auto-diagnostiquer le problème.

Si vous avez exécuté toutes les vérifications précédentes et que vous rencontrez toujours des problèmes, rassemblez les journaux à partir de l’appareil IoT Edge [avec la commande `support bundle`](../../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) pour une analyse plus approfondie par l’équipe Azure. Vous pouvez [nous contacter](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pour obtenir de l’aide et envoyer les journaux collectés.

## <a name="common-error-resolutions"></a>Résolutions des erreurs courantes

Le module périphérique Video Analyzer fonctionne en collaboration avec les modules agent et hub d’IoT Edge. Certaines erreurs courantes que vous pouvez rencontrer pendant ce déploiement sont dues à des problèmes de l’infrastructure IoT sous-jacente. Les erreurs comprennent :

- [L’agent IoT Edge s’arrête après environ une minute](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
- [L’agent IoT Edge ne peut pas accéder à l’image d’un module (403)](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
- [Le module de l’agent IoT Edge rapporte le message « fichier config vide » et aucun module ne démarre sur l’appareil](../../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
- [Le hub IoT Edge ne parvient pas à démarrer](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
- [Le démon de sécurité IoT Edge échoue en raison d’un nom d’hôte non valide](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
- [Le module Video Analyzer ou tout autre module IoT Edge personnalisé ne parvient pas à envoyer un message au hub IoT Edge avec l’erreur 404](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
- [Le module IoT Edge est correctement déployé, puis disparaît de l’appareil](../../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

  > [!TIP]
  > Si vous rencontrez des problèmes lors de l’exécution de modules Azure IoT Edge dans votre environnement, utilisez l’article **[Étapes de diagnostic standard d’Azure IoT Edge](../../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** qui vous guidera pour la résolution des problèmes et les diagnostics.

Si vous rencontrez des problèmes supplémentaires pour lesquels vous avez besoin d’aide, **[collectez des journaux et soumettez un ticket de support](#collect-logs-for-submitting-a-support-ticket)** . Vous pouvez également nous contacter en nous envoyant un e-mail à l’adresse **[videoanalyzerhelp@microsoft.com](mailto:videoanalyzerhelp@microsoft.com)** .

### <a name="working-with-external-modules"></a>Utilisation de modules externes

Quand vous utilisez des processeurs d’extension de pipeline, vous pouvez étendre le pipeline pour envoyer et recevoir des données dans d’autres modules IoT Edge à l’aide des protocoles HTTP ou gRPC. À titre [d’exemple spécifique](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/httpExtension), ce pipeline en direct peut envoyer des trames vidéo sous forme d’images à un module d’inférence externe comme Yolo v3 et recevoir des résultats analytiques JSON en utilisant le protocole HTTP. Dans ce type de topologie, la destination des événements est principalement le hub IoT. Dans les cas où vous ne voyez pas les événements d’inférence sur le hub, vérifiez les éléments suivants :

- Regardez si le hub sur lequel est publié le pipeline en direct est le même que celui que vous examinez. Lorsque vous créez plusieurs déploiements, vous pouvez vous retrouver avec plusieurs hubs et vous risquez d’inspecter le mauvais hub d’événement par erreur.
- Sur le Portail Azure, vérifiez si le module externe est déployé et s’il fonctionne. Dans cette image d’exemple, rtspsim, yolov3, tinyyolov3 et logAnalyticsAgent sont des modules IoT Edge qui s’exécutent à l’extérieur du module avaedge.

  [ ![Capture d’écran de l’état d’exécution des modules dans Azure IoT Hub.](./media/troubleshoot/iot-hub-azure.png) ](./media/troubleshoot/iot-hub-azure.png#lightbox)

- Vérifiez que vous envoyez des événements au point de terminaison d’URL approprié. Le conteneur d’IA externe expose une URL et un port par lesquels il reçoit et renvoie les données des requêtes POST. Cette URL est spécifiée en tant que propriété `endpoint: url` pour le processeur d’extension HTTP. Comme indiqué dans l’[URL de topologie](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json), le point de terminaison est défini sur le paramètre d’URL d’inférence. Assurez-vous que la valeur par défaut du paramètre ou la valeur transmise est exacte. Vous pouvez effectuer un test pour déterminer s’il fonctionne à l’aide de l’URL du client (cURL).

  À titre d’exemple, voici un conteneur Yolo v3 qui s’exécute sur l’ordinateur local avec l’adresse IP 172.17.0.3.

  ```shell
  curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
  ```

  Résultat retourné :

  ```json
  {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
  ```

  > [!TIP]
  > Utilisez la **[commande Docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)** pour trouver l’adresse IP de l’ordinateur.

- Si vous exécutez un ou plusieurs pipelines en direct qui utilisent le processeur d’extension de pipeline, vous devez utiliser le champ `samplingOptions` pour gérer la fréquence d’images par seconde (FPS) du flux vidéo.

  - Dans certains cas, lorsque le processeur ou la mémoire de l’ordinateur Edge est fortement sollicité, vous pouvez perdre certains événements d’inférence. Pour résoudre ce problème, définissez une valeur faible pour la propriété `maximumSamplesPerSecond` dans le champ `samplingOptions`. Vous pouvez choisir la valeur 0,5 ("maximumSamplesPerSecond": "0.5") sur chacune des instances du pipeline, puis réexécuter l’instance pour rechercher des événements d’inférence sur le hub.

### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Plusieurs méthodes directes en parallèle – échec au terme du délai d’expiration

Video Analyzer sur IoT Edge fournit un modèle de programmation basé sur une méthode directe qui vous permet de configurer plusieurs topologies et plusieurs pipelines. Dans le cadre de la configuration de la topologie et du pipeline, vous allez procéder à plusieurs appels de méthode directe sur le module IoT Edge. Si vous invoquez ces multiples appels de méthode en parallèle, en particulier ceux qui démarrent et arrêtent les pipelines, vous pourriez connaître un échec au terme du délai d’expiration, comme ci-dessous :

La méthode d’Assembly Initialization Microsoft.Media.VideoAnalyzer.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync a levé l’exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException : <br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution.`

Nous vous recommandons de _pas_ appeler des méthodes directes en parallèle. Appelez-les de manière séquentielle (c’est-à-dire, effectuez un appel de méthode directe seulement après la fin du précédent).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Collecter les journaux pour l’envoi d’un ticket de support

Lorsque les étapes de résolution des problèmes auto-guidée ne résolvent pas votre problème, accédez au portail Azure et [ouvrez un ticket de support](../../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Les journaux peuvent contenir des informations d’identification personnelle telles que votre adresse IP. Toutes les copies locales des journaux seront supprimées dès que nous aurons terminé leur examen et fermé le ticket de support.

Pour collecter les journaux qui doivent être ajoutés au ticket, suivez les instructions ci-dessous dans l’ordre et chargez les fichiers journaux dans le volet **Détails** de la demande de support.

1. [Configurer le module Video Analyzer pour collecter des journaux détaillés](#configure-video-analyzer-module-to-collect-verbose-logs)
1. [Activer les journaux de débogage](#video-analyzer-edge-module-debug-logs).
1. Reproduisez le problème
1. Redémarrez le module Edge Video Analyzer. 
    > [!NOTE]
    > Cette étape est nécessaire pour terminer normalement le module Edge et obtenir tous les fichiers journaux dans un format utilisable sans supprimer d’événements.   
    
    Sur l’appareil IoT Edge, utilisez la commande suivante après avoir remplacé `<avaedge>` par le nom de votre module périphérique Video Analyzer :
    
    ```cmd
    sudo iotedge restart <avaedge>
    ```

   Vous pouvez également redémarrer les modules à distance sur le Portail Azure. Pour plus d’informations, consultez [Surveillance et résolution des problèmes des appareils IoT Edge sur le Portail Azure](../../../iot-edge/troubleshoot-in-portal.md).
1. Connectez-vous à la machine virtuelle sur la page **IoT Hub** du portail.

   1. Compressez tous les fichiers dans le dossier _debugLogs_. Ces fichiers journaux ne sont pas destinés à l’auto-diagnostic. Ils permettent aux ingénieurs Azure d’analyser vos problèmes.

      - Dans la commande suivante, veillez à remplacer **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** par l’emplacement des journaux de débogage sur l’appareil Edge que vous avez configuré à **l’Étape 2**.

        ```
        sudo apt install zip unzip
        zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE
        ```

   1. Joignez le fichier _debugLogs.zip_ au ticket de support.

1. Exécutez la [commande de bundle de support](#use-the-support-bundle-command), collectez les journaux et joignez-les au ticket de support.

### <a name="configure-video-analyzer-module-to-collect-verbose-logs"></a>Configurer le module Video Analyzer pour collecter des journaux détaillés

Configurez votre module Video Analyzer pour collecter des journaux détaillés en définissant `logLevel` et `logCategories` de la façon suivante :

```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Il existe deux endroits pour le faire :

- Sur le **portail Azure**, mettez à jour les propriétés Jumeau d’identité de module du module Video Analyzer [![Propriétés Jumeau d’identité de module](media/troubleshoot/module-twin.png)](media/troubleshoot/module-twin.png#lightbox).
- Dans votre fichier **manifeste de déploiement**, vous pouvez également ajouter ces entrées dans le nœud Propriétés du module Video Analyzer.

### <a name="use-the-support-bundle-command"></a>Utiliser la commande support-bundle

Lorsque vous devez récupérer des journaux d'activité depuis un périphérique IoT Edge, la façon la plus simple est d’utiliser la commande `support-bundle`. Cette commande récupère :

- Les journaux de module
- Les journaux du gestionnaire de sécurité IoT Edge et du moteur de conteneur
- La sortie JSON du contrôle IoT Edge
- Des informations de débogage utiles

1. Exécutez la commande `support-bundle` avec l’indicateur _--since_ pour spécifier la période que vos journaux doivent couvrir. Par exemple, « 2 h » vous permettra de récupérer les journaux des deux dernières heures. Vous pouvez modifier la valeur de cet indicateur pour inclure les journaux de différentes périodes.

   ```shell
   sudo iotedge support-bundle --since 2h
   ```

   Cette commande crée un fichier nommé _support_bundle.zip_ dans le répertoire où vous avez exécuté la commande.

1. Joignez le fichier _support_bundle.zip_ au ticket de support.

### <a name="video-analyzer-edge-module-debug-logs"></a>Journaux de débogage du module périphérique Video Analyzer

Afin de configurer le module périphérique Video Analyzer pour qu’il puisse générer des journaux de débogage, effectuez les actions suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.
1. Dans le volet gauche, sélectionnez **IoT Edge**.
1. Dans la liste des appareils, sélectionnez l’ID de l’appareil cible.
1. En haut du volet, sélectionnez **Définir les modules**.

   ![Capture d’écran du bouton « Définir les modules » dans le portail Azure.](media/troubleshoot/set-modules.png)

1. Dans la section **Modules IoT Edge**, sélectionnez **avaedge**.
1. Sélectionnez **Jumeau d’identité de module**. Un volet modifiable s’ouvre.
1. Sous **Clé souhaitée**, ajoutez la paire clé-valeur suivante :

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > Cette commande lie les dossiers de journaux entre l’appareil Edge et le conteneur. Si vous souhaitez collecter les journaux à un autre emplacement sur l’appareil :
   >
   > 1. Créez une liaison pour l’emplacement du journal de débogage dans la section **Liaisons**, en remplaçant **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** et **$DEBUG_LOG_LOCATION** par l’emplacement de votre choix : `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
   > 2. Utilisez la commande suivante, en remplaçant **$DEBUG_LOG_LOCATION** par l’emplacement utilisé à l’étape précédente : `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. Sélectionnez **Enregistrer**.

1. Vous pouvez arrêter la collecte des journaux en définissant la valeur dans **Jumeau d’identité de module** sur _Null_. Retournez sur la page **Module Identity Twin** et mettez à jour le paramètre suivant comme suit :

   `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Meilleures pratiques de journalisation

L’article [Surveillance et journalisation](monitor-log-edge.md) aide à comprendre la taxonomie et à générer des journaux permettant de déboguer les problèmes liés à Video Analyzer.

Étant donné que l’implémentation du serveur gRPC diffère selon les langages, il n’existe pas de méthode standard pour ajouter la journalisation dans le serveur.

Par exemple, si vous générez un serveur gRPC à l’aide de .NET Core, le service gRPC ajoute les journaux sous la catégorie **gRPC**. Pour activer les journaux détaillés à partir de gRPC, configurez les préfixes gRPC au niveau de débogage dans votre fichier appsettings.json en ajoutant les éléments suivants à la sous-section LogLevel de Logging :

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information",
      "Grpc": "Debug"
       }
  }
}
```

Vous pouvez également configurer cet aspect dans le fichier Startup.cs avec ConfigureLogging :

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {

           logging.AddFilter("Grpc", LogLevel.Debug);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });

```

L’article [Journalisation et diagnostics dans gRPC sur .NET](/aspnet/core/grpc/diagnostics?view=aspnetcore-5.0&preserve-view=true) donne des conseils pour collecter certains journaux de diagnostic à partir d’un serveur gRPC.

### <a name="a-failed-grpc-connection"></a>Échec d’une connexion gRPC

Si un pipeline est actif et diffusé en streaming à partir d’une caméra, la connexion est maintenue par Video Analyzer.

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Surveillance et équilibrage de la charge des ressources de processeur et de GPU lorsque ces dernières deviennent des goulots d’étranglement

Le module périphérique Video Analyzer ne supervise pas les ressources matérielles. Les développeurs doivent utiliser les solutions de monitoring des fabricants de matériel. Toutefois, si vous utilisez des conteneurs Kubernetes, vous pouvez surveiller l’appareil à l’aide du [tableau de bord Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Les documents gRPC dans .NET Core partagent également des informations précieuses sur les [Meilleures pratiques en matière de niveau de performance](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true) et [l’Équilibrage de charge](/aspnet/core/grpc/performance?view=aspnetcore-5.0&preserve-view=true#load-balancing).

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Résolution des problèmes d’un serveur d’inférence qui ne reçoit aucune trame et donne une erreur de protocole « inconnu »

Il existe plusieurs moyens d’obtenir des informations sur le problème.

- Incluez la catégorie de journal **mediaPipeline** dans les propriétés souhaitées du module Video Analyzer et vérifiez que le niveau de consignation est défini sur `Information`.
- Pour tester la connectivité réseau, vous pouvez exécuter la commande suivante à partir de l’appareil Edge.

  ```
  sudo docker exec avaedge /bin/bash -c "apt update; apt install -y telnet; telnet <inference-host> <inference-port>"
  ```

  Si la commande génère une courte chaîne de texte brouillé, c’est le signe que Telnet a pu ouvrir une connexion à votre serveur d’inférence et ouvrir un canal gRPC binaire. Dans le cas contraire, Telnet signale une erreur réseau.

- Dans votre serveur d’inférence, vous pouvez activer une journalisation supplémentaire dans la bibliothèque gRPC. Elle vous fournira des informations supplémentaires sur le canal gRPC proprement dit. La procédure varie en fonction du langage. Voici des instructions pour [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1).

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Prélèvement d’images supplémentaires dans la mémoire tampon de gRPC sans renvoyer le résultat de la première mémoire tampon

Dans le cadre du contrat de transfert de données gRPC, tous les messages que Video Analyzer envoie au serveur d’inférence gRPC doivent faire l’objet d’un accusé de réception. Tout défaut d’accusé de réception d’une trame d’image rompt le contrat de données, ce qui peut aboutir à des situations indésirables.

Quand vous utilisez votre serveur gRPC avec le module périphérique Video Analyzer, utilisez la mémoire partagée pour obtenir des performances optimales. Vous devez pour cela vous servir des fonctionnalités de mémoire partagée Linux exposées par le langage/l’environnement de programmation.

1. Ouvrez le descripteur de la mémoire partagée Linux.
1. À la réception d’une trame, accédez au décalage d’adresse dans la mémoire partagée.
1. Confirmez la fin du traitement des trames pour que la mémoire utilisée puisse être récupérée par le module périphérique Video Analyzer

   > [!NOTE]
   > Si vous tardez trop à envoyer l’accusé de réception de l’image à Video Analyzer, la mémoire partagée risque de saturer, ce qui peut entraîner des suppressions de données.

1. Stockez chaque trame dans une structure de données de votre choix (liste, tableau, etc.) sur le serveur d’inférence.
1. Vous pouvez exécuter votre logique de traitement dès vous disposez du nombre souhaité de trames d’image.
1. Retournez le résultat d’inférence au module périphérique Video Analyzer quand il est prêt.

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Enregistrement vidéo basé sur les événements dans le Cloud et lecture à partir du Cloud](record-event-based-live-video.md)
