---
title: Intégrer Azure HPC Cache à Azure Kubernetes Service
description: Découvrez comment intégrer HPC Cache à Azure Kubernetes Service
services: container-service
author: jbut
ms.author: jebutl
ms.topic: article
ms.date: 09/08/2021
ms.openlocfilehash: 208147894b5fb483bc9ebcc49c01fd93013c3a3d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491523"
---
# <a name="integrate-azure-hpc-cache-with-azure-kubernetes-service"></a>Intégrer Azure HPC Cache à Azure Kubernetes Service

[Azure HPC Cache][hpc-cache] accélère l’accès à vos données pour les tâches de calcul haute performance. En mettant les fichiers en cache dans Azure, Azure HPC Cache apporte la scalabilité du cloud computing à votre workflow existant. Cet article vous montre comment intégrer Azure HPC Cache à Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Votre cluster AKS doit être situé [dans une région qui prend en charge Azure HPC Cache][hpc-cache-regions].

Vous devez également installer et configurer Azure CLI version 2.7 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].  Pour plus d’informations sur l’utilisation d’Azure CLI avec HPC Cache, consultez [hpc-cache-cli-prerequisites].

Vous devez également installer l’extension Azure CLI hpc-cache.  Voici les étapes à suivre :

```azurecli
az extension add --upgrade -n hpc-cache
```

## <a name="set-up-azure-hpc-cache"></a>Configurer Azure HPC Cache

Cette section décrit les étapes de création et de configuration de votre cache HPC Cache.

### <a name="1-find-the-aks-node-resource-group"></a>1. Rechercher le groupe de ressources du nœud AKS

Tout d’abord, obtenez le nom du groupe de ressources avec la commande [az aks show][az-aks-show] et ajoutez le paramètre de requête `--query nodeResourceGroup`. Vous allez créer votre cache HPC Cache dans le même groupe de ressources.

L’exemple suivant obtient le nom du groupe de ressources de nœud pour le cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

### <a name="2-create-the-cache-subnet"></a>2. Créer le sous-réseau de cache

Un certain nombre de [conditions préalables][hpc-cache-prereqs] doivent être satisfaites avant l’exécution d’un cache HPC Cache.  Plus important encore, le cache nécessite un sous-réseau *dédié* avec au moins 64 adresses IP disponibles. Ce sous-réseau ne doit pas héberger d’autres machines virtuelles ou conteneurs.  Ce sous-réseau doit être accessible à partir des nœuds AKS.

Créez le sous-réseau du cache HPC Cache dédié :

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --address-prefixes 10.0.0.0/26
```

Réinscrivez le fournisseur de ressources *Microsoft.StorageCache* :

```azurecli
az provider register --namespace Microsoft.StorageCache --wait
```

> [!NOTE]
> L’inscription du fournisseur de ressources peut prendre un certain temps.

### <a name="3-create-the-hpc-cache"></a>3. Créer le cache HPC Cache

Créez un cache HPC Cache dans le groupe de ressources du nœud de l’étape 1 et dans la même région que votre cluster AKS. Utilisez [az hpc-cache create][az-hpc-cache-create].

> [!NOTE]
> La création du cache HPC Cache prend environ 20 minutes.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyHpcCacheSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
az hpc-cache create \
  --resource-group $RESOURCE_GROUP \
  --cache-size-gb "3072" \
  --location eastus \
  --subnet $SUBNET_ID \
  --sku-name "Standard_2G" \
  --name MyHpcCache
```

### <a name="4-create-a-storage-account-and-new-container"></a>4. Créer un compte de stockage et un conteneur

Créez le compte de stockage Azure pour le conteneur de Stockage Blob.  Le cache HPC Cache met en cache le contenu qui est stocké dans ce conteneur de Stockage Blob.

> [!IMPORTANT]
> Vous devez sélectionner un nom de compte de stockage unique.  Remplacez « uniquestorageaccount » par un nom qui sera unique pour vous.

Vérifiez que le nom du compte de stockage que vous avez sélectionné est disponible.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account check-name --name $STORAGE_ACCOUNT_NAME
```

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
az storage account create \
  -n $STORAGE_ACCOUNT_NAME \
  -g $RESOURCE_GROUP \
  -l eastus \
  --sku Standard_LRS
```

Créez le conteneur Blob dans le compte de stockage.

```azurecli
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
AD_USER=$(az ad signed-in-user show --query objectId -o tsv)
CONTAINER_NAME=mystoragecontainer
az role assignment create --role "Storage Blob Data Contributor" --assignee $AD_USER --scope $STORAGE_ACCOUNT_ID
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --auth-mode login
```

Accordez des autorisations au compte de service Azure HPC Cache pour accéder à votre compte de stockage et à votre conteneur Blob.

```azurecli
HPC_CACHE_USER="StorageCache Resource Provider"
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
$HPC_CACHE_ID=$(az ad sp list --display-name "${HPC_CACHE_USER}" --query "[].objectId" -o tsv)
az role assignment create --role "Storage Account Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
az role assignment create --role "Storage Blob Data Contributor" --assignee $HPC_CACHE_ID --scope $STORAGE_ACCOUNT_ID
```

### <a name="5-configure-the-storage-target"></a>5. Configurer la cible de stockage

