---
title: 'Démarrage rapide : Créer une machine virtuelle Intel SGX dans le portail Azure'
description: Commencez vos déploiements en découvrant comment créer rapidement une machine virtuelle Intel SGX dans le portail Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: JenCook
ms.custom: mode-portal, ignite-fall-2021
ms.openlocfilehash: 51a91b6bb5ff5991ad2d92a41f7f70ef39c2a0c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033276"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-portal"></a>Démarrage rapide : Créer une machine virtuelle Intel SGX dans le portail Azure

Ce didacticiel vous guide tout au long du processus de déploiement de machines virtuelles Intel SGX à l’aide du portail Azure. Autrement, nous vous recommandons de suivre les modèles de la [Place de marché Azure](quick-create-marketplace.md).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce didacticiel. Veuillez passer à un abonnement avec paiement à l’utilisation.


## <a name="sign-in-to-azure"></a>Connexion à Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. En haut, sélectionnez **Créer une ressource**.

1. Dans le volet gauche, sélectionnez **Compute**.

1. Sélectionnez **Créer une machine virtuelle**.

    ![Déployer une machine virtuelle](media/quick-create-portal/compute-virtual-machine.png)

## <a name="configure-an-intel-sgx-virtual-machine"></a>Configurer une machine virtuelle Intel SGX

1. Sous l’onglet **De base**, sélectionnez votre **Abonnement** et votre **Groupe de ressources**.

1. Dans **Nom de la machine virtuelle**, indiquez le nom de votre nouvelle machine virtuelle.

