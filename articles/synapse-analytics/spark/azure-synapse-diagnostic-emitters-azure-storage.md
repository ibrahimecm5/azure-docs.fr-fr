---
title: Collecter les journaux et les métriques de vos applications Apache Spark avec un compte de stockage Azure
description: Cet article explique comment utiliser l’extension d’émission de diagnostic de Synapse Spark pour collecter des journaux, des journaux d’événements et des métriques.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: 52d9c903cbd4aafe8799151663a37b09ed5676c2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241038"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-storage-account"></a>Collecter les journaux et les métriques de vos applications Apache Spark avec un compte de stockage Azure

L’extension d’émission de diagnostic Synapse Apache Spark est une bibliothèque qui permet à une application Apache Spark d’envoyer des journaux, des journaux des événements et des métriques vers une ou plusieurs destinations, y compris Azure Log Analytics, le stockage Azure et Azure Event Hubs. 

Dans ce tutoriel, vous apprenez à utiliser l’extension d’émission de diagnostic Synapse Apache Spark pour envoyer à votre compte de stockage les journaux, journaux d’événements et métriques de vos applications Apache Spark.

## <a name="collect-logs-and-metrics-to-storage-account"></a>Collecter les journaux et les métriques pour les envoyer vers un compte de stockage

### <a name="step-1-create-a-storage-account"></a>Étape 1 : Créez un compte de stockage.

Pour collecter les journaux de diagnostic et les métriques afin de les envoyer vers un compte de stockage, vous pouvez utiliser des comptes de stockage Azure existants. Si vous n’en avez pas, vous pouvez [créer un compte de stockage Blob Azure](../../storage/common/storage-account-create.md) ou [créer un compte de stockage à utiliser avec Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

### <a name="step-2-create-an-apache-spark-configuration-file"></a>Étape 2 : Créer un fichier de configuration pour Apache Spark

Créez un fichier `diagnostic-emitter-azure-storage-conf.txt` puis copiez-y le contenu suivant. Vous pouvez également télécharger un [exemple de modèle](https://go.microsoft.com/fwlink/?linkid=2169375) pour la configuration du pool Apache Spark.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureStorage
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.uri https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>
spark.synapse.diagnostic.emitter.MyDestination1.auth AccessKey
spark.synapse.diagnostic.emitter.MyDestination1.secret <storage-access-key>
```

Dans le fichier de configuration, configurez les paramètres suivants : `<my-blob-storage>`, `<container-name>`, `<folder-name>`, `<storage-access-key>`.
Pour une description des paramètres, consultez [Configurations du Stockage Azure](#available-configurations)

### <a name="step-3-upload-the-apache-spark-configuration-file-to-spark-pool"></a>Étape 3 : Charger le fichier de configuration Apache Spark dans un pool Spark

1. Accédez à votre pool Apache Spark dans Synapse Studio **(Gérer -> Pools Apache Spark)**
1. Cliquez sur le bouton **« ... »** situé à droite de votre pool Apache Spark, puis sélectionnez **Configuration Apache Spark**
1. Cliquez sur **Charger**, choisissez le fichier de configuration « .txt », puis cliquez sur **Appliquer**.

### <a name="step-4-view-the-logs-files-in-azure-storage-account"></a>Étape 4 : Afficher les fichiers journaux dans le compte de stockage Azure


Une fois que vous avez envoyé un travail au pool Apache Spark configuré, les fichiers de journaux et de métriques doivent être visibles dans le compte de stockage de destination.
Les journaux seront placés dans les chemins correspondants de chaque application par `<workspaceName>.<sparkPoolName>.<livySessionId>`.
Tous les fichiers journaux ont un format de lignes JSON (également appelé JSON délimité par des sauts de ligne ou ndjson), ce qui est pratique pour le traitement des données.

## <a name="available-configurations"></a>Configurations disponibles

| Configuration | Description |
| --- | --- |
| `spark.synapse.diagnostic.emitters`                                         | Obligatoire. Noms des destinations des émetteurs de diagnostic, séparés par des virgules. Par exemple, `MyDest1,MyDest2` |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | Obligatoire. Type de destination intégrée. Pour activer la destination Azure Storage, vous devez inclure `AzureStorage` dans ce champ. |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | facultatif. Catégories de journaux sélectionnées séparées par des virgules. Les valeurs disponibles sont les suivantes : `DriverLog`, `ExecutorLog`, `EventLog`, `Metrics`. Si aucune valeur n’est définie, la valeur par défaut sera **all** (toutes les catégories). |
| `spark.synapse.diagnostic.emitter.<destination>.auth`                       | Obligatoire. `AccessKey` pour utiliser l’autorisation [Clé d’accès](../../storage/common/storage-account-keys-manage.md) du compte de stockage. `SAS` pour l’autorisation [signatures d’accès partagé](../../storage/common/storage-sas-overview.md). |
| `spark.synapse.diagnostic.emitter.<destination>.uri`                        | Obligatoire. URI du dossier du conteneur d’objets blob de destination. Doit correspondre au modèle `https://<my-blob-storage>.blob.core.windows.net/<container-name>/<folder-name>`. |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | facultatif. Contenu du secret (clé d’accès ou SAS). |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | Requis si `.secret` n'est pas spécifié. Nom du [coffre de clés Azure](../../key-vault/general/overview.md) dans lequel est stocké le secret (clé d’accès ou SAS). |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | Obligatoire si `.secret.keyVault` est spécifié. Nom du coffre de clés Azure dans lequel le secret (clé d’accès ou SAS) est stocké. |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | Optionnel. Noms des événements Spark séparés par des virgules. Vous pouvez spécifier les événements à collecter. Par exemple : `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | facultatif. Noms des enregistreurs d’événements log4j séparés par des virgules. Vous pouvez spécifier les journaux à collecter. Par exemple : `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | facultatif. Suffixes des noms de métriques Spark séparés par des virgules. Vous pouvez spécifier les métriques à collecter. Par exemple : `jvm.heap.used` |

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


1. Accédez à **Synapse Studio > Gérer > Points de terminaison privés managés**, cliquez sur le bouton **Nouveau**, sélectionnez **Stockage Blob Azure** ou **Azure Data Lake Storage Gen2** puis **Continuer**.
   > [!div class="mx-imgBorder"]
   > ![Créer un point de terminaison privé managé 1](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-1.png)
2. Choisissez votre compte de stockage Azure dans **Nom du compte de stockage**, puis cliquez sur le bouton **Créer**.
   > [!div class="mx-imgBorder"]
   > ![Créer un point de terminaison privé managé 2](./media/azure-synapse-diagnostic-emitters-azure-storage/create-private-endpoint-2.png)
3. Attendez quelques minutes que le provisionnement du point de terminaison privé soit terminé.
4. Accédez à votre compte de stockage dans le portail Azure et, dans la page **Réseau** > **Connexions de point de terminaison privé**, sélectionnez la connexion provisionnée, puis **Approuver**.


