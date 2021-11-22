---
title: Alertes de sécurité pour les environnements
description: Cet article explique comment afficher des alertes de sécurité pour un environnement dans DevTest Labs, et prendre les mesures nécessaires.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 57f1a0b240035d75746b9d23482446be71d1b1f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398038"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertes de sécurité concernant les environnements dans Azure DevTest Labs
En tant qu’utilisateur du laboratoire, vous pouvez afficher les alertes Microsoft Defender pour le cloud pour vos laboratoires. Defender pour le cloud collecte, analyse et intègre automatiquement les données du journal. Les données proviennent des ressources Azure, du réseau et des solutions connectées, telles que le pare-feu et la protection de point de terminaison. Defender pour le cloud détecte les véritables menaces et réduit le nombre de faux positifs. Defender pour le cloud :

- Répertorie les alertes de sécurité hiérarchisées.
- Fournit des informations pour examiner rapidement les problèmes.
- Recommande le mode de résolution des attaques.

[En savoir plus sur les alertes de sécurité dans Microsoft Defender pour le cloud](../security-center//security-center-alerts-overview.md)


## <a name="prerequisites"></a>Conditions préalables requises
Vous pouvez afficher des alertes de sécurité uniquement pour les environnements PaaS (Platform as a Service) qui sont déployés dans votre lab. Pour tester ou utiliser cette fonctionnalité, [déployez un environnement dans votre laboratoire](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Voir les alertes de sécurité d’un environnement

1. Dans la page d’accueil de votre laboratoire, sélectionnez **Alertes de sécurité** dans le menu de gauche. Le nombre d’alertes de sécurité (de niveau élevé, moyen ou faible) doit s’afficher. Apprenez-en davantage sur [la façon dont les alertes sont classées](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Alertes de sécurité - Vue d’ensemble](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Cliquez avec le bouton droit sur les trois points (...) de la dernière colonne, puis sélectionnez **Voir les alertes de sécurité**. 

    ![Capture d’écran montrant la page Alertes de sécurité avec l’option « Afficher les alertes de sécurité » sélectionnée.](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Vous verrez des informations détaillées sur les alertes et les recommandations Advisor. En savoir plus sur la [gestion des alertes de sécurité et les réponses à apporter dans Microsoft Defender pour le cloud](../security-center/security-center-managing-and-responding-alerts.md)

    ![Afficher les alertes de sécurité](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les environnements, consultez les articles suivants :

- [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurer et utiliser des environnements publics](devtest-lab-configure-use-public-environments.md)
