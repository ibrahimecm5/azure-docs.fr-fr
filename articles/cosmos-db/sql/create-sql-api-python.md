---
title: 'Démarrage rapide : Générer une application Python à l’aide du compte d’API SQL Azure Cosmos DB'
description: Cet article présente un exemple de code Python que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB et pour l’interroger.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 08/26/2021
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: 6cdfcff203f9694ab9c9e0b7a693b51070f73aac
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070961"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Démarrage rapide : Générer une application Python à l’aide d’un compte d’API SQL Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Kit SDK Java v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Connecteur Spark v3](create-sql-api-spark.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Dans ce guide de démarrage rapide, vous allez créer et gérer un compte d’API SQL Azure Cosmos DB à partir du portail Azure et de Visual Studio Code à l’aide d’une application Python clonée à partir de GitHub. Azure Cosmos DB est un service de base de données multimodèle qui vous permet de créer et d’interroger rapidement des bases de données de documents, de tables, de paires clé/valeur et de graphes avec des capacités de distribution mondiale et de mise à l’échelle horizontale.

## <a name="prerequisites"></a>Prérequis

- Un compte Cosmos DB. Vos options sont :
    * Au sein d’un abonnement Azure actif :
        * [Créer un compte Azure gratuit](https://azure.microsoft.com/free) ou utiliser votre abonnement existant 
        * [Crédits mensuels Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Niveau gratuit d’Azure Cosmos DB](../optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Sans abonnement Azure actif :
        * [Essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/), un environnement de tests pendant 30 jours.
        * [Émulateur Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 ou 3.6+](https://www.python.org/downloads/) avec l’exécutable `python` dans votre `PATH`.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [SDK Python Azure Cosmos DB pour l’API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="important-update-on-python-2x-support"></a>Mise à jour importante sur la prise en charge de Python 2.x

Les nouvelles versions de ce kit SDK ne prendront pas en charge Python 2.x à compter du 1er janvier 2022. Consultez le [JOURNAL DES MODIFICATIONS](./sql-api-sdk-python.md) pour plus d’informations.

## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Ajouter un conteneur

[!INCLUDE [cosmos-db-create-collection](../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons à présent cloner une application API SQL à partir de GitHub, définir la chaîne de connexion et l’exécuter. Ce guide de démarrage rapide utilise la version 4 du [SDK Python](https://pypi.org/project/azure-cosmos/#history).

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```cmd
    md git-samples
    ```

   Si vous utilisez une invite de commandes bash, utilisez plutôt la commande suivante :

   ```bash
   mkdir "git-samples"
   ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans votre compte Azure Cosmos DB, dans le [portail Azure](https://portal.azure.com/), sélectionnez **Clés** dans le volet de navigation de gauche. Utilisez les boutons de copie sur le côté droit de l’écran pour copier l’**URI** et la **clé primaire** dans le fichier *cosmos_get_started.py* à l’étape suivante.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Obtenir une clé d’accès et un URI dans les paramètres de clés du portail Azure":::

2. Dans Visual Studio Code, ouvrez le fichier *cosmos_get_started.py* sous *\git-samples\azure-cosmos-db-python-getting-started*.

3. Copiez la valeur de votre **URI** dans le portail (à l’aide du bouton Copier), puis définissez-la comme valeur de la variable **endpoint** dans le fichier *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Ensuite, copiez la valeur de votre **CLÉ PRIMAIRE** à partir du portail et définissez-la comme valeur pour **key** dans le fichier *cosmos_get_started.py*. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

    `key = 'FILLME'`

5. Enregistrez le fichier *cosmos_get_started.py*.

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. En savoir plus sur les ressources de base de données créées dans le code, ou passer directement à [Mettre à jour votre chaîne de connexion](#update-your-connection-string).

Tous les extraits de code suivants proviennent du fichier *cosmos_get_started.py*.

* Le CosmosClient est initialisé. Veillez à mettre à jour les valeurs de « endpoint » et de « key », comme décrit dans la section [Mettre à jour votre chaîne de connexion](#update-your-connection-string). 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Une nouvelle base de données est créée.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Un nouveau conteneur est créé, avec 400 RU/s de [débit provisionné](../request-units.md). Nous choisissons `lastName` comme [clé de partition](../partitioning-overview.md#choose-partitionkey), ce qui nous permet de faire des requêtes efficaces qui filtrent sur cette propriété. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Certains éléments sont ajoutés au conteneur. Les conteneurs sont une collection d’éléments (des documents JSON) qui peuvent avoir différents schémas. Les méthodes d’assistance ```get_[name]_family_item``` retournent des représentations d’une famille stockées dans Azure Cosmos DB en tant que documents JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Les lectures des points (des recherches de valeurs de clés) sont effectuées avec la méthode `read_item`. Nous affichons les [frais en RU (unités de requête)](../request-units.md) de chaque opération.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Une requête est effectuée avec la syntaxe des requêtes SQL. Étant donné que nous utilisons les valeurs de clé de partition de ```lastName``` dans la clause WHERE, Azure Cosmos DB route cette requête vers les partitions pertinentes, ce qui améliore les performances.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Exécuter l’application

1. Dans Visual Studio Code, sélectionnez **Affichage** > **Palette de commandes**. 

2. À l’invite, entrez **Python: Select Interpreter**, puis sélectionnez la version de Python à utiliser.

    Le pied de page dans Visual Studio Code affiche désormais l’interpréteur sélectionné. 

3. Sélectionnez **Affichage** > **Terminal intégré** pour ouvrir le terminal intégré Visual Studio Code.

4. Dans la fenêtre du terminal intégré, vérifiez que vous êtes bien dans le dossier *azure-cosmos-db-python-getting-started*. Si ce n’est pas le cas, exécutez la commande suivante pour passer au dossier d’exemple. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Exécutez la commande suivante pour installer le package azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Si vous obtenez une erreur d’accès refusé quand vous tentez d’installer azure-cosmos, vous devrez [exécuter VS Code en tant qu’administrateur](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Exécutez la commande suivante pour exécuter l’exemple, et pour créer et stocker de nouveaux documents dans Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Pour vérifier que les nouveaux éléments ont été créés et enregistrés, dans le portail Azure, sélectionnez **Explorateur de données** > **AzureSampleFamilyDatabase** > **Éléments**. Regardez les éléments qui ont été créés. Par exemple, voici un exemple de document JSON pour la famille Andersen :
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer un conteneur à l’aide de l’Explorateur de données et à exécuter une application Python dans Visual Studio Code. Vous pouvez maintenant importer des données supplémentaires dans votre compte Azure Cosmos DB. 

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requête typiques de votre charge de travail actuelle, lisez la section [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API SQL](../import-data.md)
