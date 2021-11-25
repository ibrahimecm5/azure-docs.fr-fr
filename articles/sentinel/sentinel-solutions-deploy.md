---
title: Découvrir et déployer de manière centralisée du contenu et des solutions Microsoft Sentinel prêts à l’emploi
description: Cet article montre comment les clients peuvent facilement rechercher et déployer des outils d’analyse de données empaquetés avec des connecteurs de données et d’autres contenus.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4a6fe8c8867cf1619f24bccd65cf736b2a5c85e5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522389"
---
# <a name="centrally-discover-and-deploy-microsoft-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Découvrir et déployer de manière centralisée du contenu et des solutions Microsoft Sentinel prêts à l’emploi (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Les solutions Microsoft Sentinel et le hub de contenu Microsoft Sentinel sont en **préversion**, tout comme les packages de solutions individuels. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le hub de contenu Microsoft Sentinel fournit un accès au contenu et aux solutions Microsoft Sentinel prêts à l’emploi (intégrés), qui sont empaquetés avec du contenu répondant à des besoins de produits, de domaines ou de secteurs de bout en bout.

Cet article explique comment installer des solutions dans votre espace de travail Microsoft Sentinel, afin que vous puissiez exploiter leur contenu.

- Trouvez vos solutions dans le hub de contenu en fonction de leurs états, du contenu inclus, du support et bien plus encore.

- Installez la solution dans votre espace de travail quand vous en trouvez une qui répond aux besoins de votre organisation. Veillez à la mettre à jour avec les dernières modifications.

> [!TIP]
> Si vous êtes un partenaire qui souhaite créer sa propre solution, consultez le [Guide de génération de solutions Microsoft Sentinel](https://aka.ms/sentinelsolutionsbuildguide) pour la création et la publication de solutions.
>
## <a name="find-a-solution"></a>Trouver une solution

1. Dans le menu de navigation Microsoft Sentinel, sous **Gestion du contenu**, sélectionnez **Hub de contenu (préversion)** .

1. La page **Hub de contenu** affiche une grille de solutions pouvant faire l’objet de recherches et pouvant être filtrée.

    Filtrez la liste affichée, soit en sélectionnant des valeurs spécifiques dans les filtres, soit en entrant une partie du nom ou de la description d’une solution dans le champ de **recherche**.

    Pour plus d’informations, consultez [Catégories des solutions et du contenu prêt à l’emploi de Microsoft Sentinel](sentinel-solutions.md#microsoft-sentinel-out-of-the-box-content-and-solution-categories).

    > [!TIP]
    > Si vous avez déployé une solution et qu’elle a, par la suite, fait l’objet de mises à jour, un triangle orange indique que vous avez des mises à jour à déployer, information reprise dans le triangle bleu en haut de la page.
    >

Chaque solution dans la grille affiche les catégories qui lui sont appliquées ainsi que les types de contenu qu’elle inclut.

Par exemple, dans l’image suivante, la solution **Cisco Umbrella** présente une catégorie **Sécurité - Autres**, et cette solution comprend 10 règles d’analytique, 11 requêtes de chasse, un analyseur, trois playbooks et bien plus encore.

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Capture d’écran de l’hub de contenu de Microsoft Sentinel." lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>Installer ou mettre à jour une solution

1. Dans le hub de contenu, sélectionnez une solution pour afficher plus d’informations à droite. Ensuite, sélectionnez **Installer** ou **Mettre à jour**, si vous avez besoin de mises à jour. Par exemple :

1. Dans la page des détails de la solution, sélectionnez **Créer** ou **Mettre à jour** pour démarrer l’Assistant Solution. Sous l’onglet **Bases** de l’Assistant, entrez l’abonnement, le groupe de ressources et l’espace de travail dans lequel vous souhaitez déployer la solution. Par exemple :

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="Capture d’écran de l’Assistant Installation de la solution, avec l’onglet Bases.":::

1. Sélectionnez **Suivant** pour parcourir les autres onglets (correspondant aux composants inclus dans la solution), où vous pouvez en savoir plus sur et, dans certains cas, configurer, chacun des composants de contenu.

    > [!NOTE]
    > Les onglets affichés correspondent au contenu proposé par la solution. Différentes solutions pouvant avoir différents types de contenu, vous ne verrez peut-être pas les mêmes onglets dans chaque solution.
    >
    > Vous pouvez également être invité à entrer des informations d’identification associées à un service tiers afin que Microsoft Sentinel puisse s’authentifier auprès de vos systèmes. Par exemple, avec des playbooks, vous souhaiterez peut-être effectuer des actions de réponse comme prévu dans votre système.
    >

1. Enfin, sous l’onglet **Vérifier + créer**, attendez que le message `Validation Passed` s’affiche, puis sélectionnez **Créer** ou **Mettre à jour** pour déployer la solution. Vous pouvez également sélectionner le lien **Télécharger un modèle d’automatisation** afin d’obtenir un lien pour déployer la solution en tant que code.

Pour plus d’informations, consultez [Catalogue du hub de contenu Microsoft Sentinel](sentinel-solutions-catalog.md) et [Rechercher votre connecteur de données Microsoft Sentinel](data-connectors-reference.md).

## <a name="find-the-support-model-for-your-solution"></a>Rechercher le modèle de support pour votre solution

Le volet d’informations de chaque solution liste des détails sur le modèle de support de celle-ci ; dans la zone **Support**, **Microsoft** ou le nom d’un partenaire est listé. Par exemple :

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="Capture d’écran de l’endroit où vous pouvez trouver le modèle de support pour votre solution." lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

Quand vous contactez le support, vous pouvez avoir besoin d’autres détails sur votre solution, tels que le nom d’un éditeur, d’un fournisseur et des valeurs d’ID de plan. Vous pouvez trouver chacun de ces éléments dans la page de détails de la solution, sous l’onglet **Informations d’utilisation + support**. Par exemple :

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="Capture d’écran des détails d’utilisation et de support pour une solution.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert les solutions Microsoft Sentinel et appris à rechercher et à déployer du contenu intégré.

- En savoir plus sur les [solutions Microsoft Sentinel](sentinel-solutions.md).
- Voir l’intégralité du [Catalogue de solutions Microsoft Sentinel](sentinel-solutions-catalog.md)
