---
title: Utiliser Proxy d’application pour intégrer des applications locales avec Defender for Cloud Apps – Azure Active Directory
description: Configurez une application locale dans Azure Active Directory pour utiliser Microsoft Defender for Cloud Apps. Utilisez le contrôle d’application par accès conditionnel Defender for Cloud Apps pour surveiller et contrôler les sessions en temps réel en fonction des stratégies d’accès conditionnel. Vous pouvez appliquer ces stratégies aux applications locales qui utilisent le proxy d’application dans Azure Active Directory (Azure AD).
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: e392eb7158a25423e0302846b434cf4d576effbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331031"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-defender-for-cloud-apps-and-azure-active-directory"></a>Configurer la supervision de l’accès aux applications en temps réel avec Microsoft Defender for Cloud Apps et Azure Active Directory
Configurez une application locale dans Azure Active Directory (Azure AD) pour utiliser Microsoft Defender for Cloud Apps pour la supervision en temps réel. Defender for Cloud Apps utilise le contrôle d’application par accès conditionnel pour surveiller et contrôler les sessions en temps réel en fonction des stratégies d’accès conditionnel. Vous pouvez appliquer ces stratégies aux applications locales qui utilisent le proxy d’application dans Azure Active Directory (Azure AD).

Voici quelques exemples de types de stratégies que vous pouvez créer avec Defender for Cloud Apps :

- Bloquez ou protégez le téléchargement de documents sensibles sur les appareils non gérés.
- Surveillez à quel moment les utilisateurs à haut risque se connectent aux applications, puis consignez leurs actions dans le cadre de la session. Avec ces informations, vous pouvez analyser le comportement de l’utilisateur pour déterminer comment appliquer les stratégies de session.
- Utilisez les certificats clients ou la conformité des périphériques pour bloquer l’accès à des applications spécifiques à partir des appareils non gérés.
- Restreignez les sessions utilisateur des réseaux hors entreprise. Vous pouvez donner un accès restreint aux utilisateurs accédant à une application en dehors de votre réseau d’entreprise. Par exemple, cet accès restreint peut empêcher l’utilisateur de télécharger des documents sensibles.

Pour plus d’informations, consultez [Protéger les applications avec le Contrôle d’accès conditionnel aux applications Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Spécifications

Licence :

- Licence EMS E5 ou
- Azure Active Directory Premium P1 et Defender for Cloud Apps autonome.

Application locale :

- L’application locale doit utiliser Kerberos Constrained Delegation (KCD)

Configurez le proxy d’application :

- Configurez Azure AD pour utiliser le proxy d’application, y compris la préparation de votre environnement et l’installation du connecteur Proxy d’application. Pour accéder à un tutoriel, consultez [Ajouter des applications locales pour un accès à distance via le service Proxy d’application d’Azure AD](../app-proxy/application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Intégrer une application locale dans Azure AD

Intégrez une application locale dans Azure AD. Pour un démarrage rapide, consultez [Intégrer une application locale dans Azure AD](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Lors de l’ajout de l’application, assurez-vous de régler les deux paramètres suivants dans le panneau **Ajouter votre application locale** :

- **Pré-authentification** : Entrez **Azure Active Directory**.
- **Traduisez les URL dans le corps de l’application** : Choisissez **Oui**.

Ces deux paramètres sont nécessaires pour que l’application fonctionne avec Defender for Cloud Apps.

## <a name="test-the-on-premises-application"></a>Tester l’application locale

Après avoir intégré votre application dans Azure AD, effectuez la procédure [Tester l’application](../app-proxy/application-proxy-add-on-premises-application.md#test-the-application) pour ajouter un utilisateur de test et tester la connexion. 

## <a name="deploy-conditional-access-app-control"></a>Déployer le contrôle d’application par accès conditionnel

Pour configurer votre application avec le contrôle d’application à accès conditionnel, suivez les instructions de la section [Déployer le Contrôle d’application par accès conditionnel Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Tester le contrôle d’application par accès conditionnel

Pour tester le déploiement des applications Azure AD avec le contrôle d’application à accès conditionnel, suivez les instructions de la section [Tester le déploiement d’applications Azure AD](/cloud-app-security/proxy-deployment-aad).
