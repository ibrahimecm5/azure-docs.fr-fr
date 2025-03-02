---
title: Renforcer la résilience à l’aide des états des appareils dans Azure Active Directory
description: Guide permettant aux architectes et aux administrateurs informatiques de créer une résilience à l’aide des états des appareils
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9c22974ec3452df9597999eb62569a08eb85dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754403"
---
# <a name="build-resilience-with-device-states"></a>Renforcer la résilience grâce aux états des appareils

En activant les [états des appareils](../devices/overview.md) avec Azure AD, les administrateurs peuvent créer des [stratégies d’accès conditionnel](../conditional-access/overview.md) qui contrôlent l’accès aux applications en fonction de l’état de l’appareil. L’autre avantage des appareils est qu’ils satisfont aux exigences d’authentification forte pour l’accès aux ressources, ce qui réduit les demandes d’authentification MFA supplémentaires et améliore la résilience. 

L’organigramme suivant présente les différentes méthodes d’intégration des appareils dans Azure AD qui activent les états des appareils. Vous pouvez en utiliser plusieurs dans votre organisation.

![organigramme pour le choix des états des appareils](./media/resilience-with-device-states/admin-resilience-devices.png)

Lorsque vous utilisez les [états des appareils](../devices/overview.md), les utilisateurs rencontrent, dans la plupart des cas, une authentification unique pour accéder aux ressources par le biais d’un [jeton d’actualisation principal](../devices/concept-primary-refresh-token.md) (PRT). Le PRT contient des revendications relatives à l’utilisateur et à l’appareil et peut être utilisé pour obtenir des jetons d’authentification afin d’accéder aux applications à partir de l’appareil. Le PRT est valide pendant 14 jours et est renouvelé en continu tant que l’utilisateur utilise activement l’appareil, ce qui permet aux utilisateurs de bénéficier d’une expérience résiliente. Un PRT peut également recevoir une revendication d’authentification multifacteur de plusieurs façons. Pour plus d’informations, consultez [Dans quels cas les PRT reçoivent-ils une revendication MFA](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Comment les états des appareils sont-ils utiles ?

Lorsqu’un PRT est utilisé pour demander l’accès à une application, ses revendications d’appareil, de session et d’authentification multifacteur sont approuvées par Azure AD. Lorsque les administrateurs créent des stratégies qui requièrent un contrôle basé sur l’appareil ou un contrôle par authentification multifacteur, l’exigence de la stratégie peut être satisfaite par le biais de l’état de son appareil sans tentative d’authentification multifacteur. Les utilisateurs ne verront pas d’invites d’authentification multifacteur supplémentaires sur le même appareil. Cela augmente la résilience face à une interruption du service Azure MFA ou de ses dépendances, par exemple les fournisseurs locaux de télécommunications.

## <a name="how-do-i-implement-device-states"></a>Comment implémenter les états des appareils ?

* Activez la [jointure hybride Azure AD](../devices/hybrid-azuread-join-plan.md) et la [jointure Azure AD](../devices/azureadjoin-plan.md) pour les appareils Windows appartenant à l’entreprise, et exigez qu’ils soient joints si possible. Si ce n’est pas possible, exigez qu’ils soient inscrits.

  S’il existe des versions antérieures de Windows dans votre organisation, mettez à niveau ces appareils pour utiliser Windows 10.

* Normalisez l’accès des utilisateurs au navigateur pour utiliser soit [Microsoft Edge](/deployedge/microsoft-edge-security-identity), soit Google Chrome avec les [extensions](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) [prises en charge](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) qui permettent une authentification unique transparente pour les applications web utilisant le PRT.

* Pour les appareils iOS et Android personnels ou appartenant à l’entreprise, déployez l’[application Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc). Outre les capacités de connexion par authentification multifacteur et sans mot de passe, l’application Microsoft Authenticator active l’authentification unique sur une application native via [l’authentification répartie](../develop/msal-android-single-sign-on.md) avec moins d’invites d’authentification pour les utilisateurs finaux.

* Pour les appareils iOS et Android personnels ou appartenant à l’entreprise, utilisez la [gestion des applications mobiles](/mem/intune/apps/app-management) pour accéder en toute sécurité aux ressources de l’entreprise avec moins de demandes d’authentification. 

* [Utilisez le plug-in Microsoft Enterprise Single Sign-On pour les appareils Apple (préversion)](../develop/apple-sso-plugin.md). Celui-ci inscrit l’appareil et fournit une authentification unique sur les différents navigateurs et les applications Azure AD natives. 

## <a name="next-steps"></a>Étapes suivantes
Ressources de résilience pour les administrateurs et les architectes
 
* [Renforcer la résilience grâce à la gestion des informations d’identification](resilience-in-credentials.md)

* [Renforcer la résilience en utilisant Évaluation continue de l’accès (CAE)](resilience-with-continuous-access-evaluation.md)

* [Renforcer la résilience de l’authentification des utilisateurs externes](resilience-b2b-authentication.md)

* [Renforcer la résilience de votre authentification hybride](resilience-in-hybrid.md)

* [Renforcer la résilience de l’accès aux applications avec Proxy d’application](resilience-on-premises-access.md)


Ressources de résilience pour les développeurs

* [Renforcer la résilience de l’IAM dans vos applications](resilience-app-development-overview.md)

* [Renforcer la résilience de vos systèmes CIAM](resilience-b2c.md)