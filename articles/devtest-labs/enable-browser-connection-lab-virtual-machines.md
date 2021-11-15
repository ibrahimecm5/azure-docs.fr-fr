---
title: Activer la connexion du navigateur sur les machines virtuelles Azure DevTest Labs
description: Intégrez Azure Bastion à DevTest Labs pour permettre l’accès aux machines virtuelles du labo via un navigateur.
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 876a72548c70f3e7717c75973edee802e584fca2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442960"
---
# <a name="enable-browser-connection-to-devtest-labs-vms"></a>Activer la connexion du navigateur aux machines virtuelles DevTest Labs

Azure DevTest Labs s’intègre à [Azure Bastion](../bastion/index.yml) pour permettre la connexion aux machines virtuelles du labo via un navigateur. En tant que propriétaire du labo, vous pouvez activer l’accès par navigateur à toutes les machines virtuelles de votre labo par le biais d’Azure Bastion.

Azure Bastion fournit une connectivité sécurisée et fluide des protocoles RDP (Remote Desktop Protocole) et SSH (Secure Shell) via le protocole TLS (Transport Layer Security), directement à partir du portail Azure. Vous n’avez pas besoin de client, agent ou logiciel supplémentaire pour vous connecter aux machines virtuelles de votre labo par le biais de votre navigateur. Vos machines virtuelles n’ont pas besoin d’adresses IP publiques.

Cet article présente deux méthodes différentes pour activer la connectivité avec le navigateur d’Azure Bastion aux machines virtuelles DevTest Labs :

- Vous pouvez créer un nouveau réseau virtuel Azure Bastion pour votre labo et ses machines virtuelles.
- Vous pouvez déployer Azure Bastion sur votre réseau virtuel de labo existant.

## <a name="prerequisites"></a>Prérequis

