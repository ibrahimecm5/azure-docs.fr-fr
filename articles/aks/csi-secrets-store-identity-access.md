---
title: Fournir au pilote CSI du magasin de secrets une identité d’accès au fournisseur Azure Key Vault pour les secrets Azure Kubernetes Services (AKS)
description: Découvrez les différentes méthodes qui permettent d’autoriser le fournisseur Azure Key Vault à intégrer le pilote CSI du magasin de secrets à Azure Key Vault (AKV).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9e0741cd9f1f90efc4184891401d1d05fc5b5b2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511408"
---
# <a name="provide-an-identity-to-access-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Fournir une identité au pilote CSI du magasin de secrets pour accéder au fournisseur Azure Key Vault

Le pilote CSI du magasin de secrets sur Azure Kubernetes Service (AKS) permet d’utiliser divers mécanismes d’accès basé sur l’identité à Azure Key Vault (AKV). Cet article décrit ces mécanismes et comment les utiliser pour accéder à une instance AKV et à son contenu à partir de votre cluster. Pour plus d’informations, consultez [Utilisation du pilote CSI du magasin de secrets][csi-secrets-store-driver].

## <a name="use-pod-identity"></a>Utiliser une identité de pod

Les identités managées par pod Azure Active Directory utilisent des primitives Kubernetes pour associer des identités managées pour les ressources Azure et des identités dans Azure Active Directory (AAD) avec des pods. Ces identités peuvent être utilisées pour accorder au pilote CSI du magasin de secrets l’accès au fournisseur de secrets Azure Key Vault.

### <a name="prerequisites"></a>Prérequis

- Vérifier que l’[extension des identités de pod AAD][aad-pod-identity] a été activée sur votre cluster
- Vous devez utiliser un cluster Linux

### <a name="usage"></a>Utilisation

1. Suivez les étapes décrites dans [Utiliser des identités gérées par un pod AAD][aad-pod-identity-create] pour créer une identité de cluster, lui attribuer des autorisations et créer une identité de pod. Prenez note des valeurs de `clientId` et `name` de l’identité nouvellement créée.

2. Attribuez des autorisations à la nouvelle identité en lui permettant de lire votre instance AKV et de voir son contenu avec la commande suivante :

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
```

3. Créez une SecretProviderClass avec le YAML suivant, en renseignant vos valeurs pour `aadpodidbinding`, `tenantId` et les objets à récupérer à partir de votre instance AKV :

```yml
# This is a SecretProviderClass example using aad-pod-identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               # Set to true for using aad-pod-identity to access keyvault
    keyvaultName: <key-vault-name>       # Set to the name of your Azure Key Vault instance
    cloudName: ""                        # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```

4. Appliquez la SecretProviderClass à votre cluster :

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Créez un pod avec le YAML suivant, en renseignant le nom de votre identité :

```yml
# This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-podid
  labels:
    aadpodidbinding: <name>                   # Set the label value to the name of your pod identity
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
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-podid"
```

6. Appliquez le pod à votre cluster :

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-user-assigned-managed-identity"></a>Utiliser une identité managée affectée par l’utilisateur

1. Vous pouvez utiliser l’identité managée affectée par l’utilisateur qui a été créée pendant l’[activation de l’identité managée sur votre cluster AKS][use-managed-identity] pour accéder à votre instance AKV :

```azurecli-interactive
az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
```

Vous pouvez aussi en créer une et l’attribuer à votre groupe de machines virtuelles identiques ou à chaque instance VM dans votre groupe à haute disponibilité :

```azurecli-interactive
az identity create -g <resource-group> -n <identity-name> 
az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
```

2. Accordez des autorisations à votre identité en lui permettant de lire votre instance AKV et de voir son contenu avec la commande suivante :

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
```

3. Créez une SecretProviderClass avec le YAML suivant, en renseignant vos valeurs pour `userAssignedIdentityID`, `keyvaultName`, `tenantId` et les objets à récupérer à partir de votre instance AKV :

```yml
# This is a SecretProviderClass example using user-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-user-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"          # Set to true for using managed identity
    userAssignedIdentityID: <client-id>   # Set the clientID of the user-assigned managed identity to use
    keyvaultName: <key-vault-name>        # Set to the name of your Azure Key Vault instance
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret              # object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>                 # The tenant ID of the Azure Key Vault instance
```

4. Appliquez la SecretProviderClass à votre cluster :

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Créez un pod avec le YAML suivant, en renseignant le nom de votre identité :

```yml
# This is a sample pod definition for using SecretProviderClass and user-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-user-msi
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
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-user-msi"
```

6. Appliquez le pod à votre cluster :

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-system-assigned-managed-identity"></a>Utiliser une identité managée affectée par le système

### <a name="prerequisites"></a>Prérequis

- [Activer l’identité managée affectée par le système][enable-system-assigned-identity] dans les machines virtuelles ou les groupes identiques de votre cluster

### <a name="usage"></a>Utilisation

1. Vérifiez que les nœuds de votre groupe de machines virtuelles identiques ou de votre groupe à haute disponibilité ont leur propre identité affectée par le système :

```azurecli-interactive
az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
az vm identity show -g <resource group> -n <vm name> -o yaml
```

La sortie doit contenir `type: SystemAssigned`. Notez la valeur de `principalId`.

2. Attribuez des autorisations à l’identité en lui permettant de lire votre instance AKV et de voir son contenu avec la commande suivante :

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
```

3. Créez une SecretProviderClass avec le YAML suivant, en renseignant vos valeurs pour `keyvaultName`, `tenantId` et les objets à récupérer à partir de votre instance AKV :

```yml
# This is a SecretProviderClass example using system-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-system-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"    # Set to true for using managed identity
    userAssignedIdentityID: ""      # If empty, then defaults to use the system assigned identity on the VM
    keyvaultName: <key-vault-name>
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>           # The tenant ID of the Azure Key Vault instance
```

4. Appliquez la SecretProviderClass à votre cluster :

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Créez un pod avec le YAML suivant, en renseignant le nom de votre identité :

```yml
# This is a sample pod definition for using SecretProviderClass and system-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-system-msi
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
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-system-msi"
```

## <a name="next-steps"></a>Étapes suivantes

[Vérifiez que les secrets][validate-secrets] sont montés au niveau du chemin du volume spécifié dans le YAML de votre pod.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
