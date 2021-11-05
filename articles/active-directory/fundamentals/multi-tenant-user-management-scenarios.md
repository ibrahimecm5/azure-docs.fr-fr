---
title: Scénarios courants d’utilisation de la gestion des utilisateurs multilocataires dans Azure Active Directory
description: Découvrez les scénarios courants où des comptes Invité peuvent être utilisés pour configurer l’accès utilisateur pour des locataires Azure Active Directory
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
ms.openlocfilehash: fde715f688d7e19477894f3239d174d7925edb78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059392"
---
# <a name="multi-tenant-user-management-scenarios"></a>Scénarios de gestion d’utilisateurs multilocataires

## <a name="end-user-initiated-scenario"></a>Scénario lancé par un utilisateur final
Pour le scénario initié par un utilisateur final, les administrateurs de locataires de ressources délèguent certaines capacités aux utilisateurs du locataire. Les administrateurs permettent aux utilisateurs finaux d’inviter des utilisateurs invités au locataire, à une application ou à une ressource. Les utilisateurs du locataire de base sont invités ou s’inscrivent individuellement.

Un exemple de cas d’usage est une société internationale de services professionnels qui travaille avec des sous-traitants sur un projet. Les utilisateurs des sous-traitants demandent un accès aux applications et documents de la société. Les administrateurs de la société peuvent déléguer aux utilisateurs finaux de la société la possibilité d’inviter des sous-traitants ou de configurer un libre-service pour l’accès des sous-traitants aux ressources.

### <a name="provision-accounts"></a>Provisionner des comptes

Il existe de nombreuses façons d’inviter des utilisateurs finaux à accéder aux ressources d’un locataire. En voici cinq parmi les plus couramment utilisées : 

* [Invitations basées sur une application](../external-identities/o365-external-user.md). Les applications Microsoft peuvent autoriser l’invitation d’utilisateurs invités. Les paramètres d’invitation B2B doivent être configurés dans Azure AD B2B dans la ou les applications appropriées.

* [MyApps]../user-help/my-apps-portal-end-user-access). Les utilisateurs invitent et affectent un utilisateur invité à une application en utilisant MyApps. Le compte d’utilisateur doit disposer des autorisations de l’approbateur [Inscription des applications en libre-service](../manage-apps/manage-self-service-access.md). Ils peuvent inviter des utilisateurs invités à un groupe s’ils sont propriétaires du groupe.

* [Gestion des habilitations](../governance/entitlement-management-overview.md) : permet aux administrateurs ou aux propriétaires de ressources de lier des ressources, des organisations externes autorisées, une expiration des utilisateurs invités et des stratégies d’accès dans des packages d’accès. Les packages d’accès peuvent être publiés pour permettre l’inscription en libre-service pour l’accès à des ressources par des utilisateurs invités.

* [Portail Azure](../external-identities/add-users-administrator.md) : Les utilisateurs finaux ayant le [rôle Inviteur d’invités](../external-identities/delegate-invitations.md) peuvent se connecter au portail Azure et inviter des utilisateurs invités à partir du menu Utilisateurs dans Azure Active Directory.

* [Par programmation (PowerShell, API Graph)](../external-identities/customize-invitation-api.md) : Les utilisateurs finaux ayant le [rôle Inviteur d’invités](../external-identities/delegate-invitations.md) peuvent inviter des utilisateurs invités via PowerShell ou l’API Graph. 

### <a name="redeem-invitations"></a>Accepter les invitations

Dans le cadre du provisionnement de comptes pour accéder à une ressource, les invitations par e-mail sont envoyées à l’adresse e-mail des utilisateurs invités. Quand un utilisateur invité reçoit une invitation, il peut :
* Suivre le lien contenu dans l’e-mail vers l’URL d’acceptation.
* Essayer d’accéder directement à la ressource. 

Quand l’utilisateur tente d’accéder directement à la ressource, cela s’appelle « acceptation juste-à-temps ». Voici les expériences utilisateur pour chaque méthode d’acceptation. 

#### <a name="redemption-url"></a>URL d’acceptation

En accédant à l’[URL d’acceptation](../external-identities/redemption-experience.md) dans l’e-mail, l’utilisateur invité peut approuver ou refuser l’invitation (en créant si nécessaire un compte d’utilisateur invité). 

#### <a name="just-in-time-redemption"></a>Acceptation juste-à-temps

L’utilisateur peut accéder directement à l’URL de la ressource pour l’acceptation juste-à-temps si :

* L’utilisateur invité a déjà un compte Azure AD ou Microsoft  
‎- ou -

