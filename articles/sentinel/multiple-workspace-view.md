---
title: Utilisez les incidents Microsoft Sentinel dans plusieurs espaces de travail à la fois | Microsoft Docs
description: Comment voir les incidents dans plusieurs espaces de travail simultanément dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 05f0fcf96b9553648830b084d6fa5ec7f00a634d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520462"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Utiliser les incidents dans plusieurs espaces de travail simultanément 

 [!INCLUDE [Banner for top of topics](./includes/banner.md)]

Pour tirer pleinement parti des fonctionnalités de Microsoft Sentinel, Microsoft recommande l’utilisation d’un environnement avec un seul espace de travail. Toutefois, il existe certains cas qui nécessitent plusieurs espaces de travail, par exemple, dans le cas d’un[ fournisseur de services de sécurité gérés (MSSP)](./multiple-tenants-service-providers.md) et de ses clients, sur plusieurs locataires. **L’affichage de plusieurs espaces de travail** vous permet de voir et de travailler avec les incidents de sécurité sur plusieurs espaces de travail en même temps, même sur plusieurs locataires, ce qui vous permet de conserver une visibilité et un contrôle complets de la réactivité de la sécurité de votre organisation.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="entering-multiple-workspace-view"></a>Accès à l’affichage de plusieurs espaces de travail

Lorsque vous ouvrez Microsoft Sentinel, vous voyez s’afficher la liste de tous les espaces de travail pour lesquels vous avez des droits d’accès, sur l’ensemble des locataires et abonnements sélectionnés. À gauche de chaque nom d’espace de travail se trouve une case à cocher. Cliquez sur le nom d’un espace de travail pour afficher cet espace de travail. Pour choisir plusieurs espaces de travail, cliquez sur toutes les cases à cocher correspondantes, puis cliquez sur le bouton **Affichage de plusieurs espaces de travail** en haut de la page.

> [!IMPORTANT]
> L’affichage de plusieurs espaces de travail prend actuellement en charge un maximum de 10 espaces de travail affichés simultanément. 
> 
> Si vous cochez plus de 10 espaces de travail, un message d’avertissement s’affiche.

Notez que dans la liste des espaces de travail, vous pouvez voir le répertoire, l’abonnement, l’emplacement et le groupe de ressources associés à chaque espace de travail. Le répertoire correspond au locataire.

   ![Choisir plusieurs espaces de travail](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Utilisation des incidents

Dans l’**affichage de plusieurs espaces de travail**, seul l’écran **Incidents** est disponible pour l’instant. Il se présente globalement comme l’écran normal des **incidents** et fonctionne de façon similaire, bien qu’il y ait quelques différences de taille :

   ![Consulter les incidents dans plusieurs espaces de travail](./media/multiple-workspace-view/incidents.png)

- Les compteurs situés en haut de la page(*Incidents ouverts*, *Nouveaux incidents*, *En cours*, etc.) montrent les chiffres de tous les espaces de travail sélectionnés.

- Vous verrez les incidents de tous les espaces de travail et répertoires sélectionnés (locataires) dans une seule liste unifiée. Vous pouvez filtrer cette liste par espace de travail et par répertoire, en plus des filtres de l’écran normal des **incidents**.

- Vous devez disposer d’autorisations en lecture et en écriture sur tous les espaces de travail pour lesquels vous avez sélectionné des incidents. Si vous disposez uniquement d’autorisations de lecture sur certains espaces de travail, des messages d’avertissement s’affichent si vous sélectionnez les incidents dans ces espaces de travail. Vous ne pouvez pas modifier ces incidents, ni les autres que vous avez sélectionnés en même temps (même si vous disposez des autorisations pour les autres).

- Si vous choisissez un incident et que vous cliquez sur **Voir les détails complets** ou **Actions** > **Examiner**, vous vous trouverez à partir de là dans le contexte de données de l’espace de travail de cet incident et non des autres.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris comment consulter et utiliser les incidents dans plusieurs espaces de travail Microsoft Sentinel simultanément. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
