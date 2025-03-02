---
title: Créer une image de machine virtuelle et utiliser une identité managée affectée par l’utilisateur pour accéder aux fichiers dans le stockage Azure
description: Créez l’image de machine virtuelle à l’aide du générateur d’images Azure, qui peut accéder aux fichiers stockés dans le stockage Azure à l’aide d’identités managées affectées par l’utilisateur.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: f26c60bb4c15ea04acc6b966c0e1af8eec0aeabb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001349"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Créer une image et utiliser une identité managée affectée par l’utilisateur pour accéder aux fichiers dans le stockage Azure 

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Le générateur d’images Azure prend en charge l’utilisation de scripts ou la copie de fichiers à partir de plusieurs emplacements, tels que GitHub et le stockage Azure etc. Pour utiliser ces modèles, ceux-ci doivent avoir été accessibles en externe au générateur d’images Azure.

Cet article montre comment créer une image personnalisée à l’aide d’Azure VM Image Builder, où le service utilise une [identité managée attribuée par l’utilisateur](../../active-directory/managed-identities-azure-resources/overview.md) pour accéder aux fichiers dans le stockage Azure pour la personnalisation de l’image, sans avoir à rendre les fichiers publiquement accessibles.

Dans l’exemple ci-dessous, vous créez deux groupes de ressources, dont l’un est utilisé pour l’image personnalisée et l’autre héberge un compte de stockage Azure contenant un fichier de script. Cela simule un scénario réel, où vous pouvez avoir des artefacts de build, ou des fichiers image dans différents comptes de stockage, en dehors du générateur d’images. Vous allez créer une identité affectée par l’utilisateur, puis accorder ces autorisations de lecture sur le fichier de script. Toutefois, vous ne définirez pas d’accès public à ce fichier. Vous allez ensuite utiliser l’outil de personnalisation de l’interpréteur de commandes pour télécharger et exécuter ce script à partir du compte de stockage.


## <a name="register-the-features"></a>Inscrire les fonctionnalités
Pour utiliser le Générateur d’images Azure, vous devez inscrire la fonctionnalité.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Vérifiez l’état d’inscription de la fonctionnalité.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

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


## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Nous allons utiliser certains éléments d’information à plusieurs reprises, donc nous allons créer des variables pour les stocker.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Créez une variable pour votre ID d’abonnement.

```console
subscriptionID=$(az account show --query id --output tsv)
```

Créez les groupes de ressources pour le stockage de script et d’images.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Créez une identité affectée par l’utilisateur et définir des autorisations sur le groupe de ressources.

Image Builder utilise l’[identité de l’utilisateur](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fourni pour injecter l’image dans le groupe de ressources. Dans cet exemple, vous allez créer une définition de rôle Azure qui dispose des actions granulaires pour distribuer l’image. La définition de rôle sera ensuite affectée à l’identité de l’utilisateur.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Créez le stockage et copiez-y l’exemple de script à partir de GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Accordez au générateur d’images l’autorisation de créer des ressources dans le groupe de ressources d’image. La valeur `--assignee` est l’ID de l’identité de l’utilisateur.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Modifier l’exemple

Téléchargez l’exemple de fichier .json et configurez-le avec les variables que vous avez créées.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Création de l’image

Envoyez la configuration de l’image au service Générateur d’images Azure.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Lancez la génération de l’image.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Attendez la fin de la génération. Cela peut durer environ 15 minutes.

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle à partir de l’image. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Une fois la machine virtuelle créée, démarrez une session SSH avec la machine virtuelle.

```console
ssh aibuser@<publicIp>
```

Vous devriez voir que l’image a été personnalisée avec un Message du jour dès que votre connexion SSH est établie !

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Nettoyer

Lorsque vous avez terminé, vous pouvez supprimer les ressources si elles ne sont plus nécessaires.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des problèmes d’utilisation de générateur d’images Azure, consultez [Résolution des problèmes](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).
