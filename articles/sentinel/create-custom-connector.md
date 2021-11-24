---
title: Ressources pour la création de connecteurs Microsoft Sentinel personnalisés | Microsoft Docs
description: En savoir plus sur les ressources disponibles pour la création de connecteurs personnalisés pour Microsoft Sentinel. Les méthodes incluent l’agent et l’API Log Analytics, Logstash, Logic Apps, PowerShell et Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 4c1d4f5dcbb0a707f0ec6ff728ddb72a27ab1e0d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521735"
---
# <a name="resources-for-creating-microsoft-sentinel-custom-connectors"></a>Ressources pour la création de connecteurs Microsoft Sentinel personnalisés

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel propose une large gamme de [connecteurs intégrés pour les solutions externes et les services Azure](connect-data-sources.md) et prend également en charge l’ingestion de données provenant de certaines sources sans un connecteur dédié.

Si vous ne parvenez pas à connecter votre source de données à Microsoft Sentinel en utilisant l’une des solutions existantes disponibles, envisagez de créer votre propre connecteur de source de données.

Pour obtenir la liste complète des connecteurs pris en charge, consultez le billet de blog [Microsoft Sentinel : les grands connecteurs (CEF, Syslog, direct, agent, personnalisé, etc.)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891).

## <a name="compare-custom-connector-methods"></a>Comparer les méthodes de connecteur personnalisé

Le tableau suivant compare les détails essentiels de chaque méthode de création de connecteurs personnalisés décrite dans cet article. Sélectionnez les liens dans le tableau pour obtenir plus d’informations sur chaque méthode.

