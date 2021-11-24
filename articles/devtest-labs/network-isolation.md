---
title: Isolement réseau
description: Découvrir l’isolement réseau dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 555c6b13a46ba6cd70ef136d9a6a4cf88d14c4f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397715"
---
# <a name="network-isolation-in-devtest-labs"></a>Isolement réseau dans DevTest Labs

Un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) agit en tant que limite de sécurité, isolant vos ressources Azure de l’Internet public. Vous pouvez également joindre un réseau virtuel Azure à votre réseau local pour vous connecter en toute sécurité à vos ressources locales. Dans DevTest Labs, vous pouvez choisir d’[isoler toutes les machines virtuelles lab](devtest-lab-configure-vnet.md) et les [environnements sur votre réseau](connect-environment-lab-virtual-network.md) pour vous assurer que les ressources du labo suivent les stratégies de mise en réseau de l’organisation. 

En tant que propriétaire du labo, vous pouvez également choisir d’isoler complètement celui-ci. Vous isolez les machines virtuelles et les environnements sur le réseau sélectionné. Vous pouvez également isoler le compte de stockage Lab et les coffres de clés que vous avez créés dans votre abonnement. Cet article vous guide dans la création d’un labo isolé du réseau. 

Consultez également les articles suivants :

- [Utilisation du compte de stockage du labo par DevTest Labs](encrypt-storage.md)
- [Utilisation des coffres de clés par DevTest Labs](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> L’isolement réseau n’est actuellement pris en charge que pour les créations de nouveaux labos.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Étapes permettant d’activer l’isolement réseau pendant la création du labo

1. Pendant la création du labo, accédez à l’onglet **mise en réseau**.
1. Vous pouvez sélectionner un réseau **par défaut** que le labo créera pour vous, ou sélectionner un réseau existant dans la liste déroulante. Vous ne pourrez sélectionner que les réseaux qui se trouvent dans la même région et le même abonnement que le labo. 

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant la création d’un labo.](./media/network-isolation/create-lab.png)
1. Sélectionner un sous-réseau.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant la création d’un sous-réseau.](./media/network-isolation/create-lab-subnet.png)
1. Si vous choisissez d’isoler le compte de stockage Lab et le coffre de clés sur le réseau par défaut, aucune action supplémentaire n’est nécessaire. Le labo gère désormais l’isolement des ressources.
 
    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’isolement réseau.](./media/network-isolation/isolate-lab-resources.png)
1. Si vous choisissez d’isoler le compte de stockage Lab et le coffre de clés sur un réseau existant que vous avez sélectionné, effectuez les étapes suivantes après la création du labo. Ces étapes permettent de s’assurer que le labo continue à fonctionner en mode isolé. 
 
    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’isolement des ressources.](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Le propriétaire du labo doit effectuer ces étapes avant de configurer ou de créer des ressources dans le labo.

### <a name="steps-to-follow-post-lab-creation"></a>Étapes de la création d’un labo de publication

1. Dans la page d’accueil du labo, sélectionnez le **groupe de ressources** dans la page **Vue d’ensemble**. Vous devez voir la page **Groupe de ressources** pour le groupe de ressources qui contient le labo. 
 
   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant le labo Contoso.](./media/network-isolation/contoso-lab.png)
1. Sélectionnez le compte de stockage Azure du labo. La convention de nommage pour le compte de stockage du labo est la suivante : a\<*labNameWithoutInvalidCharacters*>\<*4-digit number*>. Par exemple, si le nom du labo est contosolab, le nom du compte de stockage peut être acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant le test Contoso.](./media/network-isolation/contoso-test.png)
1. Sur le compte de stockage, accédez à Pare-feux et réseaux virtuels et vérifiez que la case Autoriser les services Microsoft approuvés à accéder à ce compte de stockage est cochée. Étant donné que [DevTest Labs est un service Microsoft approuvé](../storage/common/storage-network-security.md#trusted-microsoft-services), cette option permet au labo de fonctionner normalement en mode isolé du réseau. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant les pare-feu du labo Contoso.](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Ensuite, cliquez sur **+Ajouter un réseau virtuel existant**, sélectionnez le réseau virtuel et le sous-réseau que vous avez sélectionnés lors de la création du labo, puis cliquez sur **Activer**. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant mon réseau virtuel Contoso.](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Une fois que le point de terminaison de service a été activé pour le réseau virtuel sélectionné, cliquez sur **Ajouter**. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant l’ajout.](./media/network-isolation/contoso-firewall-add.png)
 
Avec cela, le stockage Azure autorise des connexions entrantes à partir du réseau virtuel ajouté et permet au labo de fonctionner correctement en mode isolé du réseau. 

Vous pouvez également choisir d’automatiser ces étapes afin de configurer ce paramètre pour plusieurs laboratoires. 

[En savoir plus sur la gestion des règles d’accès réseau par défaut pour le stockage Azure à l’aide de PowerShell et de l’interface CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>Isolement réseau d’un labo existant

En tant que propriétaire, vous pouvez choisir d’isoler le réseau d’un labo existant. [Cet exemple de script](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1) montre comment convertir un labo existant et les ressources associées en mode réseau isolé. 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Points à retenir lors de l’utilisation d’un labo en mode isolé du réseau

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Accès au compte de stockage du labo en dehors du labo 

Le propriétaire du labo doit activer explicitement l’accès au compte de stockage d’un labo isolé du réseau à partir d’un point de terminaison autorisé. Cet accès est nécessaire pour les actions telles que le chargement d’un disque dur virtuel vers le compte de stockage pour la création d’images personnalisées. Pour ce faire, vous pouvez activer l’accès en créant une machine virtuelle et en accédant en toute sécurité au compte de stockage du labo à partir de cette machine virtuelle. 

[En savoir plus sur l’accès privé à un compte de stockage à partir d’une machine virtuelle](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportation des données d’utilisation à partir du labo 

Pour [exporter des données d’utilisation personnelles pour un labo isolé du réseau](personal-data-delete-export.md), le propriétaire du labo doit fournir explicitement un compte de stockage et générer un objet BLOB dans le compte afin de stocker les données. 

Si aucun compte de stockage Lab n’est fourni, cette opération échoue en mode isolé du réseau. Le compte de stockage Lab n’est pas accessible pour que le labo l’utilise si le client ne fournit pas de compte de stockage. 

[En savoir plus sur l’exportation des données d’utilisation de labo dans un compte de stockage spécifié](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Étapes suivantes

[Créer ou modifier des laboratoires automatiquement à l’aide de modèles Azure Resource Manager et PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
