---
title: Fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud
description: Découvrez comment activer les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memildin
ms.openlocfilehash: 0d623789b1f99e6e7728fd5d3f80bb7ca2be7218
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441777"
---
# <a name="quickstart-set-up-microsoft-defender-for-cloud"></a>Démarrage rapide : configurer Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud propose des fonctionnalités unifiées de gestion de la sécurité et de protection contre les menaces sur l’ensemble des charges de travail multiclouds et hybrides. Alors que les fonctionnalités gratuites offrent une sécurité limitée pour vos ressources Azure uniquement, l’activation des fonctionnalités de sécurité renforcée étend ces fonctions aux ressources locales et à d’autres clouds. Defender pour le cloud aide à rechercher et à corriger les failles de sécurité, applique des contrôles d’accès et d’application pour bloquer les activités malveillantes, détecte les menaces à l’aide de l’analytique et de l’analyse décisionnelle et répond rapidement en cas d’attaque. Vous pouvez essayer les fonctionnalités de sécurité renforcée gratuitement. Pour en savoir plus, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).

Cette section de démarrage rapide vous guide tout au long des étapes recommandées pour activer Microsoft Defender pour le cloud et les fonctionnalités de sécurité renforcée. Une fois que vous avez terminé toutes les étapes du démarrage rapide, vous disposez des éléments suivants :

> [!div class="checklist"]
> * Defender pour le cloud activé sur vos abonnements Azure
> * [Fonctionnalités de sécurité renforcée](enhanced-security-features-overview.md) activés sur vos abonnements Azure
> * Configuration de la collecte automatique des données
> * [Configuration des notifications par e-mail](configure-email-notifications.md) pour les alertes de sécurité
> * Vos machines hybrides et multiclouds connectées à Azure

## <a name="prerequisites"></a>Prérequis
Pour commencer à utiliser Defender pour le cloud, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Pour activer les fonctionnalités de sécurité renforcée dans le cadre d’un abonnement, vous devez avoir le rôle de propriétaire de l’abonnement, de collaborateur de l’abonnement ou d’administrateur de la sécurité.

## <a name="enable-defender-for-cloud-on-your-azure-subscription"></a>Activer Defender pour le cloud sur votre abonnement Azure

> [!TIP]
> Pour activer Defender sur le cloud sur tous les abonnements au sein d’un groupe d’administration, consultez [Activer Defender sur le cloud sur plusieurs abonnements Azure](onboard-management-group.md).

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).

1. Dans le menu du portail, sélectionnez **Defender sur le cloud**. 

    La page de présentation de Defender pour le cloud s’ouvre.

    :::image type="content" source="./media/get-started/overview.png" alt-text="Tableau de bord de vue d’ensemble de Defender pour le cloud" lightbox="./media/get-started/overview.png":::

    **Defender pour le cloud : Vue d’ensemble** donne un aperçu unifié de l’état de sécurité des charges de travail cloud hybrides, vous aidant ainsi à détecter et à évaluer leur sécurité, ainsi que d’identifier et d’atténuer les risques. Pour en savoir plus, consultez la [Page de présentation de Defender pour le cloud](overview-page.md).

    Defender sur le cloud active automatiquement et gratuitement les abonnements Azure qui n’ont pas été intégrés par vous ni par aucun autre utilisateur de l’abonnement.

Pour afficher et filtrer la liste des abonnements, sélectionnez l’élément de menu **Abonnements**. Defender pour le cloud ajuste l’affichage pour refléter la posture de sécurité des abonnements sélectionnés. 

Quelques minutes après le premier lancement de Defender pour le cloud, vous pouvez voir :

- des **recommandations** de méthodes visant à améliorer la sécurité de vos ressources connectées ;
- un inventaire de vos ressources en cours d’évaluation par Defender sur le cloud, avec l’état de sécurité de chacune.

Pour tirer pleinement parti de Defender pour le cloud, passez aux étapes suivantes de la section du démarrage rapide.



## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez activé Defender pour le cloud. L’étape suivante consiste à activer les fonctionnalités de sécurité renforcée pour la gestion unifiée de la sécurité et la protection contre les menaces dans vos charges de travail cloud hybrides.

> [!div class="nextstepaction"]
> [Démarrage rapide : Activer les fonctionnalités de sécurité renforcée](enable-enhanced-security.md)
