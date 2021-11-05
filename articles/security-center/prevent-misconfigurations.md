---
title: Comment empêcher les configurations incorrectes avec Microsoft Defender pour le cloud
description: Découvrez comment utiliser les options « Appliquer » et « Refuser » de Defender pour le cloud dans les pages des détails des recommandations
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 772055f91c10a4a2b58945ee314271e512be61bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037437"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les erreurs de configuration de la sécurité sont à l’origine de la plupart des incidents. Defender pour le cloud peut aider à *prévenir* les erreurs de configuration des nouvelles ressources en ce qui concerne les recommandations spécifiques. 

Cette fonctionnalité peut vous aider à sécuriser vos charges de travail et à stabiliser votre degré de sécurisation.

La mise en œuvre d’une configuration sécurisée, basée sur une recommandation spécifique, est proposée en deux modes :

- En utilisant l’effet **Refuser** d’Azure Policy, vous pouvez empêcher la création de ressources non saines
- En utilisant l’option **Appliquer**, vous pouvez tirer parti de l’effet **DeployIfNotExist** d’Azure Policy et corriger automatiquement les ressources non conformes lors de leur création

Cette option est disponible en haut de la page des détails des ressources pour les recommandations de sécurité sélectionnées (voir [Recommandations avec les options refuser/appliquer](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Empêcher la création de la ressource

1. Ouvrez la recommandation que vos nouvelles ressources doivent respecter, puis sélectionnez le bouton **Refuser** en haut de la page.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-deny-button.png" alt-text="Page de recommandation avec le bouton Refuser mis en évidence.":::

    Le volet de configuration s’ouvre et répertorie les options d’étendue. 

1. Définissez l’étendue en sélectionnant l’abonnement ou le groupe d’administration approprié.

    > [!TIP]
    > Vous pouvez utiliser les trois points à la fin de la ligne pour modifier un abonnement unique, ou utiliser les cases à cocher pour sélectionner plusieurs abonnements ou groupes, puis sélectionner **Remplacer par Refuser**.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-prevent-resource-creation.png" alt-text="Définition de l’étendue pour Refuser Azure Policy.":::


## <a name="enforce-a-secure-configuration"></a>Appliquer une configuration sécurisée

1. Ouvrez la recommandation pour laquelle vous allez déployer un déploiement de modèle si les nouvelles ressources ne correspondent pas, puis sélectionnez le bouton **Appliquer** en haut de la page.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-button.png" alt-text="Page de recommandation avec le bouton Appliquer mis en évidence.":::

    Le volet de configuration s’ouvre avec toutes les options de configuration de la stratégie. 

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-config.png" alt-text="Appliquer des options de configuration.":::

1. Définissez l’étendue, le nom de l’affectation et d’autres options pertinentes.

1. Sélectionnez **Revoir + créer**.

## <a name="recommendations-with-denyenforce-options"></a>Recommandations avec les options Refuser/Appliquer

Ces recommandations peuvent être utilisées avec l’option **Refuser** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Ces recommandations peuvent être utilisées avec l’option **Appliquer** :

- L’audit sur SQL Server doit être activé
- Les clusters Kubernetes avec Azure Arc doivent avoir l’extension Azure Defender installée
- Sauvegarde Azure doit être activé pour les machines virtuelles
- Microsoft Defender pour App Service doit être activé
- Microsoft Defender pour les registres de conteneurs doit être activé
- Microsoft Defender pour DNS doit être activé
- Microsoft Defender pour Key Vault doit être activé
- Microsoft Defender pour Kubernetes doit être activé
- Microsoft Defender pour Resource Manager doit être activé
- Microsoft Defender pour les serveurs doit être activé
- Microsoft Defender pour les serveurs Azure SQL Database doit être activé
- Microsoft Defender pour les serveurs SQL sur les machines doit être activé
- Microsoft Defender pour SQL doit être activé pour les serveurs Azure SQL non protégés
- Microsoft Defender pour le stockage doit être activé
- Le module complémentaire Azure Policy pour Kubernetes doit être installé et activé sur vos clusters
- Les journaux de diagnostic dans Azure Stream Analytics doivent être activés
- Les journaux de diagnostic doivent être activés dans les comptes Batch
- Les journaux de diagnostic dans Data Lake Analytics doivent être activés
- Les journaux de diagnostic dans Event Hub doivent être activés.
- Les journaux de diagnostic dans Key Vault doivent être activés.
- Les journaux de diagnostic dans Logic Apps doivent être activés.
- Les journaux de diagnostic dans les services Search doivent être activés.
- Les journaux de diagnostic dans Service Bus doivent être activés
