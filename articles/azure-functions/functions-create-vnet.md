---
title: Utiliser des points de terminaison privés pour intégrer Azure Functions à un réseau virtuel
description: Ce tutoriel pas à pas vous montre comment connecter une fonction à un réseau virtuel Azure et le verrouiller avec des points de terminaison privés.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 8eaf46cdab44fcf4ebdb69564cd05f07f8f88f1b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850030"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Tutoriel : intégrer des Azure Functions à un réseau virtuel Azure à l’aide de points de terminaison privés

Ce tutoriel vous montre comment utiliser Azure Functions pour vous connecter aux ressources d’un réseau virtuel Azure en utilisant des points de terminaison privé. Vous allez créer une fonction à l’aide d’un compte de stockage verrouillé derrière un réseau virtuel. Le réseau virtuel utilise un déclencheur de file d’attente Service Bus.

Dans ce didacticiel, vous allez :

> [!div class="checklist"]
> * Créez une application de fonction sur le plan Premium.
> * Créez des ressources Azure (Service Bus, compte de stockage, réseau virtuel).
> * Verrouillez votre compte de stockage derrière un point de terminaison privé.
> * Verrouillez votre Service Bus derrière un point de terminaison privé.
> * Déployez une application de fonction qui utilise à la fois les déclencheurs Service Bus et HTTP.
> * Verrouillez votre application de fonction derrière un point de terminaison privé.
> * Vérifiez que votre application de fonction est sécurisée dans le réseau virtuel.
> * Supprimer des ressources.

## <a name="create-a-function-app-in-a-premium-plan"></a>Créer une application de fonction sur le plan Premium

Vous allez créer une application de fonction .NET dans le plan Premium, car ce tutoriel utilise C#. D’autres langages sont également pris en charge dans Windows. Le plan Premium offre une mise à l’échelle serverless tout en prenant en charge l’intégration du réseau virtuel.

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, sélectionnez **Calcul** > **Application de fonction**.

