---
title: F5 BIG-IP APM et Azure AD SSO dans les applications avec authentification basée sur les formulaires
description: Découvrez comment intégrer BIG-IP Access Policy Manager (APM) de F5 et Azure Active Directory pour un accès hybride sécurisé aux applications basées sur des formulaires.
author: gargi-sinha
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8306c9f0035eababdcf5feb115786982d78f0d52
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137179"
---
# <a name="tutorial-integrate-azure-active-directory-with-f5-big-ip-for-forms-based-authentication-single-sign-on"></a>Tutoriel : Intégrer Azure Active Directory à F5 BIG-IP pour l’authentification unique basée sur les formulaires

Dans ce tutoriel, vous apprendrez à intégrer BIG-IP Access Policy Manager (APM) de F5 et Azure Active Directory (Azure AD) pour un accès hybride sécurisé aux applications basées sur les formulaires.

L’intégration des applications publiées de BIG-IP avec Azure AD offre de nombreux avantages, notamment :

- Gouvernance à confiance zéro améliorée via la pré-authentification et l’autorisation Azure AD

- Authentification unique (SSO) complète entre Azure AD et les services publiés de BIG IP

- Gestion des identités et de l’accès à partir d’un plan de contrôle unique – le [portail Azure](https://portal.azure.com)

Pour en savoir plus sur tous les avantages, consultez l’article sur [Intégration de F5 BIG-IP et Azure AD](f5-aad-integration.md) et sur [l’accès aux applications et l’authentification unique avec Azure AD](../active-directory-appssoaccess-whatis.md).

## <a name="scenario-description"></a>Description du scénario

Pour ce scénario, nous disposons d’une application héritée interne configurée pour l’authentification basée sur les formulaires (FBA).

Le scénario idéal serait que l’application soit gérée et régie directement par Azure AD, mais comme elle ne dispose d’aucune forme d’interopérabilité avec les protocoles modernes, sa modernisation nécessiterait des efforts et un temps considérables, ce qui entraînerait des coûts inévitables et des risques d’interruption de service.

Au lieu de cela, un BIG-IP Virtual Edition (VE) déployé entre l’Internet et le réseau virtuel Azure interne auquel l’application est connectée sera utilisé pour contrôler l’accès entrant, avec Azure AD pour son large choix de capacités d’authentification et d’autorisation.

Le fait de disposer d’un BIG-IP devant l’application nous permet de superposer le service avec la préauthentification Azure AD et l’authentification unique basée sur les formulaires, améliorant ainsi de manière significative la posture de sécurité globale de l’application, ce qui permet à l’entreprise de continuer à fonctionner à son rythme, sans interruption.

Les informations d’identification de l’utilisateur mises en cache par BIG-IP APM sont ensuite disponibles pour l’authentification unique sur les applications avec authentification basée sur les formulaires.

## <a name="scenario-architecture"></a>Architecture du scénario

La solution d’accès hybride sécurisé pour ce scénario est constituée des éléments suivants :

**Application** : Service principal à protéger par l’accès hybride sécurisé Azure AD et BIG-IP. Cette application spécifique valide les informations d’identification de l’utilisateur sur Active Directory (AD), mais il peut s’agir de n’importe quel répertoire, notamment les services AD LDS (Active Directory Lightweight Directory Services), open source, etc.

**Azure AD** : Fournisseur d’identité SAML (IdP) responsable de la vérification des informations d’identification de l’utilisateur, de l’accès conditionnel et de l’authentification unique à BIG-IP APM.

**BIG-IP** : proxy inverse et fournisseur de services SAML pour l’application, déléguant l’authentification à l’IdP SAML avant d’effectuer une authentification unique basée sur les formulaires auprès de l’application back-end.

![Capture d’écran montrant le diagramme de flux](./media/f5-big-ip-forms-advanced/flow-diagram.png)

| Étapes | Description|
|:-------|:----------|
| 1. | L’utilisateur se connecte au point de terminaison du fournisseur de services SAML de l’application (BIG-IP APM).|
| 2. | La stratégie d’accès d’APM redirige l’utilisateur vers l’IdP SAML (Azure AD) pour une préauthentification.|
| 3. | Le locataire Azure AD authentifie l’utilisateur et applique les stratégies d’accès conditionnel en vigueur.|
| 4. | L’utilisateur est redirigé vers le fournisseur de services SAML avec le jeton et les revendications émis. |
| 5. | BIG-IP invite l’utilisateur à indiquer le mot de passe de l’application et le stocke dans le cache. |
| 6. | BIG-IP envoie une requête à l’application et reçoit un formulaire de connexion.|
| 7. | Le script APM répond automatiquement en renseignant le nom d’utilisateur et le mot de passe avant d’envoyer le formulaire.|
| 8. | La charge utile de l’application est traitée par le serveur web et envoyée au client. Si vous le souhaitez, APM détecte la réussite de la connexion en examinant les en-têtes de réponse et en recherchant le cookie ou l’URI de redirection.|

## <a name="prerequisites"></a>Prérequis

Une expérience préalable de BIG-IP n’est pas nécessaire, mais vous aurez besoin des éléments suivants :

- Un abonnement gratuit Azure AD ou plus

- Un BIG-IP existant ou [déployez un BIG-IP Virtual Edition (VE) dans Azure](f5-bigip-deployment-guide.md)

- L’une des références SKU de licence F5 BIG-IP suivantes

  - F5 BIG-IP&reg; Best Bundle

  - Licence autonome F5 BIG-IP Access Policy Manager&trade; (APM)

  - Licence de module complémentaire F5 BIG-IP Access Policy Manager&trade; (APM) sur l’instance existante de BIG-IP F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM)

  - [Licence d’essai](https://www.f5.com/trial/big-ip-trial.php) de 90 jours des fonctionnalités complètes de BIG-IP

- [Synchronisation](../hybrid/how-to-connect-sync-whatis.md) des identités des utilisateurs entre un annuaire local e Azure AD

- Un compte disposant des [autorisations](../roles/permissions-reference.md#application-administrator) d’administrateur d’application Azure AD

- Un [certificat SSL](f5-bigip-deployment-guide.md#ssl-profile) pour les services de publication sur HTTPS, ou utilisez les certificats par défaut lors des tests

- Une application existante d’authentification basée sur les formulaires ou [configurez une application IIS FBA](/troubleshoot/aspnet/forms-based-authentication) à des fins de test

## <a name="deployment-modes"></a>Modes de déploiement

Il existe plusieurs méthodes pour configurer un BIG-IP pour ce scénario. Ce didacticiel décrit l’approche avancée, qui offre une approche plus flexible pour implémenter un accès hybride sécurisé en créant manuellement tous les objets de configuration du BIG-IP. Vous pouvez utiliser cette approche pour les scénarios non couverts par la configuration guidée basée sur le modèle.

>[!NOTE]
>Tous les exemples de chaînes et de valeurs référencés dans cet article doivent être remplacés par ceux de votre environnement réel.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Ajout de F5 BIG-IP depuis la galerie Azure AD

Configurer une approbation de fédération SAML entre BIG-IP APM et Azure AD est l’une des premières étapes de l’implémentation d’un accès hybride sécurisé. Elle établit l’intégration requise pour que le BIG-IP transmette la pré-authentification et l’[accès conditionnel](../conditional-access/overview.md) à Azure AD avant d’accorder l’accès au service publié.

1. Connectez-vous au **portail Azure** à l’aide d’un compte disposant des droits d’administrateur d’application.

2. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**.

3. Accédez à **Applications d’entreprise**, puis, dans le ruban supérieur, sélectionnez **+ Nouvelle application**.

4. Recherchez **F5** dans la galerie, puis sélectionnez **Intégration d’Azure AD à F5 BIG-IP APM**.

5. Fournissez un nom pour l’application, puis sélectionnez **Ajouter/créer** pour l’ajouter à votre locataire. Le nom doit refléter ce service spécifique.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

1. Une fois les propriétés de la nouvelle application **F5** affichées, allez dans **Gérer** > **Authentification unique**.

2. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML** et ignorez l’invitation à enregistrer les paramètres d’authentification unique en sélectionnant **Non, j’enregistrerai plus tard**.

3. Dans le panneau **Configurer l’authentification unique avec SAML**, sélectionnez l’icône en forme de stylet pour **Configuration SAML de base** pour spécifier les détails suivants :

   a. Remplacez l’URL prédéfinie de l’**identificateur** par l’URL de votre service publié BIG-IP. Par exemple : `https://myvacation.contoso.com`

   b. Faites de même avec l’ **URL de réponse**, mais incluez le chemin d’accès du point de terminaison SAML d’APM. Par exemple : `https://myvacation.contoso.com/saml/sp/profile/post/acs`

      >[!NOTE]
      >Dans cette configuration, le flux SAML fonctionnerait en mode Lancée par le fournisseur d’identité, où Azure AD envoie à l’utilisateur une assertion SAML avant qu’il ne soit redirigé vers le point de terminaison de service BIG-IP pour l’application. BIG-IP APM prend en charge les deux modes, Lancée par le fournisseur d’identité et Lancée par le fournisseur de services.

   c. Pour `Logout URI`, entrez le point de terminaison de Single Logout (SLO) de BIG-IP APM précédé par l’en-tête de l’hôte du service en cours de publication. `Providing an SLO URI` garantit que la session BIG-IP APM de l’utilisateur se termine également après la déconnexion d’Azure AD. Par exemple : `https://myvacation.contoso.com/saml/sp/profile/redirect/slr`

     ![Capture d’écran montrant la modification de la configuration SAML de base](./media/f5-big-ip-forms-advanced/basic-saml-configuration.png)

     >[!Note]
     > Depuis TMOS v16, le point de terminaison de déconnexion unique SAML a été remplacé par /saml/sp/profile/redirect/slo.

4. Sélectionnez **Enregistrer** avant de quitter le panneau de configuration SAML et ignorez l’invite de test de l’authentification unique.

   Observez les propriétés de la section **Attributs utilisateur et revendications**, car il s’agit de ce qu’Azure AD enverra aux utilisateurs pour l’authentification BIG-IP APM et l’authentification unique à l’application back-end.

5. Dans la section **Certificat de signature SAML**, sélectionnez le bouton **Télécharger** pour enregistrer le fichier **XML de métadonnées de fédération** sur votre ordinateur.

   ![Capture d’écran du lien de téléchargement des métadonnées de fédération](./media/f5-big-ip-forms-advanced/saml-certificate.png)

   Les certificats de signature SAML créés par Azure AD ont une durée de vie de trois ans et doivent être gérés à l’aide de l’[aide publiée](manage-certificates-for-federated-single-sign-on.md).

### <a name="azure-ad-authorization"></a>Autorisation Azure AD

Par défaut, Azure AD émet uniquement des jetons pour les utilisateurs auxquels l’accès à une application a été accordé.

1. Dans la vue de la configuration de l’application, sélectionnez **Utilisateurs et groupes**.

2. Sélectionnez **+** **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans le panneau **Ajouter une attribution**.

3. Dans la boîte de dialogue **Utilisateurs et groupes**, ajoutez les groupes d’utilisateurs qui sont autorisés à accéder à l’application interne, puis choisissez **Sélectionner**\>**Attribuer**.

Cela termine la partie Azure AD de l’approbation de fédération SAML. BIG-IP APM peut maintenant être configuré pour publier l’application web interne et configuré avec un ensemble correspondant de propriétés pour terminer l’approbation de la préauthentification SAML et de l’authentification unique.

## <a name="advanced-configuration"></a>Configuration avancée

### <a name="service-provider"></a>Fournisseur de services

Ces paramètres définissent les propriétés du fournisseur de services SAML qu’APM utilisera pour superposer la préauthentification SAML à l’application héritée.

1. Sélectionnez **Access** (Accéder) > **Federation** (Fédération) > **SAML Service Provider** (Fournisseur de services SAML) > **Local SP Services** (Services de fournisseur de services locaux) > **Create** (Créer).

   ![Capture d’écran montrant la configuration des formulaires F5](./media/f5-big-ip-forms-advanced/f5-forms-configuration.png)

2. **Fournissez un nom** et le même **ID d’entité** que celui défini précédemment dans Azure AD.

   ![Capture d’écran montrant le nouveau service de fournisseur de services SAML](./media/f5-big-ip-forms-advanced/saml-sp-service.png)

    Les paramètres de nom de fournisseur de services (**SP Name Settings**) sont requis uniquement si l’ID d’entité ne correspond pas exactement à la partie du nom d’hôte de l’URL publiée, ou également s’il n’est pas au format normal d’une URL basée sur le nom d’hôte. Spécifiez le schéma externe et le nom d’hôte de l’application publiée si l’ID d’entité est `urn:myvacation:contosoonline`.

### <a name="external-idp-connector"></a>Connecteur IdP externe

Un connecteur de fournisseur d’identité SAML définit les paramètres requis pour que BIG-IP APM fasse confiance à Azure AD en tant que fournisseur d’identité SAML. Ces paramètres permettront de mapper le fournisseur de services SAML à un fournisseur d’identité SAML, établissant ainsi la confiance en la fédération entre APM et Azure AD.

1. Faites défiler l’affichage vers le bas pour sélectionner le nouvel objet de fournisseur de services SAML et sélectionnez **Bind/UnBind IDP Connectors** (Lier/dissocier les connecteurs IdP).

   ![Capture d’écran montrant les services de fournisseur de services locaux](./media/f5-big-ip-forms-advanced/local-services.png)

2. Sélectionnez **Create New IdP Connector** (Créer un connecteur IdP), puis, dans le menu déroulant, choisissez **From Metadata** (À partir des métadonnées).

   ![Capture d’écran montrant la liste déroulante From Metadata](./media/f5-big-ip-forms-advanced/from-metadata.png)
  
3. Accédez au fichier XML de métadonnées de fédération que vous avez téléchargé précédemment et spécifiez un paramètre **Identity Provider Name** (Nom de fournisseur d’identité) pour l’objet APM qui représentera le fournisseur d’identité SAML externe. Par exemple : `MyVacation\_AzureAD`

   ![Capture d’écran montrant un nouveau connecteur de fournisseur d’identité SAML](./media/f5-big-ip-forms-advanced/new-idp-saml-connector.png)

4. Sélectionnez **Add New Row** (Ajouter une nouvelle ligne) **pour choisir le nouveau connecteur IdP SAML (** SAML IdP Connector **), puis** Update (Mettre à jour).
  
   ![Capture d’écran montrant l’ajout d’une nouvelle ligne](./media/f5-big-ip-forms-advanced/add-new-row.png)

5. Sélectionnez **OK** pour enregistrer ces paramètres.

   ![Capture d’écran montrant la modification du fournisseur d’identité SAML qui utilise ce fournisseur de services](./media/f5-big-ip-forms-advanced/edit-saml-idp-using-sp.png)

### <a name="forms-based-sso"></a>Authentification unique basée sur les formulaires

L’authentification unique basée sur les formulaires (FBA) peut être effectuée en mode Lancée par le client ou par le BIG-IP lui-même. Les deux méthodes émulent la connexion de l’utilisateur en injectant des informations d’identification dans les balises de nom d’utilisateur et de mot de passe avant d’envoyer automatiquement le formulaire. Le flux est presque transparent, sauf pour les utilisateurs qui doivent fournir leur mot de passe une fois, lors de l’accès à une application FBA. Le mot de passe est ensuite mis en cache pour être réutilisé dans d’autres applications FBA.

Cela couvre l’approche APM, qui gère l’authentification unique directement auprès de l’application back-end.

Sélectionnez **Access** (Accès) > **Single Sign-on** (Authentification unique) > **Forms Based** (Basée sur les formulaires) > **Create** (Créer) et fournissez les éléments suivants :

|Propriété | Description |
|:------|:---------|
| Nom | Un objet d’authentification unique APM peut être réutilisé par d’autres applications publiées. Par conséquent, utilisez un nom descriptif pour la configuration. Par exemple, `Contoso\FBA\sso`|
| Use SSO Template (Utiliser le modèle d’authentification unique) | Aucun |
| Username Source (Source du nom d’utilisateur) | Source de nom d’utilisateur préférée pour prérenseigner le formulaire de collecte de mots de passe. N’importe quelle variable de session APM peut être utilisée, mais la valeur par défaut `session.sso.token.last.username` a tendance à être la plus efficace, car elle contient le nom d’utilisateur principal Azure AD des utilisateurs connectés |
| Password Source (Source du mot de passe) | Laissez la variable par défaut `session.sso.token.last.password`, car il s’agit de la variable APM que Big-IP utilisera pour mettre en cache le mot de passe fourni par les utilisateurs |

![Capture d’écran montrant la nouvelle configuration SSO](./media/f5-big-ip-forms-advanced/new-sso-configuration.png)

|Propriété | Description |
|:------|:---------|
| Start URI (URI de début) | URI de connexion de votre application FBA. L’authentification basée sur les formulaires d’APM exécutera l’authentification unique lorsque l’URI de requête correspondra à cette valeur d’URI.|
| Form Actions (Actions de formulaire) | Laissez ce champ vide pour que l’URL de requête originale soit utilisée pour l’authentification unique. |
| Form Parameter for Username (Paramètre de formulaire pour le nom d’utilisateur) | Nom du champ de nom d’utilisateur de vos formulaires de connexion. Utilisez les outils de développement de votre navigateur pour le déterminer.| 
| Form Parameter for Password (Paramètre de formulaire pour le mot de passe) | Nom du champ de mot de passe de vos formulaires de connexion. Idem, utilisez les outils de développement.|

![Capture d’écran montrant la configuration de la méthode de l’authentification unique](./media/f5-big-ip-forms-advanced/sso-method-configuration.png)

![Capture d’écran montrant la page de connexion My Vacation de Contoso.](./media/f5-big-ip-forms-advanced/contoso-example.png)

Plus de détails sur la configuration d’APM pour une authentification unique FBA sont accessibles [ici](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-single-sign-on-concepts-configuration-14-1-0/single-sign-on-methods.html#GUID-F8588DF4-F395-4E44-881B-8D16EED91449).

### <a name="access-profile-configuration"></a>Configuration du profil d’accès

Un profil d’accès lie de nombreux éléments APM qui gèrent l’accès aux serveurs virtuels BIG-IP, notamment les stratégies d’accès, la configuration de l’authentification unique et les paramètres de l’interface utilisateur.

1. Sélectionnez **Access** (Accéder) > **Profiles / Policies** (Profils/stratégies) > **Access Profiles (Per-Session Policies)** [Profils d’accès (stratégies par session)] > **Create** (Créer) pour fournir les éléments suivants :

   | Propriété | Description |
   |:-----|:-------|
   | Nom | Par exemple : `MyVacation` |
   |Type de profil | Tous |
   | SSO Configuration | Objet de configuration d’authentification unique FBA que vous venez de créer|
   |Accepted Language (Langue acceptée) | Ajoutez au moins une langue|

   ![Capture d’écran montrant la création d’un nouveau profil d’accès](./media/f5-big-ip-forms-advanced/create-new-access-profile.png)

2. Modifiez la stratégie de session pour présenter une page de connexion avec le nom d’utilisateur prérempli. Sélectionnez le lien **Edit** (Modifier) pour le profil par session que vous venez de créer pour lancer APM Visual Policy Editor (VPE).

   ![Capture d’écran montrant la modification de la stratégie par session](./media/f5-big-ip-forms-advanced/edit-per-session-policy.png)

3. Une fois VPE lancé, sélectionnez le signe **+** à côté de l’option Fallback (Plan de secours).

   ![Capture d’écran montrant le signe + à côté de « fallback » une fois que VPE a été lancé](./media/f5-big-ip-forms-advanced/vpe-launched.png)

4. Dans la fenêtre contextuelle, sélectionnez **Authentication** (Authentification) > **SAML Auth** (Authentification SAML) > **Add Item** (Ajouter un élément).

   ![Capture d’écran montrant SAML Auth et le bouton Add Item](./media/f5-big-ip-forms-advanced/saml-auth-add-item.png)

5. Dans la configuration **SAML authentication SP** (Fournisseur de services d’authentification SAML), remplacez le nom par **Azure AD Auth** et définissez **AAA Server** (Serveur AAA) pour qu’il utilise l’objet de fournisseur de services SAML que vous avez créé précédemment.

   ![Capture d’écran montrant les paramètres du serveur d’authentification Azure AD](./media/f5-big-ip-forms-advanced/azure-ad-auth-server.png)

6. Sélectionnez le signe **+** sur la branche **Successful** (Opération réussie).

7. Dans la fenêtre contextuelle, sélectionnez **Authentication** (Authentification) > **Logon Page** (Page d’ouverture de session) > **Add Item** (Ajouter un élément).

   ![Capture d’écran montrant les paramètres de la page d’ouverture de session](./media/f5-big-ip-forms-advanced/logon-page.png)

8. Définissez l’option **Read Only** (Lecture seule) pour le champ du nom d’utilisateur sur **Yes** (Oui).

   ![Capture d’écran montrant Read Only définie sur Yes.](./media/f5-big-ip-forms-advanced/set-read-only-as-yes.png)

9. Ajoutez un objet de mappage des informations d’identification de l’authentification unique en sélectionnant le signe **+** pour le plan de secours de la page d’ouverture de session.

10. Dans la fenêtre contextuelle, sélectionnez **Assignment** (Attribution) > **SSO Credential Mapping** (Mappage des informations d’identification de l’authentification unique)>**Add Item** (Ajouter un élément).

    ![Capture d’écran montrant les informations de mappage des informations d’identification de l’authentification unique](./media/f5-big-ip-forms-advanced/sso-credential-mapping.png)

11. Laissez les valeurs par défaut affichées dans la fenêtre contextuelle et continuez.

    ![Capture d’écran montrant le bouton Save (Enregistrer) sur la page de mappage des informations d’identification de l’authentification unique](./media/f5-big-ip-forms-advanced/save-sso-credential-mapping.png)

12. Sélectionnez le lien dans la zone **Deny** (Refuser) du haut pour définir la branche **Successful** (Opération réussie) sur **Allow** (Autoriser) > **Save** (Enregistrer).

**Mappage d’attributs**

Bien qu’elle soit facultative, l’ajout d’une configuration LogonID_Mapping permet à la liste des sessions actives du BIG-IP d’afficher le nom d’utilisateur principal de l’utilisateur connecté au lieu d’un numéro de session. Cela est utile lors de l’analyse des journaux ou de la résolution des problèmes.

1. Sélectionnez le symbole **+** de la branche **Successful** (Opération réussie) de l’authentification SAML.

2. Dans la fenêtre contextuelle, sélectionnez **Assignment** (Attribution) > **Variable Assign** (Attribuer une variable) > **Add Item** (Ajouter un élément).

   ![Capture d’écran montrant les informations de l’option Variable Assign](./media/f5-big-ip-forms-advanced/variable-assign.png)

3. Donnez un nom descriptif et, dans la section **Variable Assign** (Attribuer une variable), sélectionnez **Add new entry** (Ajouter une nouvelle entrée) > **change** (modifier). Par exemple : `LogonID_Mapping`.

   ![Capture d’écran montrant le champ Add new entry](./media/f5-big-ip-forms-advanced/add-new-entry.png)

4. Configurez les deux variables pour utiliser les éléments suivants :

   | Propriété | Description |
   |:-----|:-------|
   | Custom Variable (Variable personnalisée) | `session.logon.last.username` |
   | Session Variable (Variable de session) | `session.saml.last.identity`|

   Ensuite, sélectionnez **Finished** (Terminé) > **Save** (Enregistrer).

5. Validez ces paramètres en sélectionnant **Apply Access Policy** (Appliquer la stratégie d’accès) dans le coin supérieur gauche et fermez l’onglet de l’éditeur de stratégie visuelle.

   ![Capture d’écran montrant l’application de la stratégie d’accès](./media/f5-big-ip-forms-advanced/apply-access-policy.png)

### <a name="backend-pool-configuration"></a>Configuration du pool de back-ends

Pour que le BIG-IP sache où transférer le trafic client, vous devez créer un objet de nœud BIG-IP représentant le serveur principal hébergeant votre application. Ensuite, placez ce nœud dans un pool de serveurs BIG-IP.

1. Sélectionnez **Local Traffic** (Traffic local) > **Pools** > **Pool List** (Liste de pools) > **Create** (Créer) et donnez un nom à l’objet de pool de serveurs. Par exemple : `MyApps_VMs`.

   ![Capture d’écran montrant la liste de pools](./media/f5-big-ip-forms-advanced/pool-list.png)

2. Ajoutez un objet de membre de pool avec les éléments suivants :

   | Propriété | Description |
   |:-----|:-------|
   | Node Name | Nom d’affichage facultatif du serveur hébergeant l’application web back-end |
   | Adresse | Adresse IP du serveur hébergeant l’application |
   | Service Port | Port HTTP/S sur lequel l’application écoute |

   ![Capture d’écran montrant le membre du pool](./media/f5-big-ip-forms-advanced/pool-member.png)

>[!NOTE]
>Les moniteurs d’intégrité nécessitent une [configuration](https://support.f5.com/csp/article/K13397) supplémentaire qui n’est pas abordée dans ce tutoriel.

## <a name="virtual-server-configuration"></a>Configuration du serveur virtuel

Un serveur virtuel est un objet de plan de données BIG-IP représenté par une adresse IP virtuelle écoutant les requêtes des clients adressées à l’application. Tout trafic reçu est traité et évalué par rapport au profil d’accès APM associé au serveur virtuel avant d’être dirigé en fonction des résultats et des paramètres de la stratégie.

1. Sélectionnez **Local Traffic** (Trafic local) > **Virtual Servers** (Serveurs virtuels) > **Virtual Server List** (Liste de serveurs virtuels) > **Create** (Créer).

2. Donnez au serveur virtuel un **nom**, une adresse IPv4/IPv6 inutilisée qui peut être attribuée au BIG-IP pour recevoir le trafic client et définissez le **port de service** sur 443.

   ![Capture d’écran montrant le serveur virtuel](./media/f5-big-ip-forms-advanced/virtual-server.png)

3. **HTTP Profile** (Profil HTTP) : à définir sur http.

4. **SSL Profile (Client)** [Profil SSL (Client)] : active le protocole TLS (Transport Layer Security), ce qui permet la publication des services sur HTTPS. Sélectionnez le profil SSL client que vous avez créé dans le cadre des prérequis ou laissez la valeur par défaut si vous effectuez des tests.

   ![Capture d’écran montrant le profil SSL](./media/f5-big-ip-forms-advanced/ssl-profile.png)

5. Définissez la propriété **Source Address Translation** (Traduction d’adresses sources) sur **Auto Map** (Mappage automatique).

   ![Capture d’écran montrant Auto Map](./media/f5-big-ip-forms-advanced/auto-map.png)

6. Sous **Access Policy** (Stratégie d’accès), définissez le **profil d’accès** créé précédemment. Cela permet de lier le profil de préauthentification SAML d’Azure AD et la stratégie d’authentification unique FBA au serveur virtuel.

   ![Capture d’écran montrant la stratégie d’accès](./media/f5-big-ip-forms-advanced/access-policy.png)

7. Enfin, définissez **Default Pool** (Pool par défaut) sur l’objet de pool principal créé dans la section précédente.

   ![Capture d’écran montrant le pool par défaut](./media/f5-big-ip-forms-advanced/default-pool.png)

## <a name="session-management"></a>Gestion des sessions

Les paramètres de gestion d’une session BIG-IP servent à définir les conditions dans lesquelles les sessions utilisateur sont terminées ou autorisées à poursuivre, les limites des utilisateurs et des adresses IP, ainsi que les pages d’erreur. Vous pouvez créer votre propre stratégie en allant dans **Access Policy** (Stratégie d’accès) > **Access Profiles** (Profils d’accès) et en sélectionnant votre application dans la liste.

En ce qui concerne la fonctionnalité SLO, la définition d’un URI de Single Logout dans Azure AD garantit qu’une déconnexion initiée par un fournisseur d’identité IdP à partir du portail MyApps met également fin à la session entre le client et BIG-IP APM.

L’importation du fichier XML de métadonnées de fédération de l’application fournit au service APM le point de terminaison SLO (SLO) SAML d’Azure AD pour les déconnexions initiées par le fournisseur de services. Mais pour que cela soit vraiment efficace, APM doit savoir exactement quand un utilisateur se déconnecte.

Dans le cas où le portail web du BIG-IP n’est pas utilisé, l’utilisateur n’a aucun moyen de demander à APM de se déconnecter. Même si l’utilisateur se déconnecte de l’application elle-même, le BIG-IP n’en est techniquement pas conscient et la session de l’application peut facilement être rétablie par le biais de l’authentification unique. Pour cette raison, la déconnexion initiée par le fournisseur de services doit être soigneusement étudiée afin de s’assurer que les sessions sont terminées de manière sécurisée lorsqu’elles ne sont plus nécessaires.

Pour cela, vous pouvez ajouter une fonction SLO au bouton de déconnexion de vos applications afin qu’il puisse rediriger votre client vers le point de terminaison de déconnexion SAML d’Azure AD. Le point de terminaison de déconnexion SAML de votre locataire se trouve dans points de terminaison **Inscriptions d’applications** > **Points de terminaison**.

Si vous ne pouvez pas modifier l’application, vous pouvez demander au BIG-IP d’écouter l’appel de déconnexion des applications et de déclencher le Single Logout dès qu’il détecte la demande. Pour plus de détails sur l’utilisation de BIG-IP iRules à cette fin, consultez l’[article K42052145](https://support.f5.com/csp/article/K42052145) et l’[article K12056](https://support.f5.com/csp/article/K12056) de F5.

## <a name="summary"></a>Résumé

Votre application doit maintenant être publiée et accessible via un accès hybride sécurisé, soit directement via son URL, soit via les portails d’application de Microsoft.

L’application doit également être visible en tant que ressource cible dans l’accès conditionnel Azure AD. Consultez l’[aide](../conditional-access/concept-conditional-access-policies.md) pour créer des stratégies d’accès conditionnel.

Pour une sécurité accrue, les entreprises qui utilisent ce modèle pourraient également envisager de bloquer tout accès direct à l’application, forçant ainsi un chemin strict via BIG-IP.

## <a name="next-steps"></a>Étapes suivantes

À partir d’un navigateur, connectez-vous à l’URL externe de l’application ou sélectionnez l’icône de l’application dans le portail MyApps. Après vous être authentifié auprès d’Azure AD, vous êtes redirigé vers le point de terminaison BIG-IP de l’application et invité à entrer un mot de passe. Notez que le service APM préremplit le nom d’utilisateur avec le nom d’utilisateur principal d’Azure AD. Le nom d’utilisateur prérempli par le service APM est en lecture seule pour garantir la cohérence de session entre Azure AD et l’application principale. Ce champ peut être masqué dans une vue avec une configuration supplémentaire, si nécessaire.

![Capture d’écran montrant l’authentification unique sécurisée](./media/f5-big-ip-forms-advanced/secured-sso.png)

Après l’envoi, l’utilisateur doit être automatiquement connecté à l’application.

![Capture d’écran montrant le message de bienvenue](./media/f5-big-ip-forms-advanced/welcome-message.png)

## <a name="troubleshoot"></a>Résoudre les problèmes

L’impossibilité d’accéder à l’application protégée par l’accès hybride sécurisé peut être due à un certain nombre de facteurs, notamment une mauvaise configuration. Voici quelques éléments à prendre en compte lors de la résolution des problèmes :

- L’authentification unique FBA est effectuée par le BIG-IP qui analyse le formulaire de connexion au niveau de l’URI spécifié et recherche les balises d’élément de nom d’utilisateur et de mot de passe définies dans votre configuration.

- Les balises d’éléments doivent être cohérentes, sinon l’authentification unique échouera. Les formulaires plus complexes générés dynamiquement peuvent nécessiter une analyse plus approfondie à l’aide d’outils de développement pour comprendre la composition du formulaire de connexion.

- L’approche initiée par le client peut être mieux adaptée aux pages de connexion contenant plusieurs formulaires, car elle permet de spécifier un nom de formulaire et même de personnaliser la logique du programme de traitement de formulaire JavaScript.

- Les deux méthodes d’authentification unique FBA optimisent l’expérience utilisateur et la sécurité en masquant toutes les interactions de formulaire, mais, dans certains cas, il peut être utile de vérifier si les informations d’identification sont réellement injectées. Cela est possible en mode Lancée par le client en désactivant l’envoi automatique du formulaire dans votre profil d’authentification unique, puis en utilisant les outils de développement pour désactiver les deux propriétés de style empêchant l’affichage de la page de connexion.

  ![Capture d’écran montrant les propriétés](./media/f5-big-ip-forms-advanced/properties.png)

Les journaux du BIG-IP constituent une source d’informations précieuse pour isoler toutes sortes de problèmes liés à l’authentification et à l’authentification unique. Lors de la résolution des problèmes, vous devez augmenter le niveau de verbosité des journaux.

1. Accédez à **Access Policy (Stratégie d’accès)**  > **Overview (Vue d’ensemble)**  > **Event Logs (Journaux des événements)**  > **Settings (Paramètres)** .

2. Sélectionnez la ligne correspondant à votre application publiée, puis **Edit (Modifiez)**  > **Access System Logs (Journaux système d’accès)** .

3. Sélectionnez **Debug** (Déboguer) dans la liste d’authentification unique, puis **OK**. Reproduisez ensuite le problème avant de consulter les journaux, mais n’oubliez pas de revenir en arrière lorsque vous avez terminé.

Si vous voyez une erreur marquée BIG-IP immédiatement après avoir réussi la préauthentification Azure AD, le problème est peut-être lié à l’authentification unique entre Azure AD et le BIG-IP.

Accédez à **Access** (Accès) > **Overview** (Vue d’ensemble) > **Access reports** (Rapports d’accès) et exécutez le rapport relatif à la dernière heure afin de voir si les journaux fournissent des indices. Le lien **View session variables** (Afficher les variables de session) pour votre session aidera également à comprendre si APM reçoit les revendications attendues d’Azure AD.

Si vous ne voyez pas de page d’erreur BIG-IP, le problème est probablement davantage lié à la demande principale ou à l’authentification unique entre le BIG-IP et l’application. Dans ce cas, allez dans **Access Policy** (Stratégie d’accès) > **Overview** (Vue d’ensemble) > **Active Sessions** (Sessions actives) et sélectionnez le lien correspondant à votre session active.

Le lien **View Variables** (Afficher les variables) à cet endroit peut également aider à déterminer la cause racine, en particulier si APM ne parvient pas à obtenir l’identifiant utilisateur et le mot de passe.

Pour plus d’informations, consultez la [référence sur les variables de session](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html) du BIG-IP de F5.
