---
title: Monitorer des applications Apache Spark avec Azure Log Analytics
description: Découvrez comment activer le connecteur Synapse Studio pour collecter et envoyer les métriques et les journaux de l’application Apache Spark à votre espace de travail Log Analytics.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: edb86a4c264b291516b3cc52d3d5dbfa159ace32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312169"
---
# <a name="monitor-apache-spark-applications-with-azure-log-analytics"></a>Monitorer des applications Apache Spark avec Azure Log Analytics

Dans ce tutoriel, vous allez apprendre à activer le connecteur Synapse Studio intégré à Log Analytics. Vous pourrez ensuite collecter et envoyer des métriques et des journaux de l’application Apache Spark à votre [espace de travail log Analytics](../../azure-monitor/logs/quick-create-workspace.md). Enfin, vous aurez la possibilité d’utiliser un classeur Azure Monitor pour visualiser ces métriques et journaux.

## <a name="configure-workspace-information"></a>Configuration des informations de l’espace de travail

Pour configurer les informations nécessaires dans Synapse Studio, procédez comme suit.

### <a name="step-1-create-a-log-analytics-workspace"></a>Étape 1 : Créer un espace de travail Log Analytics

Pour créer cet espace de travail, consultez l’une des ressources suivantes :
- [Création d’un espace de travail sur le Portail Azure](../../azure-monitor/logs/quick-create-workspace.md)
- [Création d’un espace de travail avec Azure CLI](../../azure-monitor/logs/resource-manager-workspace.md)
- [Création et configuration d’un espace de travail dans Azure Monitor avec PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md)

### <a name="step-2-prepare-an-apache-spark-configuration-file"></a>Étape 2 : Préparer un fichier de configuration Apache Spark

Utilisez l’une des options suivantes pour préparer le fichier.

#### <a name="option-1-configure-with-log-analytics-workspace-id-and-key"></a>Option 1 : Configuration avec l’ID et la clé de l’espace de travail Log Analytics 

Copiez la configuration Apache Spark suivante, enregistrez-la sous *spark_loganalytics_conf.txt*, puis spécifiez les paramètres suivants :

   - `<LOG_ANALYTICS_WORKSPACE_ID>` : ID de l’espace de travail Log Analytics
   - `<LOG_ANALYTICS_WORKSPACE_KEY>` : clé Log Analytics. Pour la trouvez, accédez au Portail Azure, puis à **Espace de travail Azure Log Analytics** > **Gestion des agents** > **Clé primaire**.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-azure-key-vault"></a>Option 2 : Configuration avec Azure Key Vault

> [!NOTE]
> Vous devez octroyer une autorisation de lecture des secrets aux utilisateurs qui soumettent des applications Apache Spark. Pour plus d’informations, consultez [Attribution de l’accès aux clés, certificats et secrets Key Vault avec un contrôle d’accès en fonction du rôle Azure](../../key-vault/general/rbac-guide.md). Quand vous activez cette fonctionnalité dans un pipeline Synapse, vous devez utiliser l’**option 3**. Celle-ci est nécessaire pour obtenir le secret auprès d’Azure Key Vault avec l’identité managée de l’espace de travail.

Pour configurer Azure Key Vault de façon à stocker la clé de l’espace de travail, procédez comme suit :

1. Créez votre coffre de clés et accédez-y sur le Portail Azure.
2. Sur les pages de paramètres du coffre de clés, sélectionnez **Secrets**.
3. Sélectionnez **Générer/Importer**.
4. Sur l’écran **Créer un secret**, choisissez les valeurs suivantes :
   - **Nom** : donnez un nom au secret. Pour sélectionner la valeur par défaut, entrez `SparkLogAnalyticsSecret`.
   - **Valeur** : entrez `<LOG_ANALYTICS_WORKSPACE_KEY>` comme secret.
   - Conservez les valeurs par défaut des autres options. Sélectionnez ensuite **Créer**.
