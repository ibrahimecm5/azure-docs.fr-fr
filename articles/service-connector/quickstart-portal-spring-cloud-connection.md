---
title: 'Démarrage rapide : Créer une connexion de service dans Spring Cloud avec le portail Azure'
description: Démarrage rapide montrant comment créer une connexion de service dans Spring Cloud avec le portail Azure
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f242eb1557a1b44f3698e06f33dc2a943658af15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028876"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-from-azure-portal"></a>Démarrage rapide : Créer une connexion de service dans Spring Cloud avec le portail Azure

Ce guide de démarrage rapide explique comment créer une nouvelle connexion de service avec Service Connector dans Spring Cloud à partir du portail Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Ce guide de démarrage rapide part du principe que vous disposez déjà d’au moins une application Cloud Spring s’exécutant sur Azure. Si vous n’avez pas d’application Spring Cloud, [créez-en une](../spring-cloud/quickstart.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [https://portal.azure.com/](https://portal.azure.com/) avec votre compte Azure.

## <a name="create-a-new-service-connection-in-spring-cloud"></a>Créer une nouvelle connexion de service dans Spring Cloud

Vous allez utiliser Service Connector pour créer une nouvelle connexion de service dans Spring Cloud.

1. Sélectionnez le bouton **Toutes les ressources** qui est situé sur la gauche du portail Azure. Tapez **Spring Cloud** dans le filtre, puis cliquez sur le nom de l’instance Spring Cloud que vous souhaitez utiliser.
1. Sélectionnez **Applications**, puis sélectionnez le nom de l’application dans la liste. 
1. Sélectionnez **Déploiements**, puis sélectionnez le déploiement **par défaut** dans la liste. Sélectionnez **Service Connector (préversion)** dans la table des matières située sur la gauche. Sélectionnez ensuite **Créer**.
1. Sélectionnez ou saisissez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | L’un de vos abonnements | L’abonnement dans lequel se trouve votre service cible (le service auquel vous souhaitez vous connecter). L’abonnement par défaut est celui où se trouve l’instance App Service. |
    | **Type de service** | Conteneur des objets blob de stockage | Type du service cible. Si vous n’avez pas de conteneur d’objets blob de stockage, vous pouvez [en créer un](../storage/blobs/storage-quickstart-blobs-portal.md) ou utiliser un autre type de service. |
    | **Nom de connexion** | Nom unique généré | Nom de la connexion qui existe entre votre instance App Service et le service cible  |
    | **Compte de stockage** | Votre serveur de stockage | Service de base de données cible auquel vous souhaitez vous connecter. Si vous choisissez un autre type de service, sélectionnez l’instance de service cible correspondante. |

4. Sélectionnez **Vérifier + créer** pour passer en revue les paramètres de la connexion. Ensuite, sélectionnez **Créer** pour démarrer la création de la connexion au service.

## <a name="view-service-connections-in-spring-cloud"></a>Afficher les connexions de service dans Spring Cloud

1. Dans **Service Connector (préversion)** , vous voyez une connexion entre Spring Cloud et le service cible.

1. Cliquez sur le bouton **>** pour développer la liste. Vous pouvez voir les propriétés qui sont exigées par votre application Spring Boot.

1. Cliquez sur le bouton **...** , puis sélectionnez **Valider**. Vous pouvez voir les détails concernant la validation de la connexion dans le panneau contextuel situé à droite.

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + Apache Kafka sur Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)
