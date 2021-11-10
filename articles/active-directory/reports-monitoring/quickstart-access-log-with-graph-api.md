---
title: Accéder aux journaux d’Azure AD avec l’API Microsoft Graph | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à accéder au journal des connexions à l’aide de l’API Graph.
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/03/2021
ms.author: markvi
author: MarkusVi
manager: karenhoran
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bf12b1fd54ee8d3a7461418c2b8828070dfc926
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997302"
---
# <a name="quickstart-access-azure-ad-logs-with-the-microsoft-graph-api"></a>Démarrage rapide : Accéder aux journaux d’Azure AD avec l’API Microsoft Graph 

Les informations contenues dans le journal des connexions Azure AD vous permettent de comprendre la cause de l’échec de la connexion d’un utilisateur. Ce démarrage rapide explique comment accéder au journal des connexions à l’aide de l’API Graph.


## <a name="prerequisites"></a>Prérequis

Pour suivre le scénario décrit dans ce démarrage rapide, vous avez besoin de ce qui suit :

- **Accès à un locataire Azure AD** : si vous n’avez pas accès à un locataire Azure AD, consultez [Créez votre compte gratuit Azure dès aujourd’hui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- **Un compte d’essai nommé Isabella Simonsen** : si vous ignorez comment créer un compte d’essai, voir [Ajouter des utilisateurs basés sur le cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="perform-a-failed-sign-in"></a>Provoquer un échec de connexion

L’objectif de cette étape est de créer un enregistrement d’échec de connexion dans le journal des connexions Azure AD.

**Pour effectuer cette étape :**

1. Connectez-vous à votre [portail Azure](https://portal.azure.com/) en tant que Isabella Simonsen en utilisant un mot de passe incorrect.

2. Attendez 5 minutes pour vous assurer de trouver un enregistrement de la connexion dans le journal des connexions. Pour plus d’informations, consultez [Rapports d’activité](reference-reports-latencies.md#activity-reports).



## <a name="find-the-failed-sign-in"></a>Rechercher l’échec de connexion

Cette section vous fournit les étapes à suivre pour obtenir des informations sur votre connexion à l’aide de l’API Graph.

 ![Requête de l’Afficheur Microsoft Graph](./media/quickstart-access-log-with-graph-api/graph-explorer-query.png)   

**Pour examiner l’échec de connexion :**

1. Accédez à l’[Afficheur Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer).

2. Connectez-vous à votre locataire en tant qu’administrateur général.

    ![Authentification dans l’Afficheur Microsoft Graph](./media/quickstart-access-log-with-graph-api/graph-explorer-authentication.png)   

3. Dans la **liste déroulante des verbes HTTP**, sélectionnez **GET**.

4. Dans la **liste déroulante des versions d’API**, sélectionnez **bêta**.

5. Dans la **barre d’adresse Demande de requête**, saisissez `https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=userDisplayName eq 'Isabella Simonsen'`.
 
6. Cliquez sur **Exécuter la requête**.

Examinez le résultat de votre requête.

 ![Aperçu de la réponse de l’Afficheur Microsoft Graph](./media/quickstart-access-log-with-graph-api/response-preview.png)   


## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur de test. Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des rapports Azure Active Directory](overview-reports.md)
