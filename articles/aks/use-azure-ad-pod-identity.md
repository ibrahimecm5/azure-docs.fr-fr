---
title: Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)
description: Découvrez comment utiliser des identités managées par pod Azure AD dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: 3792dbfe187ef6e4b850338448bde5417b78e687
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501758"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)

Les identités managées par pod Azure Active Directory (Azure AD) utilisent des primitives Kubernetes pour associer des [identités managées pour les ressources Azure][az-managed-identities] et des identités dans Azure AD avec des pods. Les administrateurs créent des identités et des liaisons en tant que primitives Kubernetes qui permettent aux pods d’accéder aux ressources Azure qui reposent sur Azure AD en tant que fournisseur d’identité.

> [!NOTE]
> La fonctionnalité décrite dans ce document, les identités managées par pod (préversion), sera remplacée par les identités managées par pod V2 (préversion).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Avant de commencer

La ressource suivante doit être installée :

* Azure CLI, version 2.20.0 ou ultérieure
* L’extension `aks-preview` version 0.5.5 ou ultérieure

### <a name="limitations"></a>Limites

* 200 identités de pods maximum sont autorisées pour un cluster.
* 200 exceptions d’identités de pods maximum sont autorisées pour un cluster.
* Les identités managées par pod sont disponibles uniquement sur les pools de nœuds Linux.

### <a name="register-the-enablepodidentitypreview"></a>Inscrivez `EnablePodIdentityPreview`

Inscrivez la fonctionnalité `EnablePodIdentityPreview` :

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installez l’interface de ligne de commande Azure `aks-preview`

Vous devez également disposer de la version 0.5.5 ou ultérieure de l’extension Azure CLI *aks-preview*. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="operation-mode-options"></a>Options du mode d’opération

L’identité du pod Azure AD prend en charge deux modes d’opération :
 
