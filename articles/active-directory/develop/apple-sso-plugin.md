---
title: Plug-in Microsoft Enterprise SSO pour les appareils Apple
titleSuffix: Microsoft identity platform | Azure
description: Découvrez le plug-in d’authentification unique Azure Active Directory pour appareils iOS, iPadOS et macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 1e12b2d9512bd7c62a2657e13e74790af6556cd4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507756"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise Single Sign-On pour appareils Apple (préversion)

> [!IMPORTANT]
> Cette fonctionnalité [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Le *plug-in Microsoft Enterprise SSO pour appareils Apple* fournit une authentification unique (SSO) pour les comptes Azure Active Directory (Azure AD) sur macOS, iOS et iPadOS, dans toutes les applications qui prennent en charge la fonctionnalité [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) d’Apple. Le plug-in fournit une authentification unique même pour d’anciennes applications dont votre entreprise peut dépendre, mais qui ne prennent pas encore en charge les bibliothèques d’identité ou les protocoles les plus récents. Microsoft a travaillé en étroite collaboration avec Apple pour développer ce plug-in afin de faciliter l’utilisation de votre application tout en offrant la meilleure protection disponible.

Le plug-in Enterprise SSO est actuellement une fonctionnalité intégrée dans les applications suivantes :

* [Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc) : iOS, iPadOS.
* [Portail d’entreprise](/mem/intune/apps/apps-company-portal-macos) Microsoft Intune : macOS.

## <a name="features"></a>Fonctionnalités

Le plug-in Microsoft Enterprise SSO pour les appareils Apple offre les avantages suivants :

- Il fournit l’authentification unique pour les comptes de Azure AD dans toutes les applications qui prennent en charge la fonctionnalité Apple Enterprise Single Sign-On.
- Il peut être activé par n’importe quelle solution de gestion des appareils mobiles (MDM).
- Il étend l’authentification unique aux applications qui n’utilisent pas encore les bibliothèques de plateforme d’identités Microsoft.
- Il étend l’authentification unique aux applications qui utilisent OAuth 2, OpenID Connect et SAML.

## <a name="requirements"></a>Spécifications

Pour utiliser le plug-in Microsoft Enterprise Single Sign-On pour des appareils Apple :

- L’appareil doit avoir et *prendre en charge* une application comprenant le plug-in Microsoft Enterprise SSO pour appareils Apple :
  - iOS 13.0 et versions ultérieures : [application Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc).
  - iPados 13.0 et versions ultérieures : [application Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc).
  - macOS 10.15 et versions ultérieures : [application Portail d’entreprise Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).
- L’appareil doit être *inscrit dans MDM*, par exemple par le biais de Microsoft Intune.
- La configuration doit être *envoyée (push) à l’appareil* afin d’activer le plug-in Enterprise Single Sign-On. Apple requiert cette contrainte de sécurité.

### <a name="ios-requirements"></a>Configuration requise pour iOS
- iOS version 13.0 ou ultérieure doit être installé sur l’appareil.
- Une application Microsoft qui fournit le plug-in Microsoft Enterprise SSO pour les appareils Apple doit être installée sur l’appareil. Dans la préversion publique, ces applications correspondent à l’[application Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc).


### <a name="macos-requirements"></a>Configuration requise pour macOS
- macOS 10.15 ou ultérieur doit être installé sur l’appareil. 
- Une application Microsoft qui fournit le plug-in Microsoft Enterprise SSO pour les appareils Apple doit être installée sur l’appareil. Dans la préversion publique, ces applications comprennent l’[application Portail d’entreprise Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>Activer le plug-in SSO

Utilisez les informations suivantes pour activer le plug-in SSO à l’aide de MDM.
### <a name="microsoft-intune-configuration"></a>Configuration de Microsoft Intune

Si vous utilisez Microsoft Intune comme service MDM, vous pouvez utiliser des paramètres de profil de configuration intégrés pour activer le plug-in Microsoft Enterprise SSO :

1. Configurez les paramètres d’[extension d’application SSO](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) d’un profil de configuration. 
1. Si le profil n’est pas attribué, [attribuez-le à un utilisateur ou à un groupe d’appareils](/mem/intune/configuration/device-profile-assign).

Les paramètres de profil qui activent le plug-in SSO seront automatiquement appliqués aux appareils du groupe la prochaine fois qu’un appareil effectuera un check-in dans Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuration manuelle des autres services MDM

Si vous n’utilisez pas Intune pour MDM, vous pouvez configurer une charge utile de profil d’authentification unique extensible pour les appareils Apple. Utilisez les paramètres suivants afin de configurer le plug-in Microsoft Enterprise SSO et ses options de configuration.

Paramètres iOS :

- **ID d’extension** : `com.microsoft.azureauthenticator.ssoextension`
- **ID d’équipe** : ce champ n’est pas nécessaire pour iOS.

Paramètres macOS :

- **ID d’extension** : `com.microsoft.CompanyPortalMac.ssoextension`
- **ID de l’équipe** : `UBF8T346G9`

Paramètres communs :

- **Type** : Rediriger
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>Autres options de configuration
Vous pouvez ajouter d’autres options de configuration pour étendre la fonctionnalité SSO à d’autres applications.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Activer l’authentification unique pour les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft

Le plug-in SSO permet à n’importe quelle application de participer à l’authentification unique, même si elle n’a pas été développée à l’aide d’un Kit de développement logiciel (SDK) Microsoft tel que Microsoft Authentication Library (MSAL).

Le plug-in SSO est installé automatiquement par les appareils qui :
* ont téléchargé l’application Authenticator sur iOS ou iPados, ou téléchargé l’application Portail d’entreprise Intune sur macOS ;
* sont inscrits auprès de votre organisation. 

Votre organisation utilise probablement l’application Authenticator pour des scénarios tels que l’authentification multifacteur (MFA), l’authentification sans mot de passe et l’accès conditionnel. À l’aide d’un fournisseur MDM, vous pouvez activer le plug-in SSO pour vos applications. Microsoft a facilité la configuration du plug-in à l’intérieur de Microsoft Endpoint Manager dans Intune. Une liste d’autorisation est utilisée pour configurer ces applications afin qu’elles utilisent le plug-in d’authentification unique.

>[!IMPORTANT]
> Le plug-in Microsoft Enterprise Single Sign-On ne prend en charge que les applications qui utilisent des technologies réseau ou des vues web natives Apple. Il ne prend pas en charge les applications qui intègrent leur propre implémentation de couche réseau.  

Utilisez les paramètres suivants pour configurer le plug-in Microsoft Enterprise SSO pour les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft.

#### <a name="enable-sso-for-all-managed-apps"></a>Activer l’authentification unique pour toutes les applications managées

- **Clé** : `Enable_SSO_On_All_ManagedApps`
- **Type** : `Integer`
- **Valeur** : 1 ou 0.

Lorsque cet indicateur est activé (sa valeur est définie sur `1`), toute application gérée par GPM (Gestion des données de référence) n’étant pas définie sur `AppBlockList` peut participer à l’authentification unique.

#### <a name="enable-sso-for-specific-apps"></a>Activer l’authentification unique pour des applications spécifiques

- **Clé** : `AppAllowList`
- **Type** : `String`
- **Valeur** : liste délimitée par des virgules d’ID de bundles d’applications pour les applications autorisées à participer à l’authentification unique.
- **Exemple** : `com.contoso.workapp, com.contoso.travelapp`

>[!NOTE]
> Safari et Safari View Service sont autorisés à utiliser l’authentification unique par défaut. Ils peuvent être configurés pour *ne pas* participer à l’authentification unique en ajoutant les ID d’offre groupée de Safari et Safari View Service dans AppBlockList. ID d’offre groupée iOS : [com.apple.mobilesafari, com.apple.SafariViewService] , macOS BundleID : com.apple.Safari

#### <a name="enable-sso-for-all-apps-with-a-specific-bundle-id-prefix"></a>Activer l’authentification unique pour toutes les applications avec un préfixe d’ID d’offre groupée spécifique
- **Clé** : `AppPrefixAllowList`
- **Type** : `String`
- **Valeur** : liste délimitée par des virgules comprenant les préfixes des ID de bundles d’applications pour les applications qui sont autorisées à participer à l’authentification unique. Ce paramètre permet à toutes les applications qui commencent par un préfixe particulier de participer à l’authentification unique.
- **Exemple** : `com.contoso., com.fabrikam.`

#### <a name="disable-sso-for-specific-apps"></a>Désactiver l’authentification unique pour des applications spécifiques

- **Clé** : `AppBlockList`
- **Type** : `String`
- **Valeur** : liste de valeurs séparées par des virgules des ID d’offre groupée d’application pour les applications qui peuvent ne pas participer à l’authentification unique.
- **Exemple** : `com.contoso.studyapp, com.contoso.travelapp`

Pour *désactiver* l’authentification unique pour Safari ou Safari View Service, vous devez explicitement ajouter leurs ID d’offre groupée à `AppBlockList` : 

- iOS : `com.apple.mobilesafari`, `com.apple.SafariViewService`
- MacOS : `com.apple.Safari`

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Activer l’authentification unique via des cookies pour une application donnée

Certaines applications disposant de paramètres réseau avancés peuvent rencontrer des problèmes inattendus lorsqu’elles sont activées pour l’authentification unique. Par exemple, une erreur indiquant que la requête réseau a été annulée ou interrompue peut s’afficher.

Si vos utilisateurs rencontrent des problèmes pour se connecter à une application même après l’avoir activée via les autres paramètres, essayez de l’ajouter à `AppCookieSSOAllowList` pour résoudre les problèmes.

- **Clé** : `AppCookieSSOAllowList`
- **Type** : `String`
- **Valeur** : liste délimitée par des virgules comprenant les préfixes des ID de bundles d’applications pour les applications qui sont autorisées à participer à l’authentification unique. Toutes les applications qui commencent par les préfixes répertoriés seront autorisées à participer à l’authentification unique.
- **Exemple** : `com.contoso.myapp1, com.fabrikam.myapp2`

**Autres exigences** : pour activer l’authentification unique à l’aide de `AppCookieSSOAllowList`, vous devez également ajouter leurs préfixes d’ID d’offre groupée`AppPrefixAllowList`.

N’essayez cette configuration que pour les applications qui rencontrent des échecs de connexion inattendus. 

#### <a name="summary-of-keys"></a>Résumé des clés

| Clé | Type | Valeur |
|--|--|--|
| `Enable_SSO_On_All_ManagedApps` | Integer | `1` pour activer l’authentification unique pour toutes les applications managées, `0` pour désactiver l’authentification unique pour toutes les applications managées. |
| `AppAllowList` | Chaîne<br/>*(liste de valeurs séparées par des virgules)* | ID d’offre groupée des applications autorisées à participer à l’authentification unique. |
| `AppBlockList` | Chaîne<br/>*(liste de valeurs séparées par des virgules)* | ID d’offre groupée des applications non autorisées à participer à l’authentification unique. |
| `AppPrefixAllowList` | Chaîne<br/>*(liste de valeurs séparées par des virgules)* | Préfixes d’ID d’offre groupée des applications autorisées à participer à l’authentification unique. |
| `AppCookieSSOAllowList` | Chaîne<br/>*(liste de valeurs séparées par des virgules)* | Préfixes d’ID d’offre groupée des applications autorisées à participer à l’authentification unique, mais qui utilisent des paramètres réseau particuliers et qui ont des problèmes avec l’authentification unique en utilisant d’autres paramètres. Les applications que vous ajoutez à `AppCookieSSOAllowList` doivent aussi être ajoutées à `AppPrefixAllowList`. |

#### <a name="settings-for-common-scenarios"></a>Paramètres pour les scénarios courants

- *Scénario* : je souhaite activer l’authentification unique pour la plupart des applications managées, mais pas pour toutes.

    | Clé | Valeur |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | Les ID d’offre groupée (liste de valeurs séparées par des virgules) des applications pour lesquelles vous souhaitez empêcher l’authentification unique. |

- *Scénario* : je souhaite désactiver l’authentification unique pour Safari, qui est activée par défaut, mais activer l’authentification unique pour toutes les applications managées.

    | Clé | Valeur |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | Les ID d’offre groupée (liste de valeurs séparées par des virgules) des applications Safari pour lesquelles vous souhaitez empêcher l’authentification unique.<ul><li>Pour iOS : `com.apple.mobilesafari`, `com.apple.SafariViewService`</li><li>Pour macOS : `com.apple.Safari`</li></ul> |

- *Scénario* : je souhaite activer l’authentification unique sur toutes les applications managées et sur quelques applications non managées, mais désactiver l’authentification unique pour d’autres applications.

    | Clé | Valeur |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppAllowList` | Les ID d’offre groupée (liste de valeurs séparées par des virgules) des applications pour lesquelles vous souhaitez autoriser l’authentification unique. |
    | `AppBlockList` | Les ID d’offre groupée (liste de valeurs séparées par des virgules) des applications pour lesquelles vous souhaitez empêcher l’authentification unique. |


##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Découvrir des identificateurs de bundles d’applications sur des appareils iOS

Apple n’offre aucun moyen simple d’accéder aux ID de bundles à partir de l’App Store. Pour découvrir les ID de bundles des applications que vous souhaitez utiliser pour l’authentification unique, le plus simple est de demander à votre fournisseur ou à votre développeur d’applications. Si cette option n’est pas disponible, vous pouvez utiliser votre configuration MDM pour découvrir les ID de bundles. 

1. Activez temporairement l’indicateur suivant dans votre configuration MDM :

    - **Clé** : `admin_debug_mode_enabled`
    - **Type** : `Integer`
    - **Valeur** : 1 ou 0
1. Quand cet indicateur est actif, connectez-vous aux applications iOS sur l’appareil dont vous souhaitez connaître l’ID de bundle. 
1. Dans l’application Authenticator, sélectionnez **Aide** > **Envoyer des journaux** > **Afficher les journaux**. 
1. Dans le fichier journal, recherchez la ligne suivante : `[ADMIN MODE] SSO extension has captured following app bundle identifiers` : Elle doit contenir tous les identificateurs de bundles d’applications visibles par l’extension SSO. 

Utilisez les ID de bundles pour configurer l’authentification unique pour les applications. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Autoriser les utilisateurs à se connecter à partir d’applications inconnues et du navigateur Safari

Par défaut, le plug-in Microsoft Enterprise SSO ne fournit l’authentification unique aux applications autorisées que lorsqu’un utilisateur s’est connecté à partir d’une application qui utilise une bibliothèque de plateforme d’identités Microsoft comme MSAL. Le plug-in Microsoft Enterprise SSO peut également acquérir des informations d’identification partagées lorsqu’il est appelé par une autre application qui utilise une bibliothèque de plateforme d’identités Microsoft, dans le cadre d’une nouvelle acquisition de jetons.

Lorsque vous activez l’indicateur `browser_sso_interaction_enabled`, les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft peuvent effectuer l’amorçage initial et obtenir des informations d’identification partagées. Le navigateur Safari peut également effectuer l’amorçage initial et obtenir des informations d’identification partagées. 

Si le plug-in Microsoft Enterprise SSO ne dispose pas encore d’informations d’identification partagées, il essaiera d’en obtenir chaque fois qu’une connexion sera demandée à partir d’une URL Azure AD dans le navigateur Safari, ASWebAuthenticationSession, SafariViewController ou une autre application native autorisée. 

Utilisez les paramètres suivants pour activer l’indicateur : 

- **Clé** : `browser_sso_interaction_enabled`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

macOS requiert ce paramètre afin d’offrir une expérience cohérente dans toutes les applications. iOS et iPadOS ne requièrent pas ce paramètre car la plupart des applications utilisent l’application Authenticator pour la connexion. Toutefois, nous vous recommandons d’activer ce paramètre car, si certaines de vos applications n’utilisent pas l’application Authenticator sur iOS ou iPados, cet indicateur améliore l’expérience. Ce paramètre est désactivé par défaut.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Désactiver la demande d’authentification multifacteur lors de l’amorçage initial

Par défaut, le plug-in Microsoft Enterprise SSO demande toujours à l’utilisateur de procéder à une authentification multifacteur lors de l’amorçage initial et lors de l’obtention des informations d’identification partagées. L’utilisateur est invité à effectuer une authentification multifacteur, même si celle-ci n’est pas requise pour l’application qu’il a ouverte. Ce comportement permet d’utiliser facilement les informations d’identification partagées dans toutes les autres applications sans devoir afficher une invite à l’utilisateur si une authentification multifacteur est requise ultérieurement. Étant donné que l’utilisateur reçoit globalement moins d’invites, cette configuration est généralement une bonne décision.

L’activation de `browser_sso_disable_mfa` a pour effet de désactiver la MFA lors de l’amorçage initial et lors de l’obtention des informations d’identification partagées. Dans ce cas, l’utilisateur reçoit une invite uniquement si une MFA est requise par une application ou une ressource. 

Pour activer l’indicateur, utilisez les paramètres suivants :

- **Clé** : `browser_sso_disable_mfa`
- **Type** : `Integer`
- **Valeur** : 1 ou 0

Nous vous recommandons de laisser cet indicateur désactivé, car il réduit le nombre de fois que l’utilisateur est invité à se connecter. Si votre organisation utilise rarement la MFA, vous pouvez activer l’indicateur. Toutefois, nous vous recommandons d’utiliser la MFA plus fréquemment à la place. C’est la raison pour laquelle l’indicateur est désactivé par défaut.

#### <a name="disable-oauth-2-application-prompts"></a>Désactiver les invites d’application OAuth 2

Si une application invite vos utilisateurs à se connecter, même si le plug-in Microsoft Enterprise SSO fonctionne pour d’autres applications sur l’appareil, l’application peut ignorer l’authentification unique au niveau de la couche de protocole.  Les informations d’identification partagées sont également ignorées par ces applications, car le plug-in fournit l’authentification unique en ajoutant les informations d’identification aux requêtes réseau effectuées par les applications autorisées.

Ces paramètres spécifient si l’extension d’authentification unique doit empêcher les applications natives et web d’ignorer l’authentification unique au niveau de la couche de protocole et forcer l’affichage d’une invite de connexion à l’utilisateur.

Pour une expérience d’authentification unique cohérente sur toutes les applications sur l’appareil, nous vous recommandons d’activer l’un de ces paramètres, qui sont désactivés par défaut.
  
Désactiver l’invite de l’application et afficher le sélecteur de compte :

- **Clé** : `disable_explicit_app_prompt`
- **Type** : `Integer`
- **Valeur** : 1 ou 0
  
Désactiver l’invite de l’application et sélectionner automatiquement un compte dans la liste des comptes d’authentification unique correspondants :
- **Clé** : `disable_explicit_app_prompt_and_autologin`
- **Type** : `Integer`
- **Valeur** : 1 ou 0


#### <a name="use-intune-for-simplified-configuration"></a>Utiliser Intune pour une configuration simplifiée

Vous pouvez utiliser Intune comme service MDM pour faciliter la configuration du plug-in Microsoft Enterprise SSO. Par exemple, vous pouvez utiliser Intune pour activer le plug-in et ajouter d’anciennes applications à une liste d’autorisation afin qu’elles bénéficient de l’authentification unique. 

Pour plus d’informations, consultez la [documentation sur la configuration d’Intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Utiliser le plug-in SSO dans votre application

La [bibliothèque MSAL pour appareils Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versions 1.1.0 et ultérieures prend en charge le plug-in Microsoft Enterprise SSO pour appareils Apple. Il s’agit de la méthode recommandée pour ajouter la prise en charge du plug-in Microsoft Enterprise Single Sign-On. Elle vous garantit de disposer de toutes les fonctionnalités de la plateforme d’identités Microsoft.

Si vous créez une application pour des scénarios d’employé de terrain, consultez [Mode d’appareil partagé pour les appareils iOS](msal-ios-shared-devices.md) pour accéder aux informations de configuration.

## <a name="understand-how-the-sso-plug-in-works"></a>Comprendre le fonctionnement du plug-in SSO

Le plug-in Microsoft Enterprise SSO s’appuie sur l’[infrastructure Apple Enterprise SSO](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Les fournisseurs d’identité qui rejoignent l’infrastructure peuvent intercepter le trafic réseau pour leurs domaines et améliorer ou modifier le mode de traitement de ces demandes. Par exemple, le plug-in SSO peut afficher davantage d’interfaces utilisateur pour collecter les informations d’identification de l’utilisateur final en toute sécurité, exiger l’authentification MFA ou fournir en mode silencieux des jetons à l’application.

Des applications natives peuvent également implémenter des opérations personnalisées et communiquer directement avec le plug-in SSO. Pour plus d’informations, consultez cette [vidéo d’Apple sur la conférence mondiale des développeurs 2019](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Applications utilisant la MSAL

La [bibliothèque MSAL pour appareils Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versions 1.1.0 et ultérieures prend en charge le plug-in Microsoft Enterprise SSO pour appareils Apple en mode natif pour les comptes professionnels et scolaires. 

Aucune configuration particulière n’est nécessaire si vous avez suivi [toutes les étapes recommandées](./quickstart-v2-ios.md) et utilisé le [format d’URI de redirection](./redirect-uris-ios.md) par défaut. Sur les appareils disposant du plug-in SSO, la MSAL appelle automatiquement celui-ci pour toutes les demandes de jeton interactives et silencieuses. Elle l’appelle également pour les opérations d’énumération des comptes et de suppression de compte. Étant donné que la MSAL implémente un protocole de plug-in SSO natif reposant sur des opérations personnalisées, cette configuration offre l’expérience native la plus fluide pour l’utilisateur final. 

Si le plug-in SSO n’est pas activé par MDM, mais que l’application Microsoft Authenticator est présente sur l’appareil, la MSAL utilise plutôt l’application Authenticator pour toutes les demandes de jeton interactives. Le plug-in SSO partage l’authentification unique avec l’application Authenticator.

### <a name="applications-that-dont-use-msal"></a>Applications n’utilisant pas la MSAL

Les applications qui n’utilisent pas de bibliothèque de plateforme d’identités Microsoft, telle que MSAL, peuvent toujours bénéficier de l’authentification unique si un administrateur les ajoute à la liste d’autorisation. 

Vous n’avez pas besoin de modifier le code de ces applications tant que les conditions suivantes sont réunies :

- L’application utilise des infrastructures Apple pour exécuter des demandes réseau. Ces infrastructure sont par exemple [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) et [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- L’application utilise des protocoles standard pour communiquer avec Azure AD. Ces protocoles incluent, par exemple, OAuth 2, SAML et WS-Federation.
- L’application ne collecte pas les noms d’utilisateur et mots de passe en texte clair dans l’interface utilisateur native.

Dans ce cas, l’authentification unique est fournie lorsque l’application crée une requête réseau et ouvre un navigateur web pour connecter l’utilisateur. Quand un utilisateur est redirigé vers une URL de connexion à Azure AD, le plug-in SSO valide l’URL et vérifie l’existence d’informations d’identification SSO pour cette URL. S’il trouve les informations d’identification, le plug-in SSO les transmet à Azure AD, ce qui permet à l’application de terminer la demande réseau sans demander à l’utilisateur d’entrer ses informations d’identification. En outre, si l’appareil est connu d’Azure AD, le plug-in SSO transmet le certificat de l’appareil pour répondre au contrôle d’accès conditionnel en fonction de l’appareil. 

Pour prendre en charge l’authentification unique pour les applications non-MSAL, le plug-in SSO implémente un protocole similaire au plug-in de navigateur Windows décrit dans [Qu’est-ce qu’un jeton d’actualisation principal ?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Comparé à des applications basées sur MSAL, le plug-in SSO agit de manière plus transparence pour des applications non-MSAL. Il s’intègre à l’expérience de connexion du navigateur existante que les applications fournissent. 

L’utilisateur final voit l’expérience familière et n’a pas besoin de se reconnecter dans chaque application. Par exemple, au lieu d’afficher le sélecteur de compte natif, le plug-in SSO ajoute des sessions SSO à l’expérience du sélecteur de compte basé sur le web. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [Mode d’appareil partagé pour les appareils iOS](msal-ios-shared-devices.md).
