---
title: Traçage distribué dans Azure Application Insights | Microsoft Docs
description: Obtenez des informations sur la prise en charge Microsoft du traçage distribué par le biais de notre projet de partenariat OpenCensus
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c4f32c0803bb5815599c81ca6c89335fa6e1ffac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455166"
---
# <a name="what-is-distributed-tracing"></a>Présentation du traçage distribué

L’avènement du cloud moderne et des architectures de [microservices](https://azure.com/microservices) a donné naissance à des services simples qui peuvent être déployés de façon indépendante et qui participent à réduire les coûts, tout en améliorant la disponibilité et le débit. Bien que ces mouvements aient facilité la compréhension des services individuels, ils rendent les systèmes globaux plus difficiles à analyser et à déboguer.

Dans les architectures monolithiques, nous sommes habitués à résoudre les problèmes à l’aide de piles des appels. Les piles des appels sont des outils parfaits pour afficher le flux d’exécution (la méthode A a appelé la méthode B, qui a appelé la méthode C) ainsi que les détails et paramètres de chacun de ces appels. Cette méthodologie est tout à fait adaptée aux monolithes ou aux services qui s’exécutent sur un processus unique. Mais comment déboguer lorsque l’appel se trouve à la limite d’un processus, et qu’il n’est pas simplement une référence sur la pile locale ? 

C’est alors qu’intervient le traçage distribué.  

Le traçage distribué est l’équivalent des piles des appels pour les architectures de microservices et de cloud modernes, avec l’ajout d’un profileur de performances très simple. Dans Azure Monitor, nous proposons deux expériences pour consommer des données de trace distribuée. Tout d’abord, nous proposons la vue des [diagnostics de transaction](./transaction-diagnostics.md), qui ressemble à une pile des appels à laquelle est ajoutée une dimension temporelle. La vue des diagnostics de transaction offre une visibilité sur une seule transaction/requête. Elle est utile pour rechercher la cause racine des problèmes de fiabilité et des goulots d’étranglement des performances sur la base de chaque requête.

Azure Monitor offre également une vue de [mise en correspondance d’applications](./app-map.md) qui regroupe de nombreuses transactions afin d’afficher une vue topologique des interactions entre les systèmes et des taux moyens de performance et d’erreur. 

## <a name="how-to-enable-distributed-tracing"></a>Activer le traçage distribué

L'activation du traçage distribué entre les services d'une application est aussi simple que l'ajout de l'agent, du SDK ou de la bibliothèque adéquats à chaque service, en fonction du langage d'implémentation du service.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Activation via Application Insights par instrumentation automatique ou SDK

Les agents et/ou kits de développement logiciel (SDK) Application Insights pour .NET, .NET Core, Java, Node.js et JavaScript prennent tous en charge le traçage distribué en mode natif. Les instructions permettant d’installer et de configurer chaque SDK Application Insights sont disponibles ci-dessous :

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.JS](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Lorsque le SDK Application Insights adéquat est installé et configuré, les collecteurs automatiques des dépendances des SDK recueillent les informations de traçage automatiquement pour les infrastructures, les bibliothèques et les technologies populaires. La liste complète des technologies prises en charge est disponible dans [la documentation de la collecte automatique de la dépendance](./auto-collect-dependencies.md).

 De plus, toute technologie peut être pistée manuellement par un appel [TrackDependency](./api-custom-events-metrics.md) sur [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opentelemetry"></a>Activer via OpenTelemetry

Application Insights prend désormais en charge le traçage distribué via [OpenTelemetry](https://opentelemetry.io/). OpenTelemetry fournit une instrumentation indépendante du fournisseur pour envoyer des traces, des métriques et des journaux à Application Insights. Initialement, la communauté OpenTelemetry s’est occupée du suivi distribué. Des métriques et journaux sont toujours en cours. Un scénario complet d’observabilité comprend les trois piliers, mais, actuellement, nos [offres Azure Monitor de préversion d’exportateurs basés sur OpenTelemetry pour .NET, Python et JavaScript](opentelemetry-enable.md) ne comprennent que le suivi distribué. Toutefois, notre offre Azure Monitor basée sur Java OpenTelemetry est GA et entièrement prise en charge. 

Les pages suivantes contiennent une aide, langage par langage, pour activer et configurer les offres de Microsoft basées sur OpenTelemetry. Il est important de noter que nous partageons les fonctionnalités disponibles et les limites de chaque offre afin que vous puissiez déterminer si OpenTelemetry convient à votre projet.

* [.NET](opentelemetry-enable.md?tabs=net)
* [Java](java-in-process-agent.md)
* [Node.JS](opentelemetry-enable.md?tabs=nodejs)
* [Python](opentelemetry-enable.md?tabs=python)

## <a name="enable-via-opencensus"></a>Activation via OpenCensus

Outre les SDK, Application Insights prend également en charge le traçage distribué via [OpenCensus](https://opencensus.io/). OpenCensus est une distribution unique de bibliothèques (Open Source et indépendante des fournisseurs) offrant la collecte de métriques et le traçage distribué pour les services. Cet outil permet également à la communauté Open Source d’activer le traçage distribué avec des technologies populaires comme Redis, Memcached ou MongoDB. [Microsoft collabore sur OpenCensus avec plusieurs partenaires de surveillance et de cloud](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Le site web OpenCensus fournit de la documentation de référence sur l’API pour [Python](https://opencensus.io/api/python/trace/usage.html) et [Go](https://godoc.org/go.opencensus.io), ainsi que divers guides sur l’utilisation d’OpenCensus. 

## <a name="next-steps"></a>Étapes suivantes

* [Guide d’utilisation Python et OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Plan de l’application](./app-map.md)
* [Analyse des performances de bout en bout](../app/tutorial-performance.md)

