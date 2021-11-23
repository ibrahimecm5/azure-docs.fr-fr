---
title: Sécurité, gouvernance et abonnements Dev/Test Azure
description: Gérez la sécurité et la gouvernance au sein des abonnements de développement et de test de votre organisation.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 6f3d9bae37e49c23dacc9a44ed4e0006df95ec29
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305880"
---
# <a name="security-within-azure-devtest-subscription"></a>Sécurité au sein d’un abonnement Dev/Test Azure

La protection de vos ressources dépend d’un effort conjoint entre votre fournisseur de cloud, Azure et vous, le client. Les abonnements Dev/Test Azure et [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md) vous offrent les outils nécessaires pour renforcer votre réseau, sécuriser vos services et garantir l’optimisation de votre posture de sécurité.  

Les outils importants compris dans les abonnements Dev/Test Azure vous aident à créer un accès sécurisé à vos ressources :  

- Groupes d'administration Azure  
- Azure Lighthouse  
- Surveillance des crédits  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Groupes d'administration Azure  

Lors de l’activation et de la configuration de vos abonnements Dev/Test Azure, Azure déploie une hiérarchie de ressources par défaut pour gérer les identités et l’accès aux ressources dans un domaine Azure Active Directory unique. La hiérarchie des ressources permet à votre organisation de configurer de puissants périmètres de sécurité pour vos ressources et vos utilisateurs.  

![Capture d’écran des Groupes d’administration Azure](media/concepts-security-governance-devtest/access-management-groups.png "Hiérarchie de ressources Azure par défaut.")  

Votre hiérarchie de ressources est un ensemble constitué de vos ressources, groupes de ressources, abonnements, groupes d’administration et autre locataire. La mise à jour et la modification de ces paramètres dans les rôles personnalisés Azure ou les attributions de stratégie Azure peuvent avoir une incidence sur toutes les ressources de votre hiérarchie de ressources. Il est important de protéger la hiérarchie de ressources contre les modifications susceptibles d’avoir des répercussions négatives sur toutes les ressources.  

Les [groupes d’administration Azure](../../governance/management-groups/overview.md) représentent un aspect important de l’administration de l’accès et de la protection de vos ressources dans un seul locataire. Les groupes d’administration Azure vous permettent de définir des quotas, des stratégies Azure et des stratégies de sécurité sur différents types d’abonnements. Ces groupes sont un composant essentiel du développement de la sécurité pour les abonnements de développement et de test de votre organisation.  

![Capture d’écran des regroupements d’organisation et de gouvernance Azure](media/concepts-security-governance-devtest/orgs-and-governance.png "Comment les groupes d’administration Azure s’intègrent à la gouvernance globale.")

Comme vous pouvez le voir ci-dessus, l’utilisation de groupes d’administration modifie la hiérarchie par défaut et ajoute un niveau pour les groupes d’administration. Ce comportement peut potentiellement créer des conditions et des failles imprévues dans la sécurité si vous ne suivez pas le [processus approprié pour protéger votre hiérarchie de ressources](../../governance/management-groups/how-to/protect-resource-hierarchy.md)  

## <a name="why-are-azure-management-groups-useful"></a>En quoi les groupes d’administration Azure sont-ils utiles ?  

Lors du développement de stratégies de sécurité pour les abonnements Dev/Test de votre organisation, vous pouvez choisir d’avoir plusieurs abonnements Dev/Test par unité d’organisation ou métier. Vous pouvez apercevoir un visuel du regroupement d’administration ci-dessous.  

![Diagramme des regroupements de gestion des abonnements pour plusieurs abonnements au sein d’une organisation.](media/concepts-security-governance-devtest/access-management-groups.png "Diagramme des regroupements d’administration pour plusieurs abonnements au sein d’une organisation.")  

Vous pouvez également choisir d’avoir un abonnement Dev/Test pour toutes vos unités différentes.  

