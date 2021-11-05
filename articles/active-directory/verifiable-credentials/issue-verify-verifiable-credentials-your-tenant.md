---
title: Tutoriel - Émettre et vérifier des justificatifs vérifiables à l’aide de votre locataire Azure (préversion)
description: Modifiez l’exemple de code de justificatifs vérifiables pour qu’il fonctionne avec votre locataire Azure
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: barclayn
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: bf1e79f2868ee5f1a3c6b0b920a93a8a1f92c068
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049156"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Tutoriel - Émettre et vérifier des justificatifs vérifiables à l’aide de votre locataire (préversion)

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Maintenant que votre locataire Azure est configuré avec le service de justificatifs vérifiables, nous vous guidons à travers les étapes nécessaires pour permettre à votre instance Azure Active Directory (Azure AD) d’émettre des justificatifs vérifiables à l’aide de l’exemple d’application.

Dans cet article, vous découvrirez comment :

> [!div class="checklist"]
> * Inscrire l’exemple d’application dans Azure AD
> * Créer des fichiers de règles et d’affichage
> * Charger des fichiers de règles et d’affichage
> * Configurer le service émetteur de justificatifs vérifiables afin qu’il utilise Azure Key Vault
> * Mettre à jour l’exemple de code avec les informations de votre locataire.

Notre exemple de code oblige les utilisateurs à s’authentifier auprès d’un fournisseur d’identité, ici Azure AD B2C, avant de pouvoir émettre les justificatifs de l’expert en justificatifs vérifiés. Tous les émetteurs de justificatifs vérifiables requièrent une authentification avant d’émettre des justificatifs.

L’authentification des jetons d’ID permet aux utilisateurs de prouver leur identité avant de recevoir leurs justificatifs. Lorsque les utilisateurs se connectent correctement, le fournisseur d’identité retourne un jeton de sécurité contenant les revendications relatives à l’utilisateur. Le service émetteur transforme ensuite ces jetons de sécurité et leurs revendications en justificatifs vérifiables. Les justificatifs vérifiables sont signés avec le DID de l’émetteur.

Tout fournisseur d’identité prenant en charge le protocole OpenID Connect est pris en charge. [Azure Active Directory](../fundamentals/active-directory-whatis.md)et [Azure AD B2C](../../active-directory-b2c/overview.md)sont des exemples de fournisseurs d’identité pris en charge. Dans ce tutoriel, nous utilisons AAD.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez avoir déjà effectué les étapes du [tutoriel précédent](enable-your-tenant-verifiable-credentials.md) et avoir accès à l’environnement que vous avez utilisé.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Inscrire une application pour permettre aux portefeuilles de DID de se connecter aux utilisateurs

Pour émettre des justificatifs vérifiables, vous devez inscrire une application de sorte que l’authentificateur ou tout autre portefeuille de justificatifs vérifiable soit autorisé à se connecter aux utilisateurs.  

Inscrivez une application appelée « VC Wallet App » dans Azure AD et obtenez un ID client.

