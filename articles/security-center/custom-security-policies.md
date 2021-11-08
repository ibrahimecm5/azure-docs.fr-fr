---
title: Créer des stratégies de sécurité personnalisées dans Microsoft Defender pour le cloud | Microsoft Docs
description: Définitions de stratégies personnalisées Azure analysées par Microsoft Defender pour le cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.custom: ignite-fall-2021
ms.openlocfilehash: e596312807e2f6d5292c4910a7e2c3e9e7d75b3f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009982"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Créer des stratégies et des initiatives de sécurité personnalisées

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Pour vous aider à sécuriser vos systèmes et votre environnement, Microsoft Defender pour le cloud génère des recommandations de sécurité. Ces recommandations sont basées sur les meilleures pratiques du secteur, qui sont incorporées à la stratégie de sécurité par défaut générique fournie à tous les clients. Elles peuvent également provenir des connaissances que Defender pour le cloud a des normes et réglementations du secteur.

Avec cette fonctionnalité, vous pouvez ajouter vos propres initiatives *personnalisées*. Vous recevez ensuite des recommandations si votre environnement ne suit pas les stratégies que vous créez. Toutes les initiatives personnalisées que vous créez apparaîtront à côté des initiatives intégrées dans le tableau de bord de conformité à la réglementation, comme décrit dans le tutoriel [Améliorer votre conformité aux normes](regulatory-compliance-dashboard.md).

