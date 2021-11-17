---
title: Désactiver PTA avec « Ne pas configurer » d’Azure AD Connect | Microsoft Docs
description: Cet article explique comment désactiver PTA avec la fonctionnalité Azure AD Connect « Ne pas configurer ».
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e58ca9cce30c03467e7171ef29cf1e360fbe02e
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371711"
---
# <a name="disable-pta"></a>Désactiver PTA 

Pour désactiver PTA, suivez les étapes décrites dans les sections [Désactiver PTA lors de l’utilisation d’Azure AD Connect](#disable-pta-when-using-azure-ad-connect) et [Désactivez PTA dans PowerShell](#disable-pta-in-powershell) de cet article.

## <a name="disable-pta-when-using-azure-ad-connect"></a>Désactiver PTA lors de l’utilisation d’Azure AD Connect

Si vous utilisez l’authentification directe avec Azure AD Connect et l’avez définie sur **« Ne pas configurer »** , vous pouvez la désactiver. 

>[!NOTE]
>Si vous avez déjà activé PHS, la désactivation de PTA entraînera le basculement du locataire sur PHS.

Pour désactiver PTA, utilisez les cmdlets suivantes. 

## <a name="prerequisites"></a>Prérequis
Les prérequis suivants sont obligatoires :
- Toute machine Windows sur lequel l’agent PTA est installé. 
- L'agent doit correspondre à la version 1.5.1742.0 ou ultérieure. 
- Un compte d’administrateur général Azure pour exécuter les cmdlets PowerShell et désactiver PTA.

>[!NOTE]
> Un agent antérieur peut ne pas disposer des cmdlets requises pour mener à bien cette opération. Vous pouvez obtenir un nouvel agent depuis le portail Azure, l'installer sur une machine Windows et fournir les informations d’identification d’administrateur. (L’installation de l’agent n’affecte pas l’état PTA dans le cloud)

> [!IMPORTANT]
> Si vous utilisez le cloud Azure Government, vous devez transmettre le paramètre ENVIRONMENTNAME avec la valeur suivante. 
>
>| Nom de l’environnement | Cloud |
>| - | - |
>| AzureUSGovernment | Gouvernement des États-Unis|


## <a name="disable-pta-in-powershell"></a>Désactiver PTA dans PowerShell

À partir d’une session PowerShell, utilisez la commande suivante pour désactiver PTA :

1. PS C:\Program Files\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus`
3. `Disable-PassthroughAuthentication`

## <a name="if-you-dont-have-access-to-an-agent"></a>Si vous n'avez accès à aucun agent

Si vous ne disposez pas de machine agent, vous pouvez utiliser la commande suivante pour installer un agent.

1. Téléchargez l'agent d’authentification le plus récent depuis portal.azure.com.
2. Installez la fonctionnalité : `.\AADConnectAuthAgentSetup.exe` ou `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Étapes suivantes

- [Connexion de l’utilisateur avec l’authentification directe Azure Active Directory](how-to-connect-pta.md)
