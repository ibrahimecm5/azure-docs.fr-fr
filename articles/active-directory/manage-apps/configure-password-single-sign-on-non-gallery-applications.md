---
title: Ajouter l’authentification unique basée sur un mot de passe à une application
description: Ajoutez l’authentification unique basée sur un mot de passe à une application dans le Répertoire actif Azure.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 0d54ba24ee13bc0084dbe6f4a14d54e38c94cded
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546839"
---
# <a name="add-password-based-single-sign-on-to-an-application"></a>Ajouter l’authentification unique basée sur un mot de passe à une application

Cet article explique comment configurer l’authentification unique (SSO) par mot de passe dans le Répertoire actif Azure (Azure AD). Avec l’authentification unique par mot de passe, un utilisateur se connecte à l’application avec un nom d’utilisateur et un mot de passe la première fois qu’il y accède. Après la première connexion, Azure AD fournit le nom d’utilisateur et le mot de passe à l’application. 

L’authentification unique par mot de passe utilise le processus d’authentification existant fourni par l’application. Quand vous activez l’authentification unique par mot de passe pour une application, Azure AD collecte et stocke de façon sécurisée les noms d’utilisateur et les mots de passe pour l’application. Les informations d’identification de l’utilisateur sont stockées à l’état chiffré dans l’annuaire. L’authentification unique par mot de passe est prise en charge pour toutes les applications cloud qui ont une page de connexion HTML.

Choisissez l’authentification unique basée sur un mot de passe lorsque :
- Une application ne prend pas en charge le protocole SSO SAML.
- Une application s’authentifie avec un nom d’utilisateur et un mot de passe au lieu de jetons d’accès et d’en-têtes.

La page de configuration de l’authentification unique par mot de passe est simple. Elle comprend uniquement l’URL de la page d’authentification utilisée par l’application. Il doit s’agir de la page qui contient le champ d’entrée du nom d’utilisateur.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’authentification unique par mot de passe dans votre locataire Azure AD, vous avez besoin de :
-   Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
-   Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
-   Application qui prend en charge l’authentification unique basée sur un mot de passe.

## <a name="configure-password-based-single-sign-on"></a>Configurer l’authentification unique par mot de passe

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec le rôle approprié.
1.  Sélectionnez **Répertoire actif Azure** dans les Services Azure, puis sélectionnez **Applications d’entreprise**.
1.  Recherchez et sélectionnez l’application que vous souhaitez ajouter à l’authentification unique SSO par mot de passe.
1.  Sélectionnez **Authentification unique**, puis sélectionnez **par mot de passe**.
1.  Saisissez l’URL pour la page de connexion de l’application.
1.  Sélectionnez **Enregistrer**. 

Azure AD analyse le code HTML de la page de connexion pour les champs d’entrée du nom d’utilisateur et du mot de passe. Si la tentative réussit, vous avez terminé. La prochaine étape consiste à [Affecter des utilisateurs et des groupes](add-application-portal-assign-users.md) à l’application. 

Après avoir affecté des utilisateurs et des groupes, vous pouvez fournir des informations d’identification à utiliser pour un utilisateur lorsqu’il se connecte à l’application. 

1. Sélectionnez **Utilisateurs et groupes**, cochez la case pour la ligne de l’utilisateur ou du groupe, puis sélectionnez **Mettre à jour les informations d’identification**. 
1. Entrez le nom d’utilisateur et le mot de passe à utiliser pour l’utilisateur ou le groupe. Si vous ne le faites pas, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

## <a name="manual-configuration"></a>Configuration manuelle

Si la tentative d'analyse Azure AD échoue, vous pouvez configurer l’authentification manuellement.

1. Sélectionnez **Configurer les Paramètres de l’authentification unique par mot de passe de {nom de l’application}** pour afficher la page **Configurer l’authentification**.
1. Sélectionnez **Détecter manuellement les champs de connexion**. D’autres instructions qui décrivent la détection manuelle des champs de connexion s’affichent.
1. Sélectionnez **Capturer les champs de connexion**. Une page d’état de la capture s’ouvre dans un nouvel onglet affichant le message Capture des métadonnées en cours.
1. Si la mention **Extension Mes applications requise** s’affiche dans un nouvel onglet, sélectionnez **Installer maintenant** pour installer l’extension de navigateur Extension de connexion sécurisée à Mes applications. (Cette extension de navigateur est compatible avec Microsoft Edge ou Chrome.) Ensuite, installez, lancez et activez l’extension, puis actualisez la page d’état de la capture. L’extension de navigateur ouvre ensuite un nouvel onglet qui affiche l’URL entrée.
1. Dans l’onglet contenant l’URL saisie, suivez le processus de connexion. Renseignez les champs de nom d’utilisateur et de mot de passe et essayez de vous connecter. (Vous n’avez pas besoin de fournir le mot de passe correct.) Une invite vous demande d’enregistrer les champs de connexion capturés.
1. Sélectionnez **OK**. L’extension de navigateur met à jour la page d’état de la capture avec le message **Les métadonnées ont été mises à jour pour l’application**. L'onglet du navigateur se ferme.
1. Dans la page Azure AD Configurer l’authentification, sélectionnez **J’ai pu me connecter à l’application**.
1. Sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer l’accès aux applications](what-is-access-management.md)
