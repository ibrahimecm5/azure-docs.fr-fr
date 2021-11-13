---
title: Créer une nouvelle version d’image Windows à partir d’une version existante à l’aide d’Azure Image Builder
description: Créer une nouvelle version d’image de machine virtuelle Windows à partir d’une version existante à l’aide d’Azure Image Builder.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.collection: windows
ms.openlocfilehash: 2f3c4c302d0b31bbd97eedcd8e83f2cb50a8af2e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462895"
---
# <a name="create-a-new-windows-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Créer une nouvelle version d’image de machine virtuelle Windows à partir d’une version existante à l’aide d’Azure Image Builder

**S’applique à :** :heavy_check_mark : Machines virtuelles Windows

Cet article explique comment prendre une version existante d’une image dans une [galerie Azure Compute Gallery](../shared-image-galleries.md) (auparavant appelée Shared Image Gallery), la mettre à jour et la publier en tant que nouvelle version dans la gallerie.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier .json que nous utilisons ici est : [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 


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

Si vous avez utilisé [Créer une image et la distribuer à une galerie Azure Compute Gallery](image-builder-gallery.md) pour créer votre galerie Azure Compute Gallery, vous avez déjà créé les variables dont nous avons besoin. Sinon, veuillez configurer quelques variables pout les utiliser dans cet exemple.

Azure VM Image Builder ne prend en charge la création d’images personnalisées que dans le même groupe de ressources que l’image managée source. Remplacez le nom du groupe de ressources de cet exemple par celui de votre image managée source.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the Azure Compute Gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Créez une variable pour votre ID d’abonnement.

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```

Récupérez la version de l’image à mettre à jour.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o tsv)
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Créer une identité affectée par l’utilisateur et définir des autorisations sur le groupe de ressources
Comme vous avez défini l’identité de l’utilisateur dans l’exemple précédent, vous devez simplement lui attribuer l’ID de ressource, puis il sera ajouté au modèle.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Si vous avez déjà votre propre galerie Azure Compute Gallery et que vous n’avez pas suivi l’exemple précédent, vous devez affecter à Image Builder les autorisations nécessaires pour accéder au groupe de ressources et donc à la galerie. Passez en revue les étapes de l’exemple [Créer une image et la distribuer à une galerie Azure Compute Gallery](image-builder-gallery.md).


## <a name="modify-helloimage-example"></a>Modifier l’exemple helloImage
Pour consulter l’exemple que nous allons utiliser, ouvrez le fichier .json qui se trouve à l’emplacement [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) avec la [référence des modèles du Générateur d’images](../linux/image-builder-json.md). 


Téléchargez l’exemple .json et configurez-le avec vos variables. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Création de l’image

Envoyez la configuration de l’image au service Générateur d’images de la machine virtuelle.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --location $location \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Attendez que l’image ait été créée et répliquée pour passer à l’étape suivante.


## <a name="create-the-vm"></a>Création de la machine virtuelle

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Vérifier la personnalisation
Créez une connexion Bureau à distance à la machine virtuelle avec le nom d’utilisateur et le mot de passe définis lors de la création de la machine virtuelle. Dans la machine virtuelle, ouvrez une invite de commande et tapez :

```console
dir c:\
```

Vous devriez maintenant voir deux répertoires :
- `buildActions`, qui a été créé dans la première version de l’image.
- `buildActions2`, qui a été créé dans le cadre de la mise à jour de la première version de l’image pour créer la deuxième version de l’image.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les composants du fichier .json utilisé dans cet article, voir [Référence des modèles du Générateur d’images](../linux/image-builder-json.md).
