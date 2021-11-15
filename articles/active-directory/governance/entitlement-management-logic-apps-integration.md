---
title: Déclencher des applications logiques personnalisées avec la gestion des droits d’utilisation Azure AD
description: Découvrez comment configurer et utiliser des applications logiques personnalisés dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: karenhoran
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 11/02/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdd706be5107558422971336cdb598c1148d6f83
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511404"
---
# <a name="trigger-custom-logic-apps-with-azure-ad-entitlement-management"></a>Déclencher des applications logiques personnalisées avec la gestion des droits d’utilisation Azure AD


[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) permet d’automatiser des flux de travail personnalisés, ainsi que de connecter des applications et des services dans un même emplacement. Les utilisateurs peuvent intégrer des applications logiques avec la gestion des droits d’utilisation pour élargir leurs flux de travail de gouvernance au-delà des cas d’utilisation de base de la gestion des droits d’utilisation.

Ces applications logiques peuvent ensuite être déclenchées pour s’exécuter conformément aux cas d’utilisation de la gestion des droits d’utilisation, par exemple, quand un package d’accès est octroyé ou demandé. Par exemple, un administrateur peut créer et lier une application logique personnalisée à la gestion des droits d’utilisation de sorte que, quand un utilisateur demande un package d’accès, une application logique est déclenchée, qui veille à ce que l’utilisateur se voie également attribuer certaines caractéristiques dans une application SaaS tierce (telle que Salesforce) ou reçoive un e-mail personnalisé.

Les cas d’usage de la gestion des droits d’utilisation qui peuvent être intégrés avec des applications logiques sont les suivants :  

- quand un package d’accès est demandé ;  

- quand une demande de package d’accès est octroyée ;  

- quand une affectation de package d’accès expire.  

Ces déclencheurs d’applications logiques sont contrôlés dans un nouvel onglet au sein de stratégies de package d’accès nommées **règles**. En outre, un onglet **Extensions personnalisées** dans la page Catalogue affiche toutes les applications logiques ajoutées pour un catalogue donné. Cet article explique comment créer et ajouter des applications logiques à des catalogues et packages d’accès dans la gestion des droits d’utilisation.

## <a name="create-and-add-a-logic-app-to-a-catalog-for-use-in-entitlement-management"></a>Créer et ajouter une application logique à un catalogue pour utilisation dans la gestion des droits d’utilisation 

**Rôles prérequis :** administrateur général, administrateur de la gouvernance des identités, propriétaire de catalogue ou propriétaire de groupe de ressources 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Identity Governance**. 

1. Dans le menu de gauche, sélectionnez **Catalogues**. 

1. Dans le menu de gauche, sélectionnez **Extensions personnalisées (préversion)** . 

1. Dans la barre de navigation de l’en-tête, sélectionnez **Ajouter une extension personnalisée**.  

1. Sous l’onglet **De base**, entrez le nom de l’extension personnalisée (application logique liée que vous ajoutez) et la description du flux de travail. Désormais, ces champs s’affichent sous l’onglet **Extensions personnalisées** du catalogue. 

    ![Volet pour créer une extension personnalisée](./media/entitlement-management-logic-apps/create-custom-extension.png)


1. Accédez ensuite à l’onglet **Details**. 

1. Dans le champ « Créer une application logique », sélectionnez **Oui**. Dans le cas contraire, sélectionnez **Non** et passez à l’étape 9 si vous prévoyez d’utiliser une application logique existante. Si vous avez sélectionné Oui, sélectionnez l’une des options ci-dessous et passez à l’étape 9 : 

    1. sélectionnez **Créer une application Azure AD** si vous souhaitez utiliser une nouvelle application comme base pour la nouvelle application logique, ou
    
        ![Volet de sélection d’une nouvelle application pour une application logique](./media/entitlement-management-logic-apps/new-app-selection.png)

    1. sélectionnez **une application Azure AD existante** si vous souhaitez utiliser une application existante comme base pour la nouvelle application logique.
    
        ![Volet de sélection d’une application existante pour une application logique](./media/entitlement-management-logic-apps/existing-app-selection.png)

    > [!Note]    
    > Plus tard, vous pourrez modifier ce que fait votre application logique dans le concepteur d’application logique. Pour ce faire, sélectionnez l’application logique que vous avez créée sous l’onglet **Extensions personnalisées** de la fenêtre **catalogues**.  

1. Ensuite, entrez l’**ID d’abonnement**, le **Groupe de ressources** et le **Nom de l’application logique**. 

