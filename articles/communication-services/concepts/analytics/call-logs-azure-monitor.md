---
title: Azure Communication Services - Aperçu des journaux d’appel
titleSuffix: An Azure Communication Services concept document
description: En savoir plus sur le résumé des appels et les journaux de diagnostic d’appel dans Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 15921a8666d6c55c90ed6d751f60a555c9c06d81
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138973"
---
# <a name="call-summary-and-call-diagnostic-logs-preview"></a>Résumé des appels et aperçu des journaux d’appel

> [!IMPORTANT]
> Les éléments suivants font référence aux journaux activés par le biais d’[Azure Monitor](/azure/azure-monitor/overview) (voir également la [FAQ](/azure/azure-monitor/faq)). Pour activer ces journaux pour vos services de communication, consultez : [Activer la journalisation dans les paramètres de diagnostic](./enable-logging.md)


## <a name="data-concepts"></a>Concepts de données
Voici les descriptions de haut niveau des concepts de données spécifiques aux appels vocaux et vidéo au sein de vos services de communication, qui sont importants à examiner afin de comprendre la signification des données capturées dans les journaux.

### <a name="entities-and-ids"></a>Entités et ID

Un *appel*, tel qu’il est en relation avec les entités représentées dans les données, est une abstraction représentée par le `correlationId`. Les `CorrelationId`s sont uniques par appel et sont associés à un délai par `callStartTime` et `callDuration`. Chaque appel est un événement qui contient des données provenant de deux ou plusieurs *points de terminaison*, qui représentent les différents participants humains, robots ou serveurs de l’appel.

Un *participant* (`participantId`) est présent uniquement lorsque l’appel est un appel de *groupe*, car il représente la connexion entre un point de terminaison et le serveur. 

Un *point de terminaison* est l’entité la plus unique, représentée par l’`endpointId`. `EndpointType` indique si le point de terminaison représente un utilisateur humain (RTPC, VoIP), un robot (bot) ou le serveur qui gère plusieurs participants au sein d’un appel. Lorsqu’un `endpointType` est `"Server"`, aucun ID unique n’est affecté au point de terminaison. En examinant `endpointType` et le nombre de `endpointId`s, vous pouvez toujours déterminer le nombre d’utilisateurs et d’autres participants non humains (robots, serveurs) sur l’appel. Les Kits de développement logiciel (SDK) natifs (comme le SDK Appel d’Android) réutilisent le même `endpointId` pour un utilisateur dans plusieurs appels, ce qui permet de comprendre l’expérience entre les sessions. Cela diffère des points de terminaison Web, ce qui génère toujours un nouveau `endpointId` pour chaque nouvel appel.

Un *flux* est l’entité la plus granulaire, car il existe un flux par direction (entrant/sortant) et `mediaType` (par exemple, audio, vidéo).  

### <a name="p2p-vs-group-calls"></a>Appels P2P et de groupe

Il existe deux types d’appels (représentés par `callType`) : P2P et groupe. 

Les appels **P2P** sont une connexion entre seulement deux points de terminaison, sans point de terminaison de serveur. Les appels P2P sont initiés en tant qu’appel entre ces points de terminaison et ne sont pas créés en tant qu’événement d’appel de groupe avant la connexion.

  :::image type="content" source="media\call-logs-azure-monitor\p2p-diagram.png" alt-text="Appel p2p":::


Les appels de **groupe** incluent tout appel créé à l’avance comme un événement de réunion/calendrier et tout appel ayant plus de 2 points de terminaison connectés. Les appels de groupe incluent un point de terminaison de serveur, et la connexion entre chaque point de terminaison et le serveur constitue un participant. Les appels P2P qui ajoutent un point de terminaison supplémentaire pendant l’appel cessent d’être P2P et ils deviennent un appel de groupe. En affichant `participantStartTime` et `participantDuration`, il est possible de déterminer la chronologie du moment où chaque extrémité a rejoint l’appel.


  :::image type="content" source="media\call-logs-azure-monitor\group-call-version-a.png" alt-text="Appel de groupe":::

## <a name="log-structure"></a>Structure de journal
Deux types de journaux sont créés : les journaux **Résumé d’appel** et les journaux **Diagnostic d’appel**. 

Les journaux de résumé d’appel contiennent des informations de base sur l’appel, y compris tous les informations pertinentes sur les ID, les timestamps, le point de terminaison et le SDK. Pour chaque point de terminaison au sein d’un appel (sans compter le serveur), un journal de résumé des appels distinct est créé.