* **Mode standard** : dans ce mode, les deux composants suivants sont déployés sur le cluster AKS : 
  * [Managed Identity Controller (MIC)](https://azure.github.io/aad-pod-identity/docs/concepts/mic/) : contrôleur Kubernetes qui surveille les modifications apportées aux pods, [AzureIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentity/) et [AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/concepts/azureidentitybinding/), via le serveur d’API Kubernetes. Lorsqu’il détecte une modification pertinente, le MIC ajoute ou supprime [AzureAssignedIdentity](https://azure.github.io/aad-pod-identity/docs/concepts/azureassignedidentity/) en fonction des besoins. Plus précisément, quand un pod est planifié, le MIC affecte l’identité managée sur Azure au groupe de machines virtuelles identiques sous-jacent que le pool de nœuds utilise au cours de la phase de création. Quand tous les pods utilisant l’identité sont supprimés, il supprime l’identité du groupe de machines virtuelles identiques du pool de nœuds, sauf si la même identité managée est utilisée par d’autres pods. Le MIC effectue des actions similaires quand AzureIdentity ou AzureIdentityBinding sont créés ou supprimés.
  * [Node Managed Identity (NMI)](https://azure.github.io/aad-pod-identity/docs/concepts/nmi/) : pod qui s’exécute en tant que DaemonSet sur chaque nœud du cluster AKS. L’identité NMI intercepte les demandes de jeton de sécurité adressées à l’[Instance Metadata Service Azure](../virtual-machines/linux/instance-metadata-service.md?tabs=linux) sur chaque nœud, les redirige vers elle-même et vérifie si le pod a accès à l’identité pour laquelle il demande un jeton et récupère le jeton auprès du locataire Azure AD pour le compte de l’application.
* **Mode managé** : ce mode offre uniquement le pod NMI. L’identité doit être affectée manuellement et gérée par l’utilisateur. Pour plus d’informations, consultez [Identité des pods en mode managé](https://azure.github.io/aad-pod-identity/docs/configure/pod_identity_in_managed_mode/).

Quand vous installez l’identité de pod Azure AD via le graphique Helm ou le manifeste YAML comme indiqué dans le [Guide d’Installation](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/), vous pouvez choisir entre les modes `standard` et `managed`. Si, à la place, vous décidez d’installer l’identité de pod Azure AD à l’aide du module complémentaire de cluster AKS comme indiqué dans l’article, le programme d’installation utilise le mode `managed`.

## <a name="create-an-aks-cluster-with-azure-container-networking-interface-cni"></a>Créer un cluster AKS avec Azure Container Networking Interface (CNI)

> [!NOTE]
> Il s’agit de la configuration recommandée par défaut.

Créez un cluster AKS avec Azure CNI et une identité managée par pod activée. Les commande suivantes utilisent [az group create][az-group-create] pour créer un groupe de ressources nommé *myResourceGroup* et la commande [az aks create][az-aks-create] pour créer un cluster AKS nommé *myAKSCluster* dans le groupe de ressources *myResourceGroup*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Utilisez [az aks get-credentials][az-aks-get-credentials] pour vous connecter à votre cluster AKS. Cette commande télécharge et configure également le certificat client `kubectl` sur votre ordinateur de développement.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> Lorsque vous activez l’identité managée par pod sur votre cluster AKS, une AzurePodIdentityException nommée *aks-addon-exception* est ajoutée à l’espace de noms *kube-system*. Une AzurePodIdentityException permet aux pods avec certaines étiquettes d’accéder au point de terminaison Instance Metadata Service (IMDS) Azure sans être interceptés par le serveur NMI. *aks-addon-exception* permet aux modules complémentaires internes AKS, comme une identité managée par pod Azure AD, de fonctionner sans avoir à configurer manuellement une AzurePodIdentityException. Si vous le souhaitez, vous pouvez ajouter, supprimer et mettre à jour une AzurePodIdentityException à l’aide de `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` ou `kubectl`.

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Mettre à jour un cluster AKS existant avec Azure CNI

Mettez à jour un cluster AKS existant avec Azure CNI pour inclure l’identité managée par pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity
```

## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Utilisation du plug-in réseau Kubenet avec des identités managées par pod Azure Active Directory 

> [!IMPORTANT]
> L’exécution d’aad-pod-identity dans un cluster avec Kubenet n’est pas une configuration recommandée en raison de l’implication de sécurité. Suivez les étapes d’atténuation et configurez les stratégies avant d’activer aad-pod-identity dans un cluster avec Kubenet.

### <a name="mitigation"></a>Limitation des risques

Pour atténuer la vulnérabilité au niveau du cluster, vous pouvez utiliser la stratégie intégrée Azure « Les conteneurs de cluster Kubernetes doivent utiliser uniquement des fonctionnalités autorisées » pour limiter les attaques de type CAP_NET_RAW.  

Ajouter NET_RAW à « Fonctionnalités Drop requises »

![image](https://user-images.githubusercontent.com/50749048/118558790-206b8880-b735-11eb-9e48-236b81116812.png)

Si vous n’utilisez pas Azure Policy, vous pouvez combiner le contrôleur d’admission OpenPolicyAgent et le webhook de validation de Gatekeeper. Si Gatekeeper est déjà installé dans votre cluster, ajoutez le modèle ConstraintTemplate de type K8sPSPCapabilities :

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Ajoutez un modèle pour limiter la génération de Pods à l’aide de la fonctionnalité NET_RAW :

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Créer un cluster AKS avec le plug-in réseau Kubenet

Créez un cluster AKS avec un plug-in réseau Kubenet et une identité managée par pod activée.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Mettre à jour un cluster AKS existant avec le plug-in réseau Kubenet

Mettez à jour un cluster AKS existant avec le plug-in réseau Kubenet pour inclure l’identité managée par pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Créer une identité

> [!IMPORTANT]
> Pour créer l’identité, vous devez disposer des autorisations appropriées (par exemple, Propriétaire) sur votre abonnement.

Créez une identité avec [az identity create][az-identity-create] et définissez les variables *IDENTITY_CLIENT_ID* et *IDENTITY_RESOURCE_ID*.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Attribuer des autorisations pour l’identité managée

Pour exécuter la démonstration, l’identité managée *IDENTITY_CLIENT_ID* doit avoir des autorisations de contributeur de machine virtuelle dans le groupe de ressources qui contient le groupe de machines virtuelles identiques de votre cluster AKS.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Créer une identité de pod

Créez une identité de pod pour le cluster à l’aide de `az aks pod-identity add`.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> La variable « POD_IDENTITY_NAME » doit être un [nom de sous-domaine DNS] valide, tel que défini dans [RFC 1123]. 

> [!NOTE]
> Lorsque vous affectez l’identité de pod à l’aide de `pod-identity add`, l’interface Azure CLI tente d’accorder le rôle d’opérateur d’identités managées sur l’identité de pod (*IDENTITY_RESOURCE_ID*) à l’identité du cluster.

## <a name="run-a-sample-application"></a>Exécuter un exemple d’application

Pour qu’un pod utilise l’identité managée par pod Azure AD, le pod a besoin d’une étiquette *aadpodidbinding* avec une valeur correspondant à un sélecteur issu d’une *AzureIdentityBinding*. Pour exécuter un exemple d’application à l’aide d’une identité managée par pod Azure AD, créez un fichier `demo.yaml` avec le contenu suivant. Remplacez *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* et *IDENTITY_RESOURCE_GROUP* par les valeurs issues des étapes précédentes. Remplacez *SUBSCRIPTION_ID* par l’ID de votre abonnement.

> [!NOTE]
> Au cours des étapes précédentes, vous avez créé les variables *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* et *IDENTITY_RESOURCE_GROUP*. Vous pouvez utiliser une commande telle que `echo` pour afficher la valeur que vous définissez pour les variables, par exemple `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: $POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=$SUBSCRIPTION_ID
      - --clientid=$IDENTITY_CLIENT_ID
      - --resourcegroup=$IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Notez que la définition du pod a une étiquette *aadpodidbinding* avec une valeur qui correspond au nom de l’identité de pod que vous avez exécutée `az aks pod-identity add` à l’étape précédente.

Déployez `demo.yaml` dans le même espace de noms que votre identité de pod à l’aide de `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Vérifiez que l’exemple d’application s’exécute correctement avec `kubectl logs`.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Vérifiez que les journaux indiquent qu’un jeton a bien été acquis et que l’opération *GET* a abouti.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="run-an-application-with-multiple-identities"></a>Exécuter une application avec plusieurs identités

Pour permettre à une application d’utiliser plusieurs identités, définissez `--binding-selector` sur le même sélecteur lors de la création d’identités de pod.

```azurecli-interactive
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_1} --identity-resource-id ${IDENTITY_RESOURCE_ID_1} --binding-selector myMultiIdentitySelector
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME_2} --identity-resource-id ${IDENTITY_RESOURCE_ID_2} --binding-selector myMultiIdentitySelector
```

Ensuite, définissez le champ `aadpodidbinding` du fichier YAML de votre pod sur le sélecteur de liaison que vous avez spécifié.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: myMultiIdentitySelector
...
```

## <a name="clean-up"></a>Nettoyage

Pour supprimer l’identité managée par pod Azure AD de votre cluster, supprimez l’exemple d’application et l’identité de pod du cluster. Puis supprimez l’identité.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[RFC 1123]: https://tools.ietf.org/html/rfc1123
[Nom de sous-domaine DNS]: https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names
