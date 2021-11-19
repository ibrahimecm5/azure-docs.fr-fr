---
title: Contrôle d’accès en fonction du rôle (RBAC) personnalisé pour les développeurs d’applications - Plateforme d’identités Microsoft
description: Découvrez ce qu’est le contrôle d’accès en fonction du rôle personnalisé et pourquoi il est important de l’implémenter dans vos applications.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/15/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: john.garland, maggie.marxen, ian.bennett, marsma
ms.openlocfilehash: fc1154965b037fbc57dcafa30f08fd951721bf6f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486740"
---
# <a name="role-based-access-control-for-application-developers"></a>Contrôle d’accès en fonction du rôle pour les développeurs d’applications

Le contrôle d’accès en fonction du rôle (RBAC) permet à certains utilisateurs ou groupes de disposer d’autorisations spécifiques concernant les ressources auxquelles ils ont accès, ce qu’ils peuvent faire avec ces ressources et qui gère quelles ressources. Le contrôle d’accès en fonction du rôle à l’application est différent du [Contrôle d’accès en fonction du rôle d’Azure](../../role-based-access-control/overview.md) et du [Contrôle d’accès en fonction du rôle d’Azure AD](../roles/custom-overview.md#understand-azure-ad-role-based-access-control). Les rôles personnalisés Azure et les rôles intégrés font tous deux partie du contrôle d’accès en fonction du rôle Azure, qui vous aide à gérer les ressources Azure. Le RBAC d’Azure AD vous permet de gérer les ressources Azure AD. Cet article explique le contrôle d’accès en fonction du rôle propre à l’application.


## <a name="what-are-roles"></a>Que sont les rôles ?

Le contrôle d’accès en fonction du rôle (RBAC) est un mécanisme populaire pour appliquer l’autorisation dans des applications. Lorsque vous utilisez le contrôle d’accès en fonction du rôle, le développeur de l’application définit des rôles plutôt que d’autoriser des utilisateurs ou des groupes individuels. L’administrateur peut alors attribuer des rôles aux différents utilisateurs et groupes pour contrôler l’accès au contenu et aux fonctionnalités.

Le contrôle d’accès en fonction du rôle vous aide en tant que développeur d’applications à gérer les ressources et ce que les utilisateurs peuvent en faire. Le contrôle d’accès en fonction du rôle permet également au développeur de l’application de contrôler les zones auxquelles les utilisateurs ont accès. Alors que les administrateurs peuvent contrôler les utilisateurs ayant accès à une application à l’aide de la propriété *attribution d’utilisateurs requise*, les développeurs doivent prendre en compte des utilisateurs spécifiques dans l’application et ce que les utilisateurs peuvent faire dans l’application.

En tant que développeur d’applications, vous devez d’abord créer une définition de rôle dans la section inscription de l’application dans le centre d’administration d’Azure AD. La définition de rôle comprend une valeur qui est retournée pour les utilisateurs affectés à ce rôle. Le développeur peut ensuite utiliser cette valeur pour implémenter la logique d’application afin de déterminer ce que ces utilisateurs peuvent ou non faire dans une application.

## <a name="options-for-adding-rbac-to-apps"></a>Options d’ajout du contrôle d’accès en fonction du rôle aux applications

Plusieurs points doivent être pris en compte lors de l’ajoute de l’autorisation de contrôle d’accès en fonction du rôle dans une application. En voici quelques-uns :
- Définition des rôles requis par les besoins d’autorisation d’une application. 
- L’application, le stockage et la récupération des rôles pertinents pour les utilisateurs authentifiés. 
- Impact sur le comportement souhaité de l’application en fonction des rôles attribués à l’utilisateur actuel. 

Une fois que vous avez défini les rôles, la plateforme d’identité Microsoft prend en charge plusieurs solutions qui peuvent être utilisées pour appliquer, stocker et récupérer les informations de rôle pour les utilisateurs authentifiés. Ces solutions incluent les rôles d’application, les groupes Azure AD et l’utilisation de banques de données personnalisées pour les informations de rôle d’utilisateur.

Les développeurs ont la possibilité de fournir leur propre implémentation pour la façon dont les attributions de rôles doivent être interprétées comme des permissions d’application. Cela peut impliquer l’utilisation d’un intergiciel ou d’autres fonctionnalités fournies par la plateforme de leurs applications ou par les bibliothèques associées. En général, les applications reçoivent les informations de rôle d’utilisateur sous forme de revendications et décident des autorisations de l’utilisateur en fonction de ces revendications.

### <a name="app-roles"></a>Rôles d'application

Azure AD prend en charge la déclaration des rôles d’application pour une inscription d’application. Quand un utilisateur se connecte à une application, Azure AD inclut une [revendication de rôle](./access-tokens.md#payload-claims) pour chaque rôle que l’utilisateur a accordé pour cette application. Les applications qui reçoivent des jetons qui contiennent ces revendications peuvent ensuite utiliser ces informations pour déterminer les autorisations que l’utilisateur peut exercer en fonction des rôles qui lui sont attribués.

### <a name="groups"></a>Groupes

Les développeurs peuvent également utiliser des [groupes Azure AD](../fundamentals/active-directory-manage-groups.md) pour implémenter le contrôle d’accès en fonction du rôle dans leurs applications, où les appartenances des utilisateurs dans des groupes spécifiques sont interprétées comme leurs appartenances aux rôles. Lorsque vous utilisez des groupes Azure AD, Azure AD inclut une [revendication de groupes](./access-tokens.md#payload-claims) qui inclut les identificateurs de tous les groupes auxquels l’utilisateur est affecté dans le client Azure AD actuel. Les applications qui reçoivent des jetons qui contiennent ces revendications peuvent ensuite utiliser ces informations pour déterminer les autorisations que l’utilisateur peut exercer en fonction des rôles qui lui sont attribués.

> [!IMPORTANT]
> Lorsque vous travaillez avec des groupes, les développeurs doivent être au fait du concept de [revendication de dépassement](./access-tokens.md#payload-claims). Par défaut, si un utilisateur est membre de plus de la limite de dépassement (150 pour les jetons SAML, 200 pour les jetons JWT et 6 avec l’utilisation du flux implicite), Azure AD n’émet pas la revendication des groupes dans le jeton. Au lieu de cela, il inclut une « revendication de dépassement » dans le jeton, qui indique que le consommateur du jeton doit interroger l’API Graph pour récupérer les appartenances aux groupes de l’utilisateur. Pour plus d’informations sur l’utilisation des revendications de dépassement, consultez [Revendications dans les jetons d’accès](./access-tokens.md#claims-in-access-tokens). Il est possible d’émettre uniquement des groupes qui sont assignés à une application, bien que l’[attribution basée sur les groupes](../manage-apps/assign-user-or-group-access-portal.md) exige l’édition Azure Active Directory Premium P1 ou P2.

### <a name="custom-data-store"></a>Magasin de données personnalisé

Les rôles d’application et les groupes stockent des informations sur les attributions d’utilisateurs dans Azure Active Directory. Une autre option pour gérer les informations de rôle d’utilisateur accessibles aux développeurs consiste à conserver les informations en dehors de l’annuaire dans un magasin de données personnalisé. Par exemple, dans une base de données SQL, un stockage de table Azure ou l’API Table Azure Cosmos DB.

L’utilisation du stockage personnalisé permet aux développeurs d’appliquer des personnalisations et des contrôles supplémentaires sur la façon d’attribuer des rôles aux utilisateurs et de les représenter. Toutefois, cette flexibilité supplémentaire découle également sur une plus grande responsabilité. Par exemple, aucun mécanisme n’est actuellement disponible pour inclure ces informations dans les jetons retournés par Azure AD. Si les développeurs placent les informations de rôle dans un magasin de données personnalisé, ils doivent faire en sorte que les applications récupèrent ces rôles. Cette opération s’effectue généralement à l’aide de points d’extensibilité définis dans l’intergiciel disponible pour la plateforme utilisée pour développer l’application. En outre, les développeurs sont responsables de la sécurisation du magasin de données personnalisé.

Avec les [stratégies personnalisées Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md), il est possible d’interagir avec les magasins de données personnalisés et d’inclure des revendications personnalisées dans un jeton.

## <a name="choosing-an-approach"></a>Choix de l’approche

En général, la solution conseillée est d’utiliser les rôles d’application. Les rôles d’application fournissent le modèle de programmation le plus simple et sont destinés aux implémentations du contrôle d’accès en fonction du rôle. Toutefois, certaines exigences spécifiques à l’application peuvent faire qu’une autre approche serait une meilleure solution.

Les développeurs peuvent utiliser les rôles d’application pour contrôler si un utilisateur peut se connecter à une application ou si une application peut obtenir un jeton d’accès pour une API web. Les rôles d’application ont la faveur des développeurs par rapport aux groupes Azure AD lorsqu’ils souhaitent décrire et contrôler eux-mêmes les paramètres d’autorisation dans leur application. Par exemple, une application qui utilise des groupes pour l’autorisation s’arrêtera au locataire suivant, car l’ID de groupe et le nom peuvent être différents. Une application utilisant des rôles d’application reste sûre. En fait, l’affectation de groupes à des rôles d’application est courante avec les applications SaaS pour les mêmes raisons.

Bien qu’il soit possible d’utiliser des rôles ou des groupes d’application pour l’autorisation, les principales différences entre ces possibilités peuvent déterminer la meilleure solution pour un scénario donné.

|          |Rôles d’application |Groupes Azure AD |Magasin de données personnalisé|
|----------|-----------|------------|-----------------|
|**Modèle de programmation** |**Plus simple**. Les groupes sont spécifiques à une application et définis dans l’inscription de l’application. Ils suivent l’application.|**Plus complexe**. Les ID de groupe varient entre les locataires et les revendications de dépassement peuvent être prises en compte. Les groupes ne sont pas spécifiques à une application, mais à un locataire Azure AD.|**Plus complexe**. Les développeurs doivent implémenter des moyens par lesquels les informations de rôle sont stockées et récupérées.|
|**Les valeurs de rôle sont statiques entre les locataires Azure AD**|Oui  |Non |Dépend de l’implémentation.|
|**Les valeurs de rôle peuvent être utilisées dans plusieurs applications**|Non. Sauf si la configuration de rôle est dupliquée dans chaque inscription d’application.|Oui |Oui |
|**Informations stockées dans l’annuaire**|Oui  |Oui |Non |
|**Informations fournies via des jetons**|Oui (revendication de rôles)  |Oui (en cas de dépassement, les *revendications de groupe* peuvent doivent être récupérées lors de l’exécution.) |Non. Récupéré au moment de l’exécution via du code personnalisé. |
|**Durée de vie**|Réside dans l’inscription de l’application dans l’annuaire. Supprimé lorsque l’inscription de l’application est supprimée.|Réside dans l’annuaire. Reste intact même lorsque l’inscription de l’application est supprimée. |Réside dans le magasin de données personnalisé. Non lié à l’inscription de l’application.|


## <a name="next-steps"></a>Étapes suivantes

- [Procédure : ajouter des rôles d’application dans votre application et les recevoir dans le jeton](./howto-add-app-roles-in-azure-ad-apps.md).
- [Inscrire une application à l’aide de la plateforme d’identités Microsoft](./quickstart-register-app.md).
- [Bonnes pratiques pour la sécurité du contrôle d’accès et de gestion des identités Azure](../../security/fundamentals/identity-management-best-practices.md).