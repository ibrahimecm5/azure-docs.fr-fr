---
title: Intégration de l’authentification unique Azure Active Directory à F5 BIG-IP pour l’authentification basée sur un en-tête
description: Découvrez comment intégrer F5 BIG-IP Access Policy Manager (APM) et l’authentification unique Azure Active Directory pour l’authentification basée sur un en-tête
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/10/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e66556e7233779d9fa7f9e2746839147cea42d54
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350454"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-with-f5-big-ip-for-header-based-authentication"></a>Tutoriel : Intégrer l’authentification unique Azure Active Directory à F5 BIG-IP pour l’authentification unique basée sur un en-tête

Dans ce tutoriel, vous apprendrez à intégrer BIG-IP Access Policy Manager (APM) de F5 et Azure Active Directory (Azure AD) pour obtenir un accès hybride sécurisé aux applications basées sur un en-tête.

L’intégration des applications publiées de BIG-IP avec Azure AD offre de nombreux avantages, notamment :

- Gouvernance à confiance zéro améliorée via la pré-authentification et l’autorisation Azure AD

- Authentification unique (SSO) complète entre Azure AD et les services publiés de BIG IP

- Gestion des identités et de l’accès à partir d’un plan de contrôle unique – le [portail Azure](https://azure.microsoft.com/features/azure-portal)

Pour en savoir plus sur tous les avantages, consultez les articles sur l’[intégration de F5 BIG-IP et Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration) et la [présentation de l’accès aux applications et de l’authentification unique avec Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="scenario-description"></a>Description du scénario

Pour ce scénario, nous disposons d’une application interne dont l’accès repose sur la réception d’en-têtes d’autorisation HTTP d’un système de répartiteur hérité. Cela permet aux utilisateurs d’être dirigés vers leurs zones de contenu respectives.

Le scénario idéal consiste à faire en sorte que l’application soit gérée et régie directement par Azure AD. Toutefois, dans la mesure où il n’existe aucune forme d’interopérabilité de protocole moderne, sa modernisation nécessiterait des efforts et un temps considérables, ce qui entraînerait des coûts inévitables et des risques d’interruption de service.

Au lieu de cela, une édition virtuelle BIG-IP Virtual Edition (VE) déployée entre l’Internet public et le réseau virtuel Azure interne auquel l’application est connectée sera utilisée. Elle permettra de contrôler l’accès entrant, avec Azure AD pour son large choix de fonctionnalités d’authentification et d’autorisation.

Le fait de disposer d’un BIG-IP devant l’application permet de superposer le service avec l’authentification préalable Azure AD et l’authentification unique basée sur l’en-tête. Cela améliore de manière significative la position globale de sécurité de l’application, ce qui permet à l’entreprise de continuer à fonctionner à son rythme, sans interruption.

La solution d’accès hybride sécurisé pour ce scénario est constituée des éléments suivants :

- **Application** : service principal à protéger par l’accès hybride sécurisé Azure AD et BIG-IP.

- **Azure AD** : Fournisseur d’identité SAML (IdP) responsable de la vérification des informations d’identification de l’utilisateur, de l’accès conditionnel et de l’authentification unique à BIG-IP APM.

- **BIG-IP** : proxy inverse et fournisseur de services SAML pour l’application, déléguant l’authentification au fournisseur d’identité SAML avant d’effectuer une authentification unique basée sur les en-têtes auprès de l’application back-end.

![Capture d’écran montrant le diagramme du flux de l’architecture](./media/f5-big-ip-header-advanced/flow-diagram.png)

| Étape | Description |
|:-------|:-----------|
| 1. | L’utilisateur se connecte au point de terminaison du fournisseur de services SAML de l’application (BIG-IP APM). |
| 2. | La stratégie d’accès d’APM redirige l’utilisateur vers l’IdP SAML (Azure AD) pour une préauthentification.|
| 3. | L’IdP SAML authentifie l’utilisateur et applique les stratégies d’accès conditionnel en vigueur. |
| 4. | Azure AD redirige l’utilisateur vers le fournisseur de services SAML avec le jeton et les revendications émis. |
| 5. | BIG-IP APM accorde un accès utilisateur et injecte des en-têtes dans la demande à l’application. |

Pour une sécurité accrue, les entreprises qui utilisent ce modèle pourraient également envisager de bloquer tout accès direct à l’application, forçant ainsi un chemin strict via BIG-IP.

## <a name="prerequisites"></a>Prérequis

Une expérience préalable de BIG-IP n’est pas nécessaire, mais vous aurez besoin des éléments suivants :

- Un abonnement gratuit Azure AD ou plus

- Un BIG-IP existant ou [déploiement d’une BIG-IP Virtual Edition (VE) dans Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide)

- L’une des références SKU de licence F5 BIG-IP suivantes

  - F5 BIG-IP® Best bundle

  - Licence autonome F5 BIG-IP Access Policy Manager™ (APM)

  - Licence de composant additionnel F5 BIG-IP Access Policy Manager™ (APM) sur une instance existante de BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)

  - [Licence d’essai](https://www.f5.com/trial/big-ip-trial.php) de 90 jours des fonctionnalités complètes BIG-IP.

- [Synchronisation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) des identités des utilisateurs entre un annuaire local et Azure AD

- Un compte disposant des [autorisations](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) d’administrateur d’application Azure AD

- Un [certificat SSL](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-bigip-deployment-guide#ssl-profile) pour les services de publication sur HTTPS ou l’utilisation de certificats par défaut lors des tests

- Une application existante basée sur l’en-tête ou la [configuration d’une simple application IIS basée sur l’en-tête](https://docs.microsoft.com/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) à des fins de test

## <a name="deployment-modes"></a>Modes de déploiement

Il existe plusieurs méthodes pour configurer un BIG-IP pour ce scénario, notamment deux options basées sur un Assistant ou une configuration avancée.

Ce tutoriel décrit l’approche avancée, qui offre une méthode plus flexible pour implémenter un accès hybride sécurisé en créant manuellement tous les objets de configuration BIG-IP. Vous pouvez également utiliser cette approche pour les scénarios non couverts par la configuration guidée.

>[!NOTE]
>Tous les exemples de chaînes et de valeurs référencés dans cet article doivent être remplacés par ceux de votre environnement réel.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Ajout de F5 BIG-IP depuis la galerie Azure AD

Configurer une approbation de fédération SAML entre BIG-IP APM et Azure AD est l’une des premières étapes de l’implémentation d’un accès hybride sécurisé. Elle établit l’intégration requise pour que le BIG-IP transmette la pré-authentification et l’[accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) à Azure AD avant d’accorder l’accès au service publié.

1. Connectez-vous au portail Azure à l’aide d’un compte disposant des droits d’administrateur d’application.

2. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**.

3. Accédez à **Applications d’entreprise**, puis, dans le ruban supérieur, sélectionnez **+ Nouvelle application**.

4. Recherchez **F5** dans la galerie, puis sélectionnez **Intégration d’Azure AD à F5 BIG-IP APM**.

5. Fournissez un nom pour l’application, puis sélectionnez **Ajouter/créer** pour l’ajouter à votre locataire. Le nom doit refléter ce service spécifique.

## <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD 

1. Une fois les propriétés de la nouvelle application **F5** affichées, allez dans **Gérer**>**Authentification unique**.

2. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML** et ignorez l’invitation à enregistrer les paramètres d’authentification unique en sélectionnant **Non, j’enregistrerai plus tard**.

3. Dans le panneau **Configurer l’authentification unique avec SAML**, sélectionnez l’icône en forme de stylet pour **Configuration SAML de base** pour spécifier les détails suivants :

   a. Remplacez l’URL prédéfinie de l’**identificateur** par l’URL de votre service publié BIG-IP. Par exemple : `https://mytravel.contoso.com`

   b. Faites de même avec l’**URL de réponse**, mais incluez le chemin d’accès du point de terminaison SAML d’APM. Par exemple : `https://mytravel.contoso.com/saml/sp/profile/post/acs`

   >[!NOTE]
   >Dans cette configuration, le flux SAML fonctionnerait en mode Lancée par le fournisseur d’identité, où Azure AD envoie à l’utilisateur une assertion SAML avant qu’il ne soit redirigé vers le point de terminaison de service BIG-IP pour l’application. BIG-IP APM prend en charge les deux modes, Lancée par le fournisseur d’identité et Lancée par le fournisseur de services.

   c. Pour `Logout URI`, entrez le point de terminaison de Single Logout (SLO) de BIG-IP APM précédé par l’en-tête de l’hôte du service en cours de publication. Le fait de fournir un URI SLO garantit que la session BIG-IP APM de l’utilisateur se termine après sa déconnexion d’Azure AD. Par exemple : `https://mytravel.contoso.com/saml/sp/profile/redirect/slr`

    ![Capture d’écran montrant la configuration SAML de base](./media/f5-big-ip-header-advanced/basic-saml-configuration.png)

    >[!Note]
    >À partir de TMOS v16, le point de terminaison SLO SAML devient `/saml/sp/profile/redirect/slo`.

4. Sélectionnez **Enregistrer** avant de quitter le panneau de configuration SAML et ignorez l’invite de test de l’authentification unique.

5. Sélectionnez l’icône de stylet pour modifier **Attributs utilisateur et revendications > + Ajouter une nouvelle revendication**.

6. Définissez les propriétés de revendication avec les éléments suivants, puis sélectionnez **Enregistrer**.

   | Propriété |Description|
   |:------|:---------|
   |Name | Employeeid |
   | Attribut source | user.employeeid |

   ![Capture d’écran montrant la gestion de la configuration des revendications](./media/f5-big-ip-header-advanced/manage-claims.png)

7. Sélectionnez **+ Ajouter une revendication de groupe** et sélectionnez **Groupes affectés à l’application** > **Attribut source** > **sAMAccountName**.

   ![Capture d’écran montrant la configuration des revendications de groupe](./media/f5-big-ip-header-advanced/group-claims.png)

8. **Enregistrez** la configuration et fermez le volet.

   Observez les propriétés de la section **Attributs utilisateur et revendications**. Azure AD émettra vers les utilisateurs ces propriétés pour l’authentification BIG-IP APM et l’authentification unique sur l’application backend :

   ![Capture d’écran présentant la configuration des attributs utilisateur et des revendications](./media/f5-big-ip-header-advanced/user-attributes-claims.png)

   N’hésitez pas à ajouter d’autres revendications spécifiques que votre application publiée BIG-IP peut attendre comme en-têtes. Les revendications définies en plus de l’ensemble par défaut sont émises uniquement si elles existent dans Azure AD. De la même façon, les appartenances à un [groupe ou rôle](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) d’annuaire doivent également être définies par rapport à un objet utilisateur dans Azure AD avant de pouvoir être émises en tant que revendication.

9. Dans la section **Certificat de signature SAML**, sélectionnez le bouton **Télécharger** pour enregistrer le fichier **XML de métadonnées de fédération** sur votre ordinateur.

   ![Capture d’écran montrant le certificat de signature SAML](./media/f5-big-ip-header-advanced/saml-signing-certificate.png)

Les certificats de signature SAML créés par Azure AD ont une durée de vie de trois ans et doivent être gérés au moyen de l’[aide publiée](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="azure-ad-authorization"></a>Autorisation Azure AD

Par défaut, Azure AD émet uniquement des jetons pour les utilisateurs auxquels l’accès à une application a été accordé.

1. Dans la vue de la configuration de l’application, sélectionnez **Utilisateurs et groupes**.

2. Sélectionnez **+** **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans le panneau **Ajouter une attribution**.

3. Dans la boîte de dialogue **Utilisateurs et groupes**, ajoutez les groupes d’utilisateurs qui sont autorisés à accéder à l’application interne basée sur un en-tête, puis choisissez **Sélectionner** > **Attribuer**.

Cela termine la partie Azure AD de l’approbation de fédération SAML. BIG-IP APM peut maintenant être configuré pour publier l’application web interne et configuré avec un ensemble correspondant de propriétés pour terminer l’approbation de la préauthentification SAML.

## <a name="advanced-configuration"></a>Configuration avancée

### <a name="saml-configuration"></a>SAML Configuration

Les étapes suivantes créent le fournisseur de services BIG-IP SAML et les objets de fournisseur d’identité SAML correspondants requis pour effectuer la fédération de l’application publiée, avec Azure AD.

1. Sélectionnez **Access** (Accéder) > **Federation** (Fédération) > **SAML Service Provider** (Fournisseur de services SAML) > **Local SP Services** (Services de fournisseur de services locaux) > **Create** (Créer).

   ![Capture d’écran montrant la création du fournisseur de services SAML](./media/f5-big-ip-header-advanced/create-saml-sp.png)

2. **Fournissez un nom** et le même **ID d’entité** que celui défini précédemment dans Azure AD.

   ![Capture d’écran montrant le nouveau fournisseur de services SAML ](./media/f5-big-ip-header-advanced/new-saml-sp-information.png)

   Les paramètres de nom de fournisseur de services (**SP Name Settings**) sont requis uniquement si l’ID d’entité ne correspond pas exactement à la partie du nom d’hôte de l’URL publiée, ou également s’il n’est pas au format normal d’une URL basée sur le nom d’hôte. Spécifiez le schéma externe et le nom d’hôte de l’application publiée si l’ID d’entité est `urn:mytravel:contosoonline`.

3. Faites défiler l’affichage vers le bas pour sélectionner le nouvel objet SP SAML et sélectionnez **Bind/UnBind IDP Connectors** (Lier/dissocier les connecteurs IDP).

   ![Capture d’écran montrant les nouveaux connecteurs d’objets de fournisseur de services SAML](./media/f5-big-ip-header-advanced/idp-connectors.png)

4. Sélectionnez **Create New IdP Connector** (Créer un connecteur IdP), puis, dans le menu déroulant, choisissez **From Metadata** (À partir des métadonnées).

   ![Capture d’écran de la modification d’un nouveau fournisseur d’identité SAML](./media/f5-big-ip-header-advanced/edit-saml-idp.png)

5. Accédez au fichier XML de métadonnées de fédération que vous avez téléchargé précédemment et spécifiez un paramètre **Identity Provider Name** (Nom de fournisseur d’identité) pour l’objet APM qui représentera le fournisseur d’identité SAML externe. Par exemple : `MyTravel_AzureAD`

   ![Capture d’écran montrant un nouveau connecteur de fournisseur d’identité](./media/f5-big-ip-header-advanced/idp-name.png)

6. Sélectionnez **Add New Row** (Ajouter une nouvelle ligne) **pour choisir le nouveau connecteur IdP SAML (** SAML IdP Connector **), puis** Update (Mettre à jour).

   ![Capture d’écran montrant comment mettre à jour le connecteur de fournisseur d’identité](./media/f5-big-ip-header-advanced/update-idp-connector.png)

7. Sélectionnez **OK** pour enregistrer les paramètres.

   ![Capture d’écran montrant l’enregistrement des paramètres](./media/f5-big-ip-header-advanced/save-settings.png)

### <a name="header-sso-configuration"></a>Configuration de l’authentification unique basée sur l’en-tête

Créez un objet d’authentification unique APM pour effectuer une authentification unique basée sur un en-tête pour l’application backend.

1. Sélectionnez **Access (Accès)**  > **Profiles/Policies (Profils/Stratégies)**  > **Per-Request Policies (Stratégies par demande)**  > **Create (Créer)** .

2. Donnez un nom unique au profil et ajoutez au moins une **langue acceptée**, puis sélectionnez **Terminé**. Par exemple, SSO_Headers

   ![Capture d’écran montrant la configuration d’en-tête](./media/f5-big-ip-header-advanced/header-configuration.png)

3. Sélectionnez le lien **Modifier** pour la nouvelle stratégie par demande que vous venez de créer.

    ![Capture d’écran montrant la modification de la stratégie par demande](./media/f5-big-ip-header-advanced/header-configuration-edit.png)

4. Une fois l’éditeur de stratégie visuel lancé, sélectionnez le symbole **+** en regard de « fallback ».

    ![Capture d’écran montrant l’éditeur de stratégie visuel](./media/f5-big-ip-header-advanced/visual-policy-editor.png)

5. Dans le menu contextuel, accédez à l’onglet **General Purpose (Usage général)** pour sélectionner **HTTP Headers (En-têtes HTTP)**  > **Add Item (Ajouter un élément)** .

    ![Capture d’écran montrant l’ajout d’un élément d’en-tête Http](./media/f5-big-ip-header-advanced/add-item.png)

6. Sélectionnez **Add new entry (Ajouter une nouvelle entrée)** pour créer 3 entrées **HTTP** **Header modify** distinctes à l’aide des éléments suivants :

   | Propriété | Description |
   |:------|:-------|
   | Nom de l’en-tête | upn |
   | Valeur de l’en-tête | %{session.saml.last.identity}|
   | Nom de l’en-tête | employeeid |
   | Valeur de l’en-tête | %{session.saml.last.attr.name.employeeid} |
   | Nom de l’en-tête | group\_authz |
   | Valeur de l’en-tête | %{session.saml.last.attr.name.`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`} |

   >[!Note]
   >Les variables de session APM définies entre accolades respectent la casse. Ainsi, si vous saisissez « EmployeeID » alors que le nom de l’attribut Azure AD est envoyé sous la forme « employeeid », le mappage de l’attribut échouera. Sauf nécessité, nous vous recommandons de définir tous les attributs en minuscules.

   ![Capture d’écran montrant la modification d’un élément d’en-tête Http](./media/f5-big-ip-header-advanced/http-header-modify.png)

7. Lorsque vous avez terminé, sélectionnez **Enregistrer**, puis fermez l’éditeur de stratégie visuel.

   ![Capture d’écran montrant la stratégie par demande terminée et enregistrée](./media/f5-big-ip-header-advanced/per-request-policy-done.png)

### <a name="access-profile-configuration"></a>Configuration du profil d’accès

Un profil d’accès lie de nombreux éléments APM qui gèrent l’accès aux serveurs virtuels BIG-IP, notamment les stratégies d’accès, la configuration de l’authentification unique et les paramètres de l’interface utilisateur.

1. Sélectionnez **Access** (Accéder) > **Profiles / Policies** (Profils/stratégies) > **Access Profiles (Per-Session Policies)** [Profils d’accès (stratégies par session)] > **Create** (Créer) pour fournir les éléments suivants, puis sélectionnez **Terminé** :

   | Propriété | Description |
   |:--------|:----------|
   | Name | MyTravel |
   | Type de profil | Tous |
   | Accepted Language (Langue acceptée) | Ajoutez au moins une langue|

   ![Capture d’écran montrant la configuration du profil d’accès](./media/f5-big-ip-header-advanced/access-profile-configuration.png)

2. Sélectionnez le lien **Edit** (Modifier) pour le profil par session que vous venez de créer.

    ![Capture d’écran montrant la modification du profil par session](./media/f5-big-ip-header-advanced/edit-per-session-profile.png)

3. Une fois l’éditeur de stratégie visuel lancé, sélectionnez le symbole **+** en regard de « fallback ».

   ![Capture d’écran montrant comment lancer l’éditeur de stratégie visuel](./media/f5-big-ip-header-advanced/visual-policy-editor-launch.png)

4. Dans la fenêtre contextuelle, sélectionnez **Authentication** (Authentification) > **SAML Auth** (Authentification SAML) > **Add Item** (Ajouter un élément).

   ![Capture d’écran illustrant l’ajout de l’authentification SAML](./media/f5-big-ip-header-advanced/add-saml-auth.png)

5. Pour la configuration du **fournisseur de service d’authentification SAML**, définissez l’option **Serveur AAA** pour utiliser l’objet SP SAML que vous avez créé précédemment, puis cliquez sur **Enregistrer**.

   ![Capture d’écran montrant l’utilisation du serveur AAA pour l’authentification SAML SP](./media/f5-big-ip-header-advanced/aaa-server.png)

### <a name="attribute-mapping"></a>Mappage d’attributs

Bien qu’elle soit facultative, l’ajout d’une configuration LogonID_Mapping permet à la liste des sessions actives du BIG-IP d’afficher le nom d’utilisateur principal de l’utilisateur connecté au lieu d’un numéro de session. Cela est utile lors de l’analyse des journaux ou de la résolution des problèmes.

1. Sélectionnez le symbole **+** de la branche **Successful** (Opération réussie) de l’authentification SAML.

    ![Capture d’écran montrant comment créer une branche d’authentification SAML](./media/f5-big-ip-header-advanced/create-saml-auth-branch.png)

2. Dans la fenêtre contextuelle, sélectionnez **Assignment** (Attribution) > **Variable Assign** (Attribuer une variable) > **Add Item** (Ajouter un élément).

   ![Capture d’écran montre comment assigner une variable](./media/f5-big-ip-header-advanced/assign-variable.png)

3. Donnez un nom descriptif et, dans la section **Variable Assign** (Attribuer une variable), sélectionnez **Add new entry** (Ajouter une nouvelle entrée) > **change** (modifier). Par exemple, LogonID_Mapping.

   ![Capture d’écran montrant comment ajouter une nouvelle entrée](./media/f5-big-ip-header-advanced/assign-variable-change.png)

4. Configurez les deux variables pour utiliser les éléments suivants, puis cliquez sur **Terminé** >
    **Enregistrer**.

   | Propriété | Description |
   |:--------|:----------|
   | Custom Variable (Variable personnalisée) | session.saml.last.identity |
   | Session Variable (Variable de session) | session.logon.last.username |

5. Sélectionnez le terminal **Deny (Refuser)** de la branche **Successful (Réussite)** de la stratégie d’accès et remplacez-la par **Allow (Autoriser)** , puis cliquez sur **Enregistrer**.

6. Validez la stratégie en sélectionnant **Apply Access Policy** (Appliquer la stratégie d’accès) et fermez l’onglet de l’éditeur de stratégie visuelle.

### <a name="backend-pool-configuration"></a>Configuration du pool de back-ends

Pour que le BIG-IP sache où transférer le trafic client, vous devez créer un objet de nœud APM représentant le serveur backend hébergeant votre application, et placer ce nœud dans un pool APM.

1. Sélectionnez **Local Traffic (Traffic local) > Pools > Pool List (Liste de pools) > Create (Créer)** et donnez un nom à l’objet de pool de serveurs. Par exemple, MyApps_VMs

      ![Capture d’écran montrant comment appliquer la stratégie d’accès](./media/f5-big-ip-header-advanced/apply-access-policy.png)

2. Ajoutez un objet de membre de pool avec les éléments suivants :

    | Propriété | Description |
    |:--------|:----------|
    | Node Name | Nom d’affichage facultatif du serveur hébergeant l’application web back-end |
    | Adresse | Adresse IP du serveur hébergeant l’application|
    | Service Port | Port HTTP/S sur lequel l’application écoute |

    ![Capture d’écran montrant comment ajouter un objet de membre de pool](./media/f5-big-ip-header-advanced/add-object.png)

>[!NOTE]
>Les moniteurs d’intégrité nécessitent une [configuration](https://support.f5.com/csp/article/K13397) supplémentaire qui n’est pas abordée dans ce tutoriel.

## <a name="virtual-server-configuration"></a>Configuration du serveur virtuel

Un serveur virtuel est un objet de plan de données BIG-IP représenté par une adresse IP virtuelle écoutant les requêtes des clients adressées à l’application. Tout trafic reçu est traité et évalué par rapport au profil d’accès APM associé au serveur virtuel avant d’être dirigé en fonction des résultats et des paramètres de la stratégie.

1. Sélectionnez **Local Traffic** (Trafic local) > **Virtual Servers** (Serveurs virtuels) > **Virtual Server List** (Liste de serveurs virtuels) > **Create** (Créer).

2. Donnez au serveur virtuel un **nom**, une adresse IPv4/IPv6 inutilisée qui peut être attribuée au BIG-IP pour recevoir le trafic client et définissez le **port de service** sur 443.

   ![Capture d’écran montrant comment ajouter un nouveau serveur virtuel](./media/f5-big-ip-header-advanced/new-virtual-server.png)

3. **HTTP Profile** (Profil HTTP) : à définir sur http.

4. **SSL Profile (Client)** [Profil SSL (Client)] : active le protocole TLS (Transport Layer Security), ce qui permet la publication des services sur HTTPS. Sélectionnez le profil SSL client que vous avez créé dans le cadre des prérequis ou laissez la valeur par défaut si vous effectuez des tests.

   ![Capture d’écran montrant le client de profil SSL](./media/f5-big-ip-header-advanced/ssl-profile.png)

5. Définissez l’option **Source Address Translation** (Traduction d’adresses sources) sur **Auto Map** (Mappage automatique).

   ![Capture d’écran montrant l’option de mappage automatique](./media/f5-big-ip-header-advanced/change-source-address.png)

6. Sous **Access Policy** (Stratégie d’accès), définissez le **profil d’accès** créé précédemment. Cela permet de lier le profil de préauthentification SAML d’Azure AD et la stratégie d’authentification unique basée sur un en-tête au serveur virtuel.

   ![Capture d’écran montrant comment définir le profil d’accès](./media/f5-big-ip-header-advanced/set-access-profile.png)

7. Enfin, définissez **Default Pool** (Pool par défaut) pour qu’il utilise les objets de pool backend créés dans la section précédente, puis sélectionnez **Terminé**.

   ![Capture d’écran montrant comment définir le pool par défaut](./media/f5-big-ip-header-advanced/default-pool.png)

## <a name="session-management"></a>Gestion des sessions

Un paramètre de gestion de session BIG-IP est utilisé pour définir les conditions dans lesquelles les sessions utilisateur sont terminées ou autorisées à se poursuivre, les limites pour les utilisateurs et les adresses IP, ainsi que les pages d’erreur. Vous pouvez créer votre propre stratégie en allant dans **Access Policy** (Stratégie d’accès) > **Access Profiles** (Profils d’accès) et en sélectionnant votre application dans la liste.

En ce qui concerne la fonctionnalité SLO, la définition d’un URI SLO dans Azure AD garantit qu’une déconnexion initiée par un fournisseur d’identité à partir du portail MyApps met également fin à la session entre le client et BIG-IP APM. L’importation du fichier XML de métadonnées de fédération de l’application fournit à APM le point de terminaison de déconnexion SAML d’Azure AD pour les déconnexions initiées par le fournisseur de services. Mais pour que cela soit vraiment efficace, APM doit savoir exactement quand un utilisateur se déconnecte.

Dans le cas où le portail web du BIG-IP n’est pas utilisé, l’utilisateur n’a aucun moyen de demander à APM de se déconnecter. Même si l’utilisateur se déconnecte de l’application elle-même, le BIG-IP n’en est techniquement pas conscient et la session de l’application peut facilement être rétablie par le biais de l’authentification unique. Pour cette raison, la déconnexion initiée par le fournisseur de services doit être soigneusement étudiée afin de s’assurer que les sessions sont terminées de manière sécurisée lorsqu’elles ne sont plus nécessaires.

Pour cela, vous pouvez ajouter une fonction SLO au bouton de déconnexion de vos applications afin qu’il puisse rediriger votre client vers le point de terminaison de déconnexion SAML d’Azure AD. Le point de terminaison de déconnexion SAML de votre locataire se trouve dans points de terminaison **Inscriptions d’applications** > **Points de terminaison**.

Si vous ne pouvez pas modifier l’application, vous pouvez demander au BIG-IP d’écouter l’appel de déconnexion des applications et de déclencher le Single Logout dès qu’il détecte la demande. Pour plus de détails sur l’utilisation de BIG-IP iRules à cette fin, consultez l’[article K42052145](https://support.f5.com/csp/article/K42052145) et l’[article K12056](https://support.f5.com/csp/article/K12056).

## <a name="summary"></a>Résumé

Cette dernière étape permet de séparer tous les paramètres appliqués avant qu’ils ne soient validés. Sélectionnez **Déployer** pour valider tous les paramètres, puis vérifiez que l’application s’est affichée dans votre locataire.

Votre application est maintenant publiée et accessible via un accès hybride sécurisé, soit directement via son URL, soit via les portails d’application de Microsoft.

Pour une sécurité accrue, les entreprises qui utilisent ce modèle pourraient également envisager de bloquer tout accès direct à l’application, forçant ainsi un chemin strict via BIG-IP.

## <a name="next-steps"></a>Étapes suivantes

En tant qu’utilisateur, lancez un navigateur et connectez-vous à l’URL externe de l’application ou sélectionnez l’icône de l’application dans le portail Microsoft MyApps. Après vous être authentifié auprès d’Azure AD, vous serez redirigé vers le serveur virtuel BIG-IP de l’application et connecté automatiquement via l’authentification unique.
La sortie des en-têtes injectés affichée par notre application basée sur les en-têtes est affichée.

![Capture d’écran montrant la sortie](./media/f5-big-ip-header-advanced/mytravel-example.png)

## <a name="troubleshooting"></a>Résolution des problèmes

L’impossibilité d’accéder à l’application protégée par l’accès hybride sécurisé peut être due à un certain nombre de facteurs potentiels, notamment une mauvaise configuration.

- Les journaux du BIG-IP constituent une source d’informations précieuse pour isoler toutes sortes de problèmes liés à l’authentification et à l’authentification unique. Lors de la résolution des problèmes, vous devez augmenter le niveau de détail du journal en accédant à **Access Policy (Stratégie d’accès)**  > **Overview (Vue d’ensemble)**  > **Event Logs (Journaux des événements)**  > **Settings (Paramètres)** . Sélectionnez la ligne correspondant à votre application publiée, puis **Edit (Modifiez)**  > **Access System Logs (Journaux système d’accès)** . Sélectionnez **Debug** (Déboguer) dans la liste d’authentification unique, puis **OK**. Vous pouvez maintenant reproduire le problème avant de consulter les journaux, mais n’oubliez pas de revenir en arrière lorsque vous avez terminé.

- Si vous voyez une erreur marquée BIG-IP après avoir été redirigé suivant la préauthentification Azure AD, le problème est probablement lié à l’authentification unique entre Azure AD et BIG-IP. Accédez à **Access** (Accès) > **Overview** (Vue d’ensemble) > **Access reports** (Rapports d’accès) et exécutez le rapport relatif à la dernière heure afin de voir si les journaux fournissent des indices. Le lien **View session variables** (Afficher les variables de session) pour votre session aidera également à comprendre si APM reçoit les revendications attendues d’Azure AD.

- Si vous ne voyez pas de page d’erreur BIG-IP, le problème est probablement davantage lié à l’authentification unique entre BIG-IP et l’application backend. Dans ce cas, vous devez accéder à **Access Policy** (Stratégie d’accès) > **Overview** (Vue d’ensemble) > **Active Sessions** (Sessions actives) et sélectionner le lien correspondant à votre session active. Le lien **View Variables** (Afficher les variables) à cet endroit peut également aider à déterminer la cause racine des problèmes d’authentification unique, en particulier si BIG-IP APM ne parvient pas à obtenir les bons identifiants d’utilisateur et de domaine.

Pour plus d’informations, consultez les [exemples d’attribution de variables Big-IP APM](https://devcentral.f5.com/s/articles/apm-variable-assign-examples-1107) et les [informations de référence sur les variables de session F5 BIG-IP](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html).

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez les articles ci-dessous :

- [Oubliez les mots de passe : optez pour l’authentification sans mot de passe](https://www.microsoft.com/security/business/identity/passwordless)

- [Qu’est-ce que l’accès conditionnel ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Zero Trust framework to enable remote work](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)
