---
title: Activer les protections de charge de travail intégrées de Microsoft Defender pour le cloud
description: Découvrez comment activer des fonctionnalités de sécurité renforcée pour étendre les protections de Microsoft Defender pour le cloud à vos ressources hybrides et multiclouds
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 06/07/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 44acbc3ace06af2e741b3daf5c6afe9a50a2efe0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028936"
---
# <a name="quickstart-enable-enhanced-security-features"></a>Démarrage rapide : Activer les fonctionnalités de sécurité renforcée

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Pour en savoir plus sur les avantages des fonctionnalités de sécurité renforcée, consultez [Fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud](enhanced-security-features-overview.md).

## <a name="prerequisites"></a>Prérequis

Dans le cadre des didacticiels et des guides de démarrage rapide de Defender pour le cloud, vous devez activer les fonctionnalités de sécurité renforcée. 

Vous pouvez protéger l’intégralité d’un abonnement Azure avec les fonctionnalités de sécurité renforcée de Defender pour le cloud et les protections seront héritées par toutes les ressources de l’abonnement.

Une version d’évaluation gratuite de 30 jours est disponible. Pour plus d’informations sur la tarification dans la devise de votre choix et en fonction de votre région, consultez la [page sur les tarifs](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-enhanced-security-features-from-the-azure-portal"></a>Activer les fonctionnalités de sécurité renforcée à partir du Portail Azure

Si vous souhaitez activer toutes les fonctionnalités de Defender pour le cloud, notamment les fonctionnalités de protection contre les menaces, vous devez activer les fonctionnalités de sécurité renforcée dans l’abonnement qui contient les charges de travail applicables. L’activation au niveau espace de travail espace de travail n’active pas l’accès juste-à-temps à la machine virtuelle, les contrôles d’application adaptatifs et les détections réseau pour les ressources Azure. De plus, les seuls plans Microsoft Defender disponibles au niveau de l’espace de travail sont Microsoft Defender pour les serveurs et Microsoft Defender pour les serveurs SQL sur les machines.

- Vous pouvez activer **Microsoft Defender pour les comptes de stockage** au niveau de l’abonnement ou de la ressource
- Vous pouvez activer **Microsoft Defender pour SQL** au niveau de l’abonnement ou de la ressource
- Il n’est possible d’activer **Microsoft Defender pour les bases de données relationnelles open source** qu’au niveau de la ressource

### <a name="to-enable-enhanced-security-features-on-your-subscriptions-and-workspaces"></a>Pour activer les fonctionnalités de sécurité renforcée sur vos abonnements et espaces de travail :

- Pour activer les fonctionnalités de sécurité renforcée sur un seul abonnement :

    1. Dans le menu principal de Defender pour le cloud, sélectionnez **Paramètres de l’environnement**.
    1. Sélectionnez l’abonnement ou l’espace de travail que vous souhaitez protéger.
    1. Sélectionnez **Activer tous les plans Microsoft Defender pour cloud** pour les mettre à niveau.
    1. Sélectionnez **Enregistrer**.

    > [!TIP]
    > Vous remarquerez que chaque plan dans Microsoft Defender est facturé séparément et peut être défini individuellement sur Activé ou Désactivé. Par exemple, vous pouvez désactiver Defender pour App Service sur les abonnements auxquels aucun plan Azure App Service n’est associé. 

    :::image type="content" source="./media/enhanced-security-features-overview/pricing-tier-page.png" alt-text="Page de tarification de Defender sur le cloud dans le portail":::

- Pour activer la sécurité renforcée sur plusieurs abonnements ou espaces de travail :

    1. Dans le menu de Defender pour le cloud, sélectionnez **Prise en main**.

        L’onglet **Mise à jour** répertorie les abonnements et les espaces de travail éligibles à l’intégration.

        :::image type="content" source="./media/enable-enhanced-security/get-started-upgrade-tab.png" alt-text="Onglet Mise à niveau de la page Prise en main."::: 

    1. Dans la liste **Sélectionner les abonnements et les espaces de travail à protéger avec Microsoft Defender pour le cloud**, sélectionnez les abonnements et les espaces de travail à mettre à niveau, puis sélectionnez **Mettre à niveau** pour activer toutes les fonctionnalités de sécurité de Microsoft Defender pour le cloud.

       - Si vous sélectionnez des abonnements et des espaces de travail qui ne sont pas éligibles pour la version d’évaluation, l’étape suivante les met à niveau et des frais seront facturés depuis lors.
       - Si vous sélectionnez un espace de travail éligible à un essai gratuit, la prochaine étape activera un essai.

        :::image type="content" source="./media/enable-enhanced-security/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Mettre à niveau tous les espaces de travail et abonnements sélectionnés à partir de la page Prise en main.":::


## <a name="disable-enhanced-security-features"></a>Désactiver les fonctionnalités de sécurité renforcée

Si vous devez désactiver les fonctionnalités de sécurité renforcée pour un abonnement, la procédure est la même, mais vous sélectionnez **Sécurité renforcée désactivée** :
 
1. Dans le menu de Defender pour le cloud, sélectionnez **Paramètres de l’environnement**.
1. Sélectionnez l’abonnement approprié.
1. Sélectionnez **Plans de Defender** et sélectionnez **Sécurité renforcée désactivée**.

    :::image type="content" source="./media/enable-enhanced-security/disable-plans.png" alt-text="Activez ou désactivez les fonctionnalités de sécurité renforcée de Defender pour le cloud.":::

1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Une fois que vous avez désactivé les fonctionnalités de sécurité renforcée (un seul plan ou tous à la fois), il peut arriver que la collecte des données se poursuive pendant une courte durée. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez activé les fonctionnalités de sécurité renforcée, activez la collecte automatique des données par les agents et extensions nécessaires décrits dans [Approvisionnement automatique d’agents et d’extensions](enable-data-collection.md).
