---
title: Effectuer une rotation des certificats dans Azure Kubernetes Service (AKS)
description: Découvrez comment assurer la rotation de vos certificats dans un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/03/2021
ms.openlocfilehash: cd1e55df9609adcc8d5d1d33b1853ba855889b8f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371616"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Effectuer une rotation des certificats dans Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) utilise des certificats pour l’authentification avec un grand nombre de ses composants. Régulièrement, vous pouvez être amené à procéder à une rotation de ces certificats pour des raisons de sécurité ou de stratégie. Par exemple, une stratégie peut demander une rotation de l’ensemble de vos certificats tous les 90 jours.

Cet article explique comment effectuer une rotation des certificats de votre cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

Pour les besoins de cet article, vous devez utiliser Azure CLI version 2.0.77 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Certificats AKS, autorités de certification et comptes de service

AKS génère et utilise les certificats, autorités de certification et comptes de service suivants :

* Le serveur d’API AKS crée une autorité de certification (CA) appelée « autorité de certification de cluster ».
* Le serveur d’API dispose d’une autorité de certification de cluster, qui signe les certificats pour la communication unidirectionnelle, du serveur d’API vers les kubelets.
* Chaque kubelet crée également une demande de signature de certificat (CSR), qui est signée par l’autorité de certification de cluster, pour la communication du kubelet vers le serveur d’API.
* L’agrégation d’API utilise l’autorité de certification de cluster pour émettre des certificats destinés à la communication avec d’autres API. L’agrégation d’API peut également disposer de sa propre autorité de certification pour émettre ces certificats, mais elle utilise actuellement l’autorité de certification de cluster.
* Chaque nœud utilise un jeton de compte de service (SA), qui est signé par l’autorité de certification de cluster.
* Le client `kubectl` dispose d’un certificat pour communiquer avec le cluster AKS.

> [!NOTE]
> Les clusters AKS créés avant le mois de mai 2019 possèdent des certificats qui expirent au bout de deux ans. Tout cluster créé après mai 2019, et tout cluster dont la rotation des certificats a été effectuée, dispose de certificats d’autorité de certification de cluster qui expirent au bout de 30 ans. Tous les autres certificats AKS, qui utilisent l’autorité de certification de cluster pour la signature, expirent au bout de deux ans et sont automatiquement renouvelés pendant la mise à niveau de la version AKS. Pour vérifier à quel moment votre cluster a été créé, utilisez `kubectl get nodes` pour afficher l’*âge* de vos pools de nœuds.
> 
> En outre, vous pouvez vérifier la date d’expiration du certificat de votre cluster. Par exemple, la commande Bash suivante affiche les détails du certificat pour le cluster *myAKSCluster* dans le groupe de ressources *rg*.
> ```console
> kubectl config view --raw -o jsonpath="{.users[?(@.name == 'clusterUser_rg_myAKSCluster')].user.client-certificate-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

* Vérifier la date d’expiration du certificat apiserver
```console
curl https://{apiserver-fqdn} -k -v 2>&1 |grep expire
```

* Vérifier la date d’expiration du certificat sur le nœud de l’agent VMAS
```console
az vm run-command invoke -g MC_rg_myAKSCluster_region -n vm-name --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

* Vérifier la date d’expiration du certificat sur un nœud de l’agent VMSS
```console
az vmss run-command invoke -g MC_rg_myAKSCluster_region -n vmss-name --instance-id 0 --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

## <a name="certificate-auto-rotation"></a>Rotation automatique des certificats

Azure Kubernetes Service assurera automatiquement la rotation des certificats autres que les certificats d’autorité de certification sur le plan de contrôle et les nœuds de l’agent avant qu’ils n’expirent, sans temps d’arrêt pour le cluster.

Pour qu’AKS puisse effectuer la rotation automatique des certificats autres que les certificats d’autorité de certification, le cluster doit disposer du [démarrage de TLS](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/). Le démarrage de TLS est actuellement disponible dans les régions suivantes :

* eastus2euap
* centraluseuap
* westcentralus
* uksouth
* eastus
* australiacentral
* australiaest

#### <a name="how-to-check-whether-current-agent-node-pool-is-tls-bootstrapping-enabled"></a>Comment vérifier si le pool de nœuds de l’agent actuel est activé pour le démarrage TLS ?
Pour vérifier si le démarrage TLS est activé sur votre cluster, accédez aux chemins d’accès suivants.  Sur un nœud Linux : /var/lib/kubelet/bootstrap-kubeconfig, sur un nœud Windows, il s’agit de c:\k\bootstrap-config.

> [Remarque] Le chemin d’accès du fichier peut changer à mesure que la version de K8S évolue à l’avenir.

> [!IMPORTANT]
>Une fois qu’une région est configurée, créez un nouveau cluster ou mettez à niveau un cluster existant à l’aide de la commande « az aks upgrade -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME » afin de définir ce cluster pour la rotation automatique des certificats. 

### <a name="limitation"></a>Limitation

La rotation automatique des certificats ne sera pas activée sur un cluster non RBAC.


## <a name="rotate-your-cluster-certificates"></a>Procéder à la rotation de vos certificats de cluster

> [!WARNING]
> La rotation de vos certificats à l’aide de `az aks rotate-certs` va recréer tous vos nœuds et leurs disques de système d’exploitation, et peut entraîner jusqu’à 30 minutes d’interruption pour votre cluster AKS.

Utilisez [az aks get-credentials][az-aks-get-credentials] pour vous connecter à votre cluster AKS. Cette commande télécharge et configure également le certificat client `kubectl` sur votre machine locale.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Utilisez `az aks rotate-certs` pour procéder à la rotation de tous les certificats, autorités de certification et comptes de services de votre cluster.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> L’exécution de `az aks rotate-certs` peut prendre jusqu’à 30 minutes. Si la commande échoue avant de se terminer, utilisez `az aks show` pour vérifier que l’état du cluster est *Certificate Rotating* (Rotation de certificat en cours). Si le cluster est dans l’état d’échec, réexécutez `az aks rotate-certs` pour procéder de nouveau à la rotation de vos certificats.

Vérifiez que les anciens certificats ne sont plus valides en exécutant une commande `kubectl`. Étant donné que vous n’avez pas mis à jour les certificats utilisés par `kubectl`, une erreur s’affiche.  Par exemple :

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Mettez à jour le certificat utilisé par `kubectl` en exécutant `az aks get-credentials`.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Vérifiez que les certificats ont été mis à jour en exécutant une commande `kubectl` qui, à présent, doit aboutir. Par exemple :

```console
kubectl get no
```

> [!NOTE]
> Si des services sont exécutés au-dessus d’AKS, vous devrez peut-être également mettre à jour les certificats liés à ces services.

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment procéder automatiquement à la rotation des certificats, autorités de certification et comptes de service de votre cluster. Pour plus d’informations sur les bonnes pratiques en matière de sécurité AKS, vous pouvez consulter [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades].


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
