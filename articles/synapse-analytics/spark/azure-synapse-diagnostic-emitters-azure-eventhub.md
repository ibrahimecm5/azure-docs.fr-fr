---
title: Collecter les journaux et les métriques de vos applications Apache Spark avec Azure Event Hubs
description: Dans ce tutoriel, vous allez voir comment utiliser l’extension d’émission de diagnostic Synapse Apache Spark qui permet d’envoyer vers Azure Event Hubs des journaux, des journaux des événements et des métriques concernant les applications Apache Spark.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: 2370e3895cc70a4303c9d91d300b47b32913ac7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131996316"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs"></a>Collecter les journaux et les métriques de vos applications Apache Spark avec Azure Event Hubs 

L’extension d’émission de diagnostic Synapse Apache Spark est une bibliothèque qui permet à une application Apache Spark d’envoyer des journaux, des journaux des événements et des métriques vers une ou plusieurs destinations, y compris Azure Log Analytics, le stockage Azure et Azure Event Hubs. 

Dans ce tutoriel, vous apprenez à utiliser l’extension d’émission de diagnostic Synapse Apache Spark pour envoyer à vos hubs d’événements Azure les journaux, journaux d’événements et métriques des applications Apache Spark.

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>Collecter des journaux et des métriques pour les envoyer vers Azure Event Hubs

### <a name="step-1-create-an-azure-event-hub-instance"></a>Étape 1 : Créer une instance Azure Event Hub

Pour collecter les journaux de diagnostic et les métriques afin de les envoyer dans Azure Event Hubs, vous pouvez utiliser l’instance Azure Event Hubs existante.
Si vous n’en avez pas, vous pouvez [créer un hub d’événements](../../event-hubs/event-hubs-create.md).

### <a name="step-2-create-an-apache-spark-configuration-file"></a>Étape 2 : Créer un fichier de configuration pour Apache Spark

Créez un fichier `diagnostic-emitter-azure-event-hub-conf.txt` puis copiez-y le contenu suivant. Vous pouvez également télécharger un [exemple de modèle](https://go.microsoft.com/fwlink/?linkid=2169375) pour la configuration du pool Apache Spark.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

Dans le fichier de configuration, configurez les paramètres suivants : `<connection-string>`.
Pour une description des paramètres, consultez [Configurations Azure EventHub](#available-configurations)

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>Étape 3 : Charger le fichier de configuration Apache Spark dans un pool Apache Spark

1. Accédez à votre pool Apache Spark dans Synapse Studio **(Gérer -> Pools Apache Spark)**
2. Cliquez sur le bouton **« ... »** situé à droite de votre pool Apache Spark, puis sélectionnez **Configuration Apache Spark**
3. Cliquez sur **Charger**, choisissez le fichier de configuration « .txt », puis cliquez sur **Appliquer**.

## <a name="available-configurations"></a>Configurations disponibles

| Configuration                                                               | Description                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | Obligatoire. Noms des destinations des émetteurs de diagnostic séparés par des virgules.                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Obligatoire. Type de destination intégrée. Pour pouvoir activer la destination Azure Event Hubs, la valeur doit être `AzureEventHub`.                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | facultatif. Catégories de journaux sélectionnées séparées par des virgules. Les valeurs disponibles sont les suivantes : `DriverLog`, `ExecutorLog`, `EventLog`, `Metrics`. Si aucune valeur n’est définie, la valeur par défaut sera **all** (toutes les catégories).              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | Optionnel. Chaîne de connexion de l’instance Azure Event Hubs. Ce champ doit correspondre à ce modèle `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>` |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Requis si `.secret` n'est pas spécifié. Nom du [coffre de clés Azure](../../key-vault/general/overview.md) dans lequel est stocké le secret (chaîne de connexion).                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Obligatoire si `.secret.keyVault` est spécifié. Nom du coffre de clés Azure dans lequel est stocké le secret (chaîne de connexion).                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.linkedService` | Optionnel. Nom du service lié au coffre de clés Azure. Quand il est activé dans le pipeline Synapse, ceci est nécessaire pour obtenir le secret auprès d’AKV. (Vérifiez que MSI dispose de l’autorisation de lecture sur le coffre de clés Azure). |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | facultatif. Noms des événements Spark séparés par des virgules. Vous pouvez spécifier les événements à collecter. Par exemple : `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | facultatif. Noms des enregistreurs d’événements log4j séparés par des virgules. Vous pouvez spécifier les journaux à collecter. Par exemple : `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | facultatif. Suffixes des noms de métriques Spark séparés par des virgules. Vous pouvez spécifier les métriques à collecter. Par exemple : `jvm.heap.used` |


> [!NOTE]
>
> La chaîne de connexion de l’instance Azure Event Hubs doit toujours contenir le `EntityPath`, qui est le nom de l’instance Azure Event Hubs.

## <a name="log-data-sample"></a>Exemple de données de journal

Voici un exemple d’enregistrement de journal au format JSON :

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>Espace de travail Synapse où est activée la protection contre l’exfiltration de données

Les espaces de travail Azure Synapse Analytics prennent en charge l’activation de la protection contre l’exfiltration de données pour les espaces de travail. Avec la protection contre l’exfiltration de données, les journaux et les métriques ne peuvent pas être envoyés directement aux points de terminaison de destination. Vous pouvez créer des [points de terminaison privés managés](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md) pour différents points de terminaison de destination ou [créer des règles de pare-feu IP](../../synapse-analytics/security/synapse-workspace-ip-firewall.md) dans ce scénario.




