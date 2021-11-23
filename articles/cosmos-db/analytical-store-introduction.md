---
title: Qu’est-ce que le magasin analytique Azure Cosmos DB ?
description: Découvrez le magasin transactionnel (basé sur des lignes) et analytique (basé sur des colonnes) d’Azure Cosmos DB. Avantages du magasin analytique, impact sur les performances pour les charges de travail à grande échelle et synchronisation automatique des données d’un magasin transactionnel à un magasin analytique
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 712b1d3e7fde41991f9cea2d62e7e0864224509d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135207"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Qu’est-ce que le magasin analytique Azure Cosmos DB ?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Le magasin analytique Azure Cosmos DB est un magasin de colonnes totalement isolé qui permet d’effectuer des traitements analytiques à grande échelle sur les données opérationnelles de votre base de données Azure Cosmos DB sans incidence sur vos charges de travail transactionnelles. 

Le magasin transactionnel Azure Cosmos DB est indépendant des schémas et il vous permet d’itérer sur vos applications transactionnelles sans avoir à vous soucier de la gestion des schémas ou des index. À l’inverse, le magasin analytique Azure Cosmos DB est schématisé pour optimiser les performances des requêtes analytiques. Cet article décrit en détail le stockage analytique.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Défis liés à l’analytique à grande échelle des données opérationnelles

Les données opérationnelles multi-modèles dans un conteneur de Azure Cosmos DB sont stockées en interne dans un « magasin transactionnel » basé sur des lignes indexées. Le format du magasin de lignes est conçu pour permettre des requêtes opérationnelles ainsi que des lectures et des écritures transactionnelles rapides avec des temps de réponse de l’ordre de la milliseconde. Si votre jeu de données devient volumineux, les requêtes analytiques complexes peuvent être coûteuses en termes de débit approvisionné sur les données stockées dans ce format. Une consommation élevée de débit approvisionné a également un impact sur les performances des charges de travail transactionnelles utilisées par vos applications et services en temps réel.

Traditionnellement, pour analyser de grandes quantités de données, les données opérationnelles sont extraites du magasin transactionnel de Azure Cosmos DB et stockées dans une couche de données distincte. Par exemple, les données sont stockées dans un entrepôt de données ou un lac de données dans un format approprié. Ces données sont ensuite utilisées pour l’analytique à grande échelle et analysées à l’aide du moteur de calcul, comme les clusters Apache Spark. Cette séparation des couches de calcul et de stockage analytique des données opérationnelles entraîne une latence supplémentaire, car les pipelines ETL (Extract, Transform, Load) sont exécutés moins fréquemment pour réduire l’impact potentiel sur vos charges de travail transactionnelles.

Les pipelines ETL deviennent également complexes lors du traitement des mises à jour des données opérationnelles par rapport au traitement des données opérationnelles nouvellement reçues. 

## <a name="column-oriented-analytical-store"></a>Magasin analytique orienté colonne

Le magasin analytique Azure Cosmos DB traite les défis de complexité et de latence qui se produisent avec les pipelines ETL traditionnels. Le magasin analytique Azure Cosmos DB peut automatiquement synchroniser vos données opérationnelles dans un magasin en colonnes séparé. Le format du magasin de colonnes est approprié pour les requêtes analytiques à grande échelle qui sont exécutées de manière optimisée, ce qui permet d’améliorer la latence de ces requêtes.

À l’aide d’Azure Synapse Link, vous pouvez désormais créer des solutions HTAP non ETL en établissant une liaison directe avec le magasin analytique Azure Cosmos DB à partir d’Azure Synapse Analytics. Elle vous permet d’exécuter des analyses à grande échelle en temps quasi réel sur vos données opérationnelles.

## <a name="features-of-analytical-store"></a>Fonctionnalités du magasin analytique 

Lorsque vous activez le magasin analytique sur un conteneur Azure Cosmos DB, un nouveau magasin de colonnes est créé en interne en fonction des données opérationnelles de votre conteneur. Ce magasin de colonnes est maintenu séparément du magasin transactionnel en lignes pour ce conteneur. Les insertions, les mises à jour et les suppressions apportées à vos données opérationnelles sont automatiquement synchronisées avec le magasin analytique. Vous n’avez pas besoin du flux de modification ou de l’ETL pour synchroniser les données.

## <a name="column-store-for-analytical-workloads-on-operational-data"></a>Magasin de colonnes pour les charges de travail analytiques sur les données opérationnelles

Les charges de travail analytiques impliquent généralement des agrégations et des analyses séquentielles de champs sélectionnés. En stockant les données dans un ordre colonne-principal, le magasin analytique permet de sérialiser un groupe de valeurs pour chaque champ. Ce format réduit le nombre d’IOPS nécessaires pour analyser ou calculer des statistiques sur des champs spécifiques. Il améliore considérablement les temps de réponse des requêtes pour les analyses sur de grands jeux de données. 

