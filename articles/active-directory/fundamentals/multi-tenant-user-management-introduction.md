---
title: Configuration de la gestion multilocataire des utilisateurs dans Azure Active Directory
description: Explorer les différents modèles utilisés pour configurer l’accès utilisateur sur les locataires Azure Active Directory avec des comptes invités
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
ms.openlocfilehash: 379135bc446dbef6a2145a6339b5ddd318a5f579
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270783"
---
# <a name="multi-tenant-user-management"></a>Gestion multilocataire des utilisateurs 

Le provisionnement d’utilisateurs dans un seul locataire Azure Active Directory (Azure AD) fournit une vue unifiée des ressources et un seul ensemble de stratégies et de contrôles. Cette approche permet une gestion cohérente du cycle de vie des utilisateurs. 

**Microsoft recommande un seul locataire dans la mesure du possible**. Toutefois, il n’est pas toujours possible de tout regrouper tout de suite dans un seul locataire Azure AD. Les organisations multilocataires peuvent englober deux locataires Azure AD ou plus. Ce scénario peut entraîner des exigences uniques en termes de gestion et de collaboration entre locataires.

Les organisations peuvent avoir des exigences de gestion des identités et des accès (IAM) qui se compliquent avec :

* les fusions, les acquisitions et les cessions.

* la collaboration entre les clouds publics, souverains et/ou régionaux.

* les structures politiques ou organisationnelles qui interdisent de tout regrouper sur un seul locataire Azure AD.

Ce guide fournit également des conseils pour vous aider à atteindre un état cohérent de la gestion du cycle de vie des utilisateurs. À savoir, le provisionnement, la gestion et le déprovisionnement d’utilisateurs dans les locataires à l’aide des outils disponibles avec Azure. Plus précisément, en utilisant [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md).

## <a name="azure-ad-b2b-collaboration"></a>Azure AD B2B Collaboration

Azure AD Collaboration vous permet de partager de façon sécurisée les applications et services de votre entreprise avec des utilisateurs invités externes. Les utilisateurs peuvent venir de n’importe quelle organisation. L’utilisation d’Azure AD B2B Collaboration vous permet de garder le contrôle de l’accès à votre environnement informatique et à vos données. Vous pouvez aussi utiliser Azure AD B2B Collaboration pour fournir un accès invité aux utilisateurs internes. Jusqu’ici, l’accès des utilisateurs invités B2B était utilisé pour autoriser l’accès à des utilisateurs externes qui n’étaient pas gérés par votre propre organisation. Toutefois, l’accès des utilisateurs invités peut également servir pour gérer l’accès à plusieurs locataires gérés par votre organisation. Même si ce n’est pas vraiment une solution B2B, Azure AD B2B Collaboration peut être utilisé pour gérer les utilisateurs internes dans votre scénario multilocataire.

Les liens suivants fournissent des informations supplémentaires que vous pouvez consulter pour en savoir plus sur Azure AD B2B Collaboration :

| Article| Description |
| - |-|
| **Articles conceptuels**|  |
| [Bonnes pratiques B2B](../external-identities/b2b-fundamentals.md)| Recommandations pour l’expérience la plus fluide pour vos utilisateurs et vos administrateurs.|  
| [Partage externe d’Office 365 et de B2B](../external-identities/o365-external-user.md)| Explique les similitudes et les différences des partages de ressources via B2B, Office 365 et SharePoint/OneDrive.|  
| [Propriétés sur un utilisateur Azure AD B2B Collaboration](../external-identities/user-properties.md)| Décrit les propriétés et les états de l’objet utilisateur invité B2B dans Azure Active Directory (Azure AD). La description fournit des détails avant et après l’utilisation d’invitation.|  
| [Jetons utilisateur B2B](../external-identities/user-token.md)| Fournit des exemples de jetons de porteur pour un utilisateur invité B2B.|  
| [Accès conditionnel pour B2B](../external-identities/conditional-access.md)| Décrit comment fonctionnent l’accès conditionnel et MFA pour les utilisateurs invités.|  
| **Articles sur les procédures**|  |
| [Utiliser PowerShell pour inviter en bloc des utilisateurs Azure AD B2B Collaboration](../external-identities/bulk-invite-powershell.md)| Découvrez comment utiliser PowerShell pour envoyer des invitations en bloc à des utilisateurs externes.|
| [Appliquer l’authentification multifacteur pour les utilisateurs invités B2B](../external-identities/b2b-tutorial-require-mfa.md)|Utilisez des stratégies d’accès conditionnel et MFA afin d’appliquer des niveaux d’authentification aux locataires, applications ou utilisateurs invités individuels. |
| [Authentification par envoi d’un code secret à usage unique par e-mail](../external-identities/one-time-passcode.md)| La fonctionnalité de code secret à usage unique d’e-mail authentifie les utilisateurs invités B2B lorsqu’ils ne peuvent pas être authentifiés via d’autres moyens, comme Azure AD, un compte Microsoft (MSA) ou la fédération Google.|

## <a name="terminology"></a>Terminologie

Ces termes sont utilisés tout au long de ce contenu :

* **Locataire de ressources** : locataire Azure AD contenant les ressources que les utilisateurs veulent partager avec d’autres personnes.

* **Locataire d’accueil** : locataire Azure AD contenant les utilisateurs qui ont besoin d’accéder aux ressources du locataire de ressources.

* **Gestion du cycle de vie des utilisateurs** : processus de provisionnement, de gestion et de déprovisionnement de l’accès utilisateur aux ressources.

