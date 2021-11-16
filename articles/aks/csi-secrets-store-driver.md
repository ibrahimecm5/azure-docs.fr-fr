---
title: Utiliser le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets pour les secrets Azure Kubernetes Service
description: Découvrez comment utiliser le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets afin d’intégrer des magasins de secrets à Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: cdab72e0a1633aa75a5594acf9b506d944f0eaa0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893004"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-azure-kubernetes-service-aks-cluster"></a>Utiliser le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets dans un cluster Azure Kubernetes Service (AKS)

Le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets permet d’intégrer Azure Key Vault comme magasin de secrets à un cluster Kubernetes via un [volume CSI][kube-csi].

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Avant de commencer, vérifiez que votre version d’Azure CLI est >= `2.30.0` ou [installez la dernière version](/cli/azure/install-azure-cli).

### <a name="supported-kubernetes-versions"></a>Versions Kubernetes prises en charge

La version Kubernetes minimale recommandée est basée sur la [fenêtre mobile de prise en charge des versions Kubernetes][kubernetes-version-support]. Veillez à exécuter la version N-2 ou une version plus récente.

## <a name="features"></a>Fonctionnalités

- Monter des secrets, des clés et/ou des certificats dans un pod à l’aide d’un volume CSI
- Prend en charge les volumes CSI inline
- Prend en charge le montage de plusieurs objets de stockage de secrets comme un seul volume
- Prend en charge la portabilité de pod avec SecretProviderClass CRD
- Prend en charge les conteneurs Windows
- Synchronise les secrets Kubernetes
- Prend en charge la permutation automatique du contenu monté et des secrets Kubernetes synchronisés

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Créer un cluster AKS avec le fournisseur Azure Key Vault pour la prise en charge du pilote CSI du magasin de secrets

Commencez par créer un groupe de ressources Azure :

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

Pour créer un cluster AKS avec la fonctionnalité du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets, utilisez la commande [az aks create][az-aks-create] avec l’extension `azure-keyvault-secrets-provider`.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

Une identité managée affectée par l'utilisateur est créée par l’extension en vue d’accéder aux ressources Azure, nommé `azurekeyvaultsecretsprovider-*`. Pour cet exemple, nous utilisons cette identité pour la connexion au coffre de clés Azure où sont stockés nos secrets, mais d’autres [mécanismes d’accès des identités][identity-access-methods] peuvent être utilisés. Prenez note de l’`clientId` de l’identité dans la sortie :

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Mettre à niveau un cluster AKS existant avec la prise en charge du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets

Pour mettre à niveau un cluster AKS existant avec la fonctionnalité du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets, utilisez la commande [az aks enable-addons][az-aks-enable-addons] avec l’extension `azure-keyvault-secrets-provider` :

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

Comme indiqué ci-dessus, l’extension crée une identité managée affectée par l'utilisateur qui peut être utilisée pour s’authentifier auprès d’Azure Key Vault.

## <a name="verify-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>Vérifier l’installation du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets

La procédure ci-dessus installe le pilote CSI du magasin de secrets et le fournisseur Azure Key Vault sur vos nœuds. Vérifiez la bonne exécution en listant tous les pods avec les étiquettes `secrets-store-csi-driver` et `secrets-store-provider-azure` dans l’espace de noms kube-system, et vérifiez que la sortie ressemble à la suivante :

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAME                                     READY   STATUS    RESTARTS   AGE
aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

Vérifiez qu’un pod de pilote CSI du magasin de secrets et un pod du fournisseur Azure Key Vault s’exécutent sur chaque nœud des pools de nœuds de votre cluster.

## <a name="create-or-use-an-existing-azure-key-vault"></a>Créer ou utiliser un Azure Key Vault existant

En plus d’un cluster AKS, vous aurez besoin d’une ressource Azure Key Vault contenant le contenu de la clé secrète. Gardez à l’esprit que le nom du Key Vault doit être globalement unique.

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault peut stocker des clés, des secrets et des certificats. Dans cet exemple, nous allons définir un secret en texte brut appelé `ExampleSecret` :

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

