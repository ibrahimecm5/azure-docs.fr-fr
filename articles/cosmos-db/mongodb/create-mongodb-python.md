---
title: Bien démarrer avec l’API Azure Cosmos DB pour MongoDB et Python
description: Présente un exemple de code Python que vous pouvez utiliser pour vous connecter et interroger l’API Azure Cosmos DB pour MongoDB.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 10/22/2021
ms.openlocfilehash: af59ce8dc6fa3fc4a2f62e3bc00e185cb8be1bbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271196"
---
# <a name="quickstart-get-started-using-azure-cosmos-db-api-for-mongodb-and-python"></a>Démarrage rapide : Bien démarrer avec l’API Azure Cosmos DB pour MongoDB et Python
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Ce [guide de démarrage rapide](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) montre comment :
1. Créer un [compte d’API Azure Cosmos DB pour MongoDB](mongodb-introduction.md) 
2. Vous connecter à votre compte à l’aide de PyMongo
3. Créer un exemple de base de données et de collection
4. Effectuer des opérations CRUD dans l’exemple de collection

## <a name="prerequisites-to-run-the-sample-app"></a>Configuration requise pour exécuter l’exemple d’application

* [Python](https://www.python.org/downloads/) 3.9+ (exécutez de préférence l’[exemple de code](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) décrit dans cet article avec cette version recommandée. Bien qu’il puisse fonctionner sur des versions antérieures de Python 3.)
* [PyMongo](https://pypi.org/project/pymongo/) installé sur votre machine

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>Découvrir le modèle objet

Avant de poursuivre la génération de l’application, examinons la hiérarchie des ressources dans l’API pour MongoDB, ainsi que le modèle objet utilisé pour créer ces ressources et y accéder. L’API pour MongoDB crée les ressources dans l’ordre suivant :

* Compte d’API Azure Cosmos DB pour MongoDB
* Bases de données 
* Regroupements 
* Documents

Pour plus d’informations sur la hiérarchie des entités, consultez l’article [Modèle des ressources d’Azure Cosmos DB](../account-databases-containers-items.md).

## <a name="get-the-code"></a>Obtenir le code

Téléchargez l’exemple de code python [à partir du dépôt](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started) ou utilisez le clone Git :

```shell
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started
```

## <a name="retrieve-your-connection-string"></a>Récupération de votre chaîne de connexion

Quand vous exécutez l’exemple de code, vous devez entrer la chaîne de connexion de votre compte d’API pour MongoDB. Utilisez les étapes suivantes pour la rechercher :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Cosmos DB.

2. Dans le menu de navigation de gauche, sélectionnez **Chaîne de connexion**, puis **Clés en lecture/écriture**. Vous utilisez les boutons de copie à droite de l’écran pour copier la chaîne de connexion principale.

> [!WARNING]
> N’archivez jamais de mots de passe ou autres données sensibles dans le code source.


## <a name="run-the-code"></a>Exécuter le code

```shell
python run.py
```

## <a name="understand-how-it-works"></a>Comprenez comment elle fonctionne

### <a name="connecting"></a>Connecting

Le code suivant invite l’utilisateur à entrer la chaîne de connexion. Ce n’est jamais une bonne idée d’avoir votre chaîne de connexion dans le code, car toute personne qui l’a peut lire ou écrire dans votre base de données.

```python
CONNECTION_STRING = getpass.getpass(prompt='Enter your primary connection string: ') # Prompts user for connection string
```

Le code suivant crée une connexion cliente à votre API pour MongoDB et la teste pour vérifier sa validité.

```python
client = pymongo.MongoClient(CONNECTION_STRING)
try:
    client.server_info() # validate connection string
except pymongo.errors.ServerSelectionTimeoutError:
    raise TimeoutError("Invalid API for MongoDB connection string or timed out when attempting to connect")
```

### <a name="resource-creation"></a>Création de ressources
Le code suivant crée l’exemple de base de données et de collection à utiliser pour les opérations CRUD. Quand vous créez des ressources programmatiquement, nous vous recommandons d’utiliser les commandes d’extension d’API pour MongoDB (comme indiqué ici), car ces commandes peuvent définir le débit des ressources (RU/s) et configurer le partitionnement. 

La création implicite de ressources fonctionne, mais définit par défaut les valeurs recommandées pour le débit et ne configure pas le partitionnement.

```python
# Database with 400 RU throughput that can be shared across the DB's collections
db.command({'customAction': "CreateDatabase", 'offerThroughput': 400})
```

```python
 # Creates a unsharded collection that uses the DBs shared throughput
db.command({'customAction': "CreateCollection", 'collection': UNSHARDED_COLLECTION_NAME})
```

### <a name="writing-a-document"></a>Écriture d’un document
La commande suivante insère l’exemple de document que nous utilisons dans tout l’exemple. Nous obtenons sa valeur de champ _id unique pour pouvoir l’interroger dans les opérations suivantes.

```python
"""Insert a sample document and return the contents of its _id field"""
document_id = collection.insert_one({SAMPLE_FIELD_NAME: randint(50, 500)}).inserted_id
```

### <a name="readingupdating-a-document"></a>Lecture/mise à jour d’un document
La commande suivante interroge, met à jour et interroge de nouveau le document précédemment inséré.

```python
print("Found a document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))

collection.update_one({"_id": document_id}, {"$set":{SAMPLE_FIELD_NAME: "Updated!"}})
print("Updated document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))
```

### <a name="deleting-a-document"></a>Suppression d’un document
Enfin, nous supprimons le document que nous avons créé dans la collection.
```python
"""Delete the document containing document_id from the collection"""
collection.delete_one({"_id": document_id})
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez appris à créer un compte d’API pour MongoDB, à créer une base de données et une collection avec du code, puis à effectuer des opérations CRUD. 

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
