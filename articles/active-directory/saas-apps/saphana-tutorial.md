---
title: 'Didacticiel : intégration d’Azure Active Directory à SAP HANA | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: a122101ee6e42e3314386319c0b1cd15024eb3e3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309066"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Didacticiel : Intégration d’Azure Active Directory à SAP HANA

Dans ce tutoriel, vous allez voir comment intégrer SAP HANA à Azure Active Directory (Azure AD). Quand vous intégrez SAP HANA à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à SAP HANA.
* Permettre à vos utilisateurs de se connecter automatiquement à SAP HANA avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SAP HANA, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SAP HANA pour lequel l’authentification unique est activée
- Une instance HANA en cours d’exécution sur une IaaS publique, locale, sur une machine virtuelle Azure ou sur une grande instance SAP dans Azure
- L’interface web d’administration XSA ainsi que HANA Studio, installés sur l’instance HANA

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production de SAP HANA pour tester les étapes de ce didacticiel. Testez d’abord l’intégration dans l’environnement de développement ou l’environnement de préproduction de l’application, puis passez à l’environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Un abonnement SAP HANA pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SAP HANA prend en charge l’authentification unique initiée par le **fournisseur d’identité**.
* SAP HANA prend en charge l’approvisionnement d’utilisateurs **juste-à-temps**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.


## <a name="adding-sap-hana-from-the-gallery"></a>Ajout de SAP HANA à partir de la galerie

Pour configurer l’intégration de SAP HANA à Azure AD, vous devez ajouter SAP HANA à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **SAP HANA** dans la zone de recherche.
1. Sélectionnez **SAP HANA** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Configurer et tester l’authentification unique Azure AD pour SAP HANA

Configurez et testez l’authentification unique Azure AD avec SAP HANA à l’aide d’un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur SAP HANA associé.

