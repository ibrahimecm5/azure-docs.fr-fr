---
title: Azure Monitor Application Insights Java
description: APM pour les applications Java s’exécutant dans n’importe quel environnement sans modification du code. Suivi distribué et mise en correspondance d’applications.
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: c1bf5936e9e01a3d0446b99d9974011679b57b0a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432184"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>Instrumentation automatique basée sur OpenTelemetry d’Azure Monitor pour les applications Java

Cet article explique comment activer et configurer l’offre Java Azure Monitor basée sur OpenTelemetry. Après avoir suivi les instructions de cet article, vous serez en mesure d’utiliser Azure Monitor Application Insights pour surveiller votre application.

## <a name="get-started"></a>Bien démarrer
L’instrumentation automatique Java peut être activée sans modification du code.

### <a name="prerequisites"></a>Prérequis
- Application Java utilisant Java 8+
- Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/)
- Ressource Application Insights – [Créer une ressource Application Insights](create-workspace-resource.md#create-workspace-based-resource)

### <a name="enable-azure-monitor-application-insights"></a>Activer Azure Monitor Application Insights
**1. Télécharger le fichier jar d’instrumentation automatique**

#### <a name="1-download-jar-file"></a>1. Télécharger le fichier jar

Téléchargez le fichier [applicationinsights-agent-3.2.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.2/applicationinsights-agent-3.2.2.jar).

> [!WARNING]
> 
> - **Si vous effectuez une mise à niveau à partir de la préversion 3.0**
>
>    Exécutez toutes les [options de configuration](./java-standalone-config.md) avec précaution. La structure JSON a été complètement modifiée. Le nom de fichier est maintenant en minuscules.
> 
> - **Si vous effectuez une mise à niveau à partir de la version 3.0.x**
> 
>    Les noms d’opération et de télémétrie de demande sont désormais préfixés selon la méthode http (`GET`, `POST`, etc.).
>    Cette modification peut avoir un impact sur des tableaux de bord ou des alertes personnalisés s’ils dépendent des valeurs précédentes.
>    Pour plus d’informations, consultez les [notes de publication 3.1.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0).
>
> - **Si vous effectuez une mise à niveau à partir de la version 3.1.x**
> 
>    Les noms des dépendances des bases de données sont désormais plus concis, la requête complète (expurgée) étant toujours présente dans le champ `data`. Les noms des dépendances HTTP sont désormais plus descriptifs.
>    Cette modification peut avoir un impact sur des tableaux de bord ou des alertes personnalisés s’ils dépendent des valeurs précédentes.
>    Pour plus d’informations, consultez les [notes de publication 3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0).

#### <a name="2-point-the-jvm-to-the-jar-file"></a>2. Pointer la machine virtuelle Java (JVM) vers le fichier jar

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` aux arguments JVM de votre application. 

> [!TIP]
> Pour obtenir une aide sur la configuration des arguments JVM de votre application, consultez [Conseils pour la mise à jour de vos arguments JVM](./java-standalone-arguments.md).

#### <a name="3-set-application-insights-connection-string"></a>3. Définir la chaîne de connexion Application Insights

Pointez le fichier jar sur votre ressource Application Insights, en définissant une variable d’environnement :

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Soit en créant un fichier config nommé `applicationinsights.json` et en le plaçant dans le même répertoire que `applicationinsights-agent-3.2.2.jar`, avec le contenu suivant :

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Recherchez la chaîne de connexion sur votre ressource Application Insights.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Chaîne de connexion Application Insights":::

#### <a name="4-confirm-data-is-flowing"></a>4. Confirmer le déplacement des données

Exécutez votre application et ouvrez votre onglet Ressource Application Insights sur le portail Azure. L’affichage des données dans le portail peut prendre plusieurs minutes.

> [!NOTE]
> Si vous n’êtes pas en mesure d’exécuter l’application ou si vous n’obtenez pas de données comme prévu, accédez à la section [Résolution des problèmes](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Capture d’écran de l’onglet Vue d’ensemble d’Application Insights avec les demandes serveur et le temps de réponse du serveur mis en évidence.":::

> [!IMPORTANT]
> Si vous avez deux ou plusieurs services émettant de la télémétrie vers la même ressource Application Insights, vous devez [définir les noms des rôles cloud](java-standalone-config.md#cloud-role-name) pour les représenter correctement sur la cartographie d’application.

> [!NOTE]
> Dans le cadre de l’utilisation de l’instrumentation Application Insights, nous collectons et envoyons des données de diagnostic à Microsoft. Ces données nous permettent d’exécuter et d’améliorer Application Insights. Vous avez la possibilité de désactiver la collecte de données non essentielles. [Plus d’informations](./statsbeat.md)

## <a name="configuration-options"></a>Options de configuration

Dans le fichier `applicationinsights.json`, vous pouvez également configurer les paramètres suivants :

* Nom du rôle cloud
* Instance de rôle cloud
* échantillonnage
* Métriques JMX
* Dimensions personnalisées
* Processeurs de télémétrie (préversion)
* Journalisation collectée automatiquement
* Métriques de Micrometer collectées automatiquement (y compris les métriques de Spring Boot Actuator)
* Heartbeat
* Proxy HTTP
* Autodiagnostics

Pour plus d’informations, consultez les [options de configuration](./java-standalone-config.md).

## <a name="instrumentation-libraries"></a>Bibliothèques d’instrumentation

Java 3.x comprend les bibliothèques d’instrumentation suivantes.

### <a name="auto-collected-requests"></a>Requêtes collectées automatiquement

* Consommateurs JMS
* Consommateurs Kafka
* Netty/WebFlux
* Servlets
* Spring Scheduling

### <a name="auto-collected-dependencies"></a>Dépendances collectées automatiquement

Dépendances collectées automatiquement et propagation de trace distribuée en aval :

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS Client
* Jetty HttpClient
* JMS
* Kafka
* Client Netty
* OkHttp

Dépendances collectées automatiquement (sans propagation de trace distribuée en aval) :

* Cassandra
* JDBC
* MongoDB (asynchrone et synchrone)
* Redis (Lettuce et Jedis)

### <a name="auto-collected-logs"></a>Journaux collectés automatiquement

* java.util.logging
* Log4j (y compris les propriétés MDC)
* SLF4J/Logback (y compris les propriétés MDC)

### <a name="auto-collected-metrics"></a>Métriques collectées automatiquement

* Micrometer (y compris les métriques de Spring Boot Actuator)
* Métriques JMX

### <a name="azure-sdks"></a>SDK Azure

La télémétrie émise par ces Kits de développement logiciel (SDK) Azure est collectée automatiquement par défaut :

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10+
* [Recherche cognitive](/java/api/overview/azure/search-documents-readme) 11.3.0+
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0+
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0+
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0+
* [Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0+
* [Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0+
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Digital Twins - Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0+
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0+
* [Event Hubs - Magasin de points de contrôle de Stockage Blob Azure](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6+
* [Identity](/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault - Certificats](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6+
* [Key Vault - Clés](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6+
* [Key Vault - Secrets](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6+
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Storage - Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0+
* [Storage - Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0+
* [Storage - Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0+
* [Storage - Common](/java/api/overview/azure/storage-common-readme) 12.11.0+
* [Storage - Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0+
* [Storage - Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0+
* [Storage - Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0+
* [Analyse de texte](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4+

[//]: # "les noms et les liens ci-dessus ont été scrapés de https://azure.github.io/azure-sdk/releases/latest/java.html"
[//]: # "et synchronisés avec la version la plus ancienne dans Maven Central, basée sur azure-core 1.14.0"
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "pour (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    continue"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="modify-telemetry"></a>Modifier la télémétrie

### <a name="add-span-attributes"></a>Ajouter des attributs d’étendue
Vous pouvez utiliser `opentelemetry-api` pour ajouter des attributs à des étendues. Ces attributs peuvent inclure l’ajout d’une dimension métier personnalisée à votre télémétrie. Vous pouvez également utiliser des attributs pour définir des champs facultatifs dans le schéma Application Insights, comme l’identifiant utilisateur ou l’adresse IP du client.

#### <a name="add-custom-dimension"></a>Ajouter une dimension personnalisée
L’ajout d’une ou de plusieurs dimensions personnalisées alimentera le champ _customDimensions_ dans la table des requêtes, des dépendances ou des exceptions.

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.2.0 et les versions ultérieures.

Ajoutez `opentelemetry-api-1.6.0.jar` à votre application :

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Ajoutez des dimensions personnalisées dans votre code :

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("mycustomdimension", "myvalue1");
```

#### <a name="set-user-id"></a>Définir l’identifiant utilisateur
Renseignez le champ Identifiant utilisateur dans la table des requêtes, des dépendances ou des exceptions.

> [!IMPORTANT]
> Consultez la législation applicable relative à la confidentialité avant de définir l’identifiant utilisateur authentifié.

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.2.0 et les versions ultérieures.

Ajoutez `opentelemetry-api-1.6.0.jar` à votre application :

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Définissez `user_Id` dans votre code :

```java
import io.opentelemetry.api.trace.Span;

Span.current().setAttribute("enduser.id", "myuser");
```

### <a name="get-trace-id-or-span-id"></a>Obtenir l’ID de trace ou l’ID d’étendue

Vous pouvez utiliser `opentelemetry-api` pour obtenir l’ID de trace ou l’ID d’étendue. Cette action peut être réalisée pour ajouter ces identificateurs à la télémétrie de journalisation existante afin d’améliorer la corrélation lors du débogage et du diagnostic des problèmes.

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.2.0 et les versions ultérieures.

Ajoutez `opentelemetry-api-1.6.0.jar` à votre application :

```xml
<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-api</artifactId>
  <version>1.6.0</version>
</dependency>
```

Obtenez l’ID de trace et l’ID d’étendue de la requête dans votre code :

```java
import io.opentelemetry.api.trace.Span;

String traceId = Span.current().getSpanContext().getTraceId();
String spanId = Span.current().getSpanContext().getSpanId();
```

## <a name="custom-telemetry"></a>Télémétrie personnalisée

Notre objectif dans Application Insights Java 3+ est de vous permettre d’envoyer vos données de télémétrie personnalisées à l’aide d’API Standard.

Nous prenons actuellement en charge Micrometer, les frameworks de journalisation populaires, et le kit de développement logiciel (SDK) Application Insights Java 2.x. Application Insights Java 3 capture automatiquement les données de télémétrie envoyées via ces API, et les met en corrélation avec les données de télémétrie collectées automatiquement.

### <a name="supported-custom-telemetry"></a>Données de télémétrie personnalisées prises en charge

Le tableau ci-dessous représente les types de données de télémétrie personnalisées actuellement pris en charge que vous pouvez activer pour compléter l’agent Java 3.x. Pour résumer, les métriques personnalisées sont prises en charge via Micrometer, les exceptions et les traces personnalisées peuvent être activées par les frameworks de journalisation, les demandes, les dépendances et les exceptions personnalisées peuvent être activées par `opentelemetry-api` et tout type de données de télémétrie personnalisées est pris en charge par le [Kit de développement logiciel (SDK) Application Insights Java 2.x](#send-custom-telemetry-using-the-2x-sdk).

|                     | Micrometer | Log4j, logback, JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| **Événements personnalisés**   |            |                     |  Oui    |                   |
| **Métriques personnalisées**  |  Oui       |                     |  Oui    |                   |
| **Dépendances**    |            |                     |  Oui    |  Oui              |
| **Exceptions**      |            |  Oui                |  Oui    |  Oui              |
| **Affichages de pages**      |            |                     |  Oui    |                   |
| **Demandes**        |            |                     |  Oui    |  Oui              |
| **Traces**          |            |  Oui                |  Oui    |                   |

Nous n’avons pas l’intention de publier un kit SDK avec Application Insights 3.x pour le moment.

Application Insights Java 3.x écoute déjà les données de télémétrie envoyées au Kit de développement logiciel (SDK) Application Insights Java 2.x. Cette fonctionnalité est une partie essentielle de la mise à niveau pour les utilisateurs 2.x existants car elle comble un écart important dans notre prise en charge de la télémétrie personnalisée jusqu’à la disponibilité générale de l’API OpenTelemetry.

### <a name="send-custom-metrics-using-micrometer"></a>Envoyer des métriques personnalisées à l'aide de Micrometer

Ajoutez Micrometer à votre application :

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Utilisez le [registre global](https://micrometer.io/docs/concepts#_global_registry) de Micrometer pour créer un compteur :

```java
static final Counter counter = Metrics.counter("test_counter");
```

Utilisez le compteur pour enregistrer les métriques :

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Envoyer des traces et des exceptions personnalisées à l'aide de votre framework de journalisation préféré

Les journalisations Log4j, Logback et java.util.logging sont instrumentées automatiquement, et la journalisation effectuée via ces frameworks de journalisation est collectée automatiquement sous forme de données de télémétrie de trace et d'exception.

Par défaut, la journalisation n'est collectée que lorsqu'elle est effectuée au niveau INFO ou à un niveau supérieur.
Pour modifier ce niveau, consultez les [options de configuration](./java-standalone-config.md#auto-collected-logging).

Si vous souhaitez attacher des dimensions personnalisées à vos journaux, vous pouvez utiliser [Log4j 1.2 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) ou [Logback MDC](http://logback.qos.ch/manual/mdc.html). Application Insights Java 3.x capture automatiquement ces propriétés MDC en tant que dimensions personnalisées sur vos données de télémétrie de trace et d’exception.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>Envoyer une télémétrie personnalisée à l’aide du kit SDK 2.x

Ajoutez `applicationinsights-core-2.6.3.jar` à votre application (toutes les versions 2.x sont prises en charge par Application Insights Java 3.x, mais il est préférable d’utiliser la dernière version si vous avez le choix) :

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

Créez un TelemetryClient :

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

Utiliser le client pour envoyer des données de télémétrie personnalisées :

##### <a name="events"></a>Événements

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Mesures

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Les dépendances

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Journaux d’activité

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Exceptions

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

## <a name="troubleshooting"></a>Résolution des problèmes

Consultez l’article sur la [Résolution des problèmes](java-standalone-troubleshoot.md).

## <a name="support"></a>Support
- Consultez les [étapes de résolution des problèmes](java-standalone-troubleshoot.md).
- Pour les problèmes de support Azure, ouvrez un [ticket de support Azure](https://azure.microsoft.com/support/create-ticket/).
- Pour les problèmes OpenTelemetry, contactez directement la [communauté OpenTelemetry](https://opentelemetry.io/community/).

## <a name="opentelemetry-feedback"></a>Commentaires OpenTelemetry
- Remplissez [l’enquête sur les commentaires des clients](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) de la communauté OpenTelemetry.
- Parlez de vous à Microsoft en rejoignant notre [communauté d’utilisateurs précoces d’OpenTelemetry](https://aka.ms/AzMonOTel/).
- Discutez avec d’autres utilisateurs d’Azure Monitor dans la [communauté technique de Microsoft](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue le code source dans le [référentiel GitHub d’instrumentation automatique Java Azure Monitor](https://github.com/Microsoft/ApplicationInsights-Java).
- Pour en savoir plus sur OpenTelemetry et sa communauté, visitez le [référentiel GitHub OpenTelemetry Java](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- [Activez la supervision utilisateur web/navigateur](javascript.md) pour activer les expériences d’utilisation.