5. Copiez la configuration Apache Spark suivante, enregistrez-la sous *spark_loganalytics_conf.txt*, puis spécifiez les paramètres suivants :

   - `<LOG_ANALYTICS_WORKSPACE_ID>` : ID de l’espace de travail Log Analytics
   - `<AZURE_KEY_VAULT_NAME>` : nom du coffre de clés configuré
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (facultatif) : nom du secret dans le coffre de clés pour la clé de l’espace de travail. Par défaut, il s’agit de `SparkLogAnalyticsSecret`.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
> Vous pouvez également stocker l’ID de l’espace de travail dans Key Vault. Pour le stocker avec le nom du secret `SparkLogAnalyticsWorkspaceId`, reportez-vous à la procédure précédente. Vous pouvez également utiliser la configuration `spark.synapse.logAnalytics.keyVault.key.workspaceId` pour spécifier le nom du secret de l’ID de l’espace de travail dans Key Vault.

#### <a name="option-3-configure-with-a-linked-service"></a>Option 3. Configuration avec un service lié

> [!NOTE]
> Dans cette option, vous devez accorder l’autorisation de lecture du secret à l’identité managée de l’espace de travail. Pour plus d’informations, consultez [Attribution de l’accès aux clés, certificats et secrets Key Vault avec un contrôle d’accès en fonction du rôle Azure](../../key-vault/general/rbac-guide.md).

Pour configurer un service lié Azure Key Vault dans Synapse Studio de façon à stocker la clé de l’espace de travail, procédez comme suit :

1. Suivez toutes les étapes de la section précédente, « Option 2 ».
2. Créez un service lié Key Vault dans Synapse Studio :

    a. Accédez à **Synapse Studio** > **Gérer** > **Services liés**, puis sélectionnez **Créer**.

    b. Dans la zone de recherche, recherchez **Azure Key Vault**.

    c. Donnez un nom au service lié.

    d. Choisissez votre coffre de clés, puis sélectionnez **Créer**.

3. Ajoutez un élément `spark.synapse.logAnalytics.keyVault.linkedServiceName` à la configuration Apache Spark.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-apache-spark-configuration"></a>Configuration Apache Spark disponible

| Nom de la configuration | Valeur par défaut | Description |
| ------------------ | ------------- | ----------- |
| spark.synapse.logAnalytics.enabled | false | True pour activer les applications Spark sur le récepteur Log Analytics. Sinon, False. |
| spark.synapse.logAnalytics.workspaceId | - | ID de l’espace de travail Log Analytics de destination. |
| spark.synapse.logAnalytics.secret | - | Secret de l’espace de travail Log Analytics de destination. |
| spark.synapse.logAnalytics.keyVault.linkedServiceName   | - | Nom du service lié Key Vault pour l’ID et la clé de l’espace de travail Log Analytics. |
| spark.synapse.logAnalytics.keyVault.name | - | Nom Key Vault pour l’ID et la clé Log Analytics. |
| spark.synapse.logAnalytics.keyVault.key.workspaceId | SparkLogAnalyticsWorkspaceId | Nom du secret Key Vault pour l’ID de l’espace de travail Log Analytics. |
| spark.synapse.logAnalytics.keyVault.key.secret | SparkLogAnalyticsSecret | Nom du secret Key Vault pour l’espace de travail Log Analytics |
| spark.synapse.logAnalytics.uriSuffix | ods.opinsights.azure.com | [Suffixe d’URI][uri_suffix] de l’espace de travail Log Analytics de destination. Si votre espace de travail ne se trouve pas dans Azure global, vous devez mettre à jour le suffixe d’URI en fonction du cloud correspondant. |
| spark.synapse.logAnalytics.filter.eventName.match | - | facultatif. Noms des événements Spark séparés par des virgules. Vous pouvez spécifier les événements à collecter. Par exemple : `SparkListenerJobStart,SparkListenerJobEnd` |
| spark.synapse.logAnalytics.filter.loggerName.match | - | Optionnel. Noms des enregistreurs d’événements log4j séparés par des virgules. Vous pouvez spécifier les journaux à collecter. Par exemple : `org.apache.spark.SparkContext,org.example.Logger` |
| spark.synapse.logAnalytics.filter.metricName.match | - | Optionnel. Suffixes des noms de métriques Spark séparés par des virgules. Vous pouvez spécifier les métriques à collecter. Par exemple : `jvm.heap.used`|

