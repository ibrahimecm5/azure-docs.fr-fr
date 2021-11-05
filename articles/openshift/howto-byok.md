---
title: Chiffrer des disques de système d’exploitation avec une clé gérée par le client (CMK) sur Azure Red Hat OpenShift (ARO)
description: Chiffrer des disques de système d’exploitation avec une clé gérée par le client (CMK) sur Azure Red Hat OpenShift (ARO)
author: sayjadha
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: chiffrement, byok, aro, déploiement, openshift, red hat
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: b4df39040ac3cfbab2ecfa8cb24c72ff19ee5bdc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048232"
---
# <a name="encrypt-os-disks-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Chiffrer des disques de système d’exploitation avec une clé gérée par le client (CMK) sur Azure Red Hat OpenShift (ARO) (préversion)

Par défaut, les disques de système d’exploitation des machines virtuelles d’un cluster Azure Red Hat OpenShift étaient chiffrés avec les clés générées automatiquement, gérées par Microsoft Azure. Pour renforcer la sécurité, les clients peuvent chiffrer les disques de système d’exploitation avec des clés auto-gérées lors du déploiement d’un cluster ARO. Cette fonctionnalité permet de mieux contrôler le chiffrement des données confidentielles avec les clés gérées par le client.

Les clusters créés avec des clés gérées par le client ont une classe de stockage par défaut activée avec leurs clés. Par conséquent, les disques de système d’exploitation et les disques de données sont chiffrés par ces clés. Les clés gérées par le client doivent être stockées dans Azure Key Vault. Pour plus d’informations sur l’utilisation d’Azure Key Vault pour créer et gérer des clés, consultez la page [Chiffrement côté serveur de Stockage sur disque Azure](../key-vault/general/basic-concepts.md) dans la documentation Microsoft Azure.

Avec le chiffrement basé sur l’hôte, les données stockées sur l’hôte de machine virtuelle des machines virtuelles de vos nœuds d’agent ARO sont chiffrées au repos et les flux sont chiffrés dans le service de Stockage. Cela signifie que les disques temporaires sont chiffrés au repos avec des clés gérées par la plateforme. Le cache du système d’exploitation et des disques de données est chiffré au repos avec des clés gérées par le client ou par la plateforme, selon le type de chiffrement défini sur ces disques. Par défaut, lorsque vous utilisez ARO, le système d’exploitation et les disques de données sont chiffrés au repos avec des clés gérées par la plateforme, ce qui signifie que les caches de ces disques sont également chiffrés au repos par défaut avec des clés gérées par la plateforme. Vous pouvez spécifier vos propres clés gérées en suivant les étapes de chiffrement ci-dessous. Le cache de ces disques sera également chiffré à l’aide de la clé que vous spécifiez au cours de cette étape.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation ARO sont disponibles en libre-service en y adhérant. Les fonctionnalités en préversion sont fournies « en l’état » et « en fonction des disponibilités », et sont exclues des contrats de niveau de service et de la garantie limitée. Les fonctionnalités en préversion sont, dans la mesure du possible, partiellement couvertes par le service clientèle. En tant que tel, ces fonctionnalités ne sont pas destinées à une utilisation en production.

## <a name="limitation"></a>Limitation
Il incombe aux clients de conserver le coffre de clés et le jeu de chiffrement de disque dans Azure. Si les clés ne sont pas conservées, les clusters ARO sont rompus. Les machines virtuelles cessent de fonctionner et, par conséquent, l’ensemble du cluster ARO cesse de fonctionner. L’équipe d’ingénierie Azure Red Hat OpenShift ne peut pas accéder aux clés. Par conséquent, elle ne peut pas sauvegarder, répliquer ou récupérer les clés. Pour plus d’informations sur l’utilisation de jeux de chiffrement de disque pour gérer vos clés de chiffrement, consultez [Chiffrement côté serveur de Stockage sur disque Azure](../virtual-machines/disk-encryption.md) dans la documentation Microsoft Azure.

