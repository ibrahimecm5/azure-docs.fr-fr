---
title: 'Démarrage rapide : Déployer un cluster AKS avec des nœuds Intel SGX de conteneur confidentiel d’enclave à l’aide de l’interface CLI Azure'
description: Découvrez comment créer un cluster AKS (Azure Kubernetes Service) avec des conteneurs confidentiels d’enclave pour déployer une application Hello World en utilisant l’interface Azure CLI.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: 142f68d332b65bb00e2b9dd4c4ea8785481e9920
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097720"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Démarrage rapide : Déployer un cluster AKS avec des nœuds d’informatique confidentielle à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous allez utiliser l’interface Azure CLI pour déployer un cluster Azure Kubernetes Service (AKS) avec des nœuds de machine virtuelle (DCsv2/DCSv3) reconnaissant les enclaves. Vous exécuterez ensuite une application Hello World simple dans une enclave. Vous pouvez également provisionner un cluster et ajouter des nœuds d’informatique confidentielle à partir du portail Azure, mais ce guide de démarrage rapide porte sur l’interface Azure CLI.

AKS est un service Kubernetes géré qui permet aux développeurs ou aux opérateurs de clusters de rapidement déployer et gérer des clusters. Pour en savoir plus, consultez la [Présentation d’AKS](../aks/intro-kubernetes.md) et la [Vue d’ensemble des nœuds confidentiels AKS](confidential-nodes-aks-overview.md).

Les fonctionnalités des nœuds d’informatique confidentielle sont les suivantes :

- Nœuds Worker Linux prenant en charge les conteneurs Linux.
- Machine virtuelle de deuxième génération avec des nœuds de machines virtuelles Ubuntu 18.04.
- Processeur compatible Intel SGX pour permettre l’exécution de vos conteneurs dans une enclave protégée qui garantit la confidentialité en tirant parti de la mémoire EPC (Encrypted Page Cache). Pour obtenir plus d’informations, consultez les [Questions fréquentes (FAQ) sur l’informatique confidentielle Azure](./confidential-nodes-aks-faq.yml).
- Pilote DCAP Intel SGX préinstallé sur les nœuds d’informatique confidentielle. Pour obtenir plus d’informations, consultez les [Questions fréquentes (FAQ) sur l’informatique confidentielle Azure](./confidential-nodes-aks-faq.yml).

> [!NOTE]
> Les machines virtuelles DCsv2/DCsv3 utilisent du matériel spécialisé qui est plus cher et dépendant de la disponibilité régionale. Pour plus d’informations, consultez les [SKU disponibles et les régions prises en charge](virtual-machine-solutions-sgx.md). Les machines virtuelles DCsv3 sont actuellement en préversion dans un nombre limité de régions. Pour obtenir des détails, reportez-vous à la page mentionnée ci-dessus. 


## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

- Un abonnement Azure actif. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Interface Azure CLI 2.0.64 ou ultérieur, installée et configurée sur votre machine de déploiement. 

  Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
- Au moins six cœurs DCsv2 disponibles dans votre abonnement. 

  Par défaut, le quota pour l’informatique confidentielle par abonnement Azure est de huit cœurs de machine virtuelle. Si vous envisagez de provisionner un cluster qui nécessite plus de huit cœurs, suivez [ces instructions](../azure-portal/supportability/per-vm-quota-requests.md) pour créer un ticket d’augmentation du quota.

## <a name="create-an-aks-cluster-with-enclave-aware-confidential-computing-nodes-and-add-on"></a>Créer un cluster AKS avec un module complémentaire et des nœuds d’informatique confidentielle reconnaissant les enclaves

Utilisez les instructions suivantes pour créer un cluster AKS avec le module complémentaire d’informatique confidentielle activé, ajouter un pool de nœuds au cluster et vérifier ce que vous avez créé.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Créer un cluster AKS avec un pool de nœuds du système

> [!NOTE]
> Si vous disposez déjà d’un cluster AKS qui répond aux critères préalables listés ci-dessus, [passez à la section suivante](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) pour ajouter un pool de nœuds d’informatique confidentielle.

Tout d’abord, créez un groupe de ressources pour le cluster en utilisant la commande [az group create][az-group-create]. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* dans la région *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

