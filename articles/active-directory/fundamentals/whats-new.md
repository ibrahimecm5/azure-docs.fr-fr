---
title: Nouveautés Notes de publication - Azure Active Directory | Microsoft Docs
description: Découvrez les nouveautés d’Azure Active Directory, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités déconseillées et les modifications à venir.
author: ajburnle
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee25c39514146ddf9e7489868c24b656211115a6
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398418"
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory

>Soyez notifié de la disponibilité des mises à jour sur cette page en faisant un copier-coller de cette URL : `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` dans votre lecteur de flux ![icône RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des Nouveautés d’Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2021"></a>Octobre 2021
 
### <a name="limits-on-the-number-of-configured-api-permissions-for-an-application-registration-will-be-enforced-starting-in-october-2021"></a>Les limites du nombre d’autorisations d’API configurées pour une inscription d’application seront appliquées à partir d’octobre 2021

**Type :** Modification planifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement
 
Parfois, les développeurs d’applications configurent leurs applications de manière à exiger plus d’autorisations qu’il n’est possible d’en accorder. Pour éviter que cela ne se produise, une limite sera imposée au nombre total d’autorisations requises qui peuvent être configurées pour une inscription d’application.

Le nombre total d’autorisations requises pour une inscription d’application unique ne doit pas dépasser 400 autorisations, toutes API confondues. La modification visant à appliquer cette limite commencera à être mise en œuvre à la mi-octobre 2021. Les applications qui dépassent cette limite ne peuvent pas augmenter le nombre d’autorisations pour lesquelles elles sont configurées. La limite actuelle du nombre d’API distinctes pour lesquelles des autorisations sont requises reste inchangée et ne peut pas dépasser 50 API.

Dans le portail Azure, les autorisations requises sont répertoriées sous Autorisations des API pour l’application que vous souhaitez configurer. À l’aide de Microsoft Graph ou de Microsoft Graph PowerShell, les autorisations requises sont répertoriées dans la propriété requiredResourceAccess d’une entité d’[application](/graph/api/resources/application?view=graph-rest-1.0). [Plus d’informations](../enterprise-users/directory-service-limits-restrictions.md)
 
---

### <a name="email-one-time-passcode-on-by-default-change-beginning-rollout-in-november-2021"></a>Changement du code secret à usage unique envoyé par e-mail par défaut à partir de novembre 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Nous avons annoncé précédemment qu’à partir du 31 octobre 2021, l’authentification par [code secret à usage unique envoyé par e-mail](../external-identities/one-time-passcode.md) de Microsoft Azure Active Directory deviendra la méthode par défaut pour l’invitation de comptes et de locataires pour les scénarios de collaboration B2B. Toutefois, en raison des calendriers de déploiement, nous commencerons le déploiement le 1er novembre 2021. Le changement sera déployé pour la plupart des locataires en janvier 2022 afin de réduire au minimum les perturbations pendant les fêtes de fin d’année et les blocages de déploiements. Après ce changement, Microsoft ne permettra plus l’échange d’invitations à l’aide de comptes Azure Active Directory non gérés. [Plus d’informations](../external-identities/one-time-passcode.md#frequently-asked-questions)
 
---

### <a name="conditional-access-guest-access-blocking-screen"></a>Écran de blocage de l’accès invité dans l’accès conditionnel

**Type :** Résolution  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
S’il n’existe aucune relation de confiance entre le locataire de base et un locataire de ressources, l’utilisateur invité aurait été invité à réinscrire son appareil, ce qui aurait rompu l’inscription précédente. Toutefois, l’utilisateur se retrouverait dans une boucle d’inscription, car seule l’inscription de l’appareil du locataire de base est prise en charge. Dans ce scénario spécifique, au lieu de cette boucle, nous avons créé une nouvelle page de blocage de l’accès conditionnel. La page indique à l’utilisateur final qu’il ne peut pas accéder aux ressources protégées par l’accès conditionnel en tant qu’utilisateur invité. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal#prerequisites)
 
---

### <a name="50105-errors-will-now-result-in-a-ux-error-message-instead-of-an-error-response-to-the-application"></a>Les erreurs 50105 donnent désormais lieu à un message d’erreur d’expérience utilisateur au lieu d’une réponse d’erreur à l’application

**Type :** Résolution  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Expérience de développement
 
Azure AD a corrigé un bogue dans une réponse d’erreur qui se produit lorsqu’un utilisateur n’est pas affecté à une application nécessitant une affectation d’utilisateur. Auparavant, Azure AD renvoyait l’erreur 50105 avec le code d’erreur OIDC « interaction_required » même pendant l’authentification interactive. Les applications bien codées pouvaient ainsi tourner indéfiniment en boucle, car elles procédaient à une authentification interactive et recevaient une erreur leur indiquant de procéder à une authentification interactive, ce qu’elles faisaient alors.  

Le bogue a été corrigé, de sorte que lors d’une authentification non interactive, une erreur « interaction_required » sera toujours renvoyée. De même, pendant l’authentification interactive, une page d’erreur sera directement affichée à l’utilisateur.  

Pour plus de détails, consultez les avis de modification pour les [protocoles Azure AD](../develop/reference-breaking-changes.md#error-50105-has-been-fixed-to-not-return-interaction_required-during-interactive-authentication). 

---

### <a name="public-preview---new-claims-transformation-capabilities"></a>Préversion publique : Nouvelles capacités de transformation des revendications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** SSO
 
Les nouvelles capacités suivantes ont été ajoutées aux transformations de revendications disponibles pour la manipulation des revendications dans les jetons émis par Azure AD :
 
- Join() sur NameID. Utilisé pour être limité à l’ajout d’une adresse e-mail avec un domaine vérifié. Désormais, Join() peut être utilisé sur la revendication NameID de la même manière que toute autre revendication, de sorte que les transformations NameID peuvent être utilisées pour créer des NameID de type compte Windows ou toute autre chaîne. Pour l’instant, si le résultat est une adresse e-mail, Azure AD validera toujours que le domaine est un domaine qui est vérifié dans le locataire.
- Substring(). Une nouvelle transformation dans l’interface utilisateur de configuration des revendications permet d’extraire des sous-chaînes dont la position est définie, telles que cinq caractères à partir du troisième caractère : substring(3,5).
- Transformations des revendications. Ces transformations peuvent maintenant être effectuées sur des attributs à valeurs multiples et peuvent émettre des revendications à valeurs multiples. Microsoft Graph peut désormais être utilisé pour lire ou écrire des attributs d’extension de schéma de répertoire à valeurs multiples. [Plus d’informations](../develop/active-directory-saml-claims-customization.md)

---

### <a name="public-preview--flagged-sign-ins"></a>Préversion publique : Connexions signalées  

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Les connexions signalées sont une fonctionnalité qui augmentera le rapport signal/bruit pour les connexions d’utilisateurs qui ont besoin d’aide. Cette fonctionnalité a pour but de permettre aux utilisateurs d’attirer l’attention sur les erreurs de connexion pour lesquelles ils souhaitent obtenir de l’aide. Elle aidera également les administrateurs et les employés du support technique à trouver rapidement et efficacement les événements de connexion appropriés. [Plus d’informations](../reports-monitoring/overview-flagged-sign-ins.md)

---

### <a name="public-preview---device-overview"></a>Préversion publique : Présentation des appareils

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
La nouvelle fonctionnalité Présentation des appareils fournit des informations utiles sur les appareils de votre locataire. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal)
 
---

### <a name="public-preview---azure-active-directory-workload-identity-federation"></a>Préversion publique : Fédération des identités de charge de travail Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Expérience de développement
 
La fédération des identités de charge de travail Azure AD est une nouvelle capacité en préversion publique. Elle libère les développeurs de la manipulation des secrets d’application ou des certificats. Cela inclut les secrets dans des scénarios tels que l’utilisation de GitHub Actions et la création d’applications sur Kubernetes. Plutôt que de créer un secret d’application et de l’utiliser pour obtenir des jetons pour cette application, les développeurs peuvent utiliser les jetons fournis par les plateformes respectives telles que GitHub et Kubernetes sans avoir à gérer manuellement des secrets. [En savoir plus](../develop/workload-identity-federation.md).

---

### <a name="public-preview---updates-to-sign-in-diagnostic"></a>Préversion publique : Mises à jour du diagnostic de connexion

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Signalement  
**Fonctionnalité de produit :** Monitoring et création de rapports
 
Avec cette mise à jour, le diagnostic couvre plus de scénarios et devient plus facilement accessible aux administrateurs.

Nouveaux scénarios couverts lors de l’utilisation du diagnostic de connexion :
- Échecs de connexion par authentification directe
- Échecs de connexion par authentification unique transparente
 
Autres changements :
- Les connexions signalées apparaîtront automatiquement pour enquête lors de l’utilisation du diagnostic de connexion à partir de Diagnostiquer et résoudre.
- Le diagnostic de connexion est désormais disponible à partir du panneau Diagnostiquer et résoudre les applications d’entreprise.
- Le diagnostic de connexion est désormais disponible dans l’onglet Informations de base de la vue des événements Journal des connexions pour tous les événements de connexion. [Plus d’informations](../reports-monitoring/concept-sign-in-diagnostics-scenarios.md#supported-scenarios)

---

### <a name="general-availability---privileged-role-administrators-can-now-create-azure-ad-access-reviews-on-role-assignable-groups"></a>Disponibilité générale : Les administrateurs de rôle privilégié peuvent désormais créer des révisions d’accès Azure AD sur des groupes assignables à un rôle

**Type :** Résolution  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les administrateurs de rôle privilégié peuvent désormais créer des révisions d’accès Azure AD sur des groupes assignables à un rôle Azure AD, en plus des rôles Azure AD. [Plus d’informations](../governance/deploy-access-reviews.md#who-will-create-and-manage-access-reviews)
 
---

### <a name="general-availability---azure-ad-single-sign-on-and-device-based-conditional-access-support-in-firefox-on-windows-1011"></a>Disponibilité générale : Authentification unique Azure AD et prise en charge de l’accès conditionnel basé sur les appareils dans Firefox sur Windows 10/11

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** SSO
 
Nous prenons désormais en charge l’authentification unique (SSO) native et l’accès conditionnel basé sur les appareils au navigateur Firefox sur Windows 10 et Windows Server 2019 à partir de la version 91 de Firefox. [Plus d’informations](../conditional-access/require-managed-devices.md#prerequisites)
 
---

### <a name="general-availability---new-app-indicator-in-my-apps"></a>Disponibilité générale : Indicateur de nouvelle application dans Mes applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Les applications qui ont été récemment attribuées à l’utilisateur s’affichent avec un indicateur « nouveau ». Lorsque l’application est lancée ou que la page est actualisée, cet indicateur disparaît. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
---

### <a name="general-availability---custom-domain-support-in-azure-ad-b2c"></a>Disponibilité générale : Prise en charge des domaines personnalisés dans Azure AD B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
Les clients d’Azure AD B2C peuvent désormais activer des domaines personnalisés afin que leurs utilisateurs finaux soient redirigés vers un domaine d’URL personnalisé pour l’authentification. Cela se fait via une intégration à l’aide de la capacité de domaines personnalisés d’Azure Front Door. [Plus d’informations](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow)
 
---

### <a name="general-availability---edge-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur Edge

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 

Les utilisateurs ayant ce rôle peuvent créer et gérer la liste des sites d’entreprise requise pour le mode Internet Explorer sur Microsoft Edge. Ce rôle accorde des autorisations pour créer, modifier et publier la liste de sites, ainsi qu’un accès pour gérer les tickets de support. [En savoir plus](https://docs.microsoft.com/deployedge/edge-ie-mode-cloud-site-list-mgmt)
 
---

### <a name="general-availability---windows-365-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur Windows 365

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les utilisateurs avec ce rôle ont des autorisations générales sur les ressources Windows 365, lorsque le service est présent. En outre, ce rôle permet de gérer les utilisateurs et les appareils pour y associer une stratégie et de créer et gérer des groupes. [En savoir plus](../roles/permissions-reference.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Octobre 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En octobre 2021, nous avons ajouté les 10 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Adaptive Shield](../saas-apps/adaptive-shield-tutorial.md), [SocialChorus Search](https://socialchorus.com/), [Hiretual-SSO](../saas-apps/hiretual-tutorial.md), [TeamSticker by Communitio](../saas-apps/teamsticker-by-communitio-tutorial.md), [embed signage](../saas-apps/embed-signage-tutorial.md), [JoinedUp](../saas-apps/joinedup-tutorial.md), [VECOS Releezme Locker management system](../saas-apps/vecos-releezme-locker-management-system-tutorial.md), [Altoura](../saas-apps/altoura-tutorial.md), [Dagster Cloud](../saas-apps/dagster-cloud-tutorial.md), [Qualaroo](../saas-apps/qualaroo-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez l’article suivant : https://aka.ms/AzureADAppRequest.

---

### <a name="continuous-access-evaluation-migration-with-conditional-access"></a>Migration de la fonctionnalité Évaluation continue de l’accès à l’aide de l’accès conditionnel

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Authentification utilisateur
 
Une nouvelle expérience utilisateur est disponible pour nos locataires Évaluation continue de l’accès (CAE). Les locataires auront désormais accès à CAE dans le cadre de l’accès conditionnel. Tous les locataires qui utilisaient précédemment CAE pour certains comptes d’utilisateurs (mais pas tous) sous l’ancienne expérience utilisateur ou qui avaient précédemment désactivé l’ancienne expérience utilisateur CAE devront désormais faire l’objet d’une migration unique. [En savoir plus](../conditional-access/concept-continuous-access-evaluation.md#migration).
 
---

###  <a name="improved-group-list-blade"></a>Amélioration du panneau de liste de groupes

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Répertoire
 
Le nouveau panneau de liste de groupes offre davantage de capacités de tri et de filtrage, un défilement infini et de meilleures performances. [Plus d’informations](../enterprise-users/groups-members-owners-search.md)
 
---

### <a name="general-availability---google-deprecation-of-gmail-sign-in-support-on-embedded-webviews-on-september-30-2021"></a>Disponibilité générale : Dépréciation par Google de la prise en charge de la connexion à Gmail sur les vues web incorporées le 30 septembre 2021

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Le 30 septembre 2021, Google a supprimé la prise en charge des connexions Gmail sur les applications mobiles et personnalisées Microsoft Teams qui utilisent l’authentification Gmail sur les vues web incorporées.

Si vous souhaitez demander une extension, les clients concernés ayant un ou plusieurs ID client OAuth doivent avoir reçu un e-mail de Google Developers avec les informations suivantes concernant une extension ponctuelle de l’application de la stratégie, qui doit être terminée avant le 31 janvier 2022.

Pour que vos utilisateurs de Gmail puissent continuer à se connecter et à accepter des invitations, nous vous recommandons vivement de vous référer à la section  [Interface utilisateur web incorporée ou système](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) de la documentation de MSAL.NET et de modifier vos applications afin d’utiliser le navigateur système pour la connexion. Tous les kits SDK MSAL utilisent la vue web système par défaut. 

En guise de solution de contournement, nous déploierons le flux de connexion des appareils d’ici le 8 octobre. Entre aujourd’hui et cette date, il est probable qu’il ne soit pas encore déployé dans toutes les régions. Dans ce cas, les utilisateurs finaux seront confrontés à un écran d’erreur jusqu’à ce qu’il soit déployé dans leur région. 

Pour plus de détails sur le flux de connexion des appareils et sur la demande d’extension à Google, consultez [Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).
 
---

### <a name="identity-governance-administrator-can-create-and-manage-azure-ad-access-reviews-of-groups-and-applications"></a>L’administrateur de la gouvernance des identités peut créer et gérer les révisions d’accès Azure AD des groupes et des applications

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
L’administrateur de la gouvernance des identités peut créer et gérer les révisions d’accès Azure AD des groupes et des applications. [Plus d’informations](../governance/deploy-access-reviews.md#who-will-create-and-manage-access-reviews)
 
---

## <a name="september-2021"></a>Septembre 2021

### <a name="limits-on-the-number-of-configured-api-permissions-for-an-application-registration-will-be-enforced-starting-in-october-2021"></a>Les limites du nombre d’autorisations d’API configurées pour une inscription d’application seront appliquées à partir d’octobre 2021

**Type :** Modification planifiée  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Expérience de développement
 
Parfois, les développeurs d’applications configurent leurs applications pour exiger plus d’autorisations qu’il n’est possible d’accorder. Pour éviter que cela ne se produise, nous appliquons une limite au nombre total d’autorisations requises qui peuvent être configurées pour une inscription d’application.

Le nombre total d’autorisations requises pour une inscription d’application unique ne doit pas dépasser 400 autorisations, sur toutes les API. L’application de cette limite s’appliquera au plus tôt mi-octobre 2021. Les applications qui dépassent cette limite ne peuvent pas augmenter le nombre d’autorisations pour lesquelles elles sont configurées. La limite actuelle du nombre d’API distinctes pour lesquelles des autorisations sont requises reste inchangée et ne peut pas dépasser 50 API.

Dans le portail Azure, les autorisations nécessaire sont répertoriées sous Azure Active Directory > Inscriptions d’applications > (sélectionner une application) > Autorisations API. À l’aide de Microsoft Graph ou de Microsoft Graph PowerShell, les autorisations requises sont répertoriées dans la propriété requiredResourceAccess d’une entité d’application. [Plus d’informations](../enterprise-users/directory-service-limits-restrictions.md)

---

###  <a name="my-apps-performance-improvements"></a>Améliorations apportées aux performances de Mes applications

**Type :** Résolution  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Le temps de chargement de Mes applications a été amélioré. Les utilisateurs qui consultent myapps.microsoft.com chargent directement Mes applications, plutôt que de se voir redirigés via un autre service. [Plus d’informations](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="single-page-apps-using-the-spa-redirect-uri-type-must-use-a-cors-enabled-browser-for-auth"></a>Les applications à page unique utilisant le type d’URI de redirection `spa` doivent utiliser un navigateur CORS pour l’authentification

**Type :** Problème connu  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Expérience de développement
 
Le navigateur moderne Edge est désormais inclus dans la configuration requise pour fournir un `Origin`en-tête lors de l’échange d’un [code d’autorisation d’application à page unique](../develop/v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps). Un correctif de compatibilité a exempté accidentellement le navigateur Edge moderne des contrôles CORS et ce bogue sera résolu en octobre. Un sous-ensemble d’applications dépendait de la désactivation de CORS dans le navigateur, ce qui a pour effet secondaire de supprimer l’en-tête `Origin` du trafic. il s’agit d’une configuration non prise en charge pour l’utilisation d’Azure AD et les applications qui dépendent de la désactivation de CORS ne peuvent plus utiliser Edge comme solution de contournement de sécurité.  Tous les navigateurs modernes doivent maintenant inclure l’en-tête `Origin` par spécification HTTP pour s’assurer que CORS est appliqué. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt) 

---

### <a name="general-availability---on-the-my-apps-portal-users-can-choose-to-view-their-apps-in-a-list"></a>Disponibilité générale : dans le portail Mes applications, les utilisateurs peuvent choisir d’afficher leurs applications dans une liste

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Par défaut, Mes applications affiche les applications sous forme de grille. Les utilisateurs peuvent désormais basculer vers la vue Mes applications pour voir les applications dans une liste. [Plus d’informations](../user-help/my-apps-portal-end-user-access.md)
 
---

### <a name="general-availability---new-and-enhanced-device-related-audit-logs"></a>Disponibilité générale - Nouveaux journaux d’audit et améliorations

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Audit  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
Les administrateurs peuvent désormais voir divers journaux d’audit, nouveaux et améliorés, liés aux appareils. Les nouveaux journaux d’audit comprennent la création et la suppression des informations d’identification sans mot de passe (connexion par téléphone, clé FIDO2 et Windows Hello Entreprise), l’inscription et la désinscription d’appareils et la précréation d’un appareil ou la suppression d’un appareil précréé. En outre, des améliorations mineures ont été apportées aux journaux d’audit existants liés aux appareils, avec l’ajout de plus de détails sur les appareils. [Plus d’informations](../reports-monitoring/concept-audit-logs.md)

---

### <a name="general-availability---azure-ad-users-can-now-view-and-report-suspicious-sign-ins-and-manage-their-accounts-within-microsoft-authenticator"></a>Disponibilité générale : les utilisateurs Azure AD peuvent désormais voir et signaler les connexions suspectes et gérer leurs comptes dans Microsoft Authenticator

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette fonctionnalité permet aux utilisateurs Azure AD de gérer leurs comptes professionnels ou scolaires au sein de l’application Microsoft Authenticator. Les fonctionnalités de gestion permettront aux utilisateurs de voir l’historique et l’activité de connexion. Ils peuvent signaler toute activité suspecte ou inconnue en fonction de l’historique et de l’activité de connexion, si nécessaire. Les utilisateurs peuvent également modifier le mot de passe de leur compte Azure AD et mettre à jour les informations de sécurité de ce compte. [Plus d’informations](../user-help/my-account-portal-sign-ins-page.md)
 
---

### <a name="general-availability---new-ms-graph-apis-for-role-management"></a>Disponibilité générale - Nouvelles API MS Graph pour la gestion des rôles

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Les nouvelles API de gestion des rôles sur le point de terminaison MS Graph v1.0 sont en disponibilité générale. Au lieu des anciens [rôles d’annuaire](/graph/api/resources/directoryrole?view=graph-rest-1.0&preserve-view=true), utilisez [unifiedRoleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-1.0&preserve-view=true) et [unifiedRoleAssignment](/graph/api/resources/unifiedroleassignment?view=graph-rest-1.0&preserve-view=true).
 
---

### <a name="general-availability---access-packages-can-expire-after-number-of-hours"></a>Disponibilité générale : Les packages d’accès peuvent expirer après un certain nombre d’heures

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation

Il est désormais possible dans la gestion des droits d’utilisation de configurer un package d’accès qui expirera en quelques heures en plus de la prise en charge précédente de jours ou de dates bien spécifiques. [Plus d’informations](../governance/entitlement-management-access-package-create.md#lifecycle)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2021"></a>Nouveaux connecteurs d’approvisionnement dans la galerie d’applications Azure AD - Septembre 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [BLDNG APP](../saas-apps/bldng-app-provisioning-tutorial.md)
- [Cato Networks](../saas-apps/cato-networks-provisioning-tutorial.md)
- [Rouse Sales](../saas-apps/rouse-sales-provisioning-tutorial.md)
- [SchoolStream ASA](../saas-apps/schoolstream-asa-provisioning-tutorial.md)
- [Taskize Connect](../saas-apps/taskize-connect-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, voir [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../manage-apps/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Septembre 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En septembre 2021, nous avons ajouté les 44 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Studybugs](https://studybugs.com/signin), [Yello](https://yello.co/yello-for-microsoft-teams/), [LawVu](../saas-apps/lawvu-tutorial.md), [Formate eVo Mail](https://www.document-genetics.co.uk/formate-evo-erp-output-management), [Revenue Grid](https://app.revenuegrid.com/login), [Orbit for Office 365](https://azuremarketplace.microsoft.com/marketplace/apps/aad.orbitforoffice365?tab=overview), [Upmarket](https://app.upmarket.ai/), [Alinto Protect](https://protect.alinto.net/), [Cloud Concinnity](https://cloudconcinnity.com/), [Matlantis](https://matlantis.com/), [ModelGen for Visio (MG4V)](https://crecy.com.au/model-gen/), [NetRef: Classroom Management](https://oauth.net-ref.com/microsoft/sso), [VergeSense](../saas-apps/vergesense-tutorial.md), [iAuditor](../saas-apps/iauditor-tutorial.md), [Secutraq](https://secutraq.net/login), [Active and Thriving](../saas-apps/active-and-thriving-tutorial.md), [Inova](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=1bacdba3-7a3b-410b-8753-5cc0b8125f81&response_type=code&redirect_uri=https:%2f%2fbroker.partneringplace.com%2fpartner-companion%2f&code_challenge_method=S256&code_challenge=YZabcdefghijklmanopqrstuvwxyz0123456789._-~&scope=1bacdba3-7a3b-410b-8753-5cc0b8125f81/.default), [TerraTrue](../saas-apps/terratrue-tutorial.md), [Facebook Work Accounts](../saas-apps/facebook-work-accounts-tutorial.md), [Beyond Identity Admin Console](../saas-apps/beyond-identity-admin-console-tutorial.md), [Visult](https://app.visult.io/), [ENGAGE TAG](https://app.engagetag.com/), [Appaegis Isolation Access Cloud](../saas-apps/appaegis-isolation-access-cloud-tutorial.md), [CrowdStrike Falcon Platform](../saas-apps/crowdstrike-falcon-platform-tutorial.md), [MY Emergency Control](https://my-emergency.co.uk/app/auth/login), [AlexisHR](../saas-apps/alexishr-tutorial.md), [Teachme Biz](../saas-apps/teachme-biz-tutorial.md), [Zero Networks](../saas-apps/zero-networks-tutorial.md), [Mavim iMprove](https://improve.mavimcloud.com/), [Azumuta](https://app.azumuta.com/login?microsoft=true), [Frankli](https://beta.frankli.io/login), [Amazon Managed Grafana](../saas-apps/amazon-managed-grafana-tutorial.md), [Productive](../saas-apps/productive-tutorial.md), [Create!Webフロー](../saas-apps/createweb-tutorial.md), [Evercate](https://evercate.com/us/sign-up/), [Ezra Coaching](../saas-apps/ezra-coaching-tutorial.md), [Baldwin Safety and Compliance](../saas-apps/baldwin-safety-&-compliance-tutorial.md), [Nulab Pass (Backlog,Cacoo,Typetalk)](../saas-apps/nulab-pass-tutorial.md), [Metatask](../saas-apps/metatask-tutorial.md), [Contrast Security](../saas-apps/contrast-security-tutorial.md), [Animaker](../saas-apps/animaker-tutorial.md), [Traction Guest](../saas-apps/traction-guest-tutorial.md), [True Office Learning - LIO](../saas-apps/true-office-learning-lio-tutorial.md), [Qiita Team](../saas-apps/qiita-team-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

###  <a name="gmail-users-signing-in-on-microsoft-teams-mobile-and-desktop-clients-will-sign-in-with-device-login-flow-starting-september-30-2021"></a>Les utilisateurs de Gmail qui se connectent aux clients Microsoft Teams mobiles et de bureau se connectent avec le workflow de connexion d’appareil à partir du 30 septembre 2021

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
À compter du 30 2021 septembre, les invités B2B Azure AD et les clients Azure AD B2C qui se connectent avec leur compte Gmail en libre-service ou échangé auront à passer une étape de connexion supplémentaire. Les utilisateurs seront invités à entrer un code dans une nouvelle fenêtre du navigateur pour terminer la connexion dans les clients mobiles et de bureau de Microsoft Teams. Si vous ne l’avez pas déjà fait, veillez à modifier vos applications pour utiliser le navigateur système pour la connexion. Pour plus d’informations, consultez  [Interface utilisateur incorporée ou Web System](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) dans la documentation de MSAL.NET. Tous les kits SDK MSAL utilisent la vue web système par défaut. 

Comme le processus de connexion de l’appareil démarrera le 30 septembre 2021, il est possible qu’il ne soit pas immédiatement disponible dans votre région. S’il n’est pas encore disponible, vos utilisateurs finaux seront satisfaits de l’écran d’erreur affiché dans le document jusqu’à ce qu’il soit déployé dans votre région.) Pour plus d’informations sur le déroulement de la connexion des appareils et des détails sur la demande d’extension à Google, consultez [Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).
 
---

### <a name="improved-conditional-access-messaging-for-non-compliant-device"></a>Amélioration de la messagerie d’accès conditionnel pour les appareils non conformes

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Le texte et la conception sur l’écran de blocage de l’accès conditionnel présenté aux utilisateurs lorsque leur appareil est marqué comme non conforme ont été mis à jour. Les utilisateurs seront bloqués jusqu’à ce qu’ils prennent les mesures nécessaires pour respecter les stratégies de conformité des appareils de leur entreprise. En outre, nous avons rationalisé le processus permettant à un utilisateur d’ouvrir son portail de gestion des appareils. Ces améliorations s’appliquent à toutes les plateformes de système d’exploitation prises en charge par l’accès conditionnel. [En savoir plus](https://support.microsoft.com/account-billing/troubleshooting-the-you-can-t-get-there-from-here-error-message-479a9c42-d9d1-4e44-9e90-24bbad96c251) 

---
 
## <a name="august-2021"></a>Août 2021

### <a name="new-major-version-of-aadconnect-available"></a>Nouvelle version principale de AADConnect disponible

**Type :** Résolution  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Nous avons publié une nouvelle version principale de Azure Active Directory Connect. Cette version contient plusieurs mises à jour des composants fondamentaux des versions les plus récentes. Elle est recommandée pour tous les clients qui utilisent Azure AD Connect. [Plus d’informations](../hybrid/whatis-azure-ad-connect-v2.md)
 
---

### <a name="public-preview---azure-ad-single-sign-on-and-device-based-conditional-access-support-in-firefox-on-windows-10"></a>Préversion publique : authentification unique Azure AD et prise en charge de l’accès conditionnel basé sur les appareils dans Firefox sur Windows 10

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** SSO
 

Nous prenons désormais en charge l’authentification unique (SSO) native et l’accès conditionnel basé sur les appareils au navigateur Firefox sur Windows 10 et Windows Server 2019. La prise en charge est également disponible pour la version 91 de Firefox. [Plus d’informations](../conditional-access/require-managed-devices.md#prerequisites)
 
---

### <a name="public-preview---beta-ms-graph-apis-for-azure-ad-access-reviews-returns-list-of-contacted-reviewer-names"></a>Préversion publique : les API Graph MS en version bêta pour les révisions d’accès Azure AD retournent la liste des noms des réviseurs contactés

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 

Nous avons publié une version bêta de l’API Graph MS pour les révisions d’accès Azure AD. L’API dispose de méthodes pour retourner une liste de noms de réviseurs contactés en plus du type de réviseur. [Plus d’informations](/graph/api/resources/accessreviewinstance?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="general-availability---register-or-join-devices-user-action-in-conditional-access"></a>Disponibilité générale : action de l’utilisateur « Inscrire ou joindre des appareils » en accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

L’action de l’utilisateur « Inscrire ou joindre des appareils » est généralement disponible dans l’accès conditionnel. Cette action de l’utilisateur vous permet de contrôler les stratégies d’authentification multifacteur (MFA) pour l’inscription d’appareils Azure Active Directory (AD). Actuellement, cette action utilisateur vous permet uniquement d’activer l’authentification multifacteur en tant que contrôle lorsque les utilisateurs inscrivent ou joignent des appareils à Azure AD. Les autres contrôles qui ne s’appliquent pas à l’inscription d’appareils Azure AD, ou qui en dépendent, continuent d’être désactivés avec cette action utilisateur. [Plus d’informations](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)

---

### <a name="general-availability---customers-can-scope-reviews-of-privileged-roles-to-eligible-or-permanent-assignments"></a>Disponibilité générale : les clients peuvent étendre les révisions de rôles privilégiés à des affectations éligibles ou permanentes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les administrateurs peuvent désormais créer des révisions d’accès des affectations permanentes ou éligibles à des rôles de ressources Azure AD ou Azure privilégiés. [Plus d’informations](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)
 
--- 

### <a name="general-availability---assign-roles-to-azure-active-directory-ad-groups"></a>Disponibilité générale : attribuer des rôles à des groupes Azure Active Directory (AD)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 

L’affectation de rôles à des groupes Azure AD est désormais mis à la disposition générale. Cette fonctionnalité peut simplifier la gestion des attributions de rôles dans Azure AD pour vos administrateurs généraux et administrateurs de rôles privilégiés. [Plus d’informations](../roles/groups-concept.md) 
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---aug-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Août 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En août 2021, nous avons ajouté les 46 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Siriux Customer Dashboard](https://portal.siriux.tech/login), [STRUXI](https://struxi.app/), [Autodesk Construction Cloud - Meetings](https://acc.autodesk.com/), [Eccentex AppBase pour Azure](../saas-apps/eccentex-appbase-for-azure-tutorial.md), [Bookado](https://adminportal.bookado.io/), [FilingRamp](https://app.filingramp.com/login), [BenQ IAM](../saas-apps/benq-iam-tutorial.md), [Rhombus Systems](../saas-apps/rhombus-systems-tutorial.md), [CorporateExperience](../saas-apps/corporateexperience-tutorial.md), [TutorOcean](../saas-apps/tutorocean-tutorial.md), [Bookado Device](https://adminportal.bookado.io/), [HiFives-AD-SSO](https://app.hifives.in/login/azure), [Darzin](https://au.darzin.com/), [Simply Stakeholders](https://au.simplystakeholders.com/), [KACTUS HCM - Smart People](https://kactusspc.digitalware.co/), [Five9 UC Adapter for Microsoft Teams V2](https://uc.five9.net/?vendor=msteams), [Automation Center](https://automationcenter.cognizantgoc.com/portal/boot/signon), [Cirrus Identity Bridge for Azure AD](../saas-apps/cirrus-identity-bridge-for-azure-ad-tutorial.md), [ShiftWizard SAML](../saas-apps/shiftwizard-saml-tutorial.md), [Safesend Returns](https://www.safesendwebsites.com/), [Brushup](../saas-apps/brushup-tutorial.md), [directprint.io Cloud Print Administration](../saas-apps/directprint-io-cloud-print-administration-tutorial.md), [plain-x](https://app.plain-x.com/#/login),[X-point Cloud](../saas-apps/x-point-cloud-tutorial.md), [SmartHub INFER](../saas-apps/smarthub-infer-tutorial.md), [Fresh Relevance](../saas-apps/fresh-relevance-tutorial.md), [FluentPro G.A. Suite](https://gas.fluentpro.com/Account/SSOLogin?provider=Microsoft), [Clockwork Recruiting](../saas-apps/clockwork-recruiting-tutorial.md), [WalkMe SAML2.0](../saas-apps/walkme-saml-tutorial.md), [Sideways 6](https://app.sideways6.com/account/login?ReturnUrl=/), [Kronos Workforce Dimensions](../saas-apps/kronos-workforce-dimensions-tutorial.md), [SysTrack Cloud Edition](https://cloud.lakesidesoftware.com/Cloud/Account/Login), [mailworx Dynamics CRM Connector](https://www.mailworx.info/), [Palo Alto Networks Cloud Identity Engine - Cloud Authentication Service](../saas-apps/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial.md), [Peripass](https://accounts.peripass.app/v1/sso/challenge), [JobDiva](https://www.jobssos.com/index_azad.jsp?SSO=AZURE&ID=1), [Sanebox For Office365](https://sanebox.com/login), [Tulip](../saas-apps/tulip-tutorial.md), [HP Wolf Security](https://bec-pocda37b439.bromium-online.com/gui/), [Genesys Engage cloud Email](https://login.microsoftonline.com/common/oauth2/authorize?prompt=consent&accessType=offline&state=07e035a7-6fb0-4411-afd9-efa46c9602f9&resource=https://graph.microsoft.com/&response_type=code&redirect_uri=https://iwd.api01-westus2.dev.genazure.com/iwd/v3/emails/oauth2/microsoft/callback&client_id=36cd21ab-862f-47c8-abb6-79facad09dda), [Meta Wiki](https://meta.dunkel.eu/), [Palo Alto Networks Cloud Identity Engine Directory Sync](https://directory-sync.us.paloaltonetworks.com/directory?instance=L2qoLVONpBHgdJp1M5K9S08Z7NBXlpi54pW1y3DDu2gQqdwKbyUGA11EgeaDfZ1dGwn397S8eP7EwQW3uyE4XL), [Valarea](https://www.valarea.com/en/download), [LanSchool Air](../saas-apps/lanschool-air-tutorial.md), [Catalyst](https://www.catalyst.org/sso-login/), [Webcargo](../saas-apps/webcargo-tutorial.md)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---august-2021"></a>Nouveaux connecteurs de provisionnement dans la galerie d’applications Azure AD - Août 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Chatwork](../saas-apps/chatwork-provisioning-tutorial.md)
- [FreshService](../saas-apps/freshservice-provisioning-tutorial.md)
- [InviteDesk](../saas-apps/invitedesk-provisioning-tutorial.md)
- [Maptician](../saas-apps/maptician-provisioning-tutorial.md)

Pour savoir comment mieux sécuriser votre organisation grâce au provisionnement automatisé du compte utilisateur, consultez Automatiser le provisionnement d’utilisateurs pour les applications SaaS avec Azure AD.
 
---

### <a name="multifactor-mfa-fraud-report--new-audit-event"></a>Rapport sur les fraudes d’authentification multifacteur (MFA) – nouvel événement d’audit

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Protection et sécurité des identités
 

Pour aider les administrateurs à comprendre que l’authentification multifacteur de leurs utilisateurs est bloquée à la suite d’un rapport de fraude, nous avons ajouté un nouvel événement d’audit. Cet événement d’audit est suivi lorsque l’utilisateur signale une fraude. Le journal d’audit est disponible en plus des informations existantes dans les journaux de connexion concernant le rapport des fraudes. Pour savoir comment obtenir le rapport d’audit, consultez [Authentification multifacteur pour l’alerte de fraude](../authentication/howto-mfa-mfasettings.md#fraud-alert).

---

### <a name="improved-low-risk-detections"></a>Détections des risques faibles améliorées

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités

Pour améliorer la qualité des alertes à risque faible émises par Identity Protection, nous avons modifié l’algorithme pour qu’il produise moins de connexions à risque faible. Les organisations peuvent constater une réduction significative de la connexion à faible risque dans leur environnement. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="non-interactive-risky-sign-ins"></a>Connexions risquées non interactives

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Identity Protection émet désormais des connexions risquées sur les connexions non interactives. Les administrateurs peuvent trouver ces connexions à risque à l’aide du filtre de **type de connexion** dans le rapport sur les connexions à risque. [Plus d’informations](../identity-protection/howto-identity-protection-investigate-risk.md)
 
---

### <a name="change-from-user-administrator-to-identity-governance-administrator-in-entitlement-management"></a>Passer d’un administrateur d’utilisateur à un administrateur de gouvernance des identités dans la gestion des droits d'utilisation 

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Rôles  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les attributions d’autorisations pour gérer les packages d’accès et d’autres ressources dans la gestion des droits d'utilisation passent du rôle Administrateur d’utilisateurs au rôle Administrateur de gouvernance des identités. 

Les utilisateurs qui se sont vu attribuer le rôle d’administrateur d’utilisateurs ne pourront plus créer de catalogues ni gérer les packages d’accès dans un catalogue dont ils ne sont pas propriétaires. Si le rôle d’administrateur d’utilisateurs de votre organisation a été attribué à des utilisateurs pour configurer des catalogues, des packages d’accès ou des stratégies de la gestion des droits d’utilisation, ils devront obtenir une nouvelle attribution. Vous devez plutôt attribuer à ces utilisateurs le rôle d’administrateur de gouvernance des identités. [En savoir plus](../governance/entitlement-management-delegate.md)

---

### <a name="windows-azure-active-directory-connector-is-deprecated"></a>L’utilisation du connecteur Windows Azure Active Directory est déconseillée

**Type :** Déprécié  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le connecteur Windows Azure AD pour FIM est dans l’état feature freeze et il est déconseillé de l’utiliser. La solution de remplacement de FIM et du connecteur Azure AD a été annoncée. Les déploiements existants doivent migrer vers [Azure AD Connect](../hybrid/whatis-hybrid-identity.md), Azure AD Connect Sync ou le [connecteur Microsoft Graph](/microsoft-identity-manager/microsoft-identity-manager-2016-connector-graph), car les interfaces internes utilisées par le connecteur Azure AD pour FIM sont retirées d’Azure AD. [Plus d’informations](/microsoft-identity-manager/microsoft-identity-manager-2016-deprecated-features)

---

### <a name="retirement-of-older-azure-ad-connect-versions"></a>Mise hors service des anciennes versions de Azure AD Connect

**Type :** Déprécié  
**Catégorie de service :** AD Connect  
**Fonctionnalité de produit :** User Management
 
À compter du 31 août 2022, toutes les versions V1 de Azure AD Connect seront mises hors service. Si vous ne l’avez pas déjà fait, vous devez mettre à jour votre serveur vers Azure AD Connect V2.0. Vous devez vous assurer que vous exécutez une version récente de Azure AD Connect pour bénéficier d'une expérience de support optimale.

Si vous utilisez une version de Azure AD Connect qui a été mise hors service, elle peut cesser de fonctionner de manière inattendue. Vous ne disposerez peut-être pas non plus des derniers correctifs de sécurité, des améliorations des performances, du dépannage, des outils de diagnostic et des améliorations du service. De même, si vous avez besoin d’aide, nous ne serons pas en mesure de vous fournir le niveau de service dont votre organisation a besoin.

Consultez [Azure Active Directory Connect V2.0](../hybrid/whatis-azure-ad-connect-v2.md), pour savoir ce qui a changé dans la version 2.0 et comment cette modification vous affecte.

---

### <a name="retirement-of-support-for-installing-mim-on-windows-server-2008-r2-or-sql-server-2008-r2"></a>Fin de la prise en charge de l’installation de MIM sur Windows Server 2008 R2 ou SQL Server 20082 R

**Type :** Déprécié  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le déploiement de MIM Sync, Service, Portal ou CM sur Windows Server 2008 R2 ou l’utilisation de SQL Server 2008 R2 comme base de données sous-jacente est déconseillé, car ces plateformes ne sont plus en support standard. L’installation de MIM Sync et d’autres composants sur Windows Server 2016 ou version ultérieure, et sur SQL Server 2016 ou version ultérieure, est recommandée.

Le déploiement de MIM pour Privileged Access Management avec un contrôleur de domaine Windows Server 2012 R2 dans la forêt PRIV est déconseillé. Utilisez Windows Server 2016 ou une version ultérieure d’Active Directory, avec Windows Server 2016 niveau fonctionnel, pour votre domaine de forêt PRIV. Le niveau fonctionnel de Windows Server 2012 R2 est toujours autorisé pour le domaine d’une forêt CORP. [Plus d’informations](/microsoft-identity-manager/microsoft-identity-manager-2016-supported-platforms)

---

## <a name="july-2021"></a>Juillet 2021

### <a name="new-google-sign-in-integration-for-azure-ad-b2c-and-b2b-self-service-sign-up-and-invited-external-users-will-stop-working-starting-july-12-2021"></a>La nouvelle intégration de la connexion Google pour l’inscription en libre-service et les utilisateurs externes invités d’Azure AD B2C et B2B cessera de fonctionner à partir du 12 juillet 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Nous avons annoncé précédemment que [l’exception concernant les vues web incorporées de l’authentification Gmail expirerait au cours du second semestre 2021](https://www.yammer.com/cepartners/threads/1188371962232832).

Le 7 juillet 2021, nous avons appris de Google que certaines de ces restrictions s’appliqueront à partir du **12 juillet 2021**. Les clients Azure AD B2B et B2C qui configurent une nouvelle connexion Google ID dans leurs applications métier ou personnalisées pour inviter des utilisateurs externes ou activer l’inscription en libre-service verront les restrictions appliquées immédiatement. Par conséquent, les utilisateurs finaux verront apparaître un écran d’erreur qui bloquera leur connexion Gmail si l’authentification n’est pas déplacée vers une vue web du système. Pour plus d’informations, consultez la documentation dont le lien figure ci-dessous. 

La plupart des applications utilisent la vue web du système par défaut, et ne seront pas concernées par ce changement Cela ne concerne que les clients qui utilisent des vues web incorporées (le paramètre non défini par défaut). Nous conseillons aux clients de déplacer l’authentification de leur application vers les navigateurs système avant de créer de nouvelles intégrations Google. Pour savoir comment passer aux navigateurs système pour les authentifications Gmail, lisez la section Interface utilisateur web incorporée ou système dans la documentation [Utilisation de navigateurs web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Tous les kits SDK MSAL utilisent la vue web système par défaut. [Plus d’informations](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support)

---

### <a name="google-sign-in-on-embedded-web-views-expiring-september-30-2021"></a>Connexion Google sur les vues web incorporées expirant le 30 septembre 2021

**Type :** Modification planifiée  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Il y a environ deux mois, nous avons annoncé que l’exception concernant les vues web incorporées pour l’authentification Gmail expirerait au cours du second semestre 2021. 

Récemment, Google a précisé que la date serait le **30 septembre 2021**. 

Le lancement mondial de cette exception débutera le 30 septembre 2021. Les invités Azure AD B2B se connectant avec leurs comptes Gmail seront désormais invités à saisir un code dans une fenêtre de navigateur distincte pour terminer la connexion sur les clients mobiles et bureau de Microsoft Teams. Cela s’applique aux invités qui ont été invités et aux invités qui se sont connectés en utilisant l’inscription en libre-service. 

Les clients Azure AD B2C qui ont configuré des authentifications Gmail en vue web incorporée dans leurs applications métier/personnalisées ou qui ont déjà des intégrations Google ne pourront plus laisser leurs utilisateurs se connecter avec des comptes Gmail. Pour pallier ce problème, veillez à modifier vos applications afin d’utiliser le navigateur système pour la connexion. Pour plus d’informations, lisez la section Interface utilisateur web incorporée ou système dans la documentation [Utilisation de navigateurs web (MSAL.NET)](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui). Tous les kits SDK MSAL utilisent la vue web système par défaut. 

Étant donné que le flux de connexion à l’appareil commencera à être déployé le 30 septembre 2021, il est probable qu’il ne soit pas encore déployé dans votre région (auquel cas, vos utilisateurs finaux seront confrontés à l’écran d’erreur présenté dans la documentation jusqu’à ce que le déploiement soit terminé dans votre région). 

Pour plus d’informations sur les scénarios concernés connus et sur l’expérience à laquelle vos utilisateurs peuvent s’attendre, lisez [Ajouter Google comme fournisseur d’identité pour les utilisateurs invités B2B](../external-identities/google-federation.md#deprecation-of-web-view-sign-in-support).

---

### <a name="bug-fixes-in-my-apps"></a>Correctifs de bogues dans Mes applications

**Type :** Résolution  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
- Auparavant, la présence de la bannière recommandant l’utilisation de collections faisait défiler le contenu derrière l’en-tête. Ce problème a été résolu. 
- Auparavant, il y avait un autre problème lors de l’ajout d’applications à une collection, où l’ordre des applications dans la collection Toutes les applications était réorganisé de manière aléatoire. Ce problème a également été résolu. 

Pour plus d’informations sur Mes applications, lisez [Se connecter et démarrer des applications à partir du portail Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

---

### <a name="public-preview----application-authentication-method-policies"></a>Préversion publique : Stratégies de méthode d’authentification des applications

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MS Graph  
**Fonctionnalité de produit :** Expérience de développement
 
Des stratégies de méthode d’authentification des applications dans MS Graph qui permettent aux administrateurs informatiques d’appliquer une durée de vie sur les informations d’identification, les secrets et les mots de passe des applications ou de bloquer complètement l’utilisation des secrets. Les stratégies peuvent être appliquées à l’ensemble d’un locataire en tant que configuration par défaut et elles peuvent être étendues à des applications ou des principaux de service spécifiques. [Plus d’informations](/graph/api/resources/policy-overview?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="public-preview----authentication-methods-nudge-to-download-microsoft-authenticator"></a>Préversion publique : Méthodes d’authentification : incitation à télécharger Microsoft Authenticator

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Authentification utilisateur
 
La stratégie d’incitation Authenticator aide les administrateurs à renforcer la sécurité de leur organisation en incitant les utilisateurs à adopter l’application Microsoft Authenticator. Avant cette fonctionnalité, un administrateur n’avait aucun moyen de pousser ses utilisateurs à configurer l’application Authenticator. 

L’incitation permet à l’administrateur d’inclure ou d’exclure des utilisateurs et des groupes de la démarche afin de garantir une adoption en douceur dans l’ensemble de l’organisation. [En savoir plus](../authentication/how-to-nudge-authenticator-app.md)
 
---

### <a name="public-preview----separation-of-duties-check"></a>Préversion publique : Vérification de la séparation des tâches 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Dans la gestion des droits d’utilisation d’Azure AD, un administrateur peut définir qu’un package d’accès est incompatible avec un autre package d’accès ou avec un groupe. Les utilisateurs dont les appartenances sont incompatibles seront alors incapables de demander un accès supplémentaire. [Plus d’informations](../governance/entitlement-management-access-package-request-policy.md#prevent-requests-from-users-with-incompatible-access-preview)
 
---

### <a name="public-preview----identity-protection-logs-in-log-analytics-storage-accounts-and-event-hubs"></a>Préversion publique : Journaux Identity Protection dans Log Analytics, Comptes de stockage et Event Hubs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Vous pouvez désormais envoyer les journaux des utilisateurs à risque et des détections de risque à Azure Monitor, Comptes de stockage ou Log Analytics à l’aide des paramètres de diagnostic dans le panneau Azure AD. [Plus d’informations](../identity-protection/howto-export-risk-data.md)
 
---

### <a name="public-preview----application-proxy-api-addition-for-backend-ssl-certificate-validation"></a>Préversion publique : Ajout de l’API Proxy d’application pour la validation des certificats SSL en back-end

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Proxy d’application  
**Fonctionnalité de produit :** Contrôle d’accès
 
Le type de ressource onPremisesPublishing comprend désormais la propriété « isBackendCertificateValidationEnabled » qui indique si la validation des certificat SSL en back-end est activée pour l’application. Pour toutes les nouvelles applications Proxy d’application, la propriété est définie sur true par défaut. Pour toutes les applications existantes, la propriété est définie sur false. Pour plus d’informations, lisez l’API du [type de ressource onPremisesPublishing](/graph/api/resources/onpremisespublishing?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---improved-authenticator-setup-experience-for-add-azure-ad-account-in-microsoft-authenticator-app-by-directly-signing-into-the-app"></a>Disponibilité générale : Amélioration de l’expérience de configuration d’Authenticator pour ajouter un compte Azure AD dans l’application Microsoft Authenticator en se connectant directement à l’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Application Microsoft Authenticator  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les utilisateurs peuvent désormais utiliser leurs méthodes d’authentification existantes pour se connecter directement à l’application Microsoft Authenticator afin de configurer leurs informations d’identification. Les utilisateurs n’ont plus besoin de scanner un code QR et peuvent utiliser un passe d’accès temporaire ou un mot de passe + SMS (ou une autre méthode d’authentification) pour configurer leur compte dans l’application Authenticator.

Cela améliore le processus d’approvisionnement des informations d’identification de l’utilisateur pour l’application Microsoft Authenticator et offre à l’utilisateur final une méthode en libre-service pour approvisionner l’application. [Plus d’informations](https://support.microsoft.com/account-billing/add-your-work-or-school-account-to-the-microsoft-authenticator-app-43a73ab5-b4e8-446d-9e54-2a4cb8e4e93c#sign-in-with-your-credentials)
 
---

### <a name="general-availability---set-manager-as-reviewer-in-azure-ad-entitlement-management-access-packages"></a>Disponibilité générale : Définir le gestionnaire comme réviseur dans les packages d’accès de gestion des droits d’utilisation d’Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les packages d’accès dans la gestion des droits d’utilisation Azure AD prennent désormais en charge la définition du responsable de l’utilisateur comme réviseur pour les révisions d’accès régulières. [Plus d’informations](../governance/entitlement-management-access-reviews-create.md)

---

### <a name="general-availability---enable-external-users-to-self-service-sign-up-in-aad-using-msa-accounts"></a>Disponibilité générale : Permettre aux utilisateurs externes de s’inscrire en libre-service dans AAD à l’aide de comptes MSA

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 
Les utilisateurs peuvent désormais permettre aux utilisateurs externes de se connecter en libre-service dans Azure Active Directory à l’aide de comptes Microsoft. [Plus d’informations](../external-identities/microsoft-account.md)
 
---
 
### <a name="general-availability---external-identities-self-service-sign-up-with-email-one-time-passcode"></a>Disponibilité générale : Inscription en libre-service des identités externes avec un code secret à usage unique envoyé par e-mail

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2B  
**Fonctionnalité de produit :** B2B/B2C
 

Les utilisateurs peuvent désormais permettre aux utilisateurs externes de s’inscrire en libre-service dans Azure Active Directory à l’aide de leur adresse e-mail et de leur code secret à usage unique. [Plus d’informations](../external-identities/one-time-passcode.md)
 
---

### <a name="general-availability---anomalous-token"></a>Disponibilité générale : Jeton anormal

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
La détection de jetons anormaux est désormais disponible dans Identity Protection. Cette fonctionnalité peut détecter la présence de caractéristiques anormales dans le jeton, telles que la durée d’activité et l’authentification à partir d’une adresse IP inconnue. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---

### <a name="general-availability---register-or-join-devices-in-conditional-access"></a>Disponibilité générale : Inscrire ou joindre des appareils en accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
L’action utilisateur Inscrire ou joindre des appareils en accès conditionnel est désormais en disponibilité générale. Cette action utilisateur vous permet de contrôler les stratégies d’authentification multifacteur pour l’inscription d’appareils Azure AD. 

Actuellement, cette action utilisateur vous permet uniquement d’activer l’authentification multifacteur en tant que contrôle lorsque les utilisateurs inscrivent ou joignent des appareils à Azure AD. Les autres contrôles qui ne s’appliquent pas à l’inscription d’appareils Azure AD, ou qui en dépendent, continuent d’être désactivés avec cette action utilisateur. [Plus d’informations](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2021"></a>Nouveaux connecteurs d’approvisionnement dans la galerie d’applications Azure AD – Juillet 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [Clebex](../saas-apps/clebex-provisioning-tutorial.md)
- [Exium](../saas-apps/exium-provisioning-tutorial.md)
- [SoSafe](../saas-apps/sosafe-provisioning-tutorial.md)
- [Talentech](../saas-apps/talentech-provisioning-tutorial.md)
- [Thrive LXP](../saas-apps/thrive-lxp-provisioning-tutorial.md)
- [Vonage](../saas-apps/vonage-provisioning-tutorial.md)
- [Compresser](../saas-apps/zip-provisioning-tutorial.md)
- [TimeClock 365](../saas-apps/timeclock-365-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes d’utilisateur, lisez [Automatiser l’attribution d’utilisateurs à des applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="changes-to-security-and-microsoft-365-group-settings-in-azure-portal"></a>Modifications des paramètres de sécurité et de groupe Microsoft 365 dans Portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion des groupes  
**Fonctionnalité de produit :** Répertoire
 

Dans le passé, les utilisateurs pouvaient créer des groupes de sécurité et des groupes Microsoft 365 dans le portail Azure. Désormais, les utilisateurs auront la possibilité de créer des groupes sur le portail Azure, PowerShell et des API. Les clients sont tenus de vérifier et de mettre à jour les nouveaux paramètres qui ont été configurés pour leur organisation. [En savoir plus](../enterprise-users/groups-self-service-management.md#group-settings)
 
---

### <a name="all-apps-collection-has-been-renamed-to-apps"></a>La collection « Toutes les applications » a été renommée en « Applications »

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 
Dans le portail Mes applications, la collection qui s’appelait « Toutes les applications » a été renommée pour s’appeler « Applications ». Le produit évoluant, « Applications » est un nom plus approprié pour cette collection par défaut. [Plus d’informations](../manage-apps/my-apps-deployment-plan.md#plan-the-user-experience)
 
---
 
## <a name="june-2021"></a>Juin 2021

### <a name="context-panes-to-display-risk-details-in-identity-protection-reports"></a>Volets contextuels pour afficher les détails des risques dans les rapports Identity Protection

**Type :** Modification planifiée  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Pour les rapports sur les utilisateurs à risque, les connexions risquées et les détections de risque dans Identity Protection, les détails du risque d’une entrée sélectionnée seront affichés dans un volet contextuel apparaissant à droite de la page Juillet 2021. Ce changement n’a d’incidence que sur l’interface utilisateur et n’affecte aucune des fonctionnalités existantes. Pour en savoir plus sur le fonctionnement de ces fonctionnalités, reportez-vous à [Guide pratique : Examiner les risques](../identity-protection/howto-identity-protection-investigate-risk.md).
 
---

### <a name="public-preview----create-azure-ad-access-reviews-of-service-principals-that-are-assigned-to-privileged-roles"></a>Préversion publique : Créer des révisions d’accès Azure AD des principaux de service affectés à des rôles privilégiés

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
 Vous pouvez utiliser les révisions d’accès Azure AD pour passer en revue l’accès du principal de service aux rôles privilégiés Azure AD et aux rôles de ressource Azure. [Plus d’informations](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md#create-access-reviews)
 
---

### <a name="public-preview----group-owners-in-azure-ad-can-create-and-manage-azure-ad-access-reviews-for-their-groups"></a>Préversion publique : Les propriétaires de groupes dans Azure AD peuvent créer et gérer des révisions d’accès Azure AD pour leurs groupes

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les propriétaires de groupes dans Azure AD peuvent désormais créer et gérer des révisions d’accès Azure AD sur leurs groupes. Cette fonctionnalité peut être activée par les administrateurs clients via les paramètres de révision d’accès Azure AD et est désactivée par défaut. [Plus d’informations](../governance/create-access-review.md#allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview)
 
---

### <a name="public-preview----customers-can-scope-access-reviews-of-privileged-roles-to-just-users-with-eligible-or-active-access"></a>Préversion publique : Les clients peuvent limiter les révisions d’accès de rôles privilégiés aux seuls utilisateurs ayant un accès actif ou admissible

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Lorsque les administrateurs créent des révisions d’accès des attributions de rôles privilégiés, ils peuvent limiter les révisions uniquement aux utilisateurs attribués admissibles ou uniquement aux utilisateurs attribués actifs. [Plus d’informations](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)
 
---

### <a name="public-preview----microsoft-graph-apis-for-mobility-mdmmam-management-policies"></a>Préversion publique : Stratégies de gestion des API Microsoft Graph pour la mobilité (MDM/MAM)

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Gestion du cycle de vie des appareils
 
La prise en charge de Microsoft Graph pour la configuration de la mobilité (MDM/MAM) dans Azure AD est en préversion publique. Les administrateurs peuvent configurer l’étendue de l’utilisateur et les URL des applications MDM comme Intune à l’aide de Microsoft Graph v1.0. Pour plus d’informations, consultez [Type de ressource mobilityManagementPolicy](/graph/api/resources/mobilitymanagementpolicy?view=graph-rest-beta&preserve-view=true).

---

### <a name="general-availability---custom-questions-in-access-package-request-flow-in-azure-active-directory-entitlement-management"></a>Disponibilité générale : Questions personnalisées dans le flux de demandes de package d’accès dans la gestion des droits d’utilisation Azure Active Directory

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
La gestion des droits d’utilisation Azure AD prend désormais en charge la création de questions personnalisées dans le flux de demandes de package d’accès. Cette fonctionnalité vous permet de configurer des questions personnalisées dans la stratégie de package d’accès. Ces questions sont affichées aux demandeurs qui peuvent saisir leurs réponses dans le cadre du processus de demande d’accès. Ces réponses seront affichées aux approbateurs, leur donnant des informations utiles qui leur permettront de prendre de meilleures décisions concernant la demande d’accès. [Plus d’informations](../governance/entitlement-management-access-package-create.md)

---

### <a name="general-availability---multi-geo-sharepoint-sites-as-resources-in-entitlement-management-access-packages"></a>Disponibilité générale : Sites SharePoint avec multigéographie comme ressources dans les packages d’accès de la gestion des droits d’utilisation

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Les packages d’accès dans la gestion des droits d’utilisation prennent désormais en charge les sites SharePoint avec multigéographie pour les clients qui utilisent les capacités de multigéographie de SharePoint Online. [Plus d’informations](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site)
 
---

### <a name="general-availability---knowledge-admin-and-knowledge-manager-built-in-roles"></a>Disponibilité générale : Rôles intégrés Administrateur des connaissances et Manager des connaissances

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
Deux nouveaux rôles, Administrateur des connaissances et Manager des connaissances, sont désormais en disponibilité générale.

- Les utilisateurs dotés du rôle Administrateur des connaissances ont un accès complet à tous les paramètres de connaissances organisationnelles dans le centre d’administration de Microsoft 365. Ils peuvent créer et gérer du contenu, comme des rubriques et des acronymes. De plus, ces utilisateurs peuvent créer des centres de contenu, superviser l’intégrité du service et créer des demandes de service. [En savoir plus](../roles/permissions-reference.md#knowledge-administrator)
- Les utilisateurs dotés du rôle Manager des connaissances peuvent créer et gérer du contenu et sont principalement responsables de la qualité et de la structure des connaissances. Ils disposent de tous les droits sur les actions de gestion des rubriques pour confirmer une rubrique, approuver des modifications ou supprimer une rubrique. Ce rôle peut également gérer les taxonomies dans le cadre de l'outil de gestion du magasin de termes et créer des centres de contenu. [Plus d’informations](../roles/permissions-reference.md#knowledge-manager)

---

### <a name="general-availability---cloud-app-security-administrator-built-in-role"></a>Disponibilité générale : Rôle intégré Administrateur Cloud App Security

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 
 Les utilisateurs dotés de ce rôle disposent d’autorisations complètes dans Cloud App Security. Ils peuvent ajouter des administrateurs, ajouter des stratégies et des paramètres Microsoft Cloud App Security (MCAS), charger des journaux et effectuer des actions de gouvernance. [Plus d’informations](../roles/permissions-reference.md#cloud-app-security-administrator)
 
---

### <a name="general-availability---windows-update-deployment-administrator"></a>Disponibilité générale : Administrateur de déploiements Windows Update

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** RBAC  
**Fonctionnalité de produit :** Contrôle d’accès
 

 Les utilisateurs dotés de ce rôle peuvent créer et gérer tous les aspects des déploiements Windows Update par le biais du service de déploiement Windows Update pour Entreprise. Le service de déploiement permet aux utilisateurs de définir des paramètres pour savoir quand et comment les mises à jour sont déployées. De plus, les utilisateurs peuvent spécifier quelles mises à jour sont proposées aux groupes d’appareils dans leur locataire. Il permet également aux utilisateurs de surveiller la progression des mises à jour. [Plus d’informations](../roles/permissions-reference.md#windows-update-deployment-administrator)
 
---

### <a name="general-availability---multi-camera-support-for-windows-hello"></a>Disponibilité générale : Prise en charge de plusieurs caméras pour Windows Hello

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Désormais, avec la mise à jour Windows 10 21H1, Windows Hello prend en charge plusieurs caméras. La mise à jour comprend les valeurs par défaut permettant d’utiliser la caméra externe lorsque des caméras intégrées et externes sont présentes. [Plus d’informations](https://techcommunity.microsoft.com/t5/windows-it-pro-blog/it-tools-to-support-windows-10-version-21h1/ba-p/2365103)

---
 
### <a name="general-availability---access-reviews-ms-graph-apis-now-in-v10"></a>Disponibilité générale : Les API MS Graph de révision d’accès maintenant en v1.0

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Révisions d’accès  
**Fonctionnalité de produit :** Gouvernance des identités
 
Les API MS Graph de révision d’accès Azure Active Directory sont désormais en v1.0 et prennent en charge des fonctionnalités de révision d’accès entièrement configurables. [Plus d’informations](/graph/api/resources/accessreviewsv2-root?view=graph-rest-1.0&preserve-view=true)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---june-2021"></a>Nouveaux connecteurs d’approvisionnement dans la galerie d’applications Azure AD – Juin 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [askSpoke](../saas-apps/askspoke-provisioning-tutorial.md)
- [Cloud Academy - SSO](../saas-apps/cloud-academy-sso-provisioning-tutorial.md)
- [CheckProof](../saas-apps/checkproof-provisioning-tutorial.md)
- [GoLinks](../saas-apps/golinks-provisioning-tutorial.md)
- [Holmes Cloud](../saas-apps/holmes-cloud-provisioning-tutorial.md)
- [H5mag](../saas-apps/h5mag-provisioning-tutorial.md)
- [LimbleCMMS](../saas-apps/limblecmms-provisioning-tutorial.md)
- [LogMeIn](../saas-apps/logmein-provisioning-tutorial.md)
- [SECURE DELIVER](../saas-apps/secure-deliver-provisioning-tutorial.md)
- [Sigma Computing](../saas-apps/sigma-computing-provisioning-tutorial.md)
- [Smallstep SSH](../saas-apps/smallstep-ssh-provisioning-tutorial.md)
- [Tribeloo](../saas-apps/tribeloo-provisioning-tutorial.md)
- [Twingate](../saas-apps/twingate-provisioning-tutorial.md)

Pour plus d’informations, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD – Juin 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En juin 2021, nous avons ajouté les 42 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[Taksel](https://help.ubuntu.com/community/Tasksel), [IDrive360](../saas-apps/idrive360-tutorial.md), [VIDA](../saas-apps/vida-tutorial.md), [ProProfs Classroom](../saas-apps/proprofs-classroom-tutorial.md), [WAN-Sign](../saas-apps/wan-sign-tutorial.md), [Citrix Cloud SAML SSO](../saas-apps/citrix-cloud-saml-sso-tutorial.md), [Fabric](../saas-apps/fabric-tutorial.md), [DssAD](https://cloudlicensing.deepseedsolutions.com/), [RICOH Creative Collaboration RICC](https://www.ricoh-europe.com/products/software-apps/collaboration-board-software/ricc/), [Styleflow](../saas-apps/styleflow-tutorial.md), [Chaos](https://accounts.chaosgroup.com/corporate_login), [Traced Connector](https://control.traced.app/signup), [Squarespace](https://account.squarespace.com/org/azure), [MX3 Diagnostics Connector](https://mx3www.playground.dynuddns.com/signin-oidc), [Ten Spot](https://tenspot.co/api/v1/sso/azure/login/), [Finvari](../saas-apps/finvari-tutorial.md), [Mobile4ERP](https://play.google.com/store/apps/details?id=com.negevsoft.mobile4erp), [WalkMe US OpenID Connect](https://www.walkme.com/), [Neustar UltraDNS](../saas-apps/neustar-ultradns-tutorial.md), [cloudtamer.io](../saas-apps/cloudtamer-io-tutorial.md), [A Cloud Guru](../saas-apps/a-cloud-guru-tutorial.md), [PetroVue](../saas-apps/petrovue-tutorial.md), [Postman](../saas-apps/postman-tutorial.md), [ReadCube Papers](../saas-apps/readcube-papers-tutorial.md), [Peklostroj](https://app.peklostroj.cz/), [SynCloud](https://onboard.syncloud.io/), [Polymerhq.io](https://www.polymerhq.io/), [Bonos](../saas-apps/bonos-tutorial.md), [Astra Schedule](../saas-apps/astra-schedule-tutorial.md), [Draup](../saas-apps/draup-inc-tutorial.md), [Inc](../saas-apps/draup-inc-tutorial.md), [Applied Mental Health](../saas-apps/applied-mental-health-tutorial.md), [iHASCO Training](../saas-apps/ihasco-training-tutorial.md), [Nexsure](../saas-apps/nexsure-tutorial.md), [XEOX](https://login.xeox.com/), [Plandisc](https://create.plandisc.com/account/logon), [foundU](../saas-apps/foundu-tutorial.md), [Standard for Success Accreditation](../saas-apps/standard-for-success-accreditation-tutorial.md), [Penji Teams](https://web.penjiapp.com/), [CheckPoint Infinity Portal](../saas-apps/checkpoint-infinity-portal-tutorial.md), [Teamgo](../saas-apps/teamgo-tutorial.md), [Hopsworks.ai](../saas-apps/hopsworks-ai-tutorial.md), [HoloMeeting 2](https://backend2.holomeeting.io/)

Pour accéder à la documentation de ces applications, cliquez https://aka.ms/AppsTutorial

Pour référencer votre application dans la Galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest
 
---

### <a name="device-code-flow-now-includes-an-app-verification-prompt"></a>Le flux de code d’appareil comprend désormais une invite de vérification d’application

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le [flux de code d’appareil](../develop/v2-oauth2-device-code.md) a été mis à jour pour inclure une invite supplémentaire pour l’utilisateur. Lors de la connexion, l’utilisateur verra une invite lui demandant de valider l’application à laquelle il se connecte.  Cette invite permet de s’assurer qu’il n’est pas victime d’une attaque de hameçonnage. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)
 
---

### <a name="user-last-sign-in-date-and-time-is-now-available-on-azure-portal"></a>La date et l’heure de la dernière connexion de l’utilisateur sont désormais disponibles sur Portail Azure

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** User Management
 
Vous pouvez désormais afficher la date et l’heure de la dernière connexion de vos utilisateurs sur le portail Azure. Ces informations sont disponibles pour chaque utilisateur sur la page de son profil. Ces informations vous aident à identifier les utilisateurs inactifs et à gérer efficacement les événements à risque. [Plus d’informations](./active-directory-users-profile-azure-portal.md?context=%2fazure%2factive-directory%2fenterprise-users%2fcontext%2fugr-context)
 
---

### <a name="mim-bhold-suite-impact-of-end-of-support-for-microsoft-silverlight"></a>Impact de la fin du support de Microsoft Silverlight sur la suite MIM BHOLD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité de produit :** Gouvernance des identités
 
Microsoft Silverlight atteindra la fin de son support le 12 octobre 2021. Ce changement n’a d’impact que sur les clients qui utilisent la suite Microsoft BHOLD et n’en a aucun sur les autres scénarios de Microsoft Identity Manager. Pour plus d’informations, consultez [Fin du support de Silverlight](https://support.microsoft.com/windows/silverlight-end-of-support-0a3be3c7-bead-e203-2dfd-74f0a64f1788).  

Les utilisateurs qui n’ont pas installé Microsoft Silverlight dans leur navigateur ne peuvent pas utiliser les modules de la suite BHOLD qui nécessitent Silverlight. Cela inclut le générateur de modèles BHOLD, l’intégration en libre-service FIM BHOLD et l’analyse BHOLD.  Les clients ayant un déploiement BHOLD existant d’un ou plusieurs de ces modules doivent planifier la désinstallation de ces modules de leurs ordinateurs serveurs BHOLD d’ici octobre 2021. Ils doivent également planifier la désinstallation de Silverlight de tous les ordinateurs des utilisateurs qui interagissaient auparavant avec ce déploiement BHOLD.
 
---

### <a name="my-experiences-end-of-support-for-internet-explorer-11"></a>Expériences My* : Fin du support d’Internet Explorer 11

**Type :** Déprécié  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 

Microsoft 365 et d’autres applications cesseront de prendre en charge Internet Explorer 11 le 21 août 2021, y compris les expériences My*. Les My* accessibles via Internet Explorer ne recevront pas de correctifs de bogues ni de mises à jour, ce qui peut entraîner des problèmes. Ces dates sont déterminées par l’équipe Edge et sont sujettes à modification. [Plus d’informations](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/)
 
---

### <a name="planned-deprecation---malware-linked-ip-address-detection-in-identity-protection"></a>Dépréciation planifiée : Détection Adresse IP liée à un programme malveillant dans Identity Protection

**Type :** Déprécié  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
À partir du 1er octobre 2021, Azure AD Identity Protection ne générera plus la détection « Adresse IP liée à un programme malveillant ». Aucune action n’est requise et les clients resteront protégés par les autres détections fournies par Identity Protection. Pour en savoir plus sur les stratégies de protection, consultez [Stratégies d’Identity Protection](../identity-protection/concept-identity-protection-policies.md).
 
---
 
## <a name="may-2021"></a>Mai 2021

### <a name="public-preview----azure-ad-verifiable-credentials"></a>Préversion publique : Justificatifs vérifiables Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Autres  
**Fonctionnalité de produit :** Authentification utilisateur
 
Les clients d’Azure AD peuvent désormais concevoir et émettre facilement des justificatifs vérifiables. Les justificatifs vérifiables peuvent être utilisés comme preuve d’emploi, d’éducation ou de toute autre affirmation tout en respectant la confidentialité. Validez numériquement tout élément d’information relatif aux personnes et activités. [Plus d’informations](../verifiable-credentials/index.yml)

---

### <a name="public-preview----device-code-flow-now-includes-an-app-verification-prompt"></a>Préversion publique : Le flux de code d’appareil comprend désormais une invite de vérification d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** authentification utilisateur  
**Fonctionnalité produit :** authentifications (connexions)
 
Pour améliorer la sécurité, le [flux de code d’appareil](../develop/v2-oauth2-device-code.md) a été mis à jour afin d’ajouter une autre invite destinée à confirmer que l’utilisateur se connecte bien à l’application prévue. Le lancement doit commencer en juin et se terminer pour le 30 juin.

Afin d’empêcher les attaques par hameçonnage au cours desquelles l’attaquant incite l’utilisateur à se connecter à une application malveillante, l’invite suivante a été ajoutée : « Essayez-vous de vous connecter à [nom d’affichage de l’application] ? » Ce message s’affiche pour tous les utilisateurs qui se connectent à l’aide du flux de code d’appareil. Pour plus de sécurité, il ne peut être ni supprimé ni contourné. [Plus d’informations](../develop/reference-breaking-changes.md#the-device-code-flow-ux-will-now-include-an-app-confirmation-prompt)

---

### <a name="public-preview----build-and-test-expressions-for-user-provisioning"></a>Préversion publique : Générer et tester des expressions pour l’attribution d’utilisateurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Gestion du cycle de vie des identités
 
Le générateur d’expressions vous permet de créer et de tester des expressions, sans avoir à attendre le cycle de synchronisation complet. [Plus d’informations](../app-provisioning/functions-for-customizing-application-data.md) 

---

### <a name="public-preview----enhanced-audit-logs-for-conditional-access-policy-changes"></a>Préversion publique : Journaux d’audit améliorés pour les modifications de stratégie d’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Comprendre les modifications apportées à vos stratégies au fil du temps constitue un aspect important de la gestion de l’accès conditionnel. Les modifications de stratégie peuvent entraîner des interruptions pour vos utilisateurs finaux et dans un tel contexte, il apparaît essentiel de conserver un journal des modifications et de permettre aux administrateurs de restaurer les versions de stratégie précédentes. 

En montrant qui a modifié la stratégie et à quel moment, les journaux d’audit contiendront désormais également la valeur des propriétés modifiées. Ce changement offre aux administrateurs une meilleure visibilité sur les affectations, les conditions ou les contrôles modifiés. Pour revenir à une version antérieure d’une stratégie, vous pouvez copier la représentation JSON de l’ancienne version et utiliser les API d’accès conditionnel pour rétablir la stratégie à son état précédent. [Plus d’informations](../conditional-access/concept-conditional-access-policies.md)

---

### <a name="public-preview----sign-in-logs-include-authentication-methods-used-during-sign-in"></a>Préversion publique : Les journaux de connexion incluent les méthodes d’authentification utilisées lors de la connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** MFA  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les administrateurs peuvent désormais voir les étapes séquentielles effectuées par les utilisateurs pour se connecter, y compris les méthodes d’authentification utilisées lors de la connexion. 

Pour accéder à ces informations, accédez aux journaux de connexion Azure AD, sélectionnez une connexion, puis accédez à l’onglet Détails de la méthode d’authentification. Ici, nous avons inclus des informations telles que la méthode utilisée, les détails qui s’y rapportent (numéro de téléphone, nom du téléphone, par exemple), la condition d’authentification satisfaite et les détails des résultats. [Plus d’informations](../reports-monitoring/concept-sign-ins.md)

---

### <a name="public-preview----pim-adds-support-for-abac-conditions-in-azure-storage-roles"></a>Préversion publique : PIM ajoute la prise en charge des conditions ABAC aux rôles Stockage Azure

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
En plus de la préversion publique du contrôle d’accès en fonction des attributs pour un rôle RBAC Azure spécifique, vous pouvez également ajouter des conditions ABAC à Privileged Identity Management pour vos affectations éligibles. [Plus d’informations](../../role-based-access-control/conditions-overview.md#conditions-and-privileged-identity-management-pim)

---

### <a name="general-availability---conditional-access-and-identity-protection-reports-in-b2c"></a>Disponibilité générale : rapports d’accès conditionnel et de protection des identités dans B2C

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

B2C prend désormais en charge l’accès conditionnel et la protection des identités pour les utilisateurs et les applications B2C (Entreprise-client). Cela permet aux clients de protéger leurs utilisateurs avec des contrôles d’accès granulaires basés sur les risques et les emplacements. Grâce à ces fonctionnalités, les clients peuvent maintenant examiner les signaux et créer une stratégie visant à offrir davantage de sécurité et d’accès à vos clients. [Plus d’informations](../../active-directory-b2c/conditional-access-identity-protection-overview.md)

---

### <a name="general-availability---kmsi-and-password-reset-now-in-next-generation-of-user-flows"></a>Disponibilité générale : KMSI et réinitialisation du mot de passe dans la nouvelle génération de flux d’utilisateurs

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C

La nouvelle génération de flux d’utilisateurs B2C prend désormais en charge la fonctionnalité [KMSI (Maintenir la connexion)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) et la réinitialisation de mot de passe. La fonctionnalité KMSI permet aux clients d’étendre la durée de vie de la session pour les utilisateurs de leurs applications web et natives à l’aide d’un cookie persistant. Cette fonctionnalité maintient la session active même lorsque l’utilisateur ferme et ouvre à nouveau le navigateur. La session est révoquée lorsque l’utilisateur se déconnecte. La réinitialisation de mot de passe permet aux utilisateurs de réinitialiser leur mot de passe à partir du lien « Mot de passe oublié ? ». Cela permet aussi à l’administrateur de forcer la réinitialisation du mot de passe expiré de l’utilisateur dans l’annuaire Azure AD B2C. [Plus d’informations](../../active-directory-b2c/add-password-reset-policy.md?pivots=b2c-user-flow)
 
---

### <a name="general-availability---new-log-analytics-workbook-application-role-assignment-activity"></a>Disponibilité générale : nouveau classeur Log Analytics pour l’activité d’attribution de rôle d’application

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Gestion de l’accès utilisateur  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Un nouveau classeur a été ajouté pour exposer les événements d’audit relatifs aux modifications d’attribution de rôle d’application. [Plus d’informations](../governance/entitlement-management-logs-and-reporting.md)

---

### <a name="general-availability---next-generation-azure-ad-b2c-user-flows"></a>Disponibilité générale : nouvelle génération de flux d’utilisateurs Azure AD B2C 

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** B2C - Gestion des identités consommateurs  
**Fonctionnalité de produit :** B2B/B2C
 
La nouvelle expérience de flux d'utilisateurs simplifiée offre la parité des fonctionnalités avec les fonctionnalités d’évaluation et est le foyer de toutes les nouvelles fonctionnalités. Les utilisateurs peuvent activer de nouvelles fonctionnalités au sein du même flux d’utilisateur, ce qui réduira la nécessité de créer plusieurs versions à chaque nouvelle mise en production de fonctionnalité. La nouvelle expérience utilisateur conviviale simplifie également la sélection et la création de flux d’utilisateurs. Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [, Créer des flux d’utilisateurs dans Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-user-flow). [Plus d’informations](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="general-availability---azure-active-directory-threat-intelligence-for-sign-in-risk"></a>Disponibilité générale : renseignement sur les menaces Azure Active Directory pour le risque de connexion

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Identity Protection  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
Cette nouvelle détection sert de méthode ad hoc pour permettre à nos équipes de sécurité de vous avertir et de protéger vos utilisateurs en augmentant leur risque de session à un niveau élevé lorsque nous observons une attaque. La détection marquera également les connexions associées comme étant risquées. Cette détection suit le renseignement sur les menaces Azure Active Directory en matière de détection de risque d’utilisateur pour fournir une couverture complète des différentes attaques observées par les équipes de sécurité Microsoft. [Plus d’informations](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)
 
---

### <a name="general-availability---conditional-access-named-locations-improvements"></a>Disponibilité générale : améliorations des emplacements nommés de l’accès conditionnel

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Accès conditionnel  
**Fonctionnalité de produit :** Protection et sécurité des identités
 
La prise en charge d’IPv6 dans les emplacements nommés est désormais mise à la disposition générale. Les mises à jour comprennent :

- Ajout de la fonctionnalité permettant de définir des plages d’adresses IPv6
- Augmentation de la limite du nombre d’emplacements nommés de 90 à 195
- Augmentation de la limite des plages d’adresses IP par emplacement nommé de 1200 à 2000
- Ajout de fonctionnalités pour rechercher et trier des emplacements nommés et filtrer par type d’emplacement et type d’approbation
- Ajout d’emplacements nommés auxquels une connexion appartenait dans les journaux de connexion
 
En outre, pour empêcher les administrateurs de définir des emplacements nommés de manière problématique, des vérifications supplémentaires ont été ajoutées afin de réduire les risques de configuration incorrecte. [Plus d’informations](../conditional-access/location-condition.md)

---

### <a name="general-availability---restricted-guest-access-permissions-in-azure-ad"></a>Disponibilité générale : Autorisations d’accès invité restreintes dans Azure AD

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** User Management  
**Fonctionnalité de produit :** Répertoire
 
Les autorisations au niveau répertoire pour les utilisateurs invités ont été mises à jour. Ces autorisations permettent aux administrateurs d’imposer des restrictions et contrôles supplémentaires à l’accès utilisateur invité externe.

Les administrateurs peuvent désormais ajouter davantage de restrictions à l’accès d’invités externes aux informations d’appartenance et de profil des utilisateurs et des groupes. En outre, les clients peuvent gérer l’accès des utilisateurs externes à grande échelle en masquant les appartenances de groupe, notamment en empêchant les utilisateurs invités de voir les appartenances des groupes dans lesquels ils se trouvent. Pour plus d’informations, consultez [Restreindre les autorisations d’accès invité dans Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
Vous pouvez désormais automatiser la création, la mise à jour et la suppression de comptes d’utilisateur pour ces applications nouvellement intégrées :

- [AuditBoard](../saas-apps/auditboard-provisioning-tutorial.md)
- [Cisco Umbrella User Management](../saas-apps/cisco-umbrella-user-management-provisioning-tutorial.md)
- [Insite LMS](../saas-apps/insite-lms-provisioning-tutorial.md)
- [kpifire](../saas-apps/kpifire-provisioning-tutorial.md)
- [UNIFI](../saas-apps/unifi-provisioning-tutorial.md)

Pour découvrir comment sécuriser plus efficacement votre organisation à l’aide de l’approvisionnement automatique de comptes utilisateur, consultez [Automatisation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2021"></a>Nouvelles applications fédérées disponibles dans la galerie d’applications Azure AD - Mai 2021

**Type :** Nouvelle fonctionnalité  
**Catégorie de service :** Applications d’entreprise  
**Fonctionnalité de produit :** Intégration tierce
 
En mai 2021, nous avons ajouté les 29 applications suivantes à notre galerie d’applications avec prise en charge de la fédération :

[InviteDesk](https://app.invitedesk.com/login), [Webrecruit ATS](https://id-test.webrecruit.co.uk/), [Workshop](../saas-apps/workshop-tutorial.md), [Gravity Sketch](https://landingpad.me/), [JustLogin](../saas-apps/justlogin-tutorial.md), [Custellence](https://custellence.com/sso/), [WEVO](https://hello.wevoconversion.com/login), [AppTec360 MDM](https://www.apptec360.com/ms/autopilot.html), [Filemail](https://www.filemail.com/login),[Ardoq](../saas-apps/ardoq-tutorial.md), [Leadfamly](../saas-apps/leadfamly-tutorial.md), [Documo](../saas-apps/documo-tutorial.md), [Autodesk SSO](../saas-apps/autodesk-sso-tutorial.md), [Check Point Harmony Connect](../saas-apps/check-point-harmony-connect-tutorial.md), [BrightHire](https://app.brighthire.ai/), [Rescana](../saas-apps/rescana-tutorial.md), [Bluewhale](https://cloud.bluewhale.dk/), [AlacrityLaw](../saas-apps/alacritylaw-tutorial.md), [Equisolve](../saas-apps/equisolve-tutorial.md), [Zip](../saas-apps/zip-tutorial.md), [Cognician](../saas-apps/cognician-tutorial.md), [Acra](https://www.acrasuite.com/), [VaultMe](https://app.vaultme.com/#/signIn), [TAP App Security](../saas-apps/tap-app-security-tutorial.md), [Cavelo Office365 Cloud Connector](https://dashboard.prod.cavelodata.com/), [Clebex](../saas-apps/clebex-tutorial.md), [Banyan Command Center](../saas-apps/banyan-command-center-tutorial.md), [Check Point Remote Access VPN](../saas-apps/check-point-remote-access-vpn-tutorial.md), [LogMeIn](../saas-apps/logmein-tutorial.md)

La documentation de toutes ces applications est disponible ici https://aka.ms/AppsTutorial

Pour référencer votre application dans la galerie d’applications Azure AD, lisez les informations détaillées ici : https://aka.ms/AzureADAppRequest

---

### <a name="improved-conditional-access-messaging-for-android-and-ios"></a>Amélioration de la messagerie d’accès conditionnel pour Android et iOS

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Gestion et inscription des appareils  
**Fonctionnalité de produit :** Expériences d’utilisateur final
 

Nous avons mis à jour la formulation sur l’écran d’accès conditionnel présenté aux utilisateurs qui ne peuvent pas accéder aux ressources de l’entreprise. Ils seront bloqués jusqu’à ce qu’ils inscrivent leur appareil auprès de la gestion des périphériques mobiles. Ces améliorations s’appliquent aux plateformes Android et iOS/iPadOS. Les modifications sont les suivantes :

- « Aidez-nous à protéger votre appareil » a été remplacé par « Configurez votre appareil pour obtenir l’accès »
- « Votre connexion a réussi, mais votre administrateur exige que votre appareil soit géré par Microsoft pour accéder à cette ressource. » a été remplacé par « [Nom de l’organisation] requiert que vous sécurisiez cet appareil pour accéder à la messagerie, aux fichiers et aux données de [Nom de l’organisation]. » 
- « Inscrivez-vous maintenant » a été remplacé par « Continuer »

Les informations présentes dans [Inscrivez votre appareil d’entreprise Android](https://support.microsoft.com/topic/enroll-your-android-enterprise-device-d661c82d-fa28-5dfd-b711-6dff41ae83bb) sont obsolètes.

---

### <a name="azure-information-protection-service-will-begin-asking-for-consent"></a>Le service Azure Information Protection commence par demander le consentement.

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Authentifications (connexions)  
**Fonctionnalité de produit :** Authentification utilisateur
 
Le service Azure Information Protection connecte les utilisateurs au locataire qui a chiffré le document afin de permettre l’accès au document.  À compter de juin, Azure AD invitera l’utilisateur à donner son consentement lorsque cet accès est accordé à plusieurs organisations.  Cela permet à l’utilisateur de comprendre que l’organisation propriétaire du document collectera des informations le concernant dans le cadre de l’accès au document. [Plus d’informations](/azure/information-protection/known-issues#sharing-external-doc-types-across-tenants)
 
---

### <a name="provisioning-logs-schema-change-impacting-graph-api-and-azure-monitor-integration"></a>Modification du schéma des journaux d’approvisionnement impactant l’intégration de l’API Graph et d’Azure Monitor

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Provisionnement d’applications  
**Fonctionnalité de produit :** Monitoring et création de rapports
 

Les attributs « Action » et « statusInfo » seront remplacés par « provisioningAction » et « provisoiningStatusInfo ». Mettez à jour tous les scripts que vous avez créés à l’aide de l’[API Graph de journaux d’approvisionnement](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) ou des [intégrations Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). 
 

---

### <a name="new-arm-api-to-manage-pim-for-azure-resources-and-azure-ad-roles"></a>Nouvelle API ARM pour gérer PIM pour les ressources Azure et les rôles Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Privileged Identity Management  
**Fonctionnalité de produit :** Privileged Identity Management
 
Une version mise à jour de l’API PIM pour le rôle Ressource Azure et le rôle Azure AD a été publiée. L’API PIM pour le rôle Ressource Azure est maintenant publiée dans le cadre de l’API ARM, qui s’aligne sur l’API de gestion des rôles pour l’attribution de rôle Azure standard. L’API PIM pour les rôles Azure AD est également publiée sous l’API Graph, alignée avec les API unifiedRoleManagement. Voici quelques-uns des avantages de ce changement :

- Alignement de l’API PIM avec des objets dans ARM et Graph pour la gestion des rôles, réduisant ainsi la nécessité d’appeler PIM pour intégrer de nouvelles ressources Azure. 
- Toutes les ressources Azure fonctionnent automatiquement avec la nouvelle API PIM.
- Réduction de la nécessité d’appeler PIM pour définir un rôle ou conserver un ID de ressource PIM
- Prise en charge des autorisations d’API d’application uniquement dans PIM pour les rôles Azure AD et Ressource Azure

Une version précédente de l’API PIM sous `/privilegedaccess` continuera de fonctionner, mais nous vous recommandons d’opter dès à présent pour cette nouvelle API. [Plus d’informations](../privileged-identity-management/pim-apis.md)
 
---

### <a name="revision-of-roles-in-azure-ad-entitlement-management"></a>Révision des rôles dans la gestion des droits d’utilisation Azure AD

**Type :** Fonctionnalité modifiée  
**Catégorie de service :** Rôles  
**Fonctionnalité de produit :** Gestion des droits d’utilisation
 
Un nouveau rôle, Administrateur de gouvernance des identités, a récemment été introduit. Ce rôle remplacement le rôle Administrateur d’utilisateurs pour la gestion des catalogues et des packages d’accès dans la gestion des droits d’utilisation Azure AD. Si vous avez attribué aux administrateurs le rôle Administrateur d’utilisateurs ou si vous leur demandez d’activer ce rôle pour gérer les packages d’accès dans la gestion des droits d’utilisation Azure AD, privilégiez plutôt le rôle Administrateur de gouvernance des identités. Le rôle Administrateur d’utilisateurs ne fournira plus de droits d’administration relatifs aux catalogues ou packages d’accès. [Plus d’informations](../governance/identity-governance-overview.md#appendix---least-privileged-roles-for-managing-in-identity-governance-features)

---

