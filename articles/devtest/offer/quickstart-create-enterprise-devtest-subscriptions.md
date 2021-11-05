---
title: Création d’abonnements dev/test Azure d’entreprise
description: Créez des abonnements dev/test Azure d’entreprise et organisationnels pour les équipes et les grandes organisations.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 5897e60a2c3c5d46dac48973d78072364af27336
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097453"
---
# <a name="creating-enterprise-and-organization-azure-devtest-subscriptions"></a>Création d’abonnements dev/test Azure d’entreprise et d’organisation

Les abonnements dev/test d’entreprise sont disponibles pour le développement en équipe dans les grandes organisations

- Pour le développement en équipe dans les grandes entreprises  
- Facturation à l’échelle de l’entreprise : aucun paiement séparé  
- Créer un abonnement à une offre dev/test d’entreprise par le biais d’Azure Enterprise Portal  

## <a name="prerequisites"></a>Prérequis

Pour créer un abonnement dev/test Azure d’entreprise, vous devez disposer d’un rôle de propriétaire de compte sur un compte d’inscription pour effectuer l’abonnement. Il existe deux façons d’accéder à ce rôle :  

- L’administrateur d’entreprise de votre inscription peut [faire de vous un propriétaire de compte](../../cost-management-billing/manage/grant-access-to-create-subscription.md).  
- Un propriétaire existant du compte d’inscription [peut vous accorder l’accès](../../cost-management-billing/manage/grant-access-to-create-subscription.md).  

### <a name="important-information-before-you-add-account-owners"></a>Informations importantes avant d’ajouter des propriétaires de comptes

Le propriétaire d’un compte EA **ne peut pas** utiliser le même compte de connexion pour le portail EA et d’autres offres Azure. Supposons que vous avez été ajouté au portail EA en tant que propriétaire de compte et que vous avez utilisé les mêmes informations d’identification que celles utilisées pour vos avantages individuels de Visual Studio. Dans ce cas, cet abonnement Visual Studio est converti en offre Dev/Test EA.  

> [!Note]  
> La première fois que vous vous connectez au portail EA en tant que propriétaire de compte, un message d’avertissement s’affiche. Il est essentiel de le lire et de le comprendre. Vos abonnements existants sont convertis en une offre EA facturable. Si vous êtes un abonné Visual Studio ajouté en tant que propriétaire de compte, vous perdez votre crédit Azure mensuel individuel jusqu’à ce que vous effectuiez une action.

### <a name="to-recover-your-individual-visual-studio-azure-benefits"></a>Pour récupérer vos avantages individuels de Visual Studio Azure  

Une fois que vous avez authentifié votre rôle en tant que propriétaire de compte EA, vous pouvez :  

- Supprimer ou déplacer les abonnements Azure que vous possédez  
- Supprimer votre rôle en tant que propriétaire de compte dans le portail EA  
- Vous réinscrire à vos avantages individuels de Visual Studio Azure  
- Ou supprimer l’abonné du site d’administration Visual Studio dans le centre VLSC et réaffecter l’abonnement, en utilisant une connexion différente, puis vous réinscrire à des avantages de Visual Studio Azure individuels  

## <a name="create-your-subscription"></a>Créer votre abonnement

Pour créer vos abonnements dev/test d’entreprise, commencez par choisir un compte d’inscription en tant que compte dev/test.  

1. Pour commencer, accédez à [Ea.azure.com](https://ea.azure.com). Azure Enterprise Portal vous permet de gérer les inscriptions de votre entreprise.  
2. Sous l’onglet Gérer, sélectionnez le numéro de votre contrat.  

    > [!Note]
    > L’inscription peut changer en fonction du cycle de renouvellement de votre Contrat Entreprise.
    
    ![Capture d’écran de la page du portail de gestion du Contrat Entreprise.](media/quickstart-create-enterprise-devtest-subscriptions/ea-manage-portal.png "Gérez les inscriptions de votre entreprise à partir d’Azure Enterprise Portal.")
    
3. Une fois que vous avez sélectionné le numéro d’inscription approprié, sélectionnez l’onglet « Compte ».  
4. À partir de là, vous pouvez modifier le(s) compte(s) et basculer l’indicateur dev/test sur « Oui ».  

Pour ajouter un compte :  

1. Dans le portail Azure Enterprise, sélectionnez Gérer dans le volet de navigation de gauche.  
2. Sélectionnez l’onglet Compte dans la page Compte, puis sélectionnez + Ajouter un compte.  
3. Vous pouvez choisir un service, si vous le souhaitez.
4. Choisissez le type d’authentification souhaité.  
5. Tapez un nom convivial pour identifier le compte dans les rapports.  
6. Tapez l’adresse e-mail du propriétaire du compte à associer au nouveau compte.  
7. Confirmez l’adresse e-mail, puis cliquez sur Ajouter.  

![Capture d’écran de la page de gestion Azure Enterprise Portal avec sélection de l’option +Ajouter un compte.](media/quickstart-create-enterprise-devtest-subscriptions/add-account.png "Cliquez sur Ajouter un compte.")

## <a name="add-your-azure-enterprise-devtest-subscription"></a>Ajouter votre abonnement dev/test Azure d’entreprise

Une fois que vous avez choisi le compte pour créer un abonnement dev/test Azure d’entreprise, ajoutez l’offre d’abonnement appropriée. Il existe deux manières de procéder. Quel que soit votre choix, l’expérience est la même.  

1. L’onglet Abonnement dans chaque portail comporte un bouton « +/Ajouter ». 
    1. Vous accédez à une page de connexion.
    1. Connectez-vous avec vos informations d’identification Microsoft Azure.
    1. Une fois connecté, vous êtes redirigé vers une page qui liste les offres disponibles. Activez **Oui** dans le portail EA pour voir les offres d’entreprise.
    
1. Sélectionnez l’offre dev/test d’entreprise et créez votre abonnement.
    1. Vous devez disposer d’un accès et des autorisations associées à votre identité.
    1. Vous devez désigner le compte en tant que compte dev/test dans le portail d’inscription.

### <a name="next-steps"></a>Étapes suivantes  

- [Administration du portail Azure EA](../../cost-management-billing/manage/ea-portal-administration.md)
- [Bien démarrer avec le portail Azure Enterprise](../../cost-management-billing/manage/ea-portal-get-started.md)
