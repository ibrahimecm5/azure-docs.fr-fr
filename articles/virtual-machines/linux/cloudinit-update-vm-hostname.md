---
title: Utiliser cloud-init pour définir le nom d’hôte d’une machine virtuelle Linux
description: Guide pratique d’utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création avec l’interface Azure CLI
author: mimckitt
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: mimckitt
ms.subservice: cloud-init
ms.openlocfilehash: b3944edfae6fe1fa98ee7adc0ed20575fe0deedc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696197"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Utiliser cloud-init pour définir le nom d’hôte d’une machine virtuelle Linux dans Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Cet article montre comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour configurer un nom d’hôte spécifique sur une machine virtuelle ou un groupe de machines virtuelles identiques au moment du provisionnement dans Azure. Ces scripts cloud-init s’exécutent au premier démarrage une fois que les ressources ont été approvisionnées par Azure. Pour plus d’informations sur le fonctionnement de cloud-init en mode natif dans Azure et sur les versions de Linux prises en charge, consultez [Présentation de cloud-init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Définir le nom d’hôte avec cloud-init
Par défaut, le nom d’hôte est identique au nom de la machine virtuelle quand vous créez une machine virtuelle dans Azure.  Pour exécuter un script cloud-init afin de changer ce nom d’hôte quand vous créez une machine virtuelle dans Azure avec [az vm create](/cli/azure/vm), spécifiez le fichier cloud-init avec le commutateur `--custom-data`.  

Pour voir le processus de mise à niveau en action, créez dans l’interpréteur de commandes actif un fichier nommé *cloud_init_hostname.txt* et collez-y la configuration suivante. Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloud_init_hostname.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne.  

```yaml
#cloud-config
fqdn: myhostname
```

Avant de déployer cette image, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_hostname.txt` comme suit :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Une fois créée, Azure CLI affiche des informations sur la machine virtuelle. Utilisez `publicIpAddress` pour établir une connexion SSH à votre machine virtuelle. Entrez votre propre adresse comme suit :

```bash
ssh <publicIpAddress>
```

Pour afficher le nom de la machine virtuelle, utilisez la commande `hostname` comme suit :

```bash
hostname
```

La machine virtuelle doit signaler le nom d’hôte comme la valeur définie dans le fichier cloud-init, comme indiqué dans l’exemple de sortie suivant :

```bash
myhostname
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init supplémentaires de modifications de configuration, consultez les rubriques suivantes :
 
- [Ajouter un utilisateur Linux supplémentaire à une machine virtuelle](cloudinit-add-user.md)
- [Exécuter un gestionnaire de package pour mettre à jour les packages existants au premier démarrage](cloudinit-update-vm.md)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Installer un package d’application, mettre à jour des fichiers de configuration et injecter des clés](tutorial-automate-vm-deployment.md)
