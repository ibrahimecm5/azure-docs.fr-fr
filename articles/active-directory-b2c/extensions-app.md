---
title: Application Extensions dans Azure Active Directory B2C
description: Restauration de l’application b2c-extensions-app.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/02/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f01a8a51f467d9a090847aeea5da0426fe66e163
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424398"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C : application Extensions 

Lors de la création d’un annuaire Azure AD B2C, une application appelée **b2c-extensions-app** est automatiquement créée dans celui-ci. Cette application est visible dans *Inscriptions d’applications*. Elle est utilisée par le service Azure AD B2C pour stocker des informations sur les utilisateurs et les attributs personnalisés. Si l’application est supprimée, Azure AD B2C ne pourra plus fonctionner correctement et votre environnement de production en sera affecté.

> [!IMPORTANT]
> Ne supprimez pas l’application **b2c-extensions-app**, sauf si vous prévoyez de supprimer immédiatement votre locataire. Si l’application reste supprimée pendant plus de 30 jours, les informations utilisateur sont définitivement perdues.

## <a name="verifying-that-the-extensions-app-is-present"></a>Vérification de la présence de l’application Extensions

Pour vérifier que l’application b2c-extensions-app est présente :

1. Dans votre locataire Azure AD B2C, cliquez sur **Tous les services** dans le menu de navigation de gauche.
1. Recherchez et ouvrez **Inscriptions des applications**.
1. Recherchez une application dont le nom commence par **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Récupérer l’application Extensions

Si vous avez supprimé accidentellement b2c-extensions-app, vous avez 30 jours pour la récupérer. Vous pouvez restaurer l’application à l’aide de l’API Graph :

1. Accédez à [https://developer.microsoft.com/en-us/graph/graph-explorer](https://developer.microsoft.com/en-us/graph/graph-explorer).
1. Ouvrez une session en tant qu’administrateur général pour l’annuaire Azure AD B2C dans lequel restaurer l’application supprimée. Cet administrateur général doit avoir une adresse e-mail semblable à la suivante : `username@{yourTenant}.onmicrosoft.com`.
1. Exécutez une requête HTTP GET sur l’URL `https://graph.microsoft.com/beta/directory/deleteditems/microsoft.graph.application`. Cette opération liste toutes les applications qui ont été supprimées au cours des 30 derniers jours.
1. Dans la liste, recherchez l’application dont le nom commence par « b2c-extensions-app », puis copiez sa valeur de propriété `objectid`.
1. Exécutez une requête HTTP POST sur l’URL `https://graph.microsoft.com/beta/directory/deleteditems/{id}/restore`. Remplacez la partie `{id}` de l’URL par le `objectid` de l’étape précédente.

Vous devez maintenant être en mesure de [voir l’application restaurée](#verifying-that-the-extensions-app-is-present) dans le portail Azure.

> [!NOTE]
> Une application ne peut être restaurée que dans les 30 jours suivant sa suppression. Après ce délai, les données sont définitivement perdues. Pour obtenir de l’aide, soumettez un ticket de support.