Les journaux de diagnostic d’appel contiennent des informations sur le flux, ainsi qu’un ensemble de métriques qui indiquent la qualité des mesures d’expérience. Pour chaque point de terminaison au sein d’un appel (y compris le serveur), un journal de diagnostic d’appel distinct est créé pour chaque flux de données (audio, vidéo, etc.) entre les points de terminaison. Dans un appel P2P, chaque journal contient des données relatives à chacun des flux sortants associés à chaque point de terminaison. Dans un appel de groupe, chaque flux associé à `endpointType`= `"Server"` crée un journal contenant des données pour les flux entrants, et tous les autres flux créent des journaux contenant des données pour les flux sortants pour tous les points de terminaison non-serveur. Dans les appels de groupe, utilisez le `participantId` comme clé pour joindre les journaux entrants/sortants associés dans une connexion de participant distincte.

### <a name="example-1-p2p-call"></a>Exemple 1 : appel P2P

Le diagramme ci-dessous représente deux points de terminaison connectés directement dans un appel P2P. Dans cet exemple, 2 journaux de résumé des appels sont créés (1 par `endpointId`) et 4 journaux de diagnostic d’appel sont créés (1 par flux média). Chaque journal contient des données relatives au flux sortant de `endpointId`.

:::image type="content" source="media\call-logs-azure-monitor\example-1-p2p-call.png" alt-text="Flux d’appels P2P":::


### <a name="example-2-group-call"></a>Exemple 2 : appel de groupe

Le diagramme ci-dessous représente un exemple d’appel de groupe avec trois `particpantIds`, ce qui signifie trois `endpointIds` (`endpointIds` peut potentiellement apparaître dans plusieurs participants, par exemple lors de la nouvelle participation à un appel à partir du même appareil) et un point de terminaison de serveur. Pour `participantId` 1, deux journaux de résumé des appels sont créés : un pour `endpointId` et un autre pour le serveur. Quatre journaux de diagnostic d’appel sont créés, associés au `participantId` 1, un pour chaque flux média. Les trois journaux avec le `endpointId` 1 contiennent les données relatives aux flux médias sortants, et le journal contenant `endpointId = null, endpointType = "Server"` contient des données relatives au flux entrant.

:::image type="content" source="media\call-logs-azure-monitor\example-2-group-call-version-a.png" alt-text="Détail de l’appel de groupe":::

## <a name="data-definitions"></a>Définitions des données

### <a name="call-summary-log"></a>Journal de résumé des appels
Le journal de résumé des appels contient des données pour vous aider à identifier les propriétés clés de tous les appels. Un journal de résumé des appels différent sera créé pour chaque `participantId` (`endpointId` dans le cas d’appels P2P) dans l’appel.

