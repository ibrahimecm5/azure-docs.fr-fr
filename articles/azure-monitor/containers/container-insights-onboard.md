---
title: Activer Container Insights | Microsoft Docs
description: Cet article explique comment activer et configurer Container Insights afin que vous puissiez comprendre les performances de votre conteneur et les problèmes liés aux performances identifiés.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2d47ea7f2f2f0dadfd979a42b0b0e9125d4bebde
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455109"
---
# <a name="enable-container-insights"></a>Activer Container Insights

Cet article offre un aperçu des options disponibles pour configurer Container Insights en vue de surveiller les performances des charges de travail qui sont déployées dans un environnement Kubernetes et hébergées sur :

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Cluster Kubernetes avec Azure Arc](../../azure-arc/kubernetes/overview.md)
   - [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou localement
   - [Moteur AKS](https://github.com/Azure/aks-engine)
   - [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4.x  
   - [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) version 4.x  


Vous pouvez activer Container Insights pour un nouveau déploiement ou pour un ou plusieurs déploiements existants de Kubernetes à l’aide de l’une des méthodes prises en charge suivantes :

- Le portail Azure
- Azure PowerShell
- L’interface Azure CLI
- [Terraform et AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

Pour tout cluster kubernetes non AKS, vous devez d’abord connecter votre cluster à [Azure Arc](../../azure-arc/kubernetes/overview.md) avant d’activer la supervision.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous respectez les exigences suivantes :

> [!IMPORTANT]
> L’agent Linux conteneurisé Log Analytics (pod replicaset) effectue des appels d’API vers tous les nœuds Windows sur le port sécurisé Kubelet (10250) au sein du cluster afin de collecter des métriques liées aux performances du nœud et du conteneur. Le port sécurisé Kubelet (:10250) doit être ouvert dans le réseau virtuel du cluster à la fois pour le regroupement entrant et sortant pour que la collecte de métriques liées aux performances des nœuds et des conteneurs Windows soit opérationnelle.
>
> Si vous disposez d’un cluster Kubernetes avec des nœuds Windows, vérifiez et configurez le groupe de sécurité réseau et les stratégies réseau pour vous assurer que le port sécurisé Kubelet (:10250) est ouvert pour le trafic entrant et sortant dans le réseau virtuel du cluster.


- Vous disposez d’un espace de travail Log Analytics.

   Container Insights prend en charge un espace de travail Log Analytics dans les régions répertoriées dans [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   Vous pouvez le créer quand vous activez la supervision de votre nouveau cluster AKS, ou vous pouvez laisser l’expérience d’intégration créer un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement de cluster AKS. 
   
   Si vous choisissez de créer l’espace de travail vous-même, vous pouvez le créer par le biais de : 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-workspace-configuration.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Portail Azure](../logs/quick-create-workspace.md) 
   
   Pour obtenir la liste des paires de mappage prises en charge à utiliser pour l’espace de travail par défaut, consultez [Mappage des régions pour Container Insights](container-insights-region-mapping.md).

- Vous êtes membre du groupe *Contributeur Log Analytics* pour activer la supervision des conteneurs. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../logs/manage-access.md).

- Vous êtes membre du [groupe *Propriétaire*](../../role-based-access-control/built-in-roles.md#owner) sur la ressource de cluster AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Pour afficher les données de surveillance, vous devez avoir le rôle [*Lecteur Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) dans l’espace de travail Log Analytics, configuré avec Container Insights.

- Les métriques Prometheus ne sont pas recueillies par défaut. Avant de [configurer l’agent](container-insights-prometheus-integration.md) pour recueillir les métriques, il est important de consulter la [documentation Prometheus](https://prometheus.io/) pour comprendre les données pouvant être récupérées et les méthodes prises en charge.
- Un cluster AKS peut être associé à un espace de travail Log Analytics dans un autre abonnement Azure dans le même locataire Azure AD. Cette opération ne peut pas être effectuée actuellement avec le Portail Azure, mais elle peut être effectuée avec Azure CLI ou le modèle Resource Manager.

## <a name="supported-configurations"></a>Configurations prises en charge

Container Insights prend officiellement en charge les configurations suivantes :

- Environnements : Azure Red Hat OpenShift, Kubernetes local et le moteur AKS sur Azure et Azure Stack. Pour plus d’informations, consultez [Moteur AKS sur Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Les versions de Kubernetes et de la stratégie de prise en charge sont les mêmes que celles [prises en charge dans Azure Kubernetes Service (AKS)](../../aks/supported-kubernetes-versions.md).
- Nous vous recommandons de connecter votre cluster à [Azure Arc](../../azure-arc/kubernetes/overview.md) et d’activer la supervision par l’intermédiaire de Container Insights via Azure Arc.

> [!IMPORTANT]
> Notez que le module complémentaire d’analyse n’est actuellement pas pris en charge pour les clusters AKS configurés avec le [Proxy HTTP (préversion)](../../aks/http-proxy.md)

## <a name="network-firewall-requirements"></a>Configuration requise du pare-feu réseau

Le tableau suivant répertorie les informations de configuration du proxy et du pare-feu requises pour permettre à l’agent conteneurisé de communiquer avec Container Insights. Tout le trafic réseau provenant de l’agent est sortant vers Azure Monitor.

|Ressource de l’agent|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

Le tableau suivant répertorie les informations de configuration du proxy et du pare-feu pour Azure Chine 21Vianet :

|Ressource de l’agent|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Ingestion de données |
| `*.oms.opinsights.azure.cn` | 443 | Intégration d’OMS |
| `dc.services.visualstudio.com` | 443 | Pour la télémétrie de l’agent qui utilise Azure Public Cloud Application Insights |

Le tableau suivant répertorie les informations de configuration du proxy et du pare-feu pour Azure - Gouvernement des États-Unis.

|Ressource de l’agent|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Ingestion de données |
| `*.oms.opinsights.azure.us` | 443 | Intégration d’OMS |
| `dc.services.visualstudio.com` | 443 | Pour la télémétrie de l’agent qui utilise Azure Public Cloud Application Insights |

## <a name="components"></a>Components

Votre capacité à surveiller les performances dépend d’un agent Log Analytics conteneurisé pour Linux, spécialement développé pour Container Insights. Cet agent spécialisé collecte les données de performances et d’événements à partir de tous les nœuds du cluster, et l’agent est automatiquement déployé puis inscrit auprès de l’espace de travail Log Analytics spécifié pendant le déploiement. 

La version d’agent est microsoft/oms:ciprod04202018 ou ultérieure. Elle est représentée par une date au format suivant : *mmjjaaaa*.

>[!NOTE]
>Avec la disponibilité générale de la prise en charge de Windows Server pour AKS, un cluster AKS avec des nœuds Windows Server dispose d’un agent de préversion installé en tant que pod daemonset sur chaque nœud de serveur Windows afin de recueillir les journaux et de les transférer à Log Analytics. Pour les métriques de performances, un nœud Linux déployé automatiquement dans le cluster dans le cadre du déploiement standard recueille et transfère les données à Azure Monitor pour le compte de tous les nœuds Windows du cluster.

Quand une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). Pour suivre les versions publiées, consultez les [annonces relatives aux versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Si vous avez déjà déployé un cluster AKS, vous avez activé la supervision à l’aide d’Azure CLI ou d’un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent.
>
> Le modèle doit être déployé dans le même groupe de ressources que le cluster.

Pour activer Container Insights, utilisez l’une des méthodes décrites dans le tableau suivant :

| État du déploiement | Méthode | Description |
|------------------|--------|-------------|
| Nouveau cluster Kubernetes | [Créer un cluster AKS à l’aide d’Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Vous pouvez activer la supervision d’un nouveau cluster AKS que vous créez à l’aide d’Azure CLI. |
| | [Créer un cluster AKS à l’aide de Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Vous pouvez activer la supervision d’un nouveau cluster AKS que vous créez à l’aide de l’outil open source Terraform. |
| | [Créer un cluster OpenShift à l’aide d’un modèle Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Vous pouvez activer la supervision d’un cluster OpenShift que vous créez à l’aide d’un modèle Azure Resource Manager préconfiguré. |
| | [Créer un cluster OpenShift à l’aide d’Azure CLI](/cli/azure/openshift#az_openshift_create) | Vous pouvez activer la supervision quand vous déployez un nouveau cluster OpenShift à l’aide d’Azure CLI. |
| Cluster AKS existant | [Activer la supervision d’un cluster AKS à l’aide d’Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Vous pouvez activer la supervision d’un cluster AKS déjà déployé à l’aide d’Azure CLI. |
| |[Activer pour un cluster AKS à l’aide de Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Vous pouvez activer la supervision d’un cluster AKS déjà déployé à l’aide de l’outil open source Terraform. |
| | [Activer pour un cluster AKS à partir d’Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Vous pouvez activer la supervision d’un ou plusieurs clusters AKS déjà déployés à partir de la page multi-cluster dans Azure Monitor. |
| | [Activer à partir du cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Vous pouvez activer la surveillance directement depuis un cluster AKS dans le portail Azure. |
| | [Activer pour un cluster AKS à l’aide d’un modèle Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Vous pouvez activer la supervision d’un cluster AKS à l’aide d’un modèle Azure Resource Manager préconfiguré. |
| Cluster Kubernetes non AKS existant | [Activer pour un cluster Kubernetes non AKS à l’aide d’Azure CLI](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-cli) | Vous pouvez activer la supervision de vos clusters Kubernetes qui sont hébergés en dehors d’Azure et dotés d’Azure Arc, y compris les clusters hybrides, OpenShift et multiclouds, à l’aide d’Azure CLI. |
| | [Activer pour un cluster Kubernetes non AKS à l’aide d’un modèle Azure Resource Manager](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-resource-manager) | Vous pouvez activer la supervision de vos clusters avec Azure Arc à l’aide d’un modèle Azure Resource Manager préconfiguré. |
| | [Activer pour un cluster Kubernetes non AKS à partir d’Azure Monitor](container-insights-enable-arc-enabled-clusters.md#create-extension-instance-using-azure-portal) | Vous pouvez activer la supervision d’un ou plusieurs clusters avec Azure Arc déjà déployés à partir de la page multi-cluster dans Azure Monitor. |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez activé la supervision, vous pouvez commencer à analyser les performances de vos clusters Kubernetes hébergés sur Azure Kubernetes Service (AKS), Azure Stack ou un autre environnement. Pour savoir comment utiliser Container Insights, consultez [Connaître les performances des clusters Kubernetes](container-insights-analyze.md).
