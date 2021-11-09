---
title: Accorder à des applications l’autorisation d’accéder à un coffre de clés Azure en utilisant le RBAC Azure | Microsoft Docs
description: Découvrez comment donner accès aux clés, secrets et certificats à l’aide d’un contrôle d’accès en fonction du rôle Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c92b17158beee9d1f6c60becb858564555bf56bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048441"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Donner accès aux clés, certificats et secrets du coffre de clés avec un contrôle d’accès en fonction du rôle Azure

> [!NOTE]
> Le fournisseur de ressources Key Vault prend en charge deux types de ressources : les **coffres** et les **HSM managés**. Le contrôle d’accès décrit dans cet article s’applique uniquement aux **coffres**. Pour en savoir plus sur le contrôle d’accès pour le HSM managé, consultez [Contrôle d’accès HSM managé](../managed-hsm/access-control.md).

Le contrôle d’accès en fonction du rôle (RBAC Azure) est un système d’autorisation basé sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md), qui permet une gestion précise des accès des ressources Azure.

Le RBAC Azure permet aux utilisateurs de gérer les autorisations de clé, de secrets et de certificats. Il fournit un emplacement unique pour gérer toutes les autorisations sur tous les coffres de clés. 

Le modèle de RBAC Azure offre la possibilité de définir des autorisations pour différents niveaux d’étendue : groupe d’administration, abonnement, groupe de ressources ou ressources individuelles.  Le RBAC Azure pour le coffre de clés offre également la possibilité d’avoir des autorisations distinctes sur des clés, des secrets et des certificats individuels.

Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md)

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Meilleures pratiques pour les clés, secrets et certificats individuels

Nous recommandons d’utiliser un coffre par application et par environnement (développement, préproduction et production).

Vous ne devez utiliser des autorisations sur des clés, secrets et certificats individuels que pour des scénarios spécifiques :

-   Partage de secrets individuels entre plusieurs applications, par exemple, lorsqu’une application doit accéder aux données d’une autre application
-   Chiffrement entre locataires avec une clé de client, par exemple, lorsqu’un fournisseur de services Internet utilise un coffre de clés de client pour chiffrer ses données

Pour des instructions sur la gestion d’Azure Key Vault, consultez :

