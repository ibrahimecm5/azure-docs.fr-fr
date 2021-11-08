---
title: Contrôles d’octroi dans une stratégie d’accès conditionnel - Azure Active Directory
description: Que sont les contrôles d’octroi dans une stratégie d’accès conditionnel Azure AD ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36d6283bd8304a33b80797e502edde45c952e06a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067391"
---
# <a name="conditional-access-grant"></a>Accès conditionnel : Accorder

Dans une stratégie d’accès conditionnel, un administrateur peut utiliser des contrôles d’accès pour accorder ou bloquer l’accès aux ressources.

![Stratégie d’accès conditionnel avec un contrôle d’octroi nécessitant l’authentification multifacteur](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Bloquer l’accès

Le contrôle de blocage prend en compte les affectations existantes et empêche l’accès en fonction de la configuration de la stratégie d’accès conditionnel.

Le blocage est un puissant contrôle qui doit être utilisé moyennant les connaissances appropriées. Les stratégies dotées d’instructions de blocage peuvent avoir des effets secondaires inattendus. Des opérations appropriées de test et de validation sont essentielles avant l’activation à grande échelle. Les administrateurs doivent utiliser des outils tels que le [mode rapport seul d’accès conditionnel](concept-conditional-access-report-only.md) et [l’outil What If dans l’accès conditionnel](what-if-tool.md) lorsqu’ils apportent des modifications.

## <a name="grant-access"></a>Accorder l'accès

Les administrateurs peuvent choisir d’appliquer un ou plusieurs contrôles lors de l’octroi de l’accès. Ces contrôles incluent les options suivantes : 

- [Exiger une authentification multifacteur (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Exiger que l’appareil soit marqué comme conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Exiger un appareil joint à une version hybride d’Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Demander une application cliente approuvée](app-based-conditional-access.md)
- [Exiger une stratégie de protection des applications](app-protection-based-conditional-access.md)
- [Exiger la modification du mot de passe](#require-password-change)

Si les administrateurs souhaitent combiner ces options, ils peuvent choisir les méthodes suivantes :

- Demander tous les contrôles sélectionnés (contrôle **ET** contrôle)
- Demander un des contrôles sélectionnés (contrôle **OU** contrôle)

Par défaut, l’accès conditionnel exige tous les contrôles sélectionnés.

### <a name="require-multi-factor-authentication"></a>Exiger une authentification multifacteur

Quand cette case est cochée, les utilisateurs doivent effectuer une authentification Azure AD Multi-Factor Authentication. Pour plus d’informations sur le déploiement d’Azure AD Multi-Factor Authentication (MFA), consultez l’article [Planifier un déploiement informatique d’Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md).

[Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-overview) présente la configuration requise pour une authentification multifacteur dans des stratégies d’accès conditionnel. 

### <a name="require-device-to-be-marked-as-compliant"></a>Exiger que l’appareil soit marqué comme conforme

Les organisations qui ont déployé Microsoft Intune peuvent utiliser les informations retournées par leurs appareils pour identifier les appareils qui remplissent les conditions de conformité spécifiques. Ces informations de conformité de la stratégie sont transmises d’Intune à Azure AD, où l’accès conditionnel peut prendre des décisions pour accorder ou bloquer l’accès aux ressources. Pour plus d’informations sur les stratégies de conformité, consultez l’article [Définir des règles sur les appareils pour autoriser l’accès aux ressources de votre organisation à l’aide d’Intune](/intune/protect/device-compliance-get-started).

Un appareil peut être marqué comme conforme par Intune (pour n’importe quel système d’exploitation d’appareil), ou par un système GPM tiers pour les appareils Windows 10. Une liste des systèmes MDM tiers pris en charge est disponible dans l’article [Prendre en charge les partenaires tiers de conformité des appareils dans Intune](/mem/intune/protect/device-compliance-partners).

Les appareils doivent être inscrits dans Azure AD pour pouvoir être marqués comme conformes. Pour plus d’informations sur l’inscription des appareils, consultez l’article [Qu’est-ce qu’une identité d’appareil ?](../devices/overview.md)

**Remarques**

- L’exigence **Exiger que l’appareil soit marqué comme conforme** présente les caractéristiques suivantes :
   - Elle ne prend en charge que les appareils Windows actuels (Windows 10 et versions ultérieures), iOS, Android et macOS inscrits auprès d’Azure AD et d’Intune.
   - Pour les appareils inscrits auprès de systèmes MDM tiers, consultez [Prise en charge des partenaires tiers de conformité des appareils dans Intune](/mem/intune/protect/device-compliance-partners).
   - L’accès conditionnel ne peut pas considérer Microsoft Edge en mode InPrivate comme un appareil conforme.


### <a name="require-hybrid-azure-ad-joined-device"></a>Exiger un appareil joint à Azure AD hybride

Les organisations peuvent choisir d’utiliser l’identité de l’appareil dans le cadre de leur stratégie d’accès conditionnel. Elles peuvent utiliser cette case à cocher pour exiger que les appareils soient joints à Azure AD hybride. Pour plus d’informations sur les identités d’appareils, consultez l’article [Qu’est-ce qu’une identité d’appareil ?](../devices/overview.md)

Lorsque le [flux OAuth de code d’appareil](../develop/v2-oauth2-device-code.md) est utilisé, ni le contrôle Exiger une autorisation d’appareil géré ni la condition d’état d’appareil ne sont pris en charge. En effet, l’appareil qui effectue l’authentification ne peut pas fournir son état à celui qui fournit un code, et l’état de l’appareil dans le jeton est verrouillé sur celui qui effectue l’authentification. Utilisez plutôt le contrôle Exiger l’autorisation d’authentification multifacteur.

**Remarques**

- L’exigence **Exiger un appareil à jointure hybride Azure AD** présente les caractéristiques suivantes :
   - Elle ne prend en charge que les Windows joints à un domaine de niveau inférieur (avant Windows 10) et les appareils Windows actuels (Windows 10 et versions ultérieures).
   - L’accès conditionnel ne peut pas considérer Microsoft Edge en mode InPrivate comme un appareil à jointure hybride Azure AD.

### <a name="require-approved-client-app"></a>Demander une application cliente approuvée

Les organisations peuvent exiger que toute tentative d’accès aux applications cloud sélectionnées provienne d’une application cliente approuvée. Ces applications clientes approuvées prennent en charge les [stratégies de protection des applications Intune](/intune/app-protection-policy), quelle que soit votre solution de gestion des périphériques mobiles (GPM).

Pour appliquer ce contrôle d’octroi, l’accès conditionnel exige que l’appareil soit inscrit dans Azure Active Directory, ce qui requiert l’utilisation d’une application de répartiteur. L’application de répartiteur peut être Microsoft Authenticator pour iOS ou bien Microsoft Authenticator ou le portail d’entreprise Microsoft pour appareils Android. Si aucune application de répartiteur n’est installée sur l’appareil lorsque l’utilisateur tente de s’authentifier, l’utilisateur est redirigé vers le magasin d’applications approprié pour installer l’application de répartiteur requise.

La prise en charge de ce paramètre est confirmée pour les applications clientes suivantes :

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Lists
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Planificateur Microsoft
- Microsoft Power Apps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Entreprise
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Tableau blanc collaboratif Microsoft
- Administration Microsoft 365

**Remarques**

- Les applications clientes approuvées prennent en charge la fonctionnalité de gestion des applications mobiles Intune.
- Exigence **Nécessite une application cliente approuvée** :
   - elle prend uniquement en charge iOS et Android pour la condition de plateforme d’appareil.
   - Une application de répartiteur est nécessaire pour inscrire l’appareil. L’application de répartiteur peut être Microsoft Authenticator pour iOS ou bien Microsoft Authenticator ou le portail d’entreprise Microsoft pour appareils Android.
- L’accès conditionnel ne peut considérer Microsoft Edge en mode InPrivate en tant qu'application cliente approuvée.
- L’utilisation du proxy d’application Azure AD pour permettre à l’application mobile Power BI de se connecter à un serveur Power BI Report Server local n’est pas prise en charge avec les stratégies d’accès conditionnel qui nécessitent l’application Microsoft Power BI comme application cliente approuvée.

Consultez l’article [Guide pratique pour exiger des applications clientes approuvées pour l’accès aux applications cloud avec l’accès conditionnel](app-based-conditional-access.md) donnant des exemples de configuration.

### <a name="require-app-protection-policy"></a>Exiger une stratégie de protection des applications

Dans votre stratégie d’accès conditionnel, vous pouvez exiger qu’une [stratégie de protection des applications Intune](/intune/app-protection-policy) soit présente sur l’application cliente pour qu’il soit possible d’accéder aux applications cloud sélectionnées. 

Pour appliquer ce contrôle d’octroi, l’accès conditionnel exige que l’appareil soit inscrit dans Azure Active Directory, ce qui requiert l’utilisation d’une application de répartiteur. L’application de répartiteur peut être Microsoft Authenticator pour iOS ou le portail d’entreprise Microsoft pour les appareils Android. Si aucune application de répartiteur n’est installée sur l’appareil lorsque l’utilisateur tente de s’authentifier, l’utilisateur est redirigé vers l’App Store pour installer cette application.

Les applications doivent disposer du kit **SDK Intune** avec une **assurance de stratégie** implémentée et répondre à certaines exigences supplémentaires pour prendre en charge ce paramètre. Les développeurs qui implémentent des applications avec le kit SDK Intune peuvent trouver plus d’informations sur ces exigences dans la documentation du kit SDK.

La prise en charge de ce paramètre est confirmée pour les applications clientes suivantes :

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Listes Microsoft (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Planificateur Microsoft
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Teams
- Microsoft Word
- MultiLine for Intune
- Nine Mail - Email & Calendar

> [!NOTE]
> Microsoft Kaizala, Microsoft Skype Entreprise et Microsoft Visio ne prennent pas en charge l’octroi **Exiger une stratégie de protection des applications**. Si vous avez besoin que ces applications fonctionnent, utilisez exclusivement l’octroi **Exiger une stratégie de protection des applications**. L’utilisation de la clause `or` entre les deux octrois ne fonctionne pas pour ces trois applications.

**Remarques**

- Les applications associées à la stratégie de protection des applications prennent en charge la fonctionnalité de gestion d’applications mobiles Intune avec la protection des stratégies.
- Exigences relatives à la stratégie **Exiger une stratégie de protection des applications** :
    - elle prend uniquement en charge iOS et Android pour la condition de plateforme d’appareil.
    - Une application de répartiteur est nécessaire pour inscrire l’appareil. Sur iOS, l’application de répartiteur est Microsoft Authenticator, sur Android, il s’agit de l’application Portail d’entreprise Intune.

Consultez l’article [Guide pratique pour exiger une stratégie de protection d’application et une application cliente approuvée pour l’accès aux applications cloud avec l’accès conditionnel](app-protection-based-conditional-access.md) donnant des exemples de configuration.

### <a name="require-password-change"></a>Nécessite une modification du mot de passe 

Lorsqu’un risque utilisateur est détecté, en s’appuyant sur les conditions de la stratégie de risque utilisateur, les administrateurs peuvent choisir de faire modifier le mot de passe de manière sécurisée à l’aide de la réinitialisation de mot de passe en libre-service Azure AD. Si un risque utilisateur est détecté, les utilisateurs peuvent effectuer la réinitialisation du mot de passe en libre-service pour une correction automatique ; ce processus fermera l’événement utilisateur à risque afin d’éviter toute perturbation inutile pour les administrateurs. 

Lorsqu'un utilisateur est invité à modifier son mot de passe, il doit d'abord procéder à une authentification multifacteur. Vous devez vous assurer que tous vos utilisateurs se sont inscrits à l’authentification multifacteur afin qu'ils soient prêts au cas où un risque serait détecté pour leur compte.  

> [!WARNING]
> Les utilisateurs doivent avoir déjà été inscrits pour la réinitialisation du mot de passe libre-service avant de déclencher la stratégie de risque utilisateur. 

Il existe quelques restrictions lorsque vous configurez une stratégie utilisant le contrôle des modifications de mot de passe :  

1. La stratégie doit être affectée à « toutes les applications cloud ». Cette exigence empêche un attaquant d’utiliser une autre application pour modifier le mot de passe de l’utilisateur et réinitialiser le risque du compte, en se connectant à une autre application. 
1. L’exigence de modification du mot de passe ne peut pas être utilisée avec d’autres contrôles, comme l’exigence d’un appareil conforme.  
1. Le contrôle de modification du mot de passe ne peut être utilisé qu’avec la condition d’affectation d’utilisateurs et de groupes, la condition d’affectation d’applications cloud (qui doit être définie sur « tous ») et les conditions de risque utilisateur. 

### <a name="terms-of-use"></a>Conditions d’utilisation

Si votre organisation a créé des Conditions d’utilisation, d’autres options peuvent être visibles sous les contrôles d’octroi. Ces options permettent aux administrateurs de demander l’acceptation des conditions d’utilisation comme condition d’accès aux ressources protégées par la stratégie. Pour plus d’informations sur les conditions d’utilisation, consultez l’article [Conditions d’utilisation d’Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : Contrôles de session](concept-conditional-access-session.md)

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

- [Mode Rapport uniquement](concept-conditional-access-report-only.md)
