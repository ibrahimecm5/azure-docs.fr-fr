---
title: 'Didacticiel : intégration d’Azure Active Directory avec ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ON24 Virtual Environment SAML Connection.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: 654673a394550d396e08a8de5e7914befddb480b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329986"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Didacticiel : intégration d’Azure Active Directory avec ON24 Virtual Environment SAML Connection

Dans ce tutoriel, vous allez apprendre à intégrer ON24 Virtual Environment SAML Connection à Azure Active Directory (Azure AD). Quand vous intégrez ON24 Virtual Environment SAML Connection à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à ON24 Virtual Environment SAML Connection.
* Permettre à vos utilisateurs de se connecter automatiquement à ON24 Virtual Environment SAML Connection avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement ON24 Virtual Environment SAML Connection pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* ON24 Virtual Environment SAML Connection prend en charge l’authentification unique initiée par le **fournisseur de services** et le **fournisseur d’identité**.

> [!NOTE]
> L’identificateur de cette application étant une valeur de chaîne fixe, une seule instance peut être configurée dans un locataire.

## <a name="add-on24-virtual-environment-saml-connection-from-the-gallery"></a>Ajouter ON24 Virtual Environment SAML Connection à partir de la galerie

Pour configurer l’intégration de la connexion SAML à l’environnement virtuel ON24 à Azure AD, vous devez ajouter la connexion SAML à l’environnement virtuel ON24 à votre liste d’applications SaaS managées depuis la galerie.

1. Connectez-vous au portail Azure avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, saisissez **ON24 Virtual Environment SAML Connection** dans la zone de recherche.
1. Sélectionnez **ON24 Virtual Environment SAML Connection** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-sso-for-on24-virtual-environment-saml-connection"></a>Configurer et tester l’authentification unique Azure AD pour ON24 Virtual Environment SAML Connection

Configurez et testez l’authentification unique Azure AD avec ON24 Virtual Environment SAML Connection à l’aide d’un utilisateur de test appelé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur associé dans ON24 Virtual Environment SAML Connection.

Pour configurer et tester l’authentification unique Azure AD avec ON24 Virtual Environment SAML Connection, procédez comme suit :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
    1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
    1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Configurer l’authentification unique d’ON24 Virtual Environment SAML Connection](#configure-on24-virtual-environment-saml-connection-sso)** pour configurer les paramètres de l’authentification unique côté application.
    1. **[Créer un utilisateur de test ON24 Virtual Environment SAML Connection](#create-on24-virtual-environment-saml-connection-test-user)** pour avoir un équivalent de B.Simon dans ON24 Virtual Environment SAML Connection lié à la représentation Azure AD de l’utilisateur.
1. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le portail Azure, accédez à la page d’intégration de l’application **ON24 Virtual Environment SAML Connection**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de crayon de **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. À la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    a. Dans la zone de texte **Identificateur**, saisissez l’une des valeurs suivantes :

    | **URL d’environnement de production** |
    |------|
    | `SAML-VSHOW.on24.com` |
    | `SAML-Gateway.on24.com` |
    | `SAP PROD SAML-EliteAudience.on24.com` |
    |
                
    | **URL d’environnement assurance qualité** |
    |-----|
    | `SAMLQA-VSHOW.on24.com` |
    | `SAMLQA-Gateway.on24.com` |
    | `SAMLQA-EliteAudience.on24.com` |
    |

    b. Dans la zone de texte **URL de réponse**, tapez l’une des URL suivantes :

    | **URL d’environnement de production** |
    |-----|
    | `https://federation.on24.com/sp/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2` |
    |

    | **URL d’environnement assurance qualité** |
    |-------|
    | `https://qafederation.on24.com/sp/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` |
    |

    c. Cliquez sur **Définir des URL supplémentaires**. 

    d. Dans la zone de texte **État de relais**, entrez une URL en utilisant le modèle suivant : `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service**, procédez comme suit :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://vshow.on24.com/vshow/<INSTANCE_NAME>`.

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’état de relais et l’URL de connexion réels. Contactez l’[équipe de support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/contact-us/) pour obtenir ces valeurs. Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

6. Dans la section **Configurer ON24 Virtual Environment SAML Connection**, copiez la ou les URL appropriées en fonction de vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

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

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ON24 Virtual Environment SAML Connection.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **ON24 Virtual Environment SAML Connection**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.
1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.
1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** au bas de l’écran.
1. Si vous attendez qu’un rôle soit attribué aux utilisateurs, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un rôle** . Si aucun rôle n’a été configuré pour cette application, vous voyez le rôle « Accès par défaut » sélectionné.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

## <a name="configure-on24-virtual-environment-saml-connection-sso"></a>Configurer l’authentification unique d’ON24 Virtual Environment SAML Connection

Pour configurer l’authentification unique sur **ON24 Virtual Environment SAML Connection**, vous devez envoyer le fichier **XML des métadonnées de fédération** et les URL correspondantes copiées à partir du portail Azure à l’[équipe de support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Créer un utilisateur de test ON24 Virtual Environment SAML Connection

Dans cette section, vous créez un utilisateur appelé Britta Simon dans ON24 Virtual Environment SAML Connection. Travaillez avec l’[équipe du support technique ON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) pour ajouter des utilisateurs à la plateforme ON24 Virtual Environment SAML Connection. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

## <a name="test-sso"></a>Tester l’authentification unique (SSO) 

Dans cette section, vous allez tester votre configuration de l’authentification unique Azure AD avec les options suivantes. 

#### <a name="sp-initiated"></a>Lancée par le fournisseur de services :

* Cliquez sur **Tester cette application** dans le portail Azure. Vous êtes alors redirigé vers l’URL de connexion d’ON24 Virtual Environment SAML Connection, à partir de laquelle vous pouvez lancer le flux de connexion.  

* Accédez directement à l’URL de connexion d’ON24 Virtual Environment SAML Connection pour initier le flux de connexion.

#### <a name="idp-initiated"></a>Lancée par le fournisseur d’identité :

* Cliquez sur **Tester cette application** dans Portail Azure : vous devez être connecté automatiquement à l’instance d’ON24 Virtual Environment SAML Connection pour laquelle vous avez configuré l’authentification unique. 

Vous pouvez aussi utiliser Mes applications de Microsoft pour tester l’application dans n’importe quel mode. Quand vous cliquez sur la vignette ON24 Virtual Environment SAML Connection dans Mes applications, si le mode Fournisseur de services est configuré, vous êtes redirigé vers la page de connexion de l’application pour lancer le flux de connexion ; si le mode Fournisseur d’identité est configuré, vous êtes automatiquement connecté à l’instance d’ON24 Virtual Environment SAML Connection pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur Mes applications, consultez [Présentation de Mes applications](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré ON24 Virtual Environment SAML Connection, vous pouvez appliquer le contrôle de session, qui protège contre l’exfiltration et l’infiltration des données sensibles de votre organisation en temps réel. Le contrôle de session est étendu à partir de l’accès conditionnel. [Découvrez comment appliquer un contrôle de session avec Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