- [Meilleurs pratiques relatives à Azure Key Vault](best-practices.md)
- [Limites du service Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Rôles intégrés Azure pour les opérations de plan de données d’Azure Key Vault

> [!NOTE]
> Le rôle `Key Vault Contributor` permet aux opérations de plan de gestion de gérer les coffres de clés. Il n’autorise pas l’accès aux clés, aux secrets et aux certificats.

| Rôle intégré | Description | id |
| --- | --- | --- |
| Administrateur Key Vault| Permet d’effectuer toutes les opérations du plan de données sur un coffre de clés et tous les objets qu’il contient, y compris les certificats, les clés et les secrets. Ne peut pas gérer les ressources du coffre de clés ni gérer les attributions de rôles. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Agent des certificats Key Vault | Permet d’effectuer une action sur les certificats d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Agent de chiffrement Key Vault | Permet d’effectuer une action sur les clés d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Utilisateur du service de chiffrement de Key Vault | Permet de lire les métadonnées des clés et d’effectuer des opérations visant à envelopper/désenvelopper. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Utilisateur de chiffrement Key Vault  | Permet d’effectuer des opérations de chiffrement à l’aide de clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Lecteur Key Vault | Permet de lire les métadonnées de coffres de clés et de leurs certificats, clés et secrets. Ne peut pas lire les valeurs sensibles, telles que les contenus secrets ou les documents clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Agent des secrets Key Vault| Permet d’effectuer une action sur les secrets d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Utilisateur des secrets Key Vault | Permet de lire le contenu du secret. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 4633458b-17de-408a-b874-0445c86b69e6 |

Pour plus d’informations sur les définitions de rôles intégrés Azure, consultez [Rôles intégrés Azure](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Utilisation des autorisations de secret, de clé et de certificat de RBAC Azure avec Key Vault

Le nouveau modèle d’autorisation de RBAC Azure pour le coffre de clés fournit une alternative au modèle d’autorisations de stratégie d’accès au coffre. 

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour ajouter des attributions de rôle, vous devez disposer d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Propriétaire](../../role-based-access-control/built-in-roles.md#owner).

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Activer les autorisations de RBAC Azure sur Key Vault

> [!NOTE]
> La modification du modèle d’autorisation nécessite l’autorisation « Microsoft.Authorization/roleAssignments/write », qui fait partie des rôles [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) et [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator). Les rôles Administrateur d’abonnement classiques tels que « Administrateur de service » et « Coadministrateur » ne sont pas pris en charge.

1.  Activer des autorisations de RBAC Azure sur un nouveau coffre de clés :

    ![Activer des autorisations Azure RBAC – Nouveau coffre](../media/rbac/image-1.png)

2.  Activez des autorisations de RBAC Azure sur un coffre de clés existant :

    ![Activer des autorisations Azure RBAC – Coffre existant](../media/rbac/image-2.png)

> [!IMPORTANT]
> La définition d’un modèle d’autorisation de RBAC Azure invalide toutes les autorisations des stratégies d’accès. Cela peut entraîner des interruptions lorsque des rôles Azure équivalents ne sont pas attribués.

### <a name="assign-role"></a>Affecter le rôle

> [!Note]
> Il est recommandé d’utiliser l’ID de rôle unique à la place du nom de rôle dans les scripts. Par conséquent, si un rôle est renommé, vos scripts continuent de fonctionner. Dans ce document, le nom de rôle est utilisé uniquement à des fins de lisibilité.

Exécutez la commande suivante pour créer une attribution de rôle :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

Pour attribuer des rôles à l’aide du portail Azure, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).  Dans le portail Azure, l’écran Attribution de rôle Azure est disponible pour toutes les ressources sous l’onglet Contrôle d’accès (IAM).

### <a name="resource-group-scope-role-assignment"></a>Attribution de rôle d’étendue de groupe de ressources

1. Accédez au groupe de ressources contenant votre coffre de clés.

    ![Attribution de rôle – Groupe de ressources](../media/rbac/image-4.png)

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | « Lecteur Key Vault » |
    | Attribuer l’accès à | Utilisateur actuel |
    | Membres | Rechercher par adresse e-mail |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

L’attribution de rôle ci-dessus permet de répertorier les objets figurant dans le coffre de clés.

### <a name="key-vault-scope-role-assignment"></a>Attribution de rôle d’étendue de coffre de clés

1. Accéder à Key Vault \> onglet Contrôle d’accès (IAM).
1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | « Agent des secrets Key Vault » |
    | Attribuer l’accès à | Utilisateur actuel |
    | Membres | Rechercher par adresse e-mail |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

### <a name="secret-scope-role-assignment"></a>Attribution de rôle d’étendue de secret

1. Ouvrez une clé secrète précédemment créée.

1. Cliquez sur l’onglet Contrôle d’accès (IAM)

    ![Attribution de rôle – Secret](../media/rbac/image-8.png)

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | « Agent des secrets Key Vault » |
    | Attribuer l’accès à | Utilisateur actuel |
    | Membres | Rechercher par adresse e-mail |

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

Pour plus d’informations, consultez [Attribuer des rôles Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

---

### <a name="test-and-verify"></a>Tester et vérifier

> [!NOTE]
> Les navigateurs utilisant une mise en cache, une actualisation de la page est requise après la suppression d’attributions de rôles.<br>
> Laissez passer quelques minutes, le temps que les attributions de rôles soient actualisées.

1. Validez l’ajout du nouveau secret sans le rôle « Responsable des secrets de Key Vault » au niveau du coffre de clés.

Accédez à l’onglet Contrôle d’accès (IAM) du coffre de clés, puis supprimez l’attribution de rôle « Responsable des secrets de Key Vault » pour cette ressource.

![Supprimer une attribution – Coffre de clés](../media/rbac/image-9.png)

Accédez au secret créé précédemment. Vous pouvez voir toutes les propriétés du secret.

![Affichage de secret avec accès](../media/rbac/image-10.png)

La page Créer un secret (Secrets \> +Générer/Importer) doit afficher l’erreur suivante :

   ![Créer un secret](../media/rbac/image-11.png)

2.  Validez la modification du secret sans le rôle « Responsable des secrets de Key Vault » au niveau du secret.

-   Accédez à l’onglet Contrôle d’accès (IAM) du secret créé, puis supprimez l’attribution de rôle « Responsable des secrets de Key Vault » pour cette ressource.

-   Accédez au secret créé précédemment. Vous pouvez voir les propriétés du secret.

   ![Affichage de secret sans accès](../media/rbac/image-12.png)

3. Valider les secrets lus sans le rôle de lecteur au niveau du coffre de clés.

-   Accédez à l’onglet Contrôle d’accès (IAM) du groupe de ressources, puis supprimez l’attribution de rôle « Lecteur de Key Vault ».

-   La navigation vers l’onglet Secrets du coffre de clés doit afficher l’erreur suivante :

   ![Onglet Secret – Erreur](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Création de rôles personnalisés 

[Commande az role definition create](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Pour plus d’informations sur la manière de créer des rôles personnalisés, consultez :

[Rôle personnalisés Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Limites et performances connues

-   Le RBAC de plan de données de Key Vault n’est pas pris en charge dans des scénarios mutualisés comme avec Azure Lighthouse
-   2000 attributions de rôles Azure par abonnement.
-   Latence des attributions de rôles : au niveau des performances attendues actuelles, jusqu’à 10 minutes (600 secondes) peuvent s’écouler après une modification des attributions de rôles, avant que le rôle soit appliqué.

## <a name="learn-more"></a>En savoir plus
1. Attribuer le rôle [NOMRÔLE] à [UTILISATEUR | GROUPE | PRINCIPALSERVICE | IDENTITÉMANAGÉE] dans l’étendue [GROUPEGESTION | ABONNEMENT | GROUPERESSOURCES | RESSOURCE].


- [Vue d’ensemble d’Azure RBAC](../../role-based-access-control/overview.md)
- [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md)
- [Tutoriel Rôles personnalisés](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Bonnes pratiques relatives à Azure Key Vault](best-practices.md)
