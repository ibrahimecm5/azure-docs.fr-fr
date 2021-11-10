---
title: Analyser les connexions à l’aide du journal des connexions Azure AD
description: Ce démarrage rapide explique comment utiliser le journal des connexions pour déterminer la raison d’un échec de connexion à Azure AD.
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
ms.openlocfilehash: b998a502ba2c3edaae263e5ac1571239f84c3543
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997340"
---
# <a name="quickstart-analyze-sign-ins-with-the-azure-ad-sign-ins-log"></a>Démarrage rapide : Analyser les connexions à l’aide du journal des connexions Azure AD 

Les informations contenues dans le journal des connexions Azure AD vous permettent de comprendre la cause de l’échec de la connexion d’un utilisateur. Ce démarrage rapide montre comment localiser un échec de connexion à l’aide du journal des connexions.


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

Cette section décrit les étapes à suivre pour analyser un échec de connexion :

- **Filtrer les connexions** : supprimez tous les enregistrements non pertinents pour votre analyse. Par exemple, définissez un filtre pour afficher uniquement les enregistrements d’un utilisateur spécifique.
- **Rechercher des informations supplémentaires sur l’erreur** : en plus des informations que vous pouvez trouver dans le journal des connexions, vous pouvez rechercher l’erreur à l’aide de l’[outil de recherche d’erreur de connexion](https://login.microsoftonline.com/error). Cet outil peut vous fournir des informations supplémentaires sur une erreur de connexion. 


**Pour examiner l’échec de connexion :**

1. Accédez au [journal des connexions](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

2. Pour afficher uniquement les enregistrements relatifs à Isabella Simonsen :

    a. Dans la barre d’outils, cliquez sur **Ajouter des filtres**.
    
    ![Ajouter un filtre d’utilisateur](./media/quickstart-analyze-sign-in/add-filters.png)   

    b. Dans la liste **Choisir un champ**, sélectionnez **Utilisateur**, puis cliquez sur **Appliquer**.

    c. Dans la zone de texte **Nom d’utilisateur**, tapez **Isabella Simonsen**, puis cliquez sur **Appliquer**.

    d. Dans la barre d’outils, cliquez sur **Actualiser**.

3. Pour analyser le problème, cliquez sur **Dépannage et assistance**.

    ![Ajouter un filtre](./media/quickstart-analyze-sign-in/troubleshooting-and-support.png)   

4. Copiez le **code d’erreur de connexion**.

    ![Code d’erreur de connexion](./media/quickstart-analyze-sign-in/sign-in-error-code.png)   


5. Collez le code d’erreur dans la zone de texte de l’[outil de recherche d’erreur de connexion](https://login.microsoftonline.com/error), puis cliquez sur **Soumettre**.

Examinez le résultat de l’exécution de l’outil et déterminez s’il fournit des informations supplémentaires.

![Outil de recherche du code d’erreur](./media/concept-all-sign-ins/error-code-lookup-tool.png)


## <a name="additional-tests"></a>Tests supplémentaires

Maintenant que vous savez comment rechercher une entrée par son nom dans le journal des connexions, vous devez également essayer de trouver l’enregistrement à l’aide des filtres suivants :

- **Date** : essayez de trouver Isabella en spécifiant un **début** et une **fin**.

    ![Filtre de date](./media/quickstart-analyze-sign-in/start-and-end-filter.png)

- **État** : essayez de trouver Isabella en utilisant **État : Échec**.

    ![État d’échec](./media/quickstart-analyze-sign-in/status-failure.png)




## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur de test. Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des rapports Azure Active Directory](overview-reports.md)
