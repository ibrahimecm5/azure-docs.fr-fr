---
title: Utiliser Azure Image Builder et Azure Compute Gallery pour les machines virtuelles Linux
description: Découvrez comment utiliser Azure image Builder et Azure CLI pour créer une version d’image dans une galerie Azure Compute Gallery, puis distribuer l’image globalement.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 8dd2e8068e21c5ee7ce4dea1b54d8f36df2e661b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437160"
---
# <a name="create-a-linux-image-and-distribute-it-to-an-azure-compute-gallery"></a>Créer une image Linux et la distribuer à une galerie Azure Compute Gallery

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles

Cet article explique comment utiliser Azure Image Builder et Azure CLI pour créer une version d’image dans une galerie [Azure Compute Gallery](../shared-image-galleries.md) (anciennement Shared Image Gallery) avant de distribuer l’image globalement. Vous pouvez également faire cela avec [Azure PowerShell](../windows/image-builder-gallery.md).


Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier en question se trouve à l’emplacement [helloImageTemplateforSIG.json](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Pour distribuer l’image à une galerie Azure Compute Gallery, le modèle utilise [sharedImage](image-builder-json.md#distribute-sharedimage) en tant que valeur de la section `distribute` du modèle.


## <a name="register-the-features"></a>Inscrire les fonctionnalités

Pour utiliser le Générateur d’images Azure, vous devez inscrire la fonctionnalité.

Vérifiez votre inscription.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Si elle n’est pas inscrite, exécutez la commande suivante :

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```

## <a name="set-variables-and-permissions"></a>Définir des variables et des autorisations 

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour les stocker.

Image Builder ne prend en charge la création d’images personnalisées que dans le même groupe de ressources que l’image managée source. Remplacez le nom du groupe de ressources de cet exemple par celui de votre image managée source.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the Azure Compute Gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Créez une variable pour votre ID d’abonnement.

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```

Créez le groupe de ressources.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Créer une identité affectée par l’utilisateur et définir des autorisations sur le groupe de ressources
Image Builder utilise l’[identité de l’utilisateur](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fournie pour injecter l’image dans la galerie Azure Compute Gallery. Dans cet exemple, vous allez créer une définition de rôle Azure qui dispose des actions granulaires pour distribuer l’image à la galerie. La définition de rôle sera ensuite affectée à l’identité de l’utilisateur.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Créer une définition d’image et une galerie

Pour utiliser Image Builder avec une galerie Azure Compute Gallery, vous devez disposer d’une galerie et d’une définition d’image existantes. Image Builder ne va pas créer la galerie et la définition d’image pour vous.

Si vous n’avez pas encore de définition d’image et de galerie à utiliser, commencez par les créer. Commencez par créer une galerie.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ensuite, créez une définition d’image.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Télécharger et configurer le fichier .json

Téléchargez le modèle .json et configurez-le avec vos variables.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Créer la version de l’image

Cette section a pour objectif de créer la version de l’image dans la galerie. 

Envoyez la configuration de l’image au service Générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

La création de l’image et sa réplication dans les deux régions peuvent prendre un certain temps. Attendez la fin de cette partie pour passer à la création d’une machine virtuelle.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez une machine virtuelle à partir de la version de l’image créée par le Générateur d’images Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Connectez-vous avec SSH à la machine virtuelle.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

L’image est personnalisée avec un *Message du jour* dès que la connexion SSH est établie.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez maintenant essayer de personnaliser à nouveau la version de l’image pour créer une nouvelle version de la même image, ignorez les étapes suivantes et passez à [Utiliser le Générateur d’images Azure pour créer une autre version de l’image](image-builder-gallery-update-image-version.md).


L’image créée ainsi que tous les autres fichiers de ressources seront ainsi supprimés. Terminez ce déploiement avant de supprimer les ressources.

Lors de la suppression de ressources de la galerie, il est nécessaire de supprimer toutes les versions de l’image pour pouvoir supprimer la définition de l’image utilisée pour les créer. Supprimer une galerie implique de supprimer au préalable toutes les définitions de l’image qu’elle comporte.

Supprimez le modèle du Générateur d’images.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Supprimer des affectations d’autorisations, des rôles et des identités
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Récupérez la version de l’image créée par le Générateur d’images, qui commence toujours par `0.`, puis supprimez la version de l’image

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Supprimez la définition de l’image.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Supprimez la galerie.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Supprimez le groupe de ressources.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [galeries Azure Compute Gallery](../shared-image-galleries.md).