Pour configurer et tester l’authentification unique Azure AD avec SAP HANA, effectuez les étapes suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
2. **[Configurer l’authentification unique SAP HANA](#configure-sap-hana-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test SAP HANA](#create-sap-hana-test-user)** pour avoir un équivalent de Britta Simon dans SAP HANA lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **SAP HANA**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir ces valeurs, contactez l’[équipe de support technique SAP HANA](https://cloudplatform.sap.com/contact.html). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

5. L’application SAP HANA attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur le bouton **Modifier** pour ouvrir la boîte de dialogue **Attributs utilisateur**.

    ![Capture d’écran montrant la section « User Attributes » avec l’icône « Edit » sélectionnée.](common/edit-attribute.png)

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Attributs et revendications de l’utilisateur**, procédez comme suit :
 
    a. Cliquez sur l’**icône Modifier** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![Capture d’écran montrant la boîte de dialogue « Attributs utilisateur et revendications » avec le bouton « Modifier » sélectionné.](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Dans la liste **Transformation**, sélectionnez **ExtractMailPrefix()** .

    c. Dans la liste **Paramètre 1**, sélectionnez **user.mail**.

    d. Cliquez sur **Enregistrer**.

7. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B. Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP HANA.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **SAP HANA**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-sap-hana-sso"></a>Configurer l’authentification unique SAP HANA

1. Pour configurer l’authentification unique côté SAP HANA, connectez-vous à votre **console web HANA XSA** en accédant au point de terminaison HTTPS correspondant.

    > [!NOTE]
    > Dans la configuration par défaut, l’URL redirige la requête vers un écran d’ouverture de session, où les informations d’identification d’un utilisateur de la base de données SAP HANA agréé sont nécessaires. L’utilisateur qui se connecte doit avoir les autorisations nécessaires pour effectuer des tâches d’administration SAML.

2. Dans l’Interface web XSA, accédez à **SAML Identity Provider** (Fournisseur d’identité SAML). Ensuite, sélectionnez le bouton **+** en bas de l’écran pour afficher le volet **Add Identity Provider Info** (Ajouter les informations du fournisseur d’identité). Ensuite, effectuez les étapes suivantes :

    ![Ajouter un fournisseur d’identité](./media/saphana-tutorial/sap1.png)

    a. Dans le volet **d’ajout des informations sur le fournisseur d’identité**, collez le contenu du fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure dans la zone **Métadonnées**.

    ![Capture d’écran montrant le volet « Add Identity Provider Info » avec les zones « Metadata » et « Name » mises en évidence.](./media/saphana-tutorial/sap2.png)

    b. Si le contenu du document XML est valide, le processus d’analyse extrait les informations requises pour les champs **Subject, Entity ID et Issuer** (Sujet, ID d’entité et Émetteur) dans la zone d’écran **General data** (Données générales). Il extrait également les informations nécessaires pour les champs d’URL dans la zone d’écran **Destination**, tels que les champs **Base URL and SingleSignOn URL (*)** (URL de base et URL d’authentification unique).

    ![Paramètres d’ajout d’un fournisseur d’identité](./media/saphana-tutorial/sap3.png)

    c. Dans la zone **Nom** de la zone d’écran **General Data** (Données générales), entrez un nom pour le nouveau fournisseur d’identité d’authentification unique SAML.

    > [!NOTE]
    > Le nom du fournisseur d’identité SAML est obligatoire et doit être unique. Il apparaît dans la liste des fournisseurs d’identité SAML disponibles qui s’affiche quand vous sélectionnez SAML comme méthode d’authentification pour les applications SAP HANA XS à utiliser. Par exemple, vous pouvez faire cela dans la zone d’écran **Authentication** (Authentification) de l’outil d’administration d’artefact XS.

3. Sélectionnez **Save** (Enregistrer) pour enregistrer les détails du fournisseur d’identité SAML et ajouter le nouveau fournisseur d’identité SAML à la liste des fournisseurs d’identité SAML connus.

    ![Bouton Enregistrer](./media/saphana-tutorial/sap4.png)

4. Dans Studio HANA, dans les propriétés système de l’onglet **Configuration**, filtrez les paramètres en fonction de **saml**. Ensuite, ajustez **assertion_timeout** en remplaçant **10 s** par **120 s**.

    ![Paramètre assertion_timeout](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Créer un utilisateur de test SAP HANA

Pour permettre aux utilisateurs Azure AD de se connecter à SAP HANA, vous devez les provisionner dans SAP HANA.
SAP HANA prend en charge le provisionnement **juste-à-temps**, qui est activé par défaut.

Si vous avez besoin de créer un utilisateur manuellement, effectuez les étapes suivantes :

>[!NOTE]
>Vous pouvez changer l’authentification externe dont se sert l’utilisateur. Il peut s’authentifier auprès d’un système externe, tel que Kerberos. Pour plus d’informations sur les identités externes, contactez votre [administrateur de domaine](https://cloudplatform.sap.com/contact.html).

1. Ouvrez [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) en tant qu’administrateur, puis activez l’utilisateur de base de données pour l’authentification unique SAML.

    ![Créer un utilisateur](./media/saphana-tutorial/sap5.png)

2. Cochez la case invisible à gauche de **SAML**, puis sélectionnez le lien **Configurer**.

3. Sélectionnez **Ajouter** pour ajouter le fournisseur d’identité SAML.  Sélectionnez le fournisseur d’identité SAML approprié, puis sélectionnez **OK**.

4. Ajouter **l’identité externe** (dans ce cas, BrittaSimon). Sélectionnez ensuite **OK**.

   > [!Note]
   > Vous devez renseigner le champ **Identité externe** pour l’utilisateur qui doit correspondre au champ **ID nom** dans le jeton SAML d’Azure AD. **Toutes** les cases ne doivent pas être cochées, car cette option requiert que le fournisseur d’identité (IDP) envoie la propriété SPProvderID dans le champ ID nom qui n’est pas pris en charge par Azure AD. Consultez [ce](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/db6db355bb571014b56eb25057daec5f.html) document pour plus d’informations.

5. À des fins de test, affectez tous les rôles **XS** à l’utilisateur.

    ![Attribution de rôles](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Vous devez donner les autorisations qui sont appropriées pour vos cas d’usage uniquement.

6. Enregistrez l’utilisateur.

### <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes.

* Dans le portail Azure, cliquez sur Tester cette application. Vous êtes alors automatiquement connecté à l’instance de SAP HANA pour laquelle vous avez configuré l’authentification unique.

* Vous pouvez utiliser Mes applications de Microsoft. Le fait de cliquer sur la vignette SAP HANA dans Mes applications doit vous connecter automatiquement à l’application SAP HANA pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré SAP HANA, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
