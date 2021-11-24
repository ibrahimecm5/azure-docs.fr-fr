---
title: Créer manuellement un partage Azure Files
titleSuffix: Azure Kubernetes Service
description: Découvrir comment créer manuellement un volume avec Azure Files pour une utilisation simultanée avec plusieurs pods dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/08/2021
ms.openlocfilehash: d303e00c7f1a7ef76bb048048123b65eb42de402
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179963"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Créer manuellement et utiliser un volume avec un partage Azure Files dans Azure Kubernetes Service (AKS)

Les applications basées sur des conteneurs doivent souvent consulter et conserver des données dans un volume de données externe. Si plusieurs pods doivent accéder simultanément au même volume de stockage, vous pouvez utiliser Azure Files pour vous connecter à l’aide du [protocole SMB (Server Message Block)][smb-overview]. Cet article vous montre comment créer manuellement un partage Azure Files et comment l’attacher à un pod dans AKS.

Pour plus d’informations sur les volumes Kubernetes, consultez [Options de stockage pour les applications dans AKS][concepts-storage].

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Azure CLI 2.0.59 (ou une version ultérieure) doit également être installé et configuré. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser Azure Files en tant que volume Kubernetes, vous devez créer un compte de stockage Azure et le partage de fichiers. Les commandes suivantes créent un groupe de ressources nommé *myAKSShare*, un compte de stockage et un partage de fichiers nommé *aksshare* :

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Notez le nom du compte de stockage et la clé indiquée à la fin de la sortie du script. Ces valeurs sont nécessaires lorsque vous créez le volume Kubernetes dans l’une des étapes suivantes.

## <a name="create-a-kubernetes-secret"></a>Créer un secret Kubernetes

Kubernetes a besoin d’informations d’identification pour accéder au partage de fichiers créé à l’étape précédente. Ces informations d’identification sont stockées dans un [secret Kubernetes][kubernetes-secret], qui est référencé lorsque vous créez un pod Kubernetes.

Utilisez la commande `kubectl create secret` pour créer le secret. L’exemple suivant crée un secret nommé *azure-secret* et remplit les valeurs *azurestorageaccountname* et *azurestorageaccountkey* à partir de l’étape précédente. Pour utiliser un compte de stockage Azure existant, indiquez le nom du compte et la clé.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Monter le partage de fichiers comme un volume inlined
> Remarque : `azureFile` le volume Inline peut uniquement accéder à la clé secrète dans le même espace de noms que pod. Pour spécifier un espace de noms de secret différent, utilisez plutôt un exemple de volume persistant.

Pour monter le partage Azure Files dans votre pod, configurez le volume dans les spécifications du conteneur. Créez un fichier nommé `azure-files-pod.yaml` avec le contenu suivant. Si vous avez renommé le partage de fichier ou le secret, mettez à jour les valeurs *shareName* et *secretName*. Si vous le souhaitez, actualisez `mountPath`. Il s’agit du chemin du partage Azure Files qui a été monté dans le pod. Pour les conteneurs Windows Server, spécifiez un *chemin de montage* en utilisant la convention de chemin Windows, par exemple, *'D:'* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Utilisez la commande `kubectl` pour créer le pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution sur lequel est monté un partage Azure Files à l’emplacement suivant : */mnt/azure*. Vous pouvez utiliser `kubectl describe pod mypod` pour vérifier que le partage est monté correctement. La sortie de l’exemple condensé suivant montre le volume monté dans le conteneur :

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Monter le partage de fichiers comme un volume persistant
 - Options de montage

La valeur par défaut de *fileMode* et *dirMode* est *0777* pour Kubernetes 1.15 et versions ultérieures. L’exemple suivant définit *0755* sur l’objet *PersistentVolume* :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Pour mettre à jour vos options de montage, créez un fichier *azurefile-mount-options-pv.yaml* avec un *PersistentVolume*. Par exemple :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Créez un fichier *azurefile-mount-options-pvc.yaml* avec un *PersistentVolumeClaim* qui utilise le *PersistentVolume*. Par exemple :

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Utilisez les commandes `kubectl` pour créer les *PersistentVolume* et *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Vérifiez que votre *PersistentVolumeClaim* est créé et lié au *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Mettez à jour les spécifications de votre conteneur pour référencer votre *PersistentVolumeClaim* et mettre à jour votre pod. Par exemple :

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

Comme la spécification pod ne peut pas être mise à jour sur place, utilisez `kubectl` les commandes pour supprimer, puis recréer le pod :

```console
kubectl delete pod mypod

kubectl apply -f azure-files-pod.yaml
```

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives au stockage et aux sauvegardes dans Azure Kubernetes Service (AKS)][operator-best-practices-storage].

Pour plus d’informations sur l’interaction des clusters AKS avec Azure Files, consultez le [Plug-in Kubernetes pour Azure Files][kubernetes-files].

Pour les paramètres de classe de stockage, consultez [Approvisionnement statique (apportez votre propre partage de fichiers)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
