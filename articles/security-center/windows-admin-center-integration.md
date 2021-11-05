---
title: Guide pratique pour protéger les serveurs Windows Admin Center avec Microsoft Defender pour le cloud
description: Cet article explique comment intégrer Microsoft Defender pour le cloud à Windows Admin Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c1c5af62c723d207b9eb5bdfc3c38fa57e9b4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055936"
---
# <a name="protect-windows-admin-center-resources-with-microsoft-defender-for-cloud"></a>Protéger des ressources Windows Admin Center avec Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Windows Admin Center est un outil de gestion pour vos serveurs Windows. Il s’agit d’un emplacement unique permettant aux administrateurs système d’accéder à la plupart des outils d’administration les plus couramment utilisés. Dans Windows Admin Center, vous pouvez directement intégrer vos serveurs locaux dans Microsoft Defender pour le cloud. Vous pouvez ensuite afficher un résumé de vos recommandations et alertes de sécurité directement dans l’expérience Windows Admin Center.

> [!NOTE]
> Votre abonnement Azure et l’espace de travail Log Analytics associé doivent tous deux disposer des fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud afin d’activer l’intégration de Windows Admin Center.
Les fonctionnalités de sécurité renforcée sont gratuites pendant les 30 premiers jours si vous ne les avez encore jamais utilisées sur l’abonnement et l’espace de travail. Pour plus d’informations sur les prix dans la devise de votre choix et en fonction de votre région, consultez la [page sur les tarifs](https://azure.microsoft.com/pricing/details/security-center/).
>

Une fois que vous avez correctement intégré un serveur Windows Admin Center à Microsoft Defender pour le cloud, vous pouvez :

* Afficher les alertes de sécurité et les recommandations à l’intérieur de l’extension Security Center dans Windows Admin Center
* Afficher la posture de sécurité et récupérer des informations détaillées supplémentaires sur vos serveurs managés par Windows Admin Center dans Defender pour le cloud dans le portail Azure (ou via une API).

En combinant ces deux outils, Defender pour le cloud devient votre fenêtre unique pour afficher toutes vos informations de sécurité, quelle que soit la ressource : protection de vos serveurs locaux managés par Windows Admin Center, de vos machines virtuelles et de toutes les charges de travail PaaS supplémentaires.

## <a name="onboard-windows-admin-center-managed-servers-into-defender-for-cloud"></a>Intégrer des serveurs managés par Windows Admin Center à Defender pour le cloud

1. Dans Windows Admin Center, sélectionnez l’un de vos serveurs, puis dans le volet **Outils**, sélectionnez l’extension Azure Security Center :

    ![Extension Azure Security Center dans Windows Admin Center.](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Si le serveur est déjà intégré à Defender pour le cloud, la fenêtre de configuration n’apparaît pas.

1. Cliquez sur **Se connecter à Azure et configurer**.
    ![Intégration de l’extension Windows Admin Center à Defender pour le cloud.](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Suivez les instructions pour connecter votre serveur à Defender pour le cloud. Une fois que vous avez entré et confirmé les informations nécessaires, Defender pour le cloud effectue les modifications de configuration nécessaires pour s’assurer que toutes les conditions suivantes sont remplies :
    * Une passerelle Azure est inscrite.
    * Le serveur dispose d’un espace de travail pour les rapports et d’un abonnement associé.
    * La solution Log Analytics de Defender pour le cloud est activée sur l’espace de travail. Cette solution fournit les fonctionnalités d’Azure Defender pour *tous* les serveurs et toutes les machines virtuelles qui produisent des rapports sur cet espace de travail.
    * Microsoft Defender pour les serveurs est activé sur l’abonnement.
    * L’agent Log Analytics est installé sur le serveur et configuré pour rendre compte à l’espace de travail sélectionné. Si le serveur est déjà dans un autre espace de travail, il est configuré pour créer des rapports sur l’espace de travail nouvellement sélectionné.

    > [!NOTE]
    > L’intégration des recommandations peut prendre un certain temps. En fait, en fonction de l’activité de votre serveur, vous risquez de ne recevoir *aucune* alerte. Pour générer des alertes de test pour vérifier que vos alertes fonctionnent correctement, suivez les instructions de [la procédure de validation d’alerte](alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Afficher des recommandations et des alertes de sécurité dans Windows Admin Center

Une fois intégré, vous pouvez afficher vos alertes et vos recommandations directement dans la zone Azure Security Center de Windows Admin Center. Cliquez sur une recommandation ou une alerte pour l’afficher dans le portail Azure. Là, vous obtiendrez des informations supplémentaires et découvrirez comment corriger les problèmes.

[![Alertes et recommandations de Defender pour le cloud telles qu’affichées dans Windows Admin Center.](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-defender-for-cloud"></a>Afficher des recommandations et des alertes de sécurité pour les serveurs managés par Windows Admin Center dans Defender pour le cloud
À partir de Microsoft Defender pour le cloud :

* Pour afficher les recommandations de sécurité concernant l’ensemble de vos serveurs Windows Admin Center, ouvrez l’[inventaire des actifs](asset-inventory.md) et appliquez un filtre sur le type de machine que vous souhaitez examiner. sélectionnez l’onglet **Machines virtuelles et ordinateurs**.

* Pour afficher les alertes de sécurité pour tous les serveurs Windows Admin Center, ouvrez **Alertes de sécurité**. Cliquez sur **Filtre** et assurez-vous que **seul** « Non Azure » est sélectionné :

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrer les alertes de sécurité pour les serveurs gérés par Windows Admin Center." lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::