|     Propriété                  |     Description                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     Horodatage (UTC) de la génération du journal.                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     Opération associée à l’enregistrement du journal.                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     Version d’api associée à l’opération, si le `operationName` a été effectué à l’aide d’une API. S’il n’existe aucune API qui corresponde à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer.                                                                                                                                                                           |
|     catégorie                  |     Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux d’activité sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob `properties` d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique.                                                                                                                                                                                                    |
|     correlationIdentifier     |    `correlationIdentifier` est l’ID unique pour un appel. Le `correlationIdentifier` identifie les événements corrélés de tous les participants et points de terminaison qui se connectent pendant un appel unique, et il peut être utilisé pour joindre les données de différents journaux.  Si vous devez ouvrir un dossier de support auprès de Microsoft, le `correlationID` sera utilisé pour identifier facilement l’appel que vous dépannez.                                                                                                                                                                      |
|     identificateur                |     Il s’agit de l’ID unique de l’utilisateur, correspondant à l’identité attribuée par le service d’authentification des communications. Vous pouvez utiliser cet ID pour mettre en corrélation les événements utilisateur dans différents journaux. Cet ID peut également être utilisé pour identifier les scénarios d’interopérabilité Microsoft Teams décrits plus loin dans ce document.                                                                                                                                                                                                                                                                                                                                                               |
|     callStartTime             |     Timestamp pour le début de l’appel, sur la base de la première tentative de connexion à partir de n’importe quel point de terminaison.                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     Durée de l’appel exprimée en secondes, en fonction de la première tentative de connexion et de la fin de la dernière connexion entre deux points de terminaison.                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     Contient `"P2P"` ou `"Group"`. Un appel `"P2P"` est une connexion 1:1 directe entre deux points de terminaison non-serveur. Un appel `"Group"` est un appel qui a plus de deux points de terminaison ou qui est créé en tant qu’appel `"Group"` avant la connexion.                                                                                                                                                                                                                                 |
|     teamsThreadId             |     Cet ID est uniquement pertinent lorsque l’appel est organisé en tant que réunion Microsoft Teams, représentant le cas d’utilisation de l’interopérabilité entre Microsoft Teams et Azure Communication Services. Cet ID est exposé dans les journaux opérationnels. Vous pouvez également vous procurer cet ID via les API de conversation.                                                                                                                                                                                              |
|     participantId             |     Cet ID est générée pour représenter la connexion bidirectionnelle entre un point de terminaison `"Participant"` (`endpointType` = `"Server"`) et le serveur. Lorsque `callType` = `"P2P"`, il y a une connexion directe entre les deux points de terminaison et aucun `participantId` n’est généré.                                                                                                                                                                                                               |
|     participantStartTime      |     Timestamp pour le début de la première tentative de connexion par le participant.                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     La durée de chaque connexion de participant, en secondes, de `participantStartTime` au timestamp lorsque la connexion est terminée.                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     Contient les codes d’erreur du Kit de développement logiciel (SDK) Appel émis par le SDK lorsque pertinent pour chaque `participantId`. Consultez les codes d’erreur du SDK Appel ci-dessous.                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     ID unique qui représente chaque point de terminaison connecté à l’appel, où le type de point de terminaison est défini par `endpointType`. Lorsque la valeur est `null`, l’entité connectée est le serveur Communication Services (`endpointType`= `"Server"`). `EndpointId` peut parfois persister pour le même utilisateur sur plusieurs appels (`correlationIdentifier`) pour les clients natifs. Le nombre de `endpointId`s détermine le nombre de journaux de résumé des appels. Un journal Résumé distinct est créé pour chaque `endpointId`.    |
|     endpointType              |     Cette valeur décrit les propriétés de chaque point de terminaison connecté à l’appel. Peut contenir `"Server"`, `"VOIP"`, `"PSTN"`, `"BOT"` ou `"Unknown"`.                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     Chaîne de version pour la version du Kit de développement logiciel (SDK) Appel Communication Services utilisée par chaque point de terminaison approprié. (Exemple : `"1.1.00.20212500"`)                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     Chaîne qui représente le système d’exploitation et la version de chaque appareil de point de terminaison.                                                                                                                                                                                                                                                                                                                                                                                       |


### <a name="call-diagnostic-log"></a>Journal de diagnostic d’appel
Les journaux de diagnostic d’appel fournissent des informations importantes sur les points de terminaison et les transferts de médias pour chaque participant, ainsi que des mesures qui aident à comprendre les problèmes de qualité. 

