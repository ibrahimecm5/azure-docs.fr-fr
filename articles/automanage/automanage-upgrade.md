---
title: Mettre à niveau vos machines Azure Automanage avec la dernière version d’Automanage
description: Découvrez comment mettre à niveau vos machines vers la dernière version d’Azure Automanage
author: mmccrory
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: memccror
ms.openlocfilehash: d7cc53c30ead02a2ca4fc40fe00ae2235016336f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479187"
---
# <a name="upgrade-your-machines-to-the-latest-automanage-version"></a>Mettre à niveau vos machines vers la dernière version d’Automanage

En novembre 2021, Automanage a sorti une nouvelle version de l’offre sur les meilleures pratiques. La nouvelle API prend désormais en charge la création de profils personnalisés dans lesquels vous pouvez choisir les services et les paramètres que vous souhaitez appliquer à vos machines. En outre, avec cette nouvelle version, le compte Automanage n’est plus nécessaire. Cet article décrit les différences entre les versions et comment effectuer une mise à niveau. 

## <a name="how-to-upgrade-your-machines"></a>Procédure de mise à niveau de vos machines

Vous trouverez ci-dessous un ensemble d’instructions sur la façon de mettre à niveau vos machines vers la dernière version d’API d’Automanage. 

### <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Veuillez passer à un abonnement avec paiement à l’utilisation.

> [!IMPORTANT]
> L’autorisation de RBAC Azure suivante est nécessaire pour activer Automanage pour la première fois sur un abonnement avec la nouvelle version d’Automanage : rôles **Propriétaire** ou **Contributeur**, ainsi que **Administrateur de l’accès utilisateur**.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).


### <a name="check-to-see-which-machines-need-to-be-upgraded"></a>Vérifier les machines qui doivent être mises à niveau

L’état **Nécessite une mise à niveau** sera affiché sur toutes les machines qui doivent être mises à niveau. Vous verrez également une bannière sur la page Vue d’ensemble d’Automanage, indiquant que vous devez mettre à niveau vos machines. 

:::image type="content" source="media\automanage-upgrade\overview-blade.png" alt-text="État Nécessite une mise à niveau.":::

### <a name="disable-automanage-machines-that-need-to-be-upgrade"></a>Désactiver les machines Automanage qui doivent être mises à niveau

Pour qu’une machine puisse être mise à niveau vers la nouvelle version d’Automanage, la machine doit être désactivée à partir de la version précédente d’Automanage. Pour désactiver les machines, procédez comme suit :
1. Cochez la case des machines virtuelles que vous souhaitez désactiver.
1. Cliquez sur le bouton **Désactiver**.
1. Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.

:::image type="content" source="media\automanage-upgrade\disable-automanage.png" alt-text="Désactiver Automanage.":::

### <a name="re-enable-automanage-on-your-machines"></a>Réactiver Automanage sur vos machines

Une fois vos machines déconnectées d’Automanage, vous pouvez à présent réactiver Automanage. Lorsque vous réactivez Automanage, Automanage utilise automatiquement la dernière version d’Automanage. 

1. Sélectionnez l’option **Activer sur la machine virtuelle existante**.

    :::image type="content" source="media\automanage-upgrade\zero-vm-list-view.png" alt-text="Activation sur la machine virtuelle existante":::

2. Sous **Profil de configuration**, sélectionnez votre type de profil : **Meilleures pratiques Azure – Production**, **Meilleures pratiques Azure – Dev/Test** ou [**Profil personnalisé**](virtual-machines-custom-profile.md)

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Sélectionnez les environnements.":::

    > [!NOTE]
    > L’environnement **Production** est mappé au profil de configuration **Meilleures pratiques Azure – Production**. L’environnement **Dev/Test** est mappé au profil de configuration **Meilleures pratiques Azure – Dev/Test**. Si vous avez tiré parti des **Préférences de configuration**, vous pouvez créer un **Profil personnalisé** avec ces mêmes modifications. 

3. Dans le panneau **Sélectionner les machines** :
    1. Filtrez la liste sur votre **abonnement** et votre **groupe de ressources**.
    1. Cochez la case des machines virtuelles que vous souhaitez intégrer.
    1. Cliquez sur le bouton **Sélectionner**.
    > [!NOTE]
    > Vous pouvez sélectionner des machines virtuelles Azure et des serveurs avec Azure Arc.

    :::image type="content" source="media\automanage-upgrade\existing-vm-select-machine.png" alt-text="Sélectionnez une machine virtuelle existante dans la liste des machines virtuelles disponibles.":::

4. Cliquez sur le bouton **Activer**.

À présent, vos machines seront intégrées à la dernière version d’Automanage.

## <a name="differences-in-the-automanage-versions"></a>Différences entre les versions d’Automanage

### <a name="environment-and-configuration-profiles"></a>Environnement et profils de configuration
Dans la version précédente d’Automanage, vous avez sélectionné votre type d’environnement : Dev/Test ou Production. Dans la nouvelle version d’Automanage, l’environnement est mappé aux profils de configuration. Les options de profils de configuration sont Meilleures pratiques Azure - Dev/Test, Meilleures pratiques Azure - Production, Profil personnalisé. L’ensemble des services et des paramètres de l’environnement **Dev/Test** est le même dans le profil de configuration **Meilleures pratiques Azure - Dev/Test**. De même, l’ensemble des services et des paramètres de l’environnement **Production** est le même dans le profil de configuration **Meilleures pratiques Azure - Production**. 

### <a name="configuration-preferences-and-custom-profiles"></a>Préférences de configuration et profils personnalisés
Dans la version précédente d’Automanage, vous avez pu personnaliser un sous-ensemble de paramètres par le biais des **Préférences de configuration**. Dans la dernière version d’Automanage, nous avons amélioré la personnalisation pour que vous puissiez choisir chaque service à intégrer et prendre en charge la modification de certains paramètres sur les services via des **Profils personnalisés**. 

### <a name="automanage-account-and-first-party-application"></a>Compte Automanage et application de première partie
Dans la version précédente d’Automanage, le compte Automanage était utilisé comme MSI pour effectuer des actions sur votre machine. Toutefois, dans la version la plus récente d’Automanage, Automanage utilise une application de première partie (ID d’application : d828acde-4B48-47F5-a6e8-52460104a052) pour effectuer des actions sur les machines Automanage. 

Pour la version précédente et la nouvelle version d’Automanage, vous devez disposer des autorisations suivantes :
* Si vous intégrez Automanage dans un abonnement pour la première fois, vous avez besoin des rôles **Propriétaire**, ou **Contributeur** ainsi que **Administrateur de l’accès utilisateur**.
* Si vous intégrez Automanage sur un abonnement qui a déjà des machines Automanage, vous avez besoin du rôle **Contributeur** sur le groupe de ressources où se trouve la machine. 
> [!NOTE]
> Si la machine que vous intégrez à Automanage est déjà connectée à un espace de travail Log Analytics dans un abonnement différent de celui de la machine, vous avez également besoin des autorisations décrites ci-dessus sur l’abonnement à l’espace de travail Log Analytics.

## <a name="next-steps"></a>Étapes suivantes 

Pour obtenir les réponses aux questions les plus fréquemment posées, consultez notre FAQ. 

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ)](faq.yml)

