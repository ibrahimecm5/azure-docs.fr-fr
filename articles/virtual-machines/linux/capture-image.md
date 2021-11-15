---
title: Capturer une image managée d’une machine virtuelle Linux avec Azure CLI
description: Capturez une image managée d’une machine virtuelle Azure et utilisez-la pour les déploiements de masse à l’aide d’Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 08/27/2021
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 0aae67dbe347c8299e00d741163370d9097c8114
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432849"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Créer une image managée d’une machine virtuelle ou d’un disque dur virtuel

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Pour créer plusieurs copies d’une machine virtuelle à utiliser dans Azure à des fins de développement et de test, capturez une image managée de la machine virtuelle ou du disque dur virtuel de système d’exploitation. Pour créer, stocker et partager des images à grande échelle, consultez [Azure Compute Gallery](../create-gallery.md).

Une image managée prend en charge jusqu’à 20 déploiements simultanés. Une tentative de création simultanée de plus de 20 machines virtuelles à partir de la même image managée peut entraîner l’expiration des délais d’approvisionnement en raison des limites de performances de stockage d’un disque dur virtuel unique. Pour créer plus de 20 machines virtuelles simultanément, utilisez une image de [Azure Compute Gallery](../shared-image-galleries.md) (anciennement Shared Image Gallery) configurée avec 1 réplica tous les 20 déploiements simultanés de machines virtuelles.

Pour créer une image managée, vous devez supprimer les informations personnelles du compte. Les étapes suivantes vous permettent de déprovisionner une machine virtuelle existante, de la désallouer et de créer une image. Vous pouvez utiliser cette image pour créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement.

Pour créer une copie de votre machine virtuelle Linux actuelle à des fins de sauvegarde ou de débogage, ou pour charger un disque dur virtuel Linux à partir d’une machine virtuelle locale, consultez [Charger et créer une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md).  

Pour générer votre image personnalisée, vous pouvez utiliser le service **Générateur d’images de machine virtuelle Azure**. À cette fin, vous n’avez pas besoin d’apprendre à utiliser des outils ou de configurer des pipelines de build. Vous fournissez une configuration d’image et le Générateur crée l’image. Pour en savoir plus, voir [Aperçu : Vue d’ensemble du Générateur d’images Azure](../image-builder-overview.md).

Vous avez besoin des éléments suivants avant de créer une image :

* Une machine virtuelle Azure créée dans le modèle de déploiement Resource Manager qui utilise des disques managés. Si vous n’avez pas encore créé de machine virtuelle Linux, vous pouvez utiliser le [portail](quick-create-portal.md), l’interface [Azure CLI](quick-create-cli.md) ou les [modèles Resource Manager](create-ssh-secured-vm-from-template.md). Configurez la machine virtuelle en fonction de vos besoins. Par exemple, [ajoutez des disques de données](add-disk.md), appliquez des mises à jour et installez des applications. 

* La dernière version d’[Azure CLI](/cli/azure/install-az-cli2) et être connecté à un compte Azure avec [az login](/cli/azure/reference-index#az_login).

## <a name="prefer-a-tutorial-instead"></a>Vous préférez un didacticiel à la place ?

Pour une version simplifiée de cet article et pour tester, évaluer ou découvrir les machines virtuelles dans Azure, consultez [Créer une image personnalisée d’une machine virtuelle Azure à l’aide de l’interface CLI](tutorial-custom-images.md).  Sinon, continuez à lire pour obtenir une image complète.


## <a name="step-1-deprovision-the-vm"></a>Étape 1 : Annuler l’approvisionnement de la machine virtuelle
Commencez par déprovisionner la machine virtuelle à l’aide de l’agent de machine virtuelle Azure pour supprimer les fichiers et les données propres à la machine. Utilisez la commande `waagent` avec le paramètre `-deprovision+user` sur votre machine virtuelle Linux source. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../extensions/agent-linux.md). Ce processus est irréversible.

