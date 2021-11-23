---
title: Qu’est-ce que le contrôle d’accès en fonction des attributs Azure (Azure ABAC) ? (préversion)
description: Obtenez une vue d’ensemble du contrôle d’accès en fonction des attributs Azure (Azure ABAC). Utilisez des attributions de rôle avec des conditions pour contrôler l’accès aux ressources Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: overview
ms.workload: identity
ms.date: 05/13/2021
ms.author: rolyon
ms.openlocfilehash: 84bcac43d22d1c90ccfe4f674c495d8439fe9536
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398323"
---
# <a name="what-is-azure-attribute-based-access-control-azure-abac-preview"></a>Qu’est-ce que le contrôle d’accès en fonction des attributs Azure (Azure ABAC) ? (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le contrôle d’accès en fonction des attributs (ABAC) est un système d’autorisation qui définit l’accès en fonction des attributs associés aux principaux de sécurité, aux ressources et à l’environnement. ABAC vous permet d’accorder à un principal de sécurité l’accès à une ressource selon les attributs. Azure ABAC fait référence à l’implémentation d’ABAC pour Azure.

## <a name="what-are-role-assignment-conditions"></a>Que sont les conditions d’attribution de rôle ?

Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) est un système d’autorisation qui vous permet de gérer qui des utilisateurs ont accès aux ressources Azure, ce qu’ils peuvent en faire et à quels emplacements ils peuvent accéder. Dans la plupart des cas, Azure RBAC assure la gestion des accès dont vous avez besoin grâce à des définitions de rôle et des attributions de rôle. En revanche, dans certains cas, vous avez peut-être besoin de disposer d’une gestion des accès plus précise ou de simplifier la gestion de centaines d’attributions de rôle.

Azure ABAC s’appuie sur Azure RBAC en ajoutant des conditions d’attribution de rôle en fonction des attributs dans le contexte d’actions spécifiques. Une *condition d’attribution de rôle* est une autre vérification que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès encore plus précis. Une condition filtre les autorisations accordées dans le cadre de la définition de rôle et de l’attribution de rôle. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Vous ne pouvez pas refuser explicitement l’accès à des ressources spécifiques en utilisant des conditions.

## <a name="why-use-conditions"></a>Pourquoi utiliser des conditions ?

L’utilisation de conditions d’attribution de rôle revêt trois principaux avantages :

- **Fournir un contrôle d’accès plus précis** : Une attribution de rôle utilise une définition de rôle comprenant des actions et des actions de données pour accorder des autorisations à un principal de sécurité. Vous pouvez écrire des conditions pour filtrer ces autorisations à des fins de contrôle d’accès plus précis. Vous pouvez aussi ajouter des conditions à des actions spécifiques. Par exemple, vous pouvez octroyer à John un accès en lecture aux objets blob de votre abonnement uniquement si ces objets blob sont étiquetés Project=Blue. 
- **Contribuer à réduire le nombre d’attributions de rôle** : Chaque abonnement Azure est soumis à une limite d’attributions de rôle. Dans certains scénarios, des milliers d’attributions de rôle sont nécessaires. Toutes ces attributions de rôle ont besoin d’être gérées. Dans ces scénarios, vous pouvez éventuellement ajouter des conditions pour utiliser considérablement moins d’attributions de rôle. 
- **Utiliser des attributs qui ont du sens pour votre entreprise** : Les conditions vous permettent d’utiliser des attributs qui ont un intérêt particulier pour vous en termes de contrôle d’accès. Par exemple, les attributs peuvent être le nom du projet, la phase de développement d’un logiciel et des niveaux de classification. Les valeurs de ces attributs de ressource sont dynamiques et évoluent à mesure que les utilisateurs passent d’une équipe à l’autre et d’un projet à l’autre.

## <a name="example-scenarios-for-conditions"></a>Exemples de scénarios pour les conditions

Dans plusieurs scénarios, vous pouvez avoir besoin d’ajouter une condition à votre attribution de rôle. Voici quelques exemples.

- Accès en lecture aux objets blob portant l’étiquette Project=Cascade.
- Les nouveaux objets blob doivent inclure l’étiquette Project=Cascade.
- Les objets blob existants doivent être étiquetés avec au moins une clé Project ou une clé Program.
- Les objets blob existants doivent être étiquetés avec une clé Project et des valeurs Cascade, Baker ou Skagit.
- Lire, écrire ou supprimer des objets blob dans des conteneurs nommés blobs-example-container.
- Accès en lecture aux objets blob dans les conteneurs nommés blobs-example-container avec un chemin readonly.
- Accès en écriture aux objets blob situés dans les conteneurs nommés Contosocorp avec un chemin uploads/contoso.
- Accès en lecture aux objets blob avec l’étiquette Program=Alpine et un chemin logs.