* **Liste d’adresses globale unifiée** : chaque utilisateur de chaque locataire peut voir les utilisateurs de chaque organisation dans sa liste d’adresses globale (GAL, global address list).

## <a name="deciding-how-to-meet-your-requirements"></a>Décider du moyen de répondre à vos exigences

Les exigences uniques de votre organisation déterminent votre stratégie pour gérer les utilisateurs dans les locataires. Pour créer une stratégie efficace, vous devez prendre en compte :

* Le nombre de locataires

* Le type d’organisation

* Les topologies actuelles

* Les besoins spécifiques de synchronisation des utilisateurs 

### <a name="common-requirements"></a>Besoins courants

De nombreuses organisations se concentrent d’abord sur les exigences qu’elles souhaitent voir en place pour une collaboration immédiate. Parfois appelées Exigences Jour 1, ces exigences ont pour but de permettre aux utilisateurs finaux de fusionner en douceur sans les empêcher de continuer à générer de la valeur pour l’entreprise. Au fur et à mesure que vous définissez vos exigences Jour 1 et administratives, pensez à inclure les objectifs suivants : 

| Catégories d’exigence| Besoins courants|
| ------------ | - |
| **Exigences de communication**|  |
| Liste d’adresses globale unifiée| Chaque utilisateur peut voir tous les autres utilisateurs de la liste d’adresses globale dans son locataire d’accueil. |
| Informations sur la disponibilité| Permettre aux utilisateurs de découvrir la disponibilité de chacun. Pour ce faire, utilisez les [Relations organisationnelles dans Exchange Online](/exchange/sharing/organization-relationships/create-an-organization-relationship).|
| Conversation instantanée et présence| Permettre aux utilisateurs de déterminer qui est là et d’envoyer un message instantané. Vous pouvez le configurer via l’[accès externe dans Microsoft Teams](/microsoftteams/manage-external-access).|
| Réserver des ressources telles que des salles de réunion| Permettre aux utilisateurs de réserver des salles de conférence ou d’autres ressources au sein de l’organisation. Actuellement, la réservation d’une salle de conférence multilocataire n’est pas possible.|
‎Domaine de courrier unique| Permettre à tous les utilisateurs d’envoyer et de recevoir des e-mails à partir d’un seul domaine de courrier, par exemple *users@contoso.com* . Actuellement, l’envoi nécessite une solution de réécriture d’adresse tierce.|
| **Condition d’accès**|  |
| Accès aux documents| Permettre aux utilisateurs de partager des documents à partir de SharePoint, OneDrive et Teams. |
| Administration| Autoriser les administrateurs à gérer la configuration des abonnements et des services déployés sur plusieurs locataires. |
| Accès aux applications| Autoriser les utilisateurs finaux à accéder aux applications de l’organisation. |
| Authentification unique| Permettre aux utilisateurs d’accéder aux ressources de l’organisation sans avoir à entrer d’autres informations d’identification.|

### <a name="patterns-for-account-creation"></a>Modèles pour la création de comptes 

Plusieurs mécanismes sont disponibles pour créer et gérer le cycle de vie de vos comptes d’utilisateur invités. Microsoft a distillé trois modèles courants. Vous pouvez utiliser les modèles pour vous aider à définir et à implémenter vos exigences. Choisissez celui qui est le mieux adapté à votre scénario, puis concentrez-vous sur ses détails.

| Mécanisme |  Description | Mieux quand |
| - | - | - |
| [À l’initiative de l’utilisateur](multi-tenant-user-management-scenarios.md#end-user-initiated-scenario) | Les administrateurs de locataires de ressources délèguent la capacité à inviter des utilisateurs invités sur le locataire, une application ou une ressource aux utilisateurs du locataire de ressources. Les utilisateurs du locataire d’accueil sont invités ou s’inscrivent individuellement. |  <li>Les utilisateurs ont besoin d’un accès improvisé aux ressources. <li>Aucune synchronisation automatique des attributs utilisateur n’est nécessaire.<li>Une liste d’adresses globale unifiée n’est pas nécessaire. |
|[Avec script](multi-tenant-user-management-scenarios.md#scripted-scenario) | Les administrateurs de locataires de ressources déploient un processus d’extraction par script pour automatiser la découverte et le provisionnement des utilisateurs invités afin de prendre en charge les scénarios de partage. |  <li>Il n’y a pas plus de deux locataires.<li>Aucune synchronisation automatique des attributs utilisateur n’est nécessaire.<li>Les utilisateurs ont besoin d’un accès préconfiguré (pas improvisé) aux ressources.|
|[Automatisé](multi-tenant-user-management-scenarios.md#automated-scenario)|Les administrateurs de locataires de ressources utilisent un système de provisionnement d’identités pour automatiser les processus de provisionnement et de déprovisionnement. |  <li>Toute la gestion du cycle de vie des identités avec provisionnement et déprovisionnement doit être automatisée.<li>La synchronisation des attributs est nécessaire pour renseigner les détails de la liste d’adresses globale et prendre en charge les scénarios de droits d’utilisation dynamiques.<li>Les utilisateurs ont besoin d’un accès préconfiguré (et non ad hoc) aux ressources le « Jour 1 ».|

  
## <a name="next-steps"></a>Étapes suivantes

[Scénarios de gestion d’utilisateurs multilocataire](multi-tenant-user-management-scenarios.md)

[Considérations courantes sur le multilocataire](multi-tenant-common-considerations.md)

[Solutions multilocataires courantes](multi-tenant-common-solutions.md)