1. Enfin, sélectionnez **Valider et créer**. 

1. Examinez le résumé de votre extension personnalisée et assurez-vous que les détails de la légende de votre application logique sont corrects. Sélectionnez ensuite **Créer**.

    ![Exemple de résumé d’extension personnalisée](./media/entitlement-management-logic-apps/custom-extension-summary.png)

1. Cette extension personnalisée de l’application logique liée s’affiche désormais sous l’onglet Extensions personnalisées dans Catalogues. Vous pouvez l’appeler dans des stratégies de package d’accès.


## <a name="edit-a-linked-logic-app"></a>Modifier une application logique liée 

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités ou propriétaire de catalogue 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Identity Governance**. 

1. Dans le menu de gauche, sélectionnez **Catalogues**. 

1. Dans le menu de gauche, sélectionnez **Extensions personnalisées**. 

1. Vous pouvez voir ici toutes les extensions personnalisées (applications logiques) que vous avez ajoutées à ce catalogue. Pour modifier un flux de travail d’application logique, ou créer un flux de travail pour une application logique nouvellement ajoutée, sélectionnez l’extension personnalisée d’application logique sous **Point de terminaison**. Cela a pour effet d’ouvrir le concepteur d’application logique pour vous permettre de créer votre flux de travail.  

 Pour plus d’informations sur la création de flux de travail d’application logique, consultez [Créer des flux de travail automatisés avec Azure Logic Apps dans le portail Azure](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

## <a name="add-custom-extension-to-access-package-policy"></a>Ajouter une extension personnalisée à la stratégie de package d’accès 

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, propriétaire de catalogue ou gestionnaire de package d’accès 

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

1. Dans le portail Azure, sélectionnez **Azure Active Directory**, puis **Identity Governance**. 

1. Dans le menu de gauche, sélectionnez **Packages d’accès**. 

1. Sélectionnez **Nouveau package d’accès** si vous souhaitez ajouter une extension personnalisée (application logique) à un nouveau package d’accès. Vous pouvez également sélectionner le package d’accès auquel vous souhaitez ajouter une extension personnalisée (application logique) dans la liste des packages d’accès qui ont déjà été créés.  

    > [!NOTE]  
    > Pour plus d’informations sur la création d’un package d’accès, consultez [Créer un package d’accès dans la gestion des droits d’utilisation](entitlement-management-access-package-create.md).  Pour plus d’informations sur la modification d’un package d’accès existant, consultez [Modifier les paramètres de demande d’un package d’accès dans la fonctionnalité de gestion des droits d’utilisation Azure AD](entitlement-management-access-package-request-policy.md#open-and-edit-an-existing-policy-of-request-settings). 

1. Dans les paramètres de stratégie du package d’accès, accédez à l’onglet **Règles (préversion)** . 

1. Dans le menu **Quand** ci-dessous, sélectionnez l’événement de package d’accès que vous souhaitez utiliser comme déclencheur pour cette extension personnalisée (application logique). Par exemple, si vous souhaitez déclencher le flux de travail de l’application logique d’extension personnalisée uniquement quand un utilisateur demande le package d’accès, sélectionnez **quand la demande est créée**. 

1. Dans le menu **Faire** ci-dessous, sélectionnez l’extension personnalisée (application logique) que vous souhaitez ajouter au package d’accès. L’action à Faire que vous sélectionnez s’exécute quand l’événement sélectionné dans le champ Quand se produit.  

1. Sélectionnez **Créer** si vous souhaitez ajouter l’extension personnalisée à un nouveau package d’accès. Sélectionnez **Mettre à jour** si vous souhaitez l’ajouter à un package d’accès existant.

    ![Ajouter une application logique à un package d’accès](./media/entitlement-management-logic-apps/add-logic-apps-access-package.png)

## <a name="troubleshooting-and-validation"></a>Résolution des problèmes et validation 

Pour vérifier que votre extension personnalisée a correctement déclenché l’application logique associée quand elle a été appelée par l’option **Faire** du package d’accès, vous pouvez consulter les journaux de l’application logique. 

La page de vue d’ensemble d’une application logique spécifique affiche des horodateurs indiquant quand l’application logique a été exécutée pour la dernière fois. En outre, la vue d’ensemble d’un groupe de ressources avec une extension personnalisée liée indique le nom de cette extension personnalisée dans la vue d’ensemble si elle a été configurée correctement.  

## <a name="next-steps"></a>Étapes suivantes