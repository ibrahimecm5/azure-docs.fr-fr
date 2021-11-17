---
title: 'Démarrage rapide : Configurer les propriétés d’une application d’entreprise'
titleSuffix: Azure AD
description: Configurez les propriétés d’une application d’entreprise dans Azure Active Directory.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 6179330f62f1966d099cfca93fc3885895af5e40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550795"
---
# <a name="quickstart-configure-enterprise-application-properties"></a>Démarrage rapide : Configurer les propriétés d’une application d’entreprise

Dans ce guide de démarrage rapide, vous utilisez le Centre d’administration Azure Active Directory pour configurer les propriétés d’une application d’entreprise que vous avez précédemment ajoutée à votre locataire Azure Active Directory (Azure AD).

Vous pouvez configurer les attributs communs suivants d’une application d’entreprise :

- **Connexion permise pour les utilisateurs ?** - Détermine si les utilisateurs affectés à l’application peuvent se connecter.
- **Assignation requise de utilisateur ?** - Détermine si les utilisateurs non affectés à l’application peuvent se connecter.
- **Visible par les utilisateurs ?** - Détermine si les utilisateurs affectés à une application peuvent voir cette dernière dans Mes applications et le lanceur d’applications Microsoft 365. (Voir le menu Gaufre en haut à gauche d’un site web Microsoft 365.)
- **Logo** - Détermine le logo qui représente l’application.
- **Notes** - Fournit un emplacement pour ajouter des notes qui ont trait à l’application.

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour configurer les propriétés d’une application d’entreprise, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Réalisation des étapes décrites dans [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).

## <a name="configure-application-properties"></a>Configurer les propriétés de l’application

Les propriétés d’une application déterminent la façon dont l’application est représentée et comment y accéder.

Pour modifier les propriétés de l’application :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD. Recherchez et sélectionnez l’application que vous souhaitez utiliser. Par exemple, **Azure AD SAML Toolkit 1**.
1. Dans la section **Gérer**, sélectionnez **Propriétés** pour ouvrir le volet **Propriétés** à des fins de modification.
1. Sélectionnez **Oui** ou **Non** pour déterminer si l’application est activée pour que les utilisateurs s’y connectent.
1. Sélectionnez **Oui** ou **Non** pour déterminer si seuls les comptes d’utilisateur qui ont été affectés à l’application peuvent se connecter.
1. Sélectionnez **Oui** ou **Non** pour déterminer si les utilisateurs affectés à une application peuvent voir cette dernière dans les portails Mes applications et Microsoft 365. 

    :::image type="content" source="media/add-application-portal-configure/configure-properties.png" alt-text="Configurez les propriétés d’une application d’entreprise.":::

## <a name="use-a-custom-logo"></a>Utiliser un logo personnalisé

Le logo de l’application apparaît dans les portails Mes applications et Microsoft 365, et quand les administrateurs affichent cette application dans la galerie d’applications d’entreprise. Les logos personnalisés doivent avoir exactement une taille de 215x215 pixels et être au format PNG. Il est recommandé d’utiliser un arrière-plan de couleur unie et sans transparence dans le logo de votre application afin qu’il soit bien visible pour les utilisateurs.

Pour utiliser un logo personnalisé :

1. Créez un logo de 215 x 215 pixels et enregistrez-le au format .png.
1. Sélectionnez l’icône dans **Sélectionner un fichier** pour charger le logo.
1. Quand vous avez terminé, Sélectionnez **Enregistrer**.

La miniature du logo ne se met pas à jour tout de suite. Vous pouvez fermer et rouvrir le volet **Propriétés** pour voir la miniature mise à jour.

## <a name="add-notes"></a>Ajouter des notes

Vous pouvez utiliser la propriété **Notes** pour ajouter toute information relative à la gestion de l’application dans Azure AD. La propriété **Notes** est un champ de texte libre d’une taille maximale de 1 024 caractères.

Pour entrer des notes pour l’application :

1. Entrez les notes que vous souhaitez conserver avec l’application.
1. Sélectionnez **Enregistrer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre le prochain guide de démarrage rapide, gardez l’application d’entreprise que vous avez créée. Dans le cas contraire, vous pouvez la supprimer pour nettoyer votre locataire. Pour plus d’informations, consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment rechercher et afficher les applications dans votre locataire Azure AD.
> [!div class="nextstepaction"]
> [Afficher les applications](view-applications-portal.md)