|Description de la méthode  |Fonctionnalité | Sans serveur    |Complexité  |
|---------|---------|---------|---------|
|**[Agent Log Analytics](#connect-with-the-log-analytics-agent)** <br>Idéale pour collecter des fichiers à partir de sources locales et IaaS   | Collecte de fichiers uniquement.  |   Non      |Faible         |
|**[Logstash](#connect-with-logstash)** <br>Idéale pour les sources locales et IaaS, toute source pour laquelle un plug-in est disponible et les organisations qui connaissent déjà Logstash  | Les capacités des plug-ins disponibles, ainsi que celles du plug-in personnalisé, offrent une flexibilité significative.   |   Non ; nécessite l’exécution d’une machine virtuelle ou d’un cluster de machines virtuelles           |   Faible ; prend en charge de nombreux scénarios avec des plug-ins      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Coût élevé ; à éviter pour les grandes quantités de données <br>Idéale pour les sources cloud à faible volume  | La programmation sans code offre une flexibilité limitée, sans prise en charge de l’implémentation des algorithmes.<br><br> Si aucune action disponible ne répond déjà à vos besoins, la création d’une action personnalisée peut ajouter une certaine complexité.    |    Oui         |   Faible ; développement simple et sans code      |
|**[PowerShell](#connect-with-powershell)** <br>Idéale pour le prototypage et les chargements de fichiers périodiques | Prise en charge directe pour la collecte de fichiers. <br><br>PowerShell peut être utilisé pour collecter davantage de sources, mais nécessitera le codage et la configuration du script en tant que service.      |Non               |  Faible       |
|**[API Log Analytics](#connect-with-the-log-analytics-api)** <br>Idéale pour les éditeurs de logiciels indépendants qui implémentent l’intégration et pour les exigences uniques de collecte   | Prend en charge toutes les capacités disponibles avec le code.  | Dépend de l’implémentation           |     Élevé    |
|**[Azure Functions](#connect-with-azure-functions)** Idéale pour les sources cloud à volume élevé et pour les exigences uniques de collecte  | Prend en charge toutes les capacités disponibles avec le code.  |  Oui             |     Élevé ; nécessite des connaissances en programmation    |
|     |         |                |

> [!TIP]
> Pour comparer l’utilisation de Logic Apps et d’Azure Functions pour le même connecteur, consultez :
>
> - [Ingérer rapidement les journaux Web Application Firewall dans Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (communauté Microsoft Sentinel GitHub) : [Connecteur d’applications logique](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data) | [Connecteur de fonction Azure](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
>

## <a name="connect-with-the-log-analytics-agent"></a>Se connecter avec l’agent Log Analytics

Si votre source de données livre des événements dans des fichiers, nous vous recommandons d’utiliser l’agent Log Analytics d’Azure Monitor pour créer votre connecteur personnalisé.

- Pour plus d’informations, consultez [Collecte de journaux personnalisés dans Azure Monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Pour découvrir un exemple de cette méthode, consultez [Collecte des sources de données JSON personnalisées à l’aide de l’agent Log Analytics pour Linux dans Azure Monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Se connecter avec Logstash

Si vous connaissez [Logstash](https://www.elastic.co/logstash), vous pouvez utiliser cette solution avec le [plug-in de sortie Logstash pour Microsoft Sentinel](connect-logstash.md) afin de créer votre connecteur personnalisé.

Grâce au plug-in de sortie Logstash pour Microsoft Sentinel, vous pouvez utiliser n’importe quel plug-in d’entrée et de filtrage Logstash et configurer Microsoft Sentinel comme sortie pour un pipeline Logstash. Logstash dispose d’une vaste bibliothèque de plug-ins qui permettent l’entrée de diverses sources, telles qu’Event Hubs, Apache Kafka, des fichiers, des bases de données et des services Cloud. Utilisez les plug-ins de filtrage pour analyser les événements, filtrer les événements inutiles, obfusquer les valeurs et bien plus encore.

Pour obtenir des exemples d’utilisation de Logstash en tant que connecteur personnalisé, consultez :

- [Chasse aux TTP de violation Capital One dans les journaux AWS à l’aide de Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Guide d’implémentation Microsoft Sentinel Radware](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Pour obtenir des exemples de plug-ins Logstash utiles, consultez :

- [Plug-in d’entrée CloudWatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Plug-in Azure Event Hubs](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Plug-in d’entrée Google Cloud Storage](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Plug-in d’entrée Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash permet également la mise à l’échelle de la collecte des données à l’aide d’un cluster. Pour plus d’informations, consultez [Utilisation d’une machine virtuelle Logstash avec équilibrage de charge à grande échelle](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Se connecter avec Logic Apps

Utilisez [Azure Logic Apps](../logic-apps/index.yml) pour créer un connecteur personnalisé serverless pour Microsoft Sentinel.

> [!NOTE]
> Si la création de connecteurs serverless à l’aide de Logic Apps peut être pratique, l’utilisation de Logic Apps pour vos connecteurs peut s’avérer coûteuse pour des volumes importants de données.
>
> Nous vous recommandons d’utiliser cette méthode uniquement pour les sources de données de faible volume ou pour enrichir vos chargements de données.
>

1. **Pour démarrer vos applications logiques, utilisez l’un des déclencheurs suivants** :

    |Déclencheur  |Description  |
    |---------|---------|
    |**Tâche récurrente**     |   Par exemple, programmez votre application logique pour récupérer régulièrement des données à partir de fichiers, de bases de données ou d’API externes spécifiques. <br>Pour plus d’informations, consultez [Créer, planifier et exécuter des tâches et des workflows récurrents avec Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**Déclenchement à la demande**     | Exécutez votre application logique à la demande pour la collecte et le test manuels des données. <br>Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques à l’aide de points de terminaison HTTPS](../logic-apps/logic-apps-http-endpoint.md).        |
    |**Point de terminaison HTTP/S**     |  Recommandé pour la diffusion en continu et si le système source peut démarrer le transfert de données. <br>Pour plus d’informations, consultez [Appeler des points de terminaison de service via HTTP ou HTTPS](../connectors/connectors-native-http.md).       |
    |     |         |

1. **Utilisez l’un des connecteurs Logic Apps qui lisent les informations pour obtenir vos événements**. Par exemple :

    - [Se connecter à une API REST](/connectors/custom-connectors/)
    - [Se connecter à un serveur SQL Server](/connectors/sql/)
    - [Se connecter à un système de fichiers](/connectors/filesystem/)

    > [!TIP]
    > Des connecteurs personnalisés pour les API REST, les serveurs SQL et les systèmes de fichiers permettent également de récupérer des données à partir de sources de données locales. Pour plus d’informations, consultez la documentation [Installer une passerelle de données locale](/connectors/filesystem/).
    >

1. **Préparez les informations que vous souhaitez récupérer**.

    Par exemple, utilisez l’[action Analyse JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) pour accéder aux propriétés du contenu JSON, ce qui vous permet de sélectionner ces propriétés dans la liste du contenu dynamique lorsque vous spécifiez des entrées pour votre application logique.

    Pour plus d’informations, consultez [Effectuer des opérations sur les données dans Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Écrivez les données dans Log Analytics**.

    Pour plus d’informations, consultez la documentation [Collecteur de données Azure Log Analytics](/connectors/azureloganalyticsdatacollector/).

Pour obtenir des exemples sur la façon dont vous pouvez créer un connecteur personnalisé pour Microsoft Sentinel à l’aide de Logic Apps, consultez :

- [Créer un pipeline de données avec l’API Collecte de données](/connectors/azureloganalyticsdatacollector/)
- [Connecteur d’application logique Palo Alto Prisma utilisant un webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (communauté GitHub Microsoft Sentinel)
- [Sécuriser les appels Microsoft Teams grâce à l’activation planifiée](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- [Ingestion d’indicateurs de menace AlienVault OTX dans Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)

## <a name="connect-with-powershell"></a>Se connecter avec PowerShell

Le [script PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) vous permet d’utiliser PowerShell pour diffuser en continu des événements ou des informations contextuelles sur Microsoft Sentinel depuis la ligne de commande. Cette diffusion en continu crée un connecteur personnalisé entre votre source de données et Microsoft Sentinel.

Par exemple, le script suivant charge un fichier CSV dans Microsoft Sentinel :

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Le [script PowerShell Upload-AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) utilise les paramètres suivants :

|Paramètre  |Description  |
|---------|---------|
|**WorkspaceId**     |   ID de l’espace de travail Microsoft Sentinel où vous stockerez vos données.  [Trouver l’ID et la clé de votre espace de travail](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Clé primaire ou secondaire de l’espace de travail Microsoft Sentinel dans lequel vous stockerez vos données. [Trouver l’ID et la clé de votre espace de travail](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Nom de la table de journal personnalisée dans laquelle vous souhaitez stocker les données. Un suffixe **_CL** est automatiquement ajouté à la fin du nom de votre table.  |
|**AddComputerName**     |   Lorsque ce paramètre existe, le script ajoute le nom de l’ordinateur actuel à chaque enregistrement de journal, dans un champ nommé **Computer**.      |
|**TaggedAzureResourceId**     | Lorsque ce paramètre existe, le script associe tous les enregistrements de journal chargés à la ressource Azure spécifiée. <br><br>Cette association permet de charger des enregistrements de journal pour les requêtes en contexte de ressources et adhère à un contrôle d’accès centré sur les ressources et basé sur les rôles.       |
|**AdditionalDataTaggingName**     |      Lorsque ce paramètre existe, le script ajoute un autre champ à chaque enregistrement de journal, avec le nom configuré et la valeur configurée pour le paramètre **AdditionalDataTaggingValue**. <br><br>Dans ce cas, **AdditionalDataTaggingValue** ne doit pas être vide. |
|**AdditionalDataTaggingValue**     |   Lorsque ce paramètre existe, le script ajoute un autre champ à chaque enregistrement de journal, avec la valeur configurée et le nom de champ configuré pour le paramètre **AdditionalDataTaggingName**. <br><br>Si le paramètre **AdditionalDataTaggingName** est vide, mais qu’une valeur est configurée, le nom de champ par défaut est **DataTagging**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Trouver l’ID et la clé de votre espace de travail

Vous trouverez les détails des paramètres **WorkspaceID** et **WorkspaceKey** dans Microsoft Sentinel :

1. Dans Microsoft Sentinel, sélectionnez **Paramètres** sur la gauche, puis sélectionnez l’onglet **Paramètres de l’espace de travail**.

1. Sous **Prise en main de Log Analytics** > **1 Connecter une source de données**, sélectionnez **Gestion des agents Windows et Linux**.

1. Recherchez l’ID de votre espace de travail, la clé primaire et la clé secondaire dans les onglets **Serveurs Windows**.

## <a name="connect-with-the-log-analytics-api"></a>Se connecter avec l’API Log Analytics

Vous pouvez diffuser en continu des événements sur Microsoft Sentinel en utilisant l’API de collecteur de données Log Analytics pour appeler directement un point de terminaison RESTful.

Bien que l’appel d’un point de terminaison RESTful nécessite directement davantage de programmation, il offre également plus de flexibilité.

Pour plus d’informations, consultez l’[API de collecteur de données Log Analytics](../azure-monitor/logs/data-collector-api.md), en particulier les exemples suivants :

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python](../azure-monitor/logs/data-collector-api.md#python-sample)

## <a name="connect-with-azure-functions"></a>Se connecter avec Azure Functions

Utilisez Azure Functions avec une API RESTful et divers langages de codage, tels que [PowerShell](../azure-functions/functions-reference-powershell.md), pour créer un connecteur personnalisé serverless.

Pour voir des exemples de cette méthode, consultez :

- [Connecter votre instance VMware Carbon Black Cloud Endpoint Standard à Microsoft Sentinel avec Azure Functions](./data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)
- [Connecter votre instance Okta Single Sign-On à Microsoft Sentinel avec Azure Functions](./data-connectors-reference.md#okta-single-sign-on-preview)
- [Connecter votre solution Proofpoint TAP à Microsoft Sentinel avec Azure Functions](./data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)
- [Connecter votre solution Qualys VM à Microsoft Sentinel avec Azure Functions](./data-connectors-reference.md#qualys-vulnerability-management-vm-preview)
- [Ingestion de données aux formats XML, CSV ou autres](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Surveiller Zoom à l’aide de Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Déployer une application de fonction permettant de transférer les données de l’API de gestion d’Office 365 dans Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (communauté GitHub Microsoft Sentinel)

## <a name="parse-your-custom-connector-data"></a>Analyser les données de votre connecteur personnalisé

Vous pouvez utiliser la technique d’analyse intégrée de votre connecteur personnalisé pour extraire les informations pertinentes et remplir les champs correspondants dans Microsoft Sentinel.

Par exemple :

- **Si vous avez utilisé Logstash**, utilisez le plug-in de filtre [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) pour analyser vos données.
- **Si vous avez utilisé une fonction Azure**, analysez vos données à l’aide du code.

Microsoft Sentinel prend en charge l’analyse au moment de la requête. L’analyse au moment de la requête vous permet d’envoyer (push) des données dans le format d’origine, puis de les analyser à la demande, le cas échéant.

L’analyse au moment de la requête signifie également que vous n’avez pas besoin de connaître la structure exacte de vos données à l’avance, lorsque vous créez votre connecteur personnalisé, ni même les informations que vous devez extraire. Au lieu de cela, analysez vos données à tout moment, même pendant une investigation.

Pour plus d’informations sur l’analyse au moment de la requête, consultez [Analyseurs](normalization-about-parsers.md).

> [!NOTE]
> La mise à jour de votre analyseur s’applique également aux données que vous avez déjà ingérées dans Microsoft Sentinel.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les données ingérées dans Microsoft Sentinel pour sécuriser votre environnement à l’aide de l’un des processus suivants :

- [Obtenir une visibilité des alertes](get-visibility.md)
- [Visualiser et superviser vos données](monitor-your-data.md)
- [Investiguer les incidents](investigate-cases.md)
- [Détecter les menaces](detect-threats-built-in.md)
- [Automatiser la prévention des menaces](tutorial-respond-threats-playbook.md)
- [Être à l’affût des menaces](hunting.md)

Découvrez également un exemple de création de connecteurs personnalisé pour la surveillance Zoom : [Surveiller Zoom à l’aide de Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516).
