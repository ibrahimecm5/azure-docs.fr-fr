---
title: Créer une liaison privée pour un point de terminaison de streaming
description: Cet article explique comment utiliser une liaison privée avec un point de terminaison de streaming. Vous allez créer une ressource de point de terminaison privé qui est un lien entre un réseau virtuel et un point de terminaison de streaming. Ce déploiement crée une adresse IP d’interface réseau dans le réseau virtuel. La liaison privée vous permet de connecter l’interface réseau du réseau privé au point de terminaison de streaming dans le compte Media Services. Vous allez également créer des zones DNS qui transmettent les adresses IP privées.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 3175925fe8d5273ec5f9bb0220b439a01df5ef11
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097678"
---
# <a name="create-a-private-link-for-a-streaming-endpoint"></a>Créer une liaison privée pour un point de terminaison de streaming

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article explique comment utiliser une liaison privée avec un point de terminaison de streaming. Nous supposons que vous savez déjà comment créer un [groupe de ressources Azure](/azure-resource-manager/management/manage-resource-groups-portal), un [compte Media Services](account-create-how-to.md) et un [réseau virtuel Azure](/virtual-network/quick-create-portal).

Vous allez créer une ressource de point de terminaison privé qui est un lien entre un réseau virtuel et un point de terminaison de streaming. Ce déploiement crée une adresse IP d’interface réseau dans le réseau virtuel. La liaison privée vous permet de connecter l’interface réseau du réseau privé au point de terminaison de streaming dans le compte Media Services. Vous allez également créer des zones DNS qui transmettent les adresses IP privées.

Le réseau virtuel créé pour cette procédure a pour seul but de contribuer à l’exemple.  Il est supposé que vous disposez d’un réseau virtuel existant que vous utiliserez pour la production.

> [!NOTE]
> À mesure que vous suivez les étapes, nommez vos ressources de la même façon afin de pouvoir comprendre facilement qu’elles ont un objectif similaire.  Par exemple, *privatelink1stor* pour votre compte de stockage et *privatelink1mi* pour votre identité managée.

## <a name="create-a-resource-group-and-a-media-services-account"></a>Créer un groupe de ressources et un compte Media Services

1. Création d’un groupe de ressources Azure.
1. Créer un compte Media Services.  Un point de terminaison de streaming par défaut est créé lorsque vous créez le compte. La création d’une identité managée est nécessaire pendant le processus de configuration.
1. Créez un réseau virtuel Azure avec les paramètres par défaut.

À ce stade, il n’y a rien dans votre réseau virtuel, et votre compte Media Services a un point de terminaison accessible sur Internet qui inclut un point de terminaison de streaming accessible sur Internet, une remise de clés et des événements en direct.  L’étape suivante consiste à rendre le point de terminaison de streaming privé.

## <a name="start-the-streaming-endpoint"></a>Démarrer le point de terminaison de streaming

1. Accédez au compte Media Services que vous avez créé.  
1. Dans le menu, sélectionnez **Points de terminaison de streaming**. L’écran Points de terminaison de streaming s’affiche.
1. Sélectionnez le point de terminaison de streaming par défaut que vous avez créé lorsque vous avez configuré le compte Media Services.  L’écran Point de terminaison de streaming par défaut s’affiche.
1. Sélectionnez **Démarrer**. Les options de démarrage s’affichent.
1. Sélectionnez **Aucun** dans la liste déroulante des niveaux tarifaires CDN.
1. Sélectionnez **Démarrer**.  L’exécution du point de terminaison de streaming démarre. Le point de terminaison est toujours accessible sur Internet.

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

1. Revenez à votre compte Media Services.
1. Sélectionnez **Mise en réseau** dans le menu.
1. Sélectionnez l’onglet **Connexions des points de terminaison privés**. L’écran des connexions des points de terminaison privés s’affiche.
1. Sélectionnez **Ajouter un point de terminaison privé**. L’écran Créer un point de terminaison privé s’affiche.
1. Dans le champ **Nom**, donnez au point de terminaison privé un nom tel que *privatelinkpe*.
1. Dans la liste déroulante **Région**, sélectionnez une région, par exemple *USA Ouest 2*.
1. Sélectionnez **Suivant : Ressource**. L’écran Ressource s’affiche.

## <a name="assign-the-private-endpoint-to-a-resource"></a>Affecter le point de terminaison privé à une ressource

1. Dans les cases d’option **Méthodes de connexion**, sélectionnez la case d’option *Se connecter à une ressource Azure dans mon répertoire*.
1. Dans la liste déroulante **Type de ressource**, sélectionnez *Microsoft.Media/mediaservices*.
1. Dans la liste déroulante **Ressource**, sélectionnez le compte Media Services que vous avez créé.
1. Dans la liste déroulante **Sous-ressource cible**, sélectionnez le point de terminaison de streaming que vous avez créé.

## <a name="deploy-the-private-endpoint-to-the-virtual-network"></a>Déployer le point de terminaison privé sur le réseau virtuel

1. Dans la liste déroulante **Réseau virtuel**, sélectionnez le réseau virtuel que vous avez créé.
1. Dans la liste déroulante **Sous-réseau**, sélectionnez le sous-réseau que vous souhaitez utiliser.
1. Restez sur cet écran.

## <a name="create-dns-zones-to-use-with-the-private-endpoint"></a>Créer des zones DNS à utiliser avec le point de terminaison privé

Pour utiliser le point de terminaison de streaming à l’intérieur de votre réseau virtuel, créez des zones DNS privées. Vous pouvez utiliser le même nom DNS et récupérer l’adresse IP privée du point de terminaison de streaming.

1. Sur le même écran, pour la configuration **media-azure-net**, sélectionnez le groupe de ressources que vous avez créé dans la liste déroulante **Groupe de ressources**.
1. Pour la configuration **privatelink-media-azure-net**, sélectionnez le même groupe de ressources dans la liste déroulante **Groupe de ressources**.
1. Sélectionnez **Suivant : Balises**. Si vous souhaitez ajouter des balises à vos ressources, faites-le ici.
1. Sélectionnez **Suivant : Vérifier + créer**. L’écran Vérifier + créer s’affiche.
1. Vérifiez vos paramètres et assurez-vous qu’ils sont corrects.
1. Sélectionnez **Create** (Créer). L’écran de déploiement du point de terminaison privé s’affiche.

Pendant que le déploiement est en cours, il crée également un [modèle Azure Resource Manager (ARM)](/azure-resource-manager/templates/overview). Vous pouvez utiliser des modèles ARM pour automatiser le déploiement. Pour voir le modèle, sélectionnez **Modèle** dans le menu.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas d’utiliser les ressources créées dans cet exercice, supprimez simplement le groupe de ressources. Si vous ne supprimez pas les ressources, elles vous seront facturées.