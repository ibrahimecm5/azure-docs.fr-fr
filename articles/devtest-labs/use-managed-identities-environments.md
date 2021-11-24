---
title: Utiliser des identités managées Azure pour déployer des environnements
description: Découvrez comment utiliser des identités managées dans Azure pour déployer des environnements dans un labo dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 220937d20c63420501e3ef6bb1b6c5f8a820613d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400316"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Utiliser des identités managées Azure pour déployer des environnements dans un labo 

En tant que propriétaire de labo, vous pouvez utiliser une identité managée pour déployer des environnements dans un labo. Cette fonctionnalité est utile dans les scénarios où l’environnement contient des ressources Azure ou fait référence à des ressources Azure qui ne font pas partie du groupe de ressources de l’environnement. Ces ressources comprennent les coffres de clés, les galeries d’images partagées et les réseaux. Les identités managées permettent de créer des environnements de bac à sable qui ne sont pas limités au groupe de ressources de cet environnement. 

Par défaut, lorsque vous créez un environnement, le laboratoire crée une identité affectée par le système lors du déploiement du modèle Azure Resource Manager (modèle ARM). L’identité affectée par le système accède aux ressources et services Azure au nom d’un utilisateur du laboratoire. DevTest Labs crée une identité affectée par le système par défaut la première fois qu’il crée l’environnement lab. En savoir plus sur [les raisons pour lesquelles un lab crée une identité affectée par le système](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity). 

En tant que propriétaire du laboratoire, vous pouvez choisir d’accorder à l’identité affectée par le système du laboratoire des autorisations d’accès aux ressources Azure en dehors du laboratoire. Vous pouvez également utiliser votre identité affectée par l’utilisateur pour le scénario. L’identité affectée par le système du lab n’est valide que pendant la durée de vie du lab. L’identification affectée par le système est supprimée lorsque vous supprimez le lab. Lorsque vous avez des environnements dans plusieurs labs qui doivent utiliser une identité, envisagez d’utiliser une identité affectée par l’utilisateur.  

> [!NOTE]
> Actuellement, une seule identité attribuée par l’utilisateur est prise en charge par labo. 

## <a name="prerequisites"></a>Conditions préalables requises

- [Créez, répertoriez, supprimez ou attribuez un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Vérifiez que votre identité managée a été créée dans la même région et le même abonnement que votre labo. L’identité managée ne doit pas nécessairement se trouver dans le même groupe de ressources.

## <a name="use-azure-portal"></a>Utiliser le portail Azure

Dans cette section, vous, en tant que propriétaire de labo, utilisez le portail Azure pour ajouter une identité managée par l’utilisateur au labo. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **DevTest Labs**.
1. Dans la liste des laboratoires, sélectionnez le laboratoire souhaité.
1. Sélectionnez **Configuration et stratégies** -> **Identité (Préversion)** . 
1. Pour ajouter une identité affectée par l’utilisateur, sélectionnez l’onglet **Affectée par l’utilisateur**.
1. Appuyez sur **Ajouter**.
1. Dans la liste déroulante, sélectionnez un utilisateur existant que vous avez créé ou auquel vous avez accès.
 
    ![Ajouter une identité managée par l’utilisateur](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Appuyez sur **Enregistrer** en haut de la page.

    Une fois cette identité enregistrée, le labo l’utilise lors du déploiement de tous les environnements de labo. Vous pouvez également accéder à la ressource d’identité dans Azure en sélectionnant l’identité dans la liste. 

Le propriétaire du laboratoire ne doit rien faire de particulier pour déployer un environnement. L’identité ajoutée au laboratoire doit avoir des autorisations pour les ressources externes auxquelles l’environnement doit accéder. 

Pour changer l’identité managée par l’utilisateur attribuée au labo, supprimez d’abord l’identité attachée au labo, puis ajoutez-lui en une autre. Pour supprimer une identité attachée au labo, sélectionnez les points de suspension (**...**), puis cliquez sur **Supprimer**. 

## <a name="use-api"></a>Utiliser une API

1. Après avoir créé une identité, notez l’ID de ressource de cette identité. Il doit ressembler à l’exemple suivant : 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. Exécutez une méthode HTTPS sur la ressource lab pour ajouter une identité affectée par l’utilisateur ou activer une identité affectée par le système pour le lab.

   > [!NOTE]
   > Que vous ayez créé une identité affectée par l’utilisateur ou non, le lab crée automatiquement une identité affectée par le système la première fois qu’un environnement lab est créé. Toutefois, si une identité affectée par l’utilisateur est déjà configurée pour le lab, le service lab DevTest continue à utiliser cette identité pour déployer les environnements lab. 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
Une fois que vous avez ajouté l’identité affectée par l’utilisateur au laboratoire, le service DevTest Labs utilise cette identité lors du déploiement des environnements Azure Resource Manager. Par exemple, si vous avez besoin que votre modèle Resource Manager accède à une image de galerie d’images partagées, vérifiez que l’identité dispose des autorisations requises pour la ressource de galerie d’images partagées. 
