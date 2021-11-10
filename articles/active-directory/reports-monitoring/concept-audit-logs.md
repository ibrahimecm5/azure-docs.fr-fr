---
title: Journaux d’audit dans Azure Active Directory | Microsoft Docs
description: Présentation des journaux d’audit dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: d551124491c33cd74f3fbdf1f4b401ada99399d4
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997169"
---
# <a name="audit-logs-in-azure-active-directory"></a>Journaux d’audit dans Azure Active Directory 

En tant qu’administrateur informatique, vous souhaitez savoir comment fonctionne votre environnement informatique. Les informations sur l’intégrité de votre système vous permettent d’évaluer si vous devez répondre aux problèmes potentiels et, le cas échéant, de quelle manière. 

Pour vous aider à atteindre cet objectif, le portail Azure Active Directory vous donne accès à trois journaux d’activité :

- **[Connexions](concept-sign-ins.md)**  : Informations sur les connexions et la manière dont vos ressources sont utilisées par vos utilisateurs.
- **[Audit](concept-audit-logs.md)**  : Informations sur les modifications appliquées à votre locataire, telles que la gestion des utilisateurs et des groupes ou les mises à jour appliquées aux ressources de votre locataire.
- **[Approvisionnement](concept-provisioning-logs.md)**  : Activités réalisées par le service d’approvisionnement, telles que la création d’un groupe dans ServiceNow ou l’importation d’un utilisateur à partir de Workday.

Cet article présente une vue d’ensemble des journaux d’audit.


## <a name="what-is-it"></a>Qu’est-ce que c’est ?

Avec les journaux d’audit dans Azure AD, vous accédez aux enregistrements des activités du système pour la conformité.
Les affichages les plus courants de ce journal sont basés sur les catégories suivantes :

- User Management

- Gestion des groupes
 
- Gestion des applications  


L’affichage centré sur l’utilisateur vous permet d’obtenir des réponses à des questions, telles que :

- Quels types de mises à jour ont été appliqués aux utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Combien de mots de passe ont été modifiés ?

- Qu’a fait un administrateur dans un répertoire ?


L’affichage centré sur les groupes vous permet d’obtenir des réponses à des questions, telles que :

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes comportant des modifications d’adhésion ?

- Les propriétaires de groupe ont-ils été modifiés ?

- Quelles licences ont été attribuées à un groupe ou un utilisateur ?

L’affichage basé sur les applications vous permet d’obtenir des réponses à des questions telles que :

- Quelles applications ont été ajoutées ou mises à jour ?

- Quelles applications ont été supprimées ?

- Le principal de service d’une application a-t-il été modifié ?

- Les noms des applications ont-ils été modifiés ?

- Qui a donné son consentement à une application ?

 
## <a name="what-license-do-i-need"></a>De quelle licence ai-je besoin ?

Le rapport d’activité d’audit est disponible dans toutes les éditions d’Azure AD.

## <a name="who-can-access-it"></a>Qui peut y accéder ?

Pour accéder aux journaux d’audit, vous devez disposer d’un des rôles suivants : 

- Security Administrator
- Lecteur de sécurité
- Lecteur de rapport
- Lecteur général
- Administrateur général

## <a name="where-can-i-find-it"></a>Où puis-je le trouver ?

Le portail Azure vous offre plusieurs options pour accéder au journal. Par exemple, dans le menu Azure Active Directory, vous pouvez ouvrir le journal dans la section **Surveillance**.  

![Ouvrir les journaux d’audit](./media/concept-audit-logs/audit-logs-menu.png)

En outre, vous pouvez accéder directement aux journaux d’audit en cliquant sur [ce lien](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents).


Vous pouvez également accéder au journal d’audit par le biais de l’API Microsoft Graph.


## <a name="what-is-the-default-view"></a>Qu’est-ce que la vue par défaut ?

Un journal d’audit inclut un mode Liste par défaut, qui indique :

- la date et l’heure de l’occurrence
- le service qui a enregistré l’occurrence
- la catégorie et le nom de l’activité (*laquelle*) 
- l’état de l’activité (réussite ou échec)
- la cible
- l’initiateur/intervenant d’une activité (qui)