Par exemple, si vos tables opérationnelles sont au format suivant :

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Exemple de table opérationnelle" border="false":::

Le magasin de lignes conserve les données ci-dessus dans un format sérialisé par ligne sur le disque. Ce format permet des lectures, des écritures et des requêtes opérationnelles plus rapides, faisant suite une demande de retour d’informations sur un produit spécifique, par exemple. Toutefois, au fur et à mesure que la taille du jeu de données augmente, l’exécution de requêtes analytiques complexes sur les données peut s’avérer plus coûteuse. Par exemple, si vous souhaitez obtenir « les tendances des ventes d’un produit de la catégorie "Équipement" dans différentes unités commerciales et sur différents mois », vous devrez lancer une requête complexe. Les analyses de grande envergure sur ce jeu de données peuvent être coûteuses en termes de débit approvisionné et avoir également un impact sur les performances des charges de travail transactionnelles qui alimentent vos applications et services en temps réel.

Le magasin analytique, qui est un magasin de colonnes, est mieux adapté à ce type de requêtes car il sérialise ensemble des champs de données similaires et réduit le nombre d’IOPS sur le disque.

L’image suivante représente le magasin de lignes transactionnelles et le magasin de colonnes analytiques dans Azure Cosmos DB :

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Magasin de lignes transactionnelles et magasin de lignes analytiques dans Azure Cosmos DB" border="false":::

## <a name="decoupled-performance-for-analytical-workloads"></a>Performances découplées pour les charges de travail analytiques

Les requêtes analytiques n’ont aucune incidence sur les performances de vos charges de travail transactionnelles, car le magasin analytique est distinct du magasin transactionnel.  Le magasin analytique n’a pas besoin d’allouer des unités de requête distinctes.

## <a name="auto-sync"></a>Synchronisation automatique