Vos groupes d’administration et abonnements Dev/Test Azure jouent un rôle de barrière de sécurité au sein de la structure de votre organisation.  

Cette barrière de sécurité comporte deux composants :  

- Identité et accès : vous devrez peut-être segmenter l’accès à des ressources spécifiques  
- Données : différents abonnements pour les ressources qui accèdent aux informations personnelles  

## <a name="using-azure-active-directory-tenants"></a>Utilisation de locataires Azure Active Directory  

[Un locataire](../../active-directory/develop/quickstart-create-new-tenant.md) est une instance Azure AD dédiée, reçue par une organisation ou un développeur d’applications lorsque l’organisation ou le développeur d’applications crée une relation avec Microsoft (l’inscription à Azure, Microsoft Intune ou Microsoft 365, par exemple).  

Chaque locataire Azure AD est indépendant des autres clients Azure AD. Chaque locataire Azure AD dispose de sa propre représentation d’identités professionnelles et scolaires, d’identités de consommateurs (dans le cas d’un locataire Azure AD B2C) et d’inscriptions d’applications. Dans votre locataire, une inscription d’application peut autoriser des authentifications à partir de comptes seulement au sein de votre locataire ou de tous les locataires.  

Si vous avez besoin de séparer l’infrastructure d’identité de votre organisation au-delà des groupes d’administration au sein d’un seul locataire, vous pouvez également créer d’autres locataires avec leur propre hiérarchie de ressources.  

Créer un locataire Azure AD constitue un moyen simple de créer des ressources et des utilisateurs distincts.  

### <a name="create-a-new-azure-ad-tenant"></a>Créer un nouveau locataire Azure AD  

Si vous ne disposez pas déjà d’un locataire Azure AD, ou si vous souhaitez en créer un autre à des fins de développement, consultez le [guide de démarrage rapide](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  ou suivez l’ [expérience de création de répertoire](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Vous devez fournir les informations suivantes pour créer un locataire :  

- **Nom de l’organisation**  
- **Domaine initial**  : est ajouté à /*.onmicrosoft.com. Vous pourrez personnaliser le domaine ultérieurement.  
- **Pays ou région**  

 [En savoir plus sur la création et la configuration des locataires Azure AD](../../active-directory/develop/quickstart-create-new-tenant.md)  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>Utilisation d’Azure Lighthouse pour gérer plusieurs locataires  

Azure Lighthouse offre une gestion inter- et multi-locataire, ce qui permet d’améliorer l’automatisation, la scalabilité et la gouvernance entre les ressources et les locataires. Les fournisseurs de services peuvent proposer des services managés à l’aide des outils de gestion complets et robustes intégrés dans la plateforme Azure. Les clients conservent le contrôle des personnes autorisées à accéder à leur locataire, des ressources auxquelles elles peuvent accéder et des actions qu’elles peuvent entreprendre.  

Un scénario courant pour Azure Lighthouse consiste à gérer des ressources dans les locataires Azure Active Directory de ses clients. Cependant, les fonctionnalités d’Azure Lighthouse peuvent également être utilisées pour simplifier la gestion inter-locataire au sein d’une entreprise qui utilise plusieurs locataires Azure AD.  

Pour la plupart des organisations, la gestion est plus facile avec un seul locataire Azure AD. Le fait de disposer de toutes les ressources dans un seul et même locataire permet de centraliser les tâches de gestion par des utilisateurs désignés, des groupes d’utilisateurs ou des principaux de service au sein de ce locataire.  

Dans les cas où une architecture multilocataire est nécessaire, Azure Lighthouse contribue à centraliser et simplifier les opérations de gestion. En utilisant la gestion déléguée des ressources Azure, les utilisateurs d’un locataire gérant peuvent effectuer des fonctions de gestion inter-locataires de manière centralisée et évolutive.  

[Plus de ressources de sécurité](../../security-center/security-center-introduction.md)
