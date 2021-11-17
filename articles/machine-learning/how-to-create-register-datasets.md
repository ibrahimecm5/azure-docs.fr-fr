---
title: Créer des jeux de données Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment créer des jeux de données Azure Machine Learning pour accéder à vos données pour des exécutions d’expérience de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: contperf-fy21q1, data4ml
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.openlocfilehash: 2916b7c8042a96e35ad138ae5ae20c389c62e751
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866636"
---
# <a name="create-azure-machine-learning-datasets"></a>Créer des jeux de données Azure Machine Learning

Dans cet article, vous découvrez comment créer des jeux de données Azure Machine Learning pour accéder aux données de vos expériences locales ou à distance avec le kit de développement logiciel (SDK) Python Azure Machine Learning. Pour comprendre où figurent les jeux de données dans le flux de travail global d’accès aux données d’Azure Machine Learning, consultez l’article [Sécuriser l’accès aux données](concept-data.md#data-workflow).

En créant un jeu de données, vous créez une référence à l’emplacement de la source de données, ainsi qu’une copie de ses métadonnées. Étant donné que les données restent à leur emplacement existant, vous n’exposez aucun coût de stockage supplémentaire et ne risquez pas l’intégrité de vos sources de données. Les jeux de données sont également évalués tardivement, ce qui contribue aux vitesses de performances de flux de travail. Vous pouvez créer des jeux de données à partir de magasins de données, d’URL publiques et de [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Pour une expérience avec peu de code, [Créez des jeux de données Azure Machine Learning avec Azure Machine Learning studio.](how-to-connect-data-ui.md#create-datasets)

Avec les jeux de données Azure Machine Learning, vous pouvez :

* Conserver une seule copie des données dans votre stockage, référencée par des jeux de données.

* Accéder simplement aux données lors de l’entraînement du modèle sans vous soucier des chaînes de connexion ou des chemins des données. [Découvrez-en plus sur l’entraînement avec des jeux de données](how-to-train-with-datasets.md).

* Partager des données et collaborer avec d’autres utilisateurs.

## <a name="prerequisites"></a>Prérequis

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](/python/api/overview/azure/ml/install), qui inclut le paquet azureml-datasets.

    * Créez une [instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md), qui est un environnement de développement entièrement configuré et géré qui comprend des blocs-notes intégrés et le kit de développement logiciel (SDK) déjà installé.

    **OR**

    * Travaillez sur votre propre notebook Jupyter et [installez vous-même le Kit de développement logiciel (SDK)](/python/api/overview/azure/ml/install).

> [!NOTE]
> Certaines classes de jeu de données ont des dépendances avec le package [azureml-dataprep](https://pypi.org/project/azureml-dataprep/), qui n’est compatible qu’avec Python 64 bits. Si vous développez sous __Linux__, ces classes reposent sur .NET Core 2.1 et ne sont prises en charge que par certaines distributions. Pour plus d’informations sur les distributions prises en charge, consultez la colonne .NET Core 2.1 dans l’article [Installer .NET sur Linux](/dotnet/core/install/linux).

> [!IMPORTANT]
> Si le package peut fonctionner sur les anciennes versions des distributions Linux, nous vous déconseillons d’utiliser un distribution dont le support standard n’est pas assuré. Les distributions qui ne bénéficient pas du support standard peuvent présenter des failles de sécurité, car ils ne reçoivent pas les dernières mises à jour. Nous vous recommandons d’utiliser la version prise en charge la plus récente avec laquelle votre distribution est compatible.

## <a name="compute-size-guidance"></a>Conseils liés à la taille de calcul

Quand vous créez un jeu de données, passez en revue la puissance de traitement de calcul et la taille de vos données en mémoire. La taille de vos données dans le stockage n’est pas la même que la taille des données dans un dataframe. Par exemple, les données des fichiers CSV peuvent décupler de volume dans un dataframe ; ainsi, un fichier CSV de 1 Go peut occuper 10 Go dans un dataframe. 

Si vos données sont compressées, elles peuvent s’étendre davantage ; 20 Go de données relativement éparses stockées au format de compression Parquet peuvent s’étendre jusqu’à environ 800 Go en mémoire. Étant donné que les fichiers Parquet stockent les données dans un format en colonnes, si vous avez uniquement besoin de la moitié des colonnes, il vous suffit de charger environ 400 Go en mémoire.

[Apprenez-en davantage sur l’optimisation du traitement des données dans Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Types de jeux de données

Il existe deux types de jeux de données, selon la façon dont les utilisateurs les consomment lors de l’entraînement : FileDatasets et TabularDatasets. Les deux types peuvent être utilisés dans des flux de travail d’apprentissage Azure Machine Learning impliquant des estimateurs, AutoML, hyperDrive et des pipelines. 

### <a name="filedataset"></a>FileDataset

Un [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) fait référence à des fichiers uniques ou multiples dans vos magasins de données ou vos URL publiques. Si vos données sont déjà nettoyées et prêtes à l’emploi dans des expériences d’apprentissage, vous pouvez [télécharger ou monter](how-to-train-with-datasets.md#mount-vs-download) les fichiers dans votre calcul en tant qu’objet FileDataset. 

Nous vous recommandons d’utiliser FileDatasets pour vos flux de travail d’apprentissage automatique, car les fichiers sources peuvent être dans n’importe quel format, ce qui permet un éventail plus large de scénarios d’apprentissage automatique, dont l’apprentissage profond.

Créez un FileDataset avec le [kit de développement logiciel (SDK) Python](#create-a-filedataset) ou [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).
### <a name="tabulardataset"></a>TabularDataset

Un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) représente les données sous forme de tableau en analysant le fichier ou la liste de fichiers fournis. Cela vous permet de matérialiser les données dans une tramedonnées pandas ou Spark afin de pouvoir travailler avec des bibliothèques de formation et de préparation des données familières sans avoir à quitter votre notebook. Vous pouvez créer un objet `TabularDataset` à partir de fichiers .csv, .tsv, [.parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) et [.jsonl](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--) et à partir de [résultats de requête SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Avec TabularDatasets, vous pouvez spécifier un horodatage à partir d’une colonne dans les données ou à partir de là où les données du modèle de chemin sont stockées pour activer une caractéristique de série chronologique. Cette spécification permet un filtrage chronologique facile et efficace. Pour un exemple, consultez [Démonstration de l’API pour une série chronologique tabulaire avec des données météo NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Créez un TabularDataset avec le [Kit de développement logiciel (SDK) Python](#create-a-tabulardataset) ou [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> Les workflows [Machine Learning automatisé](concept-automated-ml.md) générés par le biais d’Azure Machine Learning studio prennent uniquement en charge TabularDatasets pour le moment. 

## <a name="access-datasets-in-a-virtual-network"></a>Accéder à des jeux de données dans un réseau virtuel

Si votre espace de travail se trouve dans un réseau virtuel, vous devez configurer le jeu de données pour ignorer la validation. Pour plus d’informations sur l’utilisation de magasins de données et de jeux de données dans un réseau virtuel, consultez [Sécuriser un espace de travail et les ressources associées](how-to-secure-workspace-vnet.md#datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Créer des jeux de données à partir de magasins de données

Pour que les données soient accessibles par Azure Machine Learning, les jeux de données doivent être créés à partir de chemins dans les [magasins de données Azure Machine Learning](how-to-access-data.md) ou d’URL web. 

> [!TIP] 
> Vous pouvez créer des jeux de données directement à partir d’URL de stockage avec l’accès aux données basé sur l’identité. Pour plus d’informations, consultez [Se connecter au stockage avec l’accès aux données basé sur l’identité](how-to-identity-based-data-access.md).

 
Pour créer des jeux de données à partir d’un magasin de données avec le kit SDK Python :

1. Vérifiez que vous disposez de l’accès `contributor` ou `owner` au service de stockage sous-jacent de votre magasin de données Azure Machine Learning enregistré. [Vérifiez les autorisations de votre compte de stockage dans le portail Azure](../role-based-access-control/check-access.md).

1. Créez le jeu de données en référençant des chemins d’accès dans le magasin de données. Vous pouvez créer un jeu de données à partir de plusieurs chemins d’accès dans plusieurs magasins de données. Il n’existe aucune limite inconditionnelle quant au nombre de fichiers ou à la taille de données à partir desquels vous pouvez créer un jeu de données. 

> [!NOTE]
> Pour chaque chemin d’accès aux données, quelques requêtes sont envoyées au service de stockage pour vérifier s’il pointe vers un fichier ou un dossier. Cette surcharge peut entraîner une dégradation des performances ou une défaillance. Un jeu de données référençant un dossier contenant 1 000 fichiers est considéré comme référençant un chemin d’accès de données. Nous vous recommandons de créer un jeu de données référençant moins de 100 chemins d’accès dans des magasins de données pour obtenir des performances optimales.

### <a name="create-a-filedataset"></a>Créer un FileDataset

Utilisez la méthode [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) sur la classe `FileDatasetFactory` pour charger des fichiers de n’importe quel format et créer un FileDataset non inscrit. 

Si votre stockage se trouve derrière un réseau virtuel ou un pare-feu, définissez le paramètre `validate=False` dans votre méthode `from_files()`. Cela permet de contourner l’étape de validation initiale et vous permet de créer votre jeu de données à partir de ces fichiers sécurisés. Apprenez-en davantage sur l’utilisation de [magasins de données et de jeux de données dans un réseau virtuel](how-to-secure-workspace-vnet.md#datastores-and-datasets).

```Python
from azureml.core import Workspace, Datastore, Dataset

# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

Si vous souhaitez charger l’ensemble des fichiers à partir d’un répertoire local, créez un FileDataset dans une méthode unique avec [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Elle permet de charger des données dans votre stockage sous-jacent, ce qui entraîne la facturation de coûts de stockage. 

```Python
from azureml.core import Workspace, Datastore, Dataset
from azureml.data.datapath import DataPath

ws = Workspace.from_config()
datastore = Datastore.get(ws, '<name of your datastore>')
ds = Dataset.File.upload_directory(src_dir='<path to you data>',
           target=DataPath(datastore,  '<path on the datastore>'),
           show_progress=True)

```

Pour réutiliser et partager des jeux de données dans des expériences au sein de votre espace de travail, [inscrivez votre jeu de données](#register-datasets). 

### <a name="create-a-tabulardataset"></a>Créer un TabularDataset

Utilisez la méthode [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) sur la classe `TabularDatasetFactory` pour lire des fichiers au format .csv ou .tsv, puis créez un TabularDataset non inscrit. Pour lire les fichiers au format .parquet, utilisez la méthode [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-). Si vous lisez à partir de plusieurs fichiers, les résultats sont agrégés dans une même représentation tabulaire. 

Consultez la [documentation de référence de TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) pour plus d’informations sur les formats de fichiers pris en charge, ainsi que sur la syntaxe et les modèles de conception comme la [prise en charge de plusieurs lignes](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--). 

Si votre stockage se trouve derrière un réseau virtuel ou un pare-feu, définissez le paramètre `validate=False` dans votre méthode `from_delimited_files()`. Cela permet de contourner l’étape de validation initiale et vous permet de créer votre jeu de données à partir de ces fichiers sécurisés. Apprenez-en davantage sur l’utilisation de [magasins de données et de jeux de données dans un réseau virtuel](how-to-secure-workspace-vnet.md#datastores-and-datasets).

Le code suivant obtient l’espace de travail existant et le magasin de données souhaité par nom. Ensuite, il transmet les emplacements du magasin de fichiers et des fichiers au paramètre `path` pour créer un nouvel objet TabularDataset, `weather_ds`.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Définir le schéma de données

Par défaut, quand vous créez un TabularDataset, les types de données des colonnes sont inférés automatiquement. Si les types inférés ne correspondent pas à vos attentes, vous pouvez mettre à jour votre schéma de jeu de données en spécifiant les types de colonnes à l’aide du code suivant. Le paramètre `infer_column_type` s’applique uniquement aux jeux de données créés à partir de fichiers délimités. [Apprenez-en davantage sur les types de données pris en charge](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Nom|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

Pour réutiliser et partager des jeux de données dans des expériences de votre espace de travail, [inscrivez votre jeu de données](#register-datasets).

## <a name="wrangle-data"></a>Data wrangling
Une fois que vous avez créé et [inscrit](#register-datasets) votre jeu de données, vous pouvez le charger dans votre notebook pour le data wrangling et l’[exploration](#explore-data) des données avant d’effectuer l’apprentissage du modèle. 

Si vous n’avez pas besoin d’effectuer une exploration des données ou un data wrangling, découvrez comment utiliser des jeux de données dans vos scripts d’apprentissage pour soumettre des expériences ML dans [Effectuer l’apprentissage avec des jeux de données](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtrer les jeux de données (préversion)

Les capacités de filtrage dépendent du type de jeu de données dont vous disposez. 
> [!IMPORTANT]
> Le filtrage de jeux de données à l’aide de la méthode de la préversion, [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-), est une fonctionnalité d’évaluation [expérimentale](/python/api/overview/azure/ml/#stable-vs-experimental) et peut changer à tout moment. 
> 
**Pour TabularDatasets**, vous pouvez conserver ou supprimer des colonnes avec les méthodes [keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) et [drop_columns()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-).

Pour filtrer les lignes en utilisant une valeur de colonne spécifique dans un TabularDataset, utilisez la méthode [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (préversion). 

Les exemples suivants retournent un jeu de données non inscrit en fonction des expressions spécifiées.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**Dans FileDatasets**, chaque ligne correspond à un chemin d’accès à un fichier, de sorte que le filtrage par valeur de colonne n’est pas utile. Toutefois, vous pouvez utiliser [filter()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) pour filtrer les lignes par métadonnées, par exemple CreationTime, Size, etc.

Les exemples suivants retournent un jeu de données non inscrit en fonction des expressions spécifiées.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

Les **jeux de données étiquetés** créés à partir de [projets d’étiquetage d’images](how-to-create-image-labeling-projects.md) sont un cas particulier. Ces jeux de données sont un type de TabularDataset constitué de fichiers image. Pour ces types de jeux de données, vous pouvez utiliser [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) pour filtrer les images par métadonnées et par valeurs de colonne, par exemple `label` et `image_details`.

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data"></a>Données de partition

Vous pouvez partitionner un jeu de données en incluant le paramètre `partitions_format` lors de la création d’un TabularDataset ou d’un FileDataset. 

Lorsque vous partitionnez un jeu de données, les informations de partition de chaque chemin d’accès de fichier sont extraites dans des colonnes en fonction du format spécifié. Le format doit commencer à partir de la position de la première clé de partition et se poursuivre jusqu’à la fin du chemin d’accès au fichier. 

Par exemple, étant donné le chemin d’accès `../Accounts/2019/01/01/data.jsonl` où la partition se fait par nom de service et par heure, `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` crée une colonne de chaîne « Department » avec la valeur « Accounts » et une colonne DateTime « PartitionDate » avec la valeur `2019-01-01`.

Si vos données comportent déjà des partitions existantes et que vous souhaitez conserver ce format, incluez le paramètre `partitioned_format` dans votre méthode [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) pour créer un FileDataset. 

Pour créer un TabularDataset qui conserve les partitions existantes, incluez le paramètre `partitioned_format` dans la méthode [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) ou [from_delimited_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--).

L’exemple suivant :
* Crée un FileDataset à partir de fichiers partitionnés.
* Obtient les clés de partition
* Crée un nouveau FileDataset indexé avec
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Vous pouvez également créer une nouvelle structure de partitions pour TabularDatasets avec la méthode [partitions_by()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-).

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

## <a name="explore-data"></a>Explorer des données

Une fois que vous avez terminé votre data wrangling, vous pouvez [inscrire](#register-datasets) votre jeu de données, puis le charger dans votre notebook pour l’exploration des données avant la formation du modèle.

Pour les FileDataset, vous pouvez **monter** ou **télécharger** votre jeu de données, et appliquer les bibliothèques Python que vous utiliseriez normalement pour l’exploration des données. [En savoir plus sur le montage et le téléchargement](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Pour les TabularDataset, utilisez la méthode [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) pour afficher vos données dans un dataframe. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Nom|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Créer un jeu de données à partir d’une tramedonnées pandas

Pour créer un TabularDataset à partir d’un dataframe Pandas en mémoire, utilisez la méthode [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#register-pandas-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-). Cette méthode inscrit le TabularDataset dans l’espace de travail et charge les données dans votre stockage sous-jacent, ce qui entraîne des coûts de stockage. 

```python
from azureml.core import Workspace, Datastore, Dataset
import pandas as pd

pandas_df = pd.read_csv('<path to your csv file>')
ws = Workspace.from_config()
datastore = Datastore.get(ws, '<name of your datastore>')
dataset = Dataset.Tabular.register_pandas_dataframe(pandas_df, datastore, "dataset_from_pandas_df", show_progress=True)

```
> [!TIP]
> Créez et inscrivez un TabularDataset à partir d’un dataframe Spark en mémoire ou d’un dataframe Dask avec les méthodes de préversion publique [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory##register-spark-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-) et [`register_dask_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#register-dask-dataframe-dataframe--target--name--description-none--tags-none--show-progress-true-). Ces méthodes sont des fonctionnalités d’évaluation [expérimentales](/python/api/overview/azure/ml/#stable-vs-experimental) susceptibles d’évoluer à tout moment. 
> 
>  Elles chargent les données dans votre stockage sous-jacent et entraînent la facturation de coûts de stockage. 

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, inscrivez vos jeux de données dans un espace de travail. Utilisez la méthode [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) pour inscrire des jeux de données auprès de votre espace de travail afin de pouvoir les partager avec d’autres personnes et les réutiliser dans des expériences dans votre espace de travail :

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Créer des jeux de données à l'aide d'Azure Resource Manager

De nombreux modèles sont disponibles à l’adresse [https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices) et peuvent être utilisés pour créer des jeux de données.

Pour plus d'informations sur l'utilisation de ces modèles, consultez [Utiliser un modèle Azure Resource Manager afin de créer un espace de travail pour Azure Machine Learning](how-to-create-workspace-template.md).
 

## <a name="train-with-datasets"></a>Entraîner avec des jeux de données

Utilisez vos jeux de données dans vos expériences d’apprentissage automatique pour la formation de modèles ML. [Découvrez-en plus sur l’entraînement avec des jeux de données](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Jeux de données avec version

Vous pouvez inscrire un nouveau jeu de données sous le même nom en créant une nouvelle version. La version d’un jeu de données est un moyen de marquer l’état de vos données, afin de pouvoir appliquer une version spécifique du jeu de données pour une expérimentation ou une reproduction ultérieure. En savoir plus sur les [versions des jeux de données](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrir [comment former un modèle avec des jeux de données](how-to-train-with-datasets.md).
* Utilisez le Machine Learning automatisé pour [vous entraîner avec les TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Pour obtenir plus d’exemples d’entraînement de jeux de données, consultez les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
