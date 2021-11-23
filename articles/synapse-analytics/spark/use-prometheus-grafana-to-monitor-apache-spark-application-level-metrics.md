---
title: 'Tutoriel : Superviser les métriques des applications Apache Spark avec Prometheus et Grafana'
description: 'Tutoriel : Découvrez comment déployer la solution de métrique de l’application Apache Spark sur un cluster Azure Kubernetes Service (AKS) et intégrer les tableaux de bord Grafana.'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 1c26ed72a804335a675a2456b2908f582848f5a7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399083"
---
# <a name="monitor-apache-spark-applications-metrics-with-prometheus-and-grafana"></a>Superviser les métriques des applications Apache Spark avec Prometheus et Grafana

## <a name="overview"></a>Vue d’ensemble

Dans ce tutoriel, vous découvrirez comment déployer la solution de métrique de l’application Apache Spark sur un cluster Azure Kubernetes Service (AKS) et intégrer les tableaux de bord Grafana.

Vous pouvez utiliser cette solution pour collecter et interroger les données de métriques Apache Spark en quasi-temps réel. Les tableaux de bord Grafana intégrés vous permettent de diagnostiquer et de surveiller votre application Apache Spark. Le code source et les configurations sont désormais en open source sur GitHub.

## <a name="prerequisites"></a>Prérequis

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [Client Helm 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)

