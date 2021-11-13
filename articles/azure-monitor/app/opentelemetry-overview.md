---
title: Présentation d’OpenTelemetry avec Azure Monitor
description: Fournit une vue d’ensemble de l’utilisation d’OpenTelemetry avec Azure Monitor.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: a839437bd572946a6e6c2f62f2658ff4696db4e9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233485"
---
# <a name="opentelemetry-overview"></a>Présentation d’OpenTelemetry

Microsoft est ravi d’adopter [OpenTelemetry](https://opentelemetry.io/) comme l’avenir de l’instrumentation de télémétrie. Vous, nos clients, avez demandé une instrumentation indépendante des fournisseurs, et nous sommes ravis de nous associer à la communauté OpenTelemetry pour créer des API ou des Kits de développement logiciel (SDK) cohérents dans tous les langages.

Microsoft a collaboré avec les parties prenantes de deux projets de télémétrie open source précédemment populaires, [OpenCensus](https://opencensus.io/) et [OpenTracing](https://opentracing.io/), afin de créer un seul projet : OpenTelemetry. OpenTelemetry comprend des contributions de tous les principaux fournisseurs de cloud et de gestion des performances des applications (APM), et réside au sein de la [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/), dont Microsoft est un membre Platinum.

## <a name="concepts"></a>Concepts

La télémétrie, c’est-à-dire les données collectées pour observer votre application, peut être divisée en trois types ou « piliers » :
1.  Suivi distribué
2.  Mesures
3.  Journaux d’activité

Initialement, la communauté OpenTelemetry s’est occupée du suivi distribué. Des métriques et journaux sont toujours en cours. Un scénario complet d’observabilité comprend les trois piliers, mais, actuellement, nos [offres Azure Monitor de **préversion** d’exportateurs basés sur OpenTelemetry pour .NET, Python et JavaScript](opentelemetry-enable.md) **ne comprennent que le suivi distribué**.

Il existe plusieurs sources qui décrivent en détail les trois piliers, notamment le [site web de la communauté OpenTelemetry](https://opentelemetry.io/docs/concepts/data-sources/), les [spécifications OpenTelemetry](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md) et [Distributed Systems Observability](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html) de Cindy Sridharan.

Dans les sections suivantes, nous allons aborder certaines notions de base de la collecte de télémétrie.

### <a name="instrumenting-your-application"></a>Instrumentation de votre application

À un niveau de base, « l’instrumentation » consiste simplement à permettre à une application de capturer la télémétrie.

Il existe deux méthodes pour instrumenter votre application :
1.  L’instrumentation manuelle
2.  L’instrumentation automatique (auto-Instrumentation)

L’instrumentation manuelle consiste à coder sur l’API d’OpenTelemetry. Dans le contexte d’un utilisateur final, cela fait généralement référence à l’installation d’un kit SDK propre au langage dans une application. Les packages d’instrumentation manuelle se composent de nos [offres Azure Monitor de **préversion** d’exportateurs basés sur OpenTelemetry pour .NET, Python et JavaScript](opentelemetry-enable.md).

> [!IMPORTANT]
> Le terme « manuelle » ne signifie **PAS** que vous devrez écrire du code complexe pour définir des étendues pour le suivi distribué (bien que cela reste une possibilité). Un ensemble riche et croissant de bibliothèques d’instrumentation géré par les contributeurs OpenTelemetry va vous permettre de capturer facilement les signaux de télémétrie dans les frameworks et bibliothèques courants. Un sous-ensemble de bibliothèques d’instrumentation OpenTelemetry sera pris en charge par Azure Monitor, en fonction des commentaires des clients. En outre, nous travaillons à l’[instrumentation des SDK des services Azure les plus populaires à l’aide d’OpenTelemetry](https://devblogs.microsoft.com/azure-sdk/introducing-experimental-opentelemetry-support-in-the-azure-sdk-for-net/).

D’autre part, l’auto-instrumentation consiste à permettre la collecte de la télémétrie par configuration sans toucher au code de l’application. Bien que plus pratique, elle tend à être moins configurable et n’est pas disponible dans tous les langages. L’offre d’auto-instrumentation basée sur OpenTelemetry d’Azure Monitor est constituée de l’[offre de disponibilité générale basée sur OpenTelemetry Java 3.x](java-in-process-agent.md), et nous continuons à y investir en fonction des commentaires des clients. La communauté OpenTelemetry expérimente également l’auto-instrumentation en C# et Python, mais Azure Monitor est axée sur la création d’un scénario d’instrumentation manuelle simple et efficace à court terme.

### <a name="sending-your-telemetry"></a>Envoi de votre télémétrie

Il existe également deux façons d’envoyer vos données à Azure Monitor (ou à tout autre fournisseur).
1. Exportateur direct
2. Via un agent

Un exportateur direct envoie la télémétrie in-process (à partir du code de l’application) directement au point de terminaison d’ingestion d’Azure Monitor. Le principal avantage de cette approche réside dans la simplicité de l’intégration.

**Toutes les offres basées sur OpenTelemetry actuellement prises en charge par Azure Monitor utilisent un exportateur direct**. 

Par ailleurs, l’envoi de la télémétrie via un agent fournira un chemin d’accès à tout langage pris en charge par OpenTelemetry pour l’envoyer à Azure Monitor via le protocole [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md). La réception d’OTLP permettra aux clients d’observer des applications écrites dans des langages autres que nos [langages pris en charge](platforms.md). 

> [!NOTE]
> Certains clients ont commencé à utiliser le [collecteur OpenTelemetry](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) comme alternative aux agents, même si Microsoft ne prend pas encore officiellement en charge l’approche « Via un agent » pour la supervision des applications. En attendant, la communauté open source a contribué à créer un exportateur Azure Monitor de collecteur OpenTelemetry que certains clients utilisent pour envoyer des données à Azure Monitor Application Insights.

## <a name="terms"></a>Termes

Consultez le [glossaire](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md) dans les spécifications OpenTelemetry.

Certains termes hérités dans Application Insights prêtent à confusion étant donné la convergence du secteur vers OpenTelemetry. Le tableau ci-dessous met en évidence ces différences. À terme, les termes d’Application Insights seront remplacés par ceux d’OpenTelemetry.

Application Insights | OpenTelemetry
------ | ------
Collecteurs automatiques | Bibliothèques d’instrumentation
Canal | Exportateur  
Sans code/basé sur un agent |  Auto-instrumentation
Traces | Journaux d’activité


## <a name="next-step"></a>Étape suivante

Les pages suivantes contiennent une aide, langage par langage, pour activer et configurer les offres de Microsoft basées sur OpenTelemetry. Il est important de noter que nous partageons les fonctionnalités disponibles et les limites de chaque offre afin que vous puissiez déterminer si OpenTelemetry convient à votre projet.
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
