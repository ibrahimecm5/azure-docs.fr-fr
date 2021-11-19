---
title: Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory
description: Instructions pour le déploiement de l’authentification sans mot de passe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/28/2021
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5597ca90a2264d192273ebfd57ecfd51382c3c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349297"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory

Les mots de passe sont un vecteur d’attaque principal. Les personnes malveillantes utilisent l’ingénierie sociale, l’hameçonnage et les attaques par pulvérisation de mots de passe pour compromettre les mots de passe. Une stratégie d’authentification sans mot de passe atténue ces risques d’attaques.

Microsoft propose [trois options d’authentification sans mot de passe](concept-authentication-passwordless.md), qui s’intègrent à Azure AD (Azure Active Directory) :

* [Application Microsoft Authenticator](./concept-authentication-passwordless.md#microsoft-authenticator-app) : elle transforme n’importe quel téléphone iOS ou Android en informations d’identification fortes, sans mot de passe, ce qui permet aux utilisateurs de se connecter sur tous les navigateurs ou toutes les plateformes.

* [Clés de sécurité FIDO2](./concept-authentication-passwordless.md#fido2-security-keys) : elles sont pratiques pour les utilisateurs qui se connectent à des machines partagées telles que des kiosques, qui se trouvent dans des situations où l’utilisation des téléphones est restreinte ou qui disposent d’identités dotées de privilèges élevés. 

* [Windows Hello Entreprise](./concept-authentication-passwordless.md#windows-hello-for-business) : idéal pour les personnes utilisant leur ordinateur Windows dédié. 

> [!NOTE]
> Pour créer une version hors connexion de ce plan avec tous les liens, utilisez la fonctionnalité d’impression au format pdf de votre navigateur.

## <a name="use-the-passwordless-methods-wizard"></a>Utiliser l’Assistant Méthodes sans mot de passe

Le [portail Azure](https://portal.azure.com/) dispose désormais d’un Assistant Méthodes sans mot de passe, qui vous aide à sélectionner la méthode appropriée pour chacune de vos audiences. Si vous n’avez pas encore déterminé les méthodes appropriées, consultez [https://aka.ms/passwordlesswizard](https://aka.ms/passwordlesswizard), puis revenez à cet article pour continuer à planifier les méthodes sélectionnées. **Vous devez disposer de droits d’administrateur pour accéder à cet Assistant.**

## <a name="passwordless-authentication-scenarios"></a>Scénarios d’authentification sans mot de passe

Les méthodes d’authentification sans mot de passe de Microsoft permettent de nombreux scénarios. Tenez compte des besoins de votre organisation, des prérequis et des fonctionnalités de chaque méthode d’authentification pour sélectionner votre stratégie d’authentification sans mot de passe. 

Le tableau suivant liste les méthodes d’authentification sans mot de passe par type d’appareil. Nos recommandations sont en **gras**.

| Types d’appareils| Méthode d’authentification sans mot de passe |
| - | - |
| Appareils non Windows dédiés| <li> **Application Microsoft Authenticator** <li> Clés de sécurité |
| Ordinateurs Windows 10 dédiés (versions 1703 et ultérieures)| <li> **Windows Hello Entreprise** <li> Clés de sécurité |
| Ordinateurs Windows 10 dédiés (avant la version 1703)| <li> **Windows Hello Entreprise** <li> Application Microsoft Authenticator |
| Appareils partagés : tablettes et appareils mobiles| <li> **Application Microsoft Authenticator** <li> Connexion par mot de passe à usage unique |
| Kiosques (hérité)| **Application Microsoft Authenticator** |
| Kiosques et ordinateurs partagés ‎(Windows 10)| <li> **Clés de sécurité** <li> Application Microsoft Authenticator |


## <a name="prerequisites"></a>Configuration requise 

Veillez à respecter les prérequis avant de démarrer votre déploiement sans mot de passe.

### <a name="required-roles"></a>Rôles nécessaires

Voici les rôles les moins privilégiés nécessaires pour ce déploiement :
<p>

| Rôle Azure AD| Description |
| - | -|
| Administrateur général| Pour implémenter une expérience d’inscription combinée. |
| Administrateur d’authentification| Pour implémenter et gérer les méthodes d’authentification. |
| Utilisateur| Pour configurer l’application Authenticator sur un appareil ou inscrire une clé de sécurité basée sur un appareil pour une connexion web ou Windows 10. |

Dans le cadre de ce plan de déploiement, nous vous recommandons d’activer l’authentification sans mot de passe pour tous les [comptes privilégiés](../privileged-identity-management/pim-configure.md).

### <a name="microsoft-authenticator-app-and-security-keys"></a>Application Microsoft Authenticator et clés de sécurité

Les prérequis sont déterminés par les méthodes d’authentification sans mot de passe sélectionnées.

| Prérequis| Application Microsoft Authenticator| Clés de sécurité FIDO2|
| - | -|-|
| [L’inscription combinée à Azure AD MFA (Multi-Factor Authentication) et à SSPR (réinitialisation de mot de passe en libre-service)](howto-registration-mfa-sspr-combined.md) est activée| √| √|
| [Les utilisateurs peuvent effectuer l’authentification Azure AD MFA](howto-mfa-getstarted.md)| √| √|
| [Les utilisateurs se sont inscrits à Azure AD MFA et SSPR](howto-registration-mfa-sspr-combined.md)| √| √|
| [Les utilisateurs ont inscrit leurs appareils mobiles sur Azure Active Directory](../devices/overview.md)| √| |
| Windows 10 version 1809 ou ultérieure utilisant un navigateur pris en charge, comme Microsoft Edge ou Mozilla Firefox (version 67 ou ultérieure). Microsoft recommande la version 1903 ou supérieure pour la prise en charge native.| | √|
| Clés de sécurité compatibles. Veillez à utiliser une [clé de sécurité FIDO2 testée et vérifiée par Microsoft](concept-authentication-passwordless.md), ou une clé de sécurité FIDO2 compatible.| | √|


### <a name="windows-hello-for-business"></a>Windows Hello Entreprise

Les prérequis et les chemins de déploiement de Windows Hello Entreprise dépendent étroitement du fait que vous effectuez un déploiement dans une configuration locale, hybride ou cloud uniquement. Ils dépendent également de la stratégie de jonction des appareils. 

Sélectionnez Windows Hello Entreprise, puis [exécutez l’Assistant](https://aka.ms/passwordlesswizard) afin de déterminer les prérequis et le déploiement appropriés pour votre organisation.

![Sélectionner Windows Hello Entreprise dans l’Assistant](media/howto-authentication-passwordless-deployment/passwordless-wizard-select.png)


L’Assistant utilise vos entrées pour créer un plan à suivre, étape par étape.

## <a name="plan-the-project"></a>Planifier le projet

Les échecs de projets informatiques, lorsqu’ils se produisent, proviennent généralement d’une disparité entre les attentes et l’impact, les responsabilités et les résultats. Pour éviter un tel cas de figure, [veillez à faire appel aux bonnes personnes](../fundamentals/active-directory-deployment-plans.md) et à ce que les rôles des parties prenantes soient bien compris.

### <a name="plan-a-pilot"></a>Prévoir un pilote

Lorsque vous déployez l’authentification sans mot de passe, vous devez d’abord activer un ou plusieurs groupes pilotes. Vous pouvez créer des groupes spécialement à cet effet. Ajoutez les utilisateurs pilotes aux groupes. Activez ensuite de nouvelles méthodes d’authentification sans mot de passe pour les groupes sélectionnés. Consultez les [bonnes pratiques pour un pilote](../fundamentals/active-directory-deployment-plans.md).

### <a name="plan-communications"></a>Planifier les communications

Vos communications aux utilisateurs finaux doivent inclure les informations suivantes :

* [Conseils d’aide sur l’inscription combinée à Azure AD MFA et SSPR](howto-registration-mfa-sspr-combined.md)

* [Téléchargement de l’application Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)

* [Inscription dans l’application Microsoft Authenticator](howto-authentication-passwordless-phone.md)

* [Connexion avec votre téléphone](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)

Microsoft fournit des modèles de communication pour les utilisateurs finaux. Téléchargez le [matériel de déploiement de l’authentification](https://aka.ms/MFAtemplates) pour faciliter la rédaction de vos communications. Le matériel de déploiement comprend des affiches personnalisables et des modèles d’e-mail qui vous permettent d’informer vos utilisateurs sur l’arrivée des options d’authentification sans mot de passe dans votre organisation.

## <a name="plan-user-registration"></a>Planifier l’inscription des utilisateurs

Les utilisateurs inscrivent leur méthode sans mot de passe dans le cadre du **workflow d’inscription combinée des informations de sécurité** sur [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Azure AD journalise l’inscription des clés de sécurité et de l’application Microsoft Authenticator ainsi que tout autre changement apporté aux méthodes d’authentification. 

Les administrateurs peuvent fournir aux utilisateurs qui n’ont pas encore de mot de passe un code secret servant de [passe d’accès temporaire](howto-authentication-temporary-access-pass.md) pour qu’ils puissent inscrire leurs informations de sécurité dans [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo.md). Il s’agit d’un code secret à durée limitée qui répond aux impératifs de l’authentification forte. Le **passe d’accès temporaire est un processus spécifique à chaque utilisateur**.

Cette méthode peut également être utilisée pour faciliter la récupération de l’accès d’un utilisateur quand celui-ci a perdu ou oublié son facteur d’authentification, par exemple une clé de sécurité ou l’application Microsoft Authenticator, et qu’il doit se connecter pour **inscrire une nouvelle méthode d’authentification forte**. 

>[!NOTE] 
> Si vous ne pouvez pas utiliser la clé de sécurité ou l’application Microsoft Authenticator dans certains scénarios, l’authentification multifacteur avec un nom d’utilisateur et un mot de passe ainsi qu’une autre méthode inscrite peut servir d’option de secours.

## <a name="plan-for-and-deploy-the-microsoft-authenticator-app"></a>Planifier et déployer l’application Microsoft Authenticator

L’[application Microsoft Authenticator](concept-authentication-passwordless.md) transforme n’importe quel téléphone iOS ou Android en informations d’identification fortes, sans mot de passe. Vous pouvez la télécharger gratuitement sur Google Play ou l’App Store d’Apple. Demandez aux utilisateurs de [télécharger l’application Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) et de suivre les instructions fournies pour activer la connexion par téléphone.

### <a name="technical-considerations"></a>Considérations techniques

**Services de fédération Active Directory (AD FS)**  : quand un utilisateur active les informations d’identification sans mot de passe de Microsoft Authenticator, l’authentification de cet utilisateur consiste par défaut à envoyer une notification pour approbation. Les utilisateurs d’un locataire hybride ne peuvent pas être redirigés vers AD FS pour se connecter, sauf s’ils sélectionnent « Utiliser votre mot de passe à la place ». Ce processus contourne également les stratégies d’accès conditionnel locales ainsi que les flux d’authentification directe (PTA). Toutefois, si un paramètre login_hint est spécifié, l’utilisateur est transféré vers AD FS et contourne l’option permettant d’utiliser les informations d’identification sans mot de passe.

**Serveur Azure MFA** : les utilisateurs finaux pour lesquels l’authentification multifacteur est activée via le serveur Azure MFA local d’une organisation, peuvent créer et utiliser une seule instance d’informations d’identification de connexion par téléphone sans mot de passe. Si l’utilisateur tente de mettre à niveau plusieurs installations (5 ou plus) de l’application Microsoft Authenticator avec les informations d’identification, ce changement peut entraîner une erreur.

> [!IMPORTANT]
> Depuis le 1er juillet 2019, Microsoft ne propose plus MFA Server pour les nouveaux déploiements. Les nouveaux clients qui souhaitent imposer une authentification multifacteur lors des événements de connexion doivent utiliser la fonctionnalité Azure AD Multi-Factor Authentication basée sur le cloud. Les clients existants qui ont activé le serveur MFA avant le 1er juillet 2019 peuvent télécharger la dernière version, les futures mises à jour et générer des informations d’identification d’activation comme d’habitude. Nous vous recommandons de passer du serveur Azure MFA à Azure Active Directory MFA.

**Inscription de l’appareil** : pour permettre l’utilisation de l’application Authenticator dans le cadre d’une authentification sans mot de passe, l’appareil doit être inscrit dans le locataire Azure AD et ne doit pas être un appareil partagé. Un appareil ne peut être inscrit que dans un seul locataire. Cette limite signifie qu’un seul compte professionnel ou scolaire est pris en charge pour la connexion par téléphone à l’aide de l’application Microsoft Authenticator.

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Déployer la connexion par téléphone avec l’application Microsoft Authenticator

Suivez les étapes décrites dans l’article [Activer la connexion sans mot de passe avec l’application Microsoft Authenticator](howto-authentication-passwordless-phone.md) pour activer l’application Microsoft Authenticator en tant que méthode d’authentification sans mot de passe dans votre organisation.

### <a name="testing-microsoft-authenticator-app"></a>Test de l’application Microsoft Authenticator

Voici des exemples de cas de test pour l’authentification sans mot de passe, avec l’application Microsoft Authenticator :

| Scénario| Résultats attendus |
| - |-|
| L’utilisateur peut inscrire l’application Microsoft Authenticator| L’utilisateur peut inscrire l’application à partir de https://aka.ms/mysecurityinfo |
| L’utilisateur peut activer la connexion par téléphone| Connexion par téléphone configurée pour le compte professionnel |
| L’utilisateur peut accéder à une application avec une connexion par téléphone| L’utilisateur procède au processus de connexion par téléphone et accède à l’application. |
| Testez la restauration de l’inscription de la connexion par téléphone en désactivant la connexion sans mot de passe de Microsoft Authenticator. Effectuez cette opération dans l’écran Méthodes d’authentification du portail Azure AD| Les utilisateurs autorisés précédemment ne peuvent pas utiliser la connexion sans mot de passe à partir de Microsoft Authenticator. |
| Suppression de la connexion par téléphone depuis l’application Microsoft Authenticator| Le compte professionnel n’est plus disponible sur Microsoft Authenticator |


### <a name="troubleshoot-phone-sign-in"></a>Résoudre les problèmes de la connexion par téléphone


| Scénario| Solution |
| - |-|
| L’utilisateur ne peut pas effectuer d’inscription combinée.| Vérifiez que [l’inscription combinée](concept-registration-mfa-sspr-combined.md) est activée. |
| L’utilisateur ne peut pas activer la connexion par téléphone avec l’application Authenticator.| Assurez-vous que l’utilisateur se trouve dans l’étendue du déploiement. |
| L’utilisateur n’est PAS dans l’étendue de l’authentification sans mot de passe, mais l’option de connexion sans mot de passe lui est proposée, sans qu’il puisse parvenir à l’utiliser.| Se produit quand l’utilisateur a activé la connexion par téléphone dans l’application avant la création de la stratégie. Pour activer la connexion, ajoutez l’utilisateur à un groupe d’utilisateurs disposant de la connexion sans mot de passe. Pour bloquer la connexion : demandez à l’utilisateur de supprimer ses informations d’identification de l’application. |


## <a name="plan-for-and-deploy-fido2-compliant-security-keys"></a>Planifier et déployer des clés de sécurité conformes à FIDO2

Activez les clés de sécurité compatibles. Voici une liste de [fournisseurs de clés de sécurité FIDO2](concept-authentication-passwordless.md), qui proposent des clés compatibles avec l’expérience d’authentification sans mot de passe.

### <a name="plan-security-key-lifecycle"></a>Planifier le cycle de vie des clés de sécurité

Préparez et planifiez le cycle de vie des clés.

**Distribution des clés** : planifiez le provisionnement des clés pour votre organisation. Vous pouvez opter pour un processus de provisionnement centralisé, ou autoriser les utilisateurs finals à acheter des clés compatibles FIDO 2.0.

 **Activation des clés** : les utilisateurs finaux doivent activer eux-mêmes la clé de sécurité. Ils inscrivent leurs clés de sécurité sur [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) et activent le deuxième facteur (code confidentiel ou biométrie) à la première utilisation. Les nouveaux utilisateurs peuvent se servir de la fonctionnalité Passe d’accès temporaire pour inscrire leurs informations de sécurité.

 **Désactivation d’une clé** : si un administrateur souhaite supprimer une clé FIDO2 associée à un compte d’utilisateur, il peut le faire en supprimant la clé de la méthode d’authentification de l’utilisateur, comme indiqué ci-dessous. Pour plus d’informations, consultez [Désactiver une clé](howto-authentication-passwordless-security-key.md#disable-a-key)

 

**Émettre une nouvelle clé** : l’utilisateur peut inscrire la nouvelle clé FIDO2 en accédant à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 

### <a name="technical-considerations"></a>Considérations techniques

Il existe trois types de déploiements de connexion sans mot de passe disponibles avec des clés de sécurité :

* Applications web Azure AD sur un navigateur pris en charge

* Appareils Windows 10 joints à Azure AD

* Appareils Windows 10 à jonction hybride Azure AD 

  * Donne accès aux ressources informatiques et locales. Pour plus d’informations sur l’accès aux ressources locales, consultez [Authentification unique auprès de ressources locales à l’aide de clés FIDO2](howto-authentication-passwordless-security-key-on-premises.md).

**Pour les applications web Azure AD et les appareils Windows joints à Azure AD**, utilisez :

* Windows 10 version 1809 ou ultérieure utilisant un navigateur pris en charge, comme Microsoft Edge ou Mozilla Firefox (version 67 ou ultérieure).

* Windows 10 version 1809 prend en charge la connexion FIDO2 et peut nécessiter le déploiement d’un logiciel du fabricant de la clé FIDO2. Nous vous recommandons d’utiliser la version 1903 ou une version ultérieure.

**Pour les appareils joints à un domaine dans une topologie Azure AD Hybride**, utilisez : 

* Windows 10 version 2004 ou ultérieure. 

* Serveurs de domaine entièrement corrigés exécutant Windows Server 2016 ou 2019. 

* Dernière version d’Azure AD Connect.

#### <a name="enable-windows-10-support"></a>Activer la prise en charge de Windows 10

L’activation de la connexion Windows 10 à l’aide des clés de sécurité FIDO2 nécessite l’activation de la fonctionnalité de fournisseur d’informations d’identification dans Windows 10. Choisissez l’un des éléments suivants :

* [Activer le fournisseur d’informations d’identification avec Intune](howto-authentication-passwordless-security-key-windows.md)

  * Nous recommandons le déploiement d’Intune.

* [Activer le fournisseur d’informations d’identification avec un package d’approvisionnement](howto-authentication-passwordless-security-key-windows.md)

  * Si le déploiement Intune n’est pas possible, les administrateurs doivent déployer un package sur chaque machine pour activer la fonctionnalité de fournisseur d’informations d’identification. L’installation du package peut être effectuée à l’aide de l’une des options suivantes :
    * Stratégie de groupe ou Configuration Manager
    * Installation locale sur une machine Windows 10

* [Activer le fournisseur d’informations d’identification avec une stratégie de groupe](howto-authentication-passwordless-security-key-windows.md)

   * Uniquement prise en charge pour les appareils joints à Azure AD Hybride.

#### <a name="enable-on-premises-integration"></a>Activer l’intégration locale

Suivez les étapes décrites dans l’article [Activer la connexion avec clé de sécurité sans mot de passe à des ressources locales (préversion)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT] 
> Vous devez également effectuer ces étapes pour permettre aux appareils à jonction hybride Azure AD d’utiliser les clés de sécurité FIDO2 dans le cadre d’une connexion Windows 10.


### <a name="key-restrictions-policy"></a>Stratégie d’application de restrictions aux clés

Quand vous déployez la clé de sécurité, vous pouvez éventuellement restreindre l’utilisation des clés FIDO2 à des fabricants spécifiques approuvés par votre organisation. La restriction des clés nécessite un AAGUID (GUID d’attestation d’authentificateur). [Il existe deux façons d’obtenir votre AAGUID](howto-authentication-passwordless-security-key.md#security-key-authenticator-attestation-guid-aaguid).

![Comment appliquer des restrictions aux clés](media/howto-authentication-passwordless-deployment/security-key-enforce-key-restriction.png)


Si la clé de sécurité est restreinte, et si l’utilisateur tente d’inscrire la clé de sécurité FIDO2, il reçoit l’erreur suivante :

![Erreur de clé de sécurité quand la clé est restreinte](media/howto-authentication-passwordless-deployment/security-key-restricted-error.png)


Si l’AAGUID est restreint une fois que l’utilisateur a inscrit la clé de sécurité, le message suivant s’affiche :

![Fenêtre visible par l’utilisateur quand l’AAGUID est restreint](media/howto-authentication-passwordless-deployment/security-key-block-user-window.png)


*Clé FIDO2 bloquée par la stratégie de restriction des clés

### <a name="deploy-fido2-security-key-sign-in"></a>Déployer la connexion par clé de sécurité FIDO2

Suivez les étapes décrites dans l’article [Activer la connexion par clé de sécurité sans mot de passe](howto-authentication-passwordless-security-key.md) pour activer la clé de sécurité FIDO2 en tant que méthode d’authentification sans mot de passe dans votre organisation. 

### <a name="testing-security-keys"></a>Test des clés de sécurité

Voici les exemples de cas de test pour l’authentification sans mot de passe avec des clés de sécurité.

#### <a name="passwordless-fido-sign-in-to-azure-active-directory-joined-windows-10-devices"></a>Connexion FIDO sans mot de passe pour des appareils Windows 10 joints à Azure Active Directory


| Scénario (build Windows)| Résultats attendus |
| - |-|
| L’utilisateur peut inscrire l’appareil FIDO2 (1809)| L’utilisateur peut inscrire l’appareil FIDO2 en passant par Paramètres > Comptes > Options de connexion > Clé de sécurité |
| L’utilisateur peut réinitialiser l’appareil FIDO2 (1809)| L’utilisateur peut réinitialiser l’appareil FIDO2 à l’aide du logiciel du fabricant |
| L’utilisateur peut se connecter avec l’appareil FIDO2 (1809)| L’utilisateur peut sélectionner Clé de sécurité dans la fenêtre de connexion et se connecter avec succès. |
| L’utilisateur peut inscrire l’appareil FIDO2 (1903)| L’utilisateur peut inscrire l’appareil FIDO2 en passant par Paramètres > Comptes > Options de connexion > Clé de sécurité |
| L’utilisateur peut réinitialiser l’appareil FIDO2 (1903)| L’utilisateur peut réinitialiser l’appareil FIDO2 en passant par Paramètres > Comptes > Options de connexion > Clé de sécurité |
| L’utilisateur peut se connecter avec l’appareil FIDO2 (1903)| L’utilisateur peut sélectionner Clé de sécurité dans la fenêtre de connexion et se connecter avec succès. |


#### <a name="passwordless-fido-sign-in-to-azure-ad-web-apps"></a>Connexion FIDO sans mot de passe pour des applications web Azure AD


| Scénario| Résultats attendus |
| - |-|
| L’utilisateur peut inscrire l’appareil FIDO2 sur aka.ms/mysecurityinfo par l’intermédiaire de Microsoft Edge| L’inscription doit s’être déroulée correctement |
| L’utilisateur peut inscrire l’appareil FIDO2 sur aka.ms/mysecurityinfo par l’intermédiaire de Firefox| L’inscription doit s’être déroulée correctement |
| L’utilisateur peut se connecter à OneDrive en ligne avec l’appareil FIDO2 par l’intermédiaire de Microsoft Edge| La connexion doit s’être déroulée correctement |
| L’utilisateur peut se connecter à OneDrive en ligne avec l’appareil FIDO2 par l’intermédiaire de Firefox| La connexion doit s’être déroulée correctement |
| Test de restauration de l’inscription de l’appareil FIDO2 en désactivant les clés de sécurité FIDO2 dans la fenêtre Méthodes d’authentification du portail Azure Active Directory| Les utilisateurs : <li> Sont invités à se connecter à l’aide de leur clé de sécurité. <li> Se connectent correctement et voient s’afficher l’erreur suivante : « Votre stratégie d’entreprise vous oblige à utiliser une autre méthode de connexion ». <li>Peuvent sélectionner une autre méthode et se connecter correctement. Fermez la fenêtre et connectez-vous de nouveau pour vérifier qu’ils n’affichent pas le même message d’erreur. |


### <a name="troubleshoot-security-key-sign-in"></a>Résoudre les problèmes de connexion par clé de sécurité

| Scénario| Solution |
| - | -|
| L’utilisateur ne peut pas effectuer d’inscription combinée.| Vérifiez que [l’inscription combinée](concept-registration-mfa-sspr-combined.md) est activée. |
| L’utilisateur ne peut pas ajouter de clé de sécurité dans ses [paramètres de sécurité](https://aka.ms/mysecurityinfo).| Vérifiez que les [clés de sécurité](howto-authentication-passwordless-security-key.md) sont activées. |
| L’utilisateur ne peut pas ajouter de clé de sécurité dans les options de connexion Windows 10.| [Vérifiez que les clés de sécurité pour la connexion Windows](concept-authentication-passwordless.md) sont activées |
| **Message d’erreur** : Nous avons détecté que ce navigateur ou ce système d’exploitation ne prend pas en charge les clés de sécurité FIDO2.| Les appareils de sécurité FIDO2 sans mot de passe ne peuvent être inscrits que dans les navigateurs pris en charge (Microsoft Edge, Firefox version 67) sur Windows 10 version 1809 ou ultérieure. |
| **Message d’erreur** : La stratégie de votre entreprise vous demande d’utiliser une autre méthode pour vous connecter.| Vérifiez que les clés de sécurité sont activées dans le locataire. |
| L’utilisateur ne peut pas gérer ma clé de sécurité sur Windows 10 version 1809| La version 1809 vous demande d’utiliser le logiciel de gestion de clés de sécurité fourni par le fournisseur de clés FIDO2. Contactez le fournisseur pour obtenir de l’aide. |
| Je pense que ma clé de sécurité FIDO2 peut être défectueuse, comment la tester.| Accédez à [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), entrez les informations d’identification d’un compte de test, connectez la clé de sécurité suspecte, sélectionnez le bouton + en haut à droite de l’écran, sélectionnez Créer, puis suivez le processus de création. Si ce scénario échoue, votre appareil est peut-être défectueux. |

## <a name="manage-passwordless-authentication"></a>Gérer l’authentification sans mot de passe

Pour gérer les méthodes d’authentification sans mot de passe de l’utilisateur dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte d’utilisateur, puis sélectionnez Méthodes d’authentification.

### <a name="microsoft-graph-apis"></a>API Microsoft Graph 

Vous pouvez également gérer les méthodes d’authentification sans mot de passe à l’aide de l’API des méthodes d’authentification dans Microsoft Graph. Par exemple :

* Vous pouvez récupérer les détails de la clé de sécurité FIDO2 d’un utilisateur, et les supprimer si cet utilisateur a perdu sa clé.

* Vous pouvez récupérer les détails liés à l’inscription de Microsoft Authenticator pour un utilisateur, et les supprimer si cet utilisateur a perdu son téléphone.

* Gérez vos stratégies de méthode d’authentification pour les clés de sécurité et l’application Microsoft Authenticator.

Pour plus d’informations sur les méthodes d’authentification pouvant être gérées dans Microsoft Graph, consultez la [Vue d’ensemble de l’API des méthodes d’authentification Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

### <a name="rollback"></a>Restauration

Bien que l’authentification sans mot de passe soit une fonctionnalité légère, avec un impact minimal sur les utilisateurs finals, il peut s’avérer nécessaire d’effectuer une restauration.

Pour effectuer la restauration, l’administrateur doit se connecter au portail Azure, sélectionner les méthodes d’authentification forte souhaitées et affecter la valeur Non à l’option d’activation. Ce processus désactive la fonctionnalité sans mot de passe pour tous les utilisateurs.

![Restauration sans mot de passe](media/howto-authentication-passwordless-deployment/passwordless-rollback.png)

Les utilisateurs qui ont déjà inscrit des appareils de sécurité FIDO2 sont invités à utiliser l’appareil de sécurité à leur prochaine connexion, mais l’erreur suivante s’affiche :

![Fenêtre d’erreur pour la restauration du mot de passe](media/howto-authentication-passwordless-deployment/passswordless-rollback-error-window.png)

### <a name="reporting-and-monitoring"></a>Création de rapports et surveillance

Azure AD propose des rapports fournissant des insights techniques et commerciaux. Demandez à vos propriétaires d’applications métier et techniques de s’attribuer la propriété de ces rapports et de les consommer en fonction des exigences de votre organisation.

Le tableau ci-dessous fournit quelques exemples de scénarios de création de rapports typiques :

| Gérer le risque.| Augmenter la productivité| Gouvernance et conformité| other|
|-|-|-|-|
| Types de rapports| Méthodes d’authentification - utilisateurs enregistrés pour l’inscription à la sécurité combinée| Méthodes d’authentification - utilisateurs inscrits pour la notification d’application| Connexions : vérifier qui accède au locataire et comment |
| Actions potentielles| Utilisateurs cibles pas encore inscrits| Favoriser l’adoption de l’application Microsoft Authenticator ou des clés de sécurité| Révoquer l’accès ou appliquer des stratégies de sécurité supplémentaires pour les administrateurs |


#### <a name="track-usage-and-insights"></a>Effectuer le suivi de l’utilisation et des insights

Azure AD ajoute des entrées aux journaux d’audit dans les cas suivants :

* Un administrateur apporte des modifications dans la section Méthodes d’authentification.

* Un utilisateur apporte des changements de n’importe quel genre aux informations d’identification dans Azure AD.

* Un utilisateur active ou désactive son compte sur une clé de sécurité, ou réinitialise le deuxième facteur de la clé de sécurité sur sa machine Windows 10. Consultez les ID d’événements 4670 et 5382.

**Azure AD conserve la plupart des données d’audit pendant 30 jours** et rend les données disponibles via l’API ou le portail d’administration Azure afin que vous puissiez les télécharger dans vos systèmes d’analyse. Si vous avez besoin d’une conservation plus longue, exportez et consommez les journaux dans un outil SIEM tel que [Microsoft Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou Sumo Logic. Nous recommandons une conservation plus longue pour l’audit, l’analyse des tendances et les autres besoins métier, le cas échéant

Il existe deux onglets dans le tableau de bord d’activité des méthodes d’authentification : Inscription et Utilisation.

L’onglet [Inscription](https://portal.azure.com/) montre le nombre d’utilisateurs pouvant effectuer une authentification sans mot de passe ainsi que d’autres méthodes d’authentification. Cet onglet affiche deux graphes :

* Utilisateurs inscrits en fonction de la méthode d’authentification.

* Inscriptions récentes en fonction de la méthode d’authentification.

![Onglet Inscription permettant de voir les méthodes d’authentification](media/howto-authentication-passwordless-deployment/monitoring-registration-tab.png)

L’onglet [Utilisation](https://portal.azure.com/) montre les connexions en fonction de la méthode d’authentification.

![Onglet Utilisation permettant de voir les méthodes d’authentification](media/howto-authentication-passwordless-deployment/monitoring-usage-tab.png)

Pour plus d’informations, consultez les indications sur le [suivi des méthodes d’authentification inscrites et de leur utilisation dans l’organisation Azure AD](howto-authentication-methods-activity.md).

#### <a name="sign-in-activity-reports"></a>Rapports d’activité de connexion

Utilisez le [rapport d’activité de connexion](../reports-monitoring/concept-sign-ins.md) afin d’effectuer le suivi des méthodes d’authentification utilisées pour se connecter aux différentes applications. 

Sélectionnez la ligne de l’utilisateur, puis sélectionnez l’onglet **Détails d’authentification** pour voir la méthode d’authentification utilisée ainsi que l’activité de connexion correspondante.

![Rapport d’activité de connexion](media/howto-authentication-passwordless-deployment/reporting-sign-in-activity.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le fonctionnement de l’authentification par mot de passe](concept-authentication-passwordless.md)

* [Déployer d’autres fonctionnalités d’identité](../fundamentals/active-directory-deployment-plans.md)
