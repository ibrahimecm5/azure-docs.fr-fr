---
title: Changer de locataire de répertoire avec vos abonnements individuels à VSS Azure
description: Changez de locataire de répertoire avec vos abonnements Azure.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: f2d1bb309a841beac4149e48ed48dcfa21f27c34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097619"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Changer de locataire de répertoire avec vos abonnements Azure  

Les organisations peuvent avoir plusieurs abonnements de crédit Azure. Chaque abonnement configuré par une organisation est associé à un locataire [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).  

Azure AD est le service informatique de gestion des identités et des accès de Microsoft. Il permet à vos employés de se connecter et d’accéder aux ressources internes et externes.  

Vous pouvez être amené à modifier le locataire Active Directory dans lequel vous travaillez ou à [transférer votre abonnement vers un autre locataire Active Directory](../../role-based-access-control/transfer-subscription.md).  

Lors de l’activation de votre abonnement, votre identité est créée en fonction de l’adresse e-mail que vous utilisez. Soit cette identité est associée au locataire Active Directory de votre organisation, soit un nouveau locataire de répertoire est créé pour cette identité. Vous pouvez voir l’identité que vous utilisez dans l’angle supérieur droit de votre portail Azure.  

![Capture d’écran du portail Azure avec l’identité mise en évidence dans le coin supérieur droit.](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "L’identité de connexion se trouve dans le coin supérieur droit de votre portail Azure.")  

À partir de là, vous pouvez soit changer d’identité, soit changer de répertoire. Vous devrez peut-être changer d’identité pour accéder à certains répertoires.  

Si l’identité sous laquelle vous êtes connecté est associée à plusieurs répertoires, changez en sélectionnant « Changer de répertoire ». Vous verrez les répertoires auxquels votre identité actuelle est associée.  

![Capture d’écran de la fenêtre Répertoire et abonnement dans Portail Azure.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "Changez de répertoire en cliquant sur l’option Changer de répertoire. Choisissez le répertoire de votre choix.")  

Votre expérience au sein du portail dépend fortement du répertoire associé à l’identité que vous avez utilisée. Pour changer de locataire de répertoire, un administrateur devra ajouter votre identité en tant qu’utilisateur dans le répertoire cible.  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Importance de changer votre locataire Azure Active Directory  

Lorsque vous configurez votre abonnement de crédit Azure via une licence Visual Studio, vous pouvez utiliser une adresse e-mail professionnelle ou personnelle pour créer votre identité.  

Si vous configurez votre abonnement de crédit à l’aide d’un compte personnel, votre identité et votre travail sont isolés du locataire Active Directory de votre organisation. Supposons que vous travailliez sur une application qui a besoin de son propre abonnement pour être testée et effectuer un apprentissage avant son déploiement. À présent, vous avez besoin d’accéder au travail ou aux références de votre organisation. En changeant le locataire de votre répertoire, vous pouvez accéder aux ressources de votre organisation et permettre à cette dernière d’accéder aux vôtres.  

Vous trouverez ci-dessous un diagramme simple qui illustre les étapes de base à suivre lorsque vous modifiez ou transférez votre abonnement.

![Diagramme illustrant la modification ou le transfert de votre abonnement.](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "Diagramme de ce qui se passe lorsque vous modifiez ou transférez votre abonnement.")  

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

Le niveau et le type de votre accès déterminent ce que vous voyez en fonction de vos informations d’identification. Cet accès peut être accordé à différents niveaux de la hiérarchie de l’organisation. L’accès peut vous être accordé au niveau du répertoire, de l’abonnement ou des groupes de ressources.  

![Capture d’écran des niveaux d’accès Azure.](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Les niveaux d’accès disponibles dans Azure.")  

Vous pouvez afficher et gérer vos niveaux d’accès dans Access Control. Vous pouvez également gérer l’accès d’autres utilisateurs à l’abonnement en fonction de vos niveaux d’accès.  

![Capture d’écran de la page de contrôle d’accès de Visual Studio Subscription.](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "Gérez l’accès à votre abonnement.")

## <a name="how-to-change-your-azure-directory-tenant"></a>Comment changer votre locataire Azure Directory

Pour accéder à un autre locataire Active Directory, vous avez besoin d’un compte actif disposant des autorisations et de l’accès nécessaires pour changer de locataire de répertoire. Un administrateur au sein du locataire de répertoire auquel vous souhaitez accéder peut vous ajouter comme :

* Utilisateur
* Invité  

Une fois que vous avez été ajouté et que vous avez reçu les autorisations appropriées, vous pouvez changer de répertoire dans votre abonnement.  

1. Connectez-vous et sélectionnez l’abonnement que vous voulez utiliser dans la [page Abonnements du portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)  
2. Sélectionnez « Changer de répertoire ».  

    ![Capture d’écran d’une page de l’abonnement de l’entreprise Contoso avec l’option Changer de répertoire mise en évidence.](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "Sélectionnez Changer de répertoire.") 
3. Une zone s’affiche pour vous permettre de choisir le nouveau répertoire.  
4. Sélectionnez « Changer ».  

    > [!NOTE]
    > Si vous n’avez pas accès au répertoire cible, il n’apparaîtra pas. Une fois que vous avez changé de répertoire pour l’abonnement, vous recevez un message de confirmation.  

    ![Capture d’écran de la page de validation de l’opération Changer de répertoire.](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "Sélectionnez le répertoire dans la liste déroulante et cliquez sur le bouton Changer.")
5. Sélectionnez « Changer de répertoire » sur la page de l’abonnement pour accéder au nouveau répertoire.  

  ![Capture d’écran de la page des abonnements avec l’option Changer de répertoire mise en évidence.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "Cliquez sur Changer de répertoire pour accéder au nouveau répertoire.")

Vous pouvez également accéder à un répertoire cible et changer votre location en vous rendant dans le répertoire cible et en ajoutant un administrateur. Suivez [ces instructions](/visualstudio/subscriptions/cloud-admin.md) pour ajouter un administrateur à votre abonnement. Une fois cette opération effectuée, l’administrateur a accès aux deux répertoires et peut changer le répertoire du locataire pour vous.  
