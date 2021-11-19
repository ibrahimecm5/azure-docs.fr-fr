---
title: Connecter des données Azure Active Directory à Microsoft Sentinel | Microsoft Docs
description: Découvrez comment collecter des données à partir d’Azure Active Directory et diffuser des journaux de connexion, d’audit et de provisionement Azure AD dans Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fa4673a573138ac3ccbac533e980f36eb148c6f6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518068"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-microsoft-sentinel"></a>Connecter des données Azure Active Directory (Azure AD) à Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Comme indiqué ci-dessous, certains des types de journaux disponibles sont actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Vous pouvez utiliser le connecteur intégré de Microsoft Sentinel pour collecter des données à partir d’[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) et de les diffuser en continu vers Microsoft Sentinel. Le connecteur vous permet de diffuser les types de journaux suivants :

- Les [**journaux de connexion**](../active-directory/reports-monitoring/concept-all-sign-ins.md), qui contiennent des informations sur les connexions utilisateur interactives où un utilisateur fournit un facteur d’authentification.

    Le connecteur Azure AD inclut maintenant les trois catégories supplémentaires de journaux de connexion, qui se trouvent toutes en **PRÉVERSION** :
    
    - Les [**journaux de connexions utilisateur non interactives**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), qui contiennent des informations sur les connexions effectuées par un client pour le compte d’un utilisateur sans interaction ou facteur d’authentification de la part de l’utilisateur.
    
    - Les [**journaux de connexion du principal du service**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), qui contiennent des informations sur les connexions des applications et des principaux de service qui n’impliquent aucun utilisateur. Dans ces connexions, l’application ou le service fournit des informations d’identification pour son propre compte afin de s’authentifier ou d’accéder à des ressources.
    
    - Les [**journaux de connexion avec une identité managée**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), qui contiennent des informations sur les connexions des ressources Azure dont les secrets sont gérés par Azure. Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

- Les [**journaux d’audit**](../active-directory/reports-monitoring/concept-audit-logs.md), qui contiennent des informations sur l’activité système relative à la gestion des utilisateurs et des groupes, aux applications gérées et aux activités de l’annuaire.

- Les [**journaux de provisionnement**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (également en **PRÉVERSION**), qui contiennent des informations sur l’activité du système sur les utilisateurs, les groupes et les rôles provisionnés par le service de provisionnement Azure AD. 


## <a name="prerequisites"></a>Prérequis

- Une licence Azure Active Directory P1 ou P2 est nécessaire pour ingérer les journaux de connexion dans Microsoft Sentinel. Toute licence Azure AD (Gratuit/O365/P1/P2) est suffisante pour ingérer les autres types de journaux. Des frais supplémentaires par gigaoctet peuvent s’appliquer pour Azure Monitor (Log Analytics) et Microsoft Sentinel.

- Le rôle Contributeur Microsoft Sentinel doit être attribué à votre utilisateur sur l’espace de travail.

- Les rôles Administrateur général ou Administrateur de sécurité doivent être attribués à votre utilisateur sur le locataire à partir duquel vous souhaitez diffuser les journaux.

- Votre utilisateur doit disposer d’autorisations en lecture et en écriture sur les paramètres de diagnostic Azure AD pour pouvoir voir l’état de la connexion. 

## <a name="connect-to-azure-active-directory"></a>Connexion à Azure Active Directory

1. Dans Microsoft Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Azure Active Directory**, puis sélectionnez **Ouvrir la page du connecteur**.

1. Activez les cases à cocher associées aux types de journaux que vous souhaitez diffuser dans Microsoft Sentinel (voir ci-dessus), puis sélectionnez **Se connecter**.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous la section **LogManagement**, dans les tables suivantes :

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Pour interroger les journaux Azure AD, entrez le nom de table approprié en haut de la fenêtre de requête.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Active Directory à Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