![Journaux d’audit](./media/concept-audit-logs/listview.png "Journaux d’audit")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Colonnes d’audit](./media/concept-audit-logs/columns.png "Colonnes d’audit")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Supprimer des champs](./media/concept-audit-logs/columnselect.png "Supprimer des champs")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![sélectionner un élément](./media/concept-audit-logs/details.png "Sélectionner un élément")

## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Vous pouvez filtrer les données d’audit des champs suivants :

- Service
- Category
- Activité
- Statut
- Cible
- Initié par (intervenant)
- Plage de dates

![Objet de filtre](./media/concept-audit-logs/filter.png "Filter (objet)")

Le filtre **Service** vous permet de sélectionner les services suivants dans une liste déroulante :

- Tous
- AAD Management UX
- Révisions d’accès
- Approvisionnement des comptes
- Application Proxy (Proxy d’application)
- Méthodes d’authentification
- B2C
- Accès conditionnel
- Annuaire principal
- Gestion des droits d’utilisation
- Authentification hybride
- Identity Protection
- Utilisateurs invités
- Service MIM
- MyApps
- PIM
- Gestion des groupes en libre-service
- Gestion des mots de passe en libre-service
- Conditions d’utilisation

Le filtre **Catégorie** vous permet de sélectionner un des filtres suivants :

- Tous
- AdministrativeUnit
- ApplicationManagement
- Authentification
- Autorisation
- Contact
- Appareil
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Étiquette
- Autres
- PermissionGrantPolicy
- Stratégie
- ResourceManagement
- RoleManagement
- UserManagement

Le filtre **Activité** est basé sur la catégorie et le type de ressource d’activité que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

Vous pouvez récupérer la liste de toutes les activités d’audit en utilisant l’API Graph : `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Le filtre **État** vous permet de filtrer en fonction de l’état d’une opération d’audit. Il peut s’agir d'un des états suivants :

- Tous
- Succès
- Échec

Le filtre **Cible** vous permet de rechercher une cible donnée par début de nom ou de nom d’utilisateur principal (UPN). Le nom et le nom d'utilisateur principal cibles sont sensibles à la casse. 

Le filtre **Initié par** vous permet de définir le début du nom d’un intervenant ou du nom d’utilisateur principal (UPN). Le nom et le nom d'utilisateur principal sont sensibles à la casse.

Le filtre **Plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 7 jours
- 24 heures
- Custom

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Vous pouvez également télécharger les données filtrées, jusqu’à 250 000 enregistrements, en sélectionnant le bouton **Télécharger**. Vous pouvez télécharger les journaux d’activité au format CSV ou JSON. Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).

![Télécharger des données](./media/concept-audit-logs/download.png "Télécharger des données")



## <a name="microsoft-365-activity-logs"></a>Journaux d’activité Microsoft 365

Vous pouvez consulter les journaux d’activité Microsoft 365 dans le [centre d’administration Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). Bien que les journaux d’activité Microsoft 365 et Azure AD partagent une grande partie des ressources du répertoire, seul le centre d’administration Microsoft 365 offre une vue complète des journaux d’activité Microsoft 365. 

Vous pouvez également accéder par programme aux journaux d’activité de Microsoft 365 en utilisant les [API de gestion Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview).

> [!NOTE]
> La plupart des abonnements Microsoft 365 autonomes ou regroupés ont des dépendances de serveur principal sur certains sous-systèmes au sein de la limite du datacenter Microsoft 365. Les dépendances requièrent une écriture différée des informations pour assurer la synchronisation des répertoires et permettre une intégration sans soucis dans un abonnement pour Exchange Online. Pour ces écritures différées, les entrées du journal d’audit affichent les actions prises par la « Gestion du substrat Microsoft ». Ces entrées du journal d’audit font référence aux opérations de création/mise à jour/suppression exécutées par Exchange Online pour Azure AD. Les entrées sont fournies à titre d’information et ne nécessitent aucune action.

## <a name="next-steps"></a>Étapes suivantes

- [Référence sur l’activité d’audit Azure AD](reference-audit-activities.md)
- [Référence sur la rétention des journaux Azure AD](reference-reports-data-retention.md)
- [Référencent des latences de journal Azure AD](reference-reports-latencies.md)
- [Acteurs inconnus dans le rapport d’audit](/troubleshoot/azure/active-directory/unknown-actors-in-audit-reports)
