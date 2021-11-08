---
title: Supervision et journalisation – Service Azure Video Analyzer
description: Cet article fournit une vue d’ensemble de la supervision et de la journalisation dans le service Azure Video Analyzer.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 370904873a3f86ebee1cd530f349c55306588d08
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564002"
---
# <a name="monitor-and-log"></a>Superviser et journaliser

[!INCLUDE [header](includes/cloud-env.md)]

Dans cet article, vous allez découvrir les événements et les journaux générés par le service Azure Video Analyzer. Vous allez également apprendre à utiliser les journaux générés par le service et à superviser les événements du service.

## <a name="taxonomy-of-events"></a>Taxonomie des événements

Le diagramme ci-dessous représente la taxonomie commune utilisée pour les événements ou les données de télémétrie émises par le service Video Analyzer :

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-taxonomy-cloud.png" alt-text="Diagramme illustrant la taxonomie des événements.":::

## <a name="event-schema"></a>Schéma d’événement

 Les événements générés par le service Video Analyzer se composent des propriétés système, des propriétés de l’application et d’un corps.

### <a name="common-properties"></a>Propriétés communes

Chaque événement dispose d’un ensemble de propriétés communes :

| Propriété      | Type de propriété       | Type de données | Description                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `trace-id`    | système              | guid      | ID d’événement unique.                                             |
| `resourceId`  | applicationProperty | string    | Chemin ARM pour le compte Azure Video Analyzer.               |
| `subject`     | applicationProperty | string    | Sous-chemin de l’entité émettrice de l’événement.                    |
| `time`        | applicationProperty | string    | Heure à laquelle l’événement a été généré.                                |
| `category`    | système              | string    | Audit, Opérationnel, Diagnostics.                              |
| `operationName`| applicationProperty| string    | Identificateur du type d’événement (consultez la section suivante).          |
| `level`        | système             | string    | Niveau de l’événement (Information, Avertissement, Erreur, Critique).           |
| `body`        | body                | object    | Données d’événement particulières.                                       |
| `operationVersion` | système         | string    | Version des données d’événement {Major}.{Minor}                           |

## <a name="event-types"></a>Types d’événements
Le service Video Analyzer émet les types de données d’événement suivants :

**Opérationnel :** événements générés par les actions d’un utilisateur ou lors de l’exécution d’un [pipeline](../pipeline.md).
* Volume : doit normalement être faible (quelques occurrences par minute, voire moins). Exemples :

| Événement        | Level               | Description courte                                              |
| ------------- | ------------------- |  ------------------------------------------------------------ |
|RecordingStarted   |Informationnel| Enregistrement de média démarré|
|RecordingAvailable |Informationnel| Enregistrement de média disponible|
|RecordingStopped   |Informationnel| Enregistrement de média arrêté|
|PipelineStateChanged   |Informationnel| État du pipeline modifié|

   *Exemple d’événement opérationnel*
      
```json
{
  "time": "2021-10-06T21:19:36.0988630Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Operational",
  "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
  "operationVersion": "1.0",
  "level": "Informational",
  "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
  "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipeline/sinks/videoSink",
    "body": {
      "type": "video",
      "location": "/videos/livetest1",
      "startTime": "2021-10-06T21:19:32.520Z"
    }
  }
}
```

**Diagnostics :** événements permettant de diagnostiquer des erreurs et/ou des problèmes de performances.
   * Volume : peut être élevé (plusieurs fois par minute). Cela peut également avoir un impact sur le coût du stockage. Il est recommandé d’activer ces événements uniquement quand le diagnostic est nécessaire ou à des fins de résolution des problèmes. Exemples :
    
| Événement             | Level           | Description courte                                            |
| ----------------- | ----------------| ------------------------------------------------------------ |
|AuthenticationError|Erreur|Erreur d’authentification serveur/client|
|AuthorizationError |   Erreur|  Erreur d’autorisation serveur/client|
|FormatError        |   Erreur|  Problèmes d’empaquetage, de format ou d’encodage|
|MediaSessionEstablished|   Informationnel|  SDP ou autres informations de session|
|NetworkError        |  Erreur|  DNS, erreur réseau|
|ProtocolError        | Erreur|  RTSP ou toute autre erreur de protocole|
|StorageError        |  Erreur|  Erreur de lecture/écriture de stockage|
|RtspPlaybackSessionEstablished|    Informationnel| La session de lecture RTSP est établie|
|RtspPlaybackSessionClosed| Informationnel|  La session de lecture RTSP est fermée|

*Exemple d’événement de diagnostic*
  
```json
{
  "time": "2021-10-06T21:19:34.1290000Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Diagnostics",
  "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
  "operationVersion": "1.0",
  "level": "Informational",
  "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipesample/sources/rtspSource",
    "body": {
        "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n"
    }
  }
}

```
**Audit :** l’événement est utilisé pour consigner l’accès à l’API. 
  * Volume : faible, il est recommandé d’activer ces événements uniquement quand l’audit est nécessaire.