Pour plus d’informations sur la manière de créer ces exemples, consultez [Exemples de conditions d’attribution de rôle Azure](../storage/common/storage-auth-abac-examples.md).

## <a name="where-can-conditions-be-added"></a>Où les conditions peuvent-elles être ajoutées ?

Actuellement, vous pouvez ajouter des conditions à des attributions de rôle intégrées ou personnalisées qui ont des [actions de données d’objet blob de stockage](conditions-format.md#actions). Sont inclus les rôles intégrés suivants :

- [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor)
- [Propriétaire des données Blob du stockage](built-in-roles.md#storage-blob-data-owner)
- [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader)

Les conditions sont ajoutées au niveau de la même étendue que l’attribution de rôle. Comme pour les attributions de rôle, vous devez avoir des autorisations `Microsoft.Authorization/roleAssignments/write` pour ajouter une condition.

Voici les attributs de stockage que vous pouvez utiliser dans vos conditions.

- Nom du conteneur
- Chemin d’accès d’objet blob
- Clés d’étiquettes d’index d’objet blob
- Étiquettes d’index d’objet blob

> [!TIP]
> Les objets blob prennent également en charge la possibilité de stocker des métadonnées de clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux étiquettes d’index d’objet blob, vous devez utiliser des étiquettes d’index d’objet blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des étiquettes d’index d’objet blob (préversion)](../storage/blobs/storage-manage-find-blobs.md).

## <a name="what-does-a-condition-look-like"></a>À quoi ressemble une condition ?

Vous pouvez ajouter des conditions à des attributions de rôle nouvelles ou existantes. Voici le rôle [Lecteur des données Blob du stockage](built-in-roles.md#storage-blob-data-reader) qui a été affecté à un utilisateur nommé Chandra à l’étendue d’un groupe de ressources. Une condition est aussi ajoutée. Elle autorise uniquement l’accès en lecture aux objets blob dont l’étiquette est Project=Cascade.

![Diagramme de l’attribution de rôle avec une condition.](./media/conditions-overview/condition-role-assignment-rg.png)

Si Chandra tente de lire un objet blob qui ne porte pas l’étiquette Project=Cascade, l’accès n’est pas autorisé.

![Le diagramme de l’accès refusé avec une condition.](./media/conditions-overview/condition-access-multiple.png)

Voici à quoi ressemble la condition dans le portail Azure :

![Section Générer l’expression avec des valeurs pour les balises d’index d’objet blob.](./media/shared/condition-expressions.png)

Voici à quoi ressemble la condition exprimée sous forme de code :

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

Pour plus d’informations sur le format des conditions, consultez [Format et syntaxe des conditions d’attribution de rôle Azure](conditions-format.md).

## <a name="conditions-and-privileged-identity-management-pim"></a>Conditions et PIM (Privileged Identity Management)

Vous pouvez également ajouter des conditions aux attributions de rôles éligibles en utilisant PIM (Privileged Identity Management). Avec PIM, vos utilisateurs finaux doivent activer une attribution de rôle éligible pour avoir l’autorisation d’effectuer certaines actions. L’utilisation de conditions dans PIM vous permet non seulement de limiter l’accès d’un utilisateur à une ressource à l’aide de conditions affinées, mais également d’utiliser PIM pour la sécuriser avec un paramètre de durée, un workflow d’approbation, une piste d’audit, etc. Pour plus d’informations, consultez [Attribuer des rôles de ressources Azure dans Privileged Identity Management](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

## <a name="terminology"></a>Terminologie

Pour mieux comprendre Azure RBAC et Azure ABAC, vous pouvez vous référer à la liste suivante de termes.

| Terme | Définition |
| --- | --- |
| contrôle d’accès en fonction des attributs (ABAC) | Système d’autorisation qui définit l’accès en fonction des attributs associés aux principaux de sécurité, aux ressources et à l’environnement. ABAC vous permet d’accorder à un principal de sécurité l’accès à une ressource selon les attributs. |
| Azure ABAC | Fait référence à l’implémentation d’ABAC pour Azure. |
| condition d’attribution de rôle | Autre vérification que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès encore plus précis. |
| attribut | Dans ce contexte, paire clé-valeur comme Project=Blue, dans laquelle Project est la clé de l’attribut et Blue la valeur de l’attribut. Les attributs et les étiquettes sont synonymes à des fins de contrôle d’accès. |
| expression | Instruction dans une condition qui prend la valeur true ou false. Le format d’une expression est le suivant : &lt;attribut&gt; &lt;opérateur&gt; &lt;valeur&gt;. |

## <a name="next-steps"></a>Étapes suivantes

- [Questions fréquentes (FAQ) sur les conditions d’attribution de rôle Azure (préversion)](conditions-faq.md)
- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant le portail Azure (préversion)](../storage/common/storage-auth-abac-portal.md)
