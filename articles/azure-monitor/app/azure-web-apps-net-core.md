---
title: Analyser la performance du Service App Azure .NET Core | Microsoft Docs
description: Analyse de la performance d’application pour les services d’application Azure à l’aide d’ASP.NET Core. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 4bb5df82dcfd045100b4fe41c70538504cf2566b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508228"
---
# <a name="application-monitoring-for-azure-app-service-and-aspnet-core"></a>Surveillance des applications pour le Service App Azure et ASP.NET Core 

L’activation de la surveillance de vos applications Web ASP.NET Core exécutées sur les [Services App Azure ](../../app-service/index.yml) n’a jamais été aussi facile. Alors qu’auparavant vous deviez instrumenter manuellement votre application, la dernière version de l’agent/extension est désormais intégrée à l’image App Service par défaut. Cet article explique pas à pas comment activer la supervision Azure Monitor Application Insights et vous donne des conseils d’automatisation du processus pour les déploiements à grande échelle.

## <a name="enable-agent-based-monitoring"></a>Activer la supervision basée sur un agent

# <a name="windows"></a>[Windows](#tab/Windows)

> [!IMPORTANT]
> Les versions suivantes d’ASP.NET Core sont prises en charge pour l’auto-instrumentation sur Windows : ASP.NET Core 3.1, 5.0 et 6.0. Les versions 2.0, 2.1, 2.2 et 3.0 ont été retirées et ne sont plus prises en charge. Effectuez une mise à niveau vers une [version prise en charge](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) de .NET Core pour que l’instrumentation automatique fonctionne.