|     Propriété              |     Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     Opération associée à l’enregistrement du journal.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     `api-version` associée à l’opération, si `operationName` a été effectuée à l’aide d’une API. S’il n’existe aucune API qui corresponde à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer.                                                                                                                                                                                                                                                                                                                                                                  |
|     catégorie              |     Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux d’activité sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob `properties` d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique.                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     `correlationIdentifier` identifie les événements corrélés de tous les participants et points de terminaison qui se connectent pendant un appel unique. `correlationIdentifier` est l’ID unique pour un appel. Si vous devez ouvrir un dossier de support auprès de Microsoft, le `correlationID` sera utilisé pour identifier facilement l’appel que vous dépannez.                                                                                                                                                                                                                                                                                                                                                                         |
|     participantId         |     Cet ID est généré pour représenter la connexion bidirectionnelle entre un point de terminaison « Participant » (`endpointType`  =  `“Server”`) et le serveur. Lorsque `callType`   = `"P2P"`, il y a une connexion directe entre les deux points de terminaison et aucun `participantId` n’est généré.                                                                                                                                                                                                                                                                                                                                                                                                |
|     identificateur            |     Cet ID représente l’identité de l’utilisateur, telle que définie par le service d’authentification. Utilisez cet ID pour mettre en corrélation différents événements entre les appels et les services.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     ID unique qui représente chaque point de terminaison connecté à l’appel, avec le type de point de terminaison défini par `endpointType`.   Lorsque la valeur est `null`, cela signifie que l’entité connectée est le serveur Communication Services.  `EndpointId` peut être conservé pour le même utilisateur sur plusieurs appels (`correlationIdentifier`) pour les clients natifs, mais il sera unique pour chaque appel lorsque le client est un navigateur web.                                                                                                                                                                                                                                                                                  |
|     endpointType          |     Cette valeur décrit les propriétés de chaque `endpointId`. Peut contenir `“Server”`, `“VOIP”`, `“PSTN”`, `“BOT”` ou `“Unknown”`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     Cette valeur de chaîne décrit le type de média transmis entre les points de terminaison au sein de chaque flux. Les valeurs possibles incluent `“Audio”`, `“Video”`, `“VBSS”` (partage d’écran vidéo), et `“AppSharing”`.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     Entier non unique qui, avec `mediaType`, peut être utilisé pour identifier de manière unique les flux du même `participantId`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     Valeur de chaîne qui décrit le protocole de transport réseau par `participantId`. Peut contenir `"UDP”`, `“TCP”` ou `“Unrecognized”`. `"Unrecognized"` indique que le système n’a pas pu déterminer si le `transportType` était TCP ou UDP.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     Il s’agit de la durée moyenne nécessaire pour obtenir un paquet IP d’un point de terminaison à un autre au sein d’un `participantDuration`. Ce délai de propagation du réseau est essentiellement lié à la distance physique entre les deux points et à la vitesse de la lumière, y compris la charge supplémentaire prise par les différents routeurs entre les deux. La latence est mesurée à sens unique ou selon la durée des boucles (RTT).  Sa valeur est exprimée en millisecondes, et une valeur RTT supérieure à 500 ms doit être considérée comme ayant un impact négatif sur la qualité des appels.                                                                                                                                                                      |
|     roundTripTimeMax      |     Le RTT maximum (ms) mesuré par flux média pendant un `participantDuration` dans un appel de groupe ou un `callDuration` dans un appel P2P.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     Variation moyenne du délai entre des paquets successifs. Azure Communication Services peut s’adapter à certains niveaux de gigue par le biais de la mise en mémoire tampon. Ce n’est que lorsque la gigue dépasse la mise en mémoire tampon, qui est approximativement de `jitterAvg` >30 ms, qu’un impact négatif sur la qualité est susceptible de se produire. Les paquets arrivant à des vitesses différentes, la voix du locuteur peut sembler robotique. Ceci est mesuré par flux média pendant la `participantDuration` dans un appel de groupe ou un `callDuration` dans un appel P2P.                                                                                                                                              |
|     jitterMax             |     Il s’agit de la valeur maximale de gigue mesurée entre les paquets par flux média. Des rafales dans les conditions du réseau peuvent causer des problèmes dans le flux du trafic audio/vidéo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     Il s’agit du pourcentage moyen de paquets perdus. La perte de paquets affecte directement la qualité du son : elle peut aller de petits paquets individuels perdus n’ayant pratiquement pas d’impact à des pertes de transmission en rafale consécutives qui provoquent une coupure audio complète. Les paquets qui sont abandonnés et qui n’arrivent pas à leur destination prévue créent des intervalles dans les médias, ce qui se traduit par des syllabes et des mots manquants, ainsi que des vidéos et des partages hachés. Un taux de perte de paquets supérieur à 10 % (0,1) doit être considéré comme un taux susceptible d’avoir un impact négatif sur la qualité. Ceci est mesuré par flux média pendant la `participantDuration` dans un appel de groupe ou un `callDuration` dans un appel P2P.    |
|     packetLossRateMax     |     Cette valeur représente le taux maximum de perte de paquets (%) par flux média sur la `participantDuration` dans un appel de groupe ou la `callDuration` dans un appel P2P. Des rafales dans les conditions du réseau peuvent causer des problèmes dans le flux du trafic audio/vidéo.                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>Codes d’erreur
`participantEndReason` contient une valeur de l’ensemble des codes d’erreur du Kit de développement logiciel (SDK) Appel. Vous pouvez consulter ces codes pour résoudre les problèmes lors de l’appel, par point de terminaison.

