---
title: Utilisation des paramètres gérés par Microsoft pour la stratégie Méthodes d’authentification – Azure Active Directory
description: Découvrez comment utiliser les paramètres gérés par Microsoft pour Microsoft Authenticator.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4134998bd3c7967d5ec151b900d67364373a2659
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566901"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>Guide pratique pour utiliser les paramètres gérés par Microsoft – Stratégie Méthodes d’authentification

En plus de configurer les paramètres de la stratégie Méthodes d’authentification comme **Activés** ou **Désactivés**, les administrateurs informatiques peuvent en paramétrer certains de sorte qu’ils soient **Gérés par Microsoft**. Un paramètre configuré comme **Géré par Microsoft** peut être activé ou désactivé par Azure AD. 

## <a name="settings-that-can-be-microsoft-managed"></a>Paramètres qui peuvent être gérés par Microsoft

Le tableau suivant donne la liste des paramètres qui peuvent être définis sur Géré par Microsoft et indique s’ils sont activés ou désactivés. 

| Paramètre         | Configuration |
|-----------------|---------------|
| [Campagne d’inscription](how-to-nudge-authenticator-app.md)  | Désactivé      |
| Correspondance de nombre        | Désactivé      |
| Contexte supplémentaire  | Désactivé      |

## <a name="next-steps"></a>Étapes suivantes

[Méthodes d’authentification dans Azure Active Directory - Application Microsoft Authenticator](concept-authentication-authenticator-app.md)
