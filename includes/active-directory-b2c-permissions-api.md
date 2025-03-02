---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: kengaderdus
ms.openlocfilehash: 02aff100840f80a24210df7d653f2fed0059d9a5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038110"
---
#### <a name="app-registrations"></a>[Inscriptions des applications](#tab/app-reg-ga/) 

1. Sélectionnez **Inscriptions d’applications**, puis sélectionnez l’application web qui doit avoir accès à l’API. Par exemple, *webapp1*.
1. Sous **Gérer**, sélectionnez **Autorisations de l’API**.
1. Sous **Autorisations configurées**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez l’onglet **Mes API**.
1. Sélectionnez l’API à laquelle l’application web doit être autorisée à accéder. Par exemple, *webapi1*.
1. Sous **Autorisation**, développez **demo**, puis sélectionnez les étendues que vous avez définies auparavant. Par exemple, *demo.read* et *demo.write*.
1. Sélectionnez **Ajouter des autorisations**.
1. Sélectionnez **Accorder le consentement de l’administrateur pour (nom de votre abonné)** .
1. Si vous êtes invité à sélectionner un compte, sélectionnez le compte administrateur actuellement connecté ou connectez-vous avec un compte de votre locataire Azure AD B2C ayant au minimum le rôle *Administrateur d’application cloud*.
1. Sélectionnez **Oui**.
1. Sélectionnez **Actualiser**, puis vérifiez que la mention « Accordé pour ... » apparaît dans **État** pour les deux étendues.

#### <a name="applications-legacy"></a>[Applications (héritées)](#tab/applications-legacy/)

1. Sélectionnez **Applications (héritées)** , puis sélectionnez l’application web qui doit avoir accès à l’API. Par exemple, *webapp1*.
1. Sélectionnez **Accès aux API**, puis **Ajouter**.
1. Dans la liste déroulante **Sélectionner une API**, sélectionnez l’API à laquelle l’application web doit être autorisée à accéder. Par exemple, *webapi1*.
1. Dans la liste déroulante **Sélectionner des étendues**, sélectionnez les étendues que vous avez définies précédemment. Par exemple, *demo.read* et *demo.write*.
1. Sélectionnez **OK**.
