---
title: 'Démarrage rapide : Supprimer une application d’entreprise'
description: Supprimez une application d’entreprise dans Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: c2ee107815186ac7877056ac8cec4954d30de747
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547523"
---
# <a name="quickstart-delete-an-enterprise-application"></a>Démarrage rapide : Supprimer une application d’entreprise

Dans ce guide de démarrage rapide, vous utilisez le Centre d’administration Azure Active Directory pour supprimer une application qui a été ajoutée à votre locataire Azure Active Directory (Azure AD).

Il est recommandé d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour supprimer une application d’entreprise, il faut :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Réalisation des étapes décrites dans [Démarrage rapide : Ajouter une application d’entreprise](add-application-portal.md).

## <a name="delete-an-enterprise-application"></a>Supprimer une application d’entreprise

Pour supprimer une application d’entreprise :

1. Accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous avec l’un des rôles listés dans les prérequis.
1. Dans le menu de gauche, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche une liste des applications de votre locataire Azure AD. Recherchez et sélectionnez l’application que vous souhaitez supprimer. Par exemple, **Azure AD SAML Toolkit 1**.
1. Dans la section **Gérer** du volet gauche, sélectionnez **Propriétés**.
1. En haut du volet **Propriétés**, sélectionnez **Supprimer**, puis **Oui** pour confirmer que vous souhaitez supprimer l’application de votre locataire Azure AD.

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="Supprimer une application d’entreprise.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous aurez terminé cette série de guides de démarrage rapide, supprimez l’application pour nettoyer votre locataire de test. La suppression d’application est traitée dans ce guide de démarrage rapide.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la planification d’un déploiement d’authentification unique.
> [!div class="nextstepaction"]
> [Planifier un déploiement de l’authentification unique](plan-sso-deployment.md)
