---
title: inscrire une application cliente de service dans Azure Active Directory-api de santé Azure pour DICOM
description: Cet article explique comment inscrire une application cliente de service dans Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814593"
---
# <a name="register-a-service-client-application"></a>Inscrire une application cliente de service

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à inscrire une application cliente de service dans Azure Active Directory (Azure AD).

## <a name="application-registrations-in-the-azure-portal"></a>Via les inscriptions d’application dans le portail Azure

les inscriptions d’application cliente sont des représentations Azure AD d’applications qui peuvent être utilisées pour authentifier et obtenir des jetons. Un client de service est destiné à être utilisé par une application pour obtenir un jeton d’accès sans authentification interactive d’un utilisateur. Il disposera de certaines permissions d’application et utilisera un secret d’application (mot de passe) pour l’obtention de jetons d’accès.

Reportez-vous aux étapes ci-dessous pour créer un client de service.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. dans le panneau **Azure Active Directory** , sélectionnez **inscriptions d’applications**.
3. Sélectionnez **Nouvelle inscription**.

   [![Inscriptions de Azure App. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Fournissez à l’application de service DICOM un nom d’affichage accessible à l’utilisateur. En général, les applications clientes de service n’utilisent pas d’URL de réponse.

   [![Inscrire une application dans Azure. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. Sélectionnez **Inscription**.

## <a name="api-permissions"></a>Autorisations des API

Maintenant que vous avez inscrit votre application de service DICOM, vous devez sélectionner les autorisations d’API que cette application doit demander au nom des utilisateurs.

1. Sélectionnez le panneau **Autorisations d’API**.

   [![Ajoutez des autorisations d’API. ](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. Sélectionnez **Ajouter une autorisation**.

   Si vous utilisez DICOM, vous devez ajouter une autorisation à l’API Azure pour DICOM en recherchant **API Azure Healthcare** sous **API utilisées par mon organisation**. Vous ne pouvez le trouver que si vous avez déjà inscrit le fournisseur de `Microsoft.HealthcareAPIs` ressources.

   [![Mes autorisations d’API. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [![Demandez les autorisations de l’API. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. Sélectionnez les étendues (autorisations) que l’application cliente confidentielle demandera au nom d’un utilisateur.

   [![Sélectionnez étendues des autorisations. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. Accordez les autorisations de l’application. Si vous n’avez pas les autorisations requises, contactez votre administrateur.


## <a name="application-secret"></a>Secret d’application

Le client de service a besoin d’un secret (mot de passe) pour obtenir un jeton.

1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**.

   [![Certificats et secrets. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Entrez une **Description** pour votre clé secrète client. Sélectionnez le menu déroulant **expirations** pour choisir un délai d’expiration, puis cliquez sur **Ajouter**.

   [![Description de la clé secrète du client. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Une fois la chaîne secrète client créée, copiez sa **valeur** et son **ID**, puis stockez-les dans un emplacement sécurisé de votre choix.

   [![ID de la valeur de clé secrète client. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. Sélectionnez Ajouter.

> [!NOTE]
> La chaîne secrète du client n’est visible qu’une seule fois dans la Portail Azure. Quand vous quittez la page de certificats & secrets, puis que vous y revenez, la chaîne de valeur devient masquée. Il est important de faire une copie de votre chaîne secrète client immédiatement après sa génération. Si vous n’avez pas de copie de sauvegarde de votre clé secrète client, vous devez répéter les étapes ci-dessus pour la régénérer.


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente de service dans Azure AD. 

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)