Prenez note des propriétés suivantes pour une utilisation dans la section suivante :

- Nom de l’objet secret dans Key Vault
- Type d’objet (secret, clé ou certificat)
- Nom de votre ressource Azure Key Vault
- ID de locataire Azure auquel appartient l’abonnement

## <a name="provide-identity-to-access-azure-key-vault"></a>Fournir l’identité pour accéder Azure Key Vault

Le pilote CSI du magasin de secrets permet aux mécanismes suivants d’accéder à une instance Azure Key Vault :
- [Identité de pod Azure Active Directory][aad-pod-identity]
- Identité managée affectée par le système ou l’utilisateur

Suivez les étapes afin de [fournir une identité pour accéder à Azure Key Vault][identity-access-methods] pour la méthode choisie.

## <a name="validate-the-secrets"></a>Valider les secrets

Une fois le pod démarré, le contenu monté au niveau du chemin d’accès du volume spécifié dans le YAML de votre déploiement est disponible.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtaining-certificates-and-keys"></a>Obtention de certificats et de clés

La conception d’Azure Key Vault fait une nette distinction entre les clés, les secrets et les certificats. Les fonctionnalités des certificats du service Key Vault ont été conçues pour utiliser ses fonctionnalités de clé et de secret. Lorsqu’un certificat Key Vault est créé, une clé et un secret adressables sont également créés avec le même nom. La clé permet d’exécuter des opérations sur les clés, et le secret permet de récupérer la valeur du certificat sous forme de secret. Un certificat Key Vault contient également des métadonnées de certificat x509 publiques. Azure Key Vault stocke à la fois les parties publiques et privées de votre certificat dans un secret. Chaque composant individuel peut être obtenu en spécifiant `objectType` dans votre SecretProviderClass. Le tableau suivant indique l’objet mappé aux différentes ressources associées à votre certificat :

|Object|Valeur retournée|Retourne la totalité de la chaîne de certificats|
|---|---|---|
|`key`|Clé publique au format PEM|N/A|
|`cert`|Certificat au format PEM|Non|
|`secret`|Clé privée et certificat au format PEM|Oui|

## <a name="disable-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>Désactiver le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets sur un cluster AKS existant

> [!NOTE]
> Avant de désactiver l’extension, vérifiez qu’aucune `SecretProviderClass` n’est en cours d’utilisation. Si vous tentez de désactiver l’extension alors qu’une `SecretProviderClass` existe, une erreur se produit.

Pour désactiver la fonctionnalité du fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets dans un cluster existant, utilisez la commande [az aks disable-addons][az-aks-disable-addons] avec l’indicateur `azure-keyvault-secrets-provider` :

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> Si l’extension est désactivée, les charges de travail existantes n’ont aucun problème et ne voient pas les mises à jour dans les secrets montés. Si le pod redémarre ou qu’un pod est créé dans le cadre d’un événement de scale-up, le pod ne parvient pas à démarrer, car le pilote n’est plus en cours d’exécution.

## <a name="additional-configuration-options"></a>Options de configuration supplémentaires

### <a name="enabling-and-disabling-autorotation"></a>Activation et désactivation de la rotation automatique

> [!NOTE]
> Quand la permutation automatique est activée, le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets met à jour le montage du pod et le secret Kubernetes défini dans secretObjects sous SecretProviderClass en recherchant régulièrement les changements selon l’intervalle d’interrogation de permutation défini. L’intervalle d’interrogation de permutation par défaut est de 2 min.

Pour activer la rotation automatique des secrets, utilisez l’indicateur `enable-secret-rotation` lors de la création de votre cluster :

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

Ou mettez à jour un cluster existant avec le module complémentaire activé :

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

Pour spécifier un intervalle de permutation personnalisé, utilisez l’indicateur `rotation-poll-interval` :

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

Pour désactiver, utilisez l’indicateur `disable-secret-rotation` :

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>Synchroniser le contenu monté avec un secret Kubernetes

