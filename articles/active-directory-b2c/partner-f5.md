---
title: Tutoriel pour configurer Azure Active Directory B2C avec F5 BIG-IP
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C avec F5 BIG-IP pour un accès hybride sécurisé.
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.service: active-directory
ms.subservice: B2C
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: ca0d912c837a4c3fb218d1bb3fb8b07b43100119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044624"
---
# <a name="tutorial-extend-azure-active-directory-b2c-to-protect-on-premises-applications-using-f5-big-ip"></a>Tutoriel : Étendre Azure Active Directory B2C pour protéger les applications locales à l’aide de F5 BIG-IP

Dans cet exemple de tutoriel, découvrez comment intégrer Azure Active Directory (Azure AD) B2C à [F5 BIG-IP Access Policy Manager (APM)](https://www.f5.com/services/resources/white-papers/easily-configure-secure-access-to-all-your-applications-via-azure-active-directory). Ce tutoriel montre la manière dont les applications existantes peuvent être exposées en toute sécurité à Internet grâce à la sécurité de BIG-IP combinée à la préauthentification Azure AD B2C, à l’accès conditionnel et à l’authentification unique (SSO).

La société F5 Inc. se concentre sur la livraison, la sécurité, les performances et la disponibilité des services connectés, y compris la disponibilité des ressources de calcul, de stockage et de réseau. Elle fournit du matériel, des logiciels modulaires et des solutions d’appliances virtuelles compatibles avec le cloud.

Le contrôleur BIG-IP Application Delivery Controller (ADC) de F5 est souvent déployé en tant que passerelle sécurisée entre les réseaux privés et l’Internet.
Il offre une multitude de fonctionnalités, notamment l’inspection au niveau des applications et des contrôles d’accès entièrement personnalisables. Lorsqu’il est déployé en tant que proxy inverse, le BIG-IP peut également être utilisé pour permettre un accès hybride sécurisé aux applications métier critiques, en mettant en place des services frontaux avec une couche d’accès d’identité fédérée gérée par APM de F5.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure

- Un BIG-IP existant, ou déployez un [environnement virtuel (VE) BIG-IP d’essai sur Azure](../active-directory/manage-apps/f5-bigip-deployment-guide.md)

- L’une des références SKU de licence F5 BIG-IP suivantes

  - F5 BIG-IP® Best bundle

  - Licence autonome F5 BIG-IP Access Policy Manager™

  - Licence de module complémentaire F5 BIG-IP Access Policy Manager™ sur une instance existante de BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)

  - [Licence d’essai](https://www.f5.com/trial/big-ip-trial.php) de 90 jours des fonctionnalités complètes de BIG-IP

- Une application web existante basée sur l’en-tête, ou [configurez une application IIS](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) à des fins de test

- Un [certificat SSL](../active-directory/manage-apps/f5-bigip-deployment-guide.md#ssl-profile) pour les services de publication sur HTTPS, ou utilisez la valeur par défaut lors des tests

## <a name="scenario-description"></a>Description du scénario

Pour ce scénario, nous disposons d’une application interne dont l’accès repose sur la réception d’en-têtes d’autorisation HTTP d’un système de répartiteur hérité, permettant aux agents commerciaux d’être dirigés vers leurs domaines de contenu respectifs.

Le service doit être étendu à une base de consommateurs plus large. L’application doit donc être mise à niveau pour offrir un choix d’options d’authentification des consommateurs ou être remplacée par une solution plus appropriée.

Dans un monde idéal, l’application serait mise à niveau pour pouvoir être gérée et gouvernée directement par un plan de contrôle moderne. Mais dans la mesure où il n’existe aucune forme d’interopérabilité moderne, sa modernisation nécessiterait des efforts et un temps considérables, ce qui entraînerait des coûts inévitables et des risques d’interruption de service.

Au lieu de cela, un BIG-IP Virtual Edition (VE) déployé entre l’Internet public et le réseau virtuel interne Azure auquel notre application est connectée sera utilisé pour ouvrir l’accès avec Azure AD B2C pour son large choix de capacités de connexion et d’inscription.

Le fait de disposer d’un BIG-IP devant l’application nous permet de superposer le service avec la préauthentification Azure AD B2C et l’authentification unique basée sur l’en-tête, améliorant ainsi de manière significative la posture de sécurité globale de l’application, ce qui permet à l’entreprise de continuer à se développer à un rythme soutenu, sans interruption.

La solution d’accès hybride sécurisé pour ce scénario est constituée des éléments suivants :

- **Application** : Service principal protégé par l’accès hybride sécurisé Azure AD B2C et BIG-IP.

- **Azure AD B2C** : Serveur d’autorisation du fournisseur d’identité (IdP) et d’OpenID Connect (OIDC), responsable de la vérification des informations d’identification des utilisateurs, de l’authentification multifacteur et de l’authentification unique sur BIG-IP APM.

- **BIG-IP** : En tant que proxy inverse pour l’application, BIG-IP APM devient également le client OIDC, déléguant l’authentification au serveur d’autorisation OIDC, avant d’effectuer une authentification unique basée sur l’en-tête auprès du service principal.

Le diagramme suivant illustre le flux initié par le fournisseur de services pour ce scénario.

![Capture d’écran montrant le flux initié par le fournisseur de services pour ce scénario](./media/partner-f5/flow-diagram.png)

|Étape| Description|
|:----|:-------|
| 1. | L’utilisateur se connecte au point de terminaison de l’application, où BIG-IP est le fournisseur de services |
| 2. | BIG-IP APM, qui est le client OIDC, redirige l’utilisateur vers le point de terminaison du locataire Azure AD B2C, le serveur d’autorisation OIDC |
| 3. | Le locataire Azure AD B2C préauthentifie l’utilisateur et applique les stratégies d’accès conditionnel en vigueur |
|4. | Azure AD B2C redirige l’utilisateur vers le fournisseur de services avec le code d’autorisation |
| 5. | Le client OIDC demande au serveur d’autorisation d’échanger le code d’autorisation contre un jeton d’ID |
| 6. | BIG-IP APM accorde l’accès à l’utilisateur et injecte les en-têtes HTTP dans la requête du client transmise à l’application |

Pour une sécurité accrue, les entreprises qui utilisent ce modèle pourraient également envisager de bloquer tout accès direct à l’application, forçant ainsi un chemin strict via BIG-IP.

## <a name="azure-ad-b2c-configuration"></a>Configuration d’Azure AD B2C

L’activation de BIG-IP avec l’authentification Azure AD B2C nécessite un locataire Azure AD B2C avec un flux d’utilisateur ou une stratégie personnalisée appropriés. [Configurez un flux d’utilisateur Azure AD B2C](tutorial-create-user-flows.md).

### <a name="create-custom-attributes"></a>Créer des attributs personnalisés

Les attributs personnalisés peuvent être obtenus à partir de diverses sources, notamment directement à partir d’objets utilisateur Azure AD B2C existants, demandés à des IdP fédérés, provenant de connecteurs d’API ou collectés pendant le parcours d’inscription d’un utilisateur. Le cas échéant, ils peuvent être inclus dans le jeton envoyé à l’application.

Comme votre application héritée attend des attributs spécifiques, incluez-les dans votre flux d’utilisateur. Mais n’hésitez pas à les remplacer par les attributs requis par votre application. Si vous configurez une application de test en suivant les instructions de la section des conditions préalables, tous les en-têtes feront l’affaire, car ils y figurent.

1. Connectez-vous au portail de votre locataire Azure AD B2C.

2. Dans le volet de gauche, sélectionnez **Attributs utilisateur**, puis sélectionnez **Ajouter** pour créer deux attributs personnalisés.

   - Identifiant de l’agent : **Type de données** chaîne

   - Zone géographique de l’agent : **Type de données** chaîne

### <a name="add-attributes-to-user-flow"></a>Ajouter des attributs au flux d’utilisateur

1. Dans le volet gauche, accédez à **Stratégies** > **Flux d’utilisateur**.

2. Sélectionnez votre stratégie, par exemple **B2C_1_SignupSignin**.

3. Sélectionnez **Attributs utilisateur** et ajoutez les deux attributs personnalisés, ainsi que l’attribut **Nom d’affichage**. Il s’agit des attributs qui seront collectés lors de l’inscription de l’utilisateur.

4. Sélectionnez **Revendications d’application** et ajoutez les deux attributs personnalisés, ainsi que le **nom d’affichage**. Il s’agit des attributs qui seront envoyés à BIG-IP.

Vous pouvez utiliser la fonctionnalité [Exécuter le flux d’utilisateur](tutorial-create-user-flows.md) dans le menu du flux d’utilisateur sur la barre de navigation de gauche pour vérifier qu’il demande tous les attributs définis.

### <a name="azure-ad-b2c-federation"></a>Fédération Azure AD B2C

Pour que BIG-IP et Azure AD B2C puissent se faire confiance, ils doivent se fédérer. BIG-IP doit donc être inscrit dans le locataire Azure AD B2C en tant qu’application OIDC.

1. Toujours dans le portail Azure AD B2C, sélectionnez **Inscriptions d’applications** >  **Nouvelle inscription**.

2. Fournissez un nom pour l’application. Par exemple, **HeaderApp1**.

3. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** .

4. Sous **URI de redirection**, sélectionnez **Web**, puis entrez le nom de domaine complet public du service protégé, ainsi que le chemin d’accès.

5. Conservez le reste et sélectionnez **Inscrire**.

6. Allez dans **Certificats et secrets** >  **+ Nouvelle clé secrète client**.

7. Spécifiez un nom descriptif et une durée de vie pour le secret qui sera utilisé par BIG-IP.

8. Notez la clé secrète client, vous en aurez besoin plus tard pour configurer BIG-IP.

L’URI de redirection est le point de terminaison de BIG-IP vers lequel un utilisateur est renvoyé par le serveur d’autorisation (Azure AD B2C) après l’authentification. [Inscrivez une application](tutorial-register-applications.md) pour Azure AD B2C.

## <a name="big-ip-configuration"></a>Configuration de BIG-IP

BIG-IP propose plusieurs méthodes pour configurer l’accès hybride sécurisé Azure AD, notamment la fonctionnalité Guided Configuration basée sur un Assistant, ce qui permet de réduire le temps et les efforts nécessaires pour implémenter plusieurs scénarios courants. Son infrastructure axée sur le flux de travail offre une expérience intuitive adaptée à des topologies d’accès spécifiques et est utilisée pour la publication rapide de services web nécessitant une configuration minimale pour être publiés.

### <a name="version-check"></a>Vérification de la version

Ce tutoriel est basé sur Guided Configuration v.7/8, mais il peut également s’appliquer aux versions précédentes. Pour vérifier votre version, connectez-vous à la configuration web de BIG-IP avec un compte administrateur et allez dans **Access (Accès)**  > **Guided Configuration (Configuration guidée)** . La version doit être affichée dans le coin supérieur droit. Pour mettre à niveau votre version de Guided Configuration de BIG-IP, suivez [ces instructions](https://support.f5.com/csp/article/K85454683).

### <a name="ssl-profiles"></a>Profils SSL

La configuration de votre instance BIG-IP avec un profil SSL client vous permettra de sécuriser le trafic côté client via TLS. Pour ce faire, vous devez importer un certificat correspondant au nom de domaine utilisé par l’URL publique de votre application. Dans la mesure du possible, nous vous recommandons d’utiliser une autorité de certification publique, mais les certificats auto-signés BIG-IP intégrés peuvent également être utilisés lors des tests.
[Ajoutez et gérez des certificats](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-ssl-administration-13-0-0.html) dans BIG-IP VE.

## <a name="guided-configuration"></a>Configuration guidée

1. Dans la configuration web, accédez à **Access (Accès)**  > **Guided Configuration (Configuration guidée)** pour lancer l’Assistant de déploiement.

2. Sélectionnez **Federation (Fédération)**  > **F5 as OAuth Client and Resource Server (F5 comme client OAuth et serveur de ressources)** .

3. Observez le résumé du flux pour ce scénario, puis sélectionnez **Next (Suivant)** pour démarrer l’Assistant.

### <a name="oauth-properties"></a>Propriétés d’OAuth

Cette section définit les propriétés permettant la fédération entre BIG-IP APM et le serveur d’autorisation OAuth, votre locataire Azure AD B2C. OAuth sera référencé dans toute la configuration de BIG-IP, mais la solution utilisera en fait OIDC, une couche d’identité simple par-dessus le protocole OAuth 2.0 permettant aux clients d’OIDC de vérifier l’identité des utilisateurs et d’obtenir d’autres informations de profil.

Portez une attention particulière aux détails, car toute erreur aura un impact sur l’authentification et l’accès.

#### <a name="configuration-name"></a>Nom de la configuration

Fournir un nom d’affichage pour la configuration vous aidera à distinguer les nombreuses configurations de déploiement qui pourraient éventuellement exister dans la configuration guidée. Une fois défini, le nom ne peut pas être modifié et n’est visible que dans la vue Guided Configuration.

#### <a name="mode"></a>Mode

BIG-IP APM agira comme un client d’OIDC, sélectionnez donc uniquement l’option Client.

#### <a name="dns-resolver"></a>Résolveur DNS

La cible spécifiée doit être capable de résoudre les IP publiques de vos points de terminaison Azure AD B2C. Choisissez un résolveur DNS public existant ou créez-en un nouveau.

#### <a name="provider-settings"></a>Paramètres du fournisseur

Ici, nous allons configurer Azure AD B2C comme IdP OAuth2. Vous remarquerez que la version 8 de Guided Configuration propose des modèles Azure AD B2C, mais, comme il manque plusieurs étendues, nous utiliserons un type personnalisé pour le moment. F5 envisage d’inclure les étendues manquantes dans une future mise à jour de Guided Configuration. Ajoutez un nouveau fournisseur et configurez-le comme suit :

- **Propriétés générales d’OAuth**

  | Propriétés | Description |
  |:-------|:---------|
  |Type de fournisseur OAuth | Custom |
  | Choisir le fournisseur OAuth | Créez un nouveau fournisseur OAuth (ou utiliser un fournisseur OAuth existant le cas échéant) |
  | Nom | Nom d’affichage unique pour l’IdP B2C. Ce nom sera affiché aux utilisateurs en tant qu’option de fournisseur pour la connexion|
  | Type de jeton | JSON Web Token |

- **Paramètres de stratégie OAuth**

  | Propriétés | Description |
  |:-----------|:----------------|
  | Étendue | Laissez ce champ vide, l’étendue OpenID pour la connexion des utilisateurs sera ajoutée automatiquement |
  | Type d’autorisation | Code d’autorisation. |
  | Activer OpenID Connect | Cochez la case pour mettre le client OAuth APM en mode OIDC |
  | Type de flux | Code d’autorisation. |

- **Paramètres du fournisseur OAuth**

  L’URI OpenID ci-dessous fait référence au point de terminaison des métadonnées utilisé par les clients d’OIDC pour découvrir automatiquement des informations d’IdP critiques telles que le renouvellement des certificats de signature. Localisez le point de terminaison des métadonnées pour votre locataire Azure AD B2C en accédant à **Inscriptions d’applications** > **Points de terminaison** et en copiant l’URI du document des métadonnées Azure AD B2C OpenID Connect. Par exemple : `https://wacketywackb2c .b2clogin.com/<tenantname>.onmicrosoft.com/<policyname>/v2.0/.well-known/openid-configuration`.

  Ensuite, mettez à jour l’URI avec vos propres propriétés, `https://<tenantname>.b2clogin.com/WacketywackB2C.onmicrosoft.com/B2C_1_SignUpIn/v2.0/.well-known/openid-configuration`.

  Collez cet URI dans le navigateur pour afficher les métadonnées OIDC de votre locataire Azure AD B2C.

  | Propriétés | Description |
  |:----------|:----------|
  | Public visé | ID client de l’application représentant BIG-IP dans votre locataire Azure AD B2C |
  | URI d’authentification | Point de terminaison d’autorisation dans vos métadonnées B2C OIDC |
  | URI de jeton | Point de terminaison de jeton dans vos métadonnées Azure AD B2C |
  | URI de requête Userinfo | Laissez ce champ vide. Azure AD B2C ne prend actuellement pas en charge cette fonctionnalité |
  |URI OpenID | Point de terminaison de métadonnées de l’URI OpenID que vous avez créé ci-dessus |
  | Ignorer la validation des certificats expirés | Laissez cette case décochée. |
  | Autoriser le certificat de configuration JWK auto-signé | Vérification |
  | Pack d’accès conditionnel approuvé | Sélectionnez ca-bundle.crt pour utiliser les autorités approuvées F5 par défaut |
  | Intervalle de détection | Indiquez l’intervalle approprié auquel BIG-IP doit interroger votre locataire Azure AD B2C pour les mises à jour. L’intervalle minimum proposé par AGC version 16.1 0.0.19 final est de 5 minutes|

- **Paramètres du serveur OAuth**

  Cette section fait référence au serveur d’autorisation OIDC, soit votre locataire Azure AD B2C.

  |Propriétés | Descriptions|
  |:---------|:---------|
  | ID client | ID client de l’application représentant BIG-IP dans votre locataire Azure AD B2C. |
  | Clé secrète client | Clé secrète client correspondant à l’application. |
  |Profil SSL client-serveur | La définition d’un profil SSL permet à APM de communiquer avec l’IdP Azure AD B2C via TLS. Sélectionnez l’option `serverssl` par défaut. |

- **Paramètres de requête OAuth**

  Il est intéressant de noter que BIG-IP possède toutes les requêtes Azure AD B2C nécessaires dans son ensemble de requêtes préconfigurées. Toutefois, il a été observé que, pour la build sur laquelle nous effectuions l’implémentation, ces requêtes étaient malformées et qu’il manquait des paramètres importants. Nous avons donc choisi de les créer manuellement.

- **Demande de jeton : activée**

  | Propriétés | Description |
  |:-----------|:------------|
  | Choisir une requête OAuth | Création |
  | HTTP method | POST |
  | Activer les en-têtes| Désactivé |
  | Activer les paramètres | Activée |

  | Type de paramètre | Nom du paramètre | Valeur du paramètre|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | nonce | nonce| |
  | redirect-uri | redirect-uri | |
  | scope | scope | |
  | response-type | response-type | |
  | client-secret | client-secret | |
  | custom | grant_type | authorization_code |

- **Demande de redirection d’authentification : activée**

  | Propriétés | Description |
  |:-----------|:------------|
  | Choisir une requête OAuth | Création |
  | HTTP method | GET |
  | Type d’invite | None |
  | Activer les en-têtes | Désactivé |
  | Activer les paramètres | Activée |

  | Type de paramètre | Nom du paramètre | Valeur du paramètre|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | redirect-uri | redirect-uri | |
  | response-type |response-type | |
  | scope | scope | |
  | nonce | nonce | |

- **Demande d’actualisation du jeton** - **désactivée** : peut être activée et configurée le cas échéant.

- **Demande UserInfo OpenID** - **désactivée** : non prise en charge actuellement dans les locataires mondiaux d’Azure AD B2C.

- **Propriétés du serveur virtuel**

  Un serveur virtuel BIG-IP doit être créé pour intercepter les demandes de clients externes pour le service principal protégé par un accès hybride sécurisé. Le serveur virtuel doit se voir attribuer une adresse IP qui est mappée à l’enregistrement DNS public pour le point de terminaison du service BIG-IP représentant l’application. Poursuivez et utilisez un serveur virtuel existant, le cas échéant. Sinon, fournissez les informations suivantes :

  | Propriétés | Description |
  |:-----------|:------------|
  | Adresse de destination | Adresse IP privée ou publique qui deviendra le point de terminaison de service BIG-IP pour l’application principale |
  | Port du service | HTTPS |
  | Activer le port de redirection | Cochez cette case pour que les utilisateurs soient automatiquement redirigés de HTTP vers HTTPS |
  | Port de redirection | HTTP |
  | Profil SSL du client | Remplacez le profil `clientssl` prédéfini par celui qui contient votre certificat SSL. Vous pouvez également tester avec le profil par défaut, mais cela entraînera probablement une alerte du navigateur. |

- **Propriétés du pool**

  Les services principaux sont représentés dans BIG-IP sous forme de pool, contenant un ou plusieurs serveurs d’applications vers lesquels le serveur virtuel dirige le trafic entrant. Sélectionnez un pool existant, sinon créez-en un.

  | Propriétés | Description |
  |:-----------|:------------|
  | Méthode d’équilibrage de charge | Laissez sur Tourniquet (round robin) |
  |Serveur de pool | Adresse IP interne de l’application principale |
  | Port | Port de service de l’application principale |
  
>[!NOTE]
>BIG-IP doit avoir une ligne de vue sur l’adresse du serveur de pool spécifié.

- **Paramètres d’authentification unique**

  BIG-IP prend en charge de nombreuses options d’authentification unique, mais, en mode client OAuth, la configuration guidée est limitée à Kerberos ou aux en-têtes HTTP. Activez l’authentification unique et utilisez les informations suivantes pour qu’APM mappe les attributs entrants que vous avez définis précédemment aux en-têtes sortants.

  | Propriétés | Description |
  |:-----------|:------------|
  | Opération d’en-tête |`Insert`|
  | Nom de l’en-tête | « nom » |
  | Valeur de l’en-tête | `%{session.oauth.client.last.id_token.name}`|
  | Opération d’en-tête | `Insert`|
  |Nom de l’en-tête| `agentid`|
  |Valeur de l’en-tête | `%{session.oauth.client.last.id_token.extension_AgentGeo}`|
 
  >[!Note]
  > Les variables de session APM définies entre accolades respectent la CASSE. Ainsi, si vous saisissez « agentid » alors que le nom de l’attribut Azure AD B2C est envoyé sous la forme « AgentID », le mappage de l’attribut échouera. Sauf nécessité, nous vous recommandons de définir tous les attributs en minuscules. Dans le cas d’Azure AD B2C, le flux d’utilisateur demande à l’utilisateur les attributs supplémentaires en utilisant le nom de l’attribut tel qu’il est affiché dans le portail. Par conséquent, il peut être préférable d’utiliser la casse normale de la phrase plutôt que des minuscules.

  ![Capture d’écran montrant les paramètres d’authentification unique de l’utilisateur](./media/partner-f5/single-sign-on.png)

- **Propriétés de personnalisation**

  Ces paramètres vous permettent de personnaliser la langue et l’aspect des écrans que vos utilisateurs rencontrent lorsqu’ils interagissent avec le flux de stratégie d’accès APM. Vous pouvez personnaliser les messages et les invites à l’écran, modifier la disposition des écrans, les couleurs et les images et localiser les légendes, les descriptions et les messages qui sont normalement personnalisables dans les éléments de la stratégie d’accès.

  Remplacez la chaîne « F5 Networks » dans le champ de texte En-tête du formulaire par le nom de votre propre organisation. Par exemple, « Accès hybride sécurisé Wacketywack Inc. »

- **Propriétés de gestion de session**

  Un paramètre de gestion de session BIG-IP est utilisé pour définir les conditions dans lesquelles les sessions utilisateur sont terminées ou autorisées à se poursuivre, les limites pour les utilisateurs et les adresses IP, ainsi que les pages d’erreur. Ces paramètres sont facultatifs, mais nous recommandons vivement d’implémenter la fonctionnalité de Single Log Out (SLO), qui garantit la fermeture sécurisée des sessions lorsqu’elles ne sont plus nécessaires, réduisant ainsi le risque qu’une personne obtienne par inadvertance un accès non autorisé aux applications publiées.

## <a name="related-information"></a>Informations connexes

La dernière étape fournit une vue d’ensemble des configurations. En cliquant sur Deploy (Déployer), vous validerez vos paramètres et créerez tous les objets BIG-IP et APM nécessaires pour permettre un accès hybride sécurisé à l’application.
L’application doit également être visible en tant que ressource cible dans l’accès conditionnel. Consultez l’[aide relative à la création de stratégies d’accès conditionnel pour Azure AD B2C](conditional-access-identity-protection-overview.md).
Pour une sécurité accrue, les entreprises qui utilisent ce modèle pourraient également envisager de bloquer tout accès direct à l’application, forçant ainsi un chemin strict via BIG-IP.

## <a name="next-steps"></a>Étapes suivantes

En tant qu’utilisateur, lancez un navigateur et connectez-vous à l’URL externe de l’application. La page de connexion au client OAuth de BIG-IP vous invitera à vous connecter en utilisant le code d’autorisation octroyé. Les instructions relatives à la suppression de cette étape sont fournies dans la section des configurations supplémentaires.

Vous serez ensuite redirigé pour vous inscrire et vous authentifier auprès de votre locataire Azure AD B2C.

![Capture d’écran montrant la connexion de l’utilisateur](./media/partner-f5/sign-in-message.png)

![Capture d’écran montrant le message de bienvenue après la connexion](./media/partner-f5/welcome-page.png)

### <a name="supplemental-configurations"></a>Configurations supplémentaires

**Single Log Out (SLO)**

Azure AD B2C prend entièrement en charge la déconnexion des applications et des fournisseurs d’identité par le biais de divers [mécanismes](session-behavior.md?pivots=b2c-custom-policy#single-sign-out).
Le fait que la fonction de déconnexion de votre application appelle le point de terminaison de déconnexion d’Azure AD B2C serait l’un des moyens de réaliser une Single Log Out. De cette façon, nous pouvons être sûrs qu’Azure AD B2C émet une redirection finale vers BIG-IP pour s’assurer que la session APM entre l’utilisateur et l’application a également été terminée.
Une autre solution consiste à demander à BIG-IP d’écouter la demande lorsque le bouton de déconnexion des applications est sélectionné et, lorsque BIG-IP détecte la demande, d’appeler simultanément le point de terminaison de déconnexion d’Azure AD B2C. Cette approche évite d’avoir à modifier l’application elle-même tout en assurant la Single Log Out. Plus d’informations sur l’utilisation de BIG-IP iRules pour implémenter cette approche sont disponibles [ici](https://support.f5.com/csp/article/K42052145).
Dans les deux cas, votre locataire Azure AD B2C doit connaître le point de terminaison de déconnexion d’APM. 

1. Accédez à **Gérer** > **Manifeste** dans votre portail Azure AD B2C et recherchez la propriété logoutUrl. Elle doit être nulle.

2. Ajoutez l’URI après déconnexion d’APM : `https://<mysite.com>/my.logout.php3`, où `<mysite.com>` est le nom de domaine complet BIG-IP de votre propre application basée sur l’en-tête.

**Flux de connexion optimisé**

Une étape facultative pour améliorer l’expérience de connexion des utilisateurs consiste à supprimer l’invite de connexion OAuth affichée aux utilisateurs avant la préauthentification Azure AD. 

1. Accédez à **Access (Accès)**  > **Guided Configuration (Configuration guidée)** et sélectionnez l’icône représentant un petit cadenas à l’extrême droite de la ligne de l’application basée sur l’en-tête pour déverrouiller la configuration stricte.

   ![Capture d’écran montrant le flux de connexion optimisé](./media/partner-f5/optimized-login-flow.png)

   Le déverrouillage de la configuration stricte empêche toute modification ultérieure par le biais de l’interface utilisateur de l’Assistant, laissant tous les objets BIG-IP associés à l’instance publiée de l’application ouverts à la gestion directe.

2. Accédez à **Access (Accès)**  > **Profiles/Policies (Profils/stratégies)**  > **Access Profiles (Per-session Policies) [Profils d’accès (Stratégies par session)]** et sélectionnez le lien Edit (Modifier) dans la colonne **Per-Session Policy** (Stratégie par session) pour l’objet de stratégie de l’application.

   ![Capture d’écran montrant des profils d’accès](./media/partner-f5/access-profile.png)

3. Sélectionnez la petite croix pour supprimer l’objet de stratégie OAuth Logon Page (Page de connexion OAuth) et, lorsque vous y êtes invité, choisissez de vous connecter au nœud précédent.

   ![Capture d’écran montrant l’objet Page de connexion OAuth](./media/partner-f5/oauth-logon.png)

4. Sélectionnez **Apply Access Policy (Appliquer la stratégie d’accès)** dans le coin supérieur gauche et fermez l’onglet de l’éditeur visuel. La prochaine tentative de connexion à l’application devrait vous conduire directement à la page de connexion d’Azure AD B2C.

>[!Note]
>La réactivation du mode strict et le déploiement d’une configuration remplacent tous les paramètres effectués en dehors de l’interface utilisateur de Guided Configuration. Il est donc recommandé d’implémenter ce scénario en créant manuellement tous les objets de configuration pour les services de production.

### <a name="troubleshooting"></a>Dépannage

L’impossibilité d’accéder à l’application protégée peut être due à un certain nombre de facteurs potentiels, y compris une mauvaise configuration.

- Les journaux de BIG-IP constituent une source d’informations précieuse pour isoler tous les problèmes liés à l’authentification et à l’authentification unique. Si vous tentez de résoudre des problèmes, vous devez augmenter le niveau de verbosité des journaux.

  1. Accédez à **Access Policy (Stratégie d’accès)**  > **Overview (Vue d’ensemble)**  > **Event Logs (Journaux des événements)**  > **Settings (Paramètres)** .

  2. Sélectionnez la ligne correspondant à votre application publiée, puis **Edit (Modifiez)**  > **Access System Logs (Journaux système d’accès)** .

  3. Sélectionnez **Debug (Déboguer)** dans la liste d’authentification unique, puis cliquez sur **OK**. Vous pouvez maintenant reproduire le problème avant de consulter les journaux, mais n’oubliez pas de revenir en arrière lorsque vous avez terminé.

- Si vous voyez une erreur marquée BIG-IP immédiatement après avoir réussi l’authentification Azure AD B2C, le problème est peut-être lié à l’authentification unique entre Azure AD et BIG-IP.

  1. Accédez à **Access (Accès)**  > **Overview (Vue d’ensemble)**  > **Access reports (Rapports d’accès)** .

  2. Exécutez le rapport qui porte sur la dernière heure afin de voir si les journaux fournissent des indices. Le lien View session variables (Afficher les variables de session) pour votre session aidera également à comprendre si APM reçoit les revendications attendues d’Azure AD.

- Si vous ne voyez pas de page d’erreur BIG-IP, le problème est probablement davantage lié à la demande principale ou à l’authentification unique entre BIG-IP et l’application.

  1. Accédez à **Access Policy (Stratégie d’accès)**  > **Overview (Vue d’ensemble)**  > **Active Sessions (Sessions actives)** .

  2. Sélectionnez le lien correspondant à votre session active.

- Le lien View Variables (Afficher les variables) à cet endroit peut également aider à déterminer la cause racine, en particulier si BIG-IP APM ne parvient pas à obtenir les attributs de session appropriés.
Les journaux de votre application peuvent alors vous aider à comprendre si elle a reçu ou non ces attributs sous forme d’en-têtes.

- Si vous utilisez Guided Configuration v8, sachez qu’un problème connu génère l’erreur BIG-IP suivante après une authentification Azure AD B2C réussie.  

  ![Capture d’écran montrant le message d’erreur](./media/partner-f5/error-message.png)

  Il s’agit d’une violation de stratégie en raison de l’incapacité de BIG-IP à valider la signature du jeton émis par Azure AD B2C. Le même journal d’accès devrait pouvoir fournir plus de détails sur le problème.

  ![Capture d’écran montrant les journaux d’accès](./media/partner-f5/access-log.png)

  La cause racine exacte est toujours en cours d’investigation par l’équipe d’ingénierie de F5, mais le problème semble lié au fait qu’AGC n’a pas activé le paramètre Auto JWT (JWT automatique) pendant le déploiement, empêchant ainsi APM d’obtenir les clés de signature du jeton actuel.

  Jusqu’à ce que le problème soit résolu, une façon de le contourner consiste à activer manuellement ce paramètre. 

  1. Accédez à **Access (Accès)**  > **Guided Configuration (Configuration guidée)** et sélectionnez l’icône représentant un petit cadenas à l’extrême droite de la ligne de votre application basée sur l’en-tête.

  2. Une fois la configuration guidée déverrouillée, accédez à **Access (Accès)**  > **Federation (Fédération)**  > **OAuth Client/Resource Server (Client OAuth/serveur de ressources)**  > **Providers (Fournisseurs)** .

  3. Sélectionnez le fournisseur pour votre configuration Azure AD B2C.

  4. Cochez la case **Use Auto JWT (Utiliser un JWT automatique)** et sélectionnez **Discover (Découvrir)** , puis **Save (Enregistrer)** .

    Vous devriez maintenant voir le champ Key (JWT) [Clé (JWT)] renseigné avec l’ID de clé (KID) du certificat de signature de jetons fourni par les métadonnées de l’URI OpenID.
  
  5. Enfin, sélectionnez l’option jaune **Apply Access Policy (Appliquer la stratégie d’accès)** dans le coin supérieur gauche, située à côté du logo F5. Appliquez ces paramètres, puis sélectionnez à nouveau **Apply (Appliquer)** pour actualiser la liste des profils d’accès.

Consultez l’aide fournie par F5 pour connaître d’autres [astuces de dépannage du client OAuth et du serveur de ressources](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-oauth-configuration-14-1-0/apm-oauth-client-and-resource-server.html#GUID-774384BC-CF63-469D-A589-1595D0DDFBA2).
