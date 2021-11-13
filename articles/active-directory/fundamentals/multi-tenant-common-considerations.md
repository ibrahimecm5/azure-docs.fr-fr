---
title: Considérations courantes sur la gestion multilocataire des utilisateurs dans Azure Active Directory
description: Découvrir les considérations de conception courantes sur l’accès utilisateur aux locataires Azure Active Directory avec des comptes invités
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a93eb869cd3135f548331a82de9593497829c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270723"
---
# <a name="common-considerations-for-multi-tenant-user-management"></a>Considérations courantes sur la gestion multilocataire des utilisateurs

Nombreuses sont les considérations qui s’appliquent à plus d’un modèle de collaboration. 

## <a name="directory-object-considerations"></a>Considérations relatives aux objets annuaire

Vous pouvez utiliser la console pour créer manuellement une invitation pour un compte d’utilisateur invité. Quand vous le faites, l’objet utilisateur est créé avec un utilisateur de type *invité*. L’utilisation d’autres techniques pour créer des invitations vous permet de définir le type d’utilisateur sur autre chose qu’un compte invité. Par exemple, lors de l’utilisation de l’API, vous pouvez configurer si le compte est un compte membre ou un compte invité.  

* Quelques-unes des [limites de la fonctionnalité Invité peuvent être enlevées](../external-identities/user-properties.md#remove-guest-user-limitations).

* [Vous pouvez convertir des comptes invités en utilisateur de type membre.](../external-identities/user-properties.md#can-azure-ad-b2b-users-be-added-as-members-instead-of-guests)

> **IMPORTANT** Si vous convertissez un compte invité en compte d’utilisateur, il risque d’y avoir des problèmes liés à la façon dont Exchange Online gère les comptes B2B. Vous ne pouvez pas activer la messagerie des comptes invités en tant que membres invités. Pour obtenir un compte membre invité à extension messagerie, la meilleure approche consiste à :
>* Inviter les utilisateurs inter-organisations en tant que comptes invités.
>* Afficher les comptes dans la liste d’adresses globale (GAL).
>* Définir UserType avec Membre.

Avec cette approche, les comptes apparaissent comme MailUser dans Exchange Online.

### <a name="issues-with-using-mail-contact-objects-instead-of-external-users-or-members"></a>Problèmes avec l’utilisation d’objets contact de courrier au lieu d’utilisateurs ou de membres externes

Vous pouvez représenter les utilisateurs d’un autre locataire à l’aide d’une synchronisation de GAL traditionnelle. Si une synchronisation de GAL est effectuée au lieu d’utiliser Azure AD B2B Collaboration, un objet contact de courrier est créé. 

* Un objet contact de courrier et un utilisateur invité à extension messagerie (membre ou invité) ne peuvent pas coexister dans le même locataire avec la même adresse e-mail en même temps. 

* Si un objet contact de courrier existe pour la même adresse e-mail que l’utilisateur invité, l’utilisateur invité est créé mais NE peut PAS avoir d’extension messagerie. 

* Si l’utilisateur invité à extension messagerie existe avec la même adresse e-mail, une tentative de création d’un objet contact de courrier lève une exception au moment de la création.

Voici les résultats des différents objets contact de courrier et des états des utilisateurs invités.

| État existant| Scénario de provisionnement| Résultat effectif |
| - |-|-|
| Aucun| Inviter un membre B2B| Utilisateur membre sans extension messagerie. Voir la note Important ci-dessus |
| Aucun| Inviter un invité B2B| Utilisateur invité à extension messagerie |
| Un objet contact de courrier existe| Inviter un membre B2B| Erreur – Conflit d’adresses de proxy |
| Un objet contact de courrier existe| Inviter un invité B2B| Utilisateur invité avec et sans extension messagerie. Voir la note Important ci-dessus |
| Utilisateur invité B2B à extension messagerie| Créer un objet contact de courrier| Error |
| Un utilisateur membre B2B à extension messagerie existe| Créer un contact de courrier| Error |


**Microsoft ne recommande pas la synchronisation de GAL traditionnelle**. Utilisez plutôt Azure AD B2B Collaboration pour créer :

* Des utilisateurs invités externes que vous activez pour qu’ils apparaissent dans la GAL

* Créer des utilisateurs membres externes, qui apparaissent dans la GAL par défaut, mais qui ne sont pas à extension messagerie.

Certaines organisations utilisent l’objet contact de courrier pour faire figurer les utilisateurs dans la GAL. Cette approche intègre une GAL sans fournir d’autres autorisations, car les contacts de courrier ne sont pas des principaux de sécurité. 

Une meilleure approche pour atteindre cet objectif consiste à :
* Inviter des utilisateurs
* Les afficher dans la GAL
* Désactivez-les en [les empêchant de se connecter](/powershell/module/azuread/set-azureaduser&preserve-view=true).

Un objet contact de courrier ne peut pas être converti en objet utilisateur. Par conséquent, les propriétés associées à un objet contact de courrier ne peuvent pas être transférées. Par exemple, les appartenances aux groupes et autres accès aux ressources ne sont pas transférés.  

L’utilisation d’un objet contact de courrier pour représenter un utilisateur présente les problèmes suivants.

* **Groupes Office 365** – Les groupes Office 365 prennent en charge les stratégies qui gouvernent les types d’utilisateurs autorisés à être membres de groupes et qui interagissent avec le contenu associé aux groupes. Par exemple, un groupe peut ne pas autoriser des comptes invités à le rejoindre. Ces stratégies ne peuvent pas gouverner les objets contact de courrier.

* **Gestion de groupes en libre-service (SSGM) Azure AD** – Les objets contact de courrier ne peuvent pas être membres de groupes en utilisant la fonctionnalité SSGM. Des outils supplémentaires peuvent s’avérer nécessaires pour gérer les groupes avec des destinataires représentés en tant que contacts et non en tant qu’objets utilisateur.

* **Azure AD Identity Governance - Révisions d’accès** – La fonctionnalité de révisions d’accès peut être utilisée pour vérifier et attester l’appartenance au groupe Office 365. Les révisions d’accès sont basées sur des objets utilisateur. Les membres représentés par des objets contact de courrier ne sont pas concernés par les révisions d’accès.

* **Azure AD Identity Governance - Gestion des droits d’utilisation (EM)** – Quand EM est utilisé pour activer les demandes d’accès en libre-service pour les utilisateurs externes via le portail EM de l’entreprise, un objet utilisateur est créé au moment de la demande. Les objets contact de courrier ne sont pas pris en charge. 

## <a name="azure-ad-conditional-access-considerations"></a>Considérations relatives à l’accès conditionnel Azure AD

L’état de l’utilisateur, de l’appareil ou du réseau dans le locataire d’accueil de l’utilisateur n’est pas transmis au locataire de ressources. Par conséquent, un compte d’utilisateur invité risque de ne pas respecter les stratégies d’accès conditionnel qui utilisent les contrôles suivants.

* **Exiger l’authentification multifacteur** – Les utilisateurs invités doivent s’inscrire/répondre à MFA dans le locataire de ressources, même si MFA a été appliquée dans le locataire d’accueil, ce qui donne lieu à plusieurs demandes MFA. De plus, s’ils ont besoin de réinitialiser leurs preuves MFA, ils n’ont peut-être pas connaissance de l’existence de plusieurs enregistrements de preuves MFA dans les locataires. Le manque de connaissance risque d’obliger l’utilisateur à contacter un administrateur dans le locataire d’accueil, le locataire de ressources, ou les deux.

* **Exiger que l’appareil soit marqué comme conforme** – L’identité de l’appareil n’est pas inscrite dans le locataire de ressources, donc l’utilisateur invité ne peut pas accéder aux ressources qui demandent ce contrôle.

* **Exiger un appareil joint à une version hybride d’Azure AD** – L’identité de l’appareil n’est pas inscrite dans le locataire de ressources (ni Active Directory local connecté au locataire de ressources), donc l’utilisateur invité ne peut pas accéder aux ressources qui demandent ce contrôle.

* **Demander une application cliente approuvée ou Exiger une stratégie de protection des applications** – Les utilisateurs invités externes ne peuvent pas appliquer la stratégie MAM (Mobile App Management) Intune du locataire de ressources parce que l’inscription de l’appareil est également obligatoire. La stratégie d’accès conditionnel du locataire de ressources avec ce contrôle n’autorise pas la protection MAM du locataire d’accueil à respecter la stratégie. Les utilisateurs invités externes doivent être exclus de toutes les stratégies d’accès conditionnel basées sur MAM. 

Par ailleurs, même si les conditions d’accès conditionnel suivantes peuvent être utilisées, attention aux éventuelles conséquences.

* **Connexion à risque et utilisateur à risque** – La connexion à risque et l’utilisateur à risque sont déterminés en partie par le comportement de l’utilisateur dans son locataire d’accueil. Le score de données et de risque est stocké dans le locataire d’accueil.  
‎Si les stratégies du locataire de ressources bloquent un utilisateur invité, l’administrateur de locataire de ressources risque de ne pas pouvoir activer l’accès. Pour plus d’informations, consultez [Protection des identités et utilisateurs B2B](../identity-protection/concept-identity-protection-b2b.md).

* **Emplacements** – Les définitions d’emplacement nommées créées dans le locataire de ressources sont utilisées pour déterminer l’étendue de la stratégie. Les emplacements approuvés gérés dans le locataire d’accueil ne sont pas évalués dans l’étendue de la stratégie. Dans certains scénarios, les organisations voudront probablement partager des emplacements approuvés entre les locataires. Pour partager des emplacements approuvés, les emplacements doivent être définis dans chaque locataire où les ressources et les stratégies d’accès conditionnel sont définies.

## <a name="other-access-control-considerations"></a>Autres considérations relatives au contrôle d’accès

Autres considérations à prendre en compte lors de la configuration du contrôle d’accès.
* Définissez des [stratégies de contrôle d’accès](../external-identities/conditional-access.md) pour contrôler l’accès aux ressources.
* Concevez des stratégies d’accès conditionnel en pensant aux utilisateurs invités. 
* Créez des stratégies spécialement pour les utilisateurs invités. 
* Si votre organisation utilise la condition [Tous les utilisateurs] dans votre stratégie d’accès conditionnel existante, cette stratégie affecte les utilisateurs invités parce que les utilisateurs [invités] sont dans l’étendue de [Tous les utilisateurs].
* Créez des stratégies d’accès conditionnel dédiées pour les comptes [Invités]. 

Pour plus d’informations sur le durcissement des groupes dynamiques qui utilisent l’expression [Tous les utilisateurs], consultez [Groupes dynamiques et Azure AD B2B Collaboration](../external-identities/use-dynamic-groups.md).

### <a name="require-user-assignment"></a>Exiger l’affectation des utilisateurs

Si la propriété [Affectation des utilisateurs exigée ?] d’une application a la valeur [Non], les utilisateurs invités peuvent accéder à l’application. Les administrateurs d’applications doivent comprendre les impacts sur le contrôle d’accès, en particulier si l’application contient des informations sensibles. Pour plus d’informations, consultez [Comment limiter votre application Azure AD à un ensemble d’utilisateurs](../develop/howto-restrict-your-app-to-a-set-of-users.md). 

### <a name="terms-and-conditions"></a>Conditions générales

Les [conditions d’utilisation d’Azure AD](../conditional-access/terms-of-use.md) offrent une méthode simple que les organisations peuvent utiliser pour présenter les informations aux utilisateurs finaux. Vous pouvez utiliser les conditions d’utilisation pour demander aux utilisateurs invités d’approuver les conditions d’utilisation avant d’accéder à vos ressources.

### <a name="licensing-considerations-for-guest-users-with-azure-ad-premium-features"></a>Considérations relatives aux licences pour les utilisateurs invités avec les fonctionnalités Azure AD Premium

Les prix d’Azure AD External Identities (utilisateur invité) sont basés sur les utilisateurs actifs mensuels (MAU, month active users). Les utilisateurs actifs correspondent au nombre d’utilisateurs uniques avec une activité d’authentification au cours d’un mois civil. La facturation MAU contribue à réduire les coûts en offrant un niveau gratuit et une tarification flexible et prévisible. De plus, les 50 000 premiers utilisateurs actifs mensuels sont gratuits pour les fonctionnalités Premium P1 et Premium P2. Les fonctionnalités Premium incluent les stratégies d’accès conditionnel et Azure MFA pour les utilisateurs invités.

Pour plus d’informations, consultez [Modèle de facturation des utilisateurs actifs mensuels pour Azure AD External Identities](../external-identities/external-identities-pricing.md).

## <a name="office-365-considerations"></a>Considérations relatives à Office 365

Les informations suivantes concernent Office 365 dans le contexte des scénarios de ce document. Des informations détaillées sont disponibles dans [Collaboration interlocataires Office 365](/office365/enterprise/office-365-inter-tenant-collaboration).

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

Exchange Online limite certaines fonctionnalités pour les utilisateurs invités. Les limites peuvent être atténuées en créant des membres externes plutôt que des invités externes. Toutefois, rien de ce qui suit n’est pris en charge pour les utilisateurs externes pour l’instant. 

* Un utilisateur invité peut se voir attribuer une licence Exchange Online. Toutefois, il n’est pas autorisé à recevoir un jeton pour Exchange Online. Résultat, il ne peut pas accéder à la ressource.

   * Les utilisateurs invités ne peuvent pas utiliser de boîtes aux lettres partagées ou déléguées Exchange Online dans le locataire de ressources.

   * Un utilisateur invité peut être affecté à une boîte aux lettres partagée, mais ne peut pas y accéder.

* Les utilisateurs invités ne doivent plus être masqués afin de figurer dans la GAL. Par défaut, ils sont masqués.

   * Les utilisateurs invités masqués sont créés au moment de l’invitation. La création est indépendante du fait que l’utilisateur a accepté son invitation. Par conséquent, si tous les utilisateurs invités ne sont plus masqués, la liste comprend les objets utilisateur des utilisateurs invités qui n’ont pas accepté d’invitation. Selon votre scénario, vous voudrez peut-être, ou pas, que les objets soient listés.

   * Les utilisateurs invités peuvent être affichés en utilisant [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) uniquement. Vous pouvez exécuter l’applet de commande PowerShell [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) pour affecter à la propriété HiddenFromAddressListsEnabled une valeur $false.  
‎  
`‎Set-MailUser [GuestUserUPN] -HiddenFromAddressListsEnabled:$false` ‎  
‎Où [GuestUserUPN] est le UserPrincipalName calculé. Exemple :  
‎  
`‎Set-MailUser guestuser1_contoso.com#EXT#@fabricam.onmicrosoft.com -HiddenFromAddressListsEnabled:$false`

* Les mises à jour des propriétés spécifiques à Exchange, telles que PrimarySmtpAddress, ExternalEmailAddress, EmailAddresses et MailTip, peuvent être définies en utilisant [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) uniquement. Le Centre d’administration Exchange Online ne vous autorise pas à modifier les attributs à l’aide de l’interface graphique utilisateur. 

Comme indiqué ci-dessus, vous pouvez utiliser l’applet de commande PowerShell [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) pour les propriétés spécifiques au courrier. Vous pouvez modifier un grand nombre de propriétés utilisateur supplémentaires à l’aide de l’applet de commande PowerShell [Set-User](/powershell/module/exchange/set-user?view=exchange-ps&preserve-view=true). La plupart des propriétés peuvent également être modifiées à l’aide des API Graph Azure AD.

### <a name="microsoft-sharepoint-online"></a>Microsoft SharePoint Online

SharePoint Online dispose de ses propres autorisations spécifiques au service, selon que l’utilisateur est membre d’un invité dans le locataire Azure Active Directory. 

Pour plus d’informations, consultez [Partage externe dans Office 365 et Azure Active Directory B2B Collaboration](../external-identities/o365-external-user.md).

Une fois le partage externe activé dans SharePoint Online, la fonctionnalité de recherche d’utilisateurs invités dans le sélecteur de personnes SharePoint Online est désactivée par défaut. Ce paramètre empêche les utilisateurs invités d’être découvrables lorsqu’ils sont masqués dans la GAL d’Exchange Online. Vous pouvez permettre aux utilisateurs invités de devenir visibles de deux manières (qui ne s’excluent pas mutuellement) :

* Vous pouvez activer la fonctionnalité de recherche d’utilisateurs invités de plusieurs façons :
   * Modifiez le paramètre « ShowPeoplePickerSuggestionsForGuestUsers » au niveau du locataire et de la collection de sites. 
   * Définissez la fonctionnalité avec les applets de commande[Set-SPOTenant](/powershell/module/sharepoint-online/Set-SPOTenant?view=sharepoint-ps&preserve-view=true) et [Set-SPOSite](/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps&preserve-view=true) [de SharePoint Online PowerShell](/powershell/sharepoint/sharepoint-online/connect-sharepoint-online?view=sharepoint-ps&preserve-view=true).  
‎

* Les utilisateurs invités qui sont visibles dans la GAL d’Exchange Online sont également visibles dans le sélecteur de personnes SharePoint Online. Les comptes sont visibles quel que soit le paramètre de « ShowPeoplePickerSuggestionsForGuestUsers ».

### <a name="microsoft-teams"></a>Microsoft Teams

Microsoft Teams présente des fonctionnalités qui limitent l’accès et qui sont basées sur le type d’utilisateur. Les changements de type d’utilisateur risquent d’affecter l’accès au contenu et les fonctionnalités disponibles. 

* Le mécanisme de « changement de locataire » pour Microsoft Teams risque d’obliger les utilisateurs à changer manuellement le contexte de leur client Teams lorsqu’ils utilisent Teams en dehors de leur locataire d’accueil.

* Vous pouvez autoriser les utilisateurs Teams d’un tout autre domaine externe à rechercher, appeler, discuter et configurer des réunions avec vos utilisateurs en utilisant Teams Federation. Pour plus d’informations, consultez [Gérer l’accès externe dans Microsoft Teams](/microsoftteams/manage-external-access). 

 

### <a name="licensing-considerations-for-guest-users-in-teams"></a>Considérations relatives aux licences pour les utilisateurs invités dans Teams

Quand vous utilisez Azure B2B avec des charges de travail Office 365, des considérations importantes sont à prendre en compte. Il existe des instances dans lesquelles les comptes invités n’ont pas la même expérience qu’un compte membre. 

**Groupes Microsoft**. Consultez [Ajout d’invités à des groupes Office 365](https://support.office.com/article/adding-guests-to-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) pour mieux comprendre l’expérience de compte invité dans les groupes Microsoft. 

**Microsoft Teams**. Consultez [Possibilités du propriétaire de l’équipe, des membres et des invités dans Teams](https://support.office.com/article/team-owner-member-and-guest-capabilities-in-teams-d03fdf5b-1a6e-48e4-8e07-b13e1350ec7b?ui=en-US&rs=en-US&ad=US) pour mieux comprendre l’expérience de compte invité dans Microsoft Teams. 

Vous pouvez obtenir une expérience de fidélité totale dans Teams à l’aide de membres externes B2B. Office 365 a récemment clarifié sa stratégie de licence pour les organisations multilocataires.

* Les utilisateurs qui ont une licence dans leur locataire d’accueil peuvent accéder aux ressources d’un autre locataire au sein de la même entité juridique. L’accès est accordé à l’aide du paramètre **Membres externes** sans frais de licence supplémentaires. Le paramètre s’applique à SharePoint, OneDrive Entreprise, Teams et Groupes. 

   * Un travail d’ingénierie est en cours pour vérifier automatiquement l’état de la licence d’un utilisateur dans son locataire d’accueil et lui permettre de participer en tant que Membre sans aucune configuration ou affectation de licence supplémentaires. Toutefois, pour les clients qui souhaitent utiliser des membres externes tout de suite, il existe une solution de contournement selon laquelle le Responsable de compte doit travailler avec Microsoft Business Desk. 

   * À partir de maintenant et jusqu’à ce que la solution de licence soit activée, les clients peuvent utiliser une *licence d’essai Teams*. La licence peut être affectée à chaque utilisateur dans son locataire étranger. La licence a une durée d’un an et active toutes les charges de travail listées ci-dessus.

   * Pour les clients qui souhaitent convertir des invités B2B en membres B2B, il existe plusieurs problèmes connus avec Microsoft Teams tels que l’impossibilité de créer des canaux et la possibilité d’ajouter des applications à une équipe existante. 

* Les fonctionnalités d’**Identity Governance** (gestion des droits d’utilisation, révisions d’accès) peuvent nécessiter d’autres licences pour les utilisateurs invités ou les membres externes. Collaborez avec l’équipe de compte ou Business Desk pour obtenir une réponse adaptée à votre organisation.

D’**autres produits** (tels que Dynamics CRM) peuvent nécessiter des licences dans chaque locataire dans lequel un utilisateur est représenté. Collaborez avec votre équipe de compte pour obtenir une réponse adaptée à votre organisation.

## <a name="next-steps"></a>Étapes suivantes
[Présentation de la gestion multilocataire des utilisateurs](multi-tenant-user-management-introduction.md)

[Scénarios de gestion multilocataire d’utilisateurs finaux](multi-tenant-user-management-scenarios.md)

[Solutions multilocataires courantes](multi-tenant-common-solutions.md)