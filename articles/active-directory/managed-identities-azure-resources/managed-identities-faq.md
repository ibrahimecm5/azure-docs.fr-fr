---
title: Identités managées pour les ressources Azure - Forum aux questions - Azure AD"
description: Forum aux questions sur les identités managées
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/20/2021
ms.author: barclayn
ms.openlocfilehash: 48d3d5e0e1b6adfb1c0763d1fb7824d604628962
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266123"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Identités managées pour les ressources Azure - Forum aux questions - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="administration"></a>Administration

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Comment trouver les ressources qui ont une identité managée ?

Vous pouvez obtenir la liste des ressources qui ont une identité managée affectée par le système en exécutant la commande Azure CLI suivante : 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="which-azure-rbac-permissions-are-required-to-use-a-managed-identity-on-a-resource"></a>Quelles sont les autorisations RBAC Azure nécessaires pour une identité managée sur une ressource ?

- Identité managée affectée par le système : Vous avez besoin d’autorisations en écriture sur la ressource. Par exemple, pour les machines virtuelles, vous avez besoin de `Microsoft.Compute/virtualMachines/write`. Cette action est incluse dans les rôles intégrés spécifiques de la ressource, tel que le [Contributeur de machines virtuelles](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Attribution d’identités managées affectées par l’utilisateur aux ressources : vous devez disposer d’autorisations en écriture sur la ressource. Par exemple, pour les machines virtuelles, vous avez besoin de `Microsoft.Compute/virtualMachines/write`. Vous aurez également besoin de l’action sur l’identité affectée par l’utilisateur. Cette action est incluse dans le rôle intégré [Opérateur de l’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-operator).
- Gestion des identités affectées par l’utilisateur : pour créer ou supprimer des identités managées affectées par l’utilisateur, vous avez besoin de l’attribution de rôle [Contributeur d’identité managée](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).
- Gestion des attributions de rôles pour les identités managées : vous avez besoin de l’attribution de rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#all) ou [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#all) sur la ressource à laquelle vous accordez l’accès. Vous aurez besoin de l’attribution de rôle [Lecteur](../../role-based-access-control/built-in-roles.md#all) à la ressource avec une identité affectée par le système ou à l’identité affectée par l’utilisateur qui reçoit l’attribution de rôle. Si vous n’avez pas d’accès en lecture, vous pouvez effectuer une recherche par « utilisateur, groupe ou principal de service » pour trouver le principal du service de sauvegarde de l’identité au lieu de rechercher par identité managée lors de l’ajout de l’attribution de rôle. [En savoir plus sur l’affectation de rôles Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Comment empêcher la création d’identités managées affectées par l’utilisateur ?

Vous pouvez empêcher vos utilisateurs de créer des identités managées affectées par l’utilisateur avec [Azure Policy](../../governance/policy/overview.md)

1. Accédez au [portail Azure](https://portal.azure.com), puis à **Stratégie**.
2. Choisissez **Définitions**
3. Sélectionnez **+ Définition de stratégie** et entrez les informations nécessaires.
4. Dans la section Règle de stratégie, collez
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

Une fois la stratégie créée, affectez-la au groupe de ressources que vous souhaitez utiliser.

1. Accédez aux groupes de ressources.
2. Recherchez le groupe de ressources que vous utilisez pour le test.
3. Choisissez **Stratégies** dans le menu de gauche.
4. Sélectionnez **Attribuer la stratégie**
5. Dans la section **De base**, indiquez :
    1. **Étendue** : groupe de ressources utilisé pour le test
    1. **Définition de stratégie** : stratégie que nous avons créée précédemment.
6. Conservez les valeurs par défaut pour tous les autres paramètres, puis choisissez **Vérifier + créer**

À ce stade, toute tentative de création d’une identité managée affectée par l’utilisateur dans le groupe de ressources échoue.

  ![Violation de stratégie](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Concepts

### <a name="do-managed-identities-have-a-backing-app-object"></a>Les identités managées ont-elles un objet de sauvegarde d’application ?

Non. Les identités managées et les inscriptions d’applications Azure AD sont deux choses différentes dans l’annuaire.

Les inscriptions d’applications ont deux composants : un objet application et un objet principal de service.
Les identités managées des ressources Azure ont un seul de ces composants : un objet principal de service.

Les identités managées n’ont pas d’objet application dans l’annuaire, qui est l’objet couramment utilisé pour accorder des autorisations d’application pour MS Graph. Au lieu de cela, les autorisations MS Graph pour les identités managées doivent être accordées directement au principal de service.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Quelles sont les informations d’identification associées à une identité managée ? Quelle est la durée de validité et quelle est la fréquence de rotation ?

> [!NOTE]
> Le mode d’authentification des identités managées est un détail d’implémentation interne susceptible d’être modifié sans préavis.

Les identités managées utilisent l’authentification par certificat. Les informations d’identification de chaque identité managée ont une date d’expiration de 90 jours et elles sont renouvelées après 45 jours.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quelle est l’identité utilisée par défaut par IMDS si vous ne spécifiez pas l’identité dans la requête ?

- Si l’identité managée affectée par le système est activée et qu’aucune identité n’est spécifiée dans la requête, IMDS utilise par défaut l’identité managée affectée par le système.
- Si l’identité managée affectée par le système n’est pas activée et qu’il n’existe qu’une seule identité managée affectée par l’utilisateur, IMDS utilise par défaut l’identité managée affectée par ce seul utilisateur.
- Si l’identité managée affectée par le système n’est pas activée et qu’il existe plusieurs identités managées affectées par l’utilisateur, la spécification d’une identité managée dans la requête est obligatoire.

## <a name="limitations"></a>Limites

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>La même identité managée peut-elle être utilisée dans plusieurs régions ?

En deux mots, oui, vous pouvez utiliser des identités managées affectées par l’utilisateur dans plusieurs régions Azure. La réponse plus longue est que, si des identités managées affectées par l’utilisateur sont créées en tant que ressources régionales, le [principal de service](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) associé créé dans Azure AD est disponible globalement. Le principal de service peut être utilisé à partir de n’importe quelle région Azure et sa disponibilité dépend de celle d’Azure AD. Par exemple, si vous avez créé une identité managée affectée par l'utilisateur dans la région Sud-Centre et que cette région devient indisponible, ce problème n’affecte que les activités de [plan de contrôle](../../azure-resource-manager/management/control-plane-and-data-plane.md) sur l’identité managée proprement dite.  Les activités effectuées par des ressources déjà configurées pour utiliser des identités managées ne seront pas affectées.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Les identités managées pour les ressources Azure fonctionnent-elles avec Azure Cloud Services ?

Non, il n’existe aucun plan de prise en charge des identités managées pour les ressources Azure dans Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Quelle est la limite de sécurité des identités managées pour les ressources Azure ?

La limite de sécurité de l’identité est la ressource à laquelle elle est jointe. Par exemple, la limite de sécurité activée pour une machine virtuelle avec des identités managées pour des ressources Azure est la machine virtuelle. Tout code s’exécutant sur cette machine virtuelle est en mesure d’appeler les identités managées pour des jetons de point de terminaison et de requête de ressources Azure. L’expérience est similaire à celle d’autres ressources qui prennent en charge les identités managées pour des ressources Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Les identités managées sont-elles recréées automatiquement si je déplace un abonnement vers un autre répertoire ?

Non. Si vous déplacez un abonnement vers un autre répertoire, vous devez les recréer manuellement et accorder à nouveau les attributions de rôle Azure.
- Pour les identités managées affectées par le système : désactivez et réactivez-les. 
- Pour les identités managées affectées par l’utilisateur : supprimez, recréez et joignez-les à nouveau aux ressources nécessaires (par exemple, des machines virtuelles)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Puis-je utiliser une identité managée pour accéder à une ressource dans un autre répertoire/abonné ?

Non. Les identités managées ne prennent actuellement pas en charge les scénarios entre répertoires. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Existe-t-il des limites de taux qui s’appliquent aux identités managées ?

Les limites des identités managées dépendent des limites de service Azure, des limites d’Azure Instance Metadata Service (IMDS) et des limites de service d’Azure Active Directory.

- Les **limites de service Azure** définissent le nombre d’opérations de création qui peuvent être effectuées au niveau du locataire et de l’abonnement. Les identités managées attribuées par l’utilisateur ont également des [limitations](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) sur la façon dont elles peuvent être nommées.
- **IMDS** En général, les demandes adressées à IMDS sont limitées à cinq par seconde. Celles qui dépassent ce seuil sont rejetées avec une réponse 429. Les demandes adressées à la catégorie Identité managée sont limitées à 20 par seconde et à 5 simultanément. Pour plus d’informations, consultez l’article sur [Azure Instance Metadata Service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity).
- **Service Azure Active Directory** Chaque identité managée compte pour la limite de quota d’objets dans un locataire Azure AD, comme décrit dans [Restrictions et limites du service Azure Active Directory](../enterprise-users/directory-service-limits-restrictions.md).


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Est-il possible de déplacer une identité managée affectée par l’utilisateur à un autre groupe de ressources/abonnement ?

Le déplacement d’une identité managée affectée par l’utilisateur à un autre groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment les identités managées fonctionnent avec les machines virtuelles](how-managed-identities-work-vm.md)
