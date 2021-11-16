---
title: Configurer des autorisations de service Azure Image Builder à l’aide d’Azure CLI
description: Configurer les exigences pour le service Azure VM Image Builder, y compris les autorisations et les privilèges à l’aide d’Azure CLI
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 3f20276c8cb1f3b777f3cbb1400559cfe85294bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428251"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Configurer des autorisations de service Azure Image Builder à l’aide d’Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Lorsque vous vous inscrivez à AIB, cela permet au service AIB de créer, de gérer et de supprimer un groupe de ressources intermédiaire (IT_ *), et d’y ajouter des ressources requise pour la génération d’image. Cette opération est effectuée par un nom de principal du service AIB rendu disponible dans votre abonnement lors d’une inscription réussie.

Pour permettre à Azure VM Image Builder de distribuer des images aux images gérées ou à une instance Azure Compute Gallery (anciennement Shared Image Gallery), vous devez créer une identité affectée par l’utilisateur Azure qui dispose des autorisations d’accès en lecture et en écriture aux images. Si vous accédez au stockage Azure, des autorisations sont nécessaires pour lire des conteneurs privés ou publics.

Vous devez configurer les autorisations et les privilèges avant de générer une image. Les sections suivantes détaillent comment configurer les scénarios possibles à l’aide d’Azure CLI.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-azure-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur Azure

