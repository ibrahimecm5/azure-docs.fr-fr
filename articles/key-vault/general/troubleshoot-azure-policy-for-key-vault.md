---
title: Résoudre des problèmes d’implémentation de la stratégie Azure sur Key Vault
description: Résolvez les problèmes d’implémentation d’Azure Policy sur Key Vault.
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 380077339920071fa56d385e6de8f7e7a9e84321
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072538"
---
# <a name="troubleshoot-issues-with-implementing-azure-policy-on-key-vault"></a>Résoudre des problèmes d’implémentation de la stratégie Azure sur Key Vault

Cet article vous explique comment résoudre les erreurs générales qui peuvent se produire lorsque vous configurez [Azure Policy pour Key Vault](./azure-policy.md) et donne des suggestions de résolution.

## <a name="about-azure-policy-for-key-vault"></a>À propos d’Azure Policy pour Key Vault

[Azure Policy](../../governance/policy/index.yml) est un outil de gouvernance qui permet aux utilisateurs d’auditer et de gérer leur environnement Azure à grande échelle. Azure Policy offre la possibilité de placer des barrières sur les ressources Azure afin de s’assurer que celles-ci sont conformes aux règles de stratégie affectées. Il permet aux utilisateurs d’effectuer des audits, l’application en temps réel et la correction de leur environnement Azure. Les résultats des audits réalisés par la stratégie sont mis à disposition des utilisateurs dans un tableau de bord de conformité qui présente une vue détaillée des ressources et composants conformes et non conformes.

### <a name="logging"></a>Journalisation

Pour effectuer un monitoring des évaluations de stratégie, vous pouvez consulter les journaux Key Vault. Pour ce faire, vous pouvez activer la journalisation d’Azure Key Vault, ce qui enregistre les informations dans un compte de stockage Azure que vous fournissez. Pour obtenir des instructions pas à pas sur la configuration de cette option, consultez [Guide pratique pour activer la journalisation de Key Vault](howto-logging.md).

Lorsque la journalisation est activée, un nouveau conteneur appelé **AzurePolicyEvaluationDetails** est automatiquement créé pour collecter les informations de journalisation relatives à la stratégie dans le compte de stockage spécifié. 

> [!NOTE]
> Vous devez réglementer strictement l’accès aux données de monitoring, et en particulier les fichiers journaux, car ils peuvent contenir des informations sensibles. Découvrez comment appliquer le [rôle Azure de monitoring intégré](../../azure-monitor/roles-permissions-security.md) et limiter l’accès.
> 
> 

Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Examinons un exemple d’entrée du journal pour une stratégie de clé : [Les clés doivent avoir une date d’expiration](azure-policy.md?tabs=keys#secrets-should-have-expiration-date-set). Cette stratégie évalue toutes les clés de vos coffres de clés et signale comme non conformes celles qui n’ont pas de date d’expiration.

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

Le tableau ci-après répertorie les noms de champ et leurs descriptions :

| Nom du champ | Description |
| --- | --- |
| **ObjectName** |Nom de l’objet |
| **ObjectType** |Type d’objet de coffre de clés (certificat, secret ou clé) |
| **IsComplianceCheck** |True si l’évaluation s’est produite pendant l’audit nocturne, false si elle s’est produite pendant la création ou la mise à jour de la ressource |
| **AssignmentId** | ID de l’attribution de stratégie |
| **AssignmentDisplayName** | Nom convivial de l’attribution de stratégie |
| **DefinitionId** | ID de la définition de stratégie pour l’affectation |
| **DefinitionDisplayName** | Nom convivial de la définition de stratégie pour l’affectation |
| **Résultat** | Résultat de l’évaluation de la stratégie |
| **ExpressionEvaluationDetails** | Détails sur les évaluations effectuées pendant l’évaluation de la stratégie |
| **ExpressionValue** | Valeur réelle du champ spécifié pendant l’évaluation de la stratégie |
| **TargetValue** | Valeur attendue du champ spécifié |


### <a name="frequently-asked-questions"></a>Forum aux questions

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Récupération de Key Vault bloquée par Azure Policy

L’une des raisons peut être que votre abonnement (ou votre groupe d’administration) comporte une stratégie qui bloque la récupération. Le correctif consiste à ajuster la stratégie de sorte qu’elle ne s’applique pas lorsqu’un coffre est en cours de récupération.

Si vous voyez le type d’erreur ```RequestDisallowedByPolicy``` pour la récupération en raison de la stratégie **intégrée**, vérifiez que vous utilisez la **dernière version**. 

Si vous avez créé une **stratégie personnalisée** avec votre propre logique, voici un exemple de partie de stratégie qui peut être utilisée pour exiger une suppression réversible. La récupération d’un coffre supprimé de manière réversible exploite la même API que la création et la mise à jour d’un coffre. Toutefois, au lieu de spécifier les propriétés du coffre, elle comporte une seule propriété « createMode » avec la valeur « recover ». Le coffre est restauré avec toutes les propriétés qu’il avait lors de sa suppression. Les stratégies qui bloquent les demandes à moins que des propriétés spécifiques ne soient configurées bloquent également la récupération des coffres supprimés de manière réversible. Le correctif consiste à inclure une clause prévoyant que la stratégie ignore les demandes pour lesquelles « createMode » possède la valeur « Recover » :

Comme vous pouvez le constater, elle comporte une clause qui limite l’application de la stratégie aux cas où « createMode » n’est pas égal à « Recover » :

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals": "recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists": "false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals": "false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Latence de suppression d’une attribution de stratégie Azure sur Key Vault 

Microsoft.KeyVault.Data : Une attribution de stratégie supprimée peut mettre jusqu’à 24 heures à cesser de s’appliquer. 

Atténuation : Mettez à jour l’effet de l’attribution de stratégie sur « Désactivé ».


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>Absence d’évaluation de stratégie dans la création de secrets par le biais du modèle ARM

Les stratégies de plan de données qui évaluent la création de secrets ne s’appliquent pas aux [secrets créés par le biais du modèle ARM](../secrets/quick-create-template.md?tabs=CLI) au moment de la création du secret. Au bout de 24 heures, lorsque la vérification de conformité automatisée se produit, les résultats de conformité peuvent être revus.


## <a name="next-steps"></a>Étapes suivantes

* [Résolution des erreurs liées à Azure Policy](../../governance/policy/troubleshoot/general.md)
* [Problèmes connus d’Azure Policy](https://github.com/azure/azure-policy#known-issues)