## <a name="prerequisites"></a>Prérequis
* [Vérifiez vos autorisations](tutorial-create-cluster.md#verify-your-permissions). Vous devez disposer d’autorisations Contributeur et d’autorisations Administrateur de l’accès utilisateur ou d’autorisations Propriétaire.
* Inscrivez les fournisseurs de ressources si vous avez plusieurs abonnements Azure. Pour plus d’informations sur l’inscription, consultez [Inscrire les fournisseurs de ressources](tutorial-create-cluster.md#register-the-resource-providers).

## <a name="install-the-preview-azure-cli-extension"></a>Installer l’extension Azure CLI en préversion
Installez et utilisez l’interface CLI (Command-Line Interface) pour créer une coffre de clés. L’interface CLI vous permet d’exécuter des commandes par le biais d’un terminal à l’aide d’invites de ligne de commande ou d’un script.
> [!NOTE]
> L’extension CLI est requise pour la fonctionnalité en préversion uniquement.

1. Cliquez sur l’URL suivante pour télécharger la roue Python et l’extension de CLI :  [https://aka.ms/az-aroext-latest.whl](https://aka.ms/az-aroext-latest.whl)
1. Exécutez la commande suivante :
    ```azurecli-interactive
    az extension add --upgrade -s <path to downloaded .whl file>
    ```
1. Vérifiez que l’extension CLI est utilisée :
    ```azurecli-interactive
    az extension list
    [
      {
        "experimental": false,
        "extensionType": "whl",
        "name": "aro",
        "path": "<path may differ depending on system>",
        "preview": true,
        "version": "1.0.1"
      }
    ]
    ```

## <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Créer un réseau virtuel contenant deux sous-réseaux vides
Créez un réseau virtuel contenant deux sous-réseaux vides. Si vous disposez d’un réseau virtuel existant qui répond à vos besoins, vous pouvez ignorer cette étape. Pour passer en revue la procédure de création d’un réseau virtuel, consultez [Créer un réseau virtuel contenant deux sous-réseaux vides](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets).

## <a name="create-an-azure-key-vault-instance"></a>Créer une instance Azure Key Vault
Vous devez utiliser une instance Azure Key Vault pour stocker vos clés. Créez un nouveau coffre de clés avec la protection de purge activée. Créez ensuite une clé dans le coffre de clés pour stocker votre propre clé personnalisée.
1. Définissez des autorisations d’environnement supplémentaires :
    ```
    export KEYVAULT_NAME=$USER-enckv
    export KEYVAULT_KEY_NAME=$USER-key
    export DISK_ENCRYPTION_SET_NAME=$USER-des
    ```
1. Créez un coffre de clés et une clé dans le coffre de clés :
    ```azurecli-interactive
    az keyvault create -n $KEYVAULT_NAME \
                   -g $RESOURCEGROUP \
                   -l $LOCATION \
                   --enable-purge-protection true \
                   --enable-soft-delete true

    az keyvault key create --vault-name $KEYVAULT_NAME \
                           -n $KEYVAULT_KEY_NAME \
                           --protection software

    KEYVAULT_ID=$(az keyvault show --name $KEYVAULT_NAME --query "[id]" -o tsv)

    KEYVAULT_KEY_URL=$(az keyvault key show --vault-name $KEYVAULT_NAME \
                                            --name $KEYVAULT_KEY_NAME \
                                            --query "[key.kid]" -o tsv)
    ```

## <a name="create-an-azure-disk-encryption-set"></a>Créer un jeu de chiffrement de disque Azure
Le jeu de chiffrement de disque Azure est utilisé comme point de référence pour les disques dans des clusters ARO. Il est connecté au coffre de clés Azure que vous avez créé à l’étape précédente, et extrait les clés gérées par le client à partir de cet emplacement.
```azurecli-interactive
az disk-encryption-set create -n $DISK_ENCRYPTION_SET_NAME \
                              -l $LOCATION \
                              -g $RESOURCEGROUP \
                              --source-vault $KEYVAULT_ID \
                              --key-url $KEYVAULT_KEY_URL

DES_ID=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME -g $RESOURCEGROUP --query 'id' -o tsv)

DES_IDENTITY=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME \
                                           -g $RESOURCEGROUP \
                                           --query "[identity.principalId]" \
                                           -o tsv)
```

## <a name="grant-permissions-for-the-disk-encryption-set-to-access-the-key-vault"></a>Accorder des autorisations pour l’ensemble de chiffrement de disque pour accéder au coffre de clés
Utilisez le jeu de chiffrement de disque que vous avez créé à l’étape précédente et accordez l’autorisation pour le jeu de chiffrement de disque pour accéder à Azure Key Vault et l’utiliser.
```azurecli-interactive
az keyvault set-policy -n $KEYVAULT_NAME \
                       -g $RESOURCEGROUP \
                       --object-id $DES_IDENTITY \
                       --key-permissions wrapkey unwrapkey get
```

## <a name="create-an-aro-cluster"></a>Créer un cluster ARO
Créez un cluster ARO pour utiliser les clés gérées par le client.
```azurecli-interactive
az aro create --resource-group $RESOURCEGROUP \
              --name $CLUSTER  \
              --vnet aro-vnet  \
              --master-subnet master-subnet \
              --worker-subnet worker-subnet \
              --disk-encryption-set $DES_ID
```
Après la création du cluster ARO, toutes les machines virtuelles sont chiffrées avec les clés de chiffrement gérées par le client.

Pour vérifier que les clés ont été correctement configurées, exécutez les commandes suivantes :
1. Obtient le nom du groupe de ressources de cluster dans lequel se trouvent les machines virtuelles du cluster, les disques, et ainsi de suite :
    ```azurecli-interactive
    CLUSTERRESOURCEGROUP=$(az aro show --resource-group $RESOURCEGROUP --name $CLUSTER --query 'clusterProfile.resourceGroupId' -o tsv | cut -d '/' -f 5)
    ```
2. Vérifiez que le jeu de chiffrement de disque correct est associé aux disques :
    ```azurecli-interactive
    az disk list -g $CLUSTERRESOURCEGROUP --query '[].encryption'
    ```
    Le champ `diskEncryptionSetId` de la sortie doit pointer vers le jeu de chiffrement de disque que vous avez spécifié lors de la création du cluster ARO.
