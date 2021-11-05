---
title: Intégration d’un groupe d’administration à Microsoft Defender pour le cloud
description: Découvrez comment utiliser une définition Azure Policy fournie afin d’activer Microsoft Defender pour le cloud pour tous les abonnements d’un groupe d’administration.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a39a46bbc78cb915e57f7e584c5c567ff8718ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064749"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>Activer Defender pour le cloud sur tous les abonnements d’un groupe d’administration

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Vous pouvez utiliser Azure Policy pour activer Defender pour le cloud sur tous les abonnements Azure au sein du même groupe d’administration. Cela est plus pratique que d’accéder aux abonnements individuellement à partir du portail et fonctionne même s’ils appartiennent à des propriétaires différents. 

Pour intégrer un groupe d’administration et tous ses abonnements :

1. En tant qu’utilisateur disposant des autorisations **Administration de sécurité**, ouvrez Azure Policy et recherchez la définition **Activer Defender pour le cloud sur votre abonnement**.

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="La définition Azure Policy Activer Defender pour le cloud sur votre abonnement.":::

1. Sélectionnez **Affecter** et veillez à définir l’étendue sur le niveau du groupe d’administration.

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="Attribution de la définition Activer Defender pour le cloud sur votre abonnement.":::

    > [!TIP]
    > Hormis l’étendue, il n’existe aucun paramètre obligatoire.

1. Sélectionnez **Créer une tâche de correction** afin que soient intégrés tous les abonnements existants pour lesquels Defender pour le cloud n’est pas activé.

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="Création d’une tâche de correction pour la définition Azure Policy Activer Defender pour le cloud sur votre abonnement.":::

1. Quand la définition est assignée, elle effectue les opérations suivantes :

    1. Elle détecte tous les abonnements du groupe d’administration qui ne sont pas encore inscrits auprès de Defender pour le cloud.
    1. Elle marque ces abonnements comme étant « non conformes ».
    1. Marquer comme « conforme » tous les abonnements inscrits (que les fonctionnalités de sécurité renforcée de Defender pour le cloud soient activées ou non).

    La tâche de correction active ensuite Defender pour le cloud, gratuitement, sur les abonnements non conformes.

> [!IMPORTANT]
> La définition de stratégie active uniquement Defender pour le cloud sur les **abonnements** existants. Pour inscrire de nouveaux abonnements, ouvrez l’onglet Conformité, sélectionnez les abonnements non conformes appropriés et créez une tâche de correction. Répétez cette étape si vous souhaitez superviser un ou plusieurs nouveaux abonnements avec Defender pour le cloud.

## <a name="optional-modifications"></a>Modifications facultatives

Vous pouvez modifier la définition Azure Policy de plusieurs façons : 

- **Définir la conformité différemment** : la stratégie fournie classifie comme « non conformes » tous les abonnements du groupe d’administration qui ne sont pas encore inscrits auprès de Defender pour le cloud. Vous pouvez choisir de la définir sur tous les abonnements où les fonctionnalités de sécurité renforcée de Defender pour le cloud sont activées.

    La définition fournie définit *un* des paramètres de tarif ci-dessous comme étant conforme. Cela signifie qu’un abonnement défini sur « standard » ou « free » (gratuit) est conforme.

    > [!TIP]
    > Quand un plan Microsoft Defender est activé, il est décrit dans une définition de stratégie comme étant associé au paramètre « Standard ». Quand il est désactivé, il est « Gratuit ». Pour en savoir plus sur les différences entre ces plans, consultez [Fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud](enhanced-security-features-overview.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Si vous le remplacez par ce qui suit, seuls les abonnements définis sur « standard » sont classifiés comme étant conformes :

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Définir des plans Microsoft Defender à appliquer lors de l’activation de Defender pour le cloud** : la stratégie fournie active Defender pour le cloud sans aucune des fonctionnalités de sécurité renforcée facultatives. Vous pouvez choisir d’activer un ou plusieurs plans Microsoft Defender.

    La section `deployment` de la définition fournie a un paramètre `pricingTier`. Par défaut, ce paramètre est défini sur `free`, mais vous pouvez le modifier. 


## <a name="next-steps"></a>Étapes suivantes :

Maintenant que vous avez intégré un groupe d’administration entier, activez les fonctionnalités de sécurité renforcée. 

> [!div class="nextstepaction"]
> [Activer les protections renforcées](enable-enhanced-security.md)
