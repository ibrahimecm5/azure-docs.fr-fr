---
title: Alertes métriques de Container Insights
description: Cet article passe en revue les alertes métriques recommandées disponibles dans Container Insights en préversion publique.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 49ae1fee1961bf722489309d2d605ca7697ab218
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455128"
---
# <a name="recommended-metric-alerts-preview-from-container-insights"></a>Alertes métriques recommandées (préversion) de Container Insights

Pour alerter concernant des problèmes de ressources système quand les conteneurs rencontrent des pics de demande et approchent de leur capacité maximale, avec Container Insights, vous devez créer une alerte de journal basée sur les données de performances stockées dans des journaux Azure Monitor. Container Insights comprend désormais des règles d’alerte métrique préconfigurées pour vos clusters AKS et Kubernetes avec Azure Arc, en préversion publique.

Cet article passe en revue l’expérience et fournit des conseils sur la configuration et la gestion de ces règles d’alerte.

Avant de commencer, si vous n’êtes pas familiarisé avec les alertes d’Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure](../alerts/alerts-overview.md). Pour en savoir plus sur les alertes métriques, consultez [Alertes métriques dans Azure Monitor](../alerts/alerts-metric-overview.md).

> [!NOTE]
> À compter du 8 octobre 2021, trois alertes ont été mises à jour pour calculer correctement la condition d’alerte : **% du processeur du conteneur**, **% de la mémoire de la plage de travail du conteneur** et **% d’utilisation du volume persistant**. Ces nouvelles alertes ont les mêmes noms que les alertes correspondantes précédemment disponibles, mais elles utilisent de nouvelles métriques mises à jour. Nous vous recommandons de désactiver les alertes qui utilisent les métriques « Old », décrites dans cet article, et d’activer les métriques « New ». Les métriques « Old » ne sont plus disponibles dans les alertes recommandées une fois qu’elles sont désactivées, mais vous pouvez les réactiver manuellement.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les éléments suivants :