Pour commencer à configurer Application Insights avec votre ressource App Service, consultez la section [Activer la supervision](#enable-monitoring ) ci-dessous. 

# <a name="linux"></a>[Linux](#tab/Linux)

> [!IMPORTANT]
> Seul ASP.NET Core 6.0 est pris en charge pour l’auto-instrumentation sur Linux.

> [!NOTE]
> L’activation du portail App Services d’auto-instrumentation de Linux est en préversion publique. Les préversions sont fournies sans contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

 Dans Linux, [le déploiement dépendant de l’infrastructure](/dotnet/core/deploying/#publish-framework-dependent) et le [déploiement autonome](/dotnet/core/deploying/#publish-self-contained) sont pris en charge. 

Pour commencer à configurer Application Insights avec votre ressource App Service, consultez la section [Activer la supervision](#enable-monitoring ) ci-dessous. 

---
 

### <a name="enable-monitoring"></a>Activer la supervision 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure de votre service d’application, puis sélectionnez **Activer**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Capture d’écran de l’onglet Application Insights avec l’option activé sélectionnée."::: 

2. Choisissez de créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour cette application.

    > [!NOTE]
    > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Capture d’écran de la liste déroulante Modifier votre ressource."::: 

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. ASP.NET Core propose **Collecte recommandée** ou **Désactivée**.

    :::image type="content"source="./media/azure-web-apps-net-core/instrument-net-core.png" alt-text="Capture d’écran de la section instrumentation de votre application."::: 


## <a name="enable-client-side-monitoring"></a>Activer la supervision côté client

La supervision côté client est **activée par défaut** pour les applications ASP.NET Core configurées avec le niveau de **collecte Recommandé**, que le paramètre d’application « APPINSIGHTS_JAVASCRIPT_ENABLED » soit défini ou non.

Si vous avez besoin de désactiver la supervision côté client, effectuez les étapes suivantes :

* **Paramètres** **>** **Configuration**
   * Sous Paramètres d’application, cliquez sur **Nouveau paramètre d’application** :

     Nom : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur: `false`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.


## <a name="automate-monitoring"></a>Automatiser la supervision

Pour activer la collecte de données de télémétrie avec Application Insights, il vous suffit de définir les paramètres de l’application :

:::image type="content"source="./media/azure-web-apps-net-core/application-settings-net-core.png" alt-text="Capture d’écran des Paramètres d’Application du Service App avec des paramètres d’Application Insights."::: 


### <a name="application-settings-definitions"></a>Définitions des paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle la supervision runtime. | `~2` pour Windows ou `~3` pour Linux |
|XDT_MicrosoftApplicationInsights_Mode |  Dans le mode par défaut, seules les fonctionnalités essentielles sont activées afin de garantir des performances optimales. | `disabled` ou `recommended`. |
|XDT_MicrosoftApplicationInsights_PreemptSdk | Pour les applications ASP.NET Core uniquement. Active la technologie interop (interopération) avec le Kit de développement logiciel (SDK) Application Insights. Charge l’extension côte à côte avec le Kit de développement logiciel (SDK), et utilise celui-ci pour envoyer des données de télémétrie (désactive le Kit de développement logiciel (SDK) Application Insights). |`1`|


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="upgrade-monitoring-extensionagent---net"></a>Mettre à niveau l’extension/agent de surveillance – .NET 

### <a name="upgrade-from-versions-289-and-up"></a>Mise à niveau à partir des versions 2.8.9 et ultérieures

La mise à niveau à partir de la version 2.8.9 s’effectue automatiquement, sans aucune action supplémentaire de votre part. Les nouveaux bits de surveillance sont fournis en arrière-plan au service d’application cible, et sont collectés au redémarrage de l’application.

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
> Quand vous créez une application Web avec les runtime `ASP.NET Core` dans les Services App Azure, elle déploie une seule page HTML statique en tant que site Web de démarrage. Il n’est **pas** recommandé de résoudre un problème lié au modèle par défaut. Déployez une application avant de résoudre un problème.

Vous trouverez ci-après les étapes à suivre pas à pas pour résoudre les problèmes rencontrés avec la surveillance basée sur un agent/une extension pour les applications ASP.NET Core exécutées sur les Services App Azure.

# <a name="windows"></a>[Windows](#tab/windows)

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini à une valeur « ~2 ».
2. Accédez à `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Capture d’écran du lien au-dessus de la page de résultats."border ="false"::: 
    
    - Vérifiez que l’extension est en cours d’exécution (`Application Insights Extension Status` affiche `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`) 
    
         Si elle ne l’est pas, suivez les instructions pour [Activer la surveillance d’Application Insights](#enable-agent-based-monitoring).

    - Vérifiez la présence d’une source d’état similaire à cette valeur : `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         S’il n’y en a pas, cela signifie que l’application n’est pas en cours d’exécution ou n’est pas prise en charge. Pour vous assurer que l’application est en cours d’exécution, essayez d’accéder manuellement à l’URL ou aux points de terminaison de l’application, afin d’exposer les informations d’exécution.

    - Vérifiez que la valeur de `IKeyExists` est `true`. Si la valeur est `false`, ajoutez `APPINSIGHTS_INSTRUMENTATIONKEY` et `APPLICATIONINSIGHTS_CONNECTION_STRING` avec votre GUID ikey aux paramètres de votre application.

    -  Si votre application fait référence à des packages d’Application Insights, par exemple si vous avez précédemment instrumenté (ou tenté d’instrumenter) votre application avec le [kit de développement logiciel (SDK) ASP.NET Core](./asp-net-core.md), l’activation de l’intégration du Service App peut ne pas prendre effet et les données peuvent ne pas apparaître dans Application Insights. Pour résoudre le problème, dans le portail, activez « Interopérabilité avec le SDK Application Insights ». Les données commencent alors à s’afficher dans Application Insights. 
    - 
        > [!IMPORTANT]
        > Cette fonctionnalité est en préversion 

        :::image type="content"source="./media/azure-web-apps-net-core/interop.png" alt-text=" Capture d’écran du paramètre d’interopérabilité activé."::: 
        
        Les données vont désormais être envoyées à l’aide d’une approche sans code, même si le SDK Application Insights, à l’origine, a été utilisé ou a fait l’objet d’une tentative d’utilisation.

        > [!IMPORTANT]
        > Si l’application a utilisé le SDK Application Insights pour envoyer des données de télémétrie, ces données de télémétrie sont désactivées ; en d’autres termes, les données de télémétrie personnalisées (le cas échéant), telles que les méthodes Track* () et les paramètres personnalisés, tels que l’échantillonnage, sont désactivés. 

# <a name="linux"></a>[Linux](#tab/linux)

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini sur une valeur « ~3 ».
2. Accédez à */home\LogFiles\ApplicationInsights\status*, puis ouvrez *status_557de146e7fa_27_1.json*.

    Vérifiez que la valeur `AppAlreadyInstrumented` est définie sur false, la valeur `AiHostingStartupLoaded` sur true et la valeur `IKeyExists` sur true.

    Voici un exemple du fichier JSON :

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    Si la valeur `AppAlreadyInstrumented` est true, cela indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application, et va s’interrompre.

##### <a name="no-data"></a>Aucune donnée

1. Répertoriez et identifiez le processus hébergeant une application. Accédez à votre terminal, puis, dans la ligne de commande, tapez `ps ax`. 
    
    La sortie doit ressembler à ce qui suit : 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. Répertoriez ensuite les variables d’environnement du processus d’application. Dans la ligne de commande, tapez `cat /proc/27/environ | tr '\0' '\n`.
    
    La sortie doit ressembler à ce qui suit : 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    
1. Vérifiez que les valeurs `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_STARTUP_HOOKS` et `APPLICATIONINSIGHTS_CONNECTION_STRING` sont définies.

---

#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Le site web par défaut déployé avec Web Apps ne prend pas en charge le monitoring automatique côté client

Quand vous créez une application Web avec les runtime `ASP.NET Core` dans les Services App Azure, elle déploie une seule page HTML statique en tant que site Web de démarrage. La page web statique charge également un composant web managé ASP.NET dans IIS. Cela permet de tester la surveillance côté serveur sans code, mais ne prend pas en charge la surveillance automatique côté client.

Si vous souhaitez tester la surveillance du serveur et du côté client sans code pour ASP.NET Core dans une application Web des Services App Azure, nous vous recommandons de suivre les guides officiels pour la [création d’une application Web ASP.NET Core](../../app-service/quickstart-dotnetcore.md). Utilisez ensuite les instructions de l’article actuel pour activer la surveillance.

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

### <a name="php-and-wordpress-are-not-supported"></a>PHP et WordPress ne sont pas pris en charge

Les sites PHP et WordPress ne sont pas pris en charge. Il n’existe actuellement aucun SDK/agent officiellement pris en charge pour la surveillance côté serveur de ces charges de travail. Toutefois, il est possible d’opérer manuellement des transactions côté client sur un site PHP ou WordPress en ajoutant le JavaScript côté client à vos pages web à l’aide du [Kit de développement logiciel (SDK) JavaScript](./javascript.md).

Ce tableau explique plus en détail la signification de ces valeurs d’erreur, leurs causes sous-jacentes et les corrections conseillées :

|Valeur d’erreur |Explication |Fix |
|---- |----|---|
| `AppAlreadyInstrumented:true` | Cette valeur indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application et qu’ils seront mis en back-off. Cela peut être dû à la présence d’une référence à `Microsoft.ApplicationInsights.AspNetCore` ou `Microsoft.ApplicationInsights`  | Supprimez la référence. Certaines de ces références sont ajoutées par défaut à partir de certains modèles Visual Studio, et les versions antérieures de Visual Studio ajoutent parfois des références à `Microsoft.ApplicationInsights`. |
|`AppAlreadyInstrumented:true` | Si l’application cible ASP.NET Core 2.1 ou 2.2, cette valeur indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application et qu’ils seront ignorés | Pour les clients sur .NET Core 2.1 ou 2.2, il est [conseillé](https://github.com/aspnet/Announcements/issues/287) d’utiliser le métapaquet Microsoft.AspNetCore.App à la place. En outre, activez « Interopérabilité avec le SDK Application Insights » dans le portail (voir les instructions ci-dessus).|
|`AppAlreadyInstrumented:true` | Cette valeur peut également être due à la présence de Microsoft.ApplicationsInsights dll dans le dossier d’application d’un déploiement précédent. | Supprimez ces DLL du dossier de l’application. Vérifiez le répertoire bin de votre application locale et le répertoire wwwroot sur l’App Service. (Pour vérifier le répertoire wwwroot de votre application web App Service : Outils avancés (Kudu) > Console de débogage > CMD > home\site\wwwroot). |
|`IKeyExists:false`|Cette valeur indique que la clé d’instrumentation n’est pas présente dans le paramètre d’application `APPINSIGHTS_INSTRUMENTATIONKEY`. Causes possibles : Vous avez peut-être supprimé accidentellement les valeurs, oublié de définir les valeurs dans le script d’automatisation, etc. | Vérifiez que le paramètre est défini dans les paramètres d’application App Service. |

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
