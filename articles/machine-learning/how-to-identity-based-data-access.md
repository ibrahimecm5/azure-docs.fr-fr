---
title: Accès aux données basé sur l’identité pour les services de stockage sur Azure
titleSuffix: Machine Learning
description: Découvrez comment utiliser l’accès basé sur l’identité pour vous connecter aux services de stockage sur Azure à l’aide des magasins de données Azure Machine Learning et du kit SDK Python de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: b251c85752af69ce58e48f93be29cb07f1bc9966
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492736"
---
# <a name="connect-to-storage-by-using-identity-based-data-access"></a>Se connecter au stockage avec l’accès aux données basé sur l’identité

Dans cet article, vous apprenez à vous connecter aux services de stockage sur Azure en utilisant l’accès aux données basé sur l’identité et aux magasins de données Azure Machine Learning via le [kit SDK d’Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro).  

En règle générale, les magasins de données utilisent un accès aux données basé sur des informations d’identification pour confirmer l’autorisation d’accéder au service de stockage. Ils conservent les informations de connexion, comme votre ID d’abonnement et votre autorisation de jeton, dans le [coffre de clés](https://azure.microsoft.com/services/key-vault/) qui est associé à l’espace de travail. Lorsque vous créez un magasin de données qui utilise l’accès aux données basé sur l’identité, votre compte Azure (le [jeton Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) est utilisé pour confirmer votre autorisation d’accès au service de stockage. Dans ce scénario, aucune information d’authentification n’est enregistrée. Seules les informations du compte de stockage sont stockées dans le magasin de données. 

Pour créer des magasins de données qui utilisent l’authentification basée sur des informations d’identification, par exemple avec des clés d’accès ou des principaux de service, consultez [Se connecter aux services de stockage sur Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Accès aux données basé sur l’identité dans Azure Machine Learning

Il existe deux scénarios dans lesquels vous pouvez appliquer l’accès aux données basé sur l’identité dans Azure Machine Learning. Ces scénarios sont adaptés à l’accès basé sur l’identité lorsque vous utilisez des données confidentielles et que vous avez besoin d’une gestion plus granulaire de l’accès aux données :

- Accès aux services de stockage
- Entraînement des modèles Machine Learning avec des données privées

### <a name="accessing-storage-services"></a>Accès aux services de stockage

Vous pouvez connecter des services de stockage par le biais de l’accès aux données basé sur l’identité à des magasins de données Azure Machine Learning ou à des [jeux de données Azure Machine Learning](how-to-create-register-datasets.md). 

Vos informations d’authentification sont généralement conservées dans un magasin de données, qui est utilisé pour garantir votre autorisation d’accéder au service de stockage. Lorsque ces informations d’identification sont inscrites via des magasins de données, tout utilisateur disposant du rôle Lecteur d’espace de travail peut les récupérer. Cette échelle d’accès peut être un problème de sécurité pour certaines organisations. [Apprenez-en davantage sur le rôle de Lecteur d’espace de travail.](how-to-assign-roles.md#default-roles) 

Lorsque vous utilisez l’accès aux données basé sur l’identité, Azure Machine Learning vous invite à indiquer votre jeton Azure Active Directory en guise d’authentification pour l’accès aux données, plutôt que de conserver vos informations d’identification dans le magasin de données. Avec cette approche, la gestion de l’accès aux données se fait au niveau du stockage et les informations d’identification demeurent confidentielles. 

Le même comportement s’applique lorsque vous :

* [créez un jeu de données directement à partir d’URL de stockage](#use-data-in-storage). 
* utilisez des données de manière interactive par le biais d’un notebook Jupyter sur votre ordinateur local ou votre [instance de calcul](concept-compute-instance.md).

> [!NOTE]
> Les informations d’identification stockées à l’aide de l’authentification basée sur des informations d’identification comprennent les éléments suivants : un ID d’abonnement, des jetons de signature d’accès partagé (SAS), des clés d’accès de stockage et des informations de principal de service, comme un ID client et un ID de locataire.

### <a name="model-training-on-private-data"></a>Entraînement de modèles sur des données privées

Certains scénarios Machine Learning supposent des modèles d’entraînement avec des données privées. Dans ces cas précis, les chercheurs de données doivent exécuter des workflows d’entraînement sans exposition des données d’entrée confidentielles. Dans ce scénario, une identité managée du calcul d’entraînement est utilisée pour l’authentification de l’accès aux données. De cette façon, les administrateurs du stockage peuvent accorder l’accès Lecteur de données blob du stockage à l’identité managée que le calcul d’entraînement utilise pour exécuter le travail d’entraînement. Il n’est pas nécessaire d’accorder l’accès à des scientifiques de données individuels. Pour plus d’informations, consultez [Configurer une identité managée pour un cluster de calcul](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

- Un compte de stockage Azure avec un type de stockage pris en charge. Les types de stockage suivants sont pris en charge dans la préversion : 
    - [Stockage Blob Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](/python/api/overview/azure/ml/install).

- Un espace de travail Azure Machine Learning.
  
  [Créez un espace de travail Azure Machine Learning](how-to-manage-workspace.md) ou utilisez [un espace de travail existant via le kit SDK Python](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Autorisations d’accès au stockage

Pour vous garantir une connexion sécurisée à votre service de stockage sur Azure, Azure Machine Learning exige que vous disposiez de l’autorisation d’accéder au stockage de données correspondant.

L’accès aux données basé sur l’identité prend uniquement en charge les connexions aux services de stockage suivants :

* Stockage Blob Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

Pour accéder à ces services de stockage, vous devez disposer au minimum de l’accès [Lecteur des données blob de stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Seuls les propriétaires de comptes de stockage peuvent [modifier votre niveau d’accès via le portail Azure](../storage/blobs/assign-azure-role-data-access.md).

Si vous entraînez un modèle sur une cible de calcul distante, l’identité de calcul doit être accordée avec au moins le rôle de lecteur des données blob de stockage du service de stockage. Découvrez comment [configurer une identité managée sur une instance de cluster de calcul](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Utiliser les réseaux virtuels

Par défaut, Azure Machine Learning ne peut pas communiquer avec un compte de stockage situé derrière un pare-feu, ou dans un réseau virtuel.

Vous pouvez configurer des comptes de stockage pour autoriser l’accès uniquement dans des réseaux virtuels spécifiques. Cette configuration nécessite des étapes supplémentaires pour garantir que les données ne sont pas divulguées en dehors du réseau. Ce comportement est le même pour l’accès aux données basé sur les informations d’identification. Pour plus d’informations, consultez les [Scénarios de configuration d’un réseau virtuel](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Créer et inscrire des magasins de données

Quand vous inscrivez un service de stockage Azure en tant que magasin de données, vous créez et inscrivez automatiquement ce magasin dans un espace de travail spécifique. Pour obtenir des conseils sur les types d’autorisation requis, consultez [Autorisations d’accès au stockage](#storage-access-permissions). Pour plus d’informations sur la façon de se connecter à un stockage de données derrière des réseaux virtuels, consultez [Utiliser les réseaux virtuels](#work-with-virtual-networks).

Dans le code suivant, notez l’absence de paramètres d’authentification, comme `sas_token`, `account_key` `subscription_id` ou le principal de service `client_id`. Cette omission indique qu’Azure Machine Learning utilise l’accès aux données basé sur l’identité pour l’authentification. La création de magasins de données se produit généralement de manière interactive dans un notebook ou via le studio. Votre jeton Azure Active Directory est donc utilisé pour l’authentification de l’accès aux données.

> [!NOTE]
> Les noms des magasins de données doivent contenir uniquement des lettres minuscules, des chiffres et des traits de soulignement. 

### <a name="azure-blob-container"></a>Conteneur d’objets blob Azure

Pour inscrire un conteneur d’objets blob Azure comme magasin de données, utilisez [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Le code suivant crée le magasin de données `credentialless_blob`, l’inscrit dans l’espace de travail `ws` et l’affecte à la variable `blob_datastore`. Ce magasin de données accède au conteneur d’objets blob `my_container_name` sur le compte de stockage `my-account-name`.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Utilisez [register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) pour inscrire un magasin de données qui se connecte à Azure Data Lake Storage Gen1.

Le code suivant crée le magasin de données `credentialless_adls1`, l’inscrit dans l’espace de travail `workspace` et l’affecte à la variable `adls_dstore`. Ce magasin de données accède au compte Azure Data Lake Storage `adls_storage`.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Utilisez [register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) pour inscrire un magasin de données qui se connecte à Azure Data Lake Storage Gen2.

Le code suivant crée le magasin de données `credentialless_adls2`, l’inscrit dans l’espace de travail `ws` et l’affecte à la variable `adls2_dstore`. Ce magasin de données accède au système de fichiers `tabular` dans le compte de stockage `myadls2`.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```
### <a name="azure-sql-database"></a>Base de données Azure SQL
Pour une base de données Azure SQL, utilisez [register_azure_sql_database()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none--subscription-id-none--resource-group-none--grant-workspace-access-false----kwargs-) afin d’inscrire un magasin de données qui se connecte à un stockage de base de données Azure SQL.

Le code suivant crée et inscrit le magasin de données `credentialless_sqldb` dans l’espace de travail `ws` et l’affecte à la variable `sqldb_dstore`. Ce magasin de données accède à la base de données `mydb` sur le serveur de base de données SQL `myserver`.  

```python
# createn sqldatabase datastore without credentials
                                                       
sqldb_dstore = Datastore.register_azure_sql_database(workspace=ws,
                                                       datastore_name='credentialless_sqldb',
                                                       server_name='myserver',
                                                       database_name='mydb')                                                       
                                                   
```

## <a name="use-data-in-storage"></a>Utiliser les données dans le stockage

Les [jeux de données Azure Machine Learning](how-to-create-register-datasets.md) sont le moyen conseillé pour interagir avec vos données dans le stockage à l’aide d’Azure Machine Learning. 

Les jeux de données intègrent vos données dans un objet consommable évalué tardivement pour les tâches de Machine Learning, comme la formation. Avec les jeux de données, vous pouvez également [télécharger ou monter](how-to-train-with-datasets.md#mount-vs-download) sur une cible de calcul des fichiers de n’importe quel format, depuis des services de stockage Azure, tels que le stockage d’objets blob Azure et Azure Data Lake Storage.


Pour créer des jeux de données avec un accès aux données basé sur l’identité, vous disposez des options suivantes. Ce type de création de jeu de données utilise votre jeton Azure Active Directory pour l’authentification de l’accès aux données. 

*  Les chemins de référence à partir des magasins de données qui utilisent également l’accès aux données basé sur l’identité. 
<br>Dans l’exemple suivant, `blob_datastore` existe déjà et utilise l’accès aux données basé sur l’identité.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignorez la création du magasin de données et créez des jeux de données directement à partir des URL de stockage. Cette fonctionnalité prend actuellement en charge uniquement les objets blob Azure et Azure Data Lake Storage Gen1 et Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Lorsque vous soumettez un travail d’entraînement qui consomme un jeu de données créé avec un accès aux données basé sur l’identité, l’identité managée du calcul d’entraînement est utilisée pour l’authentification de l’accès aux données. Votre jeton Azure Active Directory n’est pas utilisé. Pour ce scénario, assurez-vous que l’identité managée du calcul est accordée avec au moins le rôle de Lecteur des données blob de stockage à partir du service de stockage. Pour plus d’informations, consultez [Configurer une identité managée pour des clusters de calcul](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Étapes suivantes

* [Créer un jeu de données Azure Machine Learning](how-to-create-register-datasets.md)
* [Entraîner avec des jeux de données](how-to-train-with-datasets.md)
* [Créer un magasin de données avec un accès aux données basé sur des clés](how-to-access-data.md)