> [!NOTE]  
> - Pour Azure Chine, le type de paramètre `spark.synapse.logAnalytics.uriSuffix` doit être `ods.opinsights.azure.cn`. 
> - Pour Azure Government, le paramètre `spark.synapse.logAnalytics.uriSuffix` doit être `ods.opinsights.azure.us`. 

[uri_suffix]: ../../azure-monitor/logs/data-collector-api.md#request-uri


### <a name="step-3-upload-your-apache-spark-configuration-to-an-apache-spark-pool"></a>Étape 3 : Charger votre configuration Apache Spark dans un pool Apache Spark
Vous pouvez charger le fichier de configuration sur votre pool Apache Spark Azure Synapse Analytics. Dans Synapse Studio, suivez la procédure ci-dessous :

   1. Sélectionner **Gérer** > **Pools Apache Spark**.
   2. À côté de votre pool Apache Spark, sélectionnez le bouton **…** .
   3. Sélectionnez **Configuration Apache Spark**. 
   4. Sélectionnez **Charger**, puis choisissez le fichier *spark_loganalytics_conf.txt*.
   5. Sélectionnez **Charger**, puis **Appliquer**.

      > [!div class="mx-imgBorder"]
      > ![Capture d’écran montrant la configuration du pool Spark.](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Toutes les applications Apache Spark soumises au pool Apache Spark utilisent le paramètre de configuration pour envoyer les métriques et les journaux d’application Apache Spark à votre espace de travail spécifié.

## <a name="submit-an-apache-spark-application-and-view-the-logs-and-metrics"></a>Envoyer une application Apache Spark, et voir les journaux et les métriques

Voici comment procéder :

1. Envoyez une application Apache Spark au pool Apache Spark configuré à l’étape précédente. Pour cela, vous avez plusieurs possibilités :
    - Exécutez un notebook dans Synapse Studio. 
    - Dans Synapse Studio, envoyez un programme de traitement par lots Apache Spark avec une définition de travail Apache Spark.
    - Exécutez un pipeline contenant une activité Apache Spark.

1. Accédez à l’espace de travail Log Analytics spécifié, puis visualisez les métriques et les journaux de l’application Apache Spark quand celle-ci commence à s’exécuter.

## <a name="write-custom-application-logs"></a>Écrire des journaux d’application personnalisés

Vous pouvez utiliser la bibliothèque Apache Log4j pour écrire des journaux personnalisés.

Exemple pour Scala :

```scala
%%spark
val logger = org.apache.log4j.LogManager.getLogger("com.contoso.LoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

Exemple pour PySpark :

```python
%%pyspark
logger = sc._jvm.org.apache.log4j.LogManager.getLogger("com.contoso.PythonLoggerExample")
logger.info("info message")
logger.warn("warn message")
logger.error("error message")
```

## <a name="use-the-sample-workbook-to-visualize-the-metrics-and-logs"></a>Visualisation des métriques et journaux avec l’exemple de classeur

1. [Téléchargez le classeur](https://aka.ms/SynapseSparkLogAnalyticsWorkbook).
2. Ouvrez et copiez le contenu du fichier de classeur.
3. Sur le [Portail Azure](https://portal.azure.com/), sélectionnez **Espace de travail Log Analytics** > **Classeurs**. 
4. Ouvrez le classeur **Vide**. Utilisez le mode **Éditeur avancé** en sélectionnant l’icône **</>** .
5. Collez le contenu copié par-dessus le code JSON existant.
6. Sélectionnez **Appliquer**, puis **Modification terminée**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant un nouveau classeur.](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant comment importer un classeur.](./media/apache-spark-azure-log-analytics/import-workbook.png)

Soumettez ensuite votre application Apache Spark au pool Apache Spark configuré. Une fois l’application passée dans un état en cours d’exécution, choisissez-la dans la liste déroulante des classeurs.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant un classeur.](./media/apache-spark-azure-log-analytics/workbook.png)

Il est possible de personnaliser le classeur. Par exemple, vous pouvez utiliser des requêtes Kusto et configurer des alertes.

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant la personnalisation d’un classeur avec une requête et des alertes.](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="query-data-with-kusto"></a>Interroger des données avec Kusto

Voici un exemple d’interrogation d’événements Apache Spark :

```kusto
SparkListenerEvent_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| order by TimeGenerated desc
| limit 100 
```

Voici un exemple d’interrogation des journaux du pilote et des exécuteurs de l’application Apache Spark :

```kusto
SparkLoggingEvent_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| order by TimeGenerated desc
| limit 100
```

Enfin, voici un exemple d’interrogation de métriques Apache Spark :

```kusto
SparkMetrics_CL
| where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
| where name_s endswith "jvm.total.used"
| summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
| order by TimeGenerated asc
```



## <a name="create-and-manage-alerts"></a>Création et gestion des alertes

Les utilisateurs peuvent lancer des requêtes pour évaluer les métriques et les journaux à une fréquence définie et déclencher une alerte en fonction des résultats. Pour plus d’informations, consultez [Création, affichage et gestion des alertes de journaux avec Azure Monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>Espace de travail Synapse où est activée la protection contre l’exfiltration de données

Après cela, l’espace de travail Synapse est créé avec la [protection contre l’exfiltration de données](../security/workspace-data-exfiltration-protection.md) activée.

Quand vous voulez activer cette fonctionnalité, vous devez créer des demandes de connexion de point de terminaison privé managé à des [étendues de liaison privée Azure Monitor (AMPLS)](../../azure-monitor/logs/private-link-security.md) dans les locataires Azure AD approuvés de l’espace de travail.

Vous pouvez suivre les étapes ci-dessous pour créer une connexion de point de terminaison privé managé à des étendues de liaison privée Azure Monitor :

1. S’il n’existe pas d’AMPLS, vous pouvez suivre [Configuration de connexions de liaison privée Azure Monitor](../../azure-monitor/logs/private-link-security.md) pour en créer une.
2. Accédez à votre AMPLS dans le portail Azure, dans la page **Ressources Azure Monitor**, cliquez sur **Ajouter** pour ajouter une connexion à votre espace de travail Azure Log Analytics.
3. Accédez à **Synapse Studio > Gérer > Points de terminaison privés managés**, cliquez sur le bouton **Nouveau**, sélectionnez **Étendues de liaison privée Azure Monitor**, puis **Continuer**.
   > [!div class="mx-imgBorder"]
   > ![Créer un point de terminaison privé managé AMPLS 1](./media/apache-spark-azure-log-analytics/create-ampls-private-endpoint-1.png)
4. Choisissez l’étendue de liaison privée Azure Monitor que vous avez créée et cliquez sur le bouton **Créer**.
   > [!div class="mx-imgBorder"]
   > ![Créer un point de terminaison privé managé AMPLS 2](./media/apache-spark-azure-log-analytics/create-ampls-private-endpoint-2.png)
5. Attendez quelques minutes que le provisionnement du point de terminaison privé soit terminé.
6. Accédez à nouveau à votre AMPLS dans le portail Azure et, dans la page **Connexions de point de terminaison privé**, sélectionnez la connexion provisionnée, puis **Approuver**.

> [!NOTE] 
>  - L’objet AMPLS a un certain nombre de limites à prendre en compte quand vous planifiez la configuration de votre liaison privée. Consultez [Limites d’AMPLS](../../azure-monitor/logs/private-link-security.md) pour une présentation plus approfondie de ces limites. 
>  - Vérifiez si vous disposez de l’[autorisation appropriée](../security/synapse-workspace-access-control-overview.md) pour créer un point de terminaison privé managé.

## <a name="next-steps"></a>Étapes suivantes

 - [Utilisation du pool Apache Spark serverless dans Synapse Studio](../quickstart-create-apache-spark-pool-studio.md)
 - [Exécution d’une application Spark dans un notebook](./apache-spark-development-using-notebooks.md)
 - [Création d’une définition de tâche Apache Spark dans Azure Studio](./apache-spark-job-definitions.md)
 - [Collecter les journaux et les métriques des applications Apache Spark avec un compte de stockage Azure](./azure-synapse-diagnostic-emitters-azure-storage.md).
 - [Collecter les journaux et les métriques des applications Apache Spark avec Azure Event Hubs](./azure-synapse-diagnostic-emitters-azure-eventhub.md).