Azure Image Builder vous oblige à créer une [identité managée attribuée par l’utilisateur Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Azure Image Builder utilise l’identité managée attribuée par l’utilisateur pour lire et écrire des images et accéder aux comptes de stockage Azure. Vous octroyez l’autorisation d’identité pour effectuer des actions spécifiques dans votre abonnement.

> [!NOTE]
> Auparavant, Azure Image Builder utilisait le nom de principal du service (SPN) d’Azure Image Builder pour octroyer des autorisations aux groupes de ressources d’image. L’utilisation du SPN sera déconseillée. Utilisez une identité managée attribuée par l’utilisateur à la place.

L’exemple suivant montre comment créer une identité managée attribuée par l’utilisateur Azure. Remplacez les paramètres d’espaces réservés pour définir vos variables.

| Paramètre | Description |
|---------|-------------|
| \<Resource group\> | Le groupe de ressources dans lequel créer l’identité managée attribuée par l'utilisateur. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Pour plus d’informations sur les identités attribuées par l’utilisateur Azure, consultez la documentation [Identité managée attribuée Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) sur la création d’une identité.

## <a name="allow-image-builder-to-distribute-images"></a>Autoriser Image Builder à distribuer des images

Pour pouvoir distribuer des images (images managées/Azure Compute Gallery), le service Azure Image Builder doit être autorisé à injecter les images dans ces groupes de ressources. Pour octroyer les autorisations requises, vous devez créer une identité managée attribuée par l’utilisateur et lui octroyer des droits sur le groupe de ressources dans lequel l’image est générée. Azure image Builder ne dispose **pas** d’autorisation d’accès aux ressources d’autres groupes de ressources dans l’abonnement. Vous devez effectuer des actions explicites pour permettre l’accès afin d’éviter l’échec de vos builds.

Vous n’avez pas besoin d’octroyer au contributeur d’identités managées attribuées par l’utilisateur des droits sur le groupe de ressources pour distribuer des images. Toutefois, l’identité managée attribuée par l’utilisateur a besoin des autorisations `Actions` Azure suivantes dans le groupe de ressources de distribution :

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Si vous distribuez les images dans une instance Azure Compute Gallery, il vous faut également les autorisations suivantes :

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Autorisation de personnaliser des images existantes

Pour pouvoir générer des images à partir d’images personnalisées sources (images gérées/Azure Compute Gallery), le service Azure Image Builder doit être autorisé à lire les images dans ces groupes de ressources. Pour octroyer les autorisations requises, vous devez créer une identité managée attribuée par l’utilisateur et lui octroyer des droits sur le groupe de ressources dans lequel se trouve l’image.

Générer à partir d’une image personnalisée existante :

```Actions
Microsoft.Compute/galleries/read
```

Pour générer des images à partir d’une version existante d’Azure Compute Gallery, il vous faut les autorisations suivantes :

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Autorisation de personnaliser des images sur vos réseaux virtuels

Azure Image Builder a la capacité de déployer et d’utiliser un réseau virtuel existant dans votre abonnement, ce qui permet aux personnalisations d’accéder aux ressources connectées.

Vous n’avez pas besoin d’octroyer au contributeur d’identités managées attribuées par l’utilisateur des droits sur le groupe de ressources pour déployer une machine virtuelle sur le réseau virtuel existant. Toutefois, l’identité managée attribuée par l’utilisateur a besoin des autorisations `Actions` Azure suivantes dans le groupe de ressources du réseau virtuel :

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Créer une définition de rôle Azure

Les exemples suivants créent une définition de rôle Azure à partir des actions décrites dans les sections précédentes. Les exemples sont appliqués au niveau du groupe de ressources. Évaluez et testez si les exemples sont suffisamment granulaires pour répondre à vos exigences. Dans votre scénario, vous devrez peut-être les affiner pour une instance Azure Compute Gallery spécifique.

Les actions d’image autorisent la lecture et l’écriture. Décidez des éléments appropriés pour votre environnement. Par exemple, créez un rôle pour permettre à Azure Image Builder de lire des images à partir du groupe de ressources *exemple-rg-1* et d’écrire des images dans le groupe de ressources *exemple-rg-2*.

### <a name="custom-image-azure-role-example"></a>Exemple de rôle d’image personnalisée Azure

L’exemple suivant crée un rôle Azure pour utiliser et distribuer une image personnalisée source. Vous octroyez ensuite le rôle personnalisé à l’identité managée attribuée par l’utilisateur pour Azure Image Builder.

Pour simplifier le remplacement de valeurs dans l’exemple, définissez les variables suivantes en premier. Remplacez les paramètres d’espaces réservés pour définir vos variables.

| Paramètre | Description |
|---------|-------------|
| \<Subscription ID\> | L'identifiant de votre abonnement Azure. |
| \<Resource group\> | Groupes de ressources pour l’image personnalisée |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Exemple de rôle Azure du réseau virtuel existant

L’exemple suivant crée un rôle Azure pour utiliser et distribuer une image du réseau virtuel existant. Vous octroyez ensuite le rôle personnalisé à l’identité managée attribuée par l’utilisateur pour Azure Image Builder.

Pour simplifier le remplacement de valeurs dans l’exemple, définissez les variables suivantes en premier. Remplacez les paramètres d’espaces réservés pour définir vos variables.

| Paramètre | Description |
|---------|-------------|
| \<Subscription ID\> | L'identifiant de votre abonnement Azure. |
| \<Resource group\> | Groupe de ressources du réseau virtuel |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Utilisation de l’identité managée pour l’accès au Stockage Azure

Si vous voulez authentifier de façon transparente avec le Stockage Azur et utiliser des conteneurs privés, Azure Image Builder a besoin d’une identité managée attribuée par l’utilisateur. Azure Image Builder utilise l’identité pour authentifier avec le Stockage Azure.

> [!NOTE]
> Azure Image Builder n’utilise que l’identité au moment de la soumission du modèle d’image. La machine virtuelle de build n’a pas accès à l’identité pendant la génération de l’image.

Utilisez Azure CLI pour créer l’identité managée attribuée par l’utilisateur.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

Dans le modèle Image Builder, vous devez fournir l’identité managée attribuée par l’utilisateur.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Remplacez les paramètres d’espaces réservés suivants :

| Paramètre | Description |
|---------|-------------|
| \<Region\> | Région du modèle |
| \<Resource group\> | Resource group |
| \<Storage account container\> | Nom du conteneur de compte de stockage |
| \<Subscription ID\> | Abonnement Azure |

Pour plus d’informations sur l’utilisation d’une identité managée attribuée par l’utilisateur, consultez la rubrique [Créer une image personnalisée qui utilisera une identité managée attribuée par l’utilisateur Azure pour accéder de façon transparente aux fichiers du Stockage Azure](./image-builder-user-assigned-identity.md). Le Guide de démarrage rapide vous guide dans la création et la configuration de l’identité managée attribuée par l’utilisateur pour accéder à un compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Vue d’ensemble d’Azure VM Image Builder](../image-builder-overview.md).
