---
title: Tableau de bord de conformité réglementaire dans Microsoft Defender pour le cloud
description: Découvrez comment ajouter des normes réglementaires au tableau de bord de conformité réglementaire dans Defender pour le cloud et comment les supprimer.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5d54fe73c417f3d79c518ec1e78bade71135cfb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037381"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personnaliser l’ensemble de normes du tableau de bord de conformité réglementaire

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender pour le cloud compare continuellement la configuration de vos ressources avec les exigences des normes, réglementations et tests d’évaluation du secteur. Le **tableau de bord de conformité réglementaire** fournit des analyses sur votre situation de conformité selon la façon dont vous répondez aux exigences de conformité.

> [!TIP]
> Pour en savoir plus sur le tableau de bord de conformité réglementaire de Defender pour le cloud, consultez la [FAQ](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-defender-for-cloud"></a>Comment les normes de conformité réglementaire sont-elles représentées dans Defender pour le cloud ?

Les normes du secteur d’activité, les normes réglementaires et les points de référence sont représentés dans le tableau de bord de conformité réglementaire de Defender pour le cloud. Chaque norme correspond à une initiative définie dans Azure Policy.

Pour voir la correspondance entre les données de conformité et les évaluations dans votre tableau de bord, ajoutez une norme de conformité à votre groupe d’administration ou à votre abonnement sur la page **Stratégie de sécurité**. Pour en savoir plus sur Azure Policy et les initiatives, consultez [Utilisation des stratégies de sécurité](tutorial-security-policy.md).

Une fois que vous avez attribué une norme ou un point de référence à l’étendue sélectionnée, la norme s’affiche dans votre tableau de bord de conformité réglementaire avec toutes les données de conformité associées en tant qu’évaluations. Vous pouvez également télécharger des rapports de synthèse pour toutes les normes qui ont été attribuées.

Microsoft suit les normes réglementaires et améliore automatiquement sa couverture dans certains packages au fur et à mesure. Lorsque Microsoft publie du nouveau contenu pour l’initiative, il s’affiche automatiquement dans votre tableau de bord en tant que nouvelles stratégies mappées aux contrôles de la norme.


## <a name="what-regulatory-compliance-standards-are-available-in-defender-for-cloud"></a>Quelles sont les normes de conformité réglementaire disponibles dans Defender pour le cloud ?

Par défaut, le **Benchmark de sécurité Azure** est attribué à chaque abonnement. Il s’agit de l’ensemble des directives propres à Azure et créées par Microsoft contenant les meilleures pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. [En savoir plus sur le Benchmark de sécurité Azure](/security/benchmark/azure/introduction).

Vous pouvez également ajouter des normes telles que les suivantes :

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official et UK NHS
- PBMM fédéral du Canada
- Azure CIS 1.3.0
- CMMC niveau 3
- New Zealand ISM Restricted

Les normes sont ajoutées au tableau de bord dès qu’elles sont disponibles.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Ajouter une norme réglementaire au tableau de bord

La procédure suivante permet d’ajouter un package pour surveiller la conformité avec l’une des normes réglementaires prises en charge.

### <a name="prerequisites"></a>Prérequis
Pour ajouter des normes à votre tableau de bord :

- Les fonctionnalités de sécurité renforcée de Defender pour le cloud doivent être activées pour l’abonnement
- L’utilisateur doit disposer d’autorisations de propriétaire ou de contributeur de stratégie

### <a name="add-a-standard"></a>Ajouter une norme

1. Dans le menu de Defender pour le cloud, sélectionnez **Conformité réglementaire** pour ouvrir le tableau de bord de conformité réglementaire. Ici, vous pouvez voir les normes de conformité actuellement attribuées aux abonnements actuellement sélectionnés.   

1. En haut de la page, sélectionnez **Gérer les stratégies de conformité**. La page Gestion des stratégies s’affiche.

1. Sélectionnez l’abonnement ou le groupe d’administration pour lequel vous souhaitez gérer la situation de conformité réglementaire. 

    > [!TIP]
    > Nous vous recommandons de sélectionner l’étendue la plus élevée pour laquelle la norme est applicable afin que les données de conformité soient agrégées et suivies pour toutes les ressources imbriquées. 

1. Pour ajouter les standards pertinents pour votre organisation, développez la section **normes industrielles et règlementaires** et sélectionnez **Ajouter d’autres standards**.

1. Sur la page **Ajouter des normes de conformité réglementaire**, vous pouvez rechercher les normes disponibles, notamment :

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO et UK NHS**
    - **PBMM fédéral du Canada**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC niveau 3**
    - **New Zealand ISM Restricted**
    
    ![Ajout de normes réglementaires au tableau de bord de conformité réglementaire de Defender pour le cloud.](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Sélectionnez **Ajouter** et entrez tous les détails nécessaires pour l’initiative spécifique, comme l’étendue, les paramètres et la correction.

1. Dans le menu de Defender pour le cloud, sélectionnez à nouveau **Conformité réglementaire** pour revenir au tableau de bord de conformité réglementaire.

    Votre nouvelle norme s’affiche à présent dans la liste des normes réglementaires et des standards du secteur. 

    > [!NOTE]
    > L’affichage d’une norme nouvellement ajoutée dans le tableau de bord de conformité peut prendre quelques heures.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Tableau de bord de conformité réglementaire." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Supprimer une norme de votre tableau de bord

Si certaines des normes réglementaires fournies ne sont pas pertinentes pour votre organisation, il est facile de les supprimer de l’interface utilisateur. Cela vous permet de personnaliser davantage le tableau de bord de conformité réglementaire et de vous concentrer uniquement sur les normes qui vous sont applicables.

Pour supprimer une norme :

1. Dans le menu de Defender pour le cloud, sélectionnez **Stratégie de sécurité**.

1. Sélectionnez l’abonnement approprié à partir duquel vous souhaitez supprimer une norme.

    > [!NOTE]
    > Vous pouvez supprimer une norme d’un abonnement, mais pas d’un groupe d’administration. 

    La page Stratégie de sécurité s’ouvre. Pour l’abonnement sélectionné, il affiche la stratégie par défaut, le secteur et les normes réglementaires, ainsi que les initiatives personnalisées que vous avez créées.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Supprimer une norme réglementaire de votre tableau de bord de conformité réglementaire dans Microsoft Defender pour le cloud.":::

1. Pour la norme que vous souhaitez supprimer, sélectionnez **Désactiver**. Une fenêtre de confirmation s’ouvre.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirmez que vous voulez vraiment supprimer la norme réglementaire que vous avez sélectionnée":::.

1. Sélectionnez **Oui**. La norme est supprimée. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment **ajouter des normes de conformité** pour surveiller votre conformité avec des normes réglementaires et d’autres du secteur d’activité.

Pour obtenir des informations connexes, consultez les pages suivantes :

- [Benchmark de sécurité Azure](/security/benchmark/azure/introduction)
- [Tableau de bord de conformité réglementaire de Defender pour le cloud](regulatory-compliance-dashboard.md) : découvrez comment suivre et exporter vos données de conformité réglementaire avec Defender pour le cloud et des outils externes
- [Utilisation de stratégies de sécurité](tutorial-security-policy.md)
