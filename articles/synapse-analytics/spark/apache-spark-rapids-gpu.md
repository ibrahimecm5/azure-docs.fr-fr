---
title: Apache Spark sur GPU
description: Introduction aux concepts de base d’Apache Spark sur GPU dans Synapse Analytics.
services: synapse-analytics
author: Niharikadutta
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 10/18/2021
ms.author: nidutta
ms.custom: ignite-fall-2021
ms.openlocfilehash: c37e1361a1f0864dc64af3bfb91eb6f8df293949
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494322"
---
# <a name="apache-spark-gpu-accelerated-pools-in-azure-synapse-analytics"></a>Pools Apache Spark accélérés par GPU dans Azure Synapse Analytics

Apache Spark est un framework de traitement parallèle qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analytique du Big Data. Apache Spark dans Azure Synapse Analytics est l’une des implémentations par Microsoft d’Apache Spark dans le cloud. 

Azure Synapse offre désormais la possibilité de créer des pools compatibles GPU Azure Synapse pour exécuter des charges de travail Spark en utilisant les [bibliothèques RAPIDS](https://nvidia.github.io/spark-rapids/) sous-jacentes qui utilisent l’énorme puissance de traitement parallèle des GPU pour accélérer le traitement. L’accélérateur RAPIDS pour Apache Spark vous permet d’exécuter vos applications Spark existantes **sans modification du code** juste en activant un paramètre de configuration, qui est préconfiguré pour un pool compatible GPU.
Vous pouvez choisir d’activer/de désactiver l’accélération GPU basée sur RAPIDS pour votre charge de travail ou des parties de votre charge de travail en définissant cette configuration :

```
spark.conf.set('spark.rapids.sql.enabled','true/false')
```

> [!NOTE]
> Les pools compatibles GPU Azure Synapse sont actuellement en préversion publique.

## <a name="rapids-accelerator-for-apache-spark"></a>Accélérateur RAPIDS pour Apache Spark

L’accélérateur RAPIDS Spark est un plug-in qui fonctionne en remplaçant le plan physique d’un travail Spark par des opérations GPU prises en charge et en exécutant ces opérations sur les GPU, accélérant ainsi le traitement. Cette bibliothèque est actuellement en préversion et ne prend pas en charge toutes les opérations Spark (voici une liste des [opérateurs actuellement pris en charge](https://nvidia.github.io/spark-rapids/docs/supported_ops.html), d’autres prises en charge sont progressivement ajoutées dans les nouvelles versions).

## <a name="cluster-configuration-options"></a>Options de configuration de cluster

Le plug-in de l’accélérateur RAPIDS prend uniquement en charge un mappage un-à-un entre les GPU et les exécuteurs. Cela signifie qu’un travail Spark doit demander des ressources de l’exécuteur et du pilote pouvant être prises en charge par les ressources du pool (en fonction du nombre de cœurs de CPU et de GPU disponibles). Pour répondre à cette condition et garantir une utilisation optimale de toutes les ressources du pool, nous avons besoin de la configuration suivante des pilotes et des exécuteurs pour une application Spark s’exécutant sur des pools compatibles GPU :


|Taille du pool | Options de taille du pilote | Cœurs de pilote | Mémoire du pilote (Go) | Cœurs de l’exécuteur | Mémoire de l’exécuteur (Go) | Nombre d’exécuteurs |
| :------ | :-------------- | :---------- | :------------- | :------------- | :------------------- | :------------------ |
| GPU-Large | Petit pilote | 4 | 30 | 12 | 60 | Nombre de nœuds dans le pool |
| GPU-Large | Pilote moyen | 7 | 30 | 9 | 60 | Nombre de nœuds dans le pool |
| GPU-XLarge | Pilote moyen | 8 | 40 | 14 | 80 | 4 * Nombre de nœuds dans le pool |
| GPU-XLarge | Grand pilote | 12 | 40 | 13 | 80 | 4 * Nombre de nœuds dans le pool |


Toute charge de travail qui ne répond pas à l’une des configurations ci-dessus ne sera pas acceptée. Cela permet de s’assurer que les travaux Spark sont exécutés avec la configuration la plus efficace et la plus performante en utilisant toutes les ressources disponibles sur le pool.

L’utilisateur peut définir la configuration ci-dessus par le biais de sa charge de travail. Pour les notebooks, l’utilisateur peut utiliser la commande magic `%%configure` pour définir l’une des configurations ci-dessus, comme indiqué ci-après.
Par exemple, en utilisant un grand pool avec trois nœuds :

```
%%configure -f
{
    "driverMemory": "30g",
    "driverCores": 4,
    "executorMemory": "60g",
    "executorCores": 12,
    "numExecutors": 3
}
```

## <a name="run-a-sample-spark-job-through-notebook-on-an-azure-synapse-gpu-accelerated-pool"></a>Exécuter un exemple de travail Spark par le biais d’un notebook sur un pool Azure Synapse accéléré par GPU

Avant de passer à cette section, il serait judicieux de vous familiariser avec les [concepts de base de l’utilisation d’un notebook](apache-spark-development-using-notebooks.md) dans Azure Synapse Analytics. Passons en revue les étapes permettant d’exécuter une application Spark utilisant l’accélération GPU. Vous pouvez écrire une application Spark dans les quatre langages pris en charge dans Synapse : PySpark (Python), Spark (Scala), SparkSQL et .NET pour Spark (C#).

1. Créez un pool compatible GPU comme décrit dans ce guide de [démarrage rapide](../quickstart-create-apache-gpu-pool-portal.md).

2. Créez un notebook et attachez-le au pool compatible GPU que vous avez créé à la première étape.

3. Définissez les configurations comme expliqué dans la section précédente.

4. Créez un exemple de dataframe en copiant le code ci-dessous dans la première cellule de votre notebook :

### <a name="scala"></a>[Scala](#tab/scala)

```scala
import org.apache.spark.sql.types.{IntegerType, StringType, StructField, StructType}
import org.apache.spark.sql.Row
import scala.collection.JavaConversions._

val schema = StructType( Array(
  StructField("emp_id", IntegerType),
  StructField("name", StringType),
  StructField("emp_dept_id", IntegerType),
  StructField("salary", IntegerType)
))

val emp = Seq(Row(1, "Smith", 10, 100000),
    Row(2, "Rose", 20, 97600),
    Row(3, "Williams", 20, 110000),
    Row(4, "Jones", 10, 80000),
    Row(5, "Brown", 40, 60000),
    Row(6, "Brown", 30, 78000)
  )

val empDF = spark.createDataFrame(emp, schema)
```

### <a name="python"></a>[Python](#tab/python)

```python
emp = [(1, "Smith", 10, 100000),
    (2, "Rose", 20, 97600),
    (3, "Williams", 20, 110000),
    (4, "Jones", 10, 80000),
    (5, "Brown", 40, 60000),
    (6, "Brown", 30, 78000)]

empColumns = ["emp_id", "name", "emp_dept_id", "salary"]

empDF = spark.createDataFrame(data=emp, schema=empColumns)
```

### <a name="c"></a>[C#](#tab/csharp)

```csharp
using Microsoft.Spark.Sql.Types;

var emp = new List<GenericRow>
{
    new GenericRow(new object[] { 1, "Smith", 10, 100000 }),
    new GenericRow(new object[] { 2, "Rose", 20, 97600 }),
    new GenericRow(new object[] { 3, "Williams", 20, 110000 }),
    new GenericRow(new object[] { 4, "Jones", 10, 80000 }),
    new GenericRow(new object[] { 5, "Brown", 40, 60000 }),
    new GenericRow(new object[] { 6, "Brown", 30, 78000 })
};

var schema = new StructType(new List<StructField>()
{
    new StructField("emp_id", new IntegerType()),
    new StructField("name", new StringType()),
    new StructField("emp_dept_id", new IntegerType()),
    new StructField("salary", new IntegerType())
});

DataFrame empDF = spark.CreateDataFrame(emp, schema);
```
---

5. Nous allons maintenant effectuer un agrégat en obtenant le salaire maximal par ID de département et afficher le résultat :

### <a name="scala"></a>[Scala](#tab/scala1)

```scala
val resultDF = empDF.groupBy("emp_dept_id").max("salary")
resultDF.show()
```

### <a name="python"></a>[Python](#tab/python1)

```python
resultDF = empDF.groupBy("emp_dept_id").max("salary")
resultDF.show()
```

### <a name="c"></a>[C#](#tab/csharp1)

```csharp
DataFrame resultDF = empDF.GroupBy("emp_dept_id").Max("salary");
resultDF.Show();
```
---

6. Vous pouvez voir les opérations de votre requête qui se sont exécutées sur des GPU en regardant dans le plan SQL via le serveur d’historique de Spark : ![Capture d’écran montrant un plan SQL de requête via le serveur d’historique](../media/quickstart-create-apache-spark-pool/spark-gpu-sql-plan.png)

## <a name="how-to-tune-your-application-for-gpus"></a>Comment paramétrer votre application pour les GPU

La plupart des travaux Spark peuvent voir une amélioration des performances en définissant les paramètres de configuration sur d’autres valeurs que les valeurs par défaut. Idem pour les travaux tirant parti du plug-in de l’accélérateur RAPIDS pour Apache Spark. [Cette documentation](https://nvidia.github.io/spark-rapids/docs/tuning-guide.html) fournit des instructions sur le paramétrage d’un travail Spark pour qu’il s’exécute sur des GPU en utilisant le plug-in RAPIDS.

## <a name="quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools"></a>Quotas et contraintes de ressource dans les pools compatibles GPU Azure Synapse

### <a name="workspace-level"></a>Niveau Espace de travail

Chaque espace de travail Azure Synapse est fourni avec un quota par défaut de 50 vCores de GPU. Pour augmenter votre quota de cœurs de GPU, envoyez un e-mail à AzureSynapseGPU@microsoft.com avec le nom de votre espace de travail, la région et le quota de GPU total nécessaire à votre charge de travail.

## <a name="next-steps"></a>Étapes suivantes
- [Azure Synapse Analytics](../overview-what-is.md)