Dans certains cas, vous voulez créer un secret Kubernetes pour mettre en miroir le contenu monté.

Quand vous créez une SecretProviderClass, utilisez le champ `secretObjects` pour définir l’état souhaité des secrets Kubernetes :

> [!NOTE]
> Cet exemple n’est pas complet. Vous devez le modifier pour prendre en charge le mécanisme d’accès des identités Azure Key Vault que vous avez choisi.

> [!NOTE]
> Les secrets sont synchronisés une fois seulement quand vous démarrez un pod qui monte les secrets. Vous ne pouvez pas vous appuyer uniquement sur la synchronisation avec la fonctionnalité des secrets Kubernetes. Quand tous les pods consommant le secret sont supprimés, le secret Kubernetes est également supprimé.


> [!NOTE]
> Vérifiez que `objectName` dans `secretObjects` correspond au nom de fichier du contenu monté. Si `objectAlias` est utilisé à la place, il doit correspondre à l’alias de l’objet.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObject defines the desired state of synced K8s secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync. this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes Secret object
    type: Opaque                              # type of the Kubernetes Secret object e.g. Opaque, kubernetes.io/tls
```

#### <a name="set-environment-variables-to-reference-kubernetes-secrets"></a>Définir des variables d’environnement pour référencer des secrets Kubernetes

Une fois le secret Kubernetes créé, vous pouvez définir une variable d’environnement dans votre pod qui référence le secret Kubernetes :

> [!NOTE]
> Cet exemple n’est pas complet. Vous devez le modifier pour prendre en charge le mécanisme d’accès des identités Azure Key Vault que vous avez choisi.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: foosecret
            key: username
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-sync"
```

## <a name="metrics"></a>Mesures

### <a name="azure-key-vault-provider"></a>Fournisseur Azure Key Vault

Les métriques sont fournies via Prometheus à partir du port 8898, mais ce port n’est pas exposé par défaut en dehors du pod. Accédez aux métriques sur localhost en utilisant `kubectl port-forward` :

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

Le tableau suivant liste les métriques fournies par le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets :

|Métrique|Description|Étiquettes|
|----|----|----|
|keyvault_request|Répartition du temps nécessaire à l’obtention dans le coffre de clés|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|Répartition du temps nécessaires aux demandes gRPC|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|

### <a name="secrets-store-csi-driver"></a>Pilote CSI du magasin de secrets

Les métriques sont fournies à partir du port 8095, mais ce port n’est pas exposé par défaut en dehors du pod. Accédez aux métriques sur localhost en utilisant `kubectl port-forward` :

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

Le tableau suivant liste les métriques fournies par le pilote CSI du magasin de secrets :

|Métrique|Description|Étiquettes|
|----|----|----|
|total_node_publish|Nombre total de demandes de montage de volume réussies|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|Nombre total de demandes de démontage de volume réussies|`os_type=<runtime os>`|
|total_node_publish_error|Nombre total d’erreurs avec les demandes de montage de volume|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish_error|Nombre total d’erreurs avec les demandes de démontage de volume|`os_type=<runtime os>`|
|total_sync_k8s_secret|Nombre total de secrets k8s synchronisés|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|Répartition du temps nécessaire pour synchroniser le secret k8s|`os_type=<runtime os>`|
|total_rotation_reconcile|Nombre total de rapprochements de permutation|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|Nombre total de rapprochements de permutation avec une erreur|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|Répartition du temps nécessaire pour permuter le contenu de secrets-store pour les pods|`os_type=<runtime os>`|

## <a name="next-steps"></a>Étapes suivantes
<!-- Add a context sentence for the following links -->
Maintenant que vous avez appris à utiliser le fournisseur Azure Key Vault pour le pilote CSI du magasin de secrets avec un cluster AKS, consultez les ressources suivantes :

- [Activer les pilotes CSI pour les disques Azure et Azure Files sur AKS][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md
[identity-access-methods]: ./csi-secrets-store-identity-access.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[kubernetes-version-support]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
