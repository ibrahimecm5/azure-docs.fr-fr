---
title: Déployer une stratégie pouvant être corrigée
description: Pour déployer des stratégies qui utilisent une tâche de correction via Azure Lighthouse, vous devez créer une identité managée dans le locataire client.
ms.date: 11/05/2021
ms.topic: how-to
ms.openlocfilehash: 8255519f1c7229aaff1eee88d93c2b2ea73ae7ce
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892226"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Déployer une stratégie pouvant être corrigée dans un abonnement délégué

[Azure Lighthouse](../overview.md) permet aux fournisseurs de services de créer et de modifier des définitions de stratégies au sein d’un abonnement délégué. Toutefois, pour déployer des stratégies qui utilisent une [tâche de correction](../../governance/policy/how-to/remediate-resources.md) (autrement dit, des stratégies avec l'effet [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) ou [modify](../../governance/policy/concepts/effects.md#modify)), vous devez créer une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) dans le locataire du client. Cette identité managée peut être utilisée par Azure Policy pour déployer le modèle dans la stratégie. Il existe des étapes à suivre pour activer ce scénario, à la fois quand vous intégrez le client pour Azure Lighthouse et quand vous déployez la stratégie elle-même.

> [!TIP]
> Même si nous faisons référence aux fournisseurs de services et aux clients dans cette rubrique, les [entreprises gérant plusieurs locataires](../concepts/enterprise.md) peuvent utiliser les mêmes processus.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Créer un utilisateur qui peut attribuer des rôles à une identité managée dans le locataire du client

Quand vous intégrez un client à Azure Lighthouse, vous utilisez un [modèle Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) avec un fichier de paramètres pour définir les autorisations d'accès aux ressources déléguées dans le locataire du client. Chaque autorisation spécifie un paramètre **principalId** qui correspond à un utilisateur Azure AD, un groupe ou un principal du service dans le locataire gestionnaire, ainsi qu’un paramètre **roleDefinitionId** qui correspond au [rôle intégré Azure](../../role-based-access-control/built-in-roles.md) qui sera accordé.

Pour permettre à un **principalId** de créer une identité managée dans le locataire du client, vous devez définir son **roleDefinitionId** sur **Administrateur de l’accès utilisateur**. Bien que ce rôle ne soit généralement pas pris en charge, il peut être utilisé dans ce scénario spécifique, permettant à des comptes d’utilisateur disposant de cette autorisation d’affecter un ou plusieurs rôles intégrés spécifiques à des identités managées. Ces rôles sont définis dans la propriété **delegatedRoleDefinitionIds** et peuvent inclure [n’importe quel rôle intégré](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse), sauf Administrateur de l’accès utilisateur ou Propriétaire.

Une fois le client intégré, le **principalId** créé dans cette autorisation pourra affecter ces rôles intégrés à des identités managées dans le locataire du client. Toutefois, il n’aura aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur.

> [!NOTE]
> Les [attributions de rôles](../../role-based-access-control/role-assignments-steps.md#step-5-assign-role) entre les locataires doivent être effectuées actuellement par le biais d’API, et non dans le portail Azure.

L’exemple ci-dessous montre un **principalId** qui aura le rôle Administrateur de l’accès utilisateur. Cet utilisateur pourra attribuer deux rôles intégrés à des identités managées dans le locataire du client : Contributeur et Contributeur Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Déployer des stratégies pouvant être corrigées

Une fois que vous avez créé l’utilisateur avec les autorisations nécessaires comme indiqué ci-dessus, cet utilisateur peut déployer des stratégies dans les abonnements du client délégué.

Par exemple, supposons que vous vouliez activer les diagnostics sur des ressources Azure Key Vault dans le locataire du client, comme illustré dans cet [exemple](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Un utilisateur du client gestionnaire disposant des autorisations appropriées (comme décrit ci-dessus) déploierait un [modèle Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) pour activer ce scénario.

Notez que la création de l’attribution de stratégie à utiliser avec un abonnement délégué doit être effectuée par le biais d’API, et non dans le portail Azure. Dans ce cas, l’**apiVersion** doit être définie sur **2019-04-01-preview** ou ultérieur pour inclure la nouvelle propriété **delegatedManagedIdentityResourceId**. Cette propriété vous permet d’inclure une identité managée qui réside dans le locataire du client (dans un abonnement ou un groupe de ressources qui a été intégré à Azure Lighthouse).

L’exemple suivant montre une attribution de rôle avec un **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Un [exemple similaire](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) est disponible pour illustrer comment déployer une stratégie qui ajoute ou supprime une balise (à l’aide de l’effet modify) à un abonnement délégué.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Policy](../../governance/policy/index.yml).
- En savoir plus sur les [identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).
