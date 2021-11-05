---
title: Tutoriel pour configurer Azure Active Directory B2C avec Ping Identity
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C avec Ping Identity
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5c7938e9c80cbce1128981516ccac2adcbcb8949
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044529"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Tutoriel : Configurer Ping Identity avec Azure Active Directory B2C pour un accès hybride sécurisé

Cet exemple de tutoriel explique comment étendre Azure Active Directory (AD) B2C avec [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) et [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) pour activer l’accès hybride sécurisé.

De nombreuses propriétés web existantes, telles que les sites de e-commerce et les applications web qui sont exposées à Internet, sont déployées derrière un système proxy, parfois appelé système proxy inverse. Ces systèmes proxy offrent diverses fonctions, telles que la pré-authentification, l’application de la stratégie et le routage du trafic. Voici des exemples de cas d’usage : protection de l’application web par rapport au trafic web entrant et gestion uniforme des sessions entre des déploiements de serveurs distribués.

Dans la plupart des cas, cette configuration comprend une couche de traduction d’authentification qui externalise l’authentification à partir de l’application web. Les proxys inverses fournissent à leur tour le contexte des utilisateurs authentifiés aux applications web, sous une forme simplifiée telle qu’une valeur d’en-tête sous une forme claire ou condensée. Dans ce type de configuration, les applications n’utilisent pas de jetons standard tels que Security Assertion Markup Language (SAML), OAuth ou Open ID Connect (OIDC). À la place, elles dépendent du proxy pour fournir le contexte d’authentification et maintenir la session avec l’agent de l’utilisateur final, tel que le navigateur ou l’application native. En tant que service s’exécutant dans un intercepteur, les proxys peuvent fournir le contrôle de session final. Cela signifie également que le service proxy doit être hautement efficace et évolutif afin de ne devenir ni un goulot d’étranglement, ni un point de défaillance unique pour les applications derrière le service proxy. Ce diagramme présente une implémentation de proxy inverse et un flux des communications typiques.

![Image montrant l’implémentation du proxy inverse](./media/partner-ping/reverse-proxy.png)

Si vous êtes dans une situation où vous souhaitez moderniser la plateforme d’identités dans des configurations de ce type, les problèmes suivants se posent :

- Comment dissocier l’effort de modernisation des applications de la modernisation de la plateforme d’identités ?

- Comment établir un environnement de coexistence avec une authentification moderne et une authentification héritée, en utilisant le fournisseur de service d’identité modernisé ?

  a. Comment assurer la cohérence de l’expérience de l’utilisateur final ?

  b. Comment fournir une expérience d’authentification unique entre les applications coexistantes ?

Nous abordons une approche permettant de résoudre ce type de problèmes en intégrant Azure AD B2C avec les technologies [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) et [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html).

## <a name="coexistence-environment"></a>Environnement de coexistence

Une solution simple qui est à la fois techniquement viable et rentable consiste à configurer le système proxy inverse pour utiliser le système d’identité modernisé en déléguant l’authentification.  
Dans ce cas, les proxys prennent en charge les protocoles d’authentification modernes et utilisent l’authentification redirigée (passive) qui envoie l’utilisateur au nouveau fournisseur d’identité (IdP).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C en tant que fournisseur d’identité

Azure AD B2C a la possibilité de définir des **stratégies** qui pilotent des expériences et comportements différents de l’utilisateur, également appelés **parcours utilisateur** et orchestrés à partir de l’extrémité du serveur. Chacune de ces stratégies expose un point de terminaison de protocole en mesure d’effectuer l’authentification comme s’il s’agissait d’un fournisseur d’identité (IdP). Aucune action spéciale n’est nécessaire côté application pour les stratégies spécifiques. L’application effectue simplement une requête d’authentification standard au point de terminaison d’authentification propre au protocole exposé par la stratégie d’intérêt.  
Azure AD B2C peut être configuré pour partager le même fournisseur dans plusieurs stratégies ou un fournisseur unique pour chaque stratégie. Chaque application peut pointer vers une ou plusieurs de ces stratégies en effectuant une requête d’authentification de protocole native et piloter des comportements d’utilisateur souhaités, tels que la connexion, l’inscription et les modifications de profil. Le diagramme montre les workflows d’application OIDC et SAML.

