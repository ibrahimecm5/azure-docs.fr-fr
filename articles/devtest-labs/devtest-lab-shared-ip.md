---
title: Comprendre les adresses IP partagées
description: Découvrez comment Azure DevTest Labs utilise les adresses IP partagées afin de limiter le nombre des adresses IP publiques dont vous avez besoin pour accéder aux machines virtuelles de votre laboratoire.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 06aac18fb7016a7eb5bee938a4d9988719f86a2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056909"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendre les adresses IP partagées dans Azure DevTest Labs

Les machines virtuelles Azure DevTest Labs peuvent partager une adresse IP publique afin de réduire le nombre d’adresses IP publiques dont vous avez besoin pour accéder aux machines virtuelles du laboratoire.  Cet article décrit le fonctionnement des adresses IP partagées et explique comment configurer des adresses IP partagées.

## <a name="shared-ip-settings"></a>Paramètres d’adresse IP partagée

Vous créez un laboratoire DevTest Labs dans un réseau virtuel qui peut comprendre un ou plusieurs sous-réseaux. Le sous-réseau par défaut a l’option **Activer l’adresse IP publique partagée** définie sur **Oui**.  Cette configuration crée une adresse IP publique pour le sous-réseau dans son ensemble. Par défaut, toutes les machines virtuelles de ce sous-réseau utilisent l’adresse IP partagée.

Pour plus d’informations sur la configuration des réseaux virtuels et des sous-réseaux, consultez [Configurer un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Capture d’écran montrant le paramètre Adresse IP partagée sur la page Sous-réseau lab.](media/devtest-lab-shared-ip/lab-subnet.png)

Pour les laboratoires existants, vous pouvez contrôler ou définir cette option en sélectionnant **Configuration et stratégies** dans le volet de navigation gauche du laboratoire, puis en sélectionnant **Réseaux virtuels** sous **Ressources externes**. Sélectionnez un réseau virtuel dans la liste afin d’afficher les paramètres d’adresse IP partagée pour ses sous-réseaux.

Pour modifier le paramètre, sélectionnez un sous-réseau dans la liste, puis modifiez le paramétrage de l’option **Activer l’adresse IP publique partagée** de **Oui** en **Non**.

Lorsque vous créez une machine virtuelle, vous pouvez accéder à ce paramètre sur la page **Paramètres avancés** en regard de **Adresse IP**.

![Capture d’écran montrant le paramètre Adresse IP partagée dans Paramètres avancés lors de la création d’une machine virtuelle.](media/devtest-lab-shared-ip/new-vm.png)

- **Partagée :** toutes les machines virtuelles que vous créez comme **partagées** sont placées dans le même groupe de ressources. Le groupe de ressources possède une adresse IP attribuée que toutes les machines virtuelles du groupe de ressources utilisent.
- **Publique :** chaque machine virtuelle publique possède sa propre adresse IP et son propre groupe de ressources.
- **Privée :** chaque machine virtuelle privée utilise une adresse IP privée. Vous ne pouvez pas vous connecter à ces machines virtuelles à partir d’Internet à l’aide du protocole RDP (Remote Desktop Protocol).

Lorsque vous ajoutez une machine virtuelle avec une adresse IP partagée à un sous-réseau, DevTest Labs ajoute automatiquement la machine virtuelle à un équilibreur de charge et affecte à la machine un numéro de port TCP sur l’adresse IP publique. Le numéro de port est transféré vers le port SSH (Secure Shell) sur la machine virtuelle.

## <a name="use-a-shared-ip"></a>Utiliser une adresse IP partagée

- **Utilisateurs Windows :** sélectionnez **Se connecter** sur la page **Vue d’ensemble** de la machine virtuelle afin de télécharger un fichier RDP préconfiguré et d’accéder à la machine.

- **Utilisateurs Linux :** le protocole SSH (Secure Shell) se connecte à la machine virtuelle en utilisant l’adresse IP ou le nom de domaine complet, suivis du signe deux-points et du numéro port. Par exemple, la capture d’écran suivante montre une adresse de connexion SSH de `contosolab21000000000000.westus3.cloudapp.azure.com:65013`.

  ![Capture d’écran montrant les options de connexion RDP et SSH sur une page Vue d’ensemble de machine virtuelle.](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Étapes suivantes

- [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Configuration d’un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md)
