---
title: Classeur de rapport sur les opérations sensibles dans Azure AD | Microsoft Docs
description: Découvrez comment utiliser le classeur de rapport sur les opérations sensibles.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: a44aa07539be94aa645c95a6cee544bf92468bd7
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990556"
---
# <a name="sensitive-operations-report-workbook"></a>Classeur de rapport sur les opérations sensibles

En tant qu’administrateur informatique, vous devez être en mesure d’identifier les compromissions de votre environnement pour veiller à le maintenir dans un état sain. 

Le classeur de rapport sur les opérations sensibles est conçu pour aider à identifier l’activité suspecte des applications et des principaux de service susceptible d’indiquer des compromissions dans l’environnement.


Cet article vous donne une vue d’ensemble de ce classeur.


## <a name="description"></a>Description

![Catégorie du classeur](./media/workbook-sensitive-operations-report/workbook-category.png)

Ce classeur identifie les opérations sensibles récemment effectuées dans le locataire et susceptibles de compromettre le principal de service.

Si votre organisation découvre tout juste les classeurs Azure Monitor, vous devez intégrer vos journaux de connexion et d’audit Azure AD avec Azure Monitor pour pouvoir accéder au classeur. Vous pourrez ainsi stocker, interroger et visualiser vos journaux avec des classeurs pendant deux ans maximum. Seuls les événements de connexion et d’audit créés après l’intégration d’Azure Monitor sont stockés. Le classeur ne contient donc pas d’insights antérieurs à cette date. Découvrez les prérequis des classeurs Azure Monitor pour Azure Active Directory. Si vous avez déjà intégré vos journaux de connexion et d’audit Azure AD avec Azure Monitor, vous pouvez utiliser le classeur pour évaluer les informations passées. 
 
 

## <a name="sections"></a>Sections

Ce classeur est divisé en quatre sections :

![Sections du classeur](./media/workbook-sensitive-operations-report/workbook-sections.png)


- **Modification des informations d’identification/méthodes d’authentification des applications et des principaux de service** : ce rapport signale les acteurs qui ont récemment modifié les informations d’identification de nombreux principaux de service, ainsi que le nombre d’informations d’identification modifiées de chaque type.

- **Octroi de nouvelles autorisations aux principaux de service** : ce classeur met également en évidence les autorisations OAuth 2.0 récemment accordées aux principaux de service. 

- **Mise à jour des appartenances aux rôles d’annuaire et aux groupes des principaux de service**



- **Modification des paramètres de fédération** : ce rapport met en évidence les cas où un utilisateur ou une application modifie des paramètres de fédération sur un domaine. Par exemple, il signale quand un nouvel objet TrustedRealm des services de fédération Active Directory (AD FS, Active Directory Federated Services), par exemple un certificat de signature, est ajouté au domaine. La modification des paramètres de fédération de domaine doit être rare. 




### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Modification des informations d’identification/méthodes d’authentification des applications et des principaux de service

L’un des moyens les plus courants pour un attaquant de gagner en persistance dans l’environnement consiste à ajouter de nouvelles informations d’identification à des applications et principaux de service existants. Il peut ainsi s’authentifier sous l’identité de l’application ou du principal de service cible, ce qui lui accorde l’accès à toutes les ressources pour lesquelles il dispose d’autorisations.

Cette section contient les données suivantes pour vous aider à détecter les scénarios correspondants :

- Ensemble des nouvelles informations d’identification ajoutées aux applications et aux principaux de service, avec le type d’informations d’identification

- Principaux acteurs et nombre d’informations d’identification modifiées

- Chronologie de toutes les modifications apportées aux informations d’identification



### <a name="new-permissions-granted-to-service-principals"></a>Octroi de nouvelles autorisations aux principaux de service

Dans le cas où l’attaquant ne parvient pas à trouver un principal de service ni une application doté d’un ensemble d’autorisations à privilège élevé qui lui permette d’obtenir un accès, il tente souvent d’ajouter les autorisations à un autre principal de service ou à une autre application.

Cette section comprend une répartition des autorisations AppOnly accordées aux principaux de service existants. Les administrateurs doivent examiner tous les cas où des autorisations élevées excessives sont accordées, notamment dans Exchange Online, Microsoft Graph et Azure AD Graph.


### <a name="directory-role-and-group-membership-updates-for-service-principals"></a>Mise à jour des appartenances aux rôles d’annuaire et aux groupes des principaux de service 

Dans la logique de l’attaquant qui ajoute de nouvelles autorisations à des applications et principaux de service existants, une autre approche consiste à les ajouter à des rôles d’annuaire ou groupes existants.

Cette section contient une vue d’ensemble de toutes les modifications apportées aux appartenances des principaux de service. Elle doit être examinée pour tout ajout à des rôles et groupes dotés de privilèges élevés.



### <a name="modified-federation-settings"></a>Modification des paramètres de fédération

Il existe une autre approche courante pour prendre pied à long terme dans l’environnement :

- Modifier les approbations des domaines fédérés du locataire
- Ajouter un fournisseur d’identité SAML supplémentaire contrôlé par l’attaquant aux sources d’authentification approuvées 

Cette section comprend les données suivantes :

- Modifications apportées aux approbations de fédération de domaine existantes

- Ajout de nouveaux domaines et approbations


  


## <a name="filters"></a>Filtres

Ce paragraphe donne la liste des filtres pris en charge pour chaque section.


### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>Modification des informations d’identification/méthodes d’authentification des applications et des principaux de service

- Plage temporelle
- Nom d’opération
- Informations d'identification
- Acteur
- Exclusion de l’acteur


### <a name="new-permissions-granted-to-service-principals"></a>Octroi de nouvelles autorisations aux principaux de service

- Plage temporelle
- Application cliente
- Ressource

### <a name="directory-role-and-group-membership-updates-to-service-principals"></a>Mise à jour des appartenances aux rôles d’annuaire et aux groupes des principaux de service

- Plage temporelle
- Opération
- Utilisateur ou application à l’origine de l’opération

### <a name="modified-federation-settings"></a>Modification des paramètres de fédération

- Plage temporelle
- Opération
- Utilisateur ou application à l’origine de l’opération




## <a name="best-practices"></a>Meilleures pratiques


**Utilisez :**
 
- **Modification des informations d’identification des applications et des principaux de service** pour guetter les informations d’identification ajoutées à des principaux de service rarement utilisés dans l’organisation. Servez-vous des filtres présents dans cette section pour examiner plus en détail les acteurs suspects et les principaux de service modifiés.


- **Octroi de nouvelles autorisations aux principaux de service** pour guetter les autorisations étendues ou excessives ajoutées aux principaux de service par des acteurs potentiellement compromis.  

- **Modification des paramètres de fédération** pour vérifier que le domaine/l’URL cible ajouté ou modifié constitue un comportement administrateur légitime. Les actions qui modifient ou ajoutent des approbations de fédération de domaine sont rares. Elles doivent être traitées comme des actions haute fidélité pour être examinées dès que possible.





## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser des classeurs Azure AD](howto-use-azure-monitor-workbooks.md)
