---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 96d6668a25422824f0e5ddb5f3fea65f7f9daabb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571441"
---
1. Dans le [portail Azure](https://portal.azure.com), créez une application logique vide.

2. Dans le Concepteur d’applications logiques, entrez *github* comme filtre.

3. Sélectionnez le connecteur GitHub et le déclencheur à utiliser.

   ![Sélectionner le connecteur GitHub et un déclencheur](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Tous les flux de travail d’application logique doivent démarrer avec un déclencheur. Vous ne pouvez sélectionner des actions que lorsque votre flux de travail logique démarre déjà avec un déclencheur. 

4. Si vous n’avez pas créé de connexion, choisissez **Se connecter** afin de pouvoir fournir vos informations d’identification GitHub lorsque vous y êtes invité.

   ![Se connecter avec des informations d’identification GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Votre application logique utilise ces informations d’identification pour autoriser la connexion et l’accès aux données pour votre compte GitHub. 

5. Indiquez votre nom d’utilisateur et votre mot de passe GitHub, puis confirmez votre autorisation.

   ![Fournir des informations d’identification et confirmer l’autorisation](./media/connectors-create-api-github/github-connector-authorize.png)   

   Votre connexion est maintenant créée dans le portail Azure et est prête à être utilisée.

6. Continuez à définir votre flux de travail d’application logique.

   ![Ajouter des actions supplémentaires à votre flux de travail d’application logique](./media/connectors-create-api-github/github-connector-logic-app.png)