1. Connectez-vous à votre machine virtuelle Linux avec un client SSH.
2. Dans la fenêtre SSH, entrez la commande suivante :
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Exécutez uniquement cette commande sur une machine virtuelle que vous allez capturer en tant qu’image. Cette commande ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution. Le paramètre `+user` supprime également le dernier compte d’utilisateur provisionné. Pour garder les informations d’identification du compte d’utilisateur dans la machine virtuelle, utilisez uniquement `-deprovision`.
 
3. Tapez **Y** pour continuer. Vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.
4. Une fois la commande exécutée, entrez **exit** pour fermer le client SSH.  La machine virtuelle est toujours en cours d’exécution à ce stade.

## <a name="step-2-create-vm-image"></a>Étape 2 : Créer une image de machine virtuelle
Utilisez Azure CLI pour marquer la machine virtuelle comme étant généralisée et capturer l’image. Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myVnet* et *myVM*.

1. Libérez la machine virtuelle dont vous avez annulé le déploiement à l’aide de la commande [az vm deallocate](/cli/azure/vm). L’exemple suivant désalloue la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Attendez que la machine virtuelle soit complètement désallouée avant de poursuivre. L’exécution de cette opération nécessite quelques minutes.  La machine virtuelle est arrêtée pendant la désallocation.

2. Définissez l’état de la machine virtuelle sur généralisé avec [az vm generalize](/cli/azure/vm). L’exemple suivant marque comme généralisée la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup*.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Une machine virtuelle qui a été généralisée ne peut plus être redémarrée.

3. Créez une image de la ressource de machine virtuelle à l’aide de la commande [az image create](/cli/azure/image#az_image_create). L’exemple suivant crée une image nommée *myImage* dans le groupe de ressources nommé *myResourceGroup* à l’aide de la ressource de machine virtuelle nommée *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
    --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L’image est créée dans le même groupe de ressources que votre machine virtuelle source. Vous pouvez créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement à partir de cette image. En matière de gestion, peut-être voudrez-vous créer un groupe de ressources spécifique pour vos ressources de machine virtuelle et vos images.
   >
   > Si vous capturez l’image d’une machine virtuelle de 2e génération, utilisez également le paramètre `--hyper-v-generation V2`. Pour plus d’informations, consultez [Machines virtuelles de 2e génération](../generation-2.md).
   > 
   > Si vous souhaitez stocker votre image dans le stockage résilient aux zones, vous devez la créer dans une région qui prend en charge des [zones de disponibilité](../../availability-zones/az-overview.md) et inclut le paramètre `--zone-resilient true`.
   
Cette commande retourne du code JSON qui décrit l’image de machine virtuelle. Enregistrez cette sortie pour référence ultérieure.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : Créer une machine virtuelle à partir de l’image capturée
Créez une machine virtuelle en utilisant l’image que vous avez créée à l’aide de la commande [az vm create](/cli/azure/vm). L’exemple suivant crée une machine virtuelle nommée *myVMDeployed* à partir de l’image nommée *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Création de la machine virtuelle dans un autre groupe de ressources 

Vous pouvez créer des machines virtuelles à partir d’une image dans n’importe quel groupe de ressources de votre abonnement. Pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image, indiquez l’ID de ressource complet de votre image. Utilisez la commande [az image list](/cli/azure/image#az_image_list) pour afficher une liste d’images. Le résultat ressemble à l’exemple qui suit.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L’exemple suivant utilise la commande [az vm create](/cli/azure/vm#az_vm_create) pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image source en spécifiant l’ID de ressource d’image.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Étape 4 : Vérifier le déploiement

Exécutez le SSH dans la machine virtuelle que vous avez créée pour vérifier le déploiement, puis lancez le déploiement et le démarrage de l’utilisation de la nouvelle machine virtuelle. Pour vous connecter via le protocole SSH, recherchez l’adresse IP ou le nom FQDN de votre machine virtuelle à l’aide de la commande [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Étapes suivantes
Pour créer, stocker et partager des images à grande échelle, consultez [Azure Compute Gallery](../create-gallery.md).
