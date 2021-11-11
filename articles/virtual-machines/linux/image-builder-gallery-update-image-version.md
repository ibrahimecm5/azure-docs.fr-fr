---
title: Créer une nouvelle version d’image de machine virtuelle à partir d’une version existante à l’aide du Générateur d’images Azure
description: Créez une nouvelle version de l'image d'une machine virtuelle à partir d'une version existante à l'aide d'Azure VM Image Builder dans Linux.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: c0348e159b14e400e7787da7e1c04de375f96815
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437141"
---
# <a name="create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Créer une nouvelle version d’image de machine virtuelle à partir d’une version existante à l’aide d’Azure VM Image Builder dans Linux

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Cet article explique comment prendre une version existante d’une image dans une [galerie Azure Compute Gallery](../shared-image-galleries.md) (auparavant appelée Shared Image Gallery), la mettre à jour et la publier en tant que nouvelle version dans la gallerie.

Pour configurer l’image, nous allons utiliser un exemple de modèle .json. Le fichier en question se trouve à l’emplacement [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Si vous avez utilisé [Créer une image et la distribuer à une galerie Azure Compute Gallery](image-builder-gallery.md) pour créer votre galerie Azure Compute Gallery, vous avez déjà créé certaines variables dont nous avons besoin. Sinon, veuillez configurer quelques variables pout les utiliser dans cet exemple.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the Azure Compute Gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Créez une variable pour votre ID d’abonnement.

```console
subscriptionID=$(az account show --query id --output tsv)
```

Récupérez la version de l’image à mettre à jour.

```azurecli
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
Pour consulter l’exemple que nous allons utiliser, ouvrez le fichier .json qui se trouve à l’emplacement [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) avec la [référence des modèles du Générateur d’images](image-builder-json.md). 


Téléchargez l’exemple .json et configurez-le avec vos variables. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Création de l’image

Envoyez la configuration de l’image au service Générateur d’images de la machine virtuelle.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Attendez que l’image ait été créée et répliquée pour passer à l’étape suivante.


## <a name="create-the-vm"></a>Création de la machine virtuelle

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Créer une connexion SSH à la machine virtuelle à l’aide de l’adresse IP publique de cette dernière.

```console
ssh azureuser@<pubIp>
```

L’image est personnalisée avec un « Message du jour » dès que la connexion SSH est établie.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tapez `exit` pour fermer la connexion SSH.

Vous pouvez également lister les versions de l’image maintenant disponibles dans votre bibliothèque.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les composants du fichier .json utilisé dans cet article, voir [Référence des modèles du Générateur d’images](../linux/image-builder-json.md).