|     Code d'erreur                 |     Description                                                                                                       |     Action à effectuer                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     Opération réussie                                                                                                           |     L’appel (P2P) ou le participant (groupe) s’est terminé correctement.                                                                                                                                                                                                                    |
|     403                        |     Interdit / Échec de l’authentification.                                                                               |     Vérifiez que votre jeton Communication Services est valide et qu’il n’a pas expiré. Si vous utilisez l’interopérabilité Teams, assurez-vous que votre locataire Teams a été ajouté à la liste d’autorisation d’accès à la préversion. Pour activer/désactiver l’interopérabilité des locataires Teams, remplissez ce formulaire.    |
|     404                        |     Appel introuvable.                                                                                                   |     Assurez-vous que le numéro que vous appelez (ou l’appel que vous êtes en train de rejoindre) existe.                                                                                                                                                                                                   |
|     408                        |     Le délai d’attente du contrôleur d’appel a expiré.                                                                                        |     Le contrôleur d’appel a atteint le délai d’attente des messages de protocole provenant des points de terminaison utilisateur. Vérifiez que les clients sont connectés et disponibles.                                                                                                                                                 |
|     410                        |     Erreur locale de pile multimédia ou d’infrastructure multimédia.                                                                  |     Veillez à utiliser le kit SDK le plus récent dans un environnement pris en charge.                                                                                                                                                                                                      |
|     430                        |     Impossible de remettre le message à l’application cliente.                                                                  |     Vérifiez que l’application cliente est en cours d’exécution et disponible.                                                                                                                                                                                                             |
|     480                        |     Le point de terminaison de client distant n’est pas inscrit.                                                                            |     Vérifiez que le point de terminaison distant est disponible.                                                                                                                                                                                                                              |
|     481                        |     Échec du traitement de l’appel entrant.                                                                                   |     Soumettez une demande de support via le portail Azure.                                                                                                                                                                                                                           |
|     487                        |     Appel annulé, refusé localement, terminé en raison d’un problème d’incompatibilité avec le point de terminaison, ou ne parvenant pas à générer l’offre de média.    |     Comportement attendu.                                                                                                                                                                                                                                                         |
|     490, 491, 496, 487, 498    |     Problèmes de réseau de point de terminaison local.                                                                                    |     Vérifiez votre réseau.                                                                                                                                                                                                                                                        |
|     500, 503, 504              |     Erreur d’infrastructure Communication Services.                                                                      |     Soumettez une demande de support via le portail Azure.                                                                                                                                                                                                                           |
|     603                        |     Appel global refusé par le participant Communication Services distant.                                             |     Comportement attendu.                                                                                                                                                                                                                                                         |
|     Unknown                    |     Raison de la fin non standard (ne fait pas partie des codes SIP standard).                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>Exemples d’appels et exemples de données

### <a name="p2p-call"></a>Appel P2P

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-p2p-call.png" alt-text="Exemple de journal P2P":::

Champs partagés pour tous les journaux de l’appel :

```json
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>Journaux de résumé des appels
Les journaux de résumé des appels comportent des informations sur les opérations et les catégories partagées :

```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
Résumé des appels pour l’utilisateur VoIP 1
```json
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

Résumé des appels pour l’utilisateur VoIP 2
```json
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Journaux de diagnostic d’appel
Les journaux de diagnostic d’appel partagent des informations sur les opérations :
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Journal de diagnostic pour le flux audio du point de terminaison VoIP 1 vers le point de terminaison VoIP 2 :
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Journal de diagnostic pour le flux audio du point de terminaison VoIP 2 vers le point de terminaison VoIP 1 :
```json
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Journal de diagnostic pour le flux vidéo du point de terminaison VoIP 1 vers le point de terminaison VoIP 2 :
```json
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>Appel de groupe
Dans l’exemple suivant, il y a trois utilisateurs dans un appel de groupe, deux connectés via VOIP et un connecté via RTC. Tous utilisent uniquement de l’audio. 

:::image type="content" source="media\call-logs-azure-monitor\call-examples-and-sample-data-group-call.png" alt-text="Exemple de journal de l’exemple d’appel de groupe":::

Les données seraient générées dans trois journaux de résumé des appels et 6 journaux de diagnostic des appels.

Champs partagés pour tous les journaux de l’appel :
```json
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>Journaux de résumé des appels
Les journaux de résumé des appels comportent des informations sur les opérations et les catégories partagées :
```json
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

Résumé des appels pour le point de terminaison VoIP 1 :
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
Résumé des appels pour le point de terminaison VoIP 3 :
```json
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
Résumé des appels pour le point de terminaison RTC 2 :
```json
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>Journaux de diagnostic d’appel
Les journaux de diagnostic d’appel partagent des informations sur les opérations :
```json
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
Journal de diagnostic pour le flux audio du point de terminaison VoIP 1 vers le point de terminaison de serveur 1 :
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Journal de diagnostic pour le flux audio du point de terminaison de serveur vers le point de terminaison VoIP 1 :
```json
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Journal de diagnostic pour le flux audio du point de terminaison VoIP 3 vers le point de terminaison de serveur 1 :
```json
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
Journal de diagnostic pour le flux audio du point de terminaison de serveur vers le point de terminaison VoIP 3 :
```json
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```