* Les métriques personnalisées sont disponibles dans un sous-ensemble de régions Azure uniquement. La liste des régions prises en charge est disponible dans [Régions prises en charge](../essentials/metrics-custom-overview.md#supported-regions).

* Pour prendre en charge les alertes sur les métriques et l'introduction de métriques supplémentaires, la version minimale requise de l'agent est **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** pour AKS et **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** pour le cluster Kubernetes avec Azure Arc.

    Pour vérifier que votre cluster exécute la version la plus récente de l’agent, vous pouvez :

    * Exécuter la commande : `kubectl describe <omsagent-pod-name> --namespace=kube-system`. Dans l’état renvoyé, notez la valeur sous **Image** pour omsagent, dans la section *Containers* de la sortie. 
    * Sous l’onglet **Nœuds**, sélectionnez le nœud de cluster, puis, dans le volet **Propriétés** à droite, notez la valeur sous **Balise d’image de l’agent**.

    La valeur indiquée pour AKS doit être la version **ciprod05262020** ou une version ultérieure. La valeur indiquée pour le cluster Kubernetes avec Azure Arc doit être la version **ciprod09252020** ou une version ultérieure. Si votre cluster dispose d’une version antérieure, consultez [Comment mettre à niveau l’agent Container Insights](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) afin de connaître les étapes à suivre pour obtenir la dernière version.

    Pour plus d’informations sur la version de l’agent, consultez l’[historique de publication des mises en production de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Pour vérifier que les métriques sont collectées, vous pouvez utiliser Azure Monitor Metrics Explorer et vérifier qu’**Insights** figure dans l’**espace de noms métrique** . Si c’est le cas, vous pouvez commencer à configurer les alertes. Si vous ne voyez aucune métrique collectée, le principal du service de cluster ou MSI ne dispose pas des autorisations nécessaires. Pour vérifier que le SPN ou MSI est membre du rôle **Éditeur de métriques d’analyse**, suivez les étapes décrites dans la section [Effectuer une mise à niveau par cluster à l’aide d’Azure CLI](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) pour confirmer et définir l’attribution de rôle.

## <a name="alert-rules-overview"></a>Vue d’ensemble des règles d’alerte

Pour alerter sur ce qui est important, Container Insights inclut les alertes métriques suivantes pour vos clusters AKS et Kubernetes avec Azure Arc :

|Name| Description |Seuil par défaut |
|----|-------------|------------------|
|**(New)Pourcentage moyen d’UC du conteneur** |Calcule l’utilisation moyenne de l’UC par conteneur.|Lorsque l’utilisation moyenne de l’UC par conteneur est supérieure à 95 %.| 
|**(New)Pourcentage moyen de mémoire de la plage de travail du conteneur** |Calcule la mémoire moyenne de la plage de travail utilisée par conteneur.|Lorsque l’utilisation moyenne de la mémoire de la plage de travail par conteneur est supérieure à 95 %. |
|% moyen du processeur |Calcule l’utilisation moyenne de l’UC par nœud. |Lorsque l’utilisation moyenne de l’UC par nœud est supérieure à 80 % |
| Violation de la limite de données quotidienne | En cas de violation de la limite de données| Lorsque l’ingestion totale des données dans votre espace de travail Log Analytics dépasse le [quota désigné](../logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume) |
|Pourcentage moyen d’utilisation de l’UC |Calcule l’utilisation moyenne du disque pour un nœud.|Lorsque l’utilisation du disque pour un nœud est supérieure à 80 %. |
|**(Nouveau)Pourcentage moyen d’utilisation du volume persistant** |Calcule l’utilisation moyenne du volume persistant par pod. |Lorsque l’utilisation moyenne du volume persistant par pod est supérieure à 80 %.|
|Pourcentage moyen de mémoire de la plage de travail |Calcule la mémoire moyenne de la plage de travail pour un nœud. |Lorsque la mémoire moyenne de la plage de travail pour un nœud est supérieure à 80 %. |
|Nombre de conteneurs en cours de redémarrage |Calcule le nombre de conteneurs en cours de redémarrage. | Lorsque le conteneur redémarre, le nombre est supérieur à 0. |
|Nombre de pods défaillants |Calcule si un pod est en état défaillant.|Quand un nombre de pods en état défaillant est supérieur à 0. |
|État NotReady du nœud |Calcule si un nœud est en état NotReady.|Quand un nombre de nœuds en état NotReady est supérieur à 0. |
|Conteneurs arrêtés OOM |Calcule le nombre de conteneurs arrêtés OOM. |Quand un nombre de conteneurs arrêtés OOM est supérieur à 0. |
|Pourcentage de pods prêts |Calcule la moyenne d’état prêt des pods. |Lorsque l’état prêt des pods est inférieur à 80 %.|
|Nombre de travaux terminés |Calcule le nombre de travaux terminés il y a plus de six heures. |Lorsque le nombre de travaux obsolètes depuis plus de six heures est supérieur à 0.|

Il existe des propriétés communes pour toutes ces règles d’alerte :

* Toutes les règles d’alerte sont basées sur une métrique.

* Toutes les règles d’alerte sont désactivées par défaut.

* Toutes les règles d’alerte sont évaluées une fois par minute et vérifient les 5 dernières minutes de données.

* Les règles d’alerte n’ont pas de groupe d’actions affecté par défaut. Vous pouvez ajoutez un [groupe d’actions](../alerts/action-groups.md) à l’alerte, soit en sélectionnant un groupe d’actions existant, soit en créant un groupe d’actions lors de la modification de la règle d’alerte.

* Vous pouvez modifier le seuil des règles d’alerte en les modifiant directement. Toutefois, reportez-vous aux instructions fournies dans chaque règle d’alerte avant de modifier son seuil.

Les métriques suivantes basées sur les alertes présentent des caractéristiques de comportement uniques par rapport aux autres métriques :

* La métrique *completedJobsCount* n’est envoyée que s’il y a des travaux terminés depuis plus de six heures.

* La métrique *containerRestartCount* n’est envoyée que s’il y a des conteneurs qui redémarrent.

* La métrique *oomKilledContainerCount* n’est envoyée que s’il y a des conteneurs arrêtés OOM.

* Les métriques *cpuExceededPercentage*, *memoryRssExceededPercentage* et *memoryWorkingSetExceededPercentage* sont envoyées lorsque les valeurs d’UC, de mémoire Rss et de plage de travail de mémoire dépassent le seuil configuré (le seuil par défaut est de 95 %). Ces seuils sont exclusifs du seuil de condition d’alerte spécifié pour la règle d’alerte correspondante. Cela signifie que, si vous souhaitez collecter ces métriques et les analyser à partir de [Metrics Explorer](../essentials/metrics-getting-started.md), nous vous recommandons de configurer le seuil sur une valeur inférieure au seuil d’alerte. La configuration relative aux paramètres de collection pour les seuils d’utilisation des ressources de conteneur peut être remplacée dans le fichier ConfigMaps sous la section `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]`. Pour plus d’informations sur la configuration de votre fichier de configuration ConfigMap, consultez la section [Configurer les métriques alertables ConfigMaps](#configure-alertable-metrics-in-configmaps).

* La métrique *pvUsageExceededPercentage* est envoyée lorsque le pourcentage d’utilisation du volume persistant dépasse le seuil configuré (le seuil par défaut est de 60 %). Ce seuil est exclusif du seuil de condition d’alerte spécifié pour la règle d’alerte correspondante. Cela signifie que, si vous souhaitez collecter ces métriques et les analyser à partir de [Metrics Explorer](../essentials/metrics-getting-started.md), nous vous recommandons de configurer le seuil sur une valeur inférieure au seuil d’alerte. La configuration relative aux paramètres de collection pour les seuils d’utilisation du volume persistant peut être remplacée dans le fichier ConfigMap sous la section `[alertable_metrics_configuration_settings.pv_utilization_thresholds]`. Pour plus d’informations sur la configuration de votre fichier de configuration ConfigMap, consultez la section [Configurer les métriques alertables ConfigMaps](#configure-alertable-metrics-in-configmaps). La collecte des métriques de volume persistant avec des revendications dans l’espace de noms *kube-system* est exclue par défaut. Pour activer la collecte dans cet espace de noms, utilisez la section `[metric_collection_settings.collect_kube_system_pv_metrics]` dans le fichier ConfigMap. Pour plus d’informations, consultez [Paramètres de collection de métriques](./container-insights-agent-config.md#metric-collection-settings).

## <a name="metrics-collected"></a>Métriques collectées

Dans le cadre de cette fonctionnalité, sauf spécification contraire, les métriques suivantes sont activées et collectées. Les métriques en **gras** avec l’étiquette « Old » sont celles remplacées par des métriques « New » collectées pour l’évaluation correcte des alertes.

|Espace de noms de la métrique |Métrique |Description |
|---------|----|------------|
|Insights.container/nodes |cpuUsageMillicores |Utilisation de l’UC en millicœurs par hôte.|
|Insights.container/nodes |cpuUsagePercentage, cpuUsageAllocatablePercentage (préversion) |Pourcentage d’utilisation de l’UC par nœud et allouable respectivement.|
|Insights.container/nodes |memoryRssBytes |Utilisation de la mémoire RSS en octets par hôte.|
|Insights.container/nodes |memoryRssPercentage, memoryRssAllocatablePercentage (préversion) |Mémoire de l’utilisation de RSS en pourcentage par hôte et allouable respectivement.|
|Insights.container/nodes |memoryWorkingSetBytes |Utilisation de la plage de travail de mémoire en octets par hôte.|
|Insights.container/nodes |memoryWorkingSetPercentage, memoryRssAllocatablePercentage (préversion) |Mémoire de l’utilisation de Working Set en pourcentage par hôte et allouable respectivement.|
|Insights.container/nodes |nodesCount |Nombre de nœuds par état.|
|Insights.container/nodes |diskUsedPercentage |Pourcentage de disque utilisé sur le nœud par appareil.|
|Insights.container/pods |podCount |Nombre de pods par contrôleur, espace de noms, nœud et phase.|
|Insights.container/pods |completedJobsCount |Seuil configurable par l’utilisateur du nombre de travaux terminés anciens (six heures par défaut) par contrôleur, espace de noms Kubernetes. |
|Insights.container/pods |restartingContainerCount |Nombre de redémarrages de conteneur par contrôleur, espace de noms Kubernetes.|
|Insights.container/pods |oomKilledContainerCount |Nombre de conteneurs OOMkilled par contrôleur, espace de noms Kubernetes.|
|Insights.container/pods |podReadyPercentage |Pourcentage de pods dans l’état prêt par contrôleur, espace de noms Kubernetes.|
|Insights.container/containers |**(Old)cpuExceededPercentage** |Pourcentage d’utilisation de l’UC pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.<br> Collecté  |
|Insights.container/containers |**(New)cpuThresholdViolated** |Métrique déclenchée lorsque le pourcentage d’utilisation de l’UC pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.<br> Collecté  |
|Insights.container/containers |**(Old)memoryRssExceededPercentage** |Pourcentage de mémoire RSS pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.|
|Insights.container/containers |**(New)memoryRssThresholdViolated** |Métrique déclenchée lorsque le pourcentage de mémoire RSS pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.|
|Insights.container/containers |**(Old)memoryWorkingSetExceededPercentage** |Pourcentage de plage de travail de mémoire pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.|
|Insights.container/containers |**(New)memoryWorkingSetThresholdViolated** |Métrique déclenchée lorsque le pourcentage de mémoire de Plage de travail pour les conteneurs dépassant le seuil configurable par l’utilisateur (95,0 par défaut) par nom de conteneur, nom de contrôleur, espace de noms Kubernetes, nom de pod.|
|Insights. Container/persistentvolumes |**(Old)pvUsageExceededPercentage** |Pourcentage d’utilisation du volume persistant pour les volumes persistants dépassant le seuil configurable par l’utilisateur (60 par défaut) par nom de revendication, espace de noms Kubernetes, nom de volume, nom de pod ou nom de nœud.|
|Insights. Container/persistentvolumes |**(New)pvUsageThresholdViolated** |Métrique déclenchée lorsque le pourcentage d’utilisation du volume persistant pour les volumes persistants dépassant le seuil configurable par l’utilisateur (60 par défaut) par nom de revendication, espace de noms Kubernetes, nom de volume, nom de pod ou nom de nœud.

## <a name="enable-alert-rules"></a>Activer des règles d’alerte

Procédez comme suit pour activer les alertes métriques dans Azure Monitor à partir du portail Azure. Pour activer l’utilisation d’un modèle Resource Manager, consultez [Activer avec un modèle Resource Manager](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>À partir du portail Azure

Cette section décrit l’activation d’alerte métrique (préversion) de Container Insights à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Il est possible d’accéder à la fonctionnalité d’alerte métrique (préversion) de Container Insights directement à partir d’un cluster AKS en sélectionnant **Insights** dans le volet gauche du portail Azure.

3. Dans la barre de commandes, sélectionnez **Alertes recommandées**.

    ![Option Alertes recommandées dans Container Insights](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Le volet de propriétés **Alertes recommandées** s’affiche automatiquement sur le côté droit de la page. Par défaut, toutes les règles d’alerte dans la liste sont désactivées. Après que vous avez sélectionné **Activer**, la règle d’alerte est créée et son nom mis à jour pour inclure un lien vers la ressource d’alerte.

    ![Volet de propriétés Alertes recommandées](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Après que vous avez sélectionné le bouton bascule **Activer/Désactiver** pour activer l’alerte, une règle d’alerte est créée et son nom mis à jour pour inclure un lien vers la ressource d’alerte réelle.

    ![Activer une règle d’alerte](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Les règles d’alertes ne sont pas associées à un [groupe d’actions](../alerts/action-groups.md) pour notifier aux utilisateurs qu’une alerte a été déclenchée. Sélectionnez **No action group assigned** (aucun groupe d’actions affecté), puis, dans la page **Groupes d’actions**, spécifiez un groupe d’actions existant, ou créez un groupe en sélectionnant **Ajouter** ou **Créer**.

    ![Sélectionner un groupe d’actions](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Activer avec un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager et un fichier de paramètres pour créer les alertes métriques incluses dans Azure Monitor.

Les étapes de base sont les suivantes :

1. Téléchargez un ou tous les modèles disponibles qui décrivent comment créer l’alerte à partir de [GitHub](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Créez et utilisez un [fichier de paramètres](../../azure-resource-manager/templates/parameter-files.md) en tant que JSON pour définir les valeurs requises pour créer la règle d’alerte.

3. Déployez le modèle à partir du portail Azure, de PowerShell ou d’Azure CLI.

#### <a name="deploy-through-azure-portal"></a>Déployer par le biais du portail Azure

1. Téléchargez et enregistrez dans un dossier local le modèle Azure Resource Manager et un fichier de paramétrage pour créer la règle d’alerte en utilisant les commandes suivantes :

2. Pour déployer un modèle personnalisé via le portail, sélectionnez **Créer une ressource**, dans le [portail Azure](https://portal.azure.com).

3. Recherchez **modèle**, puis sélectionnez **Déploiement de modèle**.

4. Sélectionnez **Create** (Créer).

5. Vous voyez plusieurs options pour créer un modèle, sélectionnez **Créer votre propre modèle dans l’éditeur**.

6. Sur la page **Modifier le modèle**, sélectionnez **Charger le fichier**, puis sélectionnez le fichier de modèle.

7. Sur la page **Modifier le modèle**, sélectionnez **Enregistrer**.

8. Sur la page **Déploiement personnalisé**, spécifiez ce qui suit, puis, lorsque vous avez terminé, sélectionnez **Achat** pour déployer le modèle et créer la règle d’alerte.

    * Resource group
    * Emplacement
    * Nom de l’alerte
    * ID de la ressource de cluster

#### <a name="deploy-with-azure-powershell-or-cli"></a>Déployer avec Azure PowerShell ou CLI

1. Téléchargez et enregistrez dans un dossier local le modèle Azure Resource Manager et un fichier de paramétrage pour créer la règle d’alerte en utilisant les commandes suivantes :

2. Vous pouvez créer l’alerte de métrique à l’aide du modèle et du fichier de paramètres en utilisant PowerShell ou l’interface de ligne de commande Azure.

    Utilisation de Microsoft Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Utilisation de l’interface de ligne de commande Azure

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Bien que vous puissiez créer l’alerte de métrique dans un autre groupe de ressources que la ressource cible, nous vous recommandons d’utiliser le même groupe de ressources que votre ressource cible.

## <a name="edit-alert-rules"></a>Éditer des règles d’alerte

Vous pouvez afficher et gérer les règles d’alerte de Container Insights, afin de modifier le seuil, ou configurer un [groupe d’actions](../alerts/action-groups.md) pour votre cluster AKS. Si vous pouvez effectuer ces actions à partir du portail Azure et d’Azure CLI, vous pouvez également le faire directement à partir de votre cluster AKS dans Container Insights.

1. Dans la barre de commandes, sélectionnez **Alertes recommandées**.

2. Pour modifier le seuil, dans le volet **Alertes recommandées**, sélectionnez l’alerte activée. Dans **Modifier la règle**, cliquez sur le **Critère d’alerte** que vous souhaitez modifier.

    * Pour modifier le seuil de la règle d’alerte, sélectionnez la **Condition**.
    * Pour spécifier un groupe d’actions ou en créer un, sélectionnez **Ajouter** ou **Créer** sous **Groupe d’actions**.

Pour afficher les alertes créées pour les règles activées, dans le volet **Alertes recommandées**, sélectionnez **Afficher dans les alertes**. Vous êtes redirigé vers le menu d’alerte pour le cluster AKS, où vous pouvez voir toutes les alertes actuellement créées pour votre cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Configurer des métriques alertables dans ConfigMaps

Procédez comme suit pour configurer votre fichier config ConfigMap afin de remplacer les seuils d’utilisation par défaut. Ces étapes s’appliquent uniquement aux métriques d’alerte suivantes :

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Modifiez le fichier YAML ConfigMap sous la section `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` ou `[alertable_metrics_configuration_settings.pv_utilization_thresholds]`.

   - Pour modifier le seuil *cpuExceededPercentage* en le définissant sur 90 % et commencer la collecte de cette métrique lorsque ce seuil est atteint et dépassé, configurez le fichier ConfigMap à l’aide de l’exemple suivant :

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Pour modifier le seuil *pvUsageExceededPercentage* en le définissant sur 80 % et commencer la collecte de cette métrique lorsque ce seuil est atteint et dépassé, configurez le fichier ConfigMap à l’aide de l’exemple suivant :

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Exécutez la commande kubectl suivante : `kubectl apply -f <configmap_yaml_file.yaml>`.

    Exemple : `kubectl apply -f container-azm-ms-agentconfig.yaml`.

Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent ; toutefois, ils ne redémarrent pas tous en même temps. Lorsque les redémarrages sont terminés, un message semblable à l’exemple suivant s’affiche avec le résultat : `configmap "container-azm-ms-agentconfig" created`.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [exemples de requêtes de journal](container-insights-log-query.md) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.

- Pour en savoir plus sur Azure Monitor et sur la façon de surveiller d’autres aspects de votre cluster Kubernetes, consultez [Afficher les performances du cluster Kubernetes](container-insights-analyze.md).