![Image montrant l’implémentation de OIDC et SAML](./media/partner-ping/azure-ad-identity-provider.png)

Bien que le scénario mentionné soit adapté aux applications modernisées, les applications héritées peuvent avoir des difficultés pour rediriger correctement l’utilisateur car la requête d’accès aux applications peut ne pas inclure le contexte de l’expérience utilisateur. Dans la plupart des cas, la couche proxy ou un agent intégré sur l’application web intercepte la requête d’accès.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess en tant que proxy inverse

De nombreux clients ont déployé PingAccess en tant que proxy inverse pour jouer un ou plusieurs rôles, comme indiqué plus haut dans cet article. PingAccess peut intercepter une requête directe en tant qu’intercepteur ou via une redirection en provenance d’un agent s’exécutant sur le serveur d’applications web.

PingAccess peut être configuré avec OIDC, OAuth2 ou SAML pour effectuer l’authentification sur un fournisseur d’authentification en amont. Un seul fournisseur IdP en amont peut être configuré à cet effet sur le serveur PingAccess. Le diagramme suivant montre cette configuration.

![Image montrant l’implémentation de PingAccess avec OIDC](./media/partner-ping/authorization-flow.png)

Dans un déploiement typique d’Azure AD B2C avec plusieurs stratégies exposant plusieurs fournisseurs **IdP**, cela représente un défi. En effet, PingAccess peut être configuré avec un seul fournisseur IdP en amont.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate en tant que proxy de fédération

PingFederate est un pont d’identité d’entreprise qui peut être entièrement configuré en tant que fournisseur d’authentification ou proxy pour des fournisseurs IdP multiples en amont si nécessaire. Le diagramme suivant illustre cette capacité.

![Image montrant l’implémentation de PingFederate](./media/partner-ping/pingfederate.png)

Cette capacité peut être utilisée pour faire basculer de manière contextuelle/dynamique ou de manière déclarative une requête entrante vers une stratégie spécifique d’Azure AD B2C. Voici une description du flux de séquence du protocole pour cette configuration.

![Image montrant le workflow de PingAccess et PingFederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md) lié à votre abonnement Azure.

- PingAccess et PingFederate déployés dans des conteneurs Docker ou directement sur des machines virtuelles Azure.

## <a name="connectivity"></a>Connectivité

Vérifiez que les éléments suivants sont connectés.

- **Serveur PingAccess Server** – capable de communiquer avec le serveur PingFederate, le navigateur client, OIDC, OAuth well-known et découverte des clés publiée par le service Azure AD B2C et le serveur PingFederate.

- **PingFederate Server** – capable de communiquer avec le serveur PingAccess, le navigateur client, OIDC, OAuth well-known et découverte des clés publiée par le service Azure AD B2C.

- **Application AuthN basée sur un en-tête ou héritée** – capable de communiquer vers et à partir du serveur PingAccess.

- **Application par partie de confiance SAML** – capable d’atteindre le trafic du navigateur à partir du client. Capable d’accéder aux métadonnées de fédération SAML publiées par le service Azure AD B2C.

- **Application moderne** – capable d’atteindre le trafic du navigateur à partir du client. Capable d’accéder à OIDC, OAuth well-known et découverte des clés publiée par le service Azure AD B2C.

- **API REST** – capable d’atteindre le trafic à partir du client natif ou web. Capable d’accéder à OIDC, OAuth well-known et découverte des clés publiée par le service Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Configurer Azure AD B2C