- Avoir ou [créer un labo](tutorial-create-custom-lab.md#create-a-lab) dans DevTest Labs.
- Pour utiliser l’accès par le navigateur d’Azure Bastion, les utilisateurs du labo doivent disposer du rôle **Lecteur** sur l’hôte Azure Bastion et sur le réseau virtuel du labo sur lequel Azure Bastion est configuré.

## <a name="option-1-connect-a-lab-to-an-azure-bastion-enabled-virtual-network"></a>Option 1 : connecter un labo à un réseau virtuel avec Azure Bastion

Tout d’abord, créez un réseau virtuel avec un sous-réseau Azure Bastion et contenant un autre sous-réseau. Il n’est pas possible de créer des ressources non Azure Bastion dans un sous-réseau Azure Bastion. Vous avez donc besoin de l’autre sous-réseau pour créer des machines virtuelles de labo.

1. Dans le portail Azure, recherchez et sélectionnez **Réseaux virtuels**.
1. Sélectionnez **+ Créer** en haut de la page **Réseaux virtuels**.
1. Sur l’écran **Créer un réseau virtuel**, entrez un **Nom** pour le nouveau réseau virtuel, puis sélectionnez les mêmes valeurs pour **Abonnement**, **Groupe de ressources** et **Région** que sur votre labo.
1. Sélectionnez **Suivant : Adresses IP**.
1. Dans l’onglet **Adresses IP**, il existe déjà un sous-réseau, **par défaut**. Sélectionnez **Ajouter un sous-réseau**.
1. Dans le volet **Ajouter un sous-réseau**, entrez *AzureBastionSubnet* sous **Nom**.
1. Sous **Plage d’adresses de sous-réseau**, entrez une plage d’adresses comprise dans l’espace d’adressage du réseau virtuel mais qui ne chevauche pas le sous-réseau par défaut. Si nécessaire, vous pouvez ajouter de nouveaux espaces d’adressage dans les champs vides sur la page **Créer un réseau virtuel**.
1. Sélectionnez **Ajouter**.

   ![Capture d’écran montrant la création du sous-réseau AzureBastionSubnet.](media/enable-browser-connection-lab-virtual-machines/create-subnet.png)

1. Sélectionnez **Vérifier + créer**, puis quand la validation réussit, sélectionnez **Créer**.
1. Une fois le nouveau réseau virtuel créé, accédez à sa page, sélectionnez **Sous-réseaux** dans la barre de navigation gauche, puis vérifiez qu’il y a deux sous-réseaux, **par défaut** et **AzureBastionSubnet**.

   ![Capture d’écran montrant deux sous-réseaux dans le réseau virtuel Azure Bastion.](media/enable-browser-connection-lab-virtual-machines/second-subnet.png)

Connectez ensuite votre labo au nouveau réseau virtuel :

1. Dans la page **Vue d’ensemble** de votre labo, sélectionnez **Configuration et stratégies** dans la barre de navigation de gauche.
1. Sur la page **Configuration et stratégies**, sélectionnez **Réseaux virtuels** sous **Ressources externes** dans le volet de navigation gauche.
1. Dans la page **Configuration et stratégies | Réseaux virtuels**, sélectionnez **Ajouter** en haut.
1. Dans la page **Réseau virtuel**, sélectionnez **Sélectionner un réseau virtuel**.
1. Dans la page **Choisir un réseau virtuel**, sélectionnez le réseau virtuel avec Azure Bastion que vous venez de créer.
1. Sélectionnez **Enregistrer** en haut de la page **Réseau virtuel**.
1. Dans la page **Configuration et stratégies | Réseaux virtuels**, supprimez tout réseau virtuel précédent du labo. Sélectionnez **...** à côté de ce réseau virtuel, sélectionnez **Supprimer**, puis sélectionnez **Oui**. 

   ![Capture d’écran montrant la suppression de l’ancien réseau virtuel du labo.](media/enable-browser-connection-lab-virtual-machines/add-virtual-network.png)

Activer la création de machines virtuelles dans le sous-réseau non Azure Bastion :

1. Dans la page **Configuration et stratégies | Réseaux virtuels**, sélectionnez le réseau virtuel avec Azure Bastion.
1. Dans la page **Réseau virtuel**, assurez-vous que le sous-réseau **AzureBastionSubnet** et le sous-réseau **par défaut** s’affichent. Si vous ne voyez pas les deux sous-réseaux, fermez et rouvrez la page.
1. Sélectionnez le sous-réseau **par défaut**.
1. Dans l’écran **Sous-réseau de labo**, sous **Utiliser pour la création de la machine virtuelle**, sélectionnez **Oui**, puis **Enregistrer**. Vous pouvez maintenant créer des machines virtuelles dans le sous-réseau par défaut de votre réseau virtuel de labo.

   ![Capture d’écran montrant l’activation de la création de machine virtuelle dans le sous-réseau par défaut.](./media/enable-browser-connection-lab-virtual-machines/enable-vm-creation-subnet.png)

## <a name="option-2-deploy-azure-bastion-in-a-labs-existing-virtual-network"></a>Option 2 : déployer Azure Bastion dans le réseau virtuel existant d’un labo

Créez tout d’abord un sous-réseau Azure Bastion dans le réseau virtuel existant de votre labo :

1. Dans le portail Azure, recherchez et sélectionnez **Réseaux virtuels**.
1. Sélectionnez le réseau virtuel existant de votre labo dans la liste de la page **Réseaux virtuels**.
1. Sur la page Réseau virtuel, sélectionnez **Sous-réseaux** dans la barre de navigation gauche,
1. Dans la page **Sous-réseaux**, sélectionnez **+ Sous-réseau** dans le menu du haut.
1. Dans l’écran **Ajouter un sous-réseau**, entrez *AzureBastionSubnet* sous **Nom**.
1. Sous **Plage d’adresses de sous-réseau**, entrez une plage d’adresses comprise dans l’espace d’adressage du réseau virtuel mais qui ne chevauche pas le sous-réseau existant.
   >[!TIP]
   >Vous devrez peut-être annuler cette boîte de dialogue, sélectionner **Espace d’adressage** dans la barre de navigation gauche du réseau virtuel et créer un espace d’adressage pour le sous-réseau.
1. Sélectionnez **Enregistrer**.

   ![Capture d’écran montrant l’ajout d’un sous-réseau dans le réseau virtuel existant.](./media/enable-browser-connection-lab-virtual-machines/add-subnet.png)

Déployez ensuite l’hôte Azure Bastion dans le nouveau sous-réseau Azure Bastion :

1. Dans le portail Azure, recherchez ou sélectionnez **Bastions**.
1. Sur la page **Bastion**, sélectionnez **+ Créer** en haut.
1. Dans la page **Créer un bastion**, entrez un **Nom** et sélectionnez les mêmes valeurs pour **Abonnement**, **Groupe de ressources** et **Région** que sur votre labo.
1. Sous **Réseaux virtuels**, sélectionnez le réseau virtuel de votre labo dans la liste déroulante et assurez-vous que **AzureBastionSubnet** est sélectionné sous **Sous-réseau**.
1. Sélectionnez **Vérifier + créer**, puis quand la validation réussit, sélectionnez **Créer**.

   ![Capture d’écran montrant le déploiement Azure Bastion dans le réseau virtuel existant.](./media/enable-browser-connection-lab-virtual-machines/create-bastion.png)

## <a name="connect-to-lab-vms-through-azure-bastion"></a>Connecter à des machines virtuelles du labo via Azure Bastion

Une fois que vous avez déployé Azure Bastion dans le réseau virtuel de votre labo, activez les connexions du navigateur pour le labo :

1. Sur la page **Vue d’ensemble** du labo, sélectionnez **Configuration et stratégies**, puis sélectionnez **Connexion par navigateur** sous **Paramètres**.
1. Sur la page **Connexion par navigateur**, sélectionnez **Activé**.
1. En haut de la page, sélectionnez **Enregistrer**.

   ![Capture d’écran montrant l’activation de la connexion par navigateur.](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

Pour vous connecter à une machine virtuelle de labo par le biais d’Azure Bastion :

1. Sélectionnez une machine virtuelle de labo dans les **Mes machines virtuelles** sur la page **Vue d’ensemble** du labo.
1. En haut de la page de la machine virtuelle, sélectionnez **Connexion par navigateur**.
1. Dans le volet **Connexion par navigateur**, entrez le nom d’utilisateur et le mot de passe de votre machine virtuelle, puis sélectionnez **Connecter**.

## <a name="next-steps"></a>Étapes suivantes
- [Qu’est-ce qu’Azure Bastion ?](../bastion/bastion-overview.md)
- [Ajout d’une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md)
