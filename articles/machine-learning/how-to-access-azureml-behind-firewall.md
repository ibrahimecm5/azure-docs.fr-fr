---
title: Configurer le trafic réseau entrant et sortant
titleSuffix: Azure Machine Learning
description: Comment configurer le trafic réseau entrant et sortant requis lors de l’utilisation d’un espace de travail Azure Machine Learning sécurisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.custom: devx-track-python, ignite-fall-2021
ms.openlocfilehash: 075c6404acbb4e2d74967873e3a8359076c1a228
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056525"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>Configurer le trafic réseau entrant et sortant

Dans cet article, découvrez les exigences de communication réseau pour la sécurisation d’un espace de travail Azure Machine Learning dans un réseau virtuel (VNet). Il explique comment configurer le Pare-feu Azure pour contrôler l’accès à votre espace de travail Azure Machine Learning et à l’Internet public. Pour en savoir plus sur la sécurisation d’Azure Machine Learning, consultez [Sécurité de l’entreprise pour Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> Les informations dans cet article s’appliquent à l’espace de travail Azure Machine Learning configuré avec un point de terminaison privé.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>Accès Internet public obligatoire

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Pare-feu Azure

> [!IMPORTANT]
> Le Pare-feu Azure permet de sécuriser les _ressources du réseau virtuel Azure_. Certains services Azure, tels que les comptes Stockage Azure, présentent des paramètres de pare-feu qui leur sont propres et _s’appliquent au point de terminaison public de cette instance de service spécifique_. Les informations contenues dans ce document sont spécifiques au Pare-feu Azure.
> 
> Pour plus d’informations sur les paramètres de pare-feu d’instance de service, consultez [Utiliser Studio dans un réseau virtuel](how-to-enable-studio-virtual-network.md#firewall-settings).

* Pour le trafic __entrant__ vers le cluster de calcul Azure Machine Learning et l’instance de calcul, utilisez des [itinéraires définis par l’utilisateur (UDR)](../virtual-network/virtual-networks-udr-overview.md) afin d’éviter le pare-feu.

* Pour le trafic __sortant__, créez des règles de type __réseau__ et __application__. 

Ces regroupements de règles sont décrits plus en détail dans [Quels sont les concepts de Pare-feu Azure ?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Configuration entrante

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>Configuration sortante

1. Ajoutez des __règles de réseau__, permettant le trafic __vers__ et __depuis__ les étiquettes de service suivantes :

    | Étiquette de service | Protocol | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Non requis dans Azure Chine. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | KeyVault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.Region est nécessaire uniquement pour les images Docker personnalisées. Cela comprend des modifications mineures (par exemple, des packages supplémentaires) pour les images de base fournies par Microsoft.
    > * MicrosoftContainerRegistry.region est requis uniquement si vous prévoyez d’utiliser les _images Docker par défaut fournies par Microsoft_ et que vous _activez les dépendances gérées par l’utilisateur_.
    > * Key Vault.region n’est nécessaire que si votre espace de travail a été créé avec l’indicateur [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) activé.
    > * Pour les entrées contenant `region`, remplacez cette valeur par la région Azure que vous utilisez. Par exemple : `ContainerRegistry.westus`.

1. Ajoutez des __règles d’application__ pour les hôtes suivants :

    > [!NOTE]
    > Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Python sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

    | **Nom d’hôte** | **Objectif** |
    | ---- | ---- |
    | **graph.windows.net** | Utilisé par l’instance ou le cluster de calcul Azure Machine Learning. |
    | **anaconda.com**</br>**\*.anaconda.com** | Utilisé pour installer les packages par défaut. |
    | **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
    | **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |
    | **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN pour le développement R. |
    | **\*pytorch.org** | Utilisé par certains exemples basés sur PyTorch. |
    | **\*.tensorflow.org** | Utilisé par certains exemples basés sur Tensorflow. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Utilisé pour récupérer les bits du serveur VS Code qui sont installés sur l’instance de calcul par le biais d’un script d’installation.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Utilisé pour récupérer les bits du serveur websocket qui sont installés sur l’instance de calcul. Le serveur websocket est utilisé pour transmettre les requêtes du client Visual Studio Code (application de bureau) au serveur Visual Studio Code s’exécutant sur l’instance de calcul.|
    | **dc.applicationinsights.azure.com** | Utilisé pour collecter des informations sur les métriques et les diagnostics lorsque vous travaillez avec le support Microsoft. |
    | **dc.applicationinsights.microsoft.com** | Utilisé pour collecter des informations sur les métriques et les diagnostics lorsque vous travaillez avec le support Microsoft. |
    | **dc.services.visualstudio.com** | Utilisé pour collecter des informations sur les métriques et les diagnostics lorsque vous travaillez avec le support Microsoft. | 
    

    Pour __Protocol:Port__, sélectionnez __http, https__.

    Pour plus d’informations sur la configuration des règles d’application, consultez [Déployer et configurer le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Pour limiter le trafic sortant pour les modèles déployés sur Azure Kubernetes Service (AKS), consultez les articles [Restreindre le trafic sortant dans Azure Kubernetes Service](../aks/limit-egress-traffic.md) et [Déployer des modèles Machine Learning dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Lorsque vous utilisez Azure Kubernetes Service avec Azure Machine Learning, le trafic suivant doit être autorisé :

* Exigences générales en entrée/sortie pour AKS, comme décrit dans l’article [Restreindre le trafic sortant dans le service Kubernetes Azure](../aks/limit-egress-traffic.md).
* __Sortant__ vers mcr.microsoft.com.
* Lors du déploiement d’un modèle sur un cluster AKS, suivez les instructions de l’article [Déployer des modèles ML dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

## <a name="other-firewalls"></a>Autres pare-feu

Les instructions de cette section sont génériques, car chaque pare-feu dispose de sa propre terminologie et de configurations spécifiques. Si vous avez des questions, consultez la documentation du pare-feu que vous utilisez.

S’il n’est pas configuré correctement, le pare-feu peut causer des problèmes lors de l’utilisation de votre espace de travail. Des noms d’hôtes différents sont utilisés à la fois par l’espace de travail Azure Machine Learning. Les sections suivantes répertorient les hôtes requis pour Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hôtes Microsoft

Les hôtes des tableaux suivants sont détenus par Microsoft et fournissent les services nécessaires au bon fonctionnement de votre espace de travail. Les tableaux suivants répertorient les hôtes des régions Azure (public), Azure Government et Azure China 21Vianet.

**Hôtes Azure généraux**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | .chinacloudapi.cn |
| Portail Azure | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Hôtes Azure Machine Learning**

> [!IMPORTANT]
> Dans le tableau suivant, remplacez `<storage>` par le nom du compte de stockage par défaut de votre espace de travail Azure Machine Learning.

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Notebook intégré | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Notebook intégré | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| Notebook intégré | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| Notebook intégré | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| Notebook intégré | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Notebook intégré | \*.aznbcontent.net |  | |

**Hôtes d’instance de calcul et de cluster de calcul Azure Machine Learning**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Cluster/instance de calcul | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Instance de calcul | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Instance de calcul | \*.instances.azureml.ms |  |  |
| Compte Stockage Azure | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> Votre pare-feu doit autoriser la communication avec \*.instances.azureml.ms sur les ports __TCP__ __18881, 443 et 8787__.

> [!TIP]
> Le nom de domaine complet pour le Coffre de clés Azure n’est nécessaire que si votre espace de travail a été créé avec l’indicateur [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) activé.

**Images Docker gérées par Azure Machine Learning**

| **Obligatoire pour** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Registre de conteneurs Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Images prédéfinies Azure Machine Learning | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * __Azure Container Registry__ est requis pour toute image Docker personnalisée. Cela comprend des modifications mineures (par exemple, des packages supplémentaires) pour les images de base fournies par Microsoft.
> * __Microsoft Container Registry__ est requis uniquement si vous prévoyez d’utiliser les _images Docker par défaut fournies par Microsoft_ et que vous _activez les dépendances gérées par l’utilisateur_.
> * Si vous envisagez d’utiliser une identité fédérée, suivez les instructions de l’article [Bonnes pratiques pour sécuriser les services de fédération Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs).

En outre, utilisez les informations de la section [configuration entrante](#inbound-configuration) afin d’ajouter des adresses IP pour `BatchNodeManagement` et `AzureMachineLearning`.

Pour plus d’informations sur la restriction de l’accès aux modèles déployés sur AKS, consultez [Limiter le trafic de sortie dans Azure Kubernetes Service (AKS)](../aks/limit-egress-traffic.md).

**Diagnostics de support**

Pour que le Support Microsoft puisse diagnostiquer les problèmes que vous rencontrez avec votre espace de travail, vous devez autoriser le trafic sortant vers les hôtes suivants :

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

Pour obtenir la liste des adresses IP de ces hôtes, consultez la section [adresses IP utilisées par Azure Monitor](../azure-monitor/app/ip-addresses.md).

### <a name="python-hosts"></a>Hôtes Python

Les hôtes de cette section permettent d’installer des packages Python et sont requis lors du développement, de la formation et du déploiement. 

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Python sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Utilisé pour installer les packages par défaut. |
| **\*.anaconda.org** | Utilisé pour récupérer les données des dépôts. |
| **pypi.org** | Utilisé pour lister les dépendances de l’index par défaut, le cas échéant, et si l’index n’a pas été remplacé par les paramètres utilisateur. Si l’index a été remplacé, vous devez également autoriser **\*.pythonhosted.org**. |
| **\*pytorch.org** | Utilisé par certains exemples basés sur PyTorch. |
| **\*.tensorflow.org** | Utilisé par certains exemples basés sur Tensorflow. |

### <a name="r-hosts"></a>Hôtes R

Les hôtes de cette section permettent d’installer des packages R et sont requis lors du développement, de la formation et du déploiement.

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources R sur Internet, uniquement celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
| **cloud.r-project.org** | Utilisé lors de l’installation des packages CRAN. |

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Lorsque vous utilisez Azure Kubernetes Service avec Azure Machine Learning, le trafic suivant doit être autorisé :

* Exigences générales en entrée/sortie pour AKS, comme décrit dans l’article [Restreindre le trafic sortant dans le service Kubernetes Azure](../aks/limit-egress-traffic.md).
* __Sortant__ vers mcr.microsoft.com.
* Lors du déploiement d’un modèle sur un cluster AKS, suivez les instructions de l’article [Déployer des modèles ML dans Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes compatible avec Azure Arc<a id="arc-kubernetes"></a>

Les clusters Kubernetes Azure Arc dépendent des connexions Azure Arc. Veillez à respecter la [Configuration requise pour le réseau Azure Arc](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli#meet-network-requirements).

Les hôtes de cette section sont utilisés pour déployer l’extension Azure Machine Learning sur les clusters Kubernetes et envoyer des charges de travail d’apprentissage et d’inférence aux clusters.

**Déploiement de l’extension d’Azure Machine Learning**

Activez l’accès sortant aux points de terminaison suivants lors du déploiement de l’extension d’Azure Machine Learning sur le cluster.

| Point de terminaison de destination| Port | Utilisation |
|--|--|--|
|  *.data.mcr.microsoft.com| https:443 | Obligatoire pour le stockage MCR assuré par Azure Content Delivery Network (CDN). |
| quay.io, *.quay.io | https:443 | Registre Quay.io, requis pour extraire des images conteneur pour les composants d’extension AML |
| gcr.io| https:443 | Référentiel cloud Google, requis pour extraire des images conteneur pour les composants d’extension AML |
| storage.googleapis.com | https:443 | Google Cloud Storage, les images GCR sont hébergées sur |
| registry-1.docker.io, production.cloudflare.docker.com  | https:443 | Registre du hub Docker, requis pour extraire les images de conteneur pour les composants d’extension AML |
| auth.docker.io| https:443 | Authentification du référentiel Docker, requis pour accéder au registre du Hub docker |
| *.kusto.windows.net, *.table.core.windows.net, *.queue.core.windows.net | https:443 | Requis pour télécharger et analyser les journaux système dans Kusto |

**Uniquement les charges de travail d’apprentissage**

Activez l’accès sortant aux points de terminaison suivants pour soumettre des charges de travail d’apprentissage au cluster.

| Point de terminaison de destination| Port | Utilisation |
|--|--|--|
| pypi.org | https:443 | Index du package Python, pour installer les packages PIP utilisés pour initialiser l’environnement de travail |
| archive.ubuntu.com, security.ubuntu.com, ppa.launchpad.net | http:80 | Cette adresse permet au conteneur init de télécharger les mises à jour et les correctifs de sécurité requis |

**Charges de travail d’apprentissage et d’inférence**

En plus des points de terminaison pour les charges de travail d’apprentissage, activez l’accès sortant pour les points de terminaison suivants pour soumettre des charges de travail d’apprentissage et d’inférence.

| Point de terminaison de destination| Port | Utilisation |
|--|--|--|
| *.azurecr.io | https:443 | Azure Container Registry, requis pour extraire les images de conteneur pour héberger des tâches d’apprentissage ou d’inférence|
| *.blob.core.windows.net | https:443 | Stockage d’objets BLOB Azure, requis pour extraire les scripts de projet Machine Learning, les images de conteneur et les journaux de travail/métriques |
| *.workspace.\<region\>.api.azureml.ms ,  \<region\>.experiments.azureml.net,  \<region\>.api.azureml.ms | https:443 | API du service Azure Machine Learning, nécessaire pour communiquer avec AML |

### <a name="visual-studio-code-hosts"></a>Hôtes Visual Studio Code

Les hôtes de cette section permettent d’installer les packages Visual Studio Code afin d’établir une connexion à distance entre Visual Studio Code et les instances de calcul dans votre espace de travail Azure Machine Learning.

> [!NOTE]
> Il ne s’agit pas d’une liste complète des ordinateurs hôtes requis pour toutes les ressources Visual Studio Code sur Internet, uniquement pour celles les plus couramment utilisées. Par exemple, si vous avez besoin d’accéder à un référentiel GitHub ou à un autre hôte, vous devez identifier et ajouter les ordinateurs hôtes requis pour ce scénario.

| **Nom d’hôte** | **Objectif** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Utilisé pour récupérer les bits du serveur VS Code qui sont installés sur l’instance de calcul par le biais d’un script d’installation.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |Utilisé pour récupérer les bits du serveur websocket qui sont installés sur l’instance de calcul. Le serveur websocket est utilisé pour transmettre les requêtes du client Visual Studio Code (application de bureau) au serveur Visual Studio Code s’exécutant sur l’instance de calcul. |

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)

Pour plus d’informations sur la configuration du Pare-feu Azure, consultez [Didacticiel : Déployer et configurer le Pare-feu Azure à l’aide du portail Azure](../firewall/tutorial-firewall-deploy-portal.md).
