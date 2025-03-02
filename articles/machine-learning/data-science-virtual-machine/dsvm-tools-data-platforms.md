---
title: Plateformes de données prises en charge
titleSuffix: Azure Data Science Virtual Machine
description: Apprenez-en davantage sur les outils et plateformes de données pris en charge par l’environnement Azure Data Science Virtual Machine.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 04/29/2021
ms.openlocfilehash: 3e071ad5f86d13270144ec8eb7839f31601b5c39
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432303"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Plateformes de données prises en charge sur la machine virtuelle DSVM

DSVM (Data Science Virtual Machine) vous permet de générer votre analytique sur un large éventail de plateformes de données. En plus des interfaces sur les plateformes de données distantes, la machine virtuelle DSVM fournit une instance locale pour le développement et le prototypage rapides.

Les outils de plateforme de données pris en charge sur DSVM sont les suivants.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Category | Valeur |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance de base de données relationnelle locale      |
| Éditions DSVM prises en charge      | Windows 2019, Ubuntu 18.04 (SQL Server 2019)   |
| Utilisations classiques      | <ul><li>Développement rapide localement avec le plus petit jeu de données</li><li>Exécution de R en base de données</li></ul> |
| Liens vers des exemples      | <ul><li>Un petit échantillon du jeu de données New York City est chargé dans la base de données SQL :<br/>  `nyctaxi`</li><li>Vous trouverez un exemple Jupyter illustrant l’analytique Microsoft Machine Learning Server et en base de données à l’emplacement suivant :<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Outils connexes sur la machine virtuelle DSVM       | <ul><li>SQL Server Management Studio</li><li>Pilotes ODBC/JDBC</li><li>pyodbc, RODBC</li></ul> |

> [!NOTE]
> L’édition Développeur de SQL Server peut uniquement être utilisée à des fins de test et de développement. Vous avez besoin d’une licence ou de l’une des machines virtuelles SQL Server pour l’exécuter en production.

> [!NOTE]
> La prise en charge de Machine Learning Server autonome prendra fin le 1er juillet 2021. Nous allons le supprimer des images DSVM après le 30 juin. Les déploiements existants continueront d’avoir accès au logiciel, mais en raison de la date de fin du support, il n’y aura pas de prise en charge après le 1er juillet 2021.

> [!NOTE]
> Nous supprimerons SQL Server Developer Edition des images DSVM avant fin novembre 2021. SQL Server Developer Edition sera toujours installé sur les déploiements existants. Dans les nouveaux déploiements, si vous souhaitez avoir accès à SQL Server Developer Edition, vous pouvez l’installer et l’utiliser via la prise en charge de Docker. Consultez [Démarrage rapide : Exécuter des images de conteneur SQL Server avec Docker](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash&preserve-view=true).

### <a name="windows"></a>Windows

#### <a name="setup"></a>Programme d’installation

Le serveur de base de données est déjà préconfiguré et les services Windows associés à SQL Server (comme `SQL Server (MSSQLSERVER)`) sont définis pour s’exécuter automatiquement. La seule étape manuelle implique l’activation de l’analytique en base de données à l’aide de Microsoft Machine Learning Server. Vous pouvez activer les analyses en exécutant la commande suivante une seule fois dans SQL Server Management Studio (SSMS). Exécutez cette commande après vous être connecté en tant qu’administrateur de l’ordinateur, ouvrez une nouvelle requête dans SSMS et assurez-vous que la base de données sélectionnée est `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Remplacez %COMPUTERNAME% par le nom de votre machine virtuelle.)

Pour exécuter l’outil SQL Server Management Studio, vous pouvez rechercher « SQL Server Management Studio » dans la liste des programmes ou utiliser Windows Search pour le rechercher et l’exécuter. Quand vous êtes invité à fournir des informations d’identification, séelctionnez **Authentification Windows** et utilisez le nom d’ordinateur ou ```localhost``` dans le champ **Nom du serveur SQL**.

#### <a name="how-to-use-and-run-it"></a>Comment l’utiliser et l’exécuter ?

Par défaut, le serveur de base de données avec l’instance de base de données par défaut s’exécute automatiquement. Vous pouvez utiliser des outils tels que SQL Server Management Studio sur la machine virtuelle pour accéder à la base de données SQL Server localement. Les comptes administrateurs locaux ont un accès administrateur à la base de données.

De plus, l’environnement DSVM est fourni avec des pilotes ODBC et JDBC pour communiquer avec SQL Server, les bases de données Azure SQL et Azure Synapse Analytics à partir d’applications écrites dans plusieurs langages, notamment Python et Machine Learning Server.

#### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Comment est-il configuré et installé sur la machine virtuelle DSVM ? 

 SQL Server est installé de manière standard. Il se trouve dans `C:\Program Files\Microsoft SQL Server`. L’instance de Machine Learning Server en base de données se trouve à l’emplacement `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. L’environnement DSVM a aussi une instance de Machine Learning Server autonome distincte, installée à l’emplacement `C:\Program Files\Microsoft\R Server\R_SERVER`. Ces deux instances de Machine Learning Server ne partagent pas de bibliothèques.


