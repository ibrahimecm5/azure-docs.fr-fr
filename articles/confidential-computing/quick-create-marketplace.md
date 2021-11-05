---
title: 'Démarrage rapide : Créer une machine virtuelle Intel SGX dans la Place de marché Azure'
description: Commencez vos déploiements en découvrant comment créer rapidement une machine virtuelle Intel SGX dans la Place de marché Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 43a6e24ac887336854b59f747233b1cd355f4686
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077854"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-marketplace"></a>Démarrage rapide : Créer une machine virtuelle Intel SGX dans la Place de marché Azure

Ce didacticiel vous guide tout au long du processus de déploiement de machines virtuelles Intel SGX à l’aide de la Place de marché Azure. Dans le cas contraire, nous vous recommandons d’utiliser le [portail ou l’interface CLI](quick-create-portal.md) pour suivre le flux de déploiement de machine virtuelle.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce didacticiel. Veuillez passer à un abonnement avec paiement à l’utilisation.


## <a name="sign-in-to-azure"></a>Connexion à Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. En haut, sélectionnez **Créer une ressource**.

1. Dans le volet **Prise en main** par défaut, recherchez **Informatique confidentielle Azure (machine virtuelle)** .

1. Cliquez sur le modèle **Informatique confidentielle Azure (machine virtuelle)** .

    ![Déployer une machine virtuelle](media/quick-create-marketplace/portal-search-marketplace.png)

1. Dans la page d’arrivée Machine virtuelle, sélectionnez **Créer**.


## <a name="configure-an-intel-sgx-virtual-machine"></a>Configurer une machine virtuelle Intel SGX

1. Dans l’onglet **Informations de base**, sélectionnez votre **Abonnement** et votre **Groupe de ressources** (le groupe doit être vide pour déployer ce modèle).

1. Dans **Nom de la machine virtuelle**, indiquez le nom de votre nouvelle machine virtuelle.

1. Tapez ou sélectionnez les valeurs suivantes :

   * **Région** : sélectionnez la région Azure qui vous convient.

        > [!NOTE]
        > Les machines virtuelles Intel SGX s’exécutent sur du matériel spécialisé dans des régions spécifiques. Pour connaître la disponibilité régionale la plus récente, recherchez les séries DCsv2 ou DCsv3/DCdsv3 dans les [régions disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configurez l’image du système d’exploitation à utiliser pour votre machine virtuelle. Cette configuration prend uniquement en charge les déploiements d’images et de machines virtuelles Gen 2.

    * **Choisir une image** : pour ce tutoriel, sélectionnez Ubuntu 20.04 LTS (Gen 2). Vous pouvez également sélectionner Windows Server Datacenter 2019 et/ou Ubuntu 18.04 LTS. Dans ce cas, vous serez redirigé en conséquence dans ce tutoriel.
   
1. Sous l’onglet Informations de base, renseignez ce qui suit :

   * **Type d'authentification** : Sélectionnez **Clé publique SSH** si vous créez une machine virtuelle Linux. 

        > [!NOTE]
        > Vous pouvez choisir d’utiliser une clé publique SSH ou un mot de passe pour l’authentification. L’utilisation d’une clé SSH est plus sécurisée. Pour savoir comment générer une clé SSH, consultez [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nom d’utilisateur** : indiquez le nom d’administrateur pour la machine virtuelle.

    * **Clé publique SSH** : le cas échéant, entrez votre clé publique RSA.
    
    * **Mot de passe** : le cas échéant, entrez votre mot de passe pour l’authentification.
    
1. Sous l’onglet « Paramètres de machine virtuelle », renseignez ce qui suit :

   * Choisir la taille de référence (SKU) de machine virtuelle
   * La **série DCsv2** prend en charge **SSD Standard**, **SSD Premium** est pris en charge sur DC1, DC2 et DC4. 
   * Les **séries DCsv3 et DCdsv3** prennent en charge **SSD Standard**, **SSD Premium** et **Disque Ultra**
   
   * **Ports d’entrée publics** : choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** et **HTTP (80)** dans la liste **Sélectionner les ports d’entrée publics**. Si vous déployez une machine virtuelle Windows, sélectionnez **HTTP (80)** et **RDP (3389)** . Dans ce démarrage rapide, cette étape est nécessaire pour se connecter à la machine virtuelle.
   
    >[!Note]
    > Autoriser les ports RDP/SSH n’est pas recommandé pour les déploiements de production.  

     ![Ports entrants](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Choisissez l’option **Surveillance**, si nécessaire.

1. Sélectionnez **Revoir + créer**.

1. Dans le volet **Vérifier + créer**, sélectionnez **Créer**.

> [!NOTE]
> Si vous avez déployé une machine virtuelle Linux, passez à la section suivante de ce tutoriel. Si vous avez déployé une machine virtuelle Windows, [suivez cette procédure pour vous connecter à votre machine virtuelle Windows](../virtual-machines/windows/connect-logon.md).


## <a name="connect-to-the-linux-vm"></a>Se connecter à la machine virtuelle Linux

Si vous utilisez déjà un interpréteur de commandes BASH, connectez-vous à la machine virtuelle Azure à l’aide de la commande **SSH**. Dans la commande suivante, remplacez le nom d’utilisateur et l’adresse IP de la machine virtuelle pour vous connecter à votre machine virtuelle Linux.

```bash
ssh azureadmin@40.55.55.555
```

Vous trouverez l’adresse IP publique de votre machine virtuelle dans le portail Azure, sous la section Vue d’ensemble de votre machine virtuelle.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Adresse IP dans le portail Azure":::

Si vous êtes sous Windows et que vous n’avez pas d’interpréteur de commandes BASH, installez un client SSH, tel que PuTTY.

1. [Téléchargez et installez PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Exécutez PuTTY.

1. À l’écran de configuration de PuTTY, entrez l’adresse IP publique de votre machine virtuelle.

1. Sélectionnez **Open** (Ouvrir) et entrez votre nom d’utilisateur et votre mot de passe lorsque vous y êtes invité.

Pour en savoir plus la connexion aux machines virtuelles Linux, consultez [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Si vous voyez une alerte de sécurité PuTTY relative à la clé d’hôte du serveur non mise en cache dans le Registre, choisissez parmi les options suivantes. Si vous faites confiance à cet ordinateur hôte, sélectionnez **Yes** (Oui) pour ajouter la clé à la mémoire cache de PuTTy et poursuivre la connexion. Si vous souhaitez vous connecter une seule fois, sans ajouter la clé dans le cache, sélectionnez **No** (Non). Si vous ne faites pas confiance à cet ordinateur hôte, sélectionnez **Cancel** (Annuler) pour abandonner la connexion.

## <a name="intel-sgx-drivers"></a>Pilotes Intel SGX

> [!NOTE]
> Les pilotes Intel SGX font déjà partie des images de la galerie Azure Ubuntu et Windows. Pour vous assurer que vous utilisez les pilotes les plus récents, consultez la [liste des pilotes Intel SGX DCAP](https://01.org/intel-software-guard-extensions/downloads).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. 

Sélectionnez le groupe de ressources de la machine virtuelle, puis sélectionnez **Supprimer**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez opéré un déploiement et une connexion à votre machine virtuelle Intel SGX. Pour plus d’informations, consultez [Solutions sur les machines virtuelles](virtual-machine-solutions-sgx.md). 

Découvrez comment créer des applications d’informatique confidentielle en accédant aux exemples du SDK Open Enclave sur GitHub. 

> [!div class="nextstepaction"]
> [Création d’exemples de SDK Open Enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
