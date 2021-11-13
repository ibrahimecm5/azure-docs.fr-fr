---
title: Créer un pool de nœuds WASI (WebAssembly System Interface) dans Azure Kubernetes Service (AKS) pour exécuter votre charge de travail WebAssembly (WASM) (préversion)
description: Découvrez comment créer un pool de nœuds WASI (WebAssembly System Interface) dans Azure Kubernetes Service (AKS) pour exécuter votre charge de travail WebAssembly (WASM) sur Kubernetes.
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 91649a6c7893970eee5685554385eed1493c0094
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265960"
---
# <a name="create-webassembly-system-interface-wasi-node-pools-in-azure-kubernetes-service-aks-to-run-your-webassembly-wasm-workload-preview"></a>Créer un pool de nœuds WASI (WebAssembly System Interface) dans Azure Kubernetes Service (AKS) pour exécuter votre charge de travail WebAssembly (WASM) (préversion)

[WebAssembly (WASM)][wasm] est un format binaire optimisé pour un téléchargement rapide et une vitesse d’exécution maximale dans un runtime WASM. Un runtime WASM est conçu pour s’exécuter sur une architecture cible et exécuter des WebAssemblies dans un bac à sable (sandbox), isolé de l’ordinateur hôte, avec des performances quasi-natives. Par défaut, les webassemblies ne peuvent pas accéder aux ressources sur l’hôte en dehors du bac à sable (sandbox), sauf s’ils sont explicitement autorisés et qu’ils ne peuvent pas communiquer sur des sockets pour accéder aux variables d’environnement ou au trafic HTTP. La norme [WebAssembly System Interface (WASI)][wasi] définit une API pour les runtimes WASM pour fournir l’accès aux webassemblies à l’environnement et aux ressources en dehors de l’hôte à l’aide d’un modèle de fonctionnalités. [Krustlet][krustlet] est un projet open source qui permet l’exécution des modules WASM sur Kubernetes. Krustlet crée un kubelet qui s’exécute sur les nœuds avec un runtime WASM/WASI. AKS vous permet de créer des pools de nœuds qui exécutent des assemblys WASM à l’aide de nœuds avec des runtimes WASM/WASI et Krustlets.

## <a name="before-you-begin"></a>Avant de commencer

Les pools de nœuds WASM/WASI sont actuellement en préversion.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Cet article utilise [Helm 3][helm] pour installer le graphique *nginx* sur une version prise en charge de Kubernetes. Assurez-vous que vous utilisez la version la plus récente de Helm et que vous avez accès au référentiel Helm *bitnami*. Les étapes décrites dans cet article peuvent ne pas être compatibles avec les versions précédentes du Helm Chart ou Kubernetes.

La ressource suivante doit également être installée :

* La version la plus récente d’Azure CLI.
* L’extension `aks-preview` version 0.5.34 ou ultérieure

### <a name="register-the-wasmnodepoolpreview-preview-feature"></a>Inscrire la fonctionnalité d’évaluation `WasmNodePoolPreview`

Pour utiliser la fonctionnalité, vous devez activer l’indicateur de fonctionnalité `WasmNodePoolPreview` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `WasmNodePoolPreview` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "WasmNodePoolPreview"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WasmNodePoolPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installez l’interface de ligne de commande Azure `aks-preview`

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.34 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limites

* Vous ne pouvez pas exécuter les WebAssemblies et les conteneurs dans le même pool de nœuds.
* Seul le runtime WebAssembly (WASI) est disponible à l’aide du fournisseur Wasmtime.
* Les pools de nœuds WASM/WASI ne peuvent pas être utilisés pour le pool de nœuds système.
* Le type de système d’exploitation (*os-type*) doit être Linux pour les pools de nœuds WASM/WASI.
* Krustlet ne fonctionne pas avec Azure CNI pour le moment. Pour plus d’informations, consultez le [problème GitHub du support CNI pour Kruslet][krustlet-cni-support].
* Krustlet ne fournit pas de configuration de mise en réseau pour les webassemblies. Le manifeste WebAssembly doit fournir la configuration réseau, par exemple, l’adresse IP.

## <a name="add-a-wasmwasi-node-pool-to-an-existing-aks-cluster"></a>Ajouter un pool de nœuds WASM/WASI à un cluster AKS existant

