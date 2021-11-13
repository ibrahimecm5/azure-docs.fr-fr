---
title: Solutions courantes de gestion multilocataire d’utilisateurs dans Azure Active Directory
description: Explorer les solutions courantes utilisées pour configurer l’accès utilisateur sur les locataires Azure Active Directory avec des comptes invités
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48412e7ad5c7ff90f1e5b89542f55b57fec4a534
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270917"
---
# <a name="common-solutions-for-multi-tenant-user-management"></a>Solutions courantes de gestion multilocataire d’utilisateurs

Il existe deux problèmes spécifiques que nos clients ont résolus à l’aide des outils actuels. Leurs solutions sont détaillées ci-dessous. Microsoft recommande un seul locataire dans la mesure du possible et travaille sur des outils pour résoudre ces problèmes plus facilement. Si la monolocation ne fonctionne pas pour votre scénario, ces solutions marchent actuellement pour les clients. 

## <a name="automatic-user-lifecycle-management-and-resource-allocation-across-tenants"></a>Gestion automatique du cycle de vie des utilisateurs et allocation des ressources entre les locataires

Un client fait l’acquisition d’un concurrent avec lequel il avait des relations commerciales étroites. Les organisations vont garder leur identité commerciale propre.

### <a name="current-state"></a>État actuel

Les organisations synchronisent actuellement leurs utilisateurs mutuels en tant qu’objets contact de courrier pour les faire figurer dans leurs répertoires à chacune. 

* Chaque locataire de ressource a un objet contact de courrier activé pour tous les utilisateurs de l’autre locataire.

* Aucun accès aux applications n’est possible sur les locataires.

### <a name="goals"></a>Objectifs

Ce client avait les objectifs suivants :

* Chaque utilisateur continue à apparaître dans la liste d’adresses globale de chaque organisation.

   * Les changements de cycle de vie des comptes d’utilisateur du locataire d’accueil sont répercutés automatiquement dans la liste d’adresses globale des locataires de ressources. 

   * Les changements d’attribut dans les locataires d’accueil (comme département, nom, adresse SMTP) sont répercutés automatiquement dans la liste d’adresses globale des locataires de ressources et la liste d’adresses globale principale.

* Les utilisateurs peuvent accéder aux applications et aux ressources dans le locataire de ressource.

* Les utilisateurs peuvent se servir des demandes d’accès aux ressources.

### <a name="solution-architecture"></a>Architecture de solution 

Les organisations utilisent une architecture point à point avec un moteur de synchronisation tel que MIM.

![Exemple d’architecture point à point](media/multi-tenant-common-solutions/point-to-point-sync.png)

Chaque administrateur de locataire procède aux opérations suivantes pour créer les objets utilisateur :

1. S’assurer que sa base de données d’utilisateurs est à jour.

1. [Déployer et configurer MIM](/microsoft-identity-manager/microsoft-identity-manager-deploy).

   1. Résoudre les objets contact existants.

   1. Créer des objets membres externes B2B pour les membres de l’autre locataire.

   1. Synchroniser les attributs d’objet utilisateur.

1. Déployer et configurer les packages d’accès de la [gestion des droits d’utilisation](../governance/entitlement-management-overview.md).

   1. Ressources à partager

   1. Stratégies d’expiration et de révision des accès

## <a name="sharing-on-premises-apps-across-tenants"></a>Partage d’applications locales entre les locataires

Ce client, avec plusieurs organisations paires, a besoin de partager des applications locales d’un des locataires.

### <a name="current-state"></a>État actuel

Plusieurs organisations paires synchronisent les utilisateurs invités B2B dans une topologie maillée, ce qui permet d’allouer des ressources à leurs applications cloud sur les locataires. Actuellement, elles

* Partagent des applications dans Azure AD.

* S’assurent que la gestion du cycle de vie des utilisateurs dans le locataire de ressource est automatisée en fonction du locataire d’accueil. Autrement dit, ajout, modification et suppression sont reflétés.

* Seuls les utilisateurs membres de la société A accèdent aux applications locales de la société A.

![Scénario multilocataire](media/multi-tenant-user-management-scenarios/mesh.png)

### <a name="goals"></a>Objectifs

Avec les fonctionnalités actuelles, elles souhaiteraient

* Fournir un accès aux ressources locales de la société A aux utilisateurs invités externes. 

* Applications avec authentification SAML

* Applications avec authentification Windows intégrée et Kerberos

### <a name="solution-architecture"></a>Architecture de solution

La société A fournit actuellement une authentification unique aux applications locales pour ses propres membres via Azure Application Proxy.

![Exemple d’accès aux applications](media/multi-tenant-common-solutions/app-access-scenario.png)

Pour permettre à leurs utilisateurs invités d’accéder aux mêmes applications locales, les administrateurs du locataire A vont :

1. [Configurer l’accès aux applications SAML](../external-identities/hybrid-cloud-to-on-premises.md#access-to-saml-apps).

2. [Configurer l’accès aux autres applications](../external-identities/hybrid-cloud-to-on-premises.md#access-to-iwa-and-kcd-apps).

3. Créer des utilisateurs invités locaux avec [MIM](../external-identities/hybrid-cloud-to-on-premises.md#create-b2b-guest-user-objects-through-mim) ou [PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=51495).

Pour plus d’informations sur la collaboration B2B, consultez

[Accorder aux utilisateurs B2B dans Azure AD l’accès à vos ressources locales](../external-identities/hybrid-cloud-to-on-premises.md)

[Azure Active Directory B2B Collaboration pour les organisations hybrides](../external-identities/hybrid-organizations.md)

## <a name="next-steps"></a>Étapes suivantes
[Présentation de la gestion multilocataire des utilisateurs](multi-tenant-user-management-introduction.md)

[Scénarios de gestion multilocataire d’utilisateurs finaux](multi-tenant-user-management-scenarios.md)

[Considérations courantes sur le multilocataire](multi-tenant-common-considerations.md)