1. Dans la page **De base**, utilisez les paramètres d’application de fonction comme indiqué dans le tableau ci-dessous.

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction est créée. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre application de fonction. |
    | **Nom de l’application de fonction** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a-z` (insensible à la casse), `0-9`et `-`.  |
    |**Publier**| Code | Choix de publier des fichiers de code ou un conteneur Docker. |
    | **Pile d’exécution** | .NET | Ce tutoriel utilise .NET. |
    |**Région**| Région recommandée | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |

1. Sélectionnez **Suivant : Hébergement**. Dans la page **Hébergement**, entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Compte de stockage](../storage/common/storage-account-create.md)** |  Nom globalement unique |  Créez un compte de stockage utilisé par votre application de fonction. Les noms des comptes de stockage doivent comprendre entre 3 et 24 caractères. Ils peuvent contenir uniquement des chiffres et des lettres minuscules. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](./storage-considerations.md#storage-account-requirements). |
    |**Système d’exploitation**| Windows | Ce tutoriel utilise Windows. |
    | **[Plan](./functions-scale.md)** | Premium | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Par défaut, lorsque vous sélectionnez **Premium**, un plan App Service est créé. La valeur par défaut pour **Sku and size** est **EP1**, où *EP* signigie _elastic premium_. Pour plus d’informations, consultez la liste de [références SKU Premium](./functions-premium-plan.md#available-instance-skus).<br/><br/>Lorsque vous exécutez des fonctions JavaScript dans un plan Premium, choisissez une instance qui comporte moins de processeurs virtuels. Pour plus d’informations, consultez [Choisir des plans Premium à un cœur](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Sélectionnez **Suivant : Supervision**. Dans la page **Supervision**, entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Default | Créez une ressource Application Insights avec le même nom d’application dans la région prise en charge la plus proche. Développez ce paramètre si vous devez modifier le **nom de la nouvelle ressource** ou stockez vos données dans un **Emplacement** différent dans une [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Sélectionnez **Vérifier + créer** pour passer en revue les sélections de configuration d’application.

1. Dans la page **Vérifier + créer**, passez en revue vos paramètres. Sélectionnez ensuite **Créer** pour provisionner et déployer l’application de fonction.

1. Cliquez sur l’icône **Notifications** dans l’angle supérieur droit du portail pour voir le message **Déploiement réussi**.

1. Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application de fonction. Vous pouvez également sélectionner **Épingler au tableau de bord**. L’épinglage permet de revenir plus facilement à cette ressource d’application de fonction à partir de votre tableau de bord.

Félicitations ! Vous avez créé votre application de fonction Premium avec succès !

## <a name="create-azure-resources"></a>Créer des ressources Azure

Vous allez ensuite créer un compte de stockage, un Service Bus et un réseau virtuel. 
### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Vos réseaux virtuels auront besoin d’un compte de stockage distinct de celui que vous avez créé avec votre application de fonction.

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Sur la page **Nouveau**, recherchez le *compte de stockage*. Sélectionnez ensuite **Créer**.

1. Dans l’onglet **De base**, utilisez le tableau suivant pour configurer les paramètres du compte de stockage. Tous les autres paramètres peuvent utiliser les valeurs par défaut.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | mysecurestorage| Le nom du compte de stockage auquel le point de terminaison privé sera appliqué. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | La région dans laquelle vous avez créé votre application de fonction. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

### <a name="create-a-service-bus"></a>Créer un Service Bus

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, recherchez *Service Bus*. Sélectionnez ensuite **Créer**.

1. Dans l’onglet **De base**, utilisez le tableau suivant pour configurer les paramètres du Service Bus. Tous les autres paramètres peuvent utiliser les valeurs par défaut.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. |
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom de l’espace de noms** | myServiceBus| Nom du Service Bus auquel le point de terminaison privé sera appliqué. |
    | **[Emplacement](https://azure.microsoft.com/regions/)** | myFunctionRegion | La région dans laquelle vous avez créé votre application de fonction. |
    | **Niveau tarifaire** | Premium | Choisissez ce niveau pour utiliser des points de terminaison privés avec Azure Service Bus. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

### <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Les ressources Azure de ce tutoriel s’intègrent à ou sont placées dans un réseau virtuel. Vous utiliserez des points de terminaison privés pour limiter le trafic réseau dans le réseau virtuel.

Le tutoriel crée deux sous-réseaux :
- **par défaut** : sous-réseau pour les points de terminaison privés. Les adresses IP privées sont fournies à partir de ce sous-réseau.
- **fonctions**: sous-réseau pour l’intégration du réseau virtuel Azure Functions. Ce sous-réseau est délégué à l’application de fonction.

Créez le réseau virtuel dans lequel s’intègre l’application de fonction :

1. Dans le menu du portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

1. Dans la page **Nouveau**, recherchez *réseau virtuel*. Sélectionnez ensuite **Créer**.

1. Dans l’onglet **De base**, utilisez le tableau suivant pour configurer les paramètres du réseau virtuel.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | myVirtualNet| Nom du réseau virtuel auquel votre application de fonction se connectera. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | La région dans laquelle vous avez créé votre application de fonction. |

1. Sous l’onglet **Adresses IP**, sélectionnez **Ajouter un sous-réseau**. Utilisez le tableau suivant pour configurer les paramètres de sous-réseau.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Capture d’écran de la vue de création d’une configuration de réseau virtuel.":::

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Nom du sous-réseau** | functions | Nom du sous-réseau auquel votre application de fonction se connectera. | 
    | **Plage d’adresses de sous-réseau** | 10.0.1.0/24 | Plage d’adresses du sous-réseau. Dans l’image précédente, notez que l’espace d’adressage IPv4 est 10.0.0.0/16. Si la valeur était 10.1.0.0/16, la plage d’adresses de sous-réseau recommandée serait 10.1.1.0/24. |

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**.

## <a name="lock-down-your-storage-account"></a>Verrouiller votre compte de stockage

Les points de terminaison privés Azure sont utilisés pour se connecter à des ressources Azure spécifiques à l’aide d’une adresse IP privée. Cette connexion garantit que le trafic réseau reste dans le réseau virtuel choisi et que l’accès n’est disponible que pour des ressources spécifiques. 

Créez les points de terminaison privés pour les services Azure Stockage Fichier, Stockage Blob et Stockage Table en utilisant votre compte de stockage :

1. Dans votre nouveau compte de stockage, sélectionnez **Mise en réseau** dans le menu sur la gauche.

1. Sélectionnez l’onglet **Connexions des points de terminaison privés**, puis sélectionnez **Point de terminaison privé**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Capture d’écran montrant comment créer des points de terminaison privés pour le compte de stockage.":::

1. Dans l’onglet **De base**, utilisez les paramètres de point de terminaison privé indiqués dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Choisissez le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | file-endpoint | Nom du point de terminaison privé pour les fichiers de votre compte de stockage. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | Choisissez la région dans laquelle vous avez créé votre compte de stockage. |

1. Dans l’onglet **Ressource**, utilisez les paramètres de point de terminaison privé indiqués dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.Storage/storageAccounts | Le type de ressource pour les comptes de stockage. |
    | **Ressource** | mysecurestorage | Le compte de stockage que vous avez créé. |
    | **Sous-ressource cible** | fichier | Ce point de terminaison privé sera utilisé pour les fichiers du compte de stockage. |

1. Sous l’onglet **Configuration**, choisissez **Par défaut** pour le paramètre **Sous-réseau**.

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**. Les ressources du réseau virtuel peuvent désormais communiquer avec les fichiers de stockage.

1. Créez un autre point de terminaison privé pour les objets BLOB. Dans l’onglet **Ressources**, utilisez les paramètres indiqués dans le tableau suivant. Pour tous les autres paramètres, utilisez les mêmes valeurs que celles utilisées pour créer le point de terminaison privé pour les fichiers.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.Storage/storageAccounts | Le type de ressource pour les comptes de stockage. |
    | **Nom** | blob-endpoint | Nom du point de terminaison privé pour les objets blob de votre compte de stockage. |
    | **Ressource** | mysecurestorage | Le compte de stockage que vous avez créé. |
    | **Sous-ressource cible** | objet BLOB | Ce point de terminaison privé sera utilisé pour les fichiers blob du compte de stockage. |
1. Créez un autre point de terminaison privé pour les tables. Dans l’onglet **Ressources**, utilisez les paramètres indiqués dans le tableau suivant. Pour tous les autres paramètres, utilisez les mêmes valeurs que celles utilisées pour créer le point de terminaison privé pour les fichiers.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.Storage/storageAccounts | Le type de ressource pour les comptes de stockage. |
    | **Nom** | table-endpoint | Nom du point de terminaison privé pour les objets blob de votre compte de stockage. |
    | **Ressource** | mysecurestorage | Le compte de stockage que vous avez créé. |
    | **Sous-ressource cible** | table | Ce point de terminaison privé sera utilisé pour les tables du compte de stockage. |
1. Une fois les points de terminaison privés créés, revenez à la section **Pare-feu et réseaux virtuels** de votre compte de stockage.  
1. Vérifiez que l’option **Réseaux sélectionnés** est sélectionnée.  Il n’est pas nécessaire d’ajouter un réseau virtuel existant.

Les ressources du réseau virtuel peuvent désormais communiquer avec le compte de stockage à l’aide du point de terminaison privé.
## <a name="lock-down-your-service-bus"></a>Verrouiller votre Service Bus

Créez le point de terminaison privé pour verrouiller votre Service Bus :

1. Dans votre nouveau Service Bus, sélectionnez **Mise en réseau** dans le menu de gauche.

1. Sélectionnez l’onglet **Connexions des points de terminaison privés**, puis sélectionnez **Point de terminaison privé**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Capture d’écran montrant comment accéder aux points de terminaison privés pour le Service Bus.":::

1. Dans l’onglet **De base**, utilisez les paramètres de point de terminaison privé indiqués dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Le groupe de ressources que vous avez créé avec votre application de fonction. |
    | **Nom** | sb-endpoint | Nom du point de terminaison privé pour les fichiers de votre compte de stockage. |
    | **[Région](https://azure.microsoft.com/regions/)** | myFunctionRegion | La région dans laquelle vous avez créé votre compte de stockage. |

1. Dans l’onglet **Ressource**, utilisez les paramètres de point de terminaison privé indiqués dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. | 
    | **Type de ressource**  | Microsoft.ServiceBus/namespaces | Type de ressource pour le Service Bus. |
    | **Ressource** | myServiceBus | Le Service Bus que vous avez créé précédemment dans le tutoriel. |
    | **Sous-ressource cible** | espace de noms | Ce point de terminaison privé sera utilisé pour l’espace de noms du Service Bus. |

1. Sous l’onglet **Configuration**, choisissez **Par défaut** pour le paramètre **Sous-réseau**.

1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**. 
1. Une fois le point de terminaison privé créé, revenez à la section **Pare-feu et réseaux virtuels** de l’espace de noms de votre Service Bus.
1. Vérifiez que l’option **Réseaux sélectionnés** est sélectionnée.
1. Sélectionnez **+ Ajouter un réseau virtuel existant** pour ajouter le réseau virtuel récemment créé.
1. Sous l’onglet **Ajouter des réseaux**, utilisez les paramètres réseau du tableau suivant :

    | Paramètre | Valeur suggérée | Description|
    |---------|-----------------|------------|
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. |
    | **Réseaux virtuels** | myVirtualNet | Nom du réseau virtuel auquel votre application de fonction se connectera. |
    | **Sous-réseaux** | functions | Nom du sous-réseau auquel votre application de fonction se connectera. |

1. Sélectionnez **Ajouter l’adresse IP de votre client** pour permettre à l’adresse IP de votre client actuel d’accéder à l’espace de noms.
    > [!NOTE]
    > Vous devez autoriser l’adresse IP de votre client pour permettre au portail Azure de [publier des messages dans la file d’attente ultérieurement dans ce tutoriel](#test-your-locked-down-function-app).
1. Sélectionnez **Activer** pour activer le point de terminaison de service.
1. Sélectionnez **Ajouter** pour ajouter le réseau virtuel et le sous-réseau sélectionnés aux règles de pare-feu pour le Service Bus.
1. Sélectionnez **Enregistrer** pour enregistrer le paramètre d’entrée mis à jour.

Les ressources du réseau virtuel peuvent désormais communiquer avec le Service Bus à l’aide du point de terminaison privé.

## <a name="create-a-file-share"></a>Créer un partage de fichiers

1. Dans le compte de stockage que vous avez créé, sélectionnez **Partages de fichiers** dans le menu sur la gauche.

1. Sélectionnez **+ Partages de fichiers**. Dans le cadre de ce tutoriel, nommez le partages de fichiers *fichiers*.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Capture d’écran montrant comment créer un partage de fichiers dans le compte de stockage.":::

1. Sélectionnez **Create** (Créer).

## <a name="get-the-storage-account-connection-string"></a>Obtenir la chaîne de connexion de compte de stockage

1. Dans le compte de stockage que vous avez créé, sélectionnez **Clés d’accès** dans le menu sur la gauche.

1. Sélectionnez **Afficher les clés**. Copiez la chaîne de connexion **key1** et enregistrez-la. Vous aurez besoin de cette chaîne de connexion lorsque vous configurerez les paramètres de l’application.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Capture d’écran montrant comment obtenir une chaîne de connexion de compte de stockage.":::

## <a name="create-a-queue"></a>Créer une file d’attente

Créez la file d’attente dans laquelle votre déclencheur de Service Bus Azure Functions obtiendra les événements suivants :

1. Dans votre Service Bus, sélectionnez **Files d’attente** dans le menu de gauche.

1. Sélectionnez **File d’attente**. Dans le cadre de ce tutoriel, indiquez la *file d’attente* comme nom de la nouvelle file d’attente.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Capture d’écran montrant comment créer une file d’attente Service Bus.":::

1. Sélectionnez **Create** (Créer).

## <a name="get-a-service-bus-connection-string"></a>Obtenir une chaîne de connexion Service Bus

1. Dans votre Service Bus, sélectionnez **Stratégies d’accès partagé** dans le menu de gauche.

1. Sélectionnez **RootManageSharedAccessKey**. Copiez et enregistrez la **Chaîne de connexion principale**. Vous aurez besoin de cette chaîne de connexion lorsque vous configurerez les paramètres de l’application.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Capture d’écran montrant comment obtenir une chaîne de connexion Service Bus.":::

## <a name="integrate-the-function-app"></a>Intégrer l’application de fonction

Pour utiliser votre application de fonction avec des réseaux virtuels, vous devez la joindre à un sous-réseau. Vous utiliserez un sous-réseau spécifique pour l’intégration du réseau virtuel Azure Functions. Vous allez utiliser le sous-réseau par défaut pour les autres points de terminaison privés que vous créez dans ce tutoriel.

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Mise en réseau**.

1. Sous **Intégration de réseau virtuel**, sélectionnez **Cliquez ici pour configurer**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Capture d’écran montrant comment accéder à l’intégration du réseau virtuel.":::

1. Sélectionnez **Ajouter un réseau virtuel**.

1. Sous **Réseau virtuel**, sélectionnez le réseau virtuel que vous avez créé précédemment.

1. Sélectionnez le sous-réseau **fonctions** créé précédemment. Sélectionnez **OK**.  Votre application de fonction est désormais intégrée à votre réseau virtuel !

    Si le réseau virtuel et l’application de fonction se trouvent dans des abonnements différents, vous devez d’abord fournir un accès **Contributeur** au principal de service **Microsoft Azure App Service** sur le réseau virtuel.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Capture d’écran montrant comment connecter une application de fonction à un sous-réseau.":::

## <a name="configure-your-function-app-settings"></a>Configurer vos paramètres d’application de fonction

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Configuration**.

1. Pour utiliser votre application de fonction avec des réseaux virtuels, mettez à jour les paramètres d’application indiqués dans le tableau suivant. Pour ajouter ou modifier un paramètre, sélectionnez **+ Nouveau paramètre d'application** ou l’icône **Modifier** dans la colonne la plus à droite du tableau des paramètre d'application. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | La chaîne de connexion du compte de stockage que vous avez créé. Cette chaîne de connexion de stockage provient de la section [Obtenir la chaîne de connexion de compte de stockage](#get-the-storage-account-connection-string). Ce paramètre permet à votre application de fonction d’utiliser le compte de stockage sécurisé pour les opérations normales au moment du runtime. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | La chaîne de connexion du compte de stockage que vous avez créé. Ce paramètre permet à votre application de fonction d’utiliser le compte de stockage sécurisé pour Azure Files, qui est utilisé au cours du déploiement. |
    | **WEBSITE_CONTENTSHARE** | files | Le nom du partage de fichiers que vous avez créé dans le compte de stockage. Utilisez ce paramètre avec WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Créez ce paramètre d’application pour la chaîne de connexion de votre Service Bus. Cette chaîne de connexion de stockage provient de la section [Obtenir la chaîne de connexion Service Bus](#get-a-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Créez ce paramètre d’application. La valeur 1 permet à votre application de fonction de se mettre à l’échelle lorsque votre compte de stockage est limité à un réseau virtuel. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Créez ce paramètre d’application. Lorsque votre application s’intègre à un réseau virtuel, elle utilise le même serveur DNS que le réseau virtuel. Votre application de fonction a besoin de ce paramètre pour pouvoir fonctionner avec des zones privées Azure DNS. Elle est requise lorsque vous utilisez des points de terminaison privés. |

1. Dans l’affichage **Configuration**, sélectionnez l’onglet **Paramètres du runtime de fonction**.

1. Définissez **Supervision de l’échelle de runtime** sur **Activé**. Ensuite, sélectionnez **Enregistrer**. La mise à l’échelle pilotée par le runtime vous permet de connecter des fonctions de déclencheur non HTTP à des services qui s’exécutent dans votre réseau virtuel.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Capture d’écran montrant comment activer la mise à l’échelle pilotée par le runtime pour Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Déployer un déclencheur Service Bus et un déclencheur HTTP

1. Dans GitHub, accédez à l’exemple de référentiel suivant. Il contient une application de fonction et deux fonctions : un déclencheur HTTP et un déclencheur de file d’attente Service bus.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. En haut de la page, sélectionnez le bouton **Duplication** pour créer une duplication (fork) de ce référentiel dans votre propre compte ou organisation GitHub.

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Centre de déploiement**. Sélectionnez ensuite **Paramètres**.

1. Dans l’onglet **Paramètres**, utilisez les paramètres de déploiement indiqués dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Source** | GitHub | Vous devez avoir créé un référentiel GitHub avec l’exemple de code à l’étape 2. | 
    | **Organisation**  | myOrganization | Organisation dans laquelle votre référentiel est archivée. Il s’agit généralement de votre compte. |
    | **Référentiel** | functions-vnet-tutorial | Référentiel dupliqué (fork) depuis https://github.com/Azure-Samples/functions-vnet-tutorial. |
    | **Branche** | main | Branche primaire du référentiel que vous avez créé. |
    | **Pile d’exécution** | .NET | L’exemple de code est dans C#. |
    | **Version** | .NET Core 3.1 | Version du runtime |

1. Sélectionnez **Enregistrer**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Capture d’écran montrant comment déployer le code Azure Functions via le portail.":::

1. Votre déploiement initial peut prendre quelques minutes. Lorsque votre application est déployée avec succès, un message d’état **Succès (actif)** s’affiche dans l’onglet **Journaux**. Si nécessaire, actualisez la page.

Félicitations ! Vous avez déployé avec succès votre exemple d’application de fonction.

## <a name="lock-down-your-function-app"></a>Verrouiller votre application de fonction

À présent, créez le point de terminaison privé pour verrouiller votre application de fonction. Ce point de terminaison privé connecte votre application de fonction en toute sécurité et de façon privée à votre réseau virtuel à l’aide d’une adresse IP privée. 

Pour plus d'informations, consultez la [documentation de point de terminaison privé](../private-link/private-endpoint-overview.md).

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Mise en réseau**.

1. Sous **Connexions au point de terminaison privé**, sélectionnez **Configurer vos connexions de point de terminaison privé**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Capture d’écran montrant comment accéder à un point de terminaison privé d’une application de fonction.":::

1. Sélectionnez **Ajouter**.

1. Dans le volet qui s’ouvre, utilisez les paramètres de point de terminaison privé suivants :

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé d’une application de fonction. Le nom est functionapp-endpoint. L’abonnement est 'Private Test Sub CACHHAI'. Le réseau virtuel est MyVirtualNet-tutorial. Le sous-réseau est default.":::

1. Sélectionnez **OK** pour ajouter le point de terminaison privé. 
 
Félicitations ! Vous avez correctement sécurisé votre application de fonction, Service Bus et votre compte de stockage en ajoutant des points de terminaison privés.

### <a name="test-your-locked-down-function-app"></a>Tester votre application de fonction verrouillée

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Fonctions**.

1. Sélectionnez **ServiceBusQueueTrigger**.

1. Dans le menu de gauche, sélectionnez **Surveiller**. 
 
Vous verrez que vous ne pouvez pas surveiller votre application. En effet, votre navigateur n’a pas accès au réseau virtuel, il ne peut donc pas accéder directement aux ressources au sein du réseau virtuel. 
 
Voici une autre façon de surveiller votre fonction en utilisant Application Insights :

1. Dans votre application de fonction, dans le menu sur la gauche, sélectionnez **Application Insights**. Sélectionnez ensuite **Voir les données Application Insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Capture d’écran montrant comment afficher Application Insights pour une application de fonction.":::

1. Dans le menu sur la gauche, sélectionnez **Métriques temps réel**.

1. Ouvrez un nouvel onglet. Dans votre Service Bus, sélectionnez **Files d’attente** dans le menu de gauche.

1. Sélectionnez votre file d’attente.

1. Dans le menu sur la gauche, sélectionnez **Service Bus Explorer**. Sous **Envoyer**, pour **Type de contenu**, choisissez **Texte/brut**. Entrez un message. 

1. Sélectionnez **Envoyer** pour envoyer un message.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Capture d’écran montrant comment envoyer des messages Service Bus à l’aide du portail.":::

1. Sous l’onglet **Métriques temps réel**, vous devez voir que votre déclencheur de file d’attente Service bus a été activé. Si ce n’est pas le cas, renvoyez le message à partir de **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Capture d’écran montrant comment afficher les messages à l’aide de métriques en temps réel pour les applications de fonction.":::

Félicitations ! Vous avez testé avec succès la configuration de votre application de fonction avec des points de terminaison privés.

## <a name="understand-private-dns-zones"></a>Comprendre les zones DNS privées
Vous avez utilisé un point de terminaison privé pour vous connecter aux ressources Azure. Vous vous connectez à une adresse IP privée au lieu du point de terminaison public. Les services Azure existants sont configurés afin d’utiliser un DNS existant pour se connecter au point de terminaison public. Vous devez remplacer la configuration DNS pour vous connecter au point de terminaison privé.

Une zone DNS privée est créée pour chaque ressource Azure configurée avec un point de terminaison privé. Un enregistrement DNS est créé pour chaque adresse IP privée associée au point de terminaison privé.

Les zones DNS suivantes ont été créées dans ce tutoriel :

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une application de fonction Premium, un compte de stockage et un Service Bus. Vous avez sécurisé toutes ces ressources derrière des points de terminaison privés. 

Utilisez les liens suivants pour en savoir plus sur les options de mise en réseau et les points de terminaison privés d’Azure Functions :

- [Options de mise en réseau d’Azure Functions](./functions-networking-options.md)
- [Plan Premium Azure Functions](./functions-premium-plan.md)
- [Points de terminaison privés Service Bus](../service-bus-messaging/private-link-service.md)
- [Points de terminaison privés Stockage Azure](../storage/common/storage-private-endpoints.md)