Pour ajouter un pool de nœuds WASM/WASI, exécutez la commande [az aks nodepool add][az-aks-nodepool-add]. L’exemple suivant crée un pool de nœuds WASI nommé *mywasipool* avec un nœud.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mywasipool \
    --node-count 1 \
    --workload-runtime wasmwasi 
```

> [!NOTE]
> La valeur par défaut du paramètre *workload-runtime* est *ocicontainer*. Pour créer un pool de nœuds qui exécute des charges de travail de conteneur, omettez le paramètre de *workload-runtime* ou définissez la valeur sur *ocicontainer*.

Vérifiez la valeur *workloadRuntime* à l’aide de `az aks nodepool show`. Par exemple :

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mywasipool
```

L’exemple de sortie suivant montre que le *mywasipool* a le type *workloadRuntime* *WasmWasi*.

```output
{
  ...
  "name": "mywasipool",
  ..
  "workloadRuntime": "WasmWasi"
}
```

Dans le cas d’un pool WASM/WASI, vérifiez que la teinte est définie sur `kubernetes.io/arch=wasm32-wagi:NoSchedule` et `kubernetes.io/arch=wasm32-wagi:NoExecute`, ce qui empêchera la planification des pods de conteneur sur ce pool de nœuds. En outre, nodeLabels doit être `kubernetes.io/arch: wasm32-wasi`, ce qui empêche la planification des pods WASM sur les pools de nœuds de conteneur standard (OCI).

> [!NOTE]
> Les teintes d’un pool de nœuds WASI ne sont pas visibles à l’aide de `az aks nodepool list`. Utilisez `kubectl` pour vérifier que les teintes sont définies sur les nœuds du pool de nœuds WASI.

Configurez `kubectl` afin de vous connecter à votre cluster Kubernetes avec la commande [az aks get-credentials][az-aks-get-credentials]. La commande ci-après effectue les opérations suivantes :  

```azurecli
az aks get-credentials -n myakscluster -g myresourcegroup
```

Utilisez la commande `kubectl get nodes` pour afficher les nœuds figurant dans votre cluster.

```output
$ kubectl get nodes -o wide
NAME                                 STATUS   ROLES   AGE   VERSION         INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
aks-mywasipool-12456878-vmss000000   Ready    agent    9m   1.0.0-alpha.1   WASINODE_IP   <none>        <unknown>            <unknown>          mvp
aks-nodepool1-12456878-vmss000000    Ready    agent   13m   v1.20.9         NODE1_IP      <none>        Ubuntu 18.04.6 LTS   5.4.0-1059-azure   containerd://1.4.9+azure
```

Enregistrez la valeur de *WASINODE_IP* telle qu’elle est utilisée dans une étape ultérieure.

Utilisez `kubectl describe node` pour afficher les étiquettes et les teintes sur un nœud dans le pool de nœuds WASI. L’exemple suivant montre les détails de *aks-mywasipool-12456878-vmss000000*.

```output
$ kubectl describe node aks-mywasipool-12456878-vmss000000

Name:               aks-mywasipool-12456878-vmss000000
Roles:              agent
Labels:             agentpool=mywasipool
...
                    kubernetes.io/arch=wasm32-wagi
...
Taints:             kubernetes.io/arch=wasm32-wagi:NoExecute
                    kubernetes.io/arch=wasm32-wagi:NoSchedule
```


## <a name="running-wasmwasi-workload"></a>Exécution de la charge de travail WASM/WASI

Pour exécuter une charge de travail sur un pool de nœuds WASM/WASI, ajoutez un sélecteur de nœud et des tolérances à votre déploiement. Par exemple :

```yml
...
spec:
  nodeSelector:
    kubernetes.io/arch: "wasm32-wagi"
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
...
```

Pour exécuter un exemple de déploiement, créez un fichier `wasi-example.yaml` à l’aide de la définition YAML suivante :