Vous pouvez aussi utiliser [Azure Cloud Shell](https://shell.azure.com/), qui intègre déjà Azure CLI, le client Helm et kubectl prêts à l’emploi.

## <a name="log-in-to-azure"></a>Connexion à Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Créer une instance de service Azure Kubernetes (AKS)

Utilisez la commande Azure CLI pour créer un cluster Kubernetes dans votre abonnement.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Remarque : vous pouvez ignorer cette étape si vous possédez déjà un cluster AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Créer un principal du service et octroyer l’autorisation à l’espace de travail Synapse

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Le résultat doit avoir l’aspect suivant :

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Notez l’appId, le mot de passe et le tenantID.

[![capture d’écran octroi d’autorisation srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Connectez-vous à votre [espace de travail Azure Synapse Analytics](https://web.azuresynapse.net/) en tant qu’administrateur Synapse

2. Dans Synapse Studio, sélectionnez **Gérer > Contrôle d’accès** dans le volet de gauche

3. Cliquez sur le bouton Ajouter situé en haut à gauche pour **ajouter une attribution de rôle**

4. Pour Étendue, choisissez **Espace de travail**

5. Pour Rôle, choisissez **Opérateur de capacité de calcul Synapse**

6. Pour Sélectionner un utilisateur, entrez votre **<service_principal_name>** et cliquez sur votre principal de service

7. Cliquez sur **Appliquer** (attendez 3 minutes pour que l’autorisation prenne effet.)

> [!NOTE]
> Vérifiez que votre principal de service joue au moins un rôle de « Lecteur » dans votre espace de travail Synapse. Accédez à l’onglet Access Control (IAM) du portail Azure et vérifiez les paramètres d’autorisation.

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Installer le connecteur, le serveur Prometheus et le tableau de bord Grafana

1. Ajoutez le référentiel synapse-charts au client Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Installez les composants à l’aide du client Helm :

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name : nom de l’espace de travail Synapse.
 - subscription_id : ID de l’abonnement de l’espace de travail Synapse.
 - workspace_resource_group_name : nom du groupe de ressources de l’espace de travail Synapse.
 - tenant_id : ID de locataire de l’espace de travail Synapse.
 - service_principal_app_id : principal de service « appId »
 - service_principal_password : mot de passe du principal de service que vous avez créé.

## <a name="log-in-to-grafana"></a>Se connecter à Grafana

Obtenez le mot de passe et l’adresse par défaut de Grafana. Vous pouvez modifier le mot de passe dans les paramètres Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Obtenez l’IP de service, copiez et collez l’adresse IP externe dans le navigateur, puis connectez-vous avec le nom d’utilisateur « admin » et le mot de passe.

## <a name="use-grafana-dashboards"></a>Utiliser les tableaux de bord Grafana

Recherchez le tableau de bord Synapse en haut à gauche de la page Grafana (Accueil -> Espace de travail Synapse/Application Synapse). Essayez d’exécuter un exemple de code dans Synapse Studio et patientez quelques secondes pour que les métriques soient extraites.

Vous pouvez aussi utiliser les tableaux de bord « Espace de travail Synapse/Espace de travail » et « Espace de travail Synapse/Pools Apache Spark » pour obtenir une vue d’ensemble de votre espace de travail et de vos pools Apache Spark.

## <a name="uninstall"></a>Désinstaller l’interface

Procédez comme suit pour supprimer les composants à l’aide de la commande Helm.

```bash
helm delete <release_name> -n <namespace>
```

Supprimez le cluster AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Présentation des composants

Azure Synapse Analytics fournit un [graphique Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) en fonction de l’opérateur Prometheus et du connecteur Synapse Prometheus. Le graphique Helm comprend le serveur Prometheus, le serveur Grafana et les tableaux de bord Grafana pour les métriques de l’application Apache Spark. Vous pouvez utiliser [Prometheus](https://prometheus.io/), un système de surveillance open source populaire, pour collecter ces métriques en quasi-temps réel et utiliser [Grafana](https://github.com/grafana/grafana) pour la visualisation.

### <a name="synapse-prometheus-connector"></a>Connecteur Synapse Prometheus

Le connecteur Synapse Prometheus vous permet de connecter le pool Azure Synapse Apache Spark et le serveur Prometheus. Ce dernier implémente :

1.  L’authentification : authentification AAD permettant d’actualiser automatiquement le jeton AAD du principal de service pour la découverte d’applications, l’ingestion des métriques et d’autres fonctions.
2.  La découverte des applications Apache Spark : lorsque vous soumettez des applications dans l’espace de travail cible, le connecteur Synapse Prometheus découvre automatiquement ces applications.
3.  Les métadonnées de l’application Apache Spark : collecte les informations de base de l’application et exporte les données vers Prometheus.

Le connecteur Synapse Prometheus est publié en tant qu’image Docker hébergée sur [Microsoft Container Registry](https://github.com/microsoft/containerregistry). Il s’agit d’un connecteur open source qui se trouve dans les [métriques de l’application Azure Synapse Apache Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Serveur Prometheus

Prometheus est un kit de ressources de surveillance et d’alerte open source. Prometheus a été désigné projet Graduated par la Cloud Native Computing Foundation (CNCF) et est devenu la norme de facto pour la surveillance cloud native. Prometheus peut vous aider à collecter, interroger et stocker un grand nombre de données de séries chronologiques, et il peut être facilement intégré à Grafana. Dans cette solution, nous déployons le composant Prometheus selon le graphique Helm.

### <a name="grafana-and-dashboards"></a>Grafana et tableaux de bord

Grafana est un logiciel de visualisation et d’analyse open source. Ce logiciel vous permet d’interroger, de visualiser, d’alerter et d’explorer vos métriques. Azure Synapse Analytics fournit un ensemble de tableaux de bord Grafana par défaut pour visualiser les métriques de l’application Apache Spark.

Grâce au tableau de bord « Espace de travail Synapse/Espace de travail », vous pouvez voir l’espace de travail de tous les pools Apache Spark, le nombre d’applications, les cœurs de processeurs, etc.

[![capture d’écran de l’espace de travail du tableau de bord](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Le tableau de bord « Espace de travail Synapse/Pools Apache Spark » contient les métriques des applications Apache Spark s’exécutant dans le pool Apache Spark sélectionné au cours de la période.

[![capture d’écran du tableau de bord sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Le tableau de bord « Espace de travail Synapse/Application Apache Spark » contient l’application Apache Spark sélectionnée.

[![capture d’écran de l’application de tableau de bord](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Les modèles de tableau de bord ci-dessus sont désormais en open source dans les [métriques de l’application Azure Synapse Apache Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).