Vous pouvez utiliser les flux d’utilisateur de base ou les stratégies avancées d’Identity Enterprise Framework (IEF) à cet effet. PingAccess génère le point de terminaison des métadonnées basé sur la valeur de l’**émetteur** à l’aide de la convention de découverte basée sur [WebFinger](https://tools.ietf.org/html/rfc7033).
Pour respecter cette convention, effectuez la mise à jour de l’émetteur Azure AD B2C à l’aide des propriétés de stratégie dans les flux d’utilisateurs.

![Image montrant les paramètres de jeton](./media/partner-ping/token-setting.png)

Dans les stratégies avancées, la configuration peut être effectuée à l’aide de l’élément de métadonnées **IssuanceClaimPattern** avec la valeur **AuthorityWithTfp** dans le [profil technique de l’émetteur de jetons JWT](./jwt-issuer-technical-profile.md).

## <a name="configure-pingaccesspingfederate"></a>Configurer PingAccess/PingFederate

La section suivante décrit la configuration requise.
Le diagramme illustre le flux d’utilisateur global pour l’intégration.

![Image montrant l’intégration de PingAccess et PingFederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Configurer PingFederate en tant que fournisseur de jetons

Pour configurer PingFederate en tant que fournisseur de jetons pour PingAccess, assurez-vous que la connectivité de PingFederate vers PingAccess est établie, suivie de la connectivité de PingAccess vers PingFederate.  
Consultez [cet article](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) pour plus d’informations sur les étapes de la configuration.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Configurer une application PingAccess pour l’authentification basée sur l’en-tête

Une application PingAccess doit être créée pour l’application web cible pour l’authentification basée sur l’en-tête. Procédez comme suit.

#### <a name="step-1--create-a-virtual-host"></a>Étape 1 – Créer un hôte virtuel

>[!IMPORTANT]
>Pour la configuration de cette solution, l’hôte virtuel doit être créé pour chaque application. Pour plus d’informations sur les considérations relatives à la configuration et leur impact, consultez [Considérations relatives aux clés](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Procédez comme suit pour créer un hôte virtuel :

1. Accédez à **Paramètres** > **Accès** > **Hôtes virtuels**.

2. Sélectionnez **Ajouter un hôte virtuel**.

3. Dans le champ Hôte, entrez la partie de nom de domaine complet de l’URL de l’application.

4. Dans le champ Port, entrez **443**.

5. Sélectionnez **Enregistrer**.

#### <a name="step-2--create-a-web-session"></a>Étape 2 – Créer une session web

Procédez comme suit pour créer une session web :

1. Accédez à **Paramètres** > **Accès** > **Sessions Web**.

2. Sélectionnez **Ajouter une session web**.

3. Spécifiez un **Nom** pour la session web.

4. Sélectionnez le **Type de cookie**  : **Signed JWT** ou **Encrypted JWT**.

5. Spécifiez une valeur unique pour **Audience**.

6. Dans le champ **ID client**, entrez l’**ID de l’application Azure AD**.

7. Dans le champ **Clé secrète client**, entrez la **clé** que vous avez générée pour l’application dans Azure AD.

8. Facultatif : vous pouvez créer et utiliser des revendications personnalisées avec l’API Microsoft Graph. Si vous choisissez de le faire, sélectionnez **Avancé** et désélectionnez les options **Demander un profil** et **Actualiser les attributs utilisateur**. Pour plus d’informations sur l’utilisation des revendications personnalisées, consultez [Utiliser une revendication personnalisée](../active-directory/app-proxy/application-proxy-configure-single-sign-on-with-headers.md).

9. Sélectionnez **Enregistrer**.

#### <a name="step-3--create-identity-mapping"></a>Étape 3 – Créer un mappage d’identité

>[!NOTE]
>Un mappage d’identité peut être utilisé avec plusieurs applications si plusieurs applications attendent les mêmes données dans l’en-tête.

Procédez comme suit pour créer un mappage d’identité :

1. Accédez à **Paramètres** > **Accès** > **Mappage d’identité**.

2. Sélectionnez **Ajouter un mappage d’identité**.

3. Spécifiez un **Nom**.

4. Sélectionnez le mappage d’identité **Mappage d’identité selon le type d’en-tête**.

5. Dans la table **Mappage d’attributs**, spécifiez les mappages requis. Par exemple,

   Nom de l’attribut | Nom de l’en-tête |
   |---|---|
   | 'upn' | x-userprincipalname |
   | 'email' | x-email |
   | 'oid' | x-oid |
   | 'scp' | x-scope |
   | 'amr' | x-amr |

6. Sélectionnez **Enregistrer**.

#### <a name="step-4--create-a-site"></a>Étape 4 – Créer un site

>[!NOTE]
>Dans certaines configurations, il est possible qu’un site contienne plusieurs applications. Un site peut être utilisé avec plusieurs applications le cas échéant.

Procédez comme suit pour créer un site :

1. Accédez à **Principal** > **Sites**.

2. Sélectionner **Ajouter un site**.

3. Spécifiez un **Nom** pour le site.

4. Entrer la **Cible** du site. La cible est la paire nom_d’hôte:port pour le serveur hébergeant l’application. N’entrez pas le chemin d’accès à l’application dans ce champ. Par exemple, une application à l’adresse https://mysite:9999/AppName a la valeur cible de mysite : 9 999

5. Indiquez si la cible attend des connexions sécurisées.

6. Si la cible attend des connexions sécurisées, définissez le groupe de certificats approuvés sur **Faire confiance à tous**.

7. Sélectionnez **Enregistrer**.

#### <a name="step-5--create-an-application"></a>Étape 5 – Créer une application

Procédez comme suit pour créer une application dans PingAccess pour chaque application dans Azure que vous souhaitez protéger.

1. Accédez à **Principal** > **Applications**.

2. Sélectionnez **Ajouter une application**.

3. Spécifiez un **Nom** pour l’application.

4. Si vous le souhaitez, entrez une **Description** pour l’application.

5. Spécifiez la **Racine du contexte** pour l’application. Par exemple, une application à l’adresse https://mysite:9999/AppName a une racine du contexte /AppName. La racine du contexte doit commencer par une barre oblique (/), ne doit pas se terminer par une barre oblique (/) et peut avoir une profondeur de plus d’une couche par exemple : /Apps/MyApp.

6. Sélectionnez l’**hôte virtuel** que vous avez créé.

   >[!NOTE]
   >La combinaison de l’hôte virtuel et de la racine du contexte doit être unique dans PingAccess.

7. Sélectionnez la **session web** vous avez créée.

8. Sélectionnez le **Site** que vous avez créé et qui contient l’application.

9. Sélectionnez le **Mappage d’identité** que vous avez créé.

10. Sélectionnez **Activé** pour activer le site lors de l’enregistrement.

11. Sélectionnez **Enregistrer**.

### <a name="configure-the-pingfederate-authentication-policy"></a>Configurer la stratégie d’authentification de PingFederate

Configurez la stratégie d’authentification de PingFederate pour fédérer sur plusieurs fournisseurs IdP fournis par les locataires Azure AD B2C.

1. Créez un contrat pour relier les attributs entre les fournisseurs IdP et SP. Pour plus d’informations, consultez [Hub de fédération et contrats de stratégie d’authentification](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). Vous aurez probablement besoin d’un seul contrat, sauf si le fournisseur SP requiert un ensemble d’attributs différent pour chaque fournisseur IdP.

2. Pour chaque fournisseur IdP, créez une connexion IdP entre l’IdP et PingFederate, le hub de fédération en tant que SP.

3. Dans la fenêtre **Mappage de session cible**, ajoutez les contrats de stratégie d’authentification applicables à la connexion IdP.

4. Dans la fenêtre **Sélecteurs**, configurez un sélecteur d’authentification. Par exemple, reportez-vous à une instance du **premier adaptateur de l’identificateur** pour mapper chaque fournisseur IdP à la connexion IdP correspondante dans une stratégie d’authentification.

5. Créez une connexion SP entre PingFederate, le hub de fédération en tant que fournisseur IdP et le fournisseur SP.

6. Ajoutez le contrat de stratégie d’authentification correspondant à la connexion SP dans la fenêtre **Mappage de la source d’authentification**.

7. Travaillez avec chaque fournisseur IdP pour vous connecter à PingFederate, avec le hub de fédération en tant que SP.

8. Travaillez avec le fournisseur SP pour vous connecter à PingFederate, avec le hub de fédération en tant que IdP.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