```yml
apiVersion: v1
kind: Pod
metadata:
  name: krustlet-wagi-demo
  labels:
    app: krustlet-wagi-demo
  annotations:
    alpha.wagi.krustlet.dev/default-host: "0.0.0.0:3001"
    alpha.wagi.krustlet.dev/modules: |
      {
        "krustlet-wagi-demo-http-example": {"route": "/http-example", "allowed_hosts": ["https://api.brigade.sh"]},
        "krustlet-wagi-demo-hello": {"route": "/hello/..."},
        "krustlet-wagi-demo-error": {"route": "/error"},
        "krustlet-wagi-demo-log": {"route": "/log"},
        "krustlet-wagi-demo-index": {"route": "/"}
      }
spec:
  hostNetwork: true
  nodeSelector:
    kubernetes.io/arch: wasm32-wagi
  containers:
    - image: webassembly.azurecr.io/krustlet-wagi-demo-http-example:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-http-example
    - image: webassembly.azurecr.io/krustlet-wagi-demo-hello:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-hello
    - image: webassembly.azurecr.io/krustlet-wagi-demo-index:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-index
    - image: webassembly.azurecr.io/krustlet-wagi-demo-error:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-error
    - image: webassembly.azurecr.io/krustlet-wagi-demo-log:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-log
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
```

Utilisez `kubectl` pour exécuter votre exemple de déploiement :

```azurecli-interactive
kubectl apply -f wasi-example.yaml
```

> [!NOTE]
> Le pod de l’exemple de déploiement peut rester dans l’état *Registered*. Ce comportement est normal et vous devez passer à l’étape suivante.

Créez `values.yaml` à l’aide de l’exemple de YAML ci-dessous, en remplaçant *WASINODE_IP* par la valeur de l’étape précédente.

```yml
serverBlock: |-
  server {
    listen 0.0.0.0:8080;
    location / {
            proxy_pass http://WASINODE_IP:3001;
    }
  }
```

À l’aide de [Helm][helm], ajoutez le référentiel *bitnami* et installez le graphique *nginx* avec le fichier `values.yaml` que vous avez créé à l’étape précédente. L’installation de NGINX avec l’élément `values.yaml` ci-dessus crée un proxy inverse vers l’exemple de déploiement, ce qui vous permet d’y accéder à l’aide d’une adresse IP externe.

>[!NOTE]
> L’exemple suivant tire (pull) une image conteneur publique à partir de Docker Hub. Nous vous recommandons de configurer un secret d’extraction pour l’authentification à l’aide d’un compte Docker Hub au lieu de créer une demande de tirage anonyme. Pour une plus grande fiabilité lors de l’utilisation de contenu public, importez et gérez l’image dans un registre de conteneurs Azure privé. [En savoir plus sur l’utilisation des images publiques.][dockerhub-callout]

```console
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install hello-wasi bitnami/nginx -f values.yaml
```

Utilisez `kubectl get service` pour afficher l’adresse IP externe du service *hello-wasi-ngnix*.

```output
$ kubectl get service
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
hello-wasi-nginx   LoadBalancer   10.0.58.239   EXTERNAL_IP      80:32379/TCP   112s
kubernetes         ClusterIP      10.0.0.1      <none>           443/TCP        145m
```

Vérifiez que l’exemple de déploiement s’exécute par la `curl` commande sur le chemin d’accès `/hello` *EXTERNAL_IP*.

```azurecli-interactive
curl EXTERNAL_IP/hello
```

L’exemple de sortie suivant confirme que l’exemple de déploiement est en cours d’exécution.

```output
$ curl EXTERNAL_IP/hello
hello world
```

> [!NOTE] 
> Pour publier le service sur votre propre domaine, voir [Azure DNS][azure-dns-zone] et le projet [external-dns][external-dns].

## <a name="clean-up"></a>Nettoyer

Pour supprimer NGINX, utilisez `helm delete`.

```console
helm delete hello-wasi
```

Pour supprimer l’exemple de déploiement, utilisez `kubectl delete`.

```azurecli-interactive
kubectl delete -f wasi-example.yaml
```

Pour supprimer le pool de nœuds WASM/WASI, utilisez `az aks nodepool delete`.

```azurecli-interactive
az aks nodepool delete --name mywasipool -g myresourcegroup --cluster-name myakscluster
```


<!-- EXTERNAL LINKS -->
[helm]: https://helm.sh/
[krustlet]: https://krustlet.dev/
[krustlet-cni-support]: https://github.com/krustlet/krustlet/issues/533
[wasm]: https://webassembly.org/
[wasi]: https://wasi.dev/
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- INTERNAL LINKS -->

[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[dockerhub-callout]: ../container-registry/buffer-gate-public-content.md
[install-azure-cli]: /cli/azure/install-azure-cli
[use-multiple-node-pools]: use-multiple-node-pools.md
[use-system-pool]: use-system-pools.md
