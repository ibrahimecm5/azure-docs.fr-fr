---
title: Utiliser l'Analyse comportementale des entités pour détecter les menaces avancées | Microsoft Docs
description: Activez l’analyse comportementale des utilisateurs et des entités dans Microsoft Sentinel et configurez des sources de données.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e8541c6205f8cfd7503b16fa918ca531d30d4339
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521355"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Activer l’analyse comportementale des utilisateurs et des entités (UEBA) dans Microsoft Sentinel 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Les fonctionnalités UEBA et Pages d’entité sont désormais en **disponibilité générale** dans **_toutes_** les régions et zones géographiques de Microsoft Sentinel. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Prérequis

Pour activer ou désactiver cette fonctionnalité (ces conditions préalables ne sont pas requises pour utiliser la fonctionnalité) :

- Votre utilisateur doit être membre de l’annuaire Azure Active Directory de votre organisation et non un utilisateur invité.

- Les rôles **Administrateur général** ou **Administrateur de sécurité** doivent être attribués à votre utilisateur dans Azure AD.

- Vous devez attribuer au moins l’un des **rôles Azure** suivants à votre utilisateur ([en savoir plus sur Azure RBAC](roles.md)) :
    - **Contributeur Microsoft Sentinel** au niveau de l’espace de travail ou du groupe de ressources.
    - **Contributeur Log Analytics** au niveau du groupe de ressources ou de l’abonnement.

- Aucun verrou de ressource Azure ne doit être appliqué à votre espace de travail. [Apprenez-en davantage sur le verrouillage des ressources Azure](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> Aucune licence spéciale n’est nécessaire pour ajouter la fonctionnalité UEBA à Microsoft Sentinel. Cependant, des **frais supplémentaires** peuvent s’appliquer.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Activer l'Analyse comportementale des utilisateurs et des entités

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Comportement des entités**.

1. Sous l'en-tête **Activer**, placez le commutateur sur **Activé**.

1. Cliquez sur le bouton **Sélectionner des sources de données**.

1. Dans le volet **Sélection des sources de données**, cochez les cases situées en regard des sources de données sur lesquelles vous souhaitez activer l'Analyse comportementale des utilisateurs et des entités, puis sélectionnez **Appliquer**.

    > [!NOTE]
    >
    > La partie inférieure du volet **Sélection des sources de données** répertorie les sources de données prises en charge par l'Analyse comportementale des utilisateurs et des entités et que vous n'avez pas encore activées. 
    >
    > Après avoir activé l'Analyse comportementale des utilisateurs et des entités, vous aurez la possibilité, lors de la connexion de nouvelles sources de données, de les activer à partir du volet des connecteurs de données si elles sont compatibles avec l'Analyse comportementale des utilisateurs et des entités.

1. Sélectionnez **Accéder à la recherche d'entités**. Vous accédez alors au volet de recherche des entités. Désormais, celui-ci s’affichera lorsque vous choisirez **Comportement des entités** dans le menu principal de Microsoft Sentinel.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à activer et configurer l’analyse comportementale des utilisateurs et des entités dans Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
