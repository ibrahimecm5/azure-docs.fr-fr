---
title: Qu’est-ce que le Connecteur de services ?
description: Comprenez mieux les scénarios classiques de cas d’usage du Connecteur de services et découvrez les principaux avantages de celui-ci.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca822d14f225372f40d14165557677dff66f6ff8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096686"
---
# <a name="what-is-service-connector"></a>Qu’est-ce que le Connecteur de services ?

Le Connecteur de services vous aide à connecter facilement un service de calcul Azure à d’autres services de soutien. Ce service configure les paramètres réseau et les informations de connexion (par exemple, la génération de variables d’environnement) entre un service de calcul et un service de soutien cible dans le plan de gestion. Les développeurs se servent simplement du Kit de développement logiciel (SDK) ou de la bibliothèque par défaut qui utilisent les informations de connexion pour effectuer des opérations de plan de données sur le service de soutien cible. 

Cet article donne une vue d’ensemble du Connecteur de services.

## <a name="what-is-service-connector-used-for"></a>À quoi sert le Connecteur de services ?

Toute application s’exécutant sur des services de calcul Azure, qui requiert un service auxiliaire peut utiliser le Connecteur de services. Voici quelques exemples d’utilisation du Connecteur de services pour simplifier l’expérience de connexion de service à service.

* **WebApp + DB :** utilisez le Connecteur de services pour connecter PostgreSQL, MySQL, SQL DB ou Cosmos DB à votre App Service.  
* **WebApp + Stockage :** utilisez le Connecteur de services pour vous connecter aux comptes Stockage Azure et utiliser facilement vos produits de stockage préférés dans votre App Service.
* **Spring Cloud + Base de données :** utilisez le Connecteur de services pour connecter PostgreSQL, MySQL, SQL DB ou Cosmos DB à votre application Spring Cloud.
* **Spring Cloud + Apache Kafka :** le Connecteur de services peut vous aider à connecter votre application Spring Cloud à Apache Kafka sur Confluent Cloud.

Consultez [Quels services sont pris en charge dans le Connecteur de services](#what-services-are-supported-in-service-connector) pour voir d’autres modèles d’application et de services pris en charge.

## <a name="what-are-the-benefits-using-service-connector"></a>Quels sont les avantages de l’utilisation du Connecteur de services ?

**Connecter pour cibler un service auxiliaire en un seule commande ou quelques clics :**

Le Connecteur de services est conçu pour faciliter l’utilisation. Il requiert trois paramètres, à savoir l’instance de service cible, le type d’authentification entre le service de calcul et le service cible et votre type de client d’application, pour créer une connexion. Les développeurs peuvent utiliser l’interface de ligne de commande de Connexion Azure ou une expérience guidée du portail Azure pour créer facilement des connexions.

**Utiliser l’état de la connexion pour surveiller ou identifier un problème de connexion :**

Une fois une connexion au service créée. Les développeurs peuvent valider et vérifier l’état d’intégrité de la connexion. Le Connecteur de services peut suggérer des actions pour corriger des connexions interrompues.

## <a name="what-services-are-supported-in-service-connector"></a>Quels services sont pris en charge dans le Connecteur de services ?

> [!NOTE]
> Le Connecteur de services est en préversion publique. L’équipe produit œuvre activement à l’ajout de types de services pris en charge à la liste.

**Service de calcul :**

* Azure App Service
* Azure Spring Cloud

**Service cible :**

* Azure Database pour PostgreSQL
* Azure Database pour MySQL
* Stockage Azure (stockages Blob, File d’attente, Fichier et Table)
* Azure Key Vault
* Service Azure SignalR
* Apache Kafka sur Confluent Cloud

## <a name="how-to-use-service-connector"></a>Comment utiliser le Connecteur de services ?

Il existe deux façons principales d’utiliser le Connecteur de services pour votre application Azure :

* **Interface de ligne de commande de Connexion Azure :** créez, répertoriez, validez et supprimez des connexions de service à service avec un groupe de commandes de connexion dans Azure CLI.
* **Expérience du Connecteur de service sur le portail Azure :** utilisez l’expérience guidée du portail pour créer des connexions de service à service et les gérer à l’aide d’une liste hiérarchique.

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Démarrage rapide : Connecteur de services dans App Service utilisant Azure CLI](./quickstart-cli-app-service-connection.md)

> [!div class="nextstepaction"]
> [Démarrage rapide : Connecteur de services dans App Service utilisant de portail Azure](./quickstart-portal-app-service-connection.md)

> [!div class="nextstepaction"]
> [Démarrage rapide : Connecteur de services dans Spring Cloud utilisant Azure CLI](./quickstart-cli-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Démarrage rapide : Connecteur de services dans Spring Cloud utilisant le portail Azure](./quickstart-portal-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
