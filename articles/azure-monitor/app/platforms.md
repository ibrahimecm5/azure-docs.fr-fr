---
title: 'Application Insights : langages, plateformes et intégrations | Microsoft Docs'
description: Langages, plateformes et intégrations disponibles pour Application Insights
ms.topic: conceptual
ms.date: 10/29/2021
ms.reviewer: olegan
ms.openlocfilehash: 44c08d1008691826299d1fa41507df26064f6274
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462023"
---
# <a name="supported-languages"></a>Langues prises en charge

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plateformes et infrastructures prises en charge

### <a name="azure-service-integration-portal-enablement-arm-deployments"></a>Intégration de service Azure (activation du portail, déploiements ARM)
* [Machines virtuelles et groupes de machines virtuelles identiques Azure](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [Azure Functions](../../azure-functions/functions-monitoring.md)
* [Azure Cloud Services](./cloudservices.md) incluant les rôles web et de travail

### <a name="auto-instrumentation-enable-without-code-changes"></a>Instrumentation automatique (activer sans modification du code)
* [ASP.NET – pour les applications web hébergées avec IIS](./status-monitor-v2-overview.md)
* [Java](./java-in-process-agent.md)

### <a name="manual-instrumentation--sdk-some-code-changes-required"></a>Instrumentation manuelle/SDK (certaines modifications du code sont nécessaires)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript - Web](./javascript.md)
  * [React](./javascript-react-plugin.md)
  * [React Native](./javascript-react-native-plugin.md)
  * [Angular](./javascript-angular-plugin.md)
* [Rôles de travail, services et applications de bureau Windows](./windows-desktop.md)
* [Application Windows universelle](../app/mobile-center-quickstart.md) (App Center)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)

> [!NOTE]
> L’instrumentation basée sur OpenTelemetry est disponible dans l’état PRÉVERSION pour [C#, Node.js et Python](opentelemetry-enable.md). Veuillez consulter les limitations indiquées au début de la documentation officielle de chaque langage. Les utilisateurs qui ont besoin d’une expérience complète doivent utiliser les kits SDK Application Insights existants.

## <a name="logging-frameworks"></a>Frameworks de journalisation
* [ILogger](./ilogger.md)
* [Log4Net, NLog ou System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J ou Logback](java-2x-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportation et analyse de données
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Kits de développement logiciel (SDK) non pris en charge
Il existe plusieurs autres SDK Application Insights pris en charge par la communauté. Toutefois, Azure Monitor assure uniquement la prise en charge lors de l’utilisation des options d’instrumentation prises en charge figurant sur cette page. Nous évaluons constamment les opportunités d’étendre notre prise en charge d’autres langages. Suivez [Mises à jour Azure pour Application Insights](https://azure.microsoft.com/updates/?query=application%20insights) pour être tenu au courant des dernières actualités concernant le SDK.
