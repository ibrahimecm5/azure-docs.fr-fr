---
title: inscrire une application cliente publique dans Azure Active Directory-api de santé Azure pour DICOM
description: Cet article explique comment inscrire une application publique dans le Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814596"
---
# <a name="register-a-public-client-application"></a>Inscrire une application cliente publique

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à inscrire une application publique dans le Azure Active Directory (Azure AD).

les inscriptions d’application cliente sont Azure AD représentations des applications qui peuvent s’authentifier et demander des autorisations d’API pour le compte d’un utilisateur. Les clients publics sont des applications telles que des applications mobiles et des applications JavaScript monopages qui ne peuvent pas garder les secrets confidentiels. La procédure est similaire à l' [inscription d’une application cliente confidentielle](dicom-register-confidential-client-application.md), mais comme les clients publics ne peuvent pas être approuvés pour contenir un secret d’application, il n’est pas nécessaire d’en ajouter une.

ce guide de démarrage rapide fournit des informations générales sur l’inscription d’une [application auprès du Plateforme d’identités Microsoft](.././../active-directory/develop/quickstart-register-app.md).

## <a name="application-registrations-in-the-azure-portal"></a>Via les inscriptions d’application dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. dans le panneau **Azure Active Directory** , sélectionnez **inscriptions d’applications**.
3. Sélectionnez **Nouvelle inscription**.

   [![L’inscription de l’application DICOM dans portail Azure. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>Vue d’ensemble de l’inscription d’application

1. Fournissez à l’application de service DICOM un nom d’affichage accessible à l’utilisateur.

   [![Inscrire une application dans Azure. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. Pour les **types de comptes pris en charge**, sélectionnez qui peut utiliser l’application ou accéder à l’API.
3. (**Facultatif**) Fournissez un **URI de redirection**. Ces détails peuvent être modifiés ultérieurement, mais si vous connaissez l’URL de réponse de votre application, entrez-la.
4. Sélectionnez **Inscription**.

## <a name="configuring-an-application-as-a-public-application"></a>Configuration d’une application en tant qu’application publique

Pour configurer votre application de [Bureau](.././../active-directory/develop/scenario-desktop-app-registration.md), [mobile](.././../active-directory/develop/scenario-mobile-app-registration.md) ou à [page unique](.././../active-directory/develop/scenario-mobile-app-registration.md) en tant qu’application publique, reportez-vous aux étapes ci-dessous.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le panneau **inscriptions d’applications** , puis sélectionnez le panneau **authentification** .

   [![Inscription d’applications publiques Azure. ](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. Dans les **Paramètres avancés**, sélectionnez **Oui**.

   Pour une application monopage, sélectionnez **Jetons d’accès** et **Jetons d’ID** afin d’activer le flux implicite.
   * Si votre application connecte des utilisateurs, sélectionnez **Jetons d’ID**.
   * Si votre application doit également appeler une API web protégée, sélectionnez **Jetons d’accès**.

3. Sélectionnez **Enregistrer**.

## <a name="api-permissions"></a>Autorisations des API

À l’instar de l' [application cliente confidentielle](dicom-register-confidential-client-application.md), vous devez sélectionner les autorisations d’API que cette application doit demander au nom des utilisateurs.

Si vous utilisez le service DICOM, vous devez ajouter une autorisation à l’API Azure pour DICOM en recherchant API Azure Healthcare sous API utilisées par mon organisation. Vous ne pouvez le trouver que si vous avez déjà inscrit le fournisseur de ressources Microsoft. HealthcareAPIs.

[![Mes autorisations d’API. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

Sélectionnez les autorisations que vous souhaitez que l’application de service DICOM demande.

[![Demandez les autorisations de l’API. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente publique dans le Azure Active Directory.  

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)