* Si le [code secret à usage unique envoyé par e-mail](../external-identities/one-time-passcode.md) est activé.

Voici quelques points à noter pour l’acceptation juste-à-temps :

* Si les administrateurs n’ont pas supprimé les conditions de confidentialité de l’acceptation, l’utilisateur doit accepter la page du contrat Conditions de confidentialité avant d’accéder à la ressource.

* PowerShell permet de contrôler si un e-mail est envoyé lors de l’invitation [via PowerShell](/powershell/module/azuread/new-azureadmsinvitation?view=azureadps-2.0&preserve-view=true).

* Vous pouvez autoriser ou bloquer les invitations à des utilisateurs invités d’organisations spécifiques en utilisant une [liste verte ou une liste rouge](../external-identities/allow-deny-list.md).

Pour plus d’informations, consultez [Utilisation d'invitations Azure Active Directory B2B Collaboration](../external-identities/redemption-experience.md).

#### <a name="important--enable-one-time-passcode-authentication"></a>Important : activer l’authentification par code secret à usage unique 

Nous recommandons fortement d’activer l’[authentification par mot de passe à usage unique envoyé par e-mail](../external-identities/one-time-passcode.md). Cette fonctionnalité authentifie les utilisateurs invités lorsqu’ils ne peuvent pas être authentifiés par d’autres moyens, comme :

* Azure AD

* Un compte Microsoft (MSA)

* Un compte Gmail via la fédération Google

* Un compte d’un fournisseur d’identité SAML/WS-Fed via la fédération directe

Avec l’authentification par code secret à usage unique, il est inutile de créer un compte Microsoft. Quand l’utilisateur invité accepte une invitation ou accède à une ressource partagée, il reçoit un code temporaire. Le code est envoyé à son adresse e-mail, puis il entre le code pour poursuivre la connexion. 

Si l’authentification par code secret à usage unique envoyé par e-mail est désactivée, vous pouvez créer un compte Microsoft ou un locataire Azure AD « non managé » juste-à-temps.

>**Important** : Microsoft déprécie la création de locataires non managés et de leurs utilisateurs, car cette fonctionnalité est maintenant en disponibilité générale dans chaque environnement cloud. 

### <a name="manage-accounts"></a>Gérer les comptes

L’administrateur de locataire de ressources gère les comptes d’utilisateurs invités dans le locataire de ressources. Les comptes d’utilisateurs invités ne sont pas mis à jour en fonction des valeurs mises à jour dans le locataire de base. En fait, les seuls attributs visibles reçus incluent l’adresse e-mail et le nom d’affichage.

Vous pouvez configurer plus d’attributs sur les objets utilisateur invités pour faciliter les scénarios. Par exemple, vous pouvez inclure le remplissage du carnet d’adresses avec les détails du contact ou dans des scénarios de droits. Par exemple, prenez en compte les points suivants :

* HiddenFromAddressListsEnabled

* GivenName

* Surname

* Titre

* Service

* TelephoneNumber

Ces attributs peuvent être définis pour [ajouter des invités à la liste d’adresses globale](/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide&preserve-view=true). D’autres scénarios peuvent nécessiter des attributs différents, par exemple pour définir des droits et des autorisations pour les packages d’accès, l’appartenance à un groupe dynamique, les revendications SAML, etc.

Remarque : Les utilisateurs invités sont masqués par défaut dans la liste d’adresses globale. Définissez les attributs des utilisateurs invités comme n’étant pas masqués pour les inclure dans la liste d’adresses globale unifiée. Pour plus d’informations, consultez la documentation de [Microsoft Exchange Online](multi-tenant-common-considerations.md#microsoft-exchange-online).

### <a name="deprovision-accounts"></a>Déprovisionner des comptes

Les scénarios lancés par l’utilisateur final décentralisent les décisions sur les accès. Cependant, la décentralisation des décisions sur les accès entraînent une problématique concernant le moment où il faut décider de supprimer un utilisateur invité et son accès associé. La [gestion des droits](../governance/entitlement-management-overview.md) et les [révisions d’accès](../governance/manage-guest-access-with-access-reviews.md) permettent de passer en revue et de supprimer des utilisateurs invités existants et leur accès aux ressources. 

Remarque : Si des utilisateurs sont invités en dehors de la gestion des droits, vous devez créer un processus distinct pour réviser et gérer l’accès de ces utilisateurs invités. Par exemple, si l’utilisateur invité est invité directement via SharePoint Online, il n’est pas inclus dans votre processus de gestion des droits.

## <a name="scripted-scenario"></a>Scénario avec script

Pour le scénario avec script, les administrateurs de locataire de ressources déploient un processus d’extraction par script pour automatiser la découverte et le provisionnement des utilisateurs invités. Cette approche est courante pour les clients qui utilisent un mécanisme de script.

Un exemple de cas d’usage serait une société de transport internationale qui fait l’acquisition d’un concurrent. Chaque société a un seul locataire Azure AD. Ils veulent que les scénarios « premier jour » suivants fonctionnent, sans que les utilisateurs aient à effectuer des étapes d’invitation ou d’acceptation. Tous les utilisateurs doivent être en mesure d’effectuer les opérations suivantes : 

* Utiliser l’authentification unique pour toutes les ressources pour lesquelles ils sont provisionnées

* Se trouver les uns les autres, et aussi trouver d’autres ressources dans une liste d’adresses globale unifiée

* Déterminer la présence les uns des autres et pouvoir lancer des messages instantanés

* Accéder à une application en fonction de l’appartenance à un groupe dynamique

Dans ce cas, le locataire de chaque organisation est le locataire d’accueil pour ses employés existants et le locataire de ressources pour les employés de l’autre organisation.

### <a name="provision-accounts"></a>Provisionner des comptes

Avec une [requête delta](/graph/delta-query-overview), les administrateurs de locataire peuvent déployer un processus d’extraction par script pour automatiser la découverte et le provisionnement des identités pour prendre en charge l’accès aux ressources. Ce processus recherche les nouveaux utilisateurs dans le locataire de base et utilise les API Graph B2B pour provisionner ces utilisateurs en tant qu’utilisateurs invités dans le locataire de ressources. Le diagramme suivant montre les composants. 
### <a name="multi-tenant-topology"></a>Topologie multilocataire

![Scénario multilocataire](media\multi-tenant-user-management-scenarios\multi-tenant-scripted-scenario.png)

* Les administrateurs de chaque locataire préconfigurent les informations d’identification et le consentement pour permettre la lecture de chaque locataire.

* Permet aux administrateurs clients d’automatiser l’énumération et « d’extraire » des utilisateurs délimités au locataire de ressources.

* Utilisez l’API MS Graph avec les autorisations accordées pour lire et provisionner des utilisateurs via l’API d’invitation.

* Le provisionnement initial peut lire les attributs de la source et les appliquer à l’objet utilisateur cible.

### <a name="manage-accounts"></a>Gérer les comptes

L’organisation des ressources peut choisir d’augmenter les données de profil pour prendre en charge des scénarios de partage en mettant à jour les attributs de métadonnées de l’utilisateur dans le locataire de ressources. Cependant, si une synchronisation continue est nécessaire, une solution synchronisée peut être une meilleure option.

### <a name="deprovision-accounts"></a>Déprovisionner des comptes

Une [requête delta](/graph/delta-query-overview) peut indiquer quand un utilisateur invité doit être déprovisionné. La [gestion des droits](../governance/entitlement-management-overview.md) et les [révisions d’accès](../governance/manage-guest-access-with-access-reviews.md) peuvent aussi fournir un moyen de passer en revue et de supprimer des utilisateurs invités existants et leur accès aux ressources. 

Remarque : Si des utilisateurs sont invités en dehors de la gestion des droits, vous devez créer un processus distinct pour réviser et gérer l’accès de ces utilisateurs invités. Par exemple, si l’utilisateur invité est invité directement via SharePoint Online, il n’est pas inclus dans votre processus de gestion des droits.

## <a name="automated-scenario"></a>Scénario automatisé

Le modèle le plus complexe est de loin un partage synchronisé entre des locataires. Ce modèle permet des scénarios de gestion et de déprovisionnement plus automatisés que ceux lancés par l’utilisateur ou scriptés. Pour les scénarios automatisés, les administrateurs de locataire de ressources utilisent un système de provisionnement d’identités pour automatiser les processus de provisionnement et de déprovisionnement.

Un exemple de cas d’usage serait un conglomérat multinational qui a plusieurs filiales. Chacun a son propre locataire Azure AD, mais ils doivent fonctionner ensemble. Outre la synchronisation des nouveaux utilisateurs entre les locataires, les mises à jour d’attributs doivent être automatiquement synchronisées. Le déprovisionnement doit être automatisé. Par exemple, si un employé n’est plus dans une filiale, son compte doit être supprimé de tous les autres locataires lors de la synchronisation suivante.

Vous pouvez aussi envisager le scénario étendu suivant. Une société du secteur de la défense a une filiale axée sur la défense et une autre sur le commerce. Celles-ci ont des exigences réglementaires en concurrence :

* L’activité de défense pour les États-Unis se trouve dans un locataire du cloud souverain des États-Unis. Par exemple, Microsoft 365 US Government GCC High.

* L’activité de commerce se trouve dans un locataire Azure AD distinct dans le cloud public. Par exemple, un environnement Azure AD s’exécutant sur le cloud Azure global. 

 Pour agir comme une seule entreprise déployée dans une architecture « inter-cloud souverain », tous les utilisateurs sont synchronisés sur les deux locataires. Ceci permet une liste d’adresses globale unifiée disponible sur les deux locataires. Cela peut aussi garantir que les utilisateurs synchronisés automatiquement sur les deux locataires incluent les droits et les restrictions sur les applications et le contenu. Par exemple :

* Les employés américains peuvent avoir un accès complet aux deux locataires.

* Les employés non américains apparaissent dans la liste d’adresses globale unifiée des deux locataires, mais n’ont pas accès au contenu protégé dans le locataire GCC High. 

Ceci va nécessiter une synchronisation automatique et une gestion des identités pour configurer les utilisateurs dans les deux locataires pour les associer aux stratégies de protection des données et aux droits appropriés.

### <a name="provision-accounts"></a>Provisionner des comptes

Ce déploiement avancé utilise [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) comme moteur de synchronisation. Microsoft Identity Manager appelle l’[API MS Graph](/developer.microsoft.com/graph/) et [Exchange Online PowerShell](/powershell/exchange/exchange-online/exchange-online-powershell?view=exchange-ps&preserve-view=true). D’autres implémentations peuvent inclure l’offre de service managé [ADSS (Active Directory Synchronization Services)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) de [Microsoft Consulting Services](/industry/services/consulting). Il existe également des offres non-Microsoft qui peuvent être créées à partir de zéro avec d’autres offres de gestion des identités.

Il s’agit de scénarios complexes et nous vous recommandons de travailler avec vos partenaires, l’équipe des comptes Microsoft et les autres ressources disponibles tout au long de votre planification et de l’exécution. 

Remarque : Il existe des points à considérer qui n’entrent pas dans le cadre de ce document. Par exemple, l’[intégration d’applications locales](../app-proxy/what-is-application-proxy.md). 

### <a name="choose-the-right-topology"></a>Choisir la bonne topologie

La plupart des clients utilisent une parmi deux topologies possibles dans les scénarios automatisés. 

* Une topologie de maillage permet de partager toutes les ressources de tous les locataires. Les utilisateurs d’autres locataires sont créés dans chaque locataire de ressources en tant qu’utilisateurs invités.

* Une topologie de locataire de ressources unique utilise un seul locataire (le locataire de ressources), dans lequel les utilisateurs d’autres sociétés sont des utilisateurs invités externes. 

Le tableau suivant peut être utilisé dans arbre de décision lors de la conception de votre solution. Nous illustrons les deux topologies à la suite du tableau. Pour vous aider à déterminer ce qui convient pour votre organisation, considérez les points suivants. 

Comparaison entre les topologies de maillage et les topologies de locataire de ressources unique

| Considération| Topologie de maillage| Locataire de ressources unique |
| - | - |-|
| Chaque entreprise a un locataire Azure AD distinct avec des utilisateurs et des ressources| Oui| Oui |
| **Emplacement des ressources et collaboration**| |  |
| Les applications partagées et les autres ressources restent dans leur locataire de base actuel| Oui| Non - seules les ressources dans le locataire de ressources sont partagées. |
| Toutes sont visibles dans les listes d’adresses globales de chaque société (liste d’adresses globale unifiée)| Oui| Non |
| **Accès et administration des ressources**| |  |
| TOUTES les applications connectées à Azure AD peuvent être partagées entre toutes les sociétés| Oui| Non - seules celles qui se trouvent dans le locataire de ressources sont partagées. Celles qui restent dans d’autres locataires ne le sont pas. |
| Administration des ressources globale ‎| Continuer au niveau du locataire| Regroupée dans le locataire de ressources |
| Licences – Office 365 <br>SharePoint Online, liste d’adresses globale unifiée, les accès Teams prennent tous en charge les invités ; cependant, ce n’est pas le cas des autres scénarios Exchange Online| Continues au niveau du locataire| Continues au niveau du locataire |
| Licences – [Azure AD (Premium)](../external-identities/external-identities-pricing.md)| Les premiers 50 000 utilisateurs actifs mensuels sont gratuits (par locataire).| Les premiers 50 000 utilisateurs actifs mensuels sont gratuits. |
| Licences – Applications SaaS| Restent dans les locataires individuels, peuvent nécessiter des licences par utilisateur par locataire| Toutes les ressources partagées se trouvent dans le locataire de ressources unique. Vous pouvez examiner un éventuel regroupement des licences sur le seul locataire si c’est approprié. |

#### <a name="mesh-topology"></a>Topologie de maillage

![Topologie de maillage](media/multi-tenant-user-management-scenarios/mesh.png)

Dans une topologie de maillage, chaque utilisateur de chaque locataire de base est synchronisé avec chacun des autres locataires, qui deviennent des locataires de ressources. 

* Ceci permet aux ressources d’un locataire d’être partagées avec des utilisateurs invités. 

* Ceci permet à chaque organisation de voir tous les utilisateurs du conglomérat. Dans l’illustration ci-dessus, il y a quatre listes d’adresses globales unifiées, chacune contenant les utilisateurs de base et les utilisateurs invités des trois autres locataires.

Pour plus d’informations sur le provisionnement, la gestion et le déprovisionnement des utilisateurs dans ce scénario, consultez la section [Considérations courantes](multi-tenant-common-considerations.md#directory-object-considerations) de ce document.

#### <a name="mesh-topology-for-cross-sovereign-cloud"></a>Topologie de maillage pour le cloud souverain transversal

La topologie de maillage peut être utilisée dès qu’il y a deux locataires, comme dans le scénario du sous-traitant travaillant dans le domaine de la défense qui utilise une solution mixte de cloud souverain transversal. Comme pour la topologie de maillage, chaque utilisateur de chaque locataire de base est synchronisé avec l’autre locataire, qui devient en fait un locataire de ressources. Dans l’illustration ci-dessus, l’utilisateur membre du locataire « Commercial » public est synchronisé avec le locataire GCC High souverain des États-Unis en tant que compte d’utilisateur invité. En même temps, l’utilisateur membre de GCC High est synchronisé avec le locataire « Commercial » en tant que compte d’utilisateur invité.

>**Remarque** : L’illustration décrit également l’emplacement de stockage des données. La catégorisation et la conformité des données ne sont pas couvertes par ce livre blanc, mais elles montrent que vous pouvez inclure des droits et des restrictions aux applications et au contenu. Le contenu peut inclure l’emplacement où se trouvent les « données personnelles » d’un utilisateur membre. Par exemple, les données stockées dans leur boîte aux lettres Exchange Online ou dans OneDrive Entreprise. Le contenu peut se trouver seulement dans son locataire de base et non pas dans le locataire de ressources. Les données partagées peuvent se trouver dans l’un ou l’autre locataire. Vous pouvez restreindre l’accès au contenu via le contrôle d’accès et des stratégies d’accès conditionnel.

#### <a name="single-resource-tenant-topology"></a>Topologie de locataire de ressources unique

![Locataire de ressources unique](media/multi-tenant-user-management-scenarios/single-resource-tenant.png)

Dans une topologie de locataire de ressources unique, les utilisateurs et leurs attributs sont synchronisés avec le locataire de ressources (la société A dans l’illustration ci-dessus). 

* Toutes les ressources partagées entre les organisations membres doivent se trouver dans le locataire de ressources unique.
  * Si plusieurs filiales ont des abonnements aux mêmes applications SaaS, ceci peut être l’occasion de regrouper ces abonnements.

* Seule la liste d’adresses globale du locataire de ressources affiche les utilisateurs de toutes les sociétés.

### <a name="manage-accounts"></a>Gérer les comptes

Cette solution détecte et synchronise les modifications des attributs des utilisateurs du locataire source avec les utilisateurs invités du locataire de ressources. Ces attributs peuvent être utilisés pour prendre des décisions sur les autorisations, par exemple lors de l’utilisation de groupes dynamiques.

### <a name="deprovision-accounts"></a>Déprovisionner des comptes

Automation détecte la suppression de l’objet dans l’environnement source et supprime l’objet utilisateur invité associé dans l’environnement cible.

Pour plus d’informations sur le provisionnement, la gestion et le déprovisionnement des utilisateurs dans ce scénario, consultez la section [Considérations courantes](multi-tenant-common-considerations.md) de ce document.

## <a name="next-steps"></a>Étapes suivantes
[Présentation de la gestion multilocataire des utilisateurs](multi-tenant-user-management-introduction.md)

[Considérations courantes sur le multilocataire](multi-tenant-common-considerations.md)

[Solutions multilocataires courantes](multi-tenant-common-solutions.md)
