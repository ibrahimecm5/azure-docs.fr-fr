---
title: Options de configuration – Azure Monitor Application Insights pour Java
description: Comment configurer Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 9c6fcc3fd0bff46bb3c1665b26502be188a3f83e
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158698"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Options de configuration – Azure Monitor Application Insights pour Java

> [!WARNING]
> **Si vous effectuez une mise à niveau à partir de la préversion 3.0**
>
> Examinez soigneusement toutes les options de configuration ci-dessous, car la structure json a complètement changé, en plus du nom de fichier désormais en minuscules.

## <a name="connection-string-and-role-name"></a>Chaîne de connexion et nom de rôle

La chaîne de connexion et le nom de rôle sont les paramètres les plus courants nécessaires pour commencer :

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

La chaîne de connexion est requise, et le nom de rôle est important chaque fois que vous envoyez des données à partir de différentes applications vers la même ressource Application Insights.

Vous trouverez plus de détails, ainsi que des options de configuration supplémentaires ci-dessous.

## <a name="configuration-file-path"></a>Chemin d'accès au fichier de configuration

Par défaut, Application Insights Java 3.x s’attend à ce que le fichier config soit nommé `applicationinsights.json` et se trouve dans le même répertoire que `applicationinsights-agent-3.2.3.jar`.

Vous pouvez spécifier votre propre chemin d’accès au fichier de configuration à l’aide d'un des éléments suivants :

* variable d’environnement `APPLICATIONINSIGHTS_CONFIGURATION_FILE`, ou
* propriété système Java `applicationinsights.configuration.file`

Si vous spécifiez un chemin d’accès relatif, il sera résolu par rapport au répertoire où se trouve `applicationinsights-agent-3.2.3.jar`.

## <a name="connection-string"></a>Chaîne de connexion

La chaîne de connexion est obligatoire. Vous pouvez trouver votre chaîne de connexion dans votre ressource Application Insights :

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Chaîne de connexion Application Insights":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Vous pouvez également définir la chaîne de connexion à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_CONNECTION_STRING` (qui sera alors prioritaire sur la chaîne de connexion spécifiée dans la configuration json).

Si vous ne définissez pas la chaîne de connexion, l’agent Java sera désactivé.

## <a name="cloud-role-name"></a>Nom du rôle cloud

Le nom du rôle cloud est utilisé pour étiqueter le composant sur la cartographie d’application.

Si vous souhaitez définir le nom du rôle cloud :

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Si le nom du rôle cloud n’est pas défini, le nom de la ressource Application Insights sera utilisé pour étiqueter le composant sur la cartographie d’application.

Vous pouvez également définir le nom du rôle cloud à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_ROLE_NAME` (qui sera alors prioritaire sur le nom du rôle cloud spécifié dans la configuration json).

## <a name="cloud-role-instance"></a>Instance de rôle cloud

Par défaut, l’instance de rôle cloud correspond au nom de la machine.

Si vous souhaitez définir l'instance de rôle cloud sur autre chose que le nom de la machine :

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Vous pouvez également définir l’instance du rôle cloud à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_ROLE_INSTANCE` (qui sera alors prioritaire sur l’instance du rôle cloud spécifié dans la configuration json).

## <a name="sampling"></a>échantillonnage

L’échantillonnage s'avère utile s'il vous faut réduire les coûts.
L’échantillonnage s'effectue sous forme de fonction sur l’ID d’opération (également appelé ID de trace) pour permettre au même ID d’opération de générer la même décision d’échantillonnage. Ainsi, vous n'obtenez pas de parties de transaction distribuée échantillonnées quand d'autres ne le sont pas.

Par exemple, si vous définissez l’échantillonnage sur 10 %, vous ne verrez que 10 % de vos transactions, mais chacun de ces 10 % présentera les détails des transactions de bout en bout.

Voici un exemple montrant comment définir l’échantillonnage de façon à capturer environ **1/3 de toutes les transactions**. Vérifiez que le taux d’échantillonnage est correct pour votre cas d’utilisation :

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Vous pouvez également définir le pourcentage d’échantillonnage à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (qui sera alors prioritaire sur le pourcentage d’échantillonnage spécifié dans la configuration json).

