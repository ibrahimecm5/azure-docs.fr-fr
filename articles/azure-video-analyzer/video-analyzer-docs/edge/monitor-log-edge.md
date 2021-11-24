---
title: Supervision et journalisation - Azure
description: Cet article fournit une vue d’ensemble de la surveillance et de la journalisation dans Azure Video Analyzer.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed86a5de4fd5d65ec3e5a3b8112cb96c95f01811
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399936"
---
# <a name="monitor-and-log-on-iot-edge"></a>Surveiller et journaliser sur IoT Edge

![icône de périphérie](media/env-icon/edge.png)  
Vous pouvez également consulter [Analyser et journaliser en le service](../cloud/monitor-log-cloud.md).

---

Cet article explique comment recevoir des événements pour la supervision à distance à partir du module IoT Edge Azure Video Analyzer. 

Vous apprendrez également à contrôler les journaux générés par le module.

## <a name="taxonomy-of-events"></a>Taxonomie des événements

Video Analyzer basé sur IoT Edge émet des événements, ou données de télémétrie, selon la taxonomie suivante :

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagramme illustrant la taxonomie des événements.":::

* Opérationnel : Événements générés par les actions d’un utilisateur ou lors de l’exécution d’un [pipeline](../pipeline.md)
  
   * Volume : Censé être faible (quelques occurrences par minute, voire moins).
   * Exemples :

      - Pipeline en direct activé
      
      - Pipeline en direct désactivé
      
      *Exemple d’événement opérationnel*
      
      ```json
      {
         "body": {
             "outputType": "filePath",
             "outputLocation": "/var/media/Sample-1-fileSink/sampleFilesFromEVR-motion-fileSinkOutput-20210426T181911Z.mp4"
           },
           "properties": {
             "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
             "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
             "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
             "eventTime": "2021-04-26T18:19:13.298Z",
             "dataVersion": "1.0"
          },
      }
      ```
* Diagnostics : Événements permettant de diagnostiquer les problèmes de performances

   * Volume : Peut être élevé (plusieurs occurrences par minute)
   * Exemples :
   
      - Informations [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) RTSP (illustré dans l’exemple suivant)       
      - Erreurs lors de l’échec d’une connexion à un élément tel qu’une caméra ou une extension d’intelligence artificielle
      
    *Exemple d’événement de diagnostic*
  
    ```json
    {
      "body": {
        "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
      },
      "applicationProperties": {
        "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
        "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
        "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
        "eventTime": "2021-04-26T18:15:13.298Z",
        "dataVersion": "1.0"
      }
    }
    ```
* Analyse : événements générés à la suite d’une analyse vidéo

   * Volume : Peut être élevé (plusieurs occurrences par minute ou plus)
   * Exemples :
     
      - Mouvement détecté (illustré dans l’exemple suivant) 
      
      - Résultat de l’inférence
     
      *Exemple d’événement d’analyse*
        
      ```json
      {
        "body": {
          "timestamp": 143039375044290,
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
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
          "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/md",
          "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
          "eventTime": "2021-04-26T18:15:13.298Z",
          "dataVersion": "1.0"
        }
      }
      ```
        
Les événements émis par le module sont envoyés au [hub IoT Edge](../../../iot-edge/iot-edge-runtime.md#iot-edge-hub). De là, ils peuvent être acheminés vers d’autres destinations. 

### <a name="events-and-video-playback"></a>Événements et lecture vidéo

Comme indiqué ci-dessus, une valeur `eventTime` est associée aux événements générés dans le cadre d’une analyse vidéo. Si vous avez [enregistré la vidéo en direct](../video-recording.md) dans le cadre de votre topologie de pipeline, cette information vous aide de trouver où, dans la vidéo enregistrée, un événement particulier se produit. Vous pouvez charger l’enregistrement vidéo dans le [widget du lecteur de Video Analyzer](../player-widget.md), et utiliser les contrôles de celui-ci pour rechercher la date et l’heure qui vous intéressent. Si votre pipeline a impliqué l’utilisation d’un modèle IA pour générer des résultats d’inférence, vous devez enregistrer les données d’inférence avec la vidéo. Cela vous permettra de lire les métadonnées d’inférence avec la vidéo, comme expliqué dans ce [tutoriel](record-stream-inference-data-with-video.md).


## <a name="routing-events"></a>Routage d’événements

Vous pouvez utiliser les propriétés de jumeau de module suivantes pour router les événements opérationnels et de diagnostic publiés par le module Video Analyzer. Ces propriétés sont documentées dans le [schéma JSON de jumeau de module](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName` : Pour obtenir des événements de diagnostic en provenance du module, incluez cette propriété et fournissez une valeur pour celle-ci. Omettez-la ou laissez-la vide pour empêcher le module de publier des événements de diagnostic.
  
- `operationalEventsOutputName` : Pour obtenir des événements opérationnels en provenance du module, incluez cette propriété et fournissez une valeur pour celle-ci. Omettez-la ou laissez-la vide pour empêcher le module de publier des événements opérationnels.

Des événements de diagnostic et opérationnels sont automatiquement émis par des nœuds tels que le processeur de détection de mouvement ou un processeur d’extension. Les événements analytiques doivent être routés à l’intérieur d’un pipeline vers un récepteur de messages IoT Hub afin d’être envoyés au hub IoT Edge. 

Vous pouvez contrôler le [routage de tous les événements précédents](../../../iot-edge/module-composition.md#declare-routes) à l’aide de la propriété `desired` du jumeau de module `$edgeHub` dans le manifeste de déploiement :

```json
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.1",
     "routes": {
       "moduleToHub": "FROM /messages/modules/avaedge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Dans le code JSON précédent, `avaedge` est le nom du module Video Analyzer. La règle d’acheminement suit le schéma défini dans [Déclarer des itinéraires](../../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Pour vous assurer que les événements analytiques atteignent le hub IoT Edge, vous devez disposer d’un nœud récepteur de message de hub IoT en aval d’un nœud processeur de détection de mouvement et/ou d’un nœud processeur d’extension.

## <a name="event-schema"></a>Schéma d’événement

Les événements provenant du périphérique peuvent être utilisés en périphérie ou dans le cloud. Les événements générés par le service Video Analyzer sont conformes au [modèle de messagerie en diffusion en continu](../../../iot-hub/iot-hub-devguide-messages-construct.md) établi par Azure IoT Hub. Le modèle se compose de propriétés système, de propriétés d’application et d’un corps.

### <a name="summary"></a>Résumé

Lorsqu’il est observé via IoT Hub, chaque événement possède un ensemble de propriétés communes :

| Propriété      | Type de propriété       | Type de données | Description                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `message-id`  | système              | guid      | ID d’événement unique.                                             |
| `topic`       | applicationProperty | string    | Chemin Azure Resource Manager du compte Azure Video Analyzer. |
| `subject`     | applicationProperty | string    | Sous-chemin de l’entité émettrice de l’événement.                    |
| `eventTime`   | applicationProperty | string    | Heure à laquelle l’événement a été généré.                                |
| `eventType`   | applicationProperty | string    | Identificateur du type d’événement. (Voir la section suivante.)          |
| `body`        | body                | object    | Données d’événement particulières.                                       |
| `dataVersion` | applicationProperty | string    | {Majeure}.{Mineure}                                              |

### <a name="properties"></a>Propriétés

#### <a name="message-id"></a>message-id

Identificateur global unique (GUID) de l’événement.

#### <a name="topic"></a>topic

Représente le module Edge Video Analyzer qui a généré l’événement. Exemple :

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/videoAnalyzers/{accountName}`

#### <a name="subject"></a>subject

Entité qui émet l’événement. Exemples :

`/edgeModules/avaedge/livePipelines/{livePipelineName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sources/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/processors/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sinks/{nodeName}`

La propriété `subject` vous permet de mapper des événements génériques au nœud dans le pipeline actif qui a émis l’événement. Par exemple, pour un nom d’utilisateur ou mot de passe RTSP non valides, l’événement généré devrait être `Microsoft.VideoAnalyzer.Diagnostics.ProtocolError` à partir du nœud `/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource`.

#### <a name="eventtype"></a>eventType

Les types d’événements sont attribués à un espace de noms selon ce schéma :

`Microsoft.VideoAnalyzer.{EventClass}.{EventType}`

La classe d’événements peut être l’une des suivantes :

| Nom de classe  | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| Analytics   | Événements générés dans le cadre de l’analyse du contenu.                |
| Diagnostics | Événements qui facilitent le diagnostic des problèmes et du niveau de performance. |
| En fonctionnement | Événements générés dans le cadre des opérations des ressources.              |

Exemples :

* `Microsoft.VideoAnalyzer.Analytics.Inference`
* `Microsoft.VideoAnalyzer.Diagnostics.AuthorizationError`
* `Microsoft.VideoAnalyzer.Operational.RecordingStarted`

#### <a name="eventtime"></a>eventTime

L’heure de l’événement est formatée dans une chaîne ISO 8601. Elle représente l’heure à laquelle l’événement s’est produit.

Exemple :

`2021-04-26T18:15:13.298Z`

## <a name="metrics"></a>Mesures

Ces métriques seront signalées à partir du module Video Analyzer via un [point de terminaison Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format) s’exécutant sur le port 9600 du module.

| Nom de métrique                           | Type    | Étiquettes                                                                              | Description                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| va_active_live_pipelines              | Jauge   | iothub, edge_device, module_name, pipeline_topology                                 | Nombre total de pipelines actifs par topologie.          |
| va_cumulative_latency_seconds_average | Jauge   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Latence moyenne entre le moment auquel une trame vidéo a été reçue par une source et le moment auquel elle a quitté le nœud au cours de la dernière minute. N’est pas signalée s’il n’y avait pas de trame. |
| va_cumulative_latency_seconds_max     | Jauge   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Latence maximale entre le moment auquel une trame vidéo a été reçue par une source et le moment auquel elle a quitté le nœud au cours de la dernière minute. N’est pas signalée s’il n’y avait pas de trame. |
| va_data_dropped_total                 | Compteur | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node, data_kind | Compteur des données supprimées (événements, médias, etc.).      |
| va_received_bytes_total               | Compteur | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Nombre total d’octets reçus par un nœud. Pris en charge uniquement pour les sources RTSP. |


> [!NOTE]
> Si vous nommez votre module Video Analyzer `avaedge`, il est possible d’accéder aux métriques en envoyant une requête GET à `http://avaedge:9600/metrics`. Selon votre déploiement, il se peut que vous deviez mapper ce port à l’appareil pour qu’il soit accessible.

### <a name="azure-monitor-collection-via-telegraf"></a>Collection Azure Monitor via Telegraf 

Pour activer la collecte de métriques à partir du module Video Analyzer, procédez comme suit. Les métriques sont collectées par [Telegraf](https://github.com/influxdata/telegraf), puis chargées dans [Azure Monitor](../../../azure-monitor/overview.md) en tant que [métrique personnalisée](../../../azure-monitor/essentials/metrics-custom-overview.md). Dans Telegraf, les journaux sont scrapés par le plug-in [Prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/prometheus) et chargés par le plug-in [azure_monitor](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor).

L’agent peut être utilisé pour collecter des métriques de n’importe quel module exposant un point de terminaison Prometheus.

1. Créez un dossier sur votre ordinateur de développement et accédez-y.
1. Dans le dossier, créez un fichier `telegraf.toml` qui contient les configurations suivantes :
    ```toml
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{AVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "{AVA_EDGE_MODULE_NAME}"
      region = "westus2"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Veillez à remplacer les variables dans le fichier .toml. Les variables sont désignées par des accolades (`{}`). En outre, mettez à jour la valeur de `region`.
1. Dans le même dossier, créez un Dockerfile qui contient les commandes suivantes :
    ```docker
    FROM telegraf:1.15.3-alpine
    COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```
1. Utilisez les commandes de l’interface de ligne de commande Docker pour générer le fichier Docker et publier l’image dans votre registre de conteneurs Azure.
   
   Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Docker pour effectuer un envoi (push) vers un registre de conteneurs, consultez [Envoyer (push) et tirer (pull) des images Docker](../../../container-registry/container-registry-get-started-docker-cli.md). Pour des informations sur Azure Container Registry, consultez la [documentation](../../../container-registry/index.yml).

   ```bash
   # Insert your own container image URL
   docker build . -t myregistry.azurecr.io/telegraf_agent
   docker push myregistry.azurecr.io/telegraf_agent
   ```

1. Une fois l’envoi (push) vers Azure Container Registry terminé, ajoutez le nœud suivant à votre manifeste de déploiement :
    ```json
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_TELEGRAF_IMAGE}"
        },
        "type": "docker",
        "version": "1.0",
        "status": "running",
        "restartPolicy": "always",
        "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    }
    ```
    > [!IMPORTANT]
    > Veillez à remplacer les variables dans le fichier manifeste. Les variables sont désignées par des accolades (`{}`).


   Azure Monitor peut être [authentifié via le principal de service](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Le plug-in Azure Monitor Telegraf expose [plusieurs méthodes d’authentification](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 
  1. Pour utiliser l’authentification par principal de service, définissez les variables d’environnement suivantes :  
     `AZURE_TENANT_ID` : locataire auprès duquel l’authentification aura lieu.  
     `AZURE_CLIENT_ID` : ID client de l’application à utiliser.  
     `AZURE_CLIENT_SECRET` : secret de l’application à utiliser.  
     
     >[!TIP]
     >Vous pouvez attribuer au principal du service le rôle **Éditeur de métriques de surveillance**. Suivez les étapes décrites dans **[Créer un principal de service](../../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** pour créer le principal de service et attribuer le rôle.
1. Une fois les modules déployés, les mesures s’affichent dans Azure Monitor sous un espace de noms unique. Les noms de métriques correspondent à ceux émis par Prometheus. 

   Dans ce cas, dans le portail Azure, accédez au hub IoT et sélectionnez **Métriques** dans le volet gauche. Les métriques y figurent. 

### <a name="log-analytics-metrics-collection"></a>Collecte de métriques Log Analytics

En utilisant le [point de terminaison Prometheus](https://prometheus.io/docs/practices/naming/) avec [Log Analytics](../../../azure-monitor/logs/log-analytics-tutorial.md), vous pouvez générer et [superviser des métriques](../../../azure-monitor/essentials/metrics-supported.md) telles que CPUPercent, MemoryUsedPercent, etc.   

> [!NOTE]
> La configuration ci-dessous ne collecte pas de journaux, mais **uniquement des métriques**. Il est possible d’étendre le module de collecte afin de collecter et charger également des journaux.

[![Diagramme montrant la collecte de métriques à l’aide de Log Analytics.](./media/telemetry-schema/log-analytics.svg)](./media/telemetry-schema/log-analytics.svg#lightbox)

1. Découvrez comment [collecter des métriques](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector).
1. Utilisez des commandes de l’interface de ligne de commande Docker pour générer le [fichier Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) et publier l’image dans votre registre de conteneurs Azure.
   
   Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Docker pour effectuer un envoi (push) vers un registre de conteneurs, consultez [Envoyer (push) et tirer (pull) des images Docker](../../../container-registry/container-registry-get-started-docker-cli.md). Pour plus d’informations sur Azure Container Registry, consultez la [documentation](../../../container-registry/index.yml).
1. Une fois l’envoi (push) vers Azure Container Registry terminé, les éléments suivants sont insérés dans le manifeste de déploiement :
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://avaedge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Les modules `edgeHub`, `edgeAgent` et `avaedge` sont les noms des modules définis dans le fichier manifeste de déploiement. Vérifiez que les noms des modules correspondent.   

    Vous pouvez récupérer vos valeurs `LogAnalyticsWorkspaceId` et `LogAnalyticsSharedKey` en effectuant les étapes suivantes :
    1. Accédez au portail Azure.
    1. Recherchez vos espaces de travail Log Analytics.
    1. Une fois que vous avez trouvé votre espace de travail Log Analytics, accédez à l’option `Agents management` dans le volet de navigation gauche.
    1. Vous y trouverez l’ID de l’espace de travail et les clés secrètes que vous pouvez utiliser.

1. Ensuite, créez un classeur en cliquant sur l’onglet `Workbooks` dans le volet de navigation gauche.
1. À l’aide du langage de requête Kusto, vous pouvez écrire des requêtes comme ci-dessous et obtenir le pourcentage du processeur utilisé par les modules IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("avaedge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Diagramme montrant les métriques à l’aide d’une requête Kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Journalisation

Comme avec d’autres modules IoT Edge, vous pouvez également [examiner les journaux de conteneur](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues) sur le périphérique. Vous pouvez configurer les informations écrites dans les journaux à l’aide des [propriétés de jumeau de module](module-twin-configuration-schema.md) suivantes :

* `logLevel`

   * Les valeurs autorisées sont `Verbose`, `Information`, `Warning`, `Error` et `None`.
   * La valeur par défaut est `Information`. Les journaux contiendront des messages d’erreur, d’avertissement et d’information.
   * Si vous définissez la valeur sur `Warning`, les journaux contiennent des messages d’erreur et d’avertissement.
   * Si vous définissez la valeur sur `Error`, les journaux contiennent uniquement des messages d’erreur.
   * Si vous définissez la valeur sur `None`, aucun journal n’est généré. Ce n’est pas recommandé.
   * Utilisez `Verbose` uniquement si vous devez partager des journaux avec Support Azure pour diagnostiquer un problème.

* `logCategories`

   * Liste séparée par des virgules d’une ou plusieurs des valeurs suivantes : `Application`, `Events`, `MediaPipeline`.
   * La valeur par défaut est `Application, Events`.
   * `Application` : Informations élémentaires provenant du module, comme des messages de démarrage du module, des erreurs d’environnement et des appels de méthode directs.
   * `Events` : Tous les événements décrits précédemment dans cet article.
   * `MediaPipeline` : Journaux détaillés qui peuvent offrir des insights lorsque vous résolvez des problèmes, comme des difficultés à établir une connexion avec une caméra compatible RTSP.
   
### <a name="generating-debug-logs"></a>Génération des journaux de débogage
Dans certains cas, pour aider Support Azure à résoudre un problème, vous aurez peut-être besoin de générer des journaux plus détaillés que ceux décrits précédemment. Pour générer ces journaux :  

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

Le module écrira maintenant les journaux de débogage dans un format binaire sous le chemin de stockage `/var/local/videoAnalyzer/debuglogs/` de l’appareil. Vous pouvez partager ces journaux avec Support Azure.  

Vous pouvez arrêter la collecte des journaux en définissant la valeur dans **Jumeau d’identité de module** sur _Null_. Retournez sur la page **Module Identity Twin** et mettez à jour le paramètre suivant comme suit :

   `"DebugLogsDirectory": ""`

## <a name="faq"></a>Forum Aux Questions

Si vous avez des questions, consultez la [FAQ sur la supervision et les métriques](faq.yml#monitoring-and-metrics).

## <a name="next-steps"></a>Étapes suivantes

[Résoudre des problèmes liés à Azure Video Analyzer](troubleshoot.md)
