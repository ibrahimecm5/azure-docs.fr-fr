---
title: Préparer un disque dur virtuel Linux Debian
description: Découvrez comment créer des images de disque dur virtuel Debian pour un déploiement dans Azure.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: e3d68e704ce91cddfabeb8518a60bba469b7adf7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301195"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Préparer un disque dur virtuel Debian pour Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

## <a name="prerequisites"></a>Conditions préalables requises
Cette section suppose que vous avez déjà installé un système d’exploitation Debian Linux à l’aide d’un fichier .iso téléchargé à partir du [site web Debian](https://www.debian.org/distrib/) sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Hyper-V n’est qu’un exemple parmi d’autres. Pour obtenir des instructions sur l’utilisation de Hyper-V, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Notes d'installation
* Consultez également les [Notes générales d’installation sous Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
* Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de l’applet de commande **convert-vhd** .
* Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. La technique [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) peut être utilisée sur les disques de données, le cas échéant.
* Ne configurez pas une partition d'échange sur le disque du système d'exploitation. Vous pouvez configurer l’agent Linux Azure pour créer un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* Tous les VDH sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. Lors de la conversion d’un disque brut vers VDH, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo avant la conversion. Pour plus d’informations, consultez [Notes d’installation sur Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Utiliser Azure-Manage pour créer des disques durs virtuels Debian
Il existe plusieurs outils permettant de créer des disques durs virtuels Debian pour Azure, par exemple les scripts [azure-manage](https://github.com/credativ/azure-manage) de [Credativ](https://www.credativ.com/). Cette approche est préférable à la création d'une image à partir de zéro. Par exemple, pour créer un disque dur virtuel Debian 8, exécutez les commandes suivantes pour télécharger l’utilitaire `azure-manage` (et les dépendances), puis exécutez le script `azure_build_image` :

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>Préparer une image Debian pour Azure

Vous pouvez créer l’image Azure Debian Cloud de base à l’aide du [générateur d’images de cloud FAI](https://salsa.debian.org/cloud-team/debian-cloud-images).

(Les commandes « git clone » et « apt install » suivantes ont été extraites du référentiel Debian Cloud Images) Commencez par cloner le référentiel et installer les dépendances :

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

(Facultatif) Personnalisez la build en ajoutant des scripts (par exemple, des scripts d’interpréteur de commandes) sous `./config_space/scripts/AZURE`.



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>Voici un exemple de script permettant de personnaliser l’image :

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

Notez qu’il est important de préfixer toutes les commandes que vous souhaitez avoir pour personnaliser l’image avec `$ROOTCMD`, car ce dernier a l’alias `chroot $target`.


## <a name="build-the-azure-debian-10-image"></a>Créez l’image Azure Debian 10 :

```
$ make image_buster_azure_amd64
```


Cela produira une poignée de fichiers dans le répertoire actuel, notamment le fichier image `image_buster_azure_amd64.raw`.

Pour convertir l’image brute en disque dur virtuel pour Azure, vous pouvez procéder comme suit :

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


Cela crée un disque dur virtuel `image_buster_azure_amd64.vhd` avec une taille arrondie pour pouvoir le copier sur un disque Azure.

Nous devons maintenant créer les ressources Azure pour cette image (cette opération utilise la variable `$rounded_size_adjusted`, donc elle doit être comprise dans le même processus d’interpréteur de commandes que ci-dessus).

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> Si la bande passante entre votre ordinateur local et le disque Azure est à l’origine d’un long délai de traitement du chargement avec azcopy, vous pouvez utiliser un serveur de rebond de machine virtuelle Azure pour accélérer le processus. Voici comment procéder :
>
>1. Créez un fichier d’archive tar du disque dur virtuel sur votre ordinateur local : `tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`.
>2. Créez une machine virtuelle Linux Azure (distribution de votre choix). Assurez-vous de la créer avec un disque suffisamment grand pour contenir le disque dur virtuel extrait.
>3. Téléchargez l’utilitaire azcopy sur la machine virtuelle Linux Azure. Il peut être récupéré [ici](../../storage/common/storage-use-azcopy-v10.md#download-azcopy).
>4. Copiez le fichier d’archive tar sur la machine virtuelle : `scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`.
>5. Sur la machine virtuelle, extrayez le disque dur virtuel : `tar -xf ./image_buster_azure_amd64.vhd.tar.gz` (cela prendra un peu de temps en raison de la taille du fichier).
>6. Enfin, sur la machine virtuelle, copiez le disque dur virtuel sur le disque Azure avec `azcopy` (la commande ci-dessus).


**Prochaines étapes :** vous êtes maintenant prêt à utiliser votre disque dur virtuel Debian Linux pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de votre premier chargement du fichier .vhd sur Azure, consultez [Créer une machine virtuelle Linux à partir d’un disque personnalisé](/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd).

