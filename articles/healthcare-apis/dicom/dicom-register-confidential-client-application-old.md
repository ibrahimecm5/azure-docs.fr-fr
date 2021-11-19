---
title: inscrire une application cliente confidentielle dans Azure Active Directory-api de santé Azure pour DICOM
description: Cet article explique comment inscrire une application cliente confidentielle dans Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814607"
---
# <a name="register-a-confidential-client-application"></a>Inscrire une application cliente confidentielle

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans ce didacticiel, vous allez apprendre à inscrire une application cliente confidentielle dans Azure Active Directory (Azure AD).

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

l’inscription d’une application cliente est une représentation Azure ADe d’une application qui peut être utilisée pour s’authentifier au nom d’un utilisateur et demander l’accès aux applications de ressources. Une application cliente confidentielle est une application qui peut être approuvée pour garder un secret et présenter ce secret lors de la demande de jetons d’accès. Ces exemples d’applications confidentielles sont des applications côté serveur.

Pour inscrire une nouvelle application cliente confidentielle, reportez-vous aux étapes ci-dessous.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. Sélectionnez le panneau **inscriptions d’applications** .
3. Sélectionnez **Nouvelle inscription**.

   [![Inscriptions de Azure App. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Entrez un nom d’affichage accessible à l’utilisateur pour l’application.

   [![Inscrire une application dans Azure. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. Pour les **types de comptes pris en charge**, sélectionnez qui peut utiliser l’application ou accéder à l’API.
6. (**Facultatif**) Fournissez un **URI de redirection**. Ces détails peuvent être modifiés ultérieurement, mais si vous connaissez l’URL de réponse de votre application, entrez-la.
7. Sélectionnez **Inscription**.

## <a name="api-permissions"></a>Autorisations des API

Maintenant que vous avez inscrit votre application, vous devez sélectionner les autorisations d’API que cette application doit demander au nom des utilisateurs.

1. Sélectionnez le panneau **Autorisations d’API**.

   [![Ajouter des autorisations ](media/dicom-add-api-permissions.png) d’API ](media/dicom-add-api-permissions.png#lightbox)

2. Sélectionnez **Ajouter une autorisation**.

   Si vous utilisez les API de santé Azure, vous devez ajouter une autorisation au service DICOM en recherchant **API Azure pour DICOM** sous API utilisées par **mon organisation** . 

   [![Rechercher des autorisations ](media/dicom-search-apis-permissions.png) d’API ](media/dicom-search-apis-permissions.png#lightbox)

   Le résultat de la recherche pour l’API Azure pour DICOM ne retournera que si vous avez déjà déployé le service DICOM dans l’espace de travail.

   Si vous référencez une autre application de ressource, sélectionnez l’inscription de votre application de ressource d’API DICOM que vous avez créée précédemment sous **API mon organisation**.

3. Sélectionnez les étendues (autorisations) que l’application cliente confidentielle demandera au nom d’un utilisateur. Sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

   [![Sélectionnez étendues des autorisations. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>Secret de l’application

1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.

   [![Certificats et secrets. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Entrez une **Description** pour votre clé secrète client. Sélectionnez le menu déroulant **expirations** pour choisir un délai d’expiration, puis cliquez sur **Ajouter**.

   [![Description de la clé secrète du client. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Une fois la chaîne secrète client créée, copiez sa **valeur** et son **ID**, puis stockez-les dans un emplacement sécurisé de votre choix.

   [![ID de la valeur de clé secrète client. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > La chaîne secrète du client n’est visible qu’une seule fois dans la Portail Azure. Quand vous quittez la page de certificats & secrets, puis que vous y revenez, la chaîne de valeur devient masquée. Il est important de faire une copie de votre chaîne secrète client immédiatement après sa génération. Si vous n’avez pas de copie de sauvegarde de votre clé secrète client, vous devez répéter les étapes ci-dessus pour la régénérer.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous vous êtes guidé à travers les étapes de l’inscription d’une application cliente confidentielle dans le Azure AD. Vous avez également guidé les étapes de la procédure d’ajout d’autorisations d’API à l’API Azure pour DICOM. Enfin, vous avez vu comment créer un secret d’application. 

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)



