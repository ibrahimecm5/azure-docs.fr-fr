---
title: Scénario - Utilisation d’Azure Active Directory pour sécuriser l’accès aux plateformes et applications SAP
description: Guide destiné aux architectes et aux administrateurs informatiques sur la sécurisation de l’accès aux plateformes et applications SAP
services: active-directory
author: xstof
manager: alberts
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: christoc
ms.reviewer: ''
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 2c987a951cbaf3795757ab0b57e8dcea0eb47aee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063996"
---
# <a name="scenario---using-azure-active-directory-to-secure-access-to-sap-platforms-and-applications"></a>Scénario - Utilisation d’Azure Active Directory pour sécuriser l’accès aux plateformes et applications SAP

Ce document fournit des conseils sur la conception technique et la configuration des plateformes et applications SAP lors de l’utilisation d’Azure Active Directory comme principal service d’authentification des utilisateurs.

## <a name="terminology-used-in-this-guide"></a>Terminologie utilisée dans ce guide

| Abréviation                                                                                                          | Description                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [BTP](https://www.sap.com/products/business-technology-platform.html)                                                 | SAP Business Technology Platform. L’ensemble de l’offre des technologies SAP. La plupart des technologies SAP présentées ici font partie de BTP. Les produits officiellement connus sous le nom de SAP Cloud Platform font partie de SAP BTP. |
| [IAS](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US)                                       | SAP Cloud Identity Services - Service d’authentification des identités. Service multi-locataire de fournisseur d’identité dans le cloud fourni par SAP. IAS aide les utilisateurs à s’authentifier auprès de leurs propres instances de service SAP.           |
| [IDS](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d6a8db70bdde459f92f2837349f95090.html) | SAP ID Service. Instance d’IAS utilisée par SAP pour authentifier les clients et les partenaires auprès des services PaaS et SaaS gérés par SAP.                                                                                 |
| [IPS](https://help.sap.com/viewer/f48e822d6d484fa5ade7dda78b64d9f5/Cloud/en-US/2d2685d469a54a56b886105a06ccdae6.html) | SAP Cloud Identity Services - Service de provisionnement des identités.  IPS permet de synchroniser les identités entre les différents magasins/systèmes cibles.                                                           |
| [XSUAA](https://blogs.sap.com/2019/01/07/uaa-xsuaa-platform-uaa-cfuaa-what-is-it-all-about/)                          | Extended Services for Cloud Foundry User Account and Authentication.  XSUAA est un serveur d’autorisation OAuth multi-locataire au sein de SAP BTP.                                                                   |
| [CF](https://www.cloudfoundry.org/)                                                                                   | Cloud Foundry. Cloud Foundry est l’environnement sur lequel SAP a bâti son offre multi-cloud pour BTP (AWS, Azure, GCP, Alibaba).                                                                                      |
| [Fiori](https://www.sap.com/products/fiori/develop.html)                                                              | L’expérience utilisateur web de SAP (par opposition à l’expérience de bureau).                                                                                                                            |

## <a name="overview"></a>Vue d’ensemble

La pile des technologies SAP et Microsoft inclut de nombreux services et composants qui jouent un rôle dans les scénarios d’authentification et d’autorisation des utilisateurs. Les principaux services sont répertoriés dans le diagramme ci-dessous.

![Présentation du paysage SAP](./media/scenario-azure-first-sap-identity-integration/sap-landscape-overview.png)

Comme il existe de nombreuses permutations de scénarios possibles à configurer, nous nous concentrons sur un scénario adapté à une stratégie d’identité Azure AD. Nous formulerons les hypothèses suivantes :

- Vous souhaitez gérer toutes vos identités de manière centralisée et seulement à partir d’Azure AD.
- Vous souhaitez réduire autant que possible les efforts de maintenance et automatiser l’authentification et l’accès aux applications sur Microsoft et SAP.
- Les conseils d’ordre général pour Azure AD avec IAS s’appliquent aux applications déployées sur BTP et aux applications SAP SaaS configurées dans IAS. Des recommandations spécifiques seront également fournies si elles s’appliquent à BTP (par exemple, l’utilisation des mappages de rôles avec des groupes Azure AD) et aux applications SAP SaaS (par exemple, l’utilisation du service de provisionnement d’identités pour l'autorisation basée sur les rôles).
- Nous supposons également que les utilisateurs sont déjà provisionnés dans Azure AD et dans tous les systèmes SAP dont le fonctionnement requiert le provisionnement des utilisateurs.  Quelle que soit la méthode utilisée, le provisionnement a pu se faire manuellement, localement à partir d’Active Directory via Azure AD Connect, ou par le biais de systèmes RH comme SAP SuccessFactors. Dans ce document, SuccessFactors est donc considéré comme une simple application à laquelle les utilisateurs (existants) se connecteront.  Nous ne couvrons pas le provisionnement réel des utilisateurs de SuccessFactors vers Azure AD.

Sur la base de ces hypothèses, nous nous concentrons principalement sur les produits et services présentés dans le diagramme ci-dessous. Ce sont les différents composants les plus pertinents pour l'authentification et l'autorisation dans un environnement cloud.

![Services SAP dans l’étendue](./media/scenario-azure-first-sap-identity-integration/sap-services-in-scope.png)

## <a name="recommendations"></a>Recommandations

### <a name="summary"></a>Résumé

- [1 - Utiliser l’authentification fédérée dans la plateforme SAP Business Technology et les applications SaaS SAP par le biais du service SAP Identity Authentication](#1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service)
- [2 - Utiliser Azure AD pour l’authentification et IAS/BTP pour l’autorisation](#2---use-azure-ad-for-authentication-and-iasbtp-for-authorization)
- [3 - Utiliser des groupes Azure AD pour l’autorisation par le biais de regroupements de rôles dans IAS/BTP](#3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp)
- [4 - Utiliser un seul sous-compte BTP pour les applications qui ont des exigences d’identité similaires](#4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements)
- [5 - Utiliser le locataire IAS de production pour l’ensemble de l’authentification et de l’autorisation de l’utilisateur final](#5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization)
- [6 - Définir un processus pour la substitution des certificats de signature SAML](#6---define-a-process-for-rollover-of-saml-signing-certificates)

### <a name="1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service"></a>1 - Utiliser l’authentification fédérée dans la plateforme SAP Business Technology et les applications SaaS SAP par le biais du service SAP Identity Authentication

#### <a name="context"></a>Context

Vos applications dans BTP peuvent utiliser des fournisseurs d’identité via des [configurations d’approbation](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/cb1bc8f1bd5c482e891063960d7acd78.html) pour authentifier les utilisateurs à l’aide du protocole SAML 2.0 entre BTP/XSUAA et le fournisseur d’identité.  Notez que seul SAML 2.0 est pris en charge, même si le protocole OpenID Connect est utilisé entre l'application elle-même et BTP/XSUAA (non pertinent dans ce contexte).

Dans BTP, vous pouvez choisir de configurer une configuration de confiance pour SAP ID Service (qui est la valeur par défaut), mais lorsque votre annuaire d’utilisateurs faisant autorité est Azure AD, vous pouvez configurer la **fédération** afin que les utilisateurs puissent se connecter avec leurs comptes d’Azure AD existants.

En plus de la fédération, vous pouvez également configurer le **provisionnement des utilisateurs** afin que les utilisateurs Azure AD soient provisionnés au préalable dans BTP. Toutefois, il n’existe pas de prise en charge native pour cela (uniquement pour Azure AD -> service SAP Identity Authentication) ; une solution intégrée avec prise en charge native est le service d’approvisionnement d’identité BTP. Un provisionnement initial des comptes d’utilisateurs peut être utile à des fins d’autorisation (par exemple, pour ajouter des utilisateurs à des rôles). Cependant, selon les besoins, vous pouvez également atteindre cet objectif avec des groupes Azure AD (voir ci-dessous), ce qui pourrait signifier qu’aucun provisionnement d’utilisateur n’est nécessaire.

La configuration de la relation de fédération offre plusieurs options :

- Vous pouvez choisir de fédérer vers Azure AD directement à partir de BTP/XSUAA.
- Vous pouvez choisir de fédérer avec IAS qui, à son tour, est configuré pour fédérer avec Azure AD en tant que fournisseur d’identité d’entreprise (également appelé « proxy SAML »).

Pour les applications SaaS SAP, IAS est provisionné et préconfiguré pour une intégration facile des utilisateurs finaux.  (Les exemples incluent SuccessFactors, Marketing Cloud, Cloud4Customer, Sales Cloud, entre autres). Ce scénario est moins complexe, car l'IAS est directement connecté à l'application cible et n'est pas transmis par proxy à XSUAA. En dans tous les cas, les mêmes règles s'appliquent pour cette configuration que pour Azure AD avec IAS en général.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Lorsque votre annuaire d’utilisateurs faisant autorité est Azure AD, nous recommandons de mettre en place une configuration de confiance dans BTP vers IAS.  À son tour, IAS est configuré pour fédérer avec Azure AD en tant que fournisseur d’identité d’entreprise.

![Configuration de confiance SAP](./media/scenario-azure-first-sap-identity-integration/sap-trust-configuration.png)

Pour la configuration de confiance dans BTP, nous recommandons d’activer l’option « Créer des utilisateurs cachés lors de l’ouverture de session ».  De cette façon, les utilisateurs qui n’ont pas encore été créés dans BTP obtiennent automatiquement un compte lorsqu’ils se connectent pour la première fois via IAS/Azure AD. Si ce paramètre est désactivé, seuls les utilisateurs préprovisionnés seront autorisés à se connecter.

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

Lorsque vous utilisez la fédération, vous pouvez choisir de définir la configuration de confiance au niveau du sous-compte BTP. Dans ce cas, vous devez répéter la configuration pour chaque autre sous-compte que vous utilisez. En utilisant IAS comme configuration de confiance intermédiaire, vous bénéficiez d’une configuration centralisée sur plusieurs sous-comptes et vous pouvez utiliser des fonctionnalités IAS, telles que [l’authentification basée sur les risques](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/bc52fbf3d59447bbb6aa22f80d8b6056.html) et [l'enrichissement centralisé des attributs d’assertion](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/7124201682434efb946e1046fde06afe.html). Pour préserver l'expérience de l'utilisateur, ces fonctionnalités de sécurité avancées ne doivent être appliquées qu'à un seul emplacement. Il peut s’agir d’un service IAS ou d’une Azure AD en tant que magasin d’utilisateurs faisant autorité unique (comme c’est le principe de ce document), qui est géré de manière centralisée par Azure AD [Gestion de l’accès conditionnel](../conditional-access/overview.md).

Remarque : pour IAS, chaque sous-compte est considéré comme une « application », même si une ou plusieurs applications peuvent être déployées dans ce sous-compte.  Dans IAS, chaque application de ce type peut être configurée pour la fédération avec le même fournisseur d’identité d’entreprise (Azure AD dans ce cas).

#### <a name="summary-of-implementation"></a>Résumé de l'implémentation

Dans Azure AD :

- Vous pouvez également [configurer Azure AD pour l'authentification unique transparente](../hybrid/how-to-connect-sso.md) (Seamless SSO), qui connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, leur nom d’utilisateur.

Dans Azure AD et IAS :

- Consultez la documentation pour connecter Azure AD au service IAS en mode de fédération (proxy) ([doc SAP](https://developers.sap.com/tutorials/cp-ias-azure-ad.html), [doc Microsoft](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)). Faites attention au paramètre `NameID` de votre configuration SSO dans Azure AD, car les UPN ne sont pas nécessairement des adresses e-mail.
- Configurez « l'application groupée » pour qu'elle utilise Azure AD en accédant à la page « [Authentification conditionnelle](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/0143dce88a604533ab5ab17e639fec09.html) » et en définissant le « fournisseur d'identité authentifiant par défaut » sur le fournisseur d'identité d'entreprise représentant votre répertoire Azure AD.

Dans BTP :

- Mettez en place une configuration de confiance vers IAS ([doc SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/7c6aa87459764b179aeccadccd4f91f3.html#loio7c6aa87459764b179aeccadccd4f91f3)) et assurez-vous que les options « [Disponible pour l’ouverture de session utilisateur](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/LATEST/en-US/affb201b1a36497996c2144c28683aed.html) » et « Créer des utilisateurs cachés lors de l’ouverture de session » sont toutes les deux activées.
- Vous pouvez également désactiver l'option « Disponible pour l’ouverture de session utilisateur » sur la configuration de confiance par défaut « SAP ID Service » afin que les utilisateurs s'authentifient toujours via Azure AD sans passer par un écran leur demandant de choisir leur fournisseur d'identité.

### <a name="2---use-azure-ad-for-authentication-and-iasbtp-for-authorization"></a>2 - Utiliser Azure AD pour l’authentification et IAS/BTP pour l’autorisation

#### <a name="context"></a>Context

Lorsque les paramètres BTP et IAS ont été configurés pour **l’authentification** utilisateur par le biais de la fédération pour Azure AD, il existe plusieurs options pour configurer **l’autorisation** :

- Dans Azure AD, vous pouvez affecter des utilisateurs et des groupes Azure AD à l'application d'entreprise représentant votre instance de SAP IAS dans Azure AD.
- Dans IAS, vous pouvez utiliser l’authentification basée sur le risque pour autoriser ou bloquer les ouvertures de session et empêcher ainsi l’accès à l'application dans BTP.
- Dans BTP, vous pouvez utiliser les collections de rôles pour définir les utilisateurs et les groupes qui peuvent accéder à l'application et obtenir certains rôles.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Nous vous recommandons de ne pas placer d’autorisation directement dans Azure AD elle-même et de désactiver explicitement « [affectation d’utilisateur requise](../manage-apps/assign-user-or-group-access-portal.md) » sur l’application d’entreprise dans Azure AD. Notez que pour les applications SAML, ce paramètre est activé par défaut, et vous devez donc prendre des mesures explicites pour le désactiver.

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

Lorsque l'application est fédérée par le biais d’IAS, du point de vue d'Azure AD, l'utilisateur « s'authentifie sur IAS » lors du processus de connexion.  Cela signifie qu'Azure AD ne dispose d'aucune information sur l'application BTP finale à laquelle l'utilisateur tente de se connecter. Cela implique également que l’autorisation dans Azure AD ne peut être utilisée que pour effectuer des autorisations très sommaires, par exemple pour permettre à l’utilisateur de se connecter à *n’importe quelle* application dans BTP, ou à *aucune*. Cela souligne également la stratégie SAP d'isolement des applications et les mécanismes d'authentification au niveau des sous-comptes BTP.

Même s’il s’agit d’une raison valable pour utiliser « Affectation de l'utilisateur obligatoire », cela signifie qu’il existe peut-être maintenant deux emplacements différents où les informations d’autorisation doivent être conservées : dans Azure AD sur l’application d’entreprise (s’applique à *toutes les* applications BTP), ainsi que dans chaque sous-compte BTP. Cela peut entraîner une certaine confusion et générer des erreurs de configuration lorsque les paramètres d'autorisation sont mis à jour dans un emplacement, mais pas dans l’autre. Par exemple : un utilisateur a été autorisé dans BTP mais n'a pas été affecté à l'application dans Azure AD, ce qui a entraîné un échec d'authentification.

#### <a name="summary-of-implementation"></a>Résumé de l'implémentation

Dans l’application d'entreprise Azure AD représentant la relation de fédération avec IAS, désactivez « [Affectation de l'utilisateur obligatoire](../manage-apps/assign-user-or-group-access-portal.md) ». Cela signifie également que vous pouvez ignorer [l’affectation d’utilisateurs en toute sécurité, comme indiqué dans Microsoft Docs](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md#assign-the-azure-ad-test-user).

### <a name="3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp"></a>3 - Utiliser des groupes Azure AD pour l’autorisation par le biais de regroupements de rôles dans IAS/BTP

#### <a name="context"></a>Context

Lorsque vous souhaitez configurer l'autorisation pour vos applications BTP, vous disposez de plusieurs options :

- Vous pouvez configurer un contrôle d'accès affiné dans l'application elle-même, en fonction de l'utilisateur connecté.
- Vous pouvez spécifier l'accès par le biais de rôles et de collections de rôles dans BTP, en fonction d’affectations d'utilisateurs ou d’affectations de groupes.

L'implémentation finale peut utiliser une combinaison de ces deux stratégies. Mais pour l'affectation par le biais de collections de rôles, vous pouvez effectuer cette opération utilisateur par utilisateur, ou utiliser des groupes du fournisseur d'identité configuré.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Si vous souhaitez utiliser Azure AD comme source faisant autorité pour une autorisation affinée, nous vous recommandons d'utiliser des groupes Azure AD et de les affecter à des collections de rôles dans BTP. Pour accorder aux utilisateurs l'accès à certaines applications, il suffit alors de les ajouter au(x) groupe(s) Azure AD correspondant(s), sans qu'aucune autre configuration ne soit nécessaire dans IAS/BTP.

Avec cette configuration, nous recommandons d'utiliser l’ID de groupe (Object ID) du groupe Azure AD comme identifiant unique du groupe, et non le nom d’affichage (« sAMAccountName »). Cela signifie que vous devez utiliser l’ID de groupe comme assertion de « groupes » dans le jeton SAML émis par Azure AD.  En outre, l'ID du groupe est utilisé pour l'affectation à la collection de rôles dans BTP.

![Utilisation des collections de rôles dans SAP](./media/scenario-azure-first-sap-identity-integration/sap-use-role-collections.png)

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

Si vous affectez des *utilisateurs* directement aux collections de rôles dans BTP, vous ne centralisez pas les décisions d'autorisation dans Azure AD. Cela signifie également que l'utilisateur doit déjà exister dans IAS avant de pouvoir être affecté à une collection de rôles dans BTP. Et étant donné que nous recommandons la fédération plutôt que le provisionnement des utilisateurs, cela signifie que le compte fictif de l’utilisateur n’existe peut-être pas dans IAS au moment où vous voulez effectuer l’affectation de l’utilisateur. L'utilisation de groupes Azure AD et leur affectation à des collections de rôles corrigent ces problèmes.

L’affectation de groupes aux collections de rôles peut sembler contredire la recommandation précédente de ne pas utiliser Azure AD pour *l'autorisation*. Mais même dans ce cas, la décision d'autorisation est toujours prise dans BTP : elle est désormais simplement basée sur l'appartenance à un groupe gérée dans Azure AD.

Nous recommandons d’utiliser l’ID de groupe du groupe Azure AD plutôt que son nom, car l’ID de groupe est unique, immuable et ne peut jamais être réutilisé pour un autre groupe par la suite, tandis que l’utilisation du nom de groupe peut entraîner des problèmes en cas de changement de nom. Il existe par ailleurs un risque de sécurité lorsqu’un groupe est supprimé et qu'un autre est créé avec le même nom, mais avec des utilisateurs qui ne devraient pas avoir accès à l’application.

#### <a name="summary-of-implementation"></a>Résumé de l'implémentation

Dans Azure AD :

- Créez des groupes auxquels peuvent être ajoutés les utilisateurs qui ont besoin d’accéder aux applications dans BTP (par exemple, créez un groupe Azure AD pour chaque collection de rôles dans BTP).
- Sur l'application d'entreprise Azure AD représentant la relation de fédération avec IAS, configurez les attributs et revendications d'utilisateur SAML pour [ajouter une revendication de groupe pour les groupes de sécurité](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration) :
    - Définissez l’attribut Source sur « ID de groupe » et le nom sur `Groups` (orthographié exactement ainsi, avec un « G » majuscule).
    - De plus, pour limiter les charges utiles des revendications et éviter de se heurter au fait qu’Azure AD limite le nombre de revendications de groupe à 150 dans les assertions SAML, nous recommandons vivement de limiter les groupes renvoyés dans les revendications aux seuls groupes qui ont été explicitement assignés :  
        - Dans la réponse « Quels groupes associés à l’utilisateur doivent être renvoyés dans la revendication ? » avec « Groupes affectés à l'application ».  Ensuite, pour les groupes que vous voulez inclure comme revendications, affectez-les à l'application d'entreprise en utilisant la section « Utilisateurs et groupes » et en sélectionnant « Ajouter un utilisateur/groupe» .

        ![Configuration d’une revendication de groupe Azure AD](./media/scenario-azure-first-sap-identity-integration/sap-aad-group-claim-configuration.png)

Dans IAS :

- Dans la configuration du fournisseur d'identité d'entreprise, sous les options de la fédération d'identité, veillez à désactiver l’option « [Utiliser le magasin d'utilisateurs d'authentification d'identité](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/LATEST/en-US/c029bbbaefbf4350af15115396ba14e2.html) » ; sinon, les informations de groupe provenant d'Azure AD ne seront pas conservées dans le jeton SAML vers BTP et l'autorisation échouera.

Dans BTP :

- Sur les collections de rôles utilisées par les applications de ce sous-compte, [mappez les collections de rôles aux groupes d'utilisateurs](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/51acfc82c0c54db59de0a528f343902c.html) en ajoutant une configuration pour le fournisseur d'identité IAS puis en définissant le nom sur l'ID de groupe (ID d'objet) du groupe Azure AD.

### <a name="4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements"></a>4 - Utiliser un seul sous-compte BTP pour les applications qui ont des exigences d’identité similaires

#### <a name="context"></a>Context

Dans BTP, chaque sous-compte peut contenir plusieurs applications. Cependant, du point de vue d’IAS, une « application regroupée » représente un sous-compte BTP complet, et non les applications plus granulaires qui le composent. Cela signifie que tous les paramètres de confiance, l'authentification et la configuration de l’accès, ainsi que les options de personnalisation et de disposition d'IAS s’appliquent à toutes les applications de ce sous-compte. De même, toutes les configurations de confiance et les collections de rôles dans BTP s'appliquent également à toutes les applications de ce sous-compte.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Nous vous recommandons de combiner plusieurs applications dans un seul sous-compte BTP uniquement si elles ont des exigences similaires au niveau de l'identité (utilisateurs, groupes, fournisseurs d'identité, rôles, configuration de confiance, personnalisation, etc.).

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

En combinant plusieurs applications ayant des exigences très différentes en matière d'identité dans un seul sous-compte du BTP, vous risquez de vous retrouver avec une configuration non sécurisée ou pouvant être plus facilement mal configurée.  Par exemple, lorsqu’un changement de configuration d’une ressource partagée, par exemple un fournisseur d'identité, est effectué pour une seule application dans BTP, ce changement affecte toutes les applications qui dépendent de cette ressource partagée.

#### <a name="summary-of-implementation"></a>Résumé de l'implémentation

Réfléchissez bien à la manière dont vous souhaitez regrouper plusieurs applications dans différents sous-comptes de BTP. Pour plus d’informations, consultez la [documentation sur le modèle de compte SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/8ed4a705efa0431b910056c0acdbf377.html).

### <a name="5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization"></a>5 - Utiliser le locataire IAS de production pour l’ensemble de l’authentification et de l’autorisation de l’utilisateur final

#### <a name="context"></a>Context

Lorsque vous utilisez IAS, vous avez généralement un locataire de production et un locataire de développement/test. Pour différents sous-comptes ou applications dans BTP, vous pouvez choisir le fournisseur d'identité (locataire IAS) à utiliser.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Nous recommandons de toujours utiliser le locataire IAS de production pour toute interaction avec les utilisateurs finaux, même dans le contexte d'une version ou d'un environnement de développement/test de *l'application* à laquelle ils doivent se connecter.

Nous recommandons d’utiliser d’autres locataires IAS uniquement pour tester la configuration liée à l'identité, ce qui doit être réalisé de manière isolée du locataire de production.

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

Comme IAS est le composant centralisé qui a été configuré pour se fédérer avec Azure AD, il n’existe qu'un seul endroit où la configuration de la fédération et des identités doit être configurée et gérée. La duplication de ce système dans d'autres locataires IAS peut entraîner des erreurs de configuration ou des incohérences entre les environnements au niveau de l'accès des utilisateurs finaux.

### <a name="6---define-a-process-for-rollover-of-saml-signing-certificates"></a>6 - Définir un processus pour la substitution des certificats de signature SAML

#### <a name="context"></a>Context

Lors de la configuration de la fédération entre Azure AD et IAS, ainsi qu’entre IAS et BTP, des métadonnées SAML sont échangées. Elles contiennent des certificats X.509 utilisés pour le chiffrement et les signatures de chiffrement des jetons SAML envoyés entre les deux parties. Ces certificats contiennent des dates d'expiration et doivent être régulièrement mis à jour (même dans les situations d'urgence où un certificat a été compromis par exemple).

Remarque : la période de validité par défaut du certificat Azure AD initial utilisé pour signer les assertions SAML est de 3 ans (notez que le certificat est spécifique à l'application d'entreprise, contrairement aux jetons OpenID Connect et OAuth 2.0 qui sont signés par un certificat global dans Azure AD). Vous pouvez choisir de [générer un nouveau certificat avec une date d'expiration différente](../manage-apps/manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate), ou créer et importer votre propre certificat.

Lorsque les certificats expirent, ils ne peuvent plus être utilisés, et de nouveaux certificats doivent être configurés. Par conséquent, un processus doit être mis en place pour maintenir à jour la configuration des certificats au sein de la partie de confiance (qui doit valider les signatures) avec les certificats réels utilisés pour signer les jetons SAML.

Dans certains cas, la partie de confiance peut le faire automatiquement en lui fournissant un point de terminaison de métadonnées qui renvoie les dernières informations de métadonnées de manière dynamique, c'est-à-dire généralement une URL accessible au public à partir de laquelle la partie de confiance peut récupérer régulièrement les métadonnées et mettre à jour son magasin de configuration interne.

Toutefois, IAS ne permet de configurer les fournisseurs d'identité d'entreprise que par le biais d'une importation du fichier XML de métadonnées. Il ne fournit aucun point de terminaison de métadonnées pour la récupération dynamique des métadonnées Azure AD (par exemple `https://login.microsoftonline.com/my-azuread-tenant/federationmetadata/2007-06/federationmetadata.xml?appid=my-app-id`). De même, BTP ne permet pas de configurer une nouvelle configuration de confiance à partir du point de terminaison des métadonnées IAS (par exemple `https://my-ias-tenant.accounts.ondemand.com/saml2/metadata`), il nécessite également un chargement unique d'un fichier XML de métadonnées.

#### <a name="what-are-we-recommending"></a>Que recommandons-nous ?

Lors de la mise en place de la fédération d’identité entre deux systèmes (par exemple, Azure AD et IAS ainsi qu’entre IAS et BTP), veillez à noter la date d’expiration des certificats utilisés. Assurez-vous que ces certificats peuvent être remplacés bien à l'avance et qu'il existe un processus documenté pour mettre à jour les nouvelles métadonnées dans toutes les parties de confiance qui dépendent de ces certificats.

Comme nous l'avons vu précédemment, nous recommandons de mettre en place une configuration de confiance dans BTP vers IAS, qui à son tour est configuré pour se fédérer avec Azure AD en tant que fournisseur d'identité d'entreprise. Dans ce cas, les certificats suivants (utilisés pour la signature et le chiffrement de SAML) sont importants :

- Le certificat du sous-compte dans BTP : lorsque celui-ci change, la configuration SAML 2.0 de l'application dans IAS doit être mise à jour.
- Le certificat du locataire dans IAS : lorsque celui-ci change, la configuration SAML 2.0 de l'application d'entreprise dans Azure AD et la configuration de confiance dans BTP doivent être mises à jour.
- Le certificat de l'application d'entreprise dans Azure AD : lorsque celui-ci change, la configuration SAML 2.0 du fournisseur d'identité d'entreprise dans IAS doit être mise à jour.

![Substitution de certificats de signature SAML](./media/scenario-azure-first-sap-identity-integration/sap-rollover-saml-signing-certs.png)

SAP propose des exemples d'implémentations pour les notifications de certificats clients avec SAP Cloud Platform Integration [ici](https://blogs.sap.com/2017/12/06/sap-cloud-platform-integration-automated-notification-of-keystore-entries-reaching-expiry/) et [ici](https://blogs.sap.com/2019/03/01/sap-cloud-platform-integration-automated-notification-for-client-certificates-reaching-expiry/). Cette approche pourrait être adaptée avec Azure Integration Services ou PowerAutomate. Mais il faudrait adapter ces éléments pour qu’ils fonctionnent avec des certificats de serveur. Une telle approche nécessite une implémentation personnalisée.

#### <a name="why-this-recommendation"></a>Pourquoi cette recommandation ?

Si on laisse les certificats expirer, ou s’ils sont remplacés à temps, mais que les parties de confiance qui en dépendent ne sont pas mises à jour avec les nouvelles informations du certificat, les utilisateurs ne pourront plus se connecter à aucune application par le biais de la fédération. Cela peut signifier un temps d’arrêt important pour tous les utilisateurs pendant que vous restaurez le service en reconfigurant les métadonnées.

#### <a name="summary-of-implementation"></a>Résumé de l'implémentation

[Ajoutez une adresse de notification par e-mail pour l’expiration du certificat](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration) dans Azure AD et définissez-la sur une boîte aux lettres de groupe afin que la notification ne soit pas envoyée à une seule personne (qui ne possède peut-être plus de compte au moment où le certificat va expirer). Par défaut, seul l’utilisateur qui a créé l’application d'entreprise recevra une notification.

Vous pouvez mettre en place une automatisation pour exécuter l'ensemble du processus de renouvellement des certificats.  Par exemple, il est possible de consulter régulièrement les certificats qui expirent et les remplacer tout en mettant à jour toutes les parties de confiance avec les nouvelles métadonnées.
