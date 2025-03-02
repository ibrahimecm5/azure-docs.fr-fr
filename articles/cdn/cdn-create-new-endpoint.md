---
title: 'Démarrage rapide : Créer un point de terminaison et un profil de réseau de distribution de contenu Azure'
description: Ce démarrage rapide décrit l’activation du réseau de distribution de contenu (Content Delivery Network, CDN) en créant un profil et un point de terminaison CDN.
author: duongau
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: de24b2e9676082da6980c59e696781fc6939f29f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423505"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Démarrage rapide : Créer un profil et un point de terminaison Azure CDN

Dans ce démarrage rapide, vous allez activer le CDN Azure en créant un profil, qui est une collection d’un ou de plusieurs points de terminaison CDN. Après avoir créé un profil et un point de terminaison, vous pouvez commencer à distribuer du contenu à vos clients.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un compte de stockage Azure nommé *cdnstorageacct123*, que vous utilisez pour le nom d’hôte d’origine. Pour remplir cette condition, consultez [Intégrer un compte de stockage Azure à Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un point de terminaison CDN

Une fois que vous avez créé un profil CDN, vous l’utilisez pour créer un point de terminaison.

1. Dans le portail Azure, sélectionnez le profil CDN que vous avez créé dans votre tableau de bord. Si vous ne le trouvez pas, vous pouvez soit ouvrir le groupe de ressources dans lequel vous l’avez créé, soit utiliser la barre de recherche en haut du portail, entrer le nom du profil et sélectionner le profil dans les résultats.
   
1. Dans la page Profil CDN, sélectionnez **+ Point de terminaison**.
   
    ![Profil CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Le volet **Ajouter un point de terminaison** s’affiche.

3. Saisissez les valeurs de paramètre suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Nom** | Entrez *cdn-endpoint-123* comme nom d’hôte de point de terminaison. Ce nom doit être globalement unique à travers Azure. S’il est déjà utilisé, saisissez-en un autre. Ce nom sert à accéder à vos ressources en cache au niveau du domaine _&lt;endpoint name&gt;_ .azureedge.net.|
    | **Type d’origine** | Sélectionnez **Stockage**. | 
    | **Nom d’hôte de l’origine** | Sélectionnez le nom d’hôte du compte de stockage Azure que vous utilisez dans la liste déroulante, par exemple *cdnstorageacct123.blob.core.windows.net*. |
    | **Chemin de l’origine** | Laisser vide. |
    | **En-tête de l’hôte d’origine** | Laissez la valeur par défaut (il s’agit du nom d’hôte d’origine). |  
    | **Protocole** | Laissez les options **HTTP** et **HTTPS** par défaut sélectionnées. |
    | **Port de l’origine** | Laissez les valeurs de port par défaut. | 
    | **Optimisé pour** | Laissez la sélection **Livraison web générale** par défaut. |

    ![Volet Ajouter un point de terminaison](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Sélectionnez **Ajouter** pour créer un point de terminaison. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour le profil.
    
   ![Point de terminaison CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Le temps nécessaire à la propagation du point de terminaison dépend du niveau tarifaire sélectionné lors de la création du profil. **Akamai standard** se termine généralement dans un délai d’une minute, **Microsoft standard** en 10 minutes et **Verizon standard** et **Verizon Premium** dans un délai allant jusqu’à 30 minutes.

> [!NOTE]
> En ce qui concerne les *points de terminaison CDN Verizon*, lorsqu’un point de terminaison est **désactivé** ou **arrêté** pour une raison quelconque, toutes les ressources configurées via le portail supplémentaire Verizon sont nettoyées. Ces configurations ne peuvent pas être restaurées automatiquement en redémarrant le point de terminaison. Vous devrez réappliquer ces modifications de configuration.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé un profil et un point de terminaison CDN au sein d’un groupe de ressources. Enregistrez ces ressources si vous souhaitez passer à la section [Étapes suivantes](#next-steps) et apprendre à ajouter un domaine personnalisé à votre point de terminaison. Toutefois, si vous ne pensez pas utiliser ces ressources à l’avenir, vous pouvez les supprimer en supprimant le groupe de ressources. Vous éviterez ainsi de payer des frais supplémentaires :

1. Dans le menu gauche du portail Azure, sélectionnez **Groupes de ressources**, puis **CDNQuickstart-rg**.

2. Sur la page **Groupe de ressources**, sélectionnez **Supprimer le groupe de ressources**, saisissez *CDNQuickstart-rg* dans la zone de texte, puis sélectionnez **Supprimer**. Cette action supprime le groupe de ressources, le profil et le point de terminaison que vous avez créés dans ce démarrage rapide.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Utiliser CDN pour délivrer du contenu statique à partir d’une application web](cdn-add-to-web-app.md)
