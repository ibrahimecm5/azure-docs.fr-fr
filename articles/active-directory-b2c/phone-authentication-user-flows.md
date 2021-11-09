---
title: Configurer le fournisseur d’identité d’un compte local
titleSuffix: Azure AD B2C
description: Définissez les types d’identité que vous pouvez utiliser (e-mail, nom d’utilisateur, numéro de téléphone) pour l’authentification d’un compte local lorsque vous configurez des flux d’utilisateurs dans votre locataire Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 5c63cb4f4e2ce41fa488e49201ba90bfc4b16222
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012830"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows"></a>Configurer l’inscription et la connexion par téléphone pour les flux d’utilisateurs

En plus de l’e-mail et du nom d’utilisateur, vous pouvez activer le numéro de téléphone comme option d’inscription pour l’ensemble du locataire en ajoutant l’inscription et la connexion par téléphone au fournisseur d’identité de votre compte local. Après avoir activé l’inscription et la connexion par téléphone pour les comptes locaux, vous pouvez ajouter l’inscription par téléphone à vos flux d’utilisateurs.

La configuration de l’inscription et de la connexion par téléphone dans un flux d’utilisateur implique les étapes suivantes :

- [Configurez l’inscription et la connexion par téléphone pour l’ensemble du locataire](#configure-phone-sign-up-and-sign-in-tenant-wide) dans le fournisseur d’identité de votre compte local pour accepter un numéro de téléphone comme identité d’utilisateur. 

- [Ajoutez l’inscription par téléphone à votre flux d’utilisateur](#add-phone-sign-up-to-a-user-flow) pour permettre aux utilisateurs de s’inscrire à votre application à l’aide de leur numéro de téléphone.

- [Activez l’invite de récupération par e-mail (préversion)](#enable-the-recovery-email-prompt-preview) pour permettre aux utilisateurs de spécifier une adresse e-mail qui peut être utilisée pour récupérer leur compte lorsqu’ils n’ont pas leur téléphone.

- [Affichez les informations relatives au consentement](#enable-consent-information) à l’utilisateur pendant le processus d’inscription ou de connexion. Vous pouvez afficher les informations par défaut relatives au consentement ou vous pouvez les personnaliser.

L’authentification multifacteur (MFA) est désactivée par défaut lorsque vous configurez un flux d’utilisateur avec l’inscription par téléphone. Vous pouvez activer la MFA dans les flux d’utilisateurs avec l’inscription par téléphone, mais, comme un numéro de téléphone est utilisé comme identificateur principal, l’envoi par e-mail d’un code secret à usage unique est la seule option disponible pour le deuxième facteur d’authentification.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Configurer l’inscription et la connexion par téléphone pour l’ensemble du locataire

L’inscription par e-mail est activée par défaut dans les paramètres du fournisseur d’identité de votre compte local. Vous pouvez modifier les types d’identité que vous prendrez en charge dans votre locataire en sélectionnant ou en désélectionnant l’inscription par e-mail, le nom d’utilisateur ou le numéro de téléphone.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Fournisseurs d’identité**.
1. Dans la liste des fournisseurs d’identité, sélectionnez **Compte local**.

   ![Fournisseurs d’identité – sélectionner Compte local](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Dans la page **Configurer un IdP local**, vérifiez que **Téléphone** est sélectionné comme l’un des types d’identité autorisés que les consommateurs peuvent utiliser pour créer leurs comptes locaux dans votre locataire Azure AD B2C.

   ![Sélectionner les types d’identité autorisés](media/phone-authentication-user-flows/configure-local-idp.png)

1. Sélectionnez **Enregistrer**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Ajouter l’inscription par téléphone à un flux d’utilisateur

Une fois que vous avez ajouté l’inscription par téléphone comme option d’identité pour les comptes locaux, vous pouvez l’ajouter aux flux d’utilisateurs tant qu’il s’agit des versions **recommandées** les plus récentes des flux d’utilisateurs. Ce qui suit est un exemple montrant comment ajouter l’inscription par téléphone aux nouveaux flux d’utilisateurs. Toutefois, vous pouvez également ajouter l’inscription par téléphone à des flux d’utilisateurs existants d’une version recommandée (sélectionnez **Flux d’utilisateurs** > *nom du flux d’utilisateur* > **Fournisseurs d’identité** > **Inscription d’un compte local par téléphone**). 

Voici un exemple montrant comment ajouter l’inscription par téléphone à un nouveau flux d’utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs**, puis sélectionnez **Nouveau flux d’utilisateur**.

    ![Page Flux d'utilisateur du portail avec bouton Nouveau flux d'utilisateur en surbrillance](./media/phone-authentication-user-flows/sign-up-sign-in-user-flow.png)

1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**.

    ![Page Sélectionner un flux d’utilisateur avec Inscription et connexion en évidence](./media/phone-authentication-user-flows/select-user-flow-type.png)

1. Sous **Sélectionner une version**, sélectionnez **Recommandé**, puis **Créer**. ([Apprenez-en davantage](user-flow-versions.md) sur les version de flux utilisateur.)

    ![Bouton Créer un flux d’utilisateur](./media/phone-authentication-user-flows/select-version.png)

1. Entrez un **nom** pour le flux d’utilisateur, par exemple *signupsignin1*.
1. Dans la section **Fournisseurs d’identité**, sous **Comptes locaux**, sélectionnez **Inscription par téléphone**.

   ![Option **Inscription par téléphone** du flux d’utilisateur sélectionnée](media/phone-authentication-user-flows/user-flow-phone-signup.png)

1. Sous **Fournisseurs d’identité sociaux**, sélectionnez tout autre fournisseur d’identité que vous souhaitez autoriser pour ce flux d’utilisateur.

   > [!NOTE]
   > L’authentification multifacteur (MFA) est désactivée par défaut pour les flux d’utilisateurs d’inscription. Vous pouvez activer la MFA pour un flux d’utilisateur avec inscription par téléphone, mais, comme un numéro de téléphone est utilisé comme identificateur principal, l’envoi par e-mail d’un code secret à usage unique est la seule option disponible pour le deuxième facteur d’authentification.

1. Dans la section **Attributs utilisateur et revendications de jetons**, choisissez les revendications et les attributs à collecter et qui doivent être envoyés par l’utilisateur pendant l’inscription. Par exemple, sélectionnez **Afficher plus**, puis choisissez des attributs et des revendications pour **Pays/région**, **Nom d’affichage** et **Code postal**. Sélectionnez **OK**.

1. Sélectionnez **Créer** pour ajouter le flux d’utilisateur. Un préfixe *B2C_1* est automatiquement ajouté au nom.

## <a name="enable-the-recovery-email-prompt-preview"></a>Activer l’invite de récupération par e-mail (préversion)

Lorsque vous activez l’inscription et la connexion par téléphone pour vos flux d’utilisateurs, il est également judicieux d’activer la fonctionnalité de récupération par e-mail. Grâce à cette fonctionnalité, un utilisateur peut fournir une adresse e-mail qui peut être utilisée pour récupérer son compte lorsqu’il n’a pas son téléphone. Cette adresse e-mail est utilisée uniquement pour la récupération du compte. Elle ne peut pas être utilisée pour se connecter.

- Lorsque l’invite de récupération par e-mail est **activée**, un utilisateur qui s’inscrit pour la première fois est invité à confirmer une adresse e-mail de secours. Un utilisateur qui n’a pas fourni d’e-mail de récupération auparavant est invité à confirmer une adresse e-mail de secours lors de sa prochaine connexion.

- Lorsque la récupération par e-mail est **désactivée**, un utilisateur qui s’inscrit ou se connecte ne reçoit pas d’invite de récupération par e-mail.
 
Vous pouvez activer l’invite de récupération par e-mail dans les propriétés du flux d’utilisateur.

> [!NOTE]
> Avant de commencer, assurez-vous d’avoir [ajouté l’inscription par téléphone à votre flux d’utilisateur](#add-phone-sign-up-to-a-user-flow) comme décrit ci-dessus.

### <a name="to-enable-the-recovery-email-prompt"></a>Pour activer l’invite de récupération par e-mail

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.
1. Sélectionnez le flux d’utilisateur dans la liste.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. À côté de l’option **Activer l’invite de récupération par e-mail pour l’inscription et la connexion par numéro de téléphone (préversion)** , sélectionnez :

   - **Activé** pour afficher l’invite de récupération par e-mail lors de l’inscription et de la connexion.
   - **Désactivé** pour masquer l’invite de récupération par e-mail.

    ![Propriétés des flux d’utilisateurs avec l’option Activer la récupération par e-mail activée](./media/phone-authentication-user-flows/recovery-email-settings.png)

1. Sélectionnez **Enregistrer**.

### <a name="to-test-the-recovery-email-prompt"></a>Pour tester l’invite de récupération par e-mail

Une fois que vous avez activé l’inscription et la connexion par téléphone et l’invite de récupération par e-mail dans votre flux d’utilisateur, vous pouvez utiliser **Exécuter le flux d’utilisateur** pour tester l’expérience utilisateur.

1. Sélectionnez **Stratégies** > **Flux d’utilisateurs**, puis sélectionnez le flux d’utilisateur que vous avez créé. Dans la page de vue d’ensemble du flux utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.

1. Pour **Application**, sélectionnez l’application web que vous avez inscrite à l’étape 1. L’**URL de réponse** doit être `https://jwt.ms`.

1. Sélectionnez **Exécuter le flux d’utilisateur** et vérifiez le comportement suivant :

   - Un utilisateur qui s’inscrit pour la première fois est invité à fournir un e-mail de récupération. 
   - Un utilisateur qui s’est déjà inscrit, mais qui n’a pas fourni d’e-mail de récupération est invité à en fournir un lors de la connexion.

1. Entrez une adresse e-mail, puis sélectionnez **Envoyer le code de vérification**. Vérifiez la réception du code dans la boîte de réception de l’adresse e-mail que vous avez fournie. Récupérez le code et entrez-le dans la zone **Code de vérification**. Ensuite, sélectionnez **Vérifier le code**.

## <a name="enable-consent-information"></a>Activer les informations relatives au consentement

Nous vous suggérons vivement d’inclure des informations relatives au consentement dans votre flux d’inscription et de connexion. Un exemple de texte est fourni. Reportez-vous au manuel relatif à la surveillance des codes courts sur le [site web de CTIA](https://www.ctia.org/programs) et consultez vos propres experts en droit ou en conformité pour obtenir des conseils sur le texte final et la configuration des fonctionnalités afin de répondre à vos propres besoins en matière de conformité :
>
> *En fournissant votre numéro de téléphone, vous acceptez de recevoir un code secret à usage unique envoyé par SMS pour vous aider à vous connecter à *&lt;insérer : le nom de votre application&gt;* . Des frais standard de messagerie et de données peuvent s’appliquer.*
>
> *&lt;insérer : lien vers votre déclaration de confidentialité&gt;*<br/>*&lt;insérer : lien vers vos conditions d’utilisation du service&gt;*

Pour activer les informations relatives au consentement

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.
1. Sélectionnez le flux d’utilisateur dans la liste.
1. Sous **Personnaliser**, sélectionnez **Langues**.
1. Pour afficher le texte de consentement, sélectionnez **Activer la personnalisation de la langue**.
  
    ![Activer la personnalisation de la langue](./media/phone-authentication-user-flows/enable-language-customization.png)

1. Pour personnaliser les informations relatives au consentement, sélectionnez une langue dans la liste.
1. Dans le panneau de langue, sélectionnez  **Page de connexion par téléphone**.
1. Sélectionnez Télécharger les paramètres par défaut.

    ![Télécharger les paramètres par défaut](./media/phone-authentication-user-flows/phone-sign-in-language-override.png)

1. Ouvrez le fichier JSON téléchargé. Recherchez le texte suivant et personnalisez-le :

    - **disclaimer_link_1_url** : Remplacez **écraser** par « true » et ajoutez l’URL de vos informations de confidentialité.

    - **disclaimer_link_2_url** : Remplacez **écraser** par « true » et ajoutez l’URL de vos Conditions d’utilisation.  

    - **disclaimer_msg_intro** : Remplacez **écraser** par « true » et **valeur** par les clause d’exclusion de responsabilité souhaitées.  

1. Enregistrez le fichier . Sous **Charger de nouveaux remplacements**, recherchez le fichier et sélectionnez-le. Vérifiez que la notification « Chargement réussi des remplacements » s’affiche.
1. Sélectionnez **Page d’inscription par téléphone**, puis répétez les étapes 10 à 12. 


## <a name="get-a-users-phone-number-in-your-directory"></a>Obtenez le numéro de téléphone d’un utilisateur dans votre répertoire

1. Exécutez la requête suivante dans Graph Explorer :

   `GET https://graph.microsoft.com/v1.0/users/{object_id}?$select=identities`

1. Trouvez la propriété `issuerAssignedId` dans la réponse :

   ```json
       "identities": [
           {
               "signInType": "phoneNumber",
               "issuer": "contoso.onmicrosoft.com",
               "issuerAssignedId": "+11231231234"
           }
       ]
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des fournisseurs d’identité externes](add-identity-provider.md)
- [Créer un flux utilisateur](tutorial-create-user-flows.md)
