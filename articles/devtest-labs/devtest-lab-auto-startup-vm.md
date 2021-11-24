---
title: Configurer les paramètres de démarrage automatique d’une machine virtuelle
description: Découvrez comment configurer les paramètres de démarrage automatique des machines virtuelles dans un laboratoire. Ce paramètre permet aux machines virtuelles du laboratoire de démarrer automatiquement selon une planification.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: ea4220af8a3d7ade997b9e64b7164757edfc165a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286713"
---
# <a name="start-up-lab-virtual-machines-automatically"></a>Démarrer automatiquement des machines virtuelles de laboratoire

Le démarrage automatique vous permet de démarrer automatiquement les machines virtuelles d’un laboratoire à une heure planifiée chaque jour. Vous devez d’abord créer une stratégie de démarrage automatique. Ensuite, vous devez sélectionner les machines virtuelles qui doivent suivre la stratégie. L’étape supplémentaire qui vise à confirmer la sélection des machines virtuelles à démarrer automatiquement a pour but d’empêcher tout démarrage involontaire de machines virtuelles qui entraînent une augmentation des coûts.

Cet article explique comment configurer une stratégie de démarrage automatique pour un laboratoire. Pour plus d’informations sur la configuration des paramètres d’arrêt automatique, consultez [Gérer les stratégies d’arrêt automatique d’un laboratoire dans Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

## <a name="configure-auto-start-settings-for-a-lab"></a>Configurer les paramètres de démarrage automatique pour un laboratoire 

La stratégie n’applique pas automatiquement le démarrage automatique aux machines virtuelles du laboratoire. Après avoir configuré la stratégie, suivez les étapes de la section [Activer le démarrage automatique d’une machine virtuelle du laboratoire](#enable-auto start-for-a-vm-in-the-lab).

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre lab dans **DevTest Labs**.

1. Sous **Paramètres**, sélectionnez **Configuration et stratégies**. 

   :::image type="content" source="./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png" alt-text="Capture d’écran du menu « Configuration et stratégies » dans DevTest Labs.":::

1. Dans la page **Configuration et stratégies**, sous **Planifications**, sélectionnez **Démarrage automatique**.

1. Pour **Autoriser le démarrage automatique**, sélectionnez **Oui**. Les informations de planification apparaissent.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/portal-lab-auto-start.png" alt-text="Capture d’écran de l’option Démarrage automatique sous Planifications.":::
 
1. Fournissez les informations de planification suivantes :

    |Propriété | Description |
    |---|---|
    |Début planifié| Entrez une heure de début.|
    |Fuseau horaire| Sélectionnez un fuseau horaire dans la liste déroulante.|
    |Jours de la semaine| Cochez chaque case en regard des jours auxquels appliquer la planification.|

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/auto-start-configuration.png" alt-text="Capture d’écran des paramètres de planification du démarrage automatique.":::

1. Sélectionnez **Enregistrer**. 

## <a name="enable-auto-start-for-a-vm-in-the-lab"></a>Activer le démarrage automatique d’une machine virtuelle du laboratoire

Ces étapes font suite à la section précédente. Maintenant qu’une stratégie de démarrage automatique a été créée, sélectionnez les machines virtuelles auxquelles appliquer cette stratégie.

1. Fermez la page **Configuration et stratégies** pour revenir à la page **DevTest Labs**.

1. Sous **Mes machines virtuelles**, sélectionnez une machine virtuelle.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-vm.png" alt-text="Capture d’écran de la sélection d’une machine virtuelle dans la liste située sous Mes machines virtuelles.":::

1. Dans la page de la **machine virtuelle**, sous **Opérations**, sélectionnez **Démarrage automatique**. 

1. Dans la page **Démarrage automatique**, sélectionnez **Oui**, puis **Enregistrer**.

    :::image type="content" source="./media/devtest-lab-auto-startup-vm/select-auto-start.png" alt-text="Capture d’écran du menu de sélection du démarrage automatique.":::

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les stratégies d’arrêt automatique d’un laboratoire dans Azure DevTest Labs](devtest-lab-auto-shutdown.md)
