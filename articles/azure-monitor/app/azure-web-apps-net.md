---
title: Surveiller les performances d’Azure App Service avec ASP.NET | Microsoft Docs
description: Surveillance des performances d’application pour Azure App Service à l’aide d’ASP.NET. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 97c0d2710d14e523338ac7cd670383caa75c7cef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443601"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet"></a>Surveillance d’applications pour Azure App Service et ASP.NET 

L’activation de la surveillance de vos applications Web ASP.NET exécutées sur les [Azure App Service](../../app-service/index.yml) n’a jamais été aussi facile. Alors qu’auparavant vous deviez instrumenter manuellement votre application, la dernière version de l’agent/extension est désormais intégrée à l’image App Service par défaut. Cet article explique pas à pas comment activer la supervision Azure Monitor Application Insights et vous donne des conseils d’automatisation du processus pour les déploiements à grande échelle.

> [!NOTE]
> L’ajout manuel d’une extension de site Application Insights via **Outils de développement** > **Extensions** est une méthode dépréciée. Cette méthode d’installation de l’extension était dépendante des mises à jour manuelles pour chaque nouvelle version. La version stable la plus récente de l’extension est désormais [préinstallée](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) dans l’image App Service. Les fichiers se trouvent dans `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` et sont automatiquement mis à jour avec chaque version stable. Si vous suivez les instructions ci-dessous pour activer la surveillance basée sur un agent, cela aura automatiquement pour effet de supprimer l’extension déconseillée.

