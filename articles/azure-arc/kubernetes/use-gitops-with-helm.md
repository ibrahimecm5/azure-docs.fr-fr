---
title: Déployer des charts Helm à l’aide de GitOps sur un cluster Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Utiliser GitOps avec Helm pour une configuration de cluster avec Azure Arc
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: bc0dc3f0583c346ae909bbb877a6e8a9a9d66a72
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053290"
---
# <a name="deploy-helm-charts-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Déployer des charts Helm à l’aide de GitOps sur un cluster Kubernetes avec Azure Arc

Helm est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l'instar de gestionnaires de package Linux tels qu'APT et Yum, Helm sert à gérer les charts Kubernetes, qui sont des packages de ressources Kubernetes préconfigurées.

Cet article vous montre comment configurer et utiliser Helm à l’aide de Kubernetes avec Azure Arc.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, consultez notre [guide de démarrage rapide sur la connexion d’un cluster Kubernetes avec Azure Arc](quickstart-connect-cluster.md).
- Compréhension suffisante des avantages et de l’architecture de cette fonctionnalité. Pour plus d’informations, consultez l’article [Configurations et GitOps – Kubernetes avec Azure Arc](conceptual-configurations.md).
- Installez l'extension `k8s-configuration` d'Azure CLI, version >= 1.0.0 :
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Présentation de l’utilisation de GitOps et Helm à l’aide de Kubernetes avec Azure Arc

 L’opérateur Helm fournit une extension à Flux qui automatise les versions Chart Helm. Une version du chart Helm est décrite par le biais d’une ressource personnalisée Kubernetes nommée HelmRelease. Flux synchronise ces ressources de Git au cluster, tandis que l’opérateur Helm vérifie que les charts Helm sont publiés comme spécifié dans les ressources.

 L'[exemple de référentiel](https://github.com/Azure/arc-helm-demo) utilisé dans cet article est structuré comme suit :

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Dans le référentiel Git, nous avons deux répertoires, l’un contenant un chart Helm et l’autre le fichier config des versions. Dans le répertoire `releases`, le `app.yaml` contient le fichier config HelmRelease indiqué ci-dessous :

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Le fichier config de la version Helm contient les champs suivants :

| Champ | Description |
| ------------- | ------------- | 
| `metadata.name` | Champ obligatoire. Doit respecter les conventions d’affectation de noms de Kubernetes. |
| `metadata.namespace` | Champ facultatif. Détermine où la version est créée. |
| `spec.releaseName` | Champ facultatif. S’il n’est pas indiqué, le nom de la version sera `$namespace-$name`. |
| `spec.chart.path` | Répertoire contenant le chart (par rapport à la racine du dépôt). |
| `spec.values` | Personnalisations par l’utilisateur de valeurs de paramètres par défaut provenant du chart lui-même. |

Les options spécifiées dans `spec.values` HelmRelease remplacent les options spécifiées dans `values.yaml` de la source du chart.

Vous pouvez en savoir plus sur le HelmRelease dans la [documentation officielle de l’opérateur Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Créer une configuration

À l’aide de l’extension Azure CLI pour `k8s-configuration`, liez votre cluster connecté à un exemple de référentiel Git. Nous donnerons à cette configuration le nom `azure-arc-sample` et déploierons l’opérateur Flux dans l’espace de noms `arc-k8s-demo`.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Paramètres de configuration

Pour personnaliser la création de la configuration, [découvrez les paramètres supplémentaires](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Valider la configuration

À l’aide d’Azure CLI, vérifiez si la configuration a été correctement créée.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

La ressource de configuration est mise à jour avec l’état de conformité, les messages et les informations de débogage.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Valider l’application

Exécutez la commande suivante et accédez à `localhost:8080` sur votre navigateur pour vérifier que l’application fonctionne.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Étapes suivantes

Appliquer des configurations de cluster à grande échelle à l’aide d’[Azure Policy](./use-azure-policy.md).