### <a name="ubuntu"></a>Ubuntu

Pour utiliser SQL Server Developer Edition sur une DSVM Ubuntu, vous devez d’abord l’installer. [Démarrage rapide : Installer SQL Server et créer une base de données sur Ubuntu](/sql/linux/quickstart-install-connect-ubuntu) vous explique comment.



## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonome)

| Category | Valeur |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance (nœud unique In-process) autonome de la plateforme populaire Apache Spark, un système pour le traitement des données et l’apprentissage automatique rapides et à grande échelle     |
| Éditions DSVM prises en charge      | Linux     |
| Utilisations classiques      | <ul><li>Développement rapide d’applications Spark/PySpark localement avec un jeu de données plus petit et un déploiement ultérieur sur des clusters Spark volumineux tels qu’Azure HDInsight</li><li>Test du contexte Spark de Microsoft Machine Learning Server</li><li>Utilisation de la bibliothèque [MMLSpark](https://github.com/Azure/mmlspark) open source de SparkML ou de Microsoft pour créer des applications ML</li></ul> |
| Liens vers des exemples      |    Exemple Jupyter :<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (contexte Spark) : /dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Outils connexes sur la machine virtuelle DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (noyaux Spark/PySpark)</li><li>Microsoft Machine Learning Server, SparkR, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Comment l’utiliser ?
Vous pouvez envoyer des travaux Spark sur la ligne de commande en exécutant la commande `spark-submit` ou `pyspark`. Vous pouvez également créer un bloc-notes Jupyter en créant un bloc-notes avec le noyau Spark.

Vous pouvez utiliser Spark à partir de R à l’aide de bibliothèques comme SparkR, Sparklyr et Microsoft Machine Learning Server, qui sont disponibles sur DSVM. Consultez les pointeurs vers les exemples dans le tableau précédent.

### <a name="setup"></a>Programme d’installation
Avant toute exécution dans un contexte Spark dans Microsoft Machine Learning Server sur l’édition DSVM Ubuntu Linux, vous devez effectuer une opération de configuration unique pour activer une instance Yarn et HDFS Hadoop à nœud unique locale. Par défaut, les services Hadoop sont installés mais désactivés sur la DSVM. Pour les activer, exécutez les commandes suivantes en tant que racine la première fois :

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Vous pouvez arrêter les services liés à Hadoop lorsque vous n’en avez plus besoin en exécutant ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Vous trouverez dans le répertoire `/dsvm/samples/MRS` un exemple montrant comment développer et tester MRS dans un contexte Spark distant (l’instance Spark autonome sur DSVM).


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Comment est-il configuré et installé sur la machine virtuelle DSVM ? 
|Plateforme|Emplacement d’installation ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Les bibliothèques pour l’accès aux données à partir du Stockage Blob Azure ou d’Azure Data Lake Storage, à l’aide des bibliothèques d’apprentissage automatique MMLSpark de Microsoft, sont préinstallées dans $SPARK_HOME/jars. Ces fichiers JAR sont automatiquement chargés au démarrage de Spark. Par défaut, Spark utilise des données sur le disque local. 

Pour que l’instance Spark sur DSVM accède aux données stockées dans le Stockage Blob ou Azure Data Lake Storage, vous devez créer et configurer le fichier `core-site.xml` en fonction du modèle figurant dans $SPARK_HOME/conf/core-site.xml.template. Vous devez également disposer des informations d’identification appropriées pour accéder au Stockage Blob et à Azure Data Lake Storage. (Notez que les fichiers de modèle utilisent des espaces réservés pour les configurations du Stockage Blob et d’Azure Data Lake Storage.)

Pour plus d’informations sur la création des informations d’identification du service Azure Data Lake Storage, consultez [Authentification avec Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Une fois que les informations d’identification pour le Stockage Blob Azure ou Azure Data Lake Storage sont entrées dans le fichier core-site.xml, vous pouvez référencer les données stockées dans ces sources par le biais du préfixe d’URI wasb:// ou adl://.