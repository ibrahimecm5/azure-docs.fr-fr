---
title: Rechercher et résoudre les manques dans une couverture d’authentification forte pour vos administrateurs dans Azure Active Directory
description: Découvrir comment rechercher et résoudre les manques dans une couverture d’authentification forte pour vos administrateurs dans Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81d3ce6564c0ed4233f34bc43d661b2d9e1c1388
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505400"
---
# <a name="find-and-address-gaps-in-strong-authentication-coverage-for-your-administrators"></a>Rechercher et résoudre les manques dans une couverture d’authentification forte pour vos administrateurs

Imposer l’authentification multifacteur (MFA) pour les administrateurs de votre locataire est l’une des premières étapes que vous pouvez effectuer pour augmenter la sécurité de votre locataire. Dans cet article, nous allons vous montrer comment vous assurer que tous vos administrateurs sont couverts par l’authentification multifacteur.

## <a name="detect-current-usage-for-azure-ad-built-in-administrator-roles"></a>Détecter l’utilisation actuelle des rôles d’administrateur intégrés Azure AD

Le [score de sécurité Azure AD](../fundamentals/identity-secure-score.md) fournit un score pour **Exiger l’authentification multifacteur pour les rôles d’administration** dans votre locataire. Cette action d’amélioration suit l’utilisation de l’authentification multifacteur par l’administrateur général, l’administrateur de la sécurité, l’administrateur Exchange et l’administrateur SharePoint. 

Il existe différents moyens de vérifier si vos administrateurs sont couverts par une stratégie MFA. 

- Pour résoudre les problèmes de connexion d’un administrateur spécifique, vous pouvez utiliser les journaux de connexion. Les journaux de connexion vous permettent de filtrer les **exigences d’authentification** pour des utilisateurs spécifiques. Toute connexion pour laquelle l’**exigence d’authentification** est une **authentification à un seul facteur** signifie qu’il n’existait aucune stratégie d’authentification multifacteur requise pour la connexion.

  ![Capture d’écran du journal de connexion.](./media/how-to-authentication-find-coverage-gaps/auth-requirement.png)

  Cliquez sur **Détails de l’authentification** pour plus d’[informations sur les exigences MFA](../reports-monitoring/concept-sign-ins.md#authentication-details).
  
  ![Capture d’écran des détails de l’activité d’authentification.](./media/how-to-authentication-find-coverage-gaps/details.png)

- Pour choisir la stratégie à activer en fonction de vos licences utilisateur, nous avons un nouvel Assistant d’activation de MFA pour vous aider à [comparer les stratégies MFA](concept-mfa-licensing.md#compare-multi-factor-authentication-policies) et à déterminer les étapes qui conviennent à votre organisation. L’Assistant montre les administrateurs qui ont été protégés par MFA au cours des 30 derniers jours.

  ![Capture d’écran de l’Assistant d’activation de MFA.](./media/how-to-authentication-find-coverage-gaps/wizard.png)

- Pour créer programmatiquement un rapport listant tous les utilisateurs ayant des rôles d’administrateur dans votre locataire et l’état de leur authentification forte, vous pouvez exécuter un [script PowerShell](https://github.com/microsoft/AzureADToolkit/blob/main/src/Find-UnprotectedUsersWithAdminRoles.ps1). Ce script énumère toutes les attributions de rôles intégrées et personnalisées, permanentes et éligibles, ainsi que les groupes avec des rôles attribués, et recherche les utilisateurs qui ne sont pas inscrits à MFA ou qui ne se connectent pas avec MFA en évaluant leurs méthodes d’authentification et leur activité de connexion.

## <a name="enforce-multi-factor-authentication-on-your-administrators"></a>Appliquer l’authentification multifacteur à vos administrateurs

En fonction des manques que vous avez trouvés, demandez aux administrateurs d’utiliser l’authentification multifacteur de l’une des manières suivantes :

- Si vos administrateurs ont une licence Azure AD Premium, vous pouvez [créer une stratégie d’accès conditionnel](tutorial-enable-azure-mfa.md) pour leur appliquer MFA. Vous pouvez également mettre à jour cette stratégie pour imposer MFA aux utilisateurs qui ont des rôles personnalisés.  

- Exécutez l’[Assistant d’activation MFA](https://aka.ms/MFASetupGuide) pour choisir votre stratégie MFA.

- Si vous attribuez des rôles d’administrateur personnalisés ou intégrés dans [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure), exigez l’authentification multifacteur lors de l’activation des rôles.

## <a name="use-passwordless-and-phishing-resistant-authentication-methods-for-your-administrators"></a>Utiliser des méthodes d’authentification sans mot de passe et résistantes à l’hameçonnage pour vos administrateurs

Une fois que l’authentification multifacteur est appliquée à vos administrateurs et que vous l’utilisez depuis un moment, il est temps de passer au niveau supérieur en termes d’authentification forte et d’utiliser une méthode d’authentification sans mot de passe et résistante à l’hameçonnage : 

- [Connexion par téléphone (avec Microsoft Authenticator)](concept-authentication-authenticator-app.md)
- [FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- [Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview)

Vous pouvez en savoir plus sur ces méthodes d’authentification et leur sécurité dans [Méthodes d’authentification Azure AD](concept-authentication-methods.md).


