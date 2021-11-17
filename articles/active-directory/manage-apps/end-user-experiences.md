---
title: Expériences d’utilisateurs finaux auprès des applications
titleSuffix: Azure AD
description: Azure Active Directory (Azure AD) offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: c21b1807468e685f61fe65178d51c549ae7b8dd1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548422"
---
# <a name="end-user-experiences-for-applications"></a>Expériences d’utilisateurs finaux auprès des applications

Azure Active Directory (Azure AD) offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation :

* Mes applications Azure AD
* Lanceur d’applications Microsoft 365
* Authentification directe pour les applications fédérées
* Liens ciblés vers des applications fédérées, avec mot de passe ou des applications existantes

Les méthodes que vous choisissez de déployer dans votre organisation sont à votre entière discrétion.

## <a name="azure-ad-my-apps"></a>Mes applications Azure AD

Mes applications à l’adresse <https://myapps.microsoft.com> est un portail web qui permet à un utilisateur final disposant d’un compte professionnel dans Azure Active Directory de voir et de lancer les applications auxquelles l’administrateur Azure AD lui a donné accès. Si vous êtes un utilisateur final avec [Azure Active Directory Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing), vous pouvez également utiliser les capacités de gestion de groupes en libre-service via Mes applications.

Par défaut, toutes les applications sont répertoriées sur une seule page. Toutefois, vous pouvez utiliser des collections pour regrouper des applications associées, puis les présenter sous un onglet distinct, ce qui les rend plus faciles à trouver. Par exemple, vous pouvez utiliser des collections pour créer des regroupements logiques d’applications pour des rôles de travail, des tâches, des projets, et autres ressources spécifiques. Pour plus d’informations, consultez [Créer des collections sur le portail Mes applications](access-panel-collections.md).

Mes applications est distinct du portail Azure. Pour y accéder, les utilisateurs n’ont pas besoin d’un abonnement Azure ou Microsoft 365.

Pour plus d’informations sur Azure AD Mes applications, consultez l’[introduction à Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="microsoft-365-application-launcher"></a>Lanceur d’applications Microsoft 365

Pour les organisations ayant déployé Microsoft 365, les applications affectées aux utilisateurs par le biais d’Azure AD apparaissent également dans le portail Office 365, à l’adresse [https://portal.office.com/myapps](https://portal.office.com/myapps). Pour les utilisateurs d’une organisation, il est donc simple de lancer les applications, sans avoir à utiliser un deuxième portail. Il s’agit de la solution de lancement d’application recommandée pour les organisations qui utilisent Microsoft 365.

Pour plus d’informations sur le lanceur d’applications Office 365, consultez [Faire apparaître votre application dans le lanceur d’applications Office 365](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Authentification directe pour les applications fédérées

La plupart des applications fédérées qui prennent en charge la connexion SAML 2.0, WS-Federation ou OpenID prennent également en charge la capacité des utilisateurs à démarrer l’application, puis à obtenir la connexion via Azure AD, soit par une redirection automatique, soit en cliquant sur un lien. Cette capacité est appelée « authentification initiée par un fournisseur de service », et la plupart des applications fédérées de la galerie d’applications Azure AD la prennent en charge (consultez la documentation mentionnée dans l’Assistant de configuration de l’authentification unique de l’application dans le portail Azure pour plus d’informations).

## <a name="direct-sign-on-links"></a>Liens d’authentification directe

Azure AD prend également en charge les liens d’authentification unique directs vers les applications qui prennent en charge l’authentification unique par mot de passe, l’authentification unique liée et l’authentification unique fédérée.

Ces liens sont des URL spécifiquement conçues qui font passer l’utilisateur par le processus de connexion Azure AD pour une application spécifique sans qu’il ait à la lancer à partir d’Azure AD Mes applications ou de Microsoft 365. Ces **URL d’accès utilisateur** se trouvent sous les propriétés des applications d’entreprise disponibles. Sur le portail Azure, sélectionnez **Azure Active Directory** > **Applications d’entreprise**. Sélectionnez l’application, puis sélectionnez **Propriétés**.

![Exemple d’URL d’accès utilisateur dans les propriétés Twitter](media/end-user-experiences/direct-sign-on-link.png)

Ces liens peuvent être copiés et collés partout où vous souhaitez fournir un lien de connexion à l’application sélectionnée. Cela peut être dans un message électronique ou dans n’importe quel portail web personnalisé que vous avez configuré pour l’accès. Voici un exemple d’URL d’authentification unique Azure AD pour Twitter :

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Comme pour les URL propres à l’organisation pour Mes applications, vous pouvez personnaliser cette URL en ajoutant un des domaines actifs ou vérifiés de votre répertoire après le domaine *myapps.microsoft.com*. Cela garantit que le logo est immédiatement chargé sur la page de connexion sans que l’utilisateur n’ait à entrer d’abord son ID utilisateur :

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Lorsqu’un utilisateur autorisé clique sur un de ces liens spécifiques de l’application, il voit tout d’abord la page de connexion de son organisation (en supposant qu’il n’est pas déjà connecté). Après la connexion, il est redirigé vers l’application sans s’arrêter au préalable sur Mes applications. Si l’utilisateur ne dispose pas des éléments requis pour accéder à l’application, par exemple l’extension de navigateur d’authentification unique basée sur mot de passe, le lien l’invite à installer l’extension manquante. L’URL du lien reste constante en cas de modification de la configuration de l’authentification unique de l’application.

Ces liens utilisent les mêmes mécanismes de contrôle d’accès que Mes applications et Microsoft 365 ; seuls les utilisateurs et les groupes qui ont été affectés à l’application dans le portail Azure seront en mesure de s’authentifier. Toutefois, tout utilisateur qui n’est pas autorisé voit un message qui explique qu’il n’a pas reçu l’accès. Lui est présenté un lien permettant de charger Mes applications pour afficher les applications disponibles auxquelles il a accès.

## <a name="next-steps"></a>Étapes suivantes

* [Série de guides de démarrage rapide sur la gestion des applications](view-applications-portal.md)
* [Qu’est-ce que l’authentification unique ?](what-is-single-sign-on.md)
* [Guide de prise en main de l’intégration d’Azure Active Directory avec les applications](plan-an-application-integration.md)