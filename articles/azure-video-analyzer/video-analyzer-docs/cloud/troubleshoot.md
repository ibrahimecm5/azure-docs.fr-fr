---
title: Résoudre les problèmes du service Azure Video Analyzer
description: Cet article explique comment résoudre les problèmes du service Azure Video Analyzer.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3760b17d71dc3526d9a16283084095174d7bb724
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398399"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Résoudre les problèmes du service Azure Video Analyzer

![icône du cloud](media/env-icon/cloud.png)  
Vous pouvez également consulter la [résolution des problèmes en périphérie](../edge/troubleshoot.md).

---

Cet article décrit les étapes de résolution des problèmes pour les scénarios d’erreur courants lors de l’utilisation du service.

## <a name="enable-diagnostics"></a>Activation des diagnostics

L’article [Supervision et journalisation](./monitor-log-cloud.md) aide à comprendre la taxonomie des événements du service Video Analyzer et à générer des journaux facilitant le débogage des problèmes.
- Sur le portail Azure, accédez à la section **Supervision** de votre compte Video Analyzer et sélectionnez **Paramètres de diagnostic**. 
- Cliquez sur **Ajouter un paramètre de diagnostic** pour activer les journaux des types d’événements souhaités : `Diagnostics`, `Audit`, `Operational`. Pour plus d’informations, cliquez [ici](./monitor-log-cloud.md).


## <a name="view-diagnostics"></a>Afficher les diagnostics

Une fois que vous avez activé les diagnostics, vous pouvez accéder aux journaux comme suit :

> [!TIP]
> Il est recommandé d’activer au moins un pipeline en direct pour émettre des événements. 

- Dans le portail, rechercher le compte de stockage dans lequel les journaux ont été écrits
- Ouvrir le panneau de gestion de ce compte de stockage
- Accédez à l’explorateur de stockage -> puis développez votre compte de stockage. Vous voyez un conteneur d’objets blob « insights-logs-category ». Cet objet blob contient des journaux au format de fichier JSON. Vous pouvez télécharger ces journaux pour examiner le problème

## <a name="view-metrics"></a>Afficher les mesures 

Video Analyzer émet également des métriques pour l’ingestion et les pipelines, ce qui peut aider à identifier les problèmes comme suit.
- IngressBytes : nombre total d’octets reçus par un pipeline. Une valeur qui augmente régulièrement indique que le pipeline est sain et reçoit des données vidéo de la caméra RTSP
- Pipelines : aide à vérifier les nombres et l’état du pipeline.

## <a name="view-activity-logs"></a>Afficher les journaux d’activité

Un **journal d’activité** est généré automatiquement. Il est accessible en accédant à la section « Journal d’activité » du panneau de gestion du compte Video Analyzer sur le portail Azure. Vous pouvez consulter l’historique des appels d’API ARM effectués sur votre compte et les détails pertinents.

## <a name="common-error-scenarios"></a>Scénarios d’erreur courants

Certaines des erreurs courantes que vous pouvez rencontrer avec le service Video Analyzer sont décrites ci-dessous.

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>Impossible de lire la vidéo après l’activation du pipeline en direct