Comme nous l’avons vu dans la [documentation Azure Policy](../governance/policy/concepts/definition-structure.md#definition-location), quand vous spécifiez un emplacement pour votre initiative personnalisée, il doit correspondre à un groupe d’administration ou à un abonnement. 

> [!TIP]
> Pour obtenir une vue d’ensemble des concepts clés de cette page, consultez [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Pour ajouter une initiative personnalisée à votre abonnement 

1. Dans le menu de Defender pour le cloud, sélectionnez **Stratégie de sécurité**.

1. Sélectionnez un abonnement ou un groupe d’administration auquel vous voulez ajouter une initiative personnalisée.

    [![Sélection d’un abonnement pour lequel vous allez créer votre stratégie personnalisée.](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Pour que vos initiatives personnalisées soient évaluées et affichées dans Defender pour le cloud, vous devez les ajouter au niveau de l’abonnement (ou à un niveau supérieur). Nous vous recommandons de sélectionner l’étendue la plus vaste disponible.

1. Dans la page Stratégie de sécurité, sous vos initiatives personnalisées, cliquez sur **Ajouter une initiative personnalisée**.

    [![Cliquez sur Ajouter une initiative personnalisée.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    La page suivante apparaît :

    ![Créer ou ajouter une stratégie.](media/custom-security-policies/create-or-add-custom-policy.png)

1. Dans la page Ajouter des initiatives personnalisées, passez en revue la liste de stratégies personnalisées déjà créées dans votre organisation. Si vous en voyez une que vous souhaitez attribuer à votre abonnement, cliquez sur **Ajouter**. Si aucune initiative de la liste ne répond à vos besoins, ignorez cette étape.

1. Pour créer une nouvelle initiative personnalisée :

    1. Cliquez sur **Créer une nouvelle**.
    1. Entrez l’emplacement et le nom de la définition.
    1. Sélectionnez les stratégies à inclure, puis cliquez sur **Ajouter**.
    1. Entrez les paramètres souhaités.
    1. Cliquez sur **Enregistrer**.
    1. Dans la page Ajouter des initiatives personnalisées, cliquez sur Actualiser. Votre nouvelle initiative apparaîtra comme étant disponible.
    1. Cliquez sur **Ajouter** et affectez-la à votre abonnement.

    > [!NOTE]
    > La création d’initiatives nécessite les informations d’identification du propriétaire de l’abonnement. Pour plus d’informations sur les rôles Azure, consultez [Autorisations dans Microsoft Defender pour le cloud](permissions.md).

    Votre nouvelle initiative est appliquée et vous pouvez visualiser son impact de deux façons :

    * Dans le menu de Defender pour le cloud, sélectionnez **Conformité réglementaire**. Le tableau de bord de conformité s'ouvre pour montrer votre nouvelle initiative personnalisée à côté des initiatives intégrées.
    
    * Vous commencerez ensuite à recevoir des recommandations si votre environnement ne suit pas les stratégies que vous avez définies.

1. Pour afficher les recommandations qui en résultent pour votre stratégie, cliquez sur **Recommandations** dans la barre latérale pour ouvrir la page de recommandations. Les recommandations apparaissent avec une étiquette « Personnalisée » et sont disponibles dans un délai d’une heure environ.

    [![Recommandations personnalisées.](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Configurer une stratégie de sécurité dans Azure Policy à l’aide de l’API REST

Dans le cadre de l’intégration native à Azure Policy, Microsoft Defender pour le cloud vous permet de tirer parti de l’API REST d’Azure Policy pour créer des affectations de stratégie. Les instructions suivantes vous guident tout au long de la création d’affectations de stratégies, ainsi que de la personnalisation d’affectations existantes. 

Concepts importants utilisés dans Azure Policy : 

- Une **définition de stratégie** est une règle 

- Une **initiative** est une collection de définitions de stratégie (règles) 

- Une **affectation** est l’application d’une initiative ou d’une stratégie à une étendue spécifique (groupe d’administration, abonnement, etc.) 

Defender pour le cloud dispose d’une initiative intégrée, [Azure Security Benchmark](/security/benchmark/azure/introduction), qui inclut toutes ses stratégies de sécurité. Pour évaluer les stratégies de Defender pour le cloud sur vos ressources Azure, vous devez créer une affectation sur le groupe d’administration ou un abonnement que vous voulez évaluer.

L’initiative intégrée a toutes les stratégies de Defender pour le cloud activées par défaut. Vous pouvez choisir de désactiver certaines stratégies de l’initiative intégrée. Par exemple, pour appliquer toutes les stratégies de Defender pour le cloud à l’exception de **Pare-feu d’application web**, changez la valeur du paramètre d’effet de la stratégie à **Désactivé**.

## <a name="api-examples"></a>Exemples d'API

Dans les exemples suivants, remplacez les variables suivantes :

- **{scope}** entrez le nom du groupe d’administration ou de l’abonnement auquel vous appliquez la stratégie
- **{policyAssignmentName}** entrez le nom de l’attribution de stratégie appropriée
- **{name}** entrez votre nom ou le nom de l’administrateur qui a approuvé le changement de stratégie

Cet exemple vous montre comment affecter l’initiative Defender pour le cloud intégrée sur un abonnement ou un groupe d’administration
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Cet exemple vous montre comment affecter l’initiative Defender pour le cloud intégrée sur un abonnement, avec les stratégies suivantes désactivées : 

- Mises à jour système (“systemUpdatesMonitoringEffect”) 

- Configurations de sécurité (« systemConfigurationsMonitoringEffect ») 

- Protection du point de terminaison (« endpointProtectionMonitoringEffect ») 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Cet exemple vous montre comment supprimer une affectation :
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Améliorez vos recommandations personnalisées grâce à des informations détaillées

Les recommandations intégrées fournies avec Microsoft Defender pour le cloud incluent des détails tels que des niveaux de gravité et des instructions de correction. Si vous souhaitez ajouter ce type d’informations à vos recommandations personnalisées afin qu’elles apparaissent dans le portail Azure ou à l’endroit où vous accédez à vos recommandations, vous devez utiliser l’API REST. 

Les deux types d’informations que vous pouvez ajouter sont les suivants :

- **RemediationDescription** : chaîne
- **Severity** – Enum [Low, Medium, High]

Les métadonnées doivent être ajoutées à la définition de stratégie pour une stratégie qui fait partie de l’initiative personnalisée. Elles doivent figurer dans la propriété « securityCenter », comme indiqué ci-dessous :

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Voici un exemple de stratégie personnalisée incluant la propriété metadata/securityCenter :

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Pour obtenir un autre exemple d’utilisation de la propriété securityCenter, consultez [cette section de la documentation de l’API REST](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des stratégies de sécurité personnalisées. 

Pour d’autres informations connexes, consultez les articles suivants : 

- [La vue d’ensemble des stratégies de sécurité](tutorial-security-policy.md)
- [Liste des stratégies de sécurité intégrées](./policy-reference.md)