*Exemple d’événement d’audit*
  
```json
{
  "time": "2021-10-07T23:53:31.6792370Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Audit",
  "operationName": "Microsoft.VideoAnalyzer.Audit.ResourceGet",
  "level": "Warning",
  "uri": "https://{GUID}.api.{region}.videoanalyzer.azure.net/videos/batchjobsinknode",
  "resultType": "Failed",
  "resultSignature": "403",
  "identity": [
    {
      "alg": "RS256",
      "kid": "{KID}",
      "typ": "JWT"
    },
    {
      "sub": "livetest1",
      "aud": [ "https://{GUID}.streaming.{region}.videoanalyzer.azure.net/{GUID}", "wss://{GUID}.rtsp-tunnel.{region}.videoanalyzer.azure.net/{GUID}" ],
      "exp": 1633410145,
      "iss": "https://{region}.videoanalyzer.azure.net/"
    }
  ],
  "traceContext": "{\n  \"traceId\": \"4bb0dcf5-5c6d-4aa3-8c03-3f3d7e2c6210\"\n}",
  "properties": { "subject": "/videos/batchjobsinknodesample" }
}

```
## <a name="metrics"></a>Mesures

Le service Video Analyzer génère des métriques pour les [pipelines](../pipeline.md). Vous pouvez accéder aux métriques à l’aide du portail Azure, en accédant à la section Supervision à partir du volet de gestion de votre compte Video Analyzer.

| Nom de métrique                           | Type    | Dimension                                                                              | Description                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Octets d’entrée              | Compteur   | Type de pipeline, État, Nom de la topologie                                 | Nombre total d’octets reçus par un pipeline. Pris en charge uniquement pour les sources RTSP.           |
| Pipelines                  | Compteur   | Type de pipeline, État, Nom de la topologie                        | Fournit les pipelines dans différents états. Émis à intervalles réguliers. |

## <a name="monitoring-of-events"></a>Supervision des événements

Vous pouvez enregistrer les événements générés par le service Video Analyzer sur votre compte de stockage et les utiliser à l’aide d’Azure Monitor.

**Collecte des événements Azure Monitor**

Le service Video Analyzer prend actuellement en charge l’écriture des événements de télémétrie dans un compte de stockage et l’utilisation d’Azure Monitor pour consommer ces événements. Utilisez [Azure Monitor](../../../azure-monitor/overview.md) pour consommer les événements générés par le service, les clients bénéficient d’une expérience de supervision intégrée via Azure Monitor.

Pour activer la supervision et la collecte des événements avec Azure Monitor, procédez comme suit :
* Sur le portail Azure, accédez à la section **Supervision** de votre compte Video Analyzer, puis sélectionnez **Paramètres de diagnostic**.
* Cliquez sur **Ajouter un paramètre de diagnostic** pour activer la collecte des journaux suivants :
    *   Opérationnel
    *   Diagnostics
    *   Audit
* Sélectionnez la catégorie des journaux que vous souhaitez activer et la période de rétention correspondante.
* Pour les détails de la destination, sélectionnez **Archiver dans un compte de stockage** et spécifiez le compte de stockage dans lequel ces journaux d’événements sont stockés.
    > [!IMPORTANT]
    > Actuellement, le service Video Analyzer ne prend pas en charge l’envoi de diagnostics vers des destinations autres que le Stockage Azure.

* Cliquer sur **Enregistrer** après avoir configuré les paramètres de diagnostic
* Pour accéder aux journaux de diagnostic, accédez à l’explorateur de stockage, puis développez votre compte de stockage. Vous y voyez les conteneurs d’objets blob. Le conteneur « insights-logs-category » contient des journaux au format de fichier JSON. 
* Téléchargez le fichier journal souhaité. Le contenu du fichier journal téléchargé ressemble à l’exemple ci-dessous :

```json
{
    "time": "2021-10-06T21:19:36.0988630Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Operational",
    "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "operationVersion": "1.0",
    "level": "Informational",
    "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
    "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipesample/sinks/videoSink",
        "body": {
            "type": "video",
            "location": "/videos/livetest1",
            "startTime": "2021-10-06T21:19:32.520Z"
        }
    }
}
{
    "time": "2021-10-06T21:19:34.1290000Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Diagnostics",
    "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "operationVersion": "1.0",
    "level": "Informational",
    "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipe/sources/rtspSource",
        "body": {
            "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n" 
        }
    }
```

Un **journal d’activité** est également généré automatiquement pour les opérations de pipeline. Il est accessible via la section « Journal d’activité » du compte Video Analyzer sur le portail Azure. Vous pouvez consulter l’historique des appels d’API ARM effectués sur votre compte et les détails pertinents.

![Exemple de journal d’activité](./media/activity-log.png)


## <a name="next-steps"></a>Étapes suivantes

[Résoudre les problèmes du service Azure Video Analyzer](./troubleshoot.md)