La synchronisation automatique fait référence à la fonctionnalité complètement managée d’Azure Cosmos DB où les insertions, les mises à jour, les suppressions de données opérationnelles sont automatiquement synchronisées à partir du magasin transactionnel vers le magasin analytique en temps quasi-réel en temps quasi-réel. La latence de synchronisation automatique est généralement en moins de 2 minutes. Dans le cas d’une base de données de débit partagé avec un grand nombre de conteneurs, la latence de la synchronisation automatique des conteneurs individuels peut être supérieure et prendre jusqu’à 5 minutes. Nous aimerions en savoir plus, pour savoir si cette latence est adaptée à vos scénarios. Pour cela, contactez l’équipe [Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

À la fin de chaque exécution du processus de synchronisation automatique, vos données transactionnelles seront immédiatement disponibles pour les runtimes Azure Synapse Analytics :

* Les pools Spark d’Azure Synapse Analytics peuvent lire toutes les données, y compris les mises à jour les plus récentes, par le biais de tables Spark, qui sont mises à jour automatiquement, ou via la commande `spark.read`, qui lit toujours le dernier état des données.

*  Les pools SQL serverless d’Azure Synapse Analytics peuvent lire toutes les données, y compris les mises à jour les plus récentes, par le biais de vues, qui sont mises à jour automatiquement, ou via les commandes `SELECT` et ` OPENROWSET`, qui lisent toujours l’état le plus récent des données.

> [!NOTE]
> Vos données transactionnelles seront synchronisées au magasin analytique, même si la TTL transactionnelle est inférieure à deux minutes. 

## <a name="scalability--elasticity"></a>Extensibilité et élasticité

En utilisant le partitionnement horizontal, le magasin transactionnel Azure Cosmos DB peut mettre à l’échelle de manière élastique le stockage et le débit sans temps d’arrêt. Le partitionnement horizontal dans le magasin transactionnel est évolutif et élastique lors de la synchronisation automatique afin de garantir la synchronisation des données avec le magasin analytique quasiment en temps réel. La synchronisation des données se produit quel que soit le débit de trafic transactionnel, qu’il s’agisse de 1 000 opérations/s ou 1 million d’opérations/s, et elle n’a pas d’impact sur le débit approvisionné dans le magasin transactionnel. 

## <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Gérer automatiquement les mises à jour de schéma

Le magasin transactionnel Azure Cosmos DB est indépendant des schémas et il vous permet d’itérer sur vos applications transactionnelles sans avoir à vous soucier de la gestion des schémas ou des index. À l’inverse, le magasin analytique Azure Cosmos DB est schématisé pour optimiser les performances des requêtes analytiques. Grâce à la capacité de synchronisation automatique, Azure Cosmos DB gère l’inférence du schéma sur les dernières mises à jour du magasin transactionnel. Il gère aussi la représentation du schéma dans le magasin analytique, qui comprend la gestion des types de données imbriqués.

À mesure que votre schéma évolue et que de nouvelles propriétés sont ajoutées au fil du temps, le magasin analytique présente automatiquement un schéma uni dans tous les schémas historiques du magasin transactionnel.

> [!NOTE]
> Dans le contexte du magasin analytique, nous considérons les structures suivantes comme des propriétés :
> * « Éléments » ou « paires chaîne-valeur séparées par le signe `:` » JSON.
> * Objets JSON, délimités par les signes `{` et `}`.
> * Tableaux JSON, délimités par les signes `[` et `]`.


### <a name="schema-constraints"></a>Contraintes de schéma

Les contraintes suivantes s’appliquent aux données opérationnelles dans Azure Cosmos DB lorsque vous activez le magasin analytique pour inférer et représenter automatiquement le schéma correctement :

* Vous disposez d’un maximum de 1 000 propriétés à n’importe quel niveau d’imbrication dans le schéma, et d’une profondeur d’imbrication maximale de 127.
  * Seules les 1 000 premières propriétés sont représentées dans le magasin analytique.
  * Seuls les 127 premiers niveaux imbriqués sont représentés dans le magasin analytique.
  * Le premier niveau d’un document JSON est son niveau racine `/`.
  * Les propriétés du premier niveau du document sont représentées sous forme de colonnes.


* Exemples de scénarios :
  * Si le premier niveau de votre document a 2 000 propriétés, seules les 1 000 premières sont représentées.
  * Si vos documents ont 5 niveaux avec 200 propriétés chacun, toutes les propriétés sont représentées.
  * Si vos documents ont 10 niveaux avec 400 propriétés chacun, seuls les 2 premiers niveaux sont entièrement représentés dans le magasin analytique. La moitié du troisième niveau est également représentée.

* Le document hypothétique ci-dessous contient 4 propriétés et 3 niveaux.
  * Les niveaux sont `root`, `myArray` et la structure imbriquée dans `myArray`.
  * Les propriétés sont `id`, `myArray`, `myArray.nested1` et `myArray.nested2`.
  * La représentation du magasin analytique compte 2 colonnes, `id` et `myArray`. Vous pouvez utiliser les fonctions Spark ou T-SQL pour exposer également les structures imbriquées en tant que colonnes.


```json
{
  "id": "1",
  "myArray": [
    "string1",
    "string2",
    {
      "nested1": "abc",
      "nested2": "cde"
    }
  ]
}
```

* Bien que les documents JSON (et les collections/conteneurs Cosmos DB) respectent la casse du point de vue de l’unicité, le magasin analytique le fait pas.

  * **Dans le même document :** les noms de propriétés dans le même niveau doivent être uniques en cas de comparaison ne respectant pas la casse. Par exemple, le document JSON suivant contient « Name » et « name » dans le même niveau. Bien qu’il s’agisse d’un document JSON valide, il ne répond pas à la contrainte d’unicité, et n’est donc pas pleinement représenté dans le magasin analytique. Dans cet exemple, « Name » et « name » sont identiques en cas de comparaison ne respectant pas la casse. Seul `"Name": "fred"` sera représenté dans le magasin analytique, car il s’agit de la première occurrence. Et `"name": "john"` ne sera pas représentés du tout.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **Dans des documents différents :** les propriétés dans le même niveau et portant le même nom, mais présentant des casses différentes, sont représentées dans la même colonne, en utilisant le format de nom de la première occurrence. Par exemple, les documents JSON suivants ont `"Name"` et `"name"` dans le même niveau. Étant donné que le premier format de document est `"Name"`, celiui-ci sera utilisé pour représenter le nom de la propriété dans le magasin analytique. En d’autres termes, le nom de colonne dans le magasin analytique sera `"Name"`. `"fred"` et `"john"` sont représentés dans la colonne `"Name"`.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Le premier document de la collection définit le schéma initial du magasin analytique.
  * Les documents contenant plus de propriétés que le schéma initial génèrent de nouvelles colonnes dans le magasin analytique.
  * Les colonnes ne peuvent pas être supprimées.
  * La suppression de tous les documents d’une collection ne réinitialise pas le schéma du magasin analytique.
  * Il n’existe pas de contrôle de version de schéma. La dernière version inférée du magasin transactionnel est ce que vous verrez dans le magasin analytique.

* Actuellement, Azure Synapse Spark ne peut pas lire les propriétés qui contiennent des caractères spéciaux dans leurs noms (indiqués ci-dessous). Azure Synapse SQL serverless n’est pas affecté.
  * : (deux points)
  * ` (accent grave)
  * , (virgule)
  * ; (point-virgule)
  * {}
  * ()
  * \n
  * \t
  * = (signe égal)
  * " (guillemet)
 
* Si vous avez des noms de propriétés qui utilisent les caractères répertoriés ci-dessus, les alternatives sont les suivantes :
   * Modifiez votre modèle de données à l’avance pour éviter ces caractères.
   * Étant donné que nous ne prenons pas en charge la réinitialisation de schéma, vous pouvez modifier votre application pour ajouter une propriété redondante avec un nom similaire, en évitant ces caractères.
   * Utilisez le flux de modification pour créer une vue matérialisée de votre conteneur sans ces caractères dans les noms de propriétés.
   * Utilisez la toute nouvelle option `dropColumn`Spark pour ignorer les colonnes affectées lors du chargement des données dans un dataframe. La syntaxe pour la suppression d’une colonne hypothétique nommée « FirstName, LastNAme », qui contient une virgule, est la suivante :

```Python
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName")\
     .load()
```

* Azure Synapse Spark prend désormais en charge les propriétés incluant des espaces blancs dans leurs noms.

* Les types de données BSON suivants ne sont pas pris en charge et ne sont pas représentés dans le magasin analytique :
  * Decimal128
  * Expression régulière
  * Pointeur de base de données
  * JavaScript
  * Symbole
  * MinKey/MaxKey 

* Lorsque vous utilisez des chaînes DateTime qui suivent la norme ISO 8601 UTC, attendez-vous au comportement suivant :
  * Les pools Spark dans Azure Synapse représenteront ces colonnes comme `string`.
  * Les pools serverless SQL dans Azure Synapse représenteront ces colonnes comme `varchar(8000)`.

* Les pools serverless SQL dans Azure Synapse prennent en charge des ensembles de résultats contenant jusqu’à 1000 colonnes, et l’exposition de colonnes imbriquées est également comptabilisée dans cette limite. Tenez compte de ces informations lors de la conception de votre architecture de données et de la modélisation de vos données transactionnelles.


### <a name="schema-representation"></a>Représentation du schéma

Il existe deux types de représentation de schéma dans le magasin analytique. Ces types définissent la méthode de représentation de schéma pour tous les conteneurs dans le compte de base de données. Ils présentent des compromis entre la simplicité de l’expérience de requête et la commodité d’une représentation en colonnes plus inclusive pour les schémas polymorphes.

* Représentation de schéma bien définie, option par défaut pour les comptes d’API SQL (Core). 
* Représentation de schéma de fidélité optimale, option par défaut pour les comptes d’API Azure Cosmos DB pour MongoDB.

#### <a name="full-fidelity-schema-for-sql-api-accounts"></a>Schéma de fidélité optimale pour les comptes API SQL

Il est possible d’utiliser un schéma de fidélité optimale pour les comptes API SQL (Core), au lieu de l’option par défaut, en définissant le type de schéma lors de la première activation de Synapse Link sur un compte Cosmos DB. Voici les considérations relatives à la modification du type de représentation de schéma par défaut :

 * Cette option n’est valide que pour les comptes pour lesquels la fonctionnalité Synapse Link n’est **pas** déjà activée.
 * Il n’est pas possible de réinitialiser le type de représentation du schéma d’une précision bien définie à une fidélité optimale ou inversement.
 * Actuellement, les comptes Azure Cosmos DB API pour MongoDB ne sont pas compatibles avec cette possibilité de modifier la représentation du schéma. Tous les comptes MongoDB auront toujours le type de représentation de schéma de fidélité optimale.
 * Actuellement, cette modification n’est pas possible via le portail Azure. Tous les comptes de base de données pour lesquels Synapse Link est activé par le portail Azure auront le type de représentation de schéma par défaut : schéma bien défini.
 
Le type de représentation du schéma doit être choisi en même temps que l’activation de Synapse Link sur le compte, à l’aide d’Azure CLI ou de PowerShell.
 
 Avec Azure CLI :
 ```cli
 az cosmosdb create --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --subscription MySubscription --analytical-storage-schema-type "FullFidelity" --enable-analytical-storage true
 ```
 
> [!NOTE]
> Dans la commande ci-dessus, remplacez `create` par `update` pour les comptes existants.
 
  Avec PowerShell :
  ```
   New-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount  -EnableAnalyticalStorage true -AnalyticalStorageSchemaType "FullFidelity"
   ```
 
> [!NOTE]
> Dans la commande ci-dessus, remplacez `New-AzCosmosDBAccount` par `Update-AzCosmosDBAccount` pour les comptes existants.
 


#### <a name="well-defined-schema-representation"></a>Représentation de schéma bien définie

La représentation de schéma bien définie crée une représentation tabulaire simple des données indépendantes du schéma dans le magasin transactionnel. La représentation de schéma bien définie prend en compte les considérations suivantes :

* Le premier document définit le schéma de base, et la propriété doit toujours avoir le même type dans tous les documents. Les exceptions sont les suivantes :
  * Du type de données Null à tout autre type de données. La première occurrence non Null définit le type de données de la colonne. Tout document ne suivant pas le premier type de données non Null n’est pas représenté dans le magasin analytique.
  * De `float` à `integer` Tous les documents sont représentés dans le magasin analytique.
  * De `integer` à `float` Tous les documents sont représentés dans le magasin analytique. Toutefois, pour lire ces données avec des pools serverless Azure Synapse SQL, vous devez utiliser une clause WITH pour convertir la colonne en `varchar`. Il est possible de reconvertir cette conversion initiale en nombre. Vérifiez l’exemple ci-dessous, où la valeur initiale **num** était entière, et la deuxième flottante.

```SQL
SELECT CAST (num as float) as num
FROM OPENROWSET(PROVIDER = 'CosmosDB',
                CONNECTION = '<your-connection',
                OBJECT = 'IntToFloat',
                SERVER_CREDENTIAL = 'your-credential'
) 
WITH (num varchar(100)) AS [IntToFloat]
```

  * Les propriétés qui ne suivent pas le type de données de schéma de base ne sont pas représentées dans le magasin analytique. Par exemple, considérez les 2 documents ci-dessous. Le premier a défini le schéma de base du magasin analytique. Le deuxième, où `id` est `2`, n’a pas de schéma bien défini, car la propriété `"a"` est une chaîne alors que, dans le premier document, la propriété `"a"` est un nombre. Dans ce cas, le magasin analytique inscrit le type de données `"a"` en tant que type de données `integer` pour toute la durée de vie du conteneur. Le deuxième document restera inclus dans un magasin analytique, mais pas sa propriété `"a"`.
  
    * `{"id": "1", "a":123}` 
    * `{"id": "2", "a": "str"}`
     
 > [!NOTE]
 > Cette condition ne s’applique pas aux propriétés Null. Par exemple, `{"a":123} and {"a":null}` est encore bien défini.

* Les types de tableau doivent contenir un type répété unique. Par exemple, `{"a": ["str",12]}` n’est pas un schéma bien défini car le tableau contient un mélange de types d’entiers et de chaînes.

> [!NOTE]
> Si le magasin analytique Azure Cosmos DB suit la représentation de schéma bien définie et que la spécification ci-dessus n’est pas respectée par certains éléments, ceux-ci ne sont pas inclus dans le magasin analytique.

* Un comportement différent est attendu en ce qui concerne les différents types dans un schéma bien défini :
  * Les pools Spark dans Azure Synapse représenteront ces valeurs comme `undefined`.
  * Les pools serverless SQL dans Azure Synapse représenteront ces valeurs comme `NULL`.

* Un comportement différent est attendu en ce qui concerne les valeurs `null` explicites :
  * Les pools Spark dans Azure Synapse liront ces valeurs comme `0` (zéro). Et elles deviendront `undefined` dès que la colonne aura une valeur non nulle.
  * Les pools SQL serverless dans Azure Synapse liront ces valeurs comme `NULL`.
    
* Un comportement différent est attendu en ce qui concerne les colonnes manquantes :
  * Les pools Spark dans Azure Synapse représenteront ces colonnes comme `undefined`.
  * Les pools serverless SQL dans Azure Synapse représenteront ces colonnes comme `NULL`.


#### <a name="full-fidelity-schema-representation"></a>Représentation de schéma avec une fidélité optimale

La représentation du schéma de fidélité optimale est conçue pour gérer l’intégralité des schémas polymorphes dans les données opérationnelles indépendantes du schéma. Dans cette représentation de schéma, aucun élément n’est supprimé du magasin analytique, même si les contraintes de schéma bien définies (qui ne sont pas des champs de type de données mixtes ou des tableaux de types de données mixtes) ne sont pas respectées.

Cela est possible en traduisant les propriétés de nœud terminal des données opérationnelles dans le magasin analytique avec des colonnes distinctes en fonction du type de données des valeurs de la propriété. Les noms de propriété de nœud terminal sont étendus avec des types de données, tels qu’un suffixe dans le schéma de magasin analytique, de sorte qu’ils peuvent être des requêtes sans ambiguïté.

Dans la représentation du schéma de fidélité optimale, chaque type de données de chaque propriété génère une colonne pour ce type de données. Chaque colonne compte comme l’une des 1 000 propriétés maximales.

Prenons l’exemple de document suivant dans le magasin transactionnel :

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

La propriété de nœud terminal `streetNo` dans l’objet imbriqué `address` sera représentée dans le schéma de magasin analytique en tant que colonne `address.object.streetNo.int32`. Le type de données est ajouté en tant que suffixe à la colonne. De cette façon, si un autre document est ajouté au magasin transactionnel où la valeur de la propriété de nœud terminal `streetNo` est « 123 » (notez qu’il s’agit d’une chaîne), le schéma du magasin analytique évolue automatiquement sans modification du type d’une colonne écrite précédemment. Nouvelle colonne ajoutée au magasin analytique en tant que `address.object.streetNo.string` où cete valeur « 123 » est stockée.

**Mappage de type de données au suffixe**

Voici un mappage de tous les types de données de propriété et de leurs représentations de suffixe dans le magasin analytique :

|Type de données d’origine  |Suffixe  |Exemple  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Array | ".array" |    ["a", "b"]|
|Binary | ".binary" |0|
|Booléen    | ".bool"   |Vrai|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|String|    ".string" | "ABC"|
|Timestamp |    ".timestamp" |  Timestamp(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Document   |".object" |    {"a": "a"}|

* Un comportement différent est attendu en ce qui concerne les valeurs `null` explicites :
  * Les pools Spark dans Azure Synapse liront ces valeurs comme `0` (zéro).
  * Les pools SQL serverless dans Azure Synapse liront ces valeurs comme `NULL`.
  
* Un comportement différent est attendu en ce qui concerne les colonnes manquantes :
  * Les pools Spark dans Azure Synapse représenteront ces colonnes comme `undefined`.
  * Les pools serverless SQL dans Azure Synapse représenteront ces colonnes comme `NULL`.

## <a name="cost-effective-archival-of-historical-data"></a>Archivage rentable des données historiques

La hiérarchisation des données fait référence à la séparation des données entre les infrastructures de stockage optimisées pour différents scénarios. Cela améliore les performances globales et la rentabilité de la pile de données de bout en bout. Avec le magasin analytique, Azure Cosmos DB prend désormais en charge la hiérarchisation automatique des données du magasin transactionnel vers le magasin analytique avec différentes dispositions de données. Le magasin analytique optimisé en termes de coût de stockage par rapport au magasin transactionnel vous permet de conserver des échéances plus longues pour l’analyse historique des données opérationnelles.

Une fois le magasin analytique activé, selon les besoins de conservation des données des charges de travail transactionnelles, vous pouvez configurer la propriété « Transactional Store Time to Live (Transactional TTL) » de façon à ce que les enregistrements soient automatiquement supprimés du magasin transactionnel au bout d’un certain laps de temps. De même, la propriété « Analytical Store Time To Live (Analytical TTL) » vous permet de gérer le cycle de vie des données conservées dans le magasin analytique indépendamment du magasin transactionnel. En activant le magasin analytique et en configurant les propriétés TTL, vous pouvez hiérarchiser et définir de manière transparente la période de conservation des données pour les deux magasins.

> [!NOTE]
>Actuellement, le magasin analytique ne prend pas en charge la sauvegarde ni la restauration. Votre stratégie de sauvegarde ne peut pas être planifiée en s’appuyant sur le magasin analytique. Pour plus d’informations, consultez la section relative aux limites de [ce document](synapse-link.md#limitations). Il est important de noter que les données du magasin analytique ont un schéma différent de celui qui existe dans le magasin transactionnel. Bien que vous puissiez générer des instantanés des données de votre magasin analytique, sans frais en unités de requête, nous ne pouvons pas garantir l’utilisation de cet instantané pour alimenter le magasin transactionnel. Ce processus n’est pas pris en charge.

## <a name="global-distribution"></a>Diffusion mondiale

Si vous avez un compte Azure Cosmos DB distribué globalement, une fois que vous avez activé le magasin analytique pour un conteneur, il est disponible dans toutes les régions de ce compte.  Toutes les modifications apportées aux données opérationnelles sont répliquées globalement dans toutes les régions. Vous pouvez effectuer des recherches analytiques efficaces sur la copie régionale la plus proche de vos données dans Azure Cosmos DB.

## <a name="partitioning"></a>Partitionnement

Le partitionnement du magasin analytique est complètement indépendant du partitionnement dans le magasin transactionnel. Par défaut, les données dans le magasin analytique ne sont pas partitionnées. Si vos requêtes analytiques ont des filtres fréquemment utilisés, vous avez la possibilité d’opérer un partitionnement sur la base de ces champs pour améliorer les performances des requêtes. Pour plus d’informations, consultez les articles [Présentation du partitionnement personnalisé](custom-partitioning-analytical-store.md) et [Comment configurer un partitionnement personnalisé](configure-custom-partitioning.md).  

## <a name="security"></a>Sécurité

* L’**authentification auprès du magasin analytique** est la même qu’auprès du magasin transactionnel pour une base de données particulière. Vous pouvez utiliser des clés primaires ou en lecture seule pour l’authentification. Vous pouvez tirer parti du service lié dans Synapse Studio pour empêcher le collage des clés Azure Cosmos DB dans les notebooks Spark. Pour Azure Synapse SQL serverless, vous pouvez utiliser les informations d’identification SQL pour empêcher également le collage des clés Azure Cosmos DB dans les notebooks SQL. L’accès à ces services liés ou à ces informations d’identification est ouvert à toute personne ayant accès à l’espace de travail.

* **Isolement réseau à l’aide de points de terminaison privés** : vous pouvez contrôler indépendamment l’accès réseau aux données dans le magasin transactionnel et le magasin analytique. L’isolement réseau s’effectue à l’aide de points de terminaison privés managés distincts pour chaque magasin, au sein de réseaux virtuels managés dans les espaces de travail Azure Synapse. Pour plus d’informations, consultez l’article [Configurer des points de terminaison privés pour le magasin analytique](analytical-store-private-endpoints.md).

* **Chiffrement des données avec des clés managées par le client** : vous pouvez chiffrer les données du magasin transactionnel et du magasin analytique de manière fluide en utilisant les mêmes clés managées par le client, de manière automatique et transparente. Azure Synapse Link prend uniquement en charge la configuration des clés gérées par le client en utilisant l’identité managée de votre compte Azure Cosmos DB. Vous devez configurer l’identité managée de votre compte dans votre stratégie d’accès Azure Key Vault avant d’[activer Azure Synapse Link](configure-synapse-link.md#enable-synapse-link) sur votre compte. Pour en savoir plus, consultez l’article [Configurer des clés gérées par le client en utilisant les identités managées des comptes Azure Cosmos DB](how-to-setup-cmk.md#using-managed-identity).

## <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Support de plusieurs runtimes Azure Synapse Analytics

Le magasin analytique est optimisé pour fournir une extensibilité, une élasticité et des performances pour les charges de travail analytiques sans aucune dépendance des runtimes de calcul. La technologie de stockage est auto-gérée pour optimiser vos charges de travail analytiques sans effort manuel.

En découplant le système de stockage analytique du système de calcul analytique, les données du magasin analytique Azure Cosmos DB peuvent être interrogées simultanément à partir des différents runtimes analytiques pris en charge par Azure Synapse Analytics. À l’heure actuelle, Azure Synapse Analytics prend en charge Apache Spark et le pool SQL serverless avec le magasin analytique Azure Cosmos DB.

> [!NOTE]
> Vous pouvez uniquement lire à partir du magasin analytique à l’aide des runtimes d’Azure Synapse Analytics. L’inverse est également vrai : les runtimes d’Azure Synapse Analytics peuvent uniquement lire à partir du magasin analytique. Seul le processus de synchronisation automatique peut modifier des données dans le magasin analytique. Vous pouvez réécrire des données dans le magasin transactionnel de Cosmos DB à l’aide du pool Spark d’Azure Synapse Analytics, en utilisant le Kit de développement logiciel (SDK) OLTP Azure Cosmos DB intégré.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Tarification

Le magasin analytique suit un modèle de tarification basé sur la consommation dans lequel vous êtes facturé :

* Stockage : volume des données conservées dans le magasin analytique chaque mois, y compris les données historiques telles que définies par le TTL analytique.

* Opérations d’écriture analytique : synchronisation complètement managée des mises à jour des données opérationnelles vers le magasin analytique à partir du magasin transactionnel (synchronisation automatique)

* Opérations de lecture analytique : opérations de lecture effectuées sur le magasin analytique à partir des runtimes du pool Azure Synapse Analytics Spark et du pool SQL serverless.

La tarification du magasin analytique est distincte du modèle de tarification du magasin de transactions. Il n’existe aucun concept d’unités de demande approvisionnées dans le magasin analytique. Pour plus d’informations sur le modèle de tarification du magasin analytique, consultez la [page de tarification Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

Les données dans le magasin d’analytiques sont accessibles uniquement via Azure Synapse Link, qui s’effectue dans les runtimes Azure Synapse Analytics : les pools Apache Spark Azure Synapse et les pools SQL serverless Azure Synapse. Consultez la [page de tarification d’Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) pour obtenir des informations complètes sur le modèle de tarification pour accéder aux données dans le magasin analytique.

Afin d’obtenir une estimation précise des coûts d’activation du magasin d’analytique sur un conteneur Azure Cosmos DB, du point de vue du magasin analytique, vous pouvez utiliser l’[outil de planification Azure Cosmos DB Capacity](https://cosmos.azure.com/capacitycalculator/) et obtenir une estimation des coûts de votre magasin analytique et des opérations d’écriture. Les coûts des opérations de lecture analytique dépendent des caractéristiques de la charge de travail analytique mais, selon une estimation précise, l’analyse de 1 To de données dans le magasin analytique entraîne généralement 130 000 opérations de lecture analytique et se traduit par un coût de 0,065 USD.

> [!NOTE]
> Les estimations des opérations de lecture du magasin analytique ne sont pas incluses dans le module de calcul du coût Cosmos DB, car elles sont une fonction de votre charge de travail analytique. Bien que l’estimation ci-dessus soit destinée à l’analyse de 1 To de données dans le magasin analytique, l’application de filtres réduit le volume de données analysées et détermine le nombre exact d’opérations de lecture analytique en fonction du modèle de tarification de la consommation. Une preuve de concept relative à la charge de travail analytique produirait une estimation plus fine des opérations de lecture analytique. Cette estimation n’inclut pas le coût d’Azure Synapse Analytics.


## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Durée de vie (TTL) analytique

La durée de vie analytique indique la durée pendant laquelle les données doivent être conservées dans votre magasin analytique, pour un conteneur. 

Si le magasin analytique est activé, les insertions, les mises à jour et les suppressions apportées aux données opérationnelles sont automatiquement synchronisées entre le magasin transactionnel et le magasin analytique, quelle que soit la configuration de la durée de vie transactionnelle. La conservation de ces données opérationnelles dans le magasin analytique peut être contrôlée par la valeur de la durée de vie analytique au niveau du conteneur, comme indiqué ci-dessous :

La durée de vie analytique sur un conteneur est définie à l’aide de la propriété `AnalyticalStoreTimeToLiveInSeconds` :

* Si la valeur est définie sur « 0 », manquante (ou définie sur null) : le magasin analytique est désactivé et aucune donnée n’est répliquée depuis le magasin transactionnel vers le magasin analytique

* S’il existe et que la valeur est définie sur « -1 » : le magasin analytique conserve toutes les données d’historique, indépendamment de la conservation des données dans le magasin transactionnel. Ce paramètre indique que le magasin analytique a une conservation infinie de vos données opérationnelles

* S’il existe et que la valeur est définie sur un nombre positif « n » : les éléments expirent du magasin analytique « n » secondes après leur dernière heure de modification dans le magasin transactionnel. Ce paramètre peut être utilisé si vous souhaitez conserver vos données opérationnelles pendant une période limitée dans le magasin analytique, indépendamment de la conservation des données dans le magasin transactionnel.

Éléments à prendre en considération :

*   Une fois que le magasin analytique est activé avec une valeur TTL analytique, il peut être mis à jour avec une valeur valide différente ultérieurement. 
*   Si la durée de vie transactionnelle peut être définie au niveau du conteneur ou de l’élément, la durée de vie analytique ne peut être définie que sur le niveau du conteneur actuellement.
*   Vous pouvez effectuer une conservation plus longue de vos données opérationnelles dans le magasin analytique en définissant la durée de vie des analyses > = transaction TTL au niveau du conteneur.
*   Le magasin analytique peut être créé pour mettre en miroir le magasin transactionnel en définissant la durée de vie transactionnelle sur la valeur de durée de vie de l’analytique TTL.

Comment activer le magasin analytique sur un conteneur :

* À partir du portail Azure, l’option de TTL analytique, lorsqu’elle est activée, est définie sur la valeur par défaut -1. Vous pouvez modifier cette valeur en « n » secondes en accédant aux paramètres du conteneur sous Explorateur de données. 
 
* À partir du Kit de développement logiciel (SDK) Azure Management, des Kits de développement logiciel (SDK) Azure Cosmos DB, de PowerShell ou de l’interface de ligne de commande, vous pouvez activer l’option de TTL analytique en lui affectant la valeur -1 ou « n » secondes. 

Pour plus d’informations, consultez [Guide pratique pour configurer la durée de vie analytique d’un conteneur](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les documents suivants :

* [Synapse Link pour Azure Cosmos DB](synapse-link.md)

* Consultez le module Learn pour savoir comment [Concevoir un traitement transactionnel et analytique hybride avec Azure Synapse Analytics](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/)

* [Prise en main d’Azure Synapse Link pour Azure Cosmos DB](configure-synapse-link.md)

* [Forum aux questions sur Azure Synapse Link pour Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)

* [Cas d’utilisation d’Azure Synapse Link pour Azure Cosmos DB](synapse-link-use-cases.md)