À présent, créez un cluster AKS, pourvu du module complémentaire d’informatique confidentielle activé, à l’aide de la commande [az aks create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addons confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Ajouter au cluster AKS un pool de nœuds utilisateur doté des fonctionnalités d’informatique confidentielle 

Exécutez la commande suivante pour ajouter au cluster AKS un pool de nœuds utilisateur de la taille `Standard_DC2s_v2` avec trois nœuds. Vous pouvez choisir une autre référence (SKU) de plus grande taille dans la [liste des régions et références (SKU) DCsv2/Dcsv3 prises en charge](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Après l’exécution de la commande, un nouveau pool de nœuds avec DCsv2 doit s’afficher, comportant les DaemonSets du module complémentaire d’informatique confidentielle ([Plug-in d’appareil SGX](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>Vérifier le pool de nœuds et le module complémentaire

Récupérez les informations d’identification de votre cluster AKS avec la commande [az aks get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Utilisez la commande `kubectl get pods` pour vérifier que les nœuds sont correctement créés et que les ressources DaemonSet liées à SGX s’exécutent sur des pools de nœuds DCsv2 :

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Si la sortie correspond au code précédent, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles.

Vous pouvez passer à la section [Déployer Hello World depuis une application isolée en enclave](#hello-world) de ce guide de démarrage rapide pour tester une application dans une enclave. Vous pouvez aussi utiliser les instructions suivantes pour ajouter d’autres pools de nœuds sur AKS. (AKS prend en charge l’association des pools de nœuds SGX avec les pools de nœuds non-SGX.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Ajouter un pool de nœuds d’informatique confidentielle à un cluster AKS existant<a id="existing-cluster"></a>

Cette section suppose que vous exécutez déjà un cluster AKS remplissant les critères préalables listés plus haut dans ce guide de démarrage rapide.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Activer le module complémentaire AKS de calcul confidentiel sur le cluster existant

Exécutez la commande suivante pour activer le module complémentaire de l’informatique confidentielle :

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2dcsv3-user-node-pool-to-the-cluster"></a>Ajouter un pool de nœuds utilisateur DCsv2/DCsv3 au cluster

> [!NOTE]
> Pour utiliser la fonctionnalité d’informatique confidentielle, votre cluster AKS existant doit disposer au minimum d’un pool de nœuds basé sur une référence (SKU) de machine virtuelle DCsv2/DCsv3. Pour en savoir plus sur les SKU de machine virtuelle DCs-v2 dédiés à l’informatique confidentielle, consultez les [références SKU disponibles et les régions prises en charge](virtual-machine-solutions-sgx.md).

Exécutez la commande suivante pour créer un pool de nœuds :

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Vérifiez que le nouveau pool de nœuds portant le nom *confcompool1* a été créé :

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Vérifier que les ressources DaemonSet s’exécutent sur les pools de nœuds confidentiels

Connectez-vous à votre cluster AKS existant pour effectuer la vérification suivante :

```console
kubectl get nodes
```

La sortie affiche normalement le pool *confcompool1* qui vient d’être ajouté au cluster AKS. Vous pouvez également voir d’autres DaemonSets.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Si la sortie correspond au code précédent, votre cluster AKS est maintenant prêt à exécuter des applications confidentielles. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Déployer Hello World depuis une application isolée en enclave<a id="hello-world"></a>
Vous êtes maintenant prêt à déployer une application de test. 

Créez un fichier nommé *hello-world-enclave.yaml* pour y coller le manifeste YAML suivant. Vous trouvez cet exemple de code d’application dans le [projet Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Ce déploiement suppose que vous avez déployé le module complémentaire *confcom*.

> [!NOTE]
> L’exemple suivant tire (pull) une image conteneur publique à partir de Docker Hub. Nous vous recommandons de configurer un secret d’extraction pour l’authentification à l’aide d’un compte Docker Hub au lieu de créer une demande de tirage anonyme. Pour une plus grande fiabilité lors de l’utilisation de contenu public, importez et gérez l’image dans un registre de conteneurs Azure privé. [En savoir plus sur l’utilisation des images publiques.](../container-registry/buffer-gate-public-content.md)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

À présent, utilisez la commande `kubectl apply` pour créer un exemple de tâche qui s’ouvrira dans une enclave sécurisée, comme dans l’exemple de sortie suivant :

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Vous pouvez vérifier que la charge de travail a bien créé un environnement d’exécution de confiance (l’enclave) en exécutant les commandes suivantes :

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le pool de nœuds d’informatique confidentielle que vous avez créé dans ce guide de démarrage rapide, utilisez la commande suivante : 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Pour supprimer le cluster AKS, utilisez la commande suivante : 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Étapes suivantes

* Exécutez Python, Node ou d’autres applications par le biais de conteneurs confidentiels à l’aide des [exemples de conteneurs confidentiels dans GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Exécutez des applications adaptées aux enclaves en utilisant ces [Exemples de conteneur Azure reconnaissant les enclaves dans GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