> [!NOTE]
> Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier. L’échantillonnage ne prend actuellement en charge aucune autre valeur.

## <a name="sampling-overrides-preview"></a>Remplacements d’échantillonnage (préversion)

Cette fonctionnalité est en préversion, à partir de 3.0.3.

Les remplacements d’échantillonnage vous permettent de remplacer le [pourcentage d’échantillonnage par défaut](#sampling), par exemple :
* Définissez le pourcentage d’échantillonnage sur 0 (ou une valeur faible) pour les contrôles d’intégrité bruyants.
* Définissez le pourcentage d’échantillonnage sur 0 (ou une valeur faible) pour les appels de dépendance bruyants.
* Définissez le pourcentage d’échantillonnage sur 100 pour un type de requête important (par exemple `/login`) même si vous avez configuré l’échantillonnage par défaut sur une valeur inférieure.

Pour plus d’informations, consultez la documentation sur les [remplacements d’échantillonnage](./java-standalone-sampling-overrides.md).

## <a name="jmx-metrics"></a>Métriques JMX

Si vous souhaitez collecter des métriques JMX supplémentaires :

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` est le nom qui sera attribué à cette métrique JMX (peut être un nom quelconque).

`objectName` est le [Nom d’objet](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) du MBean JMX que vous souhaitez collecter.

`attribute` est le nom d’attribut à l’intérieur du MBean JMX que vous souhaitez collecter.

Les valeurs de métrique JMX numériques et booléennes sont prises en charge. Les métriques JMX booléennes sont mappées à `0` pour false et à `1` pour true.

## <a name="custom-dimensions"></a>Dimensions personnalisées

Si vous souhaitez ajouter des dimensions personnalisées à toutes vos données de télémétrie :

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

Vous pouvez utiliser `${...}` pour lire la valeur de la variable d’environnement spécifiée au démarrage.

> [!NOTE]
> À partir de la version 3.0.2, si vous ajoutez une dimension personnalisée nommée `service.version`, la valeur sera stockée dans la colonne `application_Version` de la table des journaux Application Insights plutôt que sous forme de dimension personnalisée.

## <a name="inherited-attribute-preview"></a>Attribut hérité (préversion)

À partir de la version 3.2.0, si vous souhaitez définir une dimension personnalisée par programme sur la télémétrie de vos demandes et faire en sorte qu’elle soit héritée par la télémétrie des dépendances qui suit :

```json
{
  "inheritedAttributes": [
    {
      "key": "mycustomer",
      "type": "string"
    }
  ]
}
```

## <a name="telemetry-processors-preview"></a>Processeurs de télémétrie (préversion)

Elle vous permet de configurer des règles qui seront appliquées aux données de télémétrie de requêtes, de dépendances et de traces. Par exemple :
 * Masquer des données sensibles
 * Ajouter de manière conditionnelle des dimensions personnalisées
 * Mettre à jour le nom de l’étendue, qui est utilisé pour agréger des données de télémétrie similaires dans le portail Azure.
 * Supprimer les attributs d’étendue spécifiques pour contrôler les coûts d’ingestion.

Pour plus d’informations, consultez la documentation du [processeur de télémétrie](./java-standalone-telemetry-processors.md).

> [!NOTE]
> Si vous envisagez de supprimer des étendues spécifiques (entières) pour contrôler le coût d’ingestion, consultez la rubrique [Remplacements d’échantillonnage](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Journalisation collectée automatiquement

Les journalisations Log4j, Logback et java.util.logging sont instrumentées automatiquement, et la journalisation effectuée via ces frameworks de journalisation est collectée automatiquement.

La journalisation est capturée uniquement si elle est conforme au niveau configuré pour le framework de journalisation, et si elle correspond également au niveau configuré pour Application Insights.

Par exemple, si votre infrastructure de journalisation est configurée pour consigner `WARN` (et versions ultérieures) à partir du package `com.example`, et que Application Insights est configuré pour capturer `INFO` (et versions ultérieures), alors Application Insights capturera uniquement `WARN` (et versions ultérieures) à partir du package `com.example`.

Le niveau par défaut configuré pour Application Insights est `INFO`. Si vous souhaitez modifier ce seuil :

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Vous pouvez également définir le niveau à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (qui sera alors prioritaire sur le niveau spécifié dans la configuration json).

Voici les valeurs de `level` valides que vous pouvez spécifier dans le fichier `applicationinsights.json`, et leurs correspondances avec les niveaux de journalisation dans les différents frameworks de journalisation :

| level             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR (ou SEVERE) | ERROR  | ERROR   | SEVERE  |
| WARN (ou WARNING) | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (ou FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (ou FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Si un objet d’exception est transmis à l’enregistreur d’événements, le message de journal (et les détails de l’objet d’exception) s’affiche dans le Portail Azure dans la table `exceptions` au lieu de la table `traces`.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Métriques de Micrometer collectées automatiquement (y compris les métriques de Spring Boot Actuator)

Si votre application utilise [Micrometer](https://micrometer.io), les métriques envoyées au registre global de Micrometer sont collectées automatiquement.

Par ailleurs, si votre application utilise [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), les métriques configurées par Spring Boot Actuator sont également collectées automatiquement.

Pour désactiver la collection automatique des métriques de Micrometer (y compris les métriques de Spring Boot Actuator) :

> [!NOTE]
> Les métriques personnalisées sont facturées séparément et peuvent occasionner des coûts supplémentaires. Veillez à consulter les [informations sur la tarification](https://azure.microsoft.com/pricing/details/monitor/) détaillées. Pour désactiver les métriques de Micrometer et de Spring Actuator, ajoutez la configuration ci-dessous à votre fichier de configuration.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Suppression d’une télémétrie collectée automatiquement spécifique

À partir de la version 3.0.3, une télémétrie collectée automatiquement spécifique peut être supprimée à l’aide des options de configuration suivantes :

```json
{
  "instrumentation": {
    "azureSdk": {
      "enabled": false
    },
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "rabbitmq": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Vous pouvez également supprimer ces instrumentations à l’aide de ces variables d’environnement sur `false` :

* `APPLICATIONINSIGHTS_INSTRUMENTATION_AZURE_SDK_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_RABBITMQ_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(qui seront ensuite prioritaires sur le paramètre activé spécifié dans la configuration json).

> [!NOTE]
> Si vous souhaitez un contrôle plus précis, par exemple pour supprimer une partie mais pas la totalité des appels redis, consultez la rubrique [Remplacements d’échantillonnage](./java-standalone-sampling-overrides.md).

## <a name="preview-instrumentations"></a>Instrumentations en préversion

À partir de la version 3.2.0, les instrumentations en préversion suivantes peuvent être activées :

```
{
  "preview": {
    "instrumentation": {
      "apacheCamel": {
        "enabled": true
      },
      "grizzly": {
        "enabled": true
      },
      "quartz": {
        "enabled": true
      },
      "springIntegration": {
        "enabled": true
      },
      "akka": { 
        "enabled": true
      },
    }
  }
}
```
> [!NOTE]
> L’instrumentation Akka est disponible à partir de la version 3.2.2

## <a name="heartbeat"></a>Heartbeat

Par défaut, Application Insights Java 3.x envoie une métrique de pulsation toutes les 15 minutes.
Si vous utilisez la métrique de pulsation pour déclencher des alertes, vous pouvez augmenter la fréquence de cette pulsation :

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Vous ne pouvez pas augmenter l’intervalle à plus de 15 minutes, car les données de pulsation sont également utilisées pour assurer le suivi de l’utilisation d’Application Insights.

## <a name="http-proxy"></a>Proxy HTTP

Si votre application se trouve derrière un pare-feu et n’est pas en mesure de se connecter directement à Application Insights (voir [Adresses IP utilisées par Application Insights](./ip-addresses.md)), vous pouvez configurer Application Insights Java 3.x pour utiliser un proxy HTTP :

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.x respecte également les propriétés système `https.proxyHost` et `https.proxyPort` globales si elles sont définies (et `http.nonProxyHosts` le cas échéant).

## <a name="metric-interval"></a>Intervalle de métrique

Cette fonctionnalité est en préversion.

Par défaut, les métriques sont capturées toutes les 60 secondes.

À partir de la version 3.0.3, vous pouvez modifier cet intervalle :

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

Le paramètre s’applique à toutes ces métriques :

* Compteurs de performances par défaut, par exemple processeur et mémoire
* Métriques personnalisées par défaut, par exemple le minutage du nettoyage de la mémoire
* Métriques JMX configurées ([voir ci-dessus](#jmx-metrics))
* Métriques Micrometer ([voir ci-dessus](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "NOTEZ que la prise en charge d’OpenTelemetry est en préversion privée jusqu’à ce que l’API OpenTelemetry atteigne soit la version 1.0"

[//]: # "## Prise en charge des versions de l’API OpenTelemetry antérieures à la version 1.0"

[//]: # "La prise en charge des versions antérieures à la version 1.0 de l’API OpenTelemetry est acceptée, parce que l’API n’est pas encore stable"
[//]: # "Par conséquent, chaque version de l’agent ne prend en charge qu’une version spécifique antérieure à la version 1.0 de l’API OpenTelemetry"
[//]: # "(cette limitation cessera de s’appliquer après la mise en production de l’API OpenTelemetry 1.0)."

[//]: # "```json"
[//]: # "{"
[//]: # "  \"preview\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="authentication-preview"></a>Authentification (préversion)
> [!NOTE]
> La fonctionnalité d’authentification est disponible à partir de la version 3.2.0-BETA

Elle vous permet de configurer l’agent pour générer les [informations d’identification de jeton](/java/api/overview/azure/identity-readme#credentials) requises pour l’authentification Azure Active Directory.
Pour plus d’informations, consultez la documentation sur l’[Authentification](./azure-ad-authentication.md).

## <a name="instrumentation-keys-overrides-preview"></a>Remplacements de clés d’instrumentation (préversion)

Cette fonctionnalité est en préversion, à partir de 3.2.3.

Les remplacements de clés d’instrumentation vous permettent de remplacer la [clé d’instrumentation par défaut](#connection-string), par exemple :
* Définissez une clé d’instrumentation pour un préfixe de chemin d’accès HTTP `/myapp1`.
* Définissez une autre clé d’instrumentation pour un autre préfixe de chemin d’accès HTTP `/myapp2/`.

```json
{
  "preview": {
    "instrumentationKeyOverrides": [
      {
        "httpPathPrefix": "/myapp1",
        "instrumentationKey": "12345678-0000-0000-0000-0FEEDDADBEEF"
      },
      {
        "httpPathPrefix": "/myapp2",
        "instrumentationKey": "87654321-0000-0000-0000-0FEEDDADBEEF"
      }
    ]
  }
}
```

## <a name="self-diagnostics"></a>Autodiagnostics

La fonctionnalité « Autodiagnostics » fait référence à la journalisation interne à partir d’Application Insights Java 3.x.

Elle permet de détecter et de diagnostiquer des problèmes liés à Application Insights.

Par défaut, Application Insights Java 3.x se connecte au niveau `INFO` au fichier `applicationinsights.log` et à la console, ce qui correspond à la configuration suivante :

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` peut être `file`, `console` ou `file+console`.

`level` peut être `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` ou `TRACE`.

`path` peut être un chemin d’accès absolu ou relatif. Les chemins d’accès relatifs sont résolus par rapport au répertoire où se trouve le fichier `applicationinsights-agent-3.2.3.jar`.

`maxSizeMb` est la taille maximale du fichier journal avant son remplacement.

`maxHistory` est le nombre de fichiers journaux remplacés et conservés (en plus du fichier journal actuel).

À partir de la version 3.0.2, vous pouvez également définir l’autodiagnostic `level` à l’aide de la variable d’environnement `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (qui sera alors prioritaire sur l’autodiagnostic spécifié dans la configuration json).

## <a name="an-example"></a>Exemple

Il s’agit simplement d’un exemple pour montrer à quoi ressemble un fichier de configuration avec plusieurs composants.
Veuillez configurer des options spécifiques en fonction de vos besoins.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```