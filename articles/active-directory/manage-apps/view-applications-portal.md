---
title: 'Démarrage rapide : Afficher les applications d’entreprise'
description: Affichez les applications d’entreprise qui sont inscrites pour utiliser votre locataire Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: 4da70b29fb7d5a8920db05e0d7c28f5c29a101fd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553132"
---
# <a name="quickstart-view-enterprise-applications"></a>Démarrage rapide : Afficher les applications d’entreprise

Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le Centre d’administration Azure Active Directory pour rechercher et afficher les applications d’entreprise qui sont déjà configurées dans votre locataire Azure Active Directory (Azure AD).

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour afficher les applications qui ont été inscrites dans votre locataire Azure AD, vous avez besoin de l’élément suivant :

- Un compte Azure. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Réalisation des étapes décrites dans [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).

## <a name="view-a-list-of-applications"></a>Afficher une liste des applications

Pour afficher les applications d’entreprise inscrites dans votre locataire :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD.

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="Afficher les applications inscrites dans votre locataire Azure AD.":::

1. Pour afficher davantage d’applications, sélectionnez **Charger plus** en bas de la liste. Si votre locataire contient un grande nombre d’applications, il peut être plus facile de rechercher une application particulière que de faire défiler la liste.

## <a name="search-for-an-application"></a>Rechercher une application

Pour rechercher une application particulière :

1. À partir du menu déroulant **Type d’application**, sélectionnez **Toutes les applications**, puis sélectionnez **Appliquer**.
1. Entrez le nom de l’application recherchée. Si l’application a été ajoutée à votre locataire Azure AD, elle apparaît dans les résultats de recherche. Par exemple, vous pouvez rechercher l’application **Azure AD SAML Toolkit 1** qui est utilisée dans les guides de démarrage rapide précédents. 
1. Essayez d’entrer les premières lettres du nom de l’application.

## <a name="select-viewing-options"></a>Sélectionner les options d'affichage

Sélectionnez les options en fonction de ce que vous recherchez :

1. Vous pouvez afficher les applications par **Type d’application**, **État de l’application** et **Visibilité de l’application**.
1. Sous **Type d’Application**, choisissez l’une des options suivantes :
    - **Applications d’entreprise** montre les applications non Microsoft.
    - **Applications Microsoft** montre les applications Microsoft.
    - **Toutes les Applications** montre les applications non Microsoft et Microsoft.
1. Sous **État de l’Application**, choisissez **Quelconque**, **Désactivé**, ou **Activé**. L’option **Quelconque** inclut les applications activées et désactivées.
1. Sous **Visibilité de l’Application**, choisissez **Quelconque** ou **Masqué**. L’option **Masqué** affiche les applications qui sont dans le locataire, mais ne sont pas visibles pour les utilisateurs.
1. Après avoir choisi les options souhaitées, sélectionnez **Appliquer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé une application de test nommée **Azure AD SAML Toolkit 1** qui a été utilisée dans les guides de démarrage rapide, vous pouvez la supprimer maintenant pour nettoyer votre locataire. Pour plus d’informations, consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment supprimer une application d’entreprise.
> [!div class="nextstepaction"]
> [Supprimer une application](add-application-portal.md)
