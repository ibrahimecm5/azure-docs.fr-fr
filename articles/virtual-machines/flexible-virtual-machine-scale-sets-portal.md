---
title: Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portail Azure
description: Découvrez comment créer un groupe de machines virtuelles identiques en mode d’orchestration Flexible dans le portail Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/25/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3f6b0512d97b78111be5d88d20bf7800ebe9937c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008500"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portail Azure

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles

Cet article décrit l’utilisation du portail Azure pour créer un groupe identique de machines virtuelles mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez  [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur https://portal.azure.com.


## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Vous pouvez déployer un groupe identique avec une image Windows Server ou une image Linux, comme RHEL, CentOS, Ubuntu ou SLES.

1. Dans la barre de recherche du Portail Azure, recherchez et sélectionnez **Groupes de machines virtuelles identiques**.
1. Sélectionnez **Créer** dans la page **Groupes de machines virtuelles identiques**.

1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement correct est sélectionné, puis sélectionnez *myVMSSResourceGroup* dans la liste des groupes de ressources.  
1. Sous **Détails du groupe identique**, définissez *myScaleSet* pour le nom de votre groupe identique et sélectionnez une **Région** proche de votre zone.
1. Sous **Orchestration**, sélectionnez l’option *Flexible* pour le **Mode d’orchestration**. 
1. Sous **Détails d’instances**, sélectionnez une image de la place de marché pour **Image**. Dans cet exemple, nous avons choisi *Ubuntu Server 18.04 LTS*.
1. Entrez le nom d’utilisateur de votre choix, puis sélectionnez le type d’authentification que vous préférez.
   - Un **mot de passe** doit comporter au moins 12 caractères, avec au moins trois des quatre caractères suivants : une minuscule, une majuscule, un chiffre et un caractère spécial. Pour plus d’informations, consultez les [critères de nom d’utilisateur et de mot de passe](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
   - Si vous sélectionnez une image de disque du système d’exploitation Linux, vous pouvez choisir à la place **Clé publique SSH**. Entrez uniquement votre clé publique, comme *~/.ssh/id_rsa.pub*. Vous pouvez utiliser Azure Cloud Shell à partir du portail pour [créer et utiliser des clés SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Sélectionnez **Suivant** pour passer à la page suivante. 

1. Laissez les valeurs par défaut pour les pages **Disques**.

1. Sélectionnez **Suivant** pour passer à la page suivante. 

1. Dans la page **Mise en réseau**, sous **Équilibrage de charge**, cochez la case **Utiliser un équilibreur de charge** pour placer les instances du groupe identique derrière un équilibreur de charge. 
1. Pour **Options d’équilibrage de charge**, sélectionnez **Équilibreur de charge Azure**.
1. Dans **Sélectionner un équilibreur de charge**, sélectionnez un équilibreur de charge ou créez-en un nouveau.
1. Sous **Sélectionner un pool principal**, sélectionnez **Créer**, puis tapez *myBackendPool* et sélectionnez **Créer**.

    > [!NOTE]
    > Pour plus d’informations sur la mise en réseau pour les groupes identiques flexibles, consultez [connectivité réseau évolutive pour les groupes identiques flexibles](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).

1. Sélectionnez **Suivant** pour passer à la page suivante.

1. Sur la page **Mise à l’échelle**, définissez le champ **nombre d’instances initiales** sur *5*. Vous pouvez définir ce nombre jusqu’à 1 000. 
1. Pour la **stratégie de mise à l’échelle**, conservez-la *Manuelle*. 

1. Quand vous avez terminé, sélectionnez **Vérifier + créer**. 
1. Après la validation, sélectionnez **Créer** pour déployer le groupe identique.


## <a name="clean-up-resources"></a>Nettoyer les ressources
Quand vous n’en avez plus besoin, supprimez le groupe de ressources, le groupe identique et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources pour le groupe identique, puis sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment créer un groupe identique Flexible avec l’interface CLI Azure.](flexible-virtual-machine-scale-sets-cli.md)