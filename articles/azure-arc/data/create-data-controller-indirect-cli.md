---
title: Créer un contrôleur de données à l'aide de la CLI
description: Créez un contrôleur de données Azure Arc sur un cluster Kubernetes à nœuds multiples standard que vous avez déjà créé à l'aide de la CLI.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 836ca4cca73b71f98415c05fd89227f53332a265
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555378"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>Créer un contrôleur de données Azure Arc à l’aide de la CLI


## <a name="prerequisites"></a>Prérequis

Pour obtenir des informations générales, consultez la rubrique [Créer le contrôleur de données Azure Arc](create-data-controller.md).

### <a name="install-tools"></a>Installer des outils

Pour créer le contrôleur de données à l’aide de la CLI, vous devez installer l'extension `arcdata` pour Azure (az) CLI. 

[Installer [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Quelle que soit la plateforme cible que vous choisissez, vous devez définir les variables d’environnement suivantes pour le contrôleur de données avant la création. Ces variables d’environnement deviennent les informations d’identification utilisées pour accéder aux tableaux de bord des métriques et des journaux après la création du contrôleur de données.


### <a name="set-environment-variables"></a>Définir des variables d’environnement

Voici deux ensembles de variables d’environnement nécessaires pour accéder aux tableaux de bord des métriques et des journaux.

#### <a name="windows-powershell"></a>Windows PowerShell

```powershell
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

#### <a name="linux-or-macos"></a>Linux ou macOS

```console
export AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
export AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
export AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
export AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```


Vous devez vous connecter et vous authentifier auprès d’un cluster Kubernetes et avoir sélectionné un contexte Kubernetes existant avant de commencer la création du contrôleur de données Azure Arc. La façon dont vous vous connectez à un cluster ou un service Kubernetes varie. Consultez la documentation de la distribution ou du service Kubernetes que vous utilisez pour vous connecter au serveur de API de Kubernetes.

Vous pouvez vérifier que vous disposez d’une connexion Kubernetes actuelle et confirmer votre contexte actuel avec les commandes suivantes.

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Créer le contrôleur de données Azure Arc

> [!NOTE]
> Vous pouvez utiliser une valeur différente pour le paramètre `--namespace` de la commande `az arcdata dc create` dans les exemples ci-dessous, mais veillez à utiliser ce nom d’espace de noms pour le paramètre `--namespace` dans toutes les autres commandes ci-dessous.

- [Créer un contrôleur de données Azure Arc à l’aide de la CLI](#create-azure-arc-data-controller-using-the-cli)
  - [Composants requis](#prerequisites)
    - [Linux ou macOS](#linux-or-macos)
    - [Windows PowerShell](#windows-powershell)
  - [Créer le contrôleur de données Azure Arc](#create-the-azure-arc-data-controller)
    - [Créer Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)
    - [Créer sur AKS sur Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
    - [Créer sur Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
      - [Créer un profil de déploiement personnalisé](#create-custom-deployment-profile)
      - [Créer un contrôleur de données](#create-data-controller)
    - [Créer sur Red Hat OpenShift Container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
      - [Déterminer la classe de stockage](#determine-storage-class)
      - [Créer un profil de déploiement personnalisé](#create-custom-deployment-profile-1)
      - [Définir la classe de stockage](#set-storage-class)
      - [Définir LoadBalancer (facultatif)](#set-loadbalancer-optional)
      - [Créer un contrôleur de données](#create-data-controller-1)
    - [Créer sur open source, amont Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
    - [Créer sur AWS Elastic Kubernetes Service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
    - [Créer sur le service Google Cloud Kubernetes Engine (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)
  - [Surveillance de l’état de la création](#monitoring-the-creation-status)
  - [Résolution des problèmes de création](#troubleshooting-creation-problems)

### <a name="create-on-azure-kubernetes-service-aks"></a>Créer Azure Kubernetes Service (AKS)

Par défaut, le profil de déploiement AKS utilise la classe de stockage `managed-premium`. La classe de stockage `managed-premium` ne fonctionnera que si vous avez des machines virtuelles déployées à l’aide d’images de machine virtuelle qui ont des disques Premium.

Si vous envisagez d’utiliser `managed-premium` comme classe de stockage, vous pouvez exécuter la commande suivante pour créer le contrôleur de données. Remplacez les espaces réservés dans la commande par le nom de votre groupe de ressources, votre ID d’abonnement et l’emplacement Azure.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

Si vous n’êtes pas sûr de la classe de stockage à utiliser, vous devez utiliser la classe de stockage `default` qui est prise en charge, quel que soit le type de machine virtuelle que vous utilisez. Elle n’offre cependant pas les performances les plus rapides.

Si vous souhaitez utiliser la classe de stockage `default`, vous pouvez alors exécuter cette commande :

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Créer sur AKS sur Azure Stack HCI

#### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>Configurer le stockage (Azure Stack HCI avec AKS-HCI)

Si vous utilisez Azure Stack HCI avec AKS-HCI, créez une classe de stockage personnalisée avec `fsType`.

   ```json
   fsType: ext4
   ```

Utilisez ce type pour déployer le contrôleur de données. Consultez les instructions complètes dans [Créer une classe de stockage personnalisée pour un disque AKS sur Azure Stack HCI](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk).

Par défaut, le profil de déploiement utilise une classe de stockage nommée `default` et le type de service `LoadBalancer`.

Vous pouvez exécuter la commande suivante pour créer le contrôleur de données au moyen de la classe de stockage `default` et le type de service `LoadBalancer`.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Créer sur Azure Red Hat OpenShift (ARO)

#### <a name="create-custom-deployment-profile"></a>Créer un profil de déploiement personnalisé

Utilisez le profil `azure-arc-azure-openshift` pour Azure RedHat Open Shift.

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Créer un contrôleur de données

Vous pouvez exécuter la commande suivante pour créer le contrôleur de données :

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Créer sur Red Hat OpenShift Container Platform (OCP)

#### <a name="determine-storage-class"></a>Déterminer la classe de stockage

Vous devrez également déterminer la classe de stockage à utiliser en exécutant la commande suivante.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Créer un profil de déploiement personnalisé

Créez un fichier de profil de déploiement personnalisé basé sur le profil de déploiement `azure-arc-openshift` en exécutant la commande suivante. Cette commande crée un répertoire `custom` dans votre répertoire de travail actuel et un fichier de profil de déploiement personnalisé `control.json` dans ce répertoire.

Utilisez le profil `azure-arc-openshift` pour OpenShift Container Platform.

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Définir la classe de stockage 

À présent, définissez la classe de stockage souhaitée en remplaçant `<storageclassname>` dans la commande ci-dessous par le nom de la classe de stockage que vous souhaitez utiliser, qui a été déterminée en exécutant la commande `kubectl get storageclass` ci-dessus.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Définir LoadBalancer (facultatif)

Par défaut, le profil de déploiement `azure-arc-openshift` utilise `NodePort` comme type de service. Si vous utilisez un cluster OpenShift intégré à un équilibreur de charge, vous pouvez changer la configuration pour utiliser le type de service `LoadBalancer` à l’aide de la commande suivante :

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="create-data-controller"></a>Créer un contrôleur de données

Vous êtes maintenant prêt à créer le contrôleur de données à l’aide de la commande suivante.

> [!NOTE]
>   Le paramètre `--path` doit pointer vers le _répertoire_ contenant le fichier control.json et non vers le fichier control.json lui-même.

> [!NOTE]
>   Lors du déploiement sur OpenShift Container Platform, vous devez spécifier la valeur du paramètre `--infrastructure`.  Les options sont : `aws`, `azure`, `alibaba`, `gcp`, `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Créer sur open source, amont Kubernetes (kubeadm)

Par défaut, le profil de déploiement kubeadm utilise une classe de stockage appelée `local-storage` et le type de service `NodePort`. Si cela est acceptable, vous pouvez ignorer les instructions ci-dessous qui définissent la classe de stockage et le type de service souhaités, puis exécuter immédiatement la commande `az arcdata dc create` ci-dessous.

Si vous souhaitez personnaliser votre profil de déploiement pour spécifier une classe de stockage et/ou un type de service spécifique, commencez par créer un fichier de profil de déploiement personnalisé basé sur le profil de déploiement kubeadm en exécutant la commande suivante. Cette commande va créer un répertoire `custom` dans votre répertoire de travail actuel et un fichier de profil de déploiement personnalisé `control.json` dans ce répertoire.

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom 
```

Vous pouvez rechercher les classes de stockage disponibles en exécutant la commande suivante.

```console
kubectl get storageclass
```

À présent, définissez la classe de stockage souhaitée en remplaçant `<storageclassname>` dans la commande ci-dessous par le nom de la classe de stockage que vous souhaitez utiliser, qui a été déterminée en exécutant la commande `kubectl get storageclass` ci-dessus.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Par défaut, le profil de déploiement kubeadm utilise `NodePort` comme type de service. Si vous utilisez un cluster Kubernetes intégré à un équilibreur de charge, vous pouvez modifier la configuration à l’aide de la commande suivante.

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

Vous êtes maintenant prêt à créer le contrôleur de données à l’aide de la commande suivante.

> [!NOTE]
>   Lors du déploiement sur OpenShift Container Platform, vous devez spécifier la valeur du paramètre `--infrastructure`.  Les options sont : `aws`, `azure`, `alibaba`, `gcp`, `onpremises`.

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Créer sur AWS Elastic Kubernetes Service (EKS)

Par défaut, la classe de stockage EKS est `gp2` et le type de service est `LoadBalancer`.

Exécutez la commande suivante pour créer le contrôleur de données à l’aide du profil de déploiement EKS fourni.

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Créer sur le service Google Cloud Kubernetes Engine (GKE)

Par défaut, la classe de stockage GKE est `standard` et le type de service est `LoadBalancer`.

Exécutez la commande suivante pour créer le contrôleur de données à l’aide du profil de déploiement GKE fourni.

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Une fois que vous avez exécuté la commande, passez à [Contrôle de l’état de la création](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Surveillance de l’état de la création

La création du contrôleur prend plusieurs minutes. Vous pouvez superviser la progression dans une autre fenêtre de terminal avec les commandes suivantes :

> [!NOTE]
>  Les exemples de commandes ci-dessous supposent que vous avez créé un contrôleur de données et un espace de noms Kubernetes avec le nom `arc`. Si vous avez utilisé un autre nom pour le contrôleur de données/espace de noms, vous pouvez remplacer `arc` par ce nom.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Vous pouvez également vérifier l’état de la création de n’importe quel pod en exécutant une commande comme celle qui figure ci-dessous. C’est particulièrement utile pour résoudre les problèmes.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Résolution des problèmes de création

Si vous rencontrez des problèmes avec la création, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).
