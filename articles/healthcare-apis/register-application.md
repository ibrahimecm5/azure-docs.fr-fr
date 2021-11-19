---
title: inscrire une application cliente dans Azure Active Directory pour les api de santé Azure
description: comment inscrire une application cliente dans le Azure AD et comment ajouter une clé secrète et des autorisations d’api aux api de santé Azure
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: dcb88484144674122f0a108b92f8986084b80b9f
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132812595"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Inscrire une application cliente dans Azure Active Directory

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

dans cet article, vous allez apprendre à inscrire une application cliente dans Azure Active Directory (Azure AD) afin d’accéder aux api de santé. vous trouverez plus d’informations sur [l’inscription d’une application auprès du Plateforme d’identités Microsoft](../active-directory/develop/quickstart-register-app.md).

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
2. Sélectionnez **Inscriptions d’applications**.
[![Capture d’écran de la nouvelle fenêtre d’inscription d’application. ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. Sélectionnez **Nouvelle inscription**.
4. Pour les types de comptes pris en charge, sélectionnez **comptes dans ce répertoire d’organisation uniquement**. Laissez les autres options telles quelles.
[![Capture d’écran des nouvelles options de compte d’inscription. ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. Sélectionnez **Inscription**.

## <a name="application-id-client-id"></a>ID d’application (ID client)

Après l’inscription d’une nouvelle application, vous pouvez trouver l’ID de l’application (client) et l’ID de répertoire (locataire) à partir de l’option de menu vue d’ensemble. Prenez note des valeurs à utiliser ultérieurement.

[![Capture d’écran du panneau de présentation du client ID. ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![Capture d’écran de l’ID ](media/register-application-four.png) client ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>Paramètre d’authentification : confidentiel et public

Cliquez sur **authentification** pour passer en revue les paramètres. La valeur par défaut pour **autoriser les flux de clients publics** est « non ».

Si vous conservez cette valeur par défaut, l’inscription de l’application est une **application cliente confidentielle** et un certificat ou une clé secrète est requis.

[![Capture d’écran de l’application cliente confidentielle. ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

Si vous modifiez la valeur par défaut en « Oui » pour l’option « autoriser les flux de clients publics » dans le paramètre avancé, l’inscription de l’application est une **application cliente publique** et un certificat ou une clé secrète n’est pas requis. La valeur « Oui » est utile lorsque vous souhaitez utiliser l’application cliente dans votre application mobile ou une application JavaScript dans laquelle vous ne souhaitez pas stocker de secrets.

Pour les outils qui nécessitent une URL de redirection, sélectionnez **Ajouter une plateforme** pour configurer la plateforme.

[![Capture d’écran de l’ajout d’une plateforme. ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Pour poster, sélectionnez **applications mobiles et de bureau**. Entrez « https://www.getpostman.com/oauth2/callback » dans la section **URI de redirection personnalisés** . Sélectionnez le bouton **configurer** pour enregistrer le paramètre.

[![Capture d’écran de la configuration d’autres services. ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>Certificats & secrets

Sélectionnez **certificats & secrets** et sélectionnez **nouvelle clé secrète client**. Sélectionnez **6 mois recommandés** dans le champ **expiration** . Ce nouveau secret est valable pendant six mois. Vous pouvez également choisir des valeurs différentes, telles que :
 
* 03 mois
* 12 mois
* 24 mois
* Date de début et date de fin personnalisées.

>[!NOTE]
>Il est important que vous enregistriez la valeur du secret, et non l’ID du secret.

[![Capture d’écran des certificats et des secrets. ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

Si vous le souhaitez, vous pouvez télécharger un certificat (clé publique) et utiliser l’ID de certificat, une valeur GUID associée au certificat. À des fins de test, vous pouvez créer un certificat auto-signé à l’aide d’outils tels que la ligne de commande PowerShell, `New-SelfSignedCertificate` , puis exporter le certificat à partir du magasin de certificats.

## <a name="api-permissions"></a>Autorisations des API

Les étapes suivantes sont requises pour le service DICOM, mais facultatives pour le service FHIR. En outre, les autorisations d’accès utilisateur ou les attributions de rôles pour les API de santé sont gérées par le biais de RBAC. Pour plus d’informations, consultez [configurer Azure RBAC pour les API de santé](configure-azure-rbac.md).

1. Sélectionnez le panneau **Autorisations d’API**.

   [![Ajouter des autorisations ](dicom/media/dicom-add-api-permissions.png) d’API ](dicom/media/dicom-add-api-permissions.png#lightbox)

2. Sélectionnez **Ajouter une autorisation**.

   Si vous utilisez les API de santé Azure, vous devez ajouter une autorisation au service DICOM en recherchant **API Azure pour DICOM** sous API utilisées par **mon organisation** . 

   [![Rechercher des autorisations ](dicom/media/dicom-search-apis-permissions.png) d’API ](dicom/media/dicom-search-apis-permissions.png#lightbox)

   Le résultat de la recherche pour l’API Azure pour DICOM ne retournera que si vous avez déjà déployé le service DICOM dans l’espace de travail.

   Si vous référencez une autre application de ressource, sélectionnez l’inscription de votre application de ressource d’API DICOM que vous avez créée précédemment sous **API mon organisation**.

3. Sélectionnez les étendues (autorisations) que l’application cliente confidentielle demandera au nom d’un utilisateur. Sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

   [![Sélectionnez étendues des autorisations. ](dicom/media/dicom-select-scopes.png) ](dicom/media/dicom-select-scopes.png#lightbox)

>[!NOTE]
>Utilisez grant_type de client_credentials lors de la tentative d’otain d’un jeton d’accès pour le service FHIR à l’aide d’outils tels que poste ou client Rest. Pour plus d’informations, consultez [accès à l’aide du poste de publication](use-postman.md) et accès aux [API de santé à l’aide de l’extension Client Rest dans Visual Studio code](using-rest-client.md).
>>Utilisez grant_type de client_credentials ou authentication_doe lors de la tentative de otain d’un jeton d’accès pour le service DICOM. Pour plus d’informations, consultez [utilisation de DICOM avec l’boucle](dicom/dicomweb-standard-apis-curl.md).

L’inscription de votre application est maintenant terminée.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente dans le Azure AD. En outre, vous avez appris à ajouter une clé secrète et des autorisations d’API aux API de santé Azure. Pour plus d’informations sur les API de santé Azure, consultez

>[!div class="nextstepaction"]
>[Vue d’ensemble des API Azure Healthcare](healthcare-apis-overview.md)
