---
title: 'Tutoriel : Prise en main des justificatifs vérifiables Azure Active Directory à l’aide d’un exemple d’application (préversion)'
description: Dans ce tutoriel, vous allez apprendre à émettre des justificatifs vérifiables à l’aide de notre exemple d’application et de notre locataire de test.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
manager: karenh444
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ROBOTS: NOINDEX
ms.openlocfilehash: 096ca46c2a2c7c54553005fcb4a264c6ef8221d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049175"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Tutoriel : Prise en main des justificatifs vérifiables Azure Active Directory à l’aide d’un exemple d’application (préversion)

Dans ce tutoriel, nous allons passer en revue les étapes nécessaires à l’émission de votre premier justificatif vérifiable : une carte d’expert en justificatifs vérifiés. Vous pourrez ensuite utiliser cette carte pour prouver à un vérificateur que vous êtes un expert en justificatifs vérifiés et maîtrisez l’art des justificatifs numériques. Prenez en main les justificatifs vérifiables Azure Active Directory à l’aide de l’exemple d’application de justificatifs vérifiables pour émettre votre premier justificatif vérifiable.

![Image d’un exemple de carte](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

- [NodeJS](https://nodejs.org/en/download/) version 10.14 ou ultérieure installée sur notre système de test.
- Pour cloner le référentiel qui héberge l’exemple d’application, [GIT](https://git-scm.com/downloads) doit avoir été installé.
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Système pour héberger notre exemple de site.
- Appareil mobile avec Microsoft Authenticator version 6.2005.3599 ou ultérieure.
- [NGROK](https://ngrok.com/) gratuit.

## <a name="download-the-sample-code"></a>Télécharger l’exemple de code

Pour émettre une carte d’expert en justificatifs vérifiés, vous devez exécuter un site web sur votre ordinateur local. Ce site web est utilisé pour lancer un processus d’émission de justificatifs vérifiables. Nous avons mis à disposition un site web simple, écrit en NodeJS et utilisé tout au long de ce tutoriel.

Commencez par télécharger notre exemple de code à partir de GitHub [ici](https://github.com/Azure-Samples/active-directory-verifiable-credentials), ou clonez le référentiel sur votre ordinateur local :

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Vous pouvez vous familiariser avec le code dans l’exemple de site web. Le dossier `issuer` contient l’ensemble du code utilisé pour émettre des justificatifs vérifiables. De plus amples détails sont disponibles dans le [fichier Lisez-moi](https://github.com/Azure-Samples/active-directory-verifiable-credentials) de l’exemple.

## <a name="run-the-issuer-website"></a>Exécuter le site web de l’émetteur

Vous pouvez exécuter les étapes à partir de Visual Studio Code ou de n’importe quelle ligne de commande disponible dans votre système d’exploitation. 

1. Accédez au dossier `issuer`. 

    ```terminal
    cd issuer
    ```

2. Il nous faut ensuite installer tous les packages requis et démarrer le site.

   ```terminal
    npm install
    node app.js
    ```

3. Dans le terminal, vous noterez que votre application émettrice écoute sur le port 8081. Nous allons maintenant configurer un proxy inverse avec Ngrok pour permettre au service Authenticator de communiquer avec votre application. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Création d’un proxy inverse avec Ngrok

Lorsque vous exécutez l’exemple de site web, votre appareil doit communiquer avec le serveur de nœud qui s’exécute sur votre ordinateur local. Nous vous recommandons d’utiliser [ngrok](https://ngrok.com/) pour facilement mettre à disposition votre serveur de développement local sur Internet.

1.  Après avoir téléchargé et extrait **ngrok**, il nous faut exécuter :

    ```terminal
     ngrok http 8081
    ```

Par défaut, l’exemple de site web s’exécute sur le port `8081`. **Ngrok** génère deux URL de transfert pour votre serveur. Copiez l’URL avec le préfixe `https://`.

![ngrok vous aide à mettre à disposition les points de terminaison de votre application sur Internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Si vous utilisez PowerShell, vous devrez peut-être entrer `./ngrok` pour que la commande soit reconnue.

Maintenant que votre port local est exposé à Internet à l’aide de ngrok, l’exemple de site utilise automatiquement le nom d’hôte généré par ngrok. Ouvrez votre navigateur et accédez à l’URL de transfert https ngrok. Vous devriez être en mesure de consulter la page d’accueil de l’exemple de site. Si la page s’ouvre, votre appareil peut communiquer avec l’exemple d’application en cours d’exécution sur votre serveur local. Vous êtes maintenant prêt à émettre une carte d’expert en justificatifs vérifiés.

## <a name="issue-a-credential"></a>Émettre un justificatif

1. Installez Authenticator sur votre appareil mobile. Microsoft Authenticator est utilisé pour recevoir, stocker et présenter vos justificatifs vérifiables aux parties intéressées.

2. Vous pouvez ensuite émettre vous-même des justificatifs vérifiables. **Cliquez** sur le bouton **Obtenir des justificatifs**. Lorsque vous cliquez sur le bouton **Obtenir des justificatifs**, l’exemple de site web affiche un code QR que vous pouvez scanner à l’aide d’Authenticator. Si vous utilisez le navigateur de votre appareil mobile pour afficher le site, le fait de cliquer sur le bouton **Obtenir des justificatifs** déclenche un lien ciblé qui ouvre l’application Authenticator et vous évite de devoir scanner un code QR.

   ![Bouton Obtenir des justificatifs](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Scannez le code QR du site web à l’aide d’Authenticator, ou si vous accédez au site web via un appareil mobile, cliquez sur le bouton Obtenir des justificatifs pour déclencher le lien ciblé. 

   ![Code QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Notez que le bouton **Ajouter** est actuellement grisé. Sélectionnez **Se connecter à votre compte** sous l’image de carte.

   ![Se connecter ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Avant d’obtenir votre carte d’expert en justificatifs vérifiés, le locataire utilisé exige que vous fournissiez des informations d’authentification. S’il s’agit de votre première utilisation du tutoriel, vous n’avez pas de compte d’expert en justificatifs et devez créer un compte d’utilisateur dans notre locataire B2C.

   ![S’authentifier avant de continuer](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Une fois que vous êtes connecté, le bouton **Ajouter** n’est plus grisé. Sélectionnez **Ajouter** pour accepter vos nouveaux justificatifs vérifiables.

   ![Sélectionner Ajouter après authentification](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Félicitations ! Vous disposez maintenant d’une carte d’expert en justificatifs vérifiés.

   ![Carte d’expert en justificatifs vérifiés ajoutée](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Il vous faut maintenant vérifier vos justificatifs.

## <a name="validate-credentials"></a>Valider les informations d'identification

Maintenant que vous avez terminé la partie émission du tutoriel et que vous disposez de justificatifs vérifiables dans Authenticator, validez-les dans votre propre application de vérificateur.

1.  Arrêtez l’exécution du service ngrok de l’émetteur.

    ```terminal
     control+c
    ```


2. Dans une autre fenêtre de terminal, ouvrez le dossier de l’application du vérificateur et exécutez-la de la même façon que nous avons exécuté l’application émettrice.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Exécutez maintenant ngrok avec le port vérificateur 8082.

    ```terminal
    ngrok http 8082
    ```

4. Ouvrez l’URL de transfert HTTPS ngrok dans votre navigateur et appuyez sur le bouton **VÉRIFIER LES INFORMATIONS D’IDENTIFICATION**.  

   ![Bouton d’expert Vérifier les justificatifs](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Ouvrez Authenticator et scannez le code QR.

   ![Scanner le code QR pour vérifier les justificatifs](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > Il se trouve en haut à droite sur iOS et en bas à droite sur Android. Scannez le code QR.

6. Sélectionnez **Autoriser** dans l’écran Nouvelle demande d’autorisation d’Authenticator. En appuyant sur Autoriser, vous signez une présentation vérifiable à l’aide de votre DID (identificateur décentralisé) pour prouver que vous contrôlez ces justificatifs vérifiables.

   ![Nouvelle demande d’autorisation](media/get-started-verifiable-credentials/new-permission-request.png)

    Au terme d’une présentation réussie, trois éléments doivent avoir été mis à jour :

   1. La page web doit maintenant afficher « Félicitations, votre nom » + est un expert en justificatifs vérifiés !».
   
    ![Félicitations, vérifiez à nouveau](media/get-started-verifiable-credentials/congratulations.png)


   2. Le terminal de votre application de vérificateur doit également afficher le même message dans les journaux.
   
    ![Activité de l’application dans l’invite de commandes](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. Dans Authenticator, une entrée doit être présente pour l’activité récente de cette présentation.

    ![Activité dans Authenticator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Lors de l’exécution de l’application du vérificateur, ngrok peut cesser de fonctionner et afficher une erreur indiquant un trop grand nombre de connexions. Pour l’éviter, inscrivez votre compte auprès de ngrok. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez terminé ce guide de démarrage rapide, vous pouvez créer votre propre identificateur décentralisé dans le service des justificatifs vérifiables Azure AD et émettre vos propres justificatifs vérifiables.

>[!div class="nextstepaction"] 
>[Configurer votre propre émetteur à l’aide de l’exemple d’application de justificatifs vérifiables](./enable-your-tenant-verifiable-credentials.md)