- Si vous utilisez le widget Video Analyzer pour lire la vidéo, essayez d’utiliser le portail Azure si vous y avez accès. Si la vidéo est lue dans le portail Azure, mais pas dans le widget, vous devez ensuite passer à la section de vérification du widget [ci-dessous](#playback-error-with-the-widget)

- Si la vidéo n’est pas lue dans le portail Azure, vérifiez si le service Video Analyzer reçoit des données vidéo de la caméra RTSP. Sélectionnez la métrique « Octets d’entrée » en accédant à la section Supervision->Métriques du portail. Si l’agrégation augmente, la connexion entre la caméra RTSP et le service est saine. La somme des octets d’entrée est disponible sous le graphique. 

- Si le service ne reçoit pas de données vidéo de la part de la caméra RTSP, l’étape suivante consiste à [afficher les journaux de diagnostic pertinents](#view-diagnostics). Vous voyez probablement une erreur comme [ProtocolError](#diagnostic-logs-have-a-protocolerror-with-code-401). Vous pouvez résoudre les problèmes plus en détail comme indiqué ci-dessous.

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>Les journaux de diagnostic ont une erreur ProtocolError avec le code 401

- Si vous voyez Microsoft.VideoAnalyzer.Diagnostics.ProtocolError dans les journaux de diagnostic, avec un code 401 comme dans l’exemple suivant, la première étape consiste à revérifier les informations d’identification RTSP. Voici un exemple d’événement que vous pouvez rencontrer :

   ```
   {
       "time": "2021-10-15T02:56:18.7890000Z",
       "resourceId": "/SUBSCRIPTIONS/{GUID}/RESOURCEGROUPS/8AVA/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/AVASAMPLEZ2OHI3VBIRQPC",
       "region": "westcentralus",
       "category": "Diagnostics",
       "operationName": "Microsoft.VideoAnalyzer.Diagnostics.ProtocolError",
       "operationVersion": "1.0",
       "level": "Error",
       "traceContext": "{\n  \"traceId\": \"f728d155-b4fd-4aec-8307-bbe2a324f4c3\"\n}",
       "properties": {
           "subject": "/livePipelines/your-pipeline/sources/rtspSource",
           "body": {
               "code": "401",
               "target": "rtsp://127.0.0.1:33643/some-path",
               "protocol": "rtsp"
           }
       }
   }

   ```

- Si vous utilisez un pipeline en direct pour vous connecter à une caméra accessible via Internet, vérifiez l’URL RTSP, le nom d’utilisateur et le mot de passe qui ont été utilisés lors de la création du pipeline en direct. Vous pouvez appeler GET sur le pipeline en direct pour afficher l’URL et le nom d’utilisateur, mais le mot de passe n’est pas renvoyé. Vous devez vérifier le code qui a été utilisé pour créer le pipeline en direct.

- Si vous utilisez un [adaptateur pour appareil distant](./use-remote-device-adapter.md), essayez de procéder comme suit.

   - Vérifiez que votre [hub IoT est joint à votre compte Video Analyzer](../create-video-analyzer-account.md#post-deployment-steps). Cela est nécessaire pour utiliser un adaptateur pour appareil distant.
   - Exécutez la méthode directe `remoteDeviceAdapterList` sur le module Edge et vérifiez l’adresse IP. Vous trouverez des exemples de requête et de réponse [ici](../edge/direct-methods.md)
   - Examinez la réponse de l’adaptateur pour appareil distant que vous utilisez dans le pipeline en direct qui rencontre le problème et comparez-la à l’exemple de [cet article](use-remote-device-adapter.md). Vérifiez que l’adresse IP de la caméra est correcte
   - Accédez au portail Azure -> Compte Video Analyzer -> En direct -> Pipelines -> Modifier le pipeline en direct -> entrez à nouveau le nom d’utilisateur et le mot de passe RTSP. Vérifiez que l’URL RTSP que vous fournissez commence par `rtsp://localhost:554/…`. L’utilisation de `localhost` est requise ici.

- Si les étapes décrites ci-dessus n’aident pas à résoudre le problème et que la lecture vidéo ne fonctionne toujours pas, connectez-vous au portail Azure et ouvrez un ticket de support. Vous devrez peut-être joindre les journaux du module Edge de Video Analyzer, reportez-vous à la section « Utiliser la commande support-bundle » dans la [documentation sur la résolution des problèmes Edge](../edge/troubleshoot.md#common-error-resolutions)

### <a name="unable-to-record-to-a-video-resource"></a>Impossible d’enregistrer dans une ressource vidéo

Avec Video Analyzer, vous devez utiliser une ressource vidéo distincte lors de l’enregistrement à partir d’une caméra RTSP distincte. Vous devez également basculer vers une nouvelle ressource vidéo si vous modifiez les paramètres de la caméra (par exemple sa résolution). Certains des exemples de topologies de pipeline ont des noms codés en dur pour la ressource vidéo dans les propriétés du nœud du récepteur vidéo. Si vous utilisez ces topologies directement avec différentes caméras, vous allez rencontrer ce problème. Modifiez la propriété `videoName` dans le nœud du récepteur vidéo pour garantir l’unicité.

### <a name="interrupted-recording-or-playback"></a>Lecture ou enregistrement interrompu

Quand vous créez un pipeline en direct, vous devez spécifier la vitesse de transmission maximale (`bitrateKbps`) à laquelle la caméra RTSP envoie des vidéos au service.
Si la caméra dépasse cette limite, le service Video Analyzer se déconnecte brièvement de la caméra. Il peut tenter une nouvelle tentative de connexion à la caméra en cas de pic temporaire de la vitesse de transmission. Vous pouvez identifier cette situation en recherchant l’événement Microsoft.VideoAnalyzer.Diagnostics.RtspIngestionSessionEnded dans les journaux de diagnostic, avec un code d’erreur `SourceBitrateExceeded`.
Pour résoudre ce problème, vous pouvez soit réduire le paramètre de vitesse de transmission sur la caméra, soit augmenter la valeur `bitrateKbps` du pipeline en direct pour qu’elle corresponde au paramètre de la caméra.

### <a name="playback-error-with-the-widget"></a>Erreur de lecture avec le widget

En cas d’erreur « Accès interdit » dans le widget Video Analyzer, vous devez voir un événement d’avertissement dans le journal d’audit.

- Vérifiez que vous avez généré le jeton JWT de l’API cliente et la stratégie d’accès correspondante, reportez-vous à la documentation relative à la [création d’un jeton](../access-policies.md) et à la [création d’une stratégie d’accès](../access-policies.md#creating-an-access-policy). Le lecteur ne fonctionne pas si la stratégie d’accès n’a pas été configurée correctement, ni si le jeton JWT ne correspond pas à la stratégie. 
- Si le problème n’est pas résolu, collectez les journaux du widget et envoyez un ticket de support à l’aide du portail Azure
- Collecte des journaux du widget :
    - Appuyez sur F12 pour activer les outils de développement du navigateur, accédez à l’onglet Console, puis activez la journalisation pour « Tous les niveaux ».   
    - À partir de l’icône Paramètres, sélectionnez Préférences--> Console--> Afficher les horodateurs. Enregistrez les journaux.

## <a name="collect-logs-for-submitting-a-support-ticket"></a>Collecter les journaux pour l’envoi d’un ticket de support
   
Quand les étapes de résolution des problèmes auto-guidée ne résolvent pas votre problème et que vous avez besoin d’aide pour des problèmes supplémentaires, ouvrez un ticket de support contenant les journaux et les détails pertinents à l’aide du portail Azure. Vous pouvez également nous contacter en nous envoyant un e-mail à l’adresse videoanalyzerhelp@microsoft.com.
   
> [!WARNING]
> Les journaux peuvent contenir des informations d’identification personnelle telles que votre adresse IP. Toutes les copies locales des journaux seront supprimées dès que nous aurons terminé leur examen et fermé le ticket de support.
   
## <a name="next-steps"></a>Étapes suivantes

[FORUM AUX QUESTIONS](./faq.yml)
