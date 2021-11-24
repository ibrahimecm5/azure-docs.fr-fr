---
title: Collaborer dans Microsoft Teams avec une équipe de gestion des incidents Microsoft Sentinel | Microsoft Docs
description: Découvrez comment vous connecter à Microsoft Teams à partir de Microsoft Sentinel pour collaborer avec les autres membres de votre équipe en utilisant les données de Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2a6cdf796ee8fdb7302a7f7e00e517ce99004774
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518106"
---
# <a name="collaborate-in-microsoft-teams-public-preview"></a>Collaborer dans Microsoft Teams (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel prend en charge une intégration directe à [Microsoft Teams](/microsoftteams/), ce qui vous permet d’accéder directement au travail d’équipe sur des incidents spécifiques.


> [!IMPORTANT]
> L’intégration à Microsoft Teams est actuellement disponible en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="overview"></a>Vue d’ensemble

L’intégration à Microsoft Teams directement à partir de Microsoft Sentinel permet à vos équipes de collaborer en toute transparence au sein de l’organisation et avec des parties prenantes externes.

Utilisez Microsoft Teams avec une *équipe de gestion des incidents* Microsoft Sentinel pour centraliser votre communication et votre coordination parmi le personnel concerné. Les équipes d’incident sont particulièrement utiles lorsqu’elles sont utilisées en tant que pont de conférence dédié pour les incidents à gravité élevée en cours.

Les organisations qui utilisent déjà Microsoft Teams pour la communication et la collaboration peuvent utiliser l’intégration Microsoft Sentinel pour intégrer des données de sécurité directement dans leurs conversations et leurs tâches quotidiennes. 

Une équipe de gestion des incidents Microsoft Sentinel dispose toujours des données les plus récentes et à jour de Microsoft Sentinel, ce qui garantit que vos équipes ont les données les plus pertinentes à portée de main.

## <a name="required-permissions"></a>Autorisations requises

Pour créer des équipes à partir de Microsoft Sentinel :

- L’utilisateur qui crée l’équipe doit avoir des Autorisations d’écriture d’incident dans Microsoft Sentinel. Par exemple, le rôle de [Répondeur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)est idéal, étant le rôle minimum pour ce privilège.

- L’utilisateur qui crée l’équipe doit également être autorisé à créer des équipes dans Microsoft Teams.

- Tous les utilisateurs Microsoft Sentinel, y compris les utilisateurs ayant les rôles de [Lecteur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader), [Répondeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) ou [Contributeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)peuvent obtenir l’accès à l’équipe créée en formulant la demande.

## <a name="use-an-incident-team-to-investigate"></a>Utiliser une équipe de gestion des incidents pour investiguer

Investiguez en collaboration avec une *équipe de gestion des incidents* en intégrant Microsoft Teams directement depuis votre incident.

**Pour créer votre équipe de gestion des incidents** :

1. Dans Microsoft Sentinel, dans la grille **Gestion des menaces** > **Incidents**, sélectionnez l’incident que vous êtes en train d’investiguer.

1. Au bas du volet de l’incident qui apparaît à droite, sélectionnez **Actions** > **Créer une équipe**.

    [![Créer une équipe pour collaborer au sein d’une équipe de gestion des incidents.](media/collaborate-in-microsoft-teams/create-team.png)](media/collaborate-in-microsoft-teams/create-team.png#lightbox)

    Le volet **Nouvelle équipe** s’ouvre à droite. Définissez les paramètres suivants pour votre équipe de gestion des incidents :

    - **Nom de l’équipe** : Défini automatiquement comme nom de votre incident. Modifiez le nom le cas échéant afin qu’il soit facilement identifiable.
    - **Description** : Entrez une description significative pour votre équipe de gestion des incidents.
    - **Ajouter des groupes** : Sélectionnez un ou plusieurs groupes Azure AD à ajouter à votre équipe de gestion des incidents. Les utilisateurs individuels ne sont pas pris en charge sur cette page. Si vous devez ajouter des utilisateurs individuels, [faites-le dans Microsoft Teams](#more-users) après la création de l’équipe.

        > [!TIP]
        > Si vous travaillez régulièrement avec les mêmes équipes, vous pouvez sélectionner l’étoile :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: pour les enregistrer en favoris.
        >
        > Les favoris sont automatiquement sélectionnés la prochaine fois que vous créez une équipe. Si vous souhaitez la supprimer de la prochaine équipe que vous créez, vous pouvez soit sélectionner **Supprimer** :::image type="icon" source="media/collaborate-in-microsoft-teams/delete-user-group.png" border="false":::, soit resélectionner l’étoile :::image type="icon" source="media/collaborate-in-microsoft-teams/save-as-favorite.png" border="false"::: pour supprimer complètement l’équipe de vos favoris.
        >

1. Lorsque vous avez terminé d’ajouter des groupes, sélectionnez **Créer** pour créer votre équipe de gestion des incidents.

    Le volet des incidents s’actualise, avec un lien vers votre nouvelle équipe de gestion des incidents sous le titre **Nom de l’équipe**.

    [ ![Cliquez sur le lien d’intégration à Teams ajouté à votre incident.](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg)](media/collaborate-in-microsoft-teams/teams-link-added-to-incident.jpg#lightbox)


1. Sélectionnez votre lien d’**intégration à Teams** pour basculer sur Microsoft Teams, où toutes les données relatives à votre incident sont répertoriées dans l’onglet de la **page Incident**.

    [ ![Page Incident dans Microsoft Teams.](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

Poursuivez la conversation sur l’investigation dans Teams aussi longtemps que nécessaire. Les détails complets concernant l’incident se trouvent directement dans Teams.

> [!TIP]
> - <a name="more-users"></a>Si vous devez ajouter des utilisateurs individuels à votre équipe, vous pouvez le faire dans Microsoft Teams à l’aide du bouton **Ajouter plus de personnes** dans l’onglet **Publications**.
>
> - Lorsque vous [fermez un incident](investigate-cases.md#closing-an-incident), l’équipe de gestion des incidents associée que vous avez créée dans Microsoft Teams est archivée. Si l’incident est rouvert, l’équipe de gestion des incidents associée est également rouverte dans Microsoft Teams pour vous permettre de continuer la conversation là où vous l’avez laissée.
>

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Didacticiel : Examiner les incidents avec Microsoft Sentinel](investigate-cases.md)
- [Vue d’ensemble des équipes et des chaînes dans Microsoft Teams](/microsoftteams/teams-channels-overview/)