1. Tapez ou sélectionnez les valeurs suivantes :

   * **Région** : sélectionnez la région Azure qui vous convient.

        > [!NOTE]
        > Les machines virtuelles Intel SGX s’exécutent sur du matériel spécialisé dans des régions spécifiques. Pour connaître la disponibilité régionale la plus récente, recherchez les séries DCsv2 ou DCsv3/DCdsv3 dans les [régions disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configurez l’image du système d’exploitation à utiliser pour votre machine virtuelle.

    * **Choisir une image** : pour ce didacticiel, sélectionnez Ubuntu 20.04 LTS – Gen 2. Vous pouvez également sélectionner Ubuntu 18.04 LTS – Gen2 ou Windows Server 2019.
    
    * **Mettre à jour vers Génération 2** : sous Image, sélectionnez **Configurer la génération de machine virtuelle**, dans le menu volant, puis **Génération 2**.
    
        ![image](https://user-images.githubusercontent.com/63871188/137009767-421ee49a-ded8-4cfd-ac53-a3d6750880b9.png)


1. Sélectionnez **Changer la taille** pour choisir une machine virtuelle dotée de fonctionnalités Intel SGX dans le sélecteur de taille. Dans le sélecteur de taille de la machine virtuelle, cliquez sur **Effacer tous les filtres**. Choisissez **Ajouter un filtre**, sélectionnez **Famille** comme type de filtre, puis sélectionnez uniquement **Informatique confidentielle**.

    ![Machines virtuelles de la série DCsv2](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Vous devriez voir les tailles **DC(number)s_v2**, **DC(number)s_v3** et **DC(number)ds_v3**. [Plus d’informations](virtual-machine-solutions-sgx.md)

1. Renseignez les informations suivantes :

   * **Type d'authentification** : Sélectionnez **Clé publique SSH** si vous créez une machine virtuelle Linux. 

        > [!NOTE]
        > Vous pouvez choisir d’utiliser une clé publique SSH ou un mot de passe pour l’authentification. L’utilisation d’une clé SSH est plus sécurisée. Pour savoir comment générer une clé SSH, consultez [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nom d’utilisateur** : indiquez le nom d’administrateur pour la machine virtuelle.

    * **Clé publique SSH** : le cas échéant, entrez votre clé publique RSA.
    
    * **Mot de passe** : le cas échéant, entrez votre mot de passe pour l’authentification.

    * **Ports d’entrée publics** : choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **SSH (22)** et **HTTP (80)** dans la liste **Sélectionner les ports d’entrée publics**. Si vous déployez une machine virtuelle Windows, sélectionnez **HTTP (80)** et **RDP (3389)** .  

    >[!Note]
    > Autoriser les ports RDP/SSH n’est pas recommandé pour les déploiements de production.  

     ![Ports entrants](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Apportez les modifications souhaitées sous l’onglet **Disques**.

   * La **série DCsv2** prend en charge **SSD Standard**, **SSD Premium** est pris en charge sur DC1, DC2 et DC4. 
   * Les **séries DCsv3 et DCdsv3** prennent en charge **SSD Standard**, **SSD Premium** et **Disque Ultra**

1. Apportez les modifications souhaitées aux paramètres des onglets suivants ou conservez les paramètres par défaut.

    * **Mise en réseau**
    * **Gestion**
    * **Configuration de l’invité**
    * **Balises**

1. Sélectionnez **Revoir + créer**.

1. Dans le volet **Vérifier + créer**, sélectionnez **Créer**.

> [!NOTE]
> Si vous avez déployé une machine virtuelle Linux, passez à la section suivante de ce tutoriel. Si vous avez déployé une machine virtuelle Windows, [suivez cette procédure pour vous connecter à votre machine virtuelle Windows](../virtual-machines/windows/connect-logon.md), puis [installez le SDK OE sur Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Se connecter à la machine virtuelle Linux

Si vous utilisez déjà un interpréteur de commandes BASH, connectez-vous à la machine virtuelle Azure à l’aide de la commande **SSH**. Dans la commande suivante, remplacez le nom d’utilisateur et l’adresse IP de la machine virtuelle pour vous connecter à votre machine virtuelle Linux.

```bash
ssh azureadmin@40.55.55.555
```

Vous trouverez l’adresse IP publique de votre machine virtuelle dans le portail Azure, sous la section Vue d’ensemble de votre machine virtuelle.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Adresse IP dans le portail Azure":::

Si vous êtes sous Windows et que vous n’avez pas d’interpréteur de commandes BASH, installez un client SSH, tel que PuTTY.

1. [Téléchargez et installez PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Exécutez PuTTY.

1. À l’écran de configuration de PuTTY, entrez l’adresse IP publique de votre machine virtuelle.

1. Sélectionnez **Open** (Ouvrir) et entrez votre nom d’utilisateur et votre mot de passe lorsque vous y êtes invité.

Pour en savoir plus la connexion aux machines virtuelles Linux, consultez [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Si vous voyez une alerte de sécurité PuTTY relative à la clé d’hôte du serveur non mise en cache dans le Registre, choisissez parmi les options suivantes. Si vous faites confiance à cet ordinateur hôte, sélectionnez **Yes** (Oui) pour ajouter la clé à la mémoire cache de PuTTy et poursuivre la connexion. Si vous souhaitez vous connecter une seule fois, sans ajouter la clé dans le cache, sélectionnez **No** (Non). Si vous ne faites pas confiance à cet ordinateur hôte, sélectionnez **Cancel** (Annuler) pour abandonner la connexion.

## <a name="install-azure-dcap-client"></a>Installer un client Azure DCAP

> [!NOTE]
> Trusted Hardware Identity Management (THIM) est un service Azure gratuit qui vous permet de gérer les identités matérielles des différents environnements d’exécution de confiance (TEE). Il récupère la documentation auprès du service de certification d’approvisionnement (PCS) Intel et le met en cache. Le service applique un niveau de base de calcul de confiance (TCB) minimal comme ligne de base de sécurité Azure à des fins d’attestation.

Il est recommandé aux utilisateurs de machines virtuelles Azure des séries DCsv2, DCsv3 et DCdsv3 d’installer un client Azure DCAP afin d’interagir avec THIM et de récupérer la documentation TEE à des fins de génération de Quote, durant le processus d’attestation. Pour en savoir plus sur l’attestation, consultez [Microsoft Azure Attestation](/azure/attestation/overview) ou [Attestation ECDSA](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes ses ressources associées. 

Sélectionnez le groupe de ressources de la machine virtuelle, puis sélectionnez **Supprimer**. Confirmez le nom du groupe de ressources pour terminer la suppression des ressources.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez opéré un déploiement et une connexion à votre machine virtuelle Intel SGX. Pour plus d’informations, consultez [Solutions sur les machines virtuelles](virtual-machine-solutions-sgx.md). 

Découvrez comment créer des applications d’informatique confidentielle en accédant aux exemples du SDK Open Enclave sur GitHub. 

> [!div class="nextstepaction"]
> [Création d’exemples de SDK Open Enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