> [!NOTE]
> Si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un Kit de développement logiciel (SDK), sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](#troubleshooting).

## <a name="enable-agent-based-monitoring"></a>Activer la supervision basée sur un agent

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED et urlCompression ne peuvent pas être utilisés ensemble. Pour plus d’informations, consultez la [section de résolution des problèmes](#appinsights_javascript_enabled-and-urlcompression-is-not-supported).

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure de votre service d’application, puis sélectionnez **Activer**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Capture d’écran de l’onglet Application Insights avec l’option Activer sélectionnée."::: 

2. Choisissez de créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour cette application. 

    > [!NOTE]
    > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Capture d’écran de la liste déroulante Modifier votre ressource."::: 

3. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. La supervision des applications ASP.NET est activée par défaut avec deux niveaux de collecte différents.

    :::image type="content"source="./media/azure-web-apps-net/instrument-net.png" alt-text="Capture d'écran de la page des extensions de site Application Insights, sur laquelle l'option Créer une ressource est sélectionnée."::: 
 
     Voici un résumé des données collectées pour chaque itinéraire :
            
    | Données | Collecte ASP.NET De base | Collecte ASP.NET Recommandé |
    | --- | --- | --- |
    | Ajoute les tendances d’utilisation de l’UC, de la mémoire et des E/S |Oui |Oui |
    | Collecte les tendances d’utilisation et permet la mise en corrélation des résultats de la disponibilité avec les transactions | Oui |Oui |
    | Collecte les exceptions non gérées par le processus hôte | Oui |Oui |
    | Améliore la précision des métriques APM avec charge, quand l’échantillonnage est utilisé | Oui |Oui |
    | Met en corrélation les microservices dans les limites de requête/dépendance | Non (fonctionnalités APM à instance unique uniquement) |Oui |

4. Pour configurer l’échantillonnage, que vous pouviez auparavant contrôler au moyen du fichier applicationinsights.config, vous pouvez maintenant interagir avec l’échantillonnage en définissant des paramètres d’application avec le préfixe correspondant `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor`. 

    - Par exemple, pour changer le pourcentage d’échantillonnage initial, créez un paramètre d’application `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` avec la valeur `100`.
    - Pour désactiver l’échantillonnage, affectez à `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage` la valeur `100`.
    - Les paramètres pris en charge sont les suivants :
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MinSamplingPercentage`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_EvaluationInterval`
        - `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_MaxTelemetryItemsPerSecond`
        
    - Pour obtenir la liste des paramètres et définition du processeur de télémétrie pris en charge pour l’échantillonnage adaptatif, consultez le [code](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) et la [documentation sur l’échantillonnage](./sampling.md#configuring-adaptive-sampling-for-aspnet-applications).


## <a name="enable-client-side-monitoring"></a>Activer la supervision côté client

La supervision côté client est activée pour ASP.NET. Pour activer la supervision côté client :

* **Paramètres** **>** **Configuration**
   * Sous Paramètres d’application, cliquez sur **Nouveau paramètre d’application** :

     Nom : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur: `true`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.

Pour désactiver la supervision côté client, supprimez la paire clé-valeur associée dans les paramètres de l’application, ou définissez la valeur sur false.

## <a name="automate-monitoring"></a>Automatiser la supervision

Pour activer la collecte de données de télémétrie avec Application Insights, il vous suffit de définir les paramètres de l’application :

:::image type="content"source="./media/azure-web-apps-net/application-settings-net.png" alt-text="Capture d’écran des Paramètres d’Application du Service App avec des paramètres d’Application Insights."::: 

### <a name="application-settings-definitions"></a>Définitions des paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle la supervision runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Dans le mode par défaut uniquement, les fonctionnalités essentielles sont activées afin de garantir des performances optimales. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Contrôle si le moteur de réécriture binaire `InstrumentationEngine` est activé. Ce paramètre impacte les performances ainsi que les temps de démarrage/démarrage à froid. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Contrôle si le texte de tables SQL et Azure est capturé, ainsi que les appels de dépendances. Avertissement de performance : le temps de démarrage à froid de l’application sera perturbé. Ce paramètre requiert `InstrumentationEngine`. | `~1` |

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Mettre à niveau l’extension/agent de surveillance – .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Mise à niveau à partir des versions 2.8.9 et ultérieures

La mise à niveau à partir de la version 2.8.9 s’effectue automatiquement, sans aucune action supplémentaire de votre part. Les nouveaux bits de supervision sont remis en arrière-plan au service d’application cible, et sont collectés au redémarrage de l’application.

Pour connaître la version de votre extension, accédez à l’adresse `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

:::image type="content"source="./media/azure-web-apps/extension-version.png" alt-text="Capture d’écran du chemin d’accès de l’URL pour vérifier la version de l’extension que vous exécutez." border="false"::: 

### <a name="upgrade-from-versions-100---265"></a>Mise à niveau à partir des versions 1.0.0 - 2.6.5

À compter de la version 2.8.9, l’extension de site préinstallée est utilisée. Si vous avez une version antérieure, vous pouvez la mettre à jour de deux façons :

* [Mettre à niveau par activation dans le portail](#enable-agent-based-monitoring). (Même si vous avez installé l’extension Application Insights pour Azure App Service, l’interface utilisateur affiche uniquement le bouton **Activer**. En arrière-plan, l’ancienne extension de site privée sera supprimée.)

* [Mettre à niveau par le biais de PowerShell](#enable-through-powershell) :

    1. Définissez les paramètres de l’application pour activer l’extension de site préinstallée ApplicationInsightsAgent. Consultez [Activer via PowerShell](#enable-through-powershell).
    2. Supprimez manuellement l’extension de site privée nommée extension Application Insights pour Azure App Service.

Si vous réalisez la mise à niveau à partir d’une version antérieure à 2.5.1, vérifiez que les DLL ApplicationInsigths sont supprimées du dossier bin de l’application. Pour plus d’informations, [consultez les étapes de résolution des problèmes](#troubleshooting).

## <a name="troubleshooting"></a>Dépannage

> [!NOTE]
> Quand vous créez une application Web avec les runtime `ASP.NET` dans les Services App Azure, elle déploie une seule page HTML statique en tant que site Web de démarrage. Il n’est **pas** recommandé de résoudre un problème lié au modèle par défaut. Déployez une application avant de résoudre un problème.

Vous trouverez ci-après les étapes à suivre pas à pas pour résoudre les problèmes rencontrés avec la surveillance basée sur un agent/une extension pour les applications ASP.NET exécutées sur les Services App Azure.

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini à une valeur « ~2 ».
2. Accédez à `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Capture d’écran du lien au-dessus de la page de résultats."border ="false"::: 
    
    - Vérifiez que l’extension est en cours d’exécution (`Application Insights Extension Status` affiche `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`) 
    
         Si elle ne l’est pas, suivez les instructions pour [Activer la surveillance d’Application Insights](#enable-agent-based-monitoring).

    - Vérifiez la présence d’une source d’état similaire à cette valeur : `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         S’il n’y en a pas, cela signifie que l’application n’est pas en cours d’exécution ou n’est pas prise en charge. Pour vous assurer que l’application est en cours d’exécution, essayez d’accéder manuellement à l’URL ou aux points de terminaison de l’application, afin d’exposer les informations d’exécution.

    - Vérifiez que la valeur de `IKeyExists` est `true`. Si la valeur est `false`, ajoutez `APPINSIGHTS_INSTRUMENTATIONKEY` et `APPLICATIONINSIGHTS_CONNECTION_STRING` avec votre GUID ikey aux paramètres de votre application.

    - Vérifiez qu’il n’y a pas d’entrées `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` et `AppContainsAspNetTelemetryCorrelationAssembly`.

         S’il y en a, supprimez les packages suivants de votre application : `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` et `Microsoft.AspNet.TelemetryCorrelation`.

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Le site web par défaut déployé avec Web Apps ne prend pas en charge le monitoring automatique côté client

Quand vous créez une application Web avec les runtime `ASP.NET` dans les Services App Azure, elle déploie une seule page HTML statique en tant que site Web de démarrage. La page web statique charge également un composant web managé ASP.NET dans IIS. Cela permet de tester le monitoring côté serveur sans code, mais ne prend pas en charge le monitoring automatique côté client.

Si vous souhaitez tester la surveillance côté client et côté serveur sans code pour ASP.NET dans une application web Azure App Services, nous vous recommandons de suivre les guides officiels pour la [création d’une application web ASP.NET Framework](../../app-service/quickstart-dotnetcore.md?tabs=netframework48), puis d’utiliser les instructions de l’article en cours pour activer la surveillance.


### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED et urlCompression ne sont pas pris en charge

Si vous utilisez APPINSIGHTS_JAVASCRIPT_ENABLED = true là où du contenu est encodé, vous pouvez voir ce type d’erreur :

- Erreur de réécriture d’URL 500
- Erreur du module de réécriture d’URL 500.53 avec un message indiquant que les règles de réécriture sortantes ne peuvent pas être appliquées quand le contenu de la réponse HTTP est encodé (« gzip »).

Cela est dû au fait que le paramètre d’application APPINSIGHTS_JAVASCRIPT_ENABLED est défini sur true alors qu’il y a du contenu encodé en même temps. Ce scénario n’est pas pris en charge actuellement. La solution de contournement consiste à supprimer APPINSIGHTS_JAVASCRIPT_ENABLED de vos paramètres d’application. Malheureusement, cela signifie que si l’instrumentation JavaScript côté client/navigateur est toujours requise, des références manuelles au SDK sont nécessaires pour vos pages web. Suivez les [instructions](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) pour activer l’instrumentation manuelle avec le kit de développement logiciel (SDK) JavaScript.

Pour avoir les toutes dernières informations sur l’extension/agent Application Insights, consultez les [notes de publication](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP et WordPress ne sont pas pris en charge

Les sites PHP et WordPress ne sont pas pris en charge. Il n’existe actuellement aucun SDK/agent officiellement pris en charge pour la supervision côté serveur de ces charges de travail. Toutefois, il est possible d’opérer manuellement des transactions côté client sur un site PHP ou WordPress en ajoutant le JavaScript côté client à vos pages web à l’aide du [Kit de développement logiciel (SDK) JavaScript](./javascript.md).

Ce tableau explique plus en détail la signification de ces valeurs d’erreur, leurs causes sous-jacentes et les corrections conseillées :

|Valeur d’erreur|Explication|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Cette valeur indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application et qu’ils seront mis en back-off. Cela peut être dû à la présence d’une référence à `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` ou `Microsoft.ApplicationInsights`  | Supprimez la référence. Certaines de ces références sont ajoutées par défaut à partir de certains modèles Visual Studio, et les versions antérieures de Visual Studio ajoutent parfois des références à `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Si l’application cible ASP.NET Core 2.1 ou 2.2, cette valeur indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application et qu’ils seront ignorés | Pour les clients sur .NET Core 2.1 ou 2.2, il est [conseillé](https://github.com/aspnet/Announcements/issues/287) d’utiliser le métapaquet Microsoft.AspNetCore.App à la place. En outre, activez « Interopérabilité avec le SDK Application Insights » dans le portail (voir les instructions ci-dessus).|
|`AppAlreadyInstrumented:true` | Cette valeur peut également être due à la présence des DLL ci-dessus, d’un déploiement précédent, dans le dossier de l’application. | Supprimez ces DLL du dossier de l’application. Vérifiez le répertoire bin de votre application locale et le répertoire wwwroot sur l’App Service. (Pour vérifier le répertoire wwwroot de votre application web App Service : Outils avancés (Kudu) > Console de débogage > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Cette valeur indique que l’extension a détecté la présence de références à `Microsoft.AspNet.TelemetryCorrelation` dans l’application et qu’elle sera mise en back-off. | Supprimez la référence.
|`AppContainsDiagnosticSourceAssembly**:true`|Cette valeur indique que l’extension a détecté la présence de références à `System.Diagnostics.DiagnosticSource` dans l’application et qu’elle sera mise en back-off.| Pour ASP.NET, supprimez la référence. 
|`IKeyExists:false`|Cette valeur indique que la clé d’instrumentation n’est pas présente dans le paramètre d’application `APPINSIGHTS_INSTRUMENTATIONKEY`. Causes possibles : Vous avez peut-être supprimé accidentellement les valeurs, oublié de définir les valeurs dans le script d’automatisation, etc. | Vérifiez que le paramètre est défini dans les paramètres d’application App Service.

## <a name="release-notes"></a>Notes de publication

Pour obtenir les mises à jour et correctifs de bogues les plus récents, [consultez les notes de publication](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Étapes suivantes

* [Exécuter le profileur sur une application dynamique](./profiler.md).
* [Surveiller Azure Functions avec Application Insights](monitor-functions.md).
* [Autorisation de l’envoi de diagnostics Azure](../agents/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../data-platform.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../alerts/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.
