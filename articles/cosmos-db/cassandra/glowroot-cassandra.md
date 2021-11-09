---
title: Exécuter Glowroot sur l’API Cassandra Azure Cosmos DB (préversion)
description: Cet article explique comment exécuter Glowroot dans l’API Cassandra Azure Cosmos DB.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 1808e3df0b70f61c3cfabf96e61b6675dff6b011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097918"
---
# <a name="run-glowroot-on-azure-cosmos-db-cassandra-api"></a>Exécuter Glowroot sur l’API Cassandra Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Glowroot est un outil de gestion du niveau de performance des applications utilisé pour optimiser et surveiller le niveau de performance de vos applications. Cet article explique la façon dont vous pouvez désormais utiliser Glowroot dans l’API Cassandra Azure Cosmos DB pour surveiller le niveau de performance de votre application.

## <a name="prerequisites-and-setup"></a>Prérequis et configuration

* [Créez un compte d’API Cassandra Azure Cosmos DB](manage-data-java.md#create-a-database-account).
* [Installer JAVA (version 8) pour Windows](https://developers.redhat.com/products/openjdk/download).
> [!NOTE]
> Notez qu’il existe certaines cibles de build incompatibles connues avec les versions plus récentes. Si vous disposez déjà d’une version plus récente de JAVA, vous pouvez toujours télécharger JDK8.
> Si vous avez installé une version plus récente de JAVA en plus de JDK8 : définissez la variable %JAVA_HOME% dans l’invite de commandes locale pour cibler JDK8. Cette opération modifie uniquement la version Java de la session active et laisse les paramètres globaux de l’ordinateur intacts. 
* [Installez Maven](https://maven.apache.org/download.cgi).
    * Vérifiez la réussite de l’installation en exécutant : `mvn --version`.

## <a name="run-glowroot-central-collector-with-cosmos-db-endpoint"></a>Exécuter le collecteur central Glowroot avec le point de terminaison de Cosmos DB
Une fois la configuration du point de terminaison terminée : 
1. [Téléchargez la distribution du collecteur central Glowroot](https://github.com/glowroot/glowroot/wiki/Central-Collector-Installation#central-collector-installation).
2. Dans le fichier glowroot-central.properties, renseignez les propriétés suivantes de votre point de terminaison d’API Cassandra Cosmos DB :
    * cassandra.contactPoints
    * cassandra.username
    * cassandra.password.
3. Définissez les propriétés `cassandra.ssl=true`, `cassandra.gcGraceSeconds=0` et `cassandra.port=10350`.
4. Vérifiez que le fichier glowroot-central.properties se trouve dans le même dossier que le fichier glowroot-central.jar.
5. Exécutez `java -jar glowroot-central.jar` pour commencer à exécuter Glowroot.

## <a name="faqs"></a>Foire aux questions
Ouvrez un ticket de support si vous rencontrez des problèmes lors de l’exécution ou du test de Glowroot. Fournissez l’ID de l’abonnement et le nom du compte dans lequel votre test Glowroot s’exécutera.

## <a name="next-steps"></a>Étapes suivantes
- Familiarisez-vous avec la [création d’un compte d’API Cassandra, d’une base de données et d’une table](create-account-java.md) à l’aide d’une application Java.
- Apprenez-en davantage sur les [fonctionnalités prises en charge](cassandra-support.md) dans l’API Cassandra dans Azure Cosmos DB.
