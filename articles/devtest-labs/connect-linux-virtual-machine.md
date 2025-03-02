---
title: Se connecter à vos machines virtuelles Linux
description: Découvrez comment vous connecter à votre machine virtuelle Linux dans un labo (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 956c27235dcdf9d57c908ce2e840e3c77518875b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676045"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Connexion à une machine virtuelle Linux dans un labo (Azure DevTest Labs)
Cet article vous explique comment vous connecter à une machine virtuelle Linux dans votre labo. 

## <a name="connect-to-a-linux-vm"></a>Se connecter à une machine virtuelle Linux
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans la barre de recherche, recherchez et sélectionnez **DevTest Lab**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Recherchez et sélectionnez DevTest Labs":::    
1. Dans la liste de labos, sélectionnez votre **labo**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Sélection du labo":::            
1. Sur la page d’accueil de votre labo, sélectionnez votre machine virtuelle Linux dans la liste **Mes machines virtuelles**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Sélection de la machine virtuelle Linux":::        
5. La page **Vue d’ensemble** indique le nom de domaine complet (FQDN) ou l’adresse IP de la machine virtuelle. Le port apparaît également, comme sur l’image suivante.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Nom de domaine complet de la machine virtuelle":::    

    Comme vous pouvez le remarquer, le bouton **Se connecter** est grisé alors que la machine virtuelle est démarrée. Ce comportement est normal.
6.  Utilisez le protocole SSH pour vous connecter à votre machine virtuelle Linux. Dans l’exemple suivant, une connexion est établie avec la machine virtuelle portant le nom FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` avec le nom d’utilisateur `vmuser` et le port `51637`. Entrez le mot de passe permettant à l’utilisateur de se connecter à la machine virtuelle. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Vous pouvez utiliser des outils comme [Putty](https://www.putty.org/) ou n’importe quel autre client SSH pour vous connecter à la machine virtuelle. 

    Une fois la connexion SSH établie, vous pouvez installer et configurer un environnement de bureau ([xfce](https://www.xfce.org)) et le Bureau à distance ([xrdp](http://xrdp.org)).  Pour plus d’informations, consultez [Installation et configuration du Bureau à distance pour se connecter à une machine virtuelle Linux dans Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Étapes suivantes
[Guide pratique pour se connecter à une machine virtuelle Windows](connect-windows-virtual-machine.md)
