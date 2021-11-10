---
title: Filtrer votre journal d’audit Azure AD
description: Dans ce démarrage rapide, vous apprendrez à filtrer les entrées dans votre journal d’audit Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/11/2021
ms.author: markvi
author: MarkusVi
manager: karenhoran
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51bc001c5f6984e855adb11b1bdb7c938d2d5783
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995499"
---
# <a name="quickstart-filter-your-azure-ad-audit-log"></a>Démarrage rapide : filtrer votre journal d’audit Azure AD 

Avec les informations de votre journal d’audit dans Azure AD, vous accédez aux enregistrements des activités du système pour la conformité. Ce démarrage rapide montre comment vous pouvez localiser un compte utilisateur nouvellement créé dans votre journal d’audit.


## <a name="prerequisites"></a>Prérequis

Pour suivre le scénario décrit dans ce démarrage rapide, vous avez besoin de ce qui suit :

- **Accès à un locataire Azure AD** : si vous n’avez pas accès à un locataire Azure AD, consultez [Créez votre compte gratuit Azure dès aujourd’hui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- **Un compte d’essai nommé Isabella Simonsen** : si vous ignorez comment créer un compte d’essai, voir [Ajouter des utilisateurs basés sur le cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="find-the-new-user-account"></a>Rechercher le nouveau compte d'utilisateur

Cette section explique comment filtrer votre journal d'audit.


**Pour rechercher le nouvel utilisateur :**

1. Accédez au [journal d’audit](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

2. Pour afficher uniquement les enregistrements pour Isabella Simonsen :

    a. Dans la barre d’outils, cliquez sur **Ajouter des filtres**.
    
    ![Ajouter un filtre d’utilisateur](./media/quickstart-analyze-sign-in/add-filters.png)   

    b. Dans la liste **Choisir un champ**, sélectionnez **Cible**, puis cliquez sur **Appliquer**

    c. Dans la zone de texte **Cible**, entrez le **nom d’utilisateur principal** d’**Isabella Simonsen**, puis cliquez sur **Appliquer**.

3. Cliquez sur l’élément filtré.

    ![Éléments filtrés](./media/quickstart-filter-audit-log/audit-log-list.png)  

4.  Examinez les **détails du journal d'audit**.
 
    ![Détails du journal d'audit](./media/quickstart-filter-audit-log/audit-log-details.png)  
 
  

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez l’utilisateur de test. Si vous ignorez comment supprimer un utilisateur Azure AD, voir [Supprimer des utilisateurs d’Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des rapports Azure Active Directory](overview-reports.md)
