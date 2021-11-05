---
title: Activer Azure Monitor OpenTelemetry pour les applications .NET, Node.js et Python
description: Fournit des conseils sur la façon d’activer Azure Monitor sur les applications à l’aide d’OpenTelemetry
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 3961f7233de1fcd09dc8a2199dfa424b505add27
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058100"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>Activer Azure Monitor OpenTelemetry Exporter pour les applications .NET, Node.js et Python (préversion)

Cet article explique comment activer et configurer l’offre en préversion Azure Monitor basée sur OpenTelemetry. Après avoir suivi les instructions de cet article, vous pourrez envoyer des traces OpenTelemetry à Azure Monitor Application Insights. Pour en savoir plus sur OpenTelemetry, consultez la [vue d’ensemble](opentelemetry-overview.md) ou les [questions fréquentes (FAQ)](/azure/azure-monitor/faq#opentelemetry).

> [!IMPORTANT]
> Azure Monitor OpenTelemetry Exporter pour les applications .NET, Node.js et Python est actuellement en PRÉVERSION.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="limitations-of-preview-release"></a>Limitations de la préversion

### <a name="net"></a>[.NET](#tab/net)

Prenez le temps de déterminer si cette préversion vous convient. Elle **active le suivi distribué uniquement** et _exclut_ les éléments suivants :
 - API de métriques (métriques personnalisées, [métriques pré-agrégées](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Métriques temps réel](live-stream.md)
 - API de journalisation (journaux de console, bibliothèques de journalisation, etc.)
 - Capture automatique des exceptions non gérées
 - [Profiler](profiler-overview.md)
 - [Débogueur de capture instantanée](snapshot-debugger.md)
 - [Stockage sur disque hors connexion et logique de nouvelle tentative](telemetry-channels.md#built-in-telemetry-channels)
 - [Authentification Azure AD](azure-ad-authentication.md)
 - [Échantillonnage](sampling.md)
 - Remplissage automatique du nom du rôle cloud et de l’instance du rôle cloud dans les environnements Azure
 - Remplissage automatique de l’ID utilisateur et de l’ID utilisateur authentifié lors de l’utilisation du [SDK JavaScript Application Insights](javascript.md)
 - Remplissage automatique de l’adresse IP de l’utilisateur (pour déterminer les attributs de l’emplacement)
 - Possibilité de remplacer le [nom de l’opération](correlation.md#data-model-for-telemetry-correlation)
 - Possibilité de définir manuellement l’ID utilisateur ou l’ID utilisateur authentifié
 - Propagation du nom de l’opération à la télémétrie des dépendances
 - Propagation du contexte de suivi distribué (bibliothèques d’instrumentation) par le biais du Worker Azure Functions

Les utilisateurs qui ont besoin d’une expérience complète doivent utiliser le SDK [ASP.NET](asp-net.md) ou [ASP.NET Core](asp-net-core.md) Application Insights existant jusqu’à ce que l’offre OpenTelemetry ne soit plus en préversion.

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Prenez le temps de déterminer si cette préversion vous convient. Elle active le suivi distribué uniquement et _exclut_ les éléments suivants :
 - API de métriques (métriques personnalisées, [métriques pré-agrégées](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Métriques temps réel](live-stream.md)
 - API de journalisation (journaux de console, bibliothèques de journalisation, etc.)
 - Capture automatique des exceptions non gérées
 - [Authentification Azure AD](azure-ad-authentication.md)
 - [Échantillonnage](sampling.md)
 - Remplissage automatique du nom du rôle cloud et de l’instance du rôle cloud dans les environnements Azure
 - Remplissage automatique de l’ID utilisateur et de l’ID utilisateur authentifié lors de l’utilisation du [SDK JavaScript Application Insights](javascript.md)
 - Remplissage automatique de l’adresse IP de l’utilisateur (pour déterminer les attributs de l’emplacement)
 - Possibilité de remplacer le [nom de l’opération](correlation.md#data-model-for-telemetry-correlation)
 - Possibilité de définir manuellement l’ID utilisateur ou l’ID utilisateur authentifié
 - Propagation du nom de l’opération à la télémétrie des dépendances

Les utilisateurs qui ont besoin d’une expérience complète doivent utiliser le [SDK Node.js Application Insights](nodejs.md) existant jusqu’à ce que l’offre OpenTelemetry ne soit plus en préversion.

> [!WARNING] 
> À l’heure actuelle, cet outil d’exportation ne fonctionne que pour les environnements Node.js. Utilisez le [SDK JavaScript Application Insights](javascript.md) pour les scénarios web/navigateur.

### <a name="python"></a>[Python](#tab/python)

Prenez le temps de déterminer si cette préversion vous convient. Elle **active le suivi distribué uniquement** et _exclut_ les éléments suivants :
 - API de métriques (métriques personnalisées, [métriques pré-agrégées](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics))
 - [Métriques temps réel](live-stream.md)
 - API de journalisation (journaux de console, bibliothèques de journalisation, etc.)
 - Capture automatique des exceptions non gérées
 - Stockage sur disque hors connexion et logique de nouvelle tentative
 - [Authentification Azure AD](azure-ad-authentication.md)
 - [Échantillonnage](sampling.md)
 - Remplissage automatique du nom du rôle cloud et de l’instance du rôle cloud dans les environnements Azure
 - Remplissage automatique de l’ID utilisateur et de l’ID utilisateur authentifié lors de l’utilisation du [SDK JavaScript Application Insights](javascript.md)
 - Remplissage automatique de l’adresse IP de l’utilisateur (pour déterminer les attributs de l’emplacement)
 - Possibilité de remplacer le [nom de l’opération](correlation.md#data-model-for-telemetry-correlation)
 - Possibilité de définir manuellement l’ID utilisateur ou l’ID utilisateur authentifié
 - Propagation du nom de l’opération à la télémétrie des dépendances
 - Propagation du contexte de suivi distribué (bibliothèques d’instrumentation) par le biais du Worker Azure Functions

Les utilisateurs qui ont besoin d’une expérience complète doivent utiliser le [SDK Python-OpenCensus Application Insights](opencensus-python.md) existant jusqu’à ce que l’offre OpenTelemetry ne soit plus en préversion.

---

## <a name="get-started"></a>Bien démarrer

Suivez les étapes de cette section et vous serez en mesure d’instrumenter votre application avec OpenTelemetry.

### <a name="prerequisites"></a>Prérequis

- Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/)
- Ressource Application Insights - [Créer une ressource Application Insights](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- Application utilisant une version officiellement prise en charge de [.NET Core](https://dotnet.microsoft.com/download/dotnet) ou de [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) >= `.NET Framework 4.6.1`.


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- Application utilisant une [version officiellement prise en charge](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments) du runtime Node.js.
  - [Runtimes pris en charge par OpenTelemetry](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Runtimes pris en charge par Azure Monitor OpenTelemetry Exporter](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- Application Python utilisant la version 3.6+


---

### <a name="install-the-client-libraries"></a>Installer les bibliothèques de client

#### <a name="net"></a>[.NET](#tab/net)

Installez le package NuGet [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) le plus récent :

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

Si vous recevez une erreur de type « Aucune version n’est disponible pour le package 'Azure.Monitor.OpenTelemetry.Exporter' », cela est probablement dû au fait qu’il manque le paramètre des sources du package NuGet. Vous pouvez essayer de spécifier la source avec l’option `-s` :

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Installez ces packages :

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

Les packages suivants sont également utilisés pour certains scénarios spécifiques décrits plus loin dans cet article.

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

Installez le package Pypi [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) le plus récent.

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>Activer Azure Monitor Application Insights

#### <a name="add-opentelemetry-instrumentation-code"></a>Ajouter du code d’instrumentation OpenTelemetry

##### <a name="net"></a>[.NET](#tab/net)

Le code suivant illustre l’activation d’OpenTelemetry dans une application console C# en configurant OpenTelemetry TracerProvider. Ce code doit être présent au démarrage de l’application. Pour ASP.NET Core, nous utilisons généralement la méthode `ConfigureServices` de la classe `Startup` de l’application. Pour les applications ASP.NET, nous utilisons généralement `Global.aspx.cs`.

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> Les classes `Activity` et `ActivitySource` de l’espace de noms `System.Diagnostics` représentent les concepts OpenTelemetry de `Span` et `Tracer` respectivement. Et vous créez `ActivitySource` directement à l’aide de son constructeur au lieu d’utiliser `TracerProvider` (chaque [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) doit être explicitement connecté à `TracerProvider` à l’aide de `AddSource()`). Cela est dû au fait que des parties de l’API de suivi OpenTelemetry sont incorporées directement dans le runtime .NET. [Plus d’informations](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api)

##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Le code suivant illustre l’activation d’OpenTelemetry dans une application Node.js simple.

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

Le code suivant illustre l’activation d’OpenTelemetry dans une application Python simple.

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

> [!TIP]
> Ajoutez des [bibliothèques d’instrumentation](#instrumentation-libraries) pour collecter automatiquement les données de télémétrie dans les frameworks/bibliothèques populaires.

#### <a name="set-application-insights-connection-string"></a>Définir la chaîne de connexion Application Insights

Remplacez l’espace réservé `<Your Connection String>` dans le code ci-dessus par la chaîne de connexion de votre ressource Application Insights.

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="Capture d’écran de la chaîne de connexion Application Insights.":::

#### <a name="confirm-data-is-flowing"></a>Confirmer le déplacement des données

Exécutez votre application et ouvrez votre onglet Ressource Application Insights sur le portail Azure. L’affichage des données dans le portail peut prendre plusieurs minutes.

> [!NOTE]
> Si vous n’êtes pas en mesure d’exécuter l’application ou si vous n’obtenez pas de données comme prévu, accédez à [Résolution des problèmes](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Capture d’écran de l’onglet Vue d’ensemble d’Application Insights avec les demandes serveur et le temps de réponse du serveur mis en évidence.":::

> [!IMPORTANT]
> Si vous avez deux ou plusieurs services émettant de la télémétrie vers la même ressource Application Insights, vous devez [définir les noms des rôles cloud](#set-cloud-role-name-and-cloud-role-instance) pour les représenter correctement sur la cartographie d’application.

> [!NOTE]
> Dans le cadre de l’utilisation de l’instrumentation Application Insights, nous collectons et envoyons des données de diagnostic à Microsoft. Ces données nous permettent d’exécuter et d’améliorer Application Insights. Vous avez la possibilité de désactiver la collecte de données non essentielles. [En savoir plus](./statsbeat.md)

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>Définir le nom du rôle cloud et l’instance de rôle cloud

Vous pouvez définir le [nom du rôle cloud](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map) et l’instance de rôle cloud via les attributs [Ressource](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk). Cela remplace la valeur par défaut du nom du rôle cloud et de l’instance de rôle cloud par quelque chose qui a un sens pour votre équipe. Cela s’affiche sur la cartographie d’application en tant que nom sous un nœud. Le nom du rôle cloud utilise les attributs `service.namespace` et `service.name`, mais il retourne à la valeur `service.name` si `service.namespace` n’est pas défini. L’instance de rôle cloud utilise la valeur d’attribut `service.instance.id`.

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

Référence : [Conventions sémantiques des ressources](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>échantillonnage

Bien que l’échantillonnage est pris en charge dans OpenTelemetry, il n’est pas pris en charge dans Azure Monitor OpenTelemetry Explorer pour l’instant.

> [!WARNING]
> L’activation de l’échantillonnage dans OpenTelemetry rend les métriques standard et basées sur les journaux extrêmement inexactes, ce qui aura un impact négatif sur toutes les expériences Application Insights. En outre, l’activation de l’échantillonnage avec les SDK Application Insights existants entraîne des traces rompues.

## <a name="instrumentation-libraries"></a>Bibliothèques d’instrumentation
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
Les bibliothèques suivantes sont validées pour fonctionner avec la Préversion :

> [!WARNING]
> Les bibliothèques d’instrumentation sont basées sur des spécifications OpenTelemetry expérimentales. L’engagement de prise en charge de la **préversion** par Microsoft permet de s’assurer que les bibliothèques listées ci-dessous émettent des données pour Azure Monitor Application Insights, mais il est possible que des modifications avec rupture ou qu’un mappage expérimental bloquent certains éléments de données.

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md) Version : [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md) Version : [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- [Clients HTTP](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md) Version : [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- [http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md) Version : [0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django) Version : [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) Version : [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- [Requêtes](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests) Version : [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>Base de données

#### <a name="net"></a>[.NET](#tab/net)

- [Client SQL](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md) Version : [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql) Version : [0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2) Version : [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> L’offre de **préversion** comprend uniquement les instrumentations qui gèrent les requêtes HTTP et de base de données. Pour plus d’informations, consultez [Conventions sémantiques OpenTelemetry](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions).

## <a name="modify-telemetry"></a>Modifier la télémétrie

### <a name="add-span-attributes"></a>Ajouter des attributs d’étendue

Les attributs d’étendue peuvent être ajoutés de l’une des deux manières suivantes.
1. Avec les options fournies par les [bibliothèques d’instrumentation](#instrumentation-libraries).
2. Via l’ajout d’un processeur d’étendue personnalisé.

Ces attributs peuvent inclure l’ajout d’une propriété métier personnalisée à vos données de télémétrie. Vous pouvez également utiliser des attributs pour définir des champs facultatifs dans le schéma Application Insights, comme l’ID utilisateur ou l’adresse IP du client.

> [!TIP]
> L’avantage d’utiliser les « options fournies par les bibliothèques d’instrumentation » (le cas échéant) est que l’ensemble du contexte est disponible, ce qui signifie que les utilisateurs peuvent choisir d’ajouter ou de filtrer des attributs supplémentaires. Par exemple, l’option enrichir de la bibliothèque d’instrumentation HttpClient comprend la possibilité de donner aux utilisateurs l’accès à httpRequestMessage, d’y effectuer une sélection et de stocker l’élément en tant qu’attribut.

#### <a name="add-custom-property"></a>Ajouter une propriété personnalisée

Tous les [attributs](#add-span-attributes) ajoutés à l’activité/l’étendue sont exportés en tant que propriétés personnalisées. Ils remplissent le champ _customDimensions_ dans les tables de requêtes et/ou de dépendances dans Application Insights.

##### <a name="net"></a>[.NET](#tab/net)

1. De nombreuses bibliothèques d’instrumentation fournissent une option enrichir. Reportez-vous au fichier Lisez-moi des bibliothèques d’instrumentation individuelles pour obtenir de l’aide.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient et HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. Utilisation d’un processeur personnalisé :

> [!TIP]
> Ajoutez le processeur indiqué ci-dessous AVANT Azure Monitor Exporter.

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

Ajoutez `ActivityEnrichingProcessor.cs` à votre projet avec le code ci-dessous.

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Utilisation d’un processeur personnalisé :

> [!TIP]
> Ajoutez le processeur indiqué ci-dessous AVANT Azure Monitor Exporter.

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

Utilisation d’un processeur personnalisé :

> [!TIP]
> Ajoutez le processeur indiqué ci-dessous AVANT Azure Monitor Exporter.

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

Ajoutez `SpanEnrichingProcessor.py` à votre projet avec le code ci-dessous.

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>Configurer l’adresse IP de l’utilisateur

Vous pouvez renseigner le champ _client_IP_ pour les requêtes en définissant l’attribut `http.client_ip` sur activité/étendue. Application Insights utilise l’adresse IP pour générer des attributs d’emplacement utilisateur, puis [la supprime par défaut](ip-collection.md#default-behavior).

##### <a name="net"></a>[.NET](#tab/net)

Utilisez [l’exemple d’ajout d’une propriété personnalisée](#add-custom-property), mais changez les lignes de code suivantes dans `ActivityEnrichingProcessor.cs` :

```C#
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Utilisez [l’exemple d’ajout d’une propriété personnalisée](#add-custom-property), mais changez les lignes de code suivantes :

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

Utilisez [l’exemple d’ajout d’une propriété personnalisée](#add-custom-property), mais changez les lignes de code suivantes dans `SpanEnrichingProcessor.py` :

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>Filtrer les données de télémétrie

Vous pouvez utiliser les méthodes suivantes pour filtrer les données de télémétrie avant de quitter votre application.

#### <a name="net"></a>[.NET](#tab/net)

1. De nombreuses bibliothèques d’instrumentation fournissent une option de filtre. Reportez-vous au fichier Lisez-moi des bibliothèques d’instrumentation individuelles pour obtenir de l’aide.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient et HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. Utilisation d’un processeur personnalisé :
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    Ajoutez `ActivityFilteringProcessor.cs` à votre projet avec le code ci-dessous.
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. Si une source particulière n’est pas explicitement ajoutée à l’aide de `AddSource("ActivitySourceName")`, alors aucune des activités créées à l’aide de cette source n’est exportée.
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

1. Option d’exclusion d’URL fournie par de nombreuses bibliothèques d’instrumentation http.

    Vous trouverez ci-dessous un exemple de la façon d’exclure une URL d’un suivi à l’aide de la [bibliothèque d’instrumentation http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http).
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. Utilisation d’un processeur personnalisé. Vous pouvez utiliser un processeur d’étendues personnalisé pour exclure certaines étendues de l’exportation. Pour marquer des étendues à ne pas exporter, définissez leur `TraceFlag` sur `DEFAULT`.
Utilisez [l’exemple d’ajout d’une propriété personnalisée](#add-custom-property), mais changez les lignes de code suivantes :

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. Option d’exclusion d’URL fournie par de nombreuses bibliothèques d’instrumentation http.

    Vous trouverez ci-dessous un exemple de la façon d’exclure une URL d’un suivi à l’aide de l’instrumentation [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask).
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. Utilisation d’un processeur personnalisé. Vous pouvez utiliser un processeur d’étendues personnalisé pour exclure certaines étendues de l’exportation. Pour marquer des étendues à ne pas exporter, définissez leur `TraceFlag` sur `DEFAULT`.
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    Ajoutez `SpanFilteringProcessor.py` à votre projet avec le code ci-dessous.
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>Activer OTLP Exporter

Vous pouvez activer OpenTelemetry Protocol (OTLP) Exporter en plus d’Azure Monitor Exporter pour envoyer vos données de télémétrie à deux emplacements.

> [!NOTE]
> OTLP Exporter est illustré pour des raisons pratiques uniquement. Nous ne prenons pas officiellement en charge OTLP Exporter ni les composants ou les expériences tierces qui en découlent. Nous vous suggérons de signaler le problème auprès de [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector) pour les problèmes OpenTelemetry qui ne relèvent pas du Support Azure.

#### <a name="net"></a>[.NET](#tab/net)

1. Installez le package [OpenTelemetry.Exporter.OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/) avec [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) dans votre projet.

2. Ajoutez l’extrait de code suivant. Cet exemple suppose que vous disposez d’un OpenTelemetry Collector avec un récepteur OTLP en cours d’exécution. Pour plus de détails, consultez l’exemple [ici](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs).

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

1. Installez le package [OpenTelemetry Collector Exporter](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http) avec [Azure Monitor OpenTelemetry Exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter) dans votre projet.


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. Ajoutez l’extrait de code suivant. Cet exemple suppose que vous disposez d’un OpenTelemetry Collector avec un récepteur OTLP en cours d’exécution. Pour plus de détails, consultez l’exemple [ici](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node).

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. Installez les packages [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) et [opentelemetry-exporter-otlp](https://pypi.org/project/opentelemetry-exporter-otlp/).

2. Ajoutez l’extrait de code suivant. Cet exemple suppose que vous disposez d’un OpenTelemetry Collector avec un récepteur OTLP en cours d’exécution. Pour plus de détails, consultez ce fichier [LISEZ-MOI](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector).

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="enable-diagnostic-logging"></a>Activer la journalisation des diagnostics

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor Exporter utilise EventSource pour sa propre journalisation interne. Les journaux de l’outil d’exportation sont disponibles pour n’importe quel EventListener en vous abonnant à la source nommée « OpenTelemetry-AzureMonitor-Exporter ». Pour obtenir des étapes de dépannage détaillées, consultez [Résolution des problèmes OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting).

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Azure Monitor Exporter utilise l’enregistreur OpenTelemetry API Logger pour les journaux internes. Vous pouvez l’activer à l’aide du code suivant. 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor Exporter utilise la [bibliothèque](https://docs.python.org/3/library/logging.html) de journalisation standard Python pour sa propre journalisation interne. Les journaux de l’API OpenTelemetry et d’Azure Monitor Exporter sont généralement enregistrés avec un niveau de gravité AVERTISSEMENT ou ERREUR pour une activité irrégulière, et INFO pour une activité normale/réussie. Par défaut, le niveau de gravité de la bibliothèque de journalisation Python par défaut est AVERTISSEMENT. Vous devez donc modifier le niveau de gravité pour afficher les journaux sous cette gravité. Vous trouverez ci-dessous un exemple de sortie des journaux pour la gravité TOUS vers la console ET un fichier.

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>Problèmes connus

Les problèmes connus pour Azure Monitor OpenTelemetry Exporters incluent : 

- Le nom de l’opération est manquant sur la télémétrie des dépendances, ce qui a un impact négatif sur l’expérience de l’onglet des échecs et des performances.
- Le modèle d’appareil est manquant sur la télémétrie de la demande et de la dépendance, ce qui a un impact négatif sur l’analyse de la cohorte d’appareils.
- Le nom du serveur de base de données ne figure pas dans le nom de la dépendance, ce qui agrège de manière incorrecte les tables portant le même nom sur des serveurs différents.

## <a name="support"></a>Support

- Passez en revue les étapes de résolution des problèmes décrites dans cet article.
- Pour les problèmes de support Azure, ouvrez un [ticket de support Azure](https://azure.microsoft.com/support/create-ticket/).

### <a name="net"></a>[.NET](#tab/net)

Pour les problèmes OpenTelemetry, contactez directement la [communauté OpenTelemetry .NET](https://github.com/open-telemetry/opentelemetry-dotnet).

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Pour les problèmes OpenTelemetry, contactez directement la [communauté OpenTelemetry JavaScript](https://github.com/open-telemetry/opentelemetry-js).

### <a name="python"></a>[Python](#tab/python)

Pour les problèmes OpenTelemetry, contactez directement la [communauté OpenTelemetry Python](https://github.com/open-telemetry/opentelemetry-python).

---

## <a name="opentelemetry-feedback"></a>Commentaires OpenTelemetry

- Remplissez [l’enquête sur les commentaires des clients](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) de la communauté OpenTelemetry.
- Parlez de vous à Microsoft en rejoignant notre [communauté OpenTelemetry Early Adopter](https://aka.ms/AzMonOTel/).
- Discutez avec d’autres utilisateurs d’Azure Monitor dans la [communauté technique de Microsoft](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Étapes suivantes

### <a name="net"></a>[.NET](#tab/net)

- Examinez le code source dans le [référentiel GitHub Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter).
- Pour installer le package NuGet, recherchez les mises à jour ou affichez les notes de publication, visitez la page [Package NuGet Azure Monitor Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/).
- Familiarisez-vous davantage avec Azure Monitor Application Insights et OpenTelemetry grâce à [l’exemple d’application Azure Monitor](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization).
- Pour en savoir plus sur OpenTelemetry et sa communauté, visitez le [référentiel GitHub OpenTelemetry .NET](https://github.com/open-telemetry/opentelemetry-dotnet).
- [Activez la supervision utilisateur web/navigateur](javascript.md) pour activer les expériences d’utilisation.


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- Examinez le code source dans le [référentiel GitHub Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter).
- Pour installer le package NPM, recherchez les mises à jour ou affichez les notes de publication, visitez la page [Package NPM Azure Monitor Exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter).
- Familiarisez-vous davantage avec Azure Monitor Application Insights et OpenTelemetry grâce à [l’exemple d’application Azure Monitor](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples).
- Pour en savoir plus sur OpenTelemetry et sa communauté, visitez le [référentiel GitHub OpenTelemetry JavaScript](https://github.com/open-telemetry/opentelemetry-js).
- [Activez la supervision utilisateur web/navigateur](javascript.md) pour activer les expériences d’utilisation.

### <a name="python"></a>[Python](#tab/python)

- Examinez le code source dans le [référentiel GitHub Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md).
- Pour installer le package PyPI, recherchez les mises à jour ou affichez les notes de publication, visitez la page [Package PyPI Azure Monitor Exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/).
-  Familiarisez-vous davantage avec Azure Monitor Application Insights et OpenTelemetry grâce à [l’exemple d’application Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python).
- Pour en savoir plus sur OpenTelemetry et sa communauté, visitez le [référentiel GitHub OpenTelemetry Python](https://github.com/open-telemetry/opentelemetry-python).
- [Activez la supervision utilisateur web/navigateur](javascript.md) pour activer les expériences d’utilisation.

---