Ajoutez le conteneur Blob à votre cache HPC Cache en tant que cible de stockage.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
STORAGE_ACCOUNT_NAME=uniquestorageaccount
STORAGE_ACCOUNT_ID=$(az storage account show --name $STORAGE_ACCOUNT_NAME --query "id" -o tsv)
CONTAINER_NAME=mystoragecontainer
az hpc-cache blob-storage-target add \
  --resource-group $RESOURCE_GROUP \
  --cache-name MyHpcCache \
  --name MyStorageTarget \
  --storage-account $STORAGE_ACCOUNT_ID \
  --container-name $CONTAINER_NAME \
  --virtual-namespace-path "/myfilepath"
```

### <a name="6-set-up-client-load-balancing"></a>6. Configurer l’équilibrage de la charge client

Créez une zone de DNS privé Azure pour les adresses IP côté client.

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
PRIVATE_DNS_ZONE="myhpccache.local"
az network private-dns zone create \
  -g $RESOURCE_GROUP \
  -n $PRIVATE_DNS_ZONE
az network private-dns link vnet create \
  -g $RESOURCE_GROUP \
  -n MyDNSLink \
  -z $PRIVATE_DNS_ZONE \
  -v $VNET_NAME \
  -e true
```

Créez le nom DNS de type tourniquet (round robin).

```azurecli
DNS_NAME="server"
PRIVATE_DNS_ZONE="myhpccache.local"
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
HPC_MOUNTS0=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[0]" -o tsv | tr --delete '\r')
HPC_MOUNTS1=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[1]" -o tsv | tr --delete '\r')
HPC_MOUNTS2=$(az hpc-cache show --name "MyHpcCache" --resource-group $RESOURCE_GROUP --query "mountAddresses[2]" -o tsv | tr --delete '\r')
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS0
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS1
az network private-dns record-set a add-record -g $RESOURCE_GROUP -z $PRIVATE_DNS_ZONE -n $DNS_NAME -a $HPC_MOUNTS2
```

## <a name="create-the-aks-persistent-volume"></a>Créer le volume persistant AKS

Créez un fichier `pv-nfs.yaml` pour définir un [volume persistant][persistent-volume].

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 10000Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: server.myhpccache.local
    path: /
```

Tout d’abord, vérifiez que vous disposez des informations d’identification de votre cluster Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Mettez à jour *server* et *path* avec les valeurs de votre volume NFS (Network File System) que vous avez créé à l’étape précédente. Créez le volume persistant avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Vérifiez que l’état du volume persistant est **Disponible** à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistent-volume-claim"></a>Créer la revendication de volume persistant

Créez un `pvc-nfs.yaml` définissant une [revendication de volume persistant][persistent-volume-claim]. Par exemple :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 100Gi
```

Pour créer la revendication de volume persistant, utilisez la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Vérifiez que l’état de la revendication de volume persistant est **Bound** (Lié) à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-the-hpc-cache-with-a-pod"></a>Monter le cache HPC Cache avec un pod

Créez un fichier `nginx-nfs.yaml` pour définir un pod qui utilise la revendication de volume persistant. Par exemple :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/myfilepath/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Créez le pod avec la commande [kubectl apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Vérifiez que le pod est en cours d’exécution à l’aide de la commande [kubectl describe][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Vérifiez que votre volume a été monté dans le pod en utilisant [kubectl exec][kubectl-exec] pour vous connecter au pod, puis `df -h` pour vérifier si le volume est monté.

```console
kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
server.myhpccache.local:/myfilepath 8.0E         0      8.0E   0% /mnt/azure/myfilepath
...
```

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

### <a name="running-applications-as-non-root"></a>Exécution des applications en tant qu’utilisateur non racine

Si vous avez besoin d’exécuter une application en tant qu’utilisateur non-racine, vous devrez peut-être désactiver le root squash pour diriger un répertoire vers un autre utilisateur.  L’utilisateur non-racine doit posséder un répertoire pour accéder au système de fichiers.  Pour que l’utilisateur soit propriétaire d’un répertoire, l’utilisateur racine doit diriger un annuaire vers cet utilisateur, mais si le cache HPC Cache est à l’origine du root squash, cette opération est refusée car l’utilisateur racine (UID 0) est mappé à l’utilisateur anonyme.  Vous trouverez des informations supplémentaires sur le root squash et les stratégies d’accès client [ici][hpc-cache-access-policies].

### <a name="sending-feedback"></a>Envoi de commentaires

Nous sommes à votre écoute.  Veuillez envoyer vos commentaires ou vos questions à <aks-hpccache-feed@microsoft.com>.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure HPC cache, consultez [Vue d’ensemble du cache HPC Cache][hpc-cache].
* Pour plus d’informations sur l’utilisation de NFS avec AKS, consultez [Créer manuellement et utiliser un volume de serveur Linux NFS (Network File System) avec Azure Kubernetes Service (AKS)][aks-nfs].

[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[hpc-cache]: ../hpc-cache/hpc-cache-overview.md
[hpc-cache-access-policies]: ../hpc-cache/access-policies.md
[hpc-cache-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache&regions=all
[hpc-cache-cli-prerequisites]: ../hpc-cache/az-cli-prerequisites.md
[hpc-cache-prereqs]: ../hpc-cache/hpc-cache-prerequisites.md
[az-hpc-cache-create]: /cli/azure/hpc-cache#az_hpc_cache_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[persistent-volume]: concepts-storage.md#persistent-volumes
[persistent-volume-claim]: concepts-storage.md#persistent-volume-claims