1. Suivez les instructions d’inscription d’une application avec [Azure AD](../develop/quickstart-register-app.md). Lors de l’inscription, utilisez les valeurs ci-dessous.

   - Nom : « VC Wallet App »
   - Types de comptes pris en charge : Comptes dans cet annuaire organisationnel uniquement
   - URI de redirection : vcclient://openid/

   ![Inscrire une application](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Une fois l’application inscrite, notez l’ID de l’application (client). Vous aurez besoin de cette valeur ultérieurement.

   ![ID d’application (client)](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Sélectionnez le bouton **Points de terminaison**, puis copiez l’URI du document de métadonnées OpenID Connect. Vous aurez besoin de ces informations dans la section suivante. 

   ![Points de terminaison de l’émetteur](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)


Jusqu’à présent, nous avons travaillé avec notre exemple d’application. L’application utilise [Azure Active Directory B2C](../../active-directory-b2c/overview.md), et nous allons maintenant utiliser Azure AD. Nous devons donc apporter des modifications non seulement pour faire correspondre votre environnement, mais également pour prendre en charge des revendications supplémentaires qui n’ont pas été utilisées auparavant.

1. Copiez le fichier de règles ci-dessous et enregistrez-le dans **modified-expertRules.json**. 

    > [!NOTE]
    > **"scope": "openid profile"** est inclus dans ce fichier de règles et n’était pas inclus dans le fichier de règles de l’exemple d’application. La section suivante explique comment activer les revendications facultatives dans votre locataire Azure Active Directory. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Ouvrez le fichier et remplacez les valeurs de **client_id** et **configuration** par les deux valeurs que nous avons copiées dans la section précédente.

    ![Mise en évidence des deux valeurs qui doivent être modifiées dans le cadre de cette étape](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  La valeur **Configuration** est l’URI du document de métadonnées OpenID Connect.

  Étant donné que l’exemple de code utilise Azure Active Directory B2C et que nous utilisons Azure Active Directory, nous devons ajouter des revendications facultatives via des étendues afin que ces revendications soient incluses dans le jeton d’ID pour être écrites dans les justificatifs vérifiables. 

3. Dans le portail Azure, ouvrez Azure Active Directory.
4. Choisissez **Inscriptions d’applications**.
5. Ouvrez l’application VC Wallet que nous avons créée précédemment.
6. Choisissez **Jeton d’authentification**.
7. Choisissez **Ajouter une revendication facultative**

     ![Sous Configuration du jeton, ajoutez une revendication facultative](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Dans **Type de jeton**, choisissez **ID**, puis dans la liste des revendications disponibles, choisissez **given_name** et **family_name**

     ![Ajouter des revendications facultatives](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Cliquez sur **Ajouter**.
10. Si vous recevez un avertissement sur les autorisations comme indiqué ci-dessous, cochez la case et sélectionnez **Ajouter**.

     ![Ajouter des autorisations pour des revendications facultatives](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Désormais, lorsqu’un utilisateur voit la « connexion » pour obtenir vos justificatifs vérifiables, l’application VC Wallet sait qu’elle doit inclure les revendications spécifiques via le paramètre d’étendue pour l’écriture dans les justificatifs vérifiables.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Créer de nouveaux justificatifs vérifiables avec ce fichier de règles et l’ancien fichier d’affichage

1. Charger le nouveau fichier de règles dans notre conteneur
1. Dans la page Justificatifs vérifiables, créez des justificatifs appelés **modifiedCredentialExpert** à l’aide de l’ancien fichier d’affichage et du nouveau fichier de règles (**modified-credentialExpert.json**).
1. Une fois le processus de création des informations d’identification terminé à partir de la page **Vue d’ensemble**, copiez l’**URL d’émission des justificatifs** et enregistrez-la, car nous en aurons besoin dans la section suivante.

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>Configurer votre application de nœud avec accès à Azure Key Vault

Pour authentifier la demande d’émission de justificatifs d’un utilisateur, le site web émetteur utilise vos clés de chiffrement dans Azure Key Vault. Pour accéder à Azure Key Vault, votre site web a besoin d’un ID client et d’une clé secrète client qui peuvent être utilisés pour s’authentifier auprès d’Azure Key Vault.

Tout d’abord, nous devons inscrire une autre application. Cette inscription concerne le site web. L’inscription de l’application Wallet précédente ne vise qu’à permettre aux utilisateurs de se connecter au répertoire dans lequel elle se trouve. Dans le cas présent, il s’agit du même répertoire. Cependant, l’inscription de l’application Wallet aurait également pu être effectuée dans un autre répertoire. Une bonne pratique consiste à séparer les inscriptions d’applications si la responsabilité des applications est différente. Il s’agit ici que notre site web puisse accéder à Key Vault.

1. Suivez les instructions d’inscription d’une application avec [Azure AD](../develop/quickstart-register-app.md). Lors de l’inscription, utilisez les valeurs ci-dessous.

   - Nom : « VC Website »
   - Types de comptes pris en charge : Comptes dans cet annuaire organisationnel uniquement

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-registration.png" alt-text="Capture d’écran montrant comment enregistrer une application.":::

1. Une fois l’application inscrite, notez l’ID de l’application (client). Vous aurez besoin de cette valeur ultérieurement.

   :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-app-details.png" alt-text="Capture d’écran montrant l’ID client de l’application.":::

1. Tout en affichant la page Vue d’ensemble de l’application VC Website, sélectionnez **Certificats et secrets**.

    :::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/vc-website-app-certificates-secrets.png" alt-text="Capture d’écran montrant le volet Certificats et secrets.":::

1. Dans la section **Secrets client**, choisissez **Nouveau secret client**
    1. Ajoutez une description comme « Secret client VC nœud »
    1. Expire : dans un an.

    ![Secret de l’application avec une expiration d’un an](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)

1. Copiez le SECRET. Vous en aurez besoin pour mettre à jour votre exemple d’application de nœud.

>[!WARNING]
> Vous avez une seule chance de copier le secret. Le secret est haché de façon unidirectionnelle après cela. Ne copiez pas l’ID. 

Après avoir créé votre application et votre clé secrète client dans Azure AD, vous devez accorder à l’application les autorisations nécessaires pour effectuer des opérations sur votre Key Vault. Ces modifications d’autorisations sont nécessaires pour permettre au site web d’accéder aux clés privées stockées et de les utiliser.

1. Accédez à Key Vault.
2. Sélectionnez le coffre de clés que nous utilisons pour ces tutoriels.
3. Choisissez **Stratégies d’accès** dans la navigation de gauche
4. Choisissez **+Ajouter une stratégie d’accès**.
5. Dans la section **Autorisations de clé**, choisissez **Obtenir**, puis **Signer**.
6. Sélectionnez **Principal** et utilisez l’ID de l’application pour rechercher l’application que nous avons enregistrée précédemment. Sélectionnez-le.
7. Sélectionnez **Ajouter**.
8. Choisir **ENREGISTRER**.

:::image type="content" source="media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png" alt-text="Capture d’écran montrant l’ajout d’une stratégie d’accès.":::

Pour plus d’informations sur les autorisations de Key Vault et le contrôle d’accès, consultez le [Guide RBAC de Key Vault](../../key-vault/general/rbac-guide.md).

## <a name="make-changes-to-the-sample-app"></a>Modification de l’exemple d’application

Nous devons récupérer quelques valeurs avant de pouvoir apporter les modifications de code nécessaires. Nous utilisons ces valeurs dans la section suivante pour que l’exemple de code utilise vos propres clés stockées dans votre coffre. À ce stade, les valeurs suivantes devraient être prêtes.

- L’**URI de contrat** des justificatifs que nous venons de créer (URL d’émission des justificatifs)
- L’**ID client** d’application que nous avons obtenu lors de l’inscription de l’application de nœud.
- La **Clé secrète client** que nous avons créée précédemment lorsque nous avons accordé à votre application un accès à Key Vault.

Nous avons besoin de quelques autres valeurs pour pouvoir effectuer les modifications une fois dans notre exemple d’application. Allons les récupérer maintenant !

### <a name="verifiable-credentials-settings"></a>Paramètres des justificatifs vérifiables

1. Accédez à la page Justificatifs vérifiables et choisissez **Paramètres**.  
1. Copiez les valeurs suivantes :

    - Identificateur de locataire 
    - Identificateur de l’émetteur (votre DID)
    - Coffre de clés (URI)

1. Sous l’identificateur de clé de signature, il existe un URI, mais nous en avons uniquement besoin d’une partie. Copiez à partir de la partie qui indique **issuerSigningKeyION** comme mis en évidence par le rectangle rouge dans l’image ci-dessous.

   ![Identificateur de clé de connexion](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Document DID

1. Ouvrez l’[Explorateur de réseau DIF ION](https://identity.foundation/ion/explorer/)

2. Collez votre DID dans la barre de recherche.

4. À partir de la réponse mise en forme, cherchez la section appelée **verificationMethod**
5. Sous « verificationMethod », copiez `id` et étiquetez-le en tant que kvSigningKeyId.
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Nous avons maintenant tout ce dont nous avons besoin pour apporter les modifications dans notre exemple de code.

- **Émetteur :** Mettez à jour const credential dans d’app.js avec l’URI de votre nouveau contrat
- **Vérificateur :** Mettez à jour l’issuerDid dans app.js avec votre identificateur d’émetteur
- **Émetteur et le vérificateur** : mettez à jour didconfig.json avec les valeurs suivantes :


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Il s’agit d’une application de démonstration. Vous ne devriez normalement jamais transmettre directement secret à l’application.


À présent, tout est en place pour émettre et vérifier vos propres justificatifs vérifiables depuis votre locataire Azure Active Directory avec vos propres clés. 

## <a name="issue-and-verify-the-vc"></a>Émettre et vérifier les justificatifs vérifiables

Suivez les mêmes étapes que celles du tutoriel précédent pour émettre les justificatifs vérifiables et les valider avec votre application. Une fois que vous avez terminé le processus de vérification, vous êtes maintenant prêt à continuer à vous familiariser avec les justificatifs vérifiables.

1. Ouvrez une invite de commandes et ouvrez le dossier de l’émetteur.
2. Exécutez l’application de nœud mise à jour.

    ```terminal
    node app.js
    ```

3. À l’aide d’une autre invite de commandes, exécutez ngrok pour configurer une URL sur 8081.

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Vous pouvez également remarquer un avertissement indiquant que cette application ou ce site web peut être risqué. Ce message est attendu pour l’instant, car nous n’avons pas encore lié votre DID à votre domaine. Suivez les instructions de [Liaison DNS](how-to-dnsbind.md) pour configurer cela.

    
4. Ouvrez l’URL HTTPS générée par ngrok.

    ![Points de terminaison de transfert NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Sélectionnez **GET CREDENTIAL**
6. Dans Authenticator, scannez le code QR.
7. Lorsque le message d’avertissement **Cette application ou ce site web peut être risqué** s’affiche, sélectionnez **Avancé**.

  ![Avertissement initial](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Dans l’avertissement de site web risqué, sélectionnez **Continuer quand même (non sécurisé)** .

  ![Deuxième avertissement relatif à l’émetteur](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Dans l’écran **Ajouter des justificatifs**, notez quelques points : 
    1. En haut de l’écran, vous pouvez voir un message rouge **Non vérifié**
    1. Les justificatifs sont personnalisés en fonction des modifications que nous avons apportées au fichier d’affichage.
    1. L’option **Connectez-vous à votre compte** pointe sur votre page de connexion Azure AD.
    
   ![Écran Ajouter des justificatifs avec avertissement](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Choisissez **Connectez-vous à votre compte** et authentifiez-vous à l’aide d’un utilisateur de votre locataire Azure AD.
11. Une fois l’authentification réussie, le bouton **Ajouter** n’est plus grisé. Choisissez **Ajouter**.

  ![Écran Ajouter des justificatifs après authentification](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Nous avons maintenant publié des justificatifs vérifiables à l’aide de notre locataire pour générer vos justificatifs vérifiables tout en utilisant le locataire B2C d’origine à des fins d’authentification.

  ![Justificatifs vérifiables émis par votre locataire Azure AD et authentifiés par notre instance Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Tester la vérification des justificatifs vérifiables à l’aide de l’exemple d’application

Maintenant que nous avons publié les justificatifs vérifiables depuis notre propre locataire avec les revendications de votre instance Azure AD, vérifions-les à l’aide de l’exemple d’application.

1. Arrêtez l’exécution du service ngrok de l’émetteur.

    ```cmd
    control-c
    ```

2. Exécutez maintenant ngrok avec le port vérificateur 8082.

    ```cmd
    ngrok http 8082
    ```

3. Dans une autre fenêtre de terminal, accédez à l’application du vérificateur et exécutez-la de la même façon que nous avons exécuté l’application émettrice.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Ouvrez l’URL ngrok dans votre navigateur et scannez le code QR à l’aide d’Authenticator sur votre appareil mobile.
5. Sur votre appareil mobile, choisissez **Autoriser** dans l’écran **Nouvelle demande d’autorisation**.

   >[!IMPORTANT]
    > Étant donné que l’exemple d’application utilise également votre DID pour signer la requête de présentation, vous remarquerez un avertissement indiquant que cette application ou ce site web peut être risqué. Ce message est attendu pour l’instant, car nous n’avons pas encore lié votre DID à votre domaine. Suivez les instructions de [Liaison DNS](how-to-dnsbind.md) pour configurer cela.
    
   ![Nouvelle demande d’autorisation](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Vous avez correctement vérifié vos justificatifs et le site web doit afficher votre prénom et votre nom du compte utilisateur de votre instance Azure AD. 

Vous avez maintenant terminé le tutoriel et êtes officiellement un expert en justificatifs vérifié ! Votre exemple d’application utilise votre DID pour l’émission et la vérification, tout en écrivant des revendications dans des justificatifs vérifiables depuis votre instance Azure AD. 

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à créer des [justificatifs personnalisés](credential-design.md)
- [Exemples](issuer-openid.md) de communication du service émetteur
