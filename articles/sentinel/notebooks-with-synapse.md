---
title: Analyse de la sécurité à grande échelle à l’aide de notebooks Azure Sentinel et intégration d’Azure Synapse
description: Cet article explique comment exécuter des requêtes de Big Data dans Azure Synapse Analytics avec des notebooks Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 10/06/2021
ms.openlocfilehash: 6e5efb00db63f0188248c758443e944f0653e513
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096307"
---
# <a name="large-scale-security-analytics-using-azure-sentinel-notebooks-and-azure-synapse-integration-public-preview"></a>Analyse de la sécurité à grande échelle à l’aide de notebooks Azure Sentinel et intégration d’Azure Synapse (version préliminaire publique)

> [!IMPORTANT]
> L’intégration des notebooks Azure Sentinel à Azure Synapse Analytics est actuellement disponible en version préliminaire. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

L’intégration des notebooks Azure Sentinel avec Azure Synapse Analytics permet une analyse de sécurité à grande échelle.

Bien que KQL et Log Analytics soient les principaux outils et solutions pour l’interrogation et l’analyse des données dans Azure Sentinel, Azure Synapse fournit des fonctionnalités supplémentaires pour l’analyse du Big Data, avec l’accès aux lacs de données intégré et le moteur de traitement distribué Apache Spark.

L’intégration à Azure Synapse offre les éléments suivants :

- **Analyses de sécurité du Big Data**, à l’aide d’un pool de calcul Azure Synapse Apache Spark entièrement géré.

- **Accès au lac de données rentable** pour générer des analyses sur les données historiques via Azure Data Lake Storage Gen2, qui est un ensemble de fonctionnalités dédiées aux analyses de Big Data, basées sur le Stockage Blob Azure.

- **Flexibilité pour intégrer des sources de données** dans des flux de travail d’opération de sécurité à partir de plusieurs sources et formats.

- **PySpark, une API basée sur Python** pour l’utilisation de l’infrastructure Spark en association avec Python, ce qui réduit la nécessité d’apprendre un nouveau langage de programmation si vous connaissez déjà Python.

Par exemple, vous souhaiterez peut-être utiliser des notebooks avec Azure Synapse pour rechercher les comportements anormaux des journaux du pare-feu de réseau afin de détecter les balises potentielles du réseau, ou pour former et créer des modèles de Machine Learning par-dessus les données collectées à partir d’un espace de travail Log Analytics.

## <a name="prerequisites"></a>Prérequis

### <a name="understand-azure-sentinel-notebooks"></a>Comprendre les notebooks Azure Sentinel

Nous vous recommandons d’en savoir plus sur les notebooks Azure Sentinel en général avant d’effectuer les procédures décrites dans cet article.

Pour bien démarrer, consultez [Utiliser des notebooks Jupyter pour rechercher des menaces de sécurité](notebooks.md) et le [Tutoriel : prise en main des notebooks Jupyter et MSTICPy dans Azure Sentinel](notebook-get-started.md).

### <a name="required-roles-and-permissions"></a>Rôles et autorisations obligatoires

Pour utiliser Azure synapse avec des notebooks Azure Sentinel, vous devez disposer des rôles et autorisations suivants :

|Type  |Détails  |
|---------|---------|
|**Azure Sentinel**     |- Rôle **contributeur Azure Sentinel**, afin d’enregistrer et de lancer des notebooks à partir d’Azure Sentinel         |
|**Azure Machine Learning**     |- Rôle de **propriétaire** ou de **contributeur** au niveau du groupe de ressources, pour créer un espace de travail Azure Machine Learning si nécessaire. <br>- Rôle de **contributeur** sur l’espace de travail Azure Machine Learning dans lequel vous exécutez vos notebooks Azure Sentinel.    <br><br>Pour plus d’informations, consultez [Gérer l’accès à un espace de travail Azure Machine Learning](/azure/machine-learning/how-to-assign-roles).     |
|**Azure Synapse Analytics**     | - Rôle de **propriétaire** au niveau du groupe de ressources, pour créer un nouvel espace de travail Azure Synapse.<br>- Rôle de **contributeur** sur l’espace de travail Azure Synapse pour exécuter vos requêtes. <br>- Rôle de **contributeur** Azure Synapse Analytics sur Synapse Studio   <br><br>Pour plus d’informations, consultez [Comprendre les rôles requis pour effectuer des tâches courantes dans Synapse](/azure/synapse-analytics/security/synapse-workspace-understand-what-role-you-need).     |
|**Azure Data Lake Storage Gen2**     | - Rôle de **contributeur** Azure log Analytics pour exporter des données à partir d’un espace de travail log Analytics<br>- Rôle de contributeur Stockage Blob Azure, pour interroger des données à partir d’un lac de données  <br><br>Pour plus d’informations, consultez [Attribuer un rôle Azure](/azure/storage/blobs/assign-azure-role-data-access?tabs=portal).|
|     |         |

### <a name="connect-to-azure-ml-and-synapse-workspaces"></a>Se connecter à des espaces de travail Azure ML et Synapse

Pour utiliser des notebooks Azure Sentinel avec Azure Synapse, vous devez d’abord vous connecter à un espace de travail Azure Machine Learning et à un espace de travail Azure Synapse.

**Pour créer un espace de travail Azure Machine Learning ou s’y connecter** :

Les espaces de travail Azure Machine Learning sont des conditions de base pour l’utilisation des notebooks dans Azure Sentinel.

Si vous n’êtes pas déjà connecté, consultez [Utiliser des notebooks Jupyter pour rechercher des menaces de sécurité](notebooks.md).

**Pour créer un espace de travail Azure Synapse** :

En haut de la page **Notebooks** Azure Sentinel, sélectionnez **Configurer Azure Synapse**, puis sélectionnez **Créer un nouvel espace de travail Azure Synapse**.

> [!NOTE]
> Azure Data Lake Storage Gen2 est un lac de données intégré qui est fourni avec chaque espace de travail Azure Synapse. Veillez à sélectionner ou créer un lac de données qui se trouve dans la même région que votre espace de travail Azure Sentinel. Cela est nécessaire lorsque vous exportez vos données, comme décrit plus loin dans cet article.
>

Pour plus d’informations, consultez la [documentation Azure Synapse](/azure/synapse-analytics/quickstart-create-workspace).


## <a name="configure-your-azure-synapse-analytics-integration"></a>Configurer votre intégration Azure Synapse Analytics

Azure Sentinel fournit le notebook **Azure Synapse - Configurer Azure ML et Azure Synapse Analytics** pour vous guider dans les configurations requises pour l’intégration à Azure Synapse.

> [!NOTE]
> La configuration de votre intégration Azure Synapse est une procédure unique, et vous ne devez exécuter ce notebook qu’une seule fois pour votre espace de travail Azure Sentinel.
>

**Pour exécuter le notebook Azure Synapse – Configurer Azure ML et Azure Synapse Analytics** :

1. Dans la page **Notebooks** dans Azure Sentinel, sélectionnez l’onglet **Modèles**, puis saisissez **Synapse** dans la barre de recherche pour trouver le notebook.

1. Recherchez et sélectionnez le notebook **Azure Synapse – Configurer Azure ML et Azure Synapse Analytics**, puis sélectionnez **Cloner le modèle de notebook** en bas à droite.

    Dans le volet **Cloner le notebook**, modifiez le nom du notebook si nécessaire, sélectionnez votre [Espace de travail Azure Machine Learning](#connect-to-azure-ml-and-synapse-workspaces), puis sélectionnez **Enregistrer**.

1. Une fois votre notebook déployé, sélectionnez **Lancer le notebook** pour l’ouvrir.

    Le notebook s’ouvre dans votre espace de travail Azure ML, dans Azure Sentinel. Pour plus d’informations, consultez [Lancer un notebook à l’aide de votre espace de travail Azure ML](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Exécutez les cellules dans les étapes initiales du notebook pour charger les bibliothèques et fonctions Python nécessaires et pour vous authentifier auprès des ressources Azure.

1. Exécutez les cellules à l’étape 4, **Configurez le pool Azure Synapse Spark** pour créer un [pool Apache Spark Azure Synapse](/azure/synapse-analytics/spark/apache-spark-pool-configurations) à utiliser lors de l’exécution de vos requêtes Big Data.

1. Exécutez les cellules à l’étape 5, **Configurez les espace de travail Azure ML et les services liés** pour vous assurer que votre espace de travail Azure ML peut communiquer avec votre espace de travail Azure Synapse. Pour plus d’informations, consultez [Lier des espaces de travail Azure Synapse Analytics et Azure Machine Learning et attacher des pools Apache Spark](/azure/machine-learning/how-to-link-synapse-ml-workspaces).

1. Exécutez les cellules à l’étape 6, **Exporter les données à partir d’Azure Log Analytics vers Azure Data Lake Storage Gen2**, afin d’exporter les données que vous souhaitez utiliser pour vos requêtes à partir d’Azure Log Analytics vers Azure Data Lake Storage.

Une fois vos données dans Azure Data Lake Storage, vous pouvez commencer à exécuter des requêtes Big Data avec Azure Synapse. Pour plus d’informations, consultez [Exportation des données Log Analytics dans Azure Monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal).

## <a name="hunt-on-historical-data-at-scale"></a>Recherche de données historiques à l’échelle

Azure Sentinel fournit le notebook **Azure Synapse - Détecter le balisage potentiel d'un réseau à l'aide d'Apache Spark**. Utilisez ce notebook comme modèle dans le cadre d’un exemple de scénario de sécurité réaliste pour commencer rechercher dans le Big Data avec Azure Sentinel et Azure synapse.

**Pour détecter les balises de réseau potentielles à l’aide d’Azure Sentinel et d’Azure Synapse** :

1. Dans la page **Notebooks** dans Azure Sentinel, sélectionnez l’onglet **Modèles**, puis saisissez **Synapse** dans la barre de recherche pour trouver le notebook.

1. Recherchez et sélectionnez le notebook **Azure Synapse - Détecter les balises de réseau potentielles à l’aide d’Apache Spark**, puis sélectionnez **Cloner le modèle de notebook** en bas à droite.

    Dans le volet **Cloner le notebook**, modifiez le nom du notebook si nécessaire, sélectionnez votre [Espace de travail Azure Machine Learning](#connect-to-azure-ml-and-synapse-workspaces), puis sélectionnez **Enregistrer**.

1. Une fois votre notebook déployé, sélectionnez **Lancer le notebook** pour l’ouvrir.

    Le notebook s’ouvre dans votre espace de travail Azure ML, dans Azure Sentinel. Pour plus d’informations, consultez [Lancer un notebook à l’aide de votre espace de travail Azure ML](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Exécutez les cellules dans les étapes initiales du notebook pour charger les bibliothèques et fonctions Python nécessaires et pour vous authentifier auprès des ressources Azure.

1. Lorsque vous accédez à la cellule intitulée **Démarrer une session Spark**, exécutez la cellule pour commencer à utiliser votre session Azure Synapse, afin d’utiliser votre pool Apache Spark en tant que calcul pour vos tâches de préparation des données et de mis de données au lieu d’utiliser votre calcul Azure ML.

1. Exécutez les cellules suivantes pour configurer et exécuter vos requêtes sur les données qui sont maintenant stockées dans Azure Data Lake Storage. Par exemple, [mettez à jour votre période de recherche](#define-your-data-lookback-period) pour inclure les données d’une plage de temps spécifique.

1. Lorsque vous avez terminé avec votre requête, exportez les résultats d’Azure Data Lake Storage vers votre espace de travail Log Analytics.

    Le code suivant, présenté dans l’étape **Exporter les résultats à partir d’ADLS**, enregistre les résultats de votre requête sous la forme d’un seul fichier JSON. Définissez le nom de votre répertoire et exécutez la cellule :

    ```python
    %%synapse
    dir_name = "<dir-name>"  # specify desired directory name
    new_path = adls_path + dir_name
    csl_beacon_pd = csl_beacon_df.coalesce(1).write.format("json").save(new_path)
    ```

1. Après avoir exporté vos données, vous pouvez arrêter votre session Spark. Une fois que vous avez arrêté votre session Spark, les requêtes suivantes sont exécutées à l’aide du calcul Azure ML par défaut indiqué dans le champ **Calcul** en haut de la page.

    Exécutez la cellule dans l’étape **Arrêter la session Spark** :

    ```python
    %synapse stop
    ```

1. Exportez votre fichier JSON avec les résultats de votre requête Azure Data Lake Storage vers un système de fichiers local.

    Utilisez le code figurant dans les étapes **Exporter les résultats d’ADLS vers le système de fichiers local**, **Télécharger les fichiers à partir d’ADLS** et **Afficher les résultats** pour enregistrer votre fichier JSON localement et les afficher.

1. Une fois que vous avez enregistré vos résultats localement, vous pouvez les enrichir avec des données supplémentaires et exécuter des visualisations. Par exemple, le notebook **Azure Synapse - Détecter le réseau potentiel à l’aide d’Apache Spark** fournit des étapes supplémentaires pour effectuer les actions suivantes :

    - Enrichissez les résultats avec la géolocalisation des adresses IP, les WhoIs et les autres données d’informations sur les menaces, afin de disposer d’une image plus complète des comportements réseau anormaux.
    - Exécutez les visualisations MSTICPy pour mapper les emplacements tout en regardant la distribution des connexions réseau distantes ou d’autres événements.

    Les résultats peuvent être réécrits dans Azure Sentinel pour une investigation plus approfondie. Par exemple, vous pouvez créer des incidents, des watchlists ou des signets de chasse personnalisés à partir des résultats.

    > [!TIP]
    > Utilisez ces étapes pour détecter les balises potentielles du réseau, ou utilisez-les comme modèles et modifiez-les en fonction des besoins de votre organisation.
    >

## <a name="manage-your-azure-synapse-session-from-azure-sentinel"></a>Gérer votre session Azure Synapse à partir d’Azure Sentinel

Dans le cas contraire dans une session Azure Synapse, Azure Sentinel est défini par défaut sur le calcul Azure ML sélectionné dans le champ **Calcul** en haut de la page **Notebooks**.

Pour démarrer et arrêter votre session Azure Synapse, utilisez le code suivant, que vous pouvez copier à partir de cet emplacement ou à l’aide du notebook **Azure Synapse - Détecter les balises de réseau potentielles à l’aide d’Apache Spark**.

### <a name="start-an-azure-synapse-session-from-within-azure-sentinel"></a>Démarrer une session de Synapse Azure à partir d’Azure Sentinel

Exécutez le code ci-dessous :

```python
%synapse start -w $amlworkspace -s $subscription_id -r $resource_group -c $synapse_spark_compute
```

Démarrez toutes les cellules de code suivantes avec `%%synapse` pour utiliser la session Synapse que vous avez démarrée.

Par exemple :

```python
%%synapse

# Primary storage info
account_name = '<storage account name>' # fill in your primary account name
container_name = '<container name>' # fill in your container name
subscription_id = '<subscription if>' # fill in your subscription id
resource_group = '<resource group>' # fill in your resource groups for ADLS
workspace_name = '<azure sentinel/log analytics workspace name>' # fill in your workspace name
device_vendor = "Fortinet"  # Replace your desired network vendor from commonsecuritylogs

# Datetime and lookback parameters
end_date = "<enter date in the format yyyy-MM-dd e.g.2021-09-17>"  # fill in your input date
lookback_days = 21 # fill in lookback days if you want to run it on historical data. make sure you have historical data available in ADLS
```

### <a name="define-your-data-lookback-period"></a>Définir votre période de recherche de données

Les requêtes de Big Data dans cet exemple de notebook peuvent s’exécuter sur les données à partir d’une date prédéfinie, à l’aide du paramètre `end-date` ou d’une plage de temps plus longue.

Par exemple :

- Si vous souhaitez obtenir des données à partir d’une date spécifique, spécifiez le 15 novembre 2021 comme date actuelle, et la requête s’exécutera uniquement sur les données du 15 novembre 2021. 

- Pour définir une étendue de temps plus longue pour votre requête, en plus de la date actuelle, définissez un paramètre de recherche. Par exemple, si le paramètre `lookback_days` est défini sur `21` jours et que le paramètre `end_date` a la valeur `2021-11-17`, la requête examinera les données pour les 21 jours, à compter du 17 novembre 2021.

Dans le notebook **Azure Synapse - Détecter les balises de réseau potentielles à l’aide d’Apache Spark**, vous trouverez ce code dans l'**étape de préparation des données**.

Par exemple :

```python
# Datetime and lookback parameters
end_date = "2021-11-17>"  # fill in your input date
lookback_days = "21" # fill in lookback days if you want to run it on historical data. Make sure you have historical data available in ADLS
```

Dans l’exemple ci-dessus, les requêtes sont exécutées sur les données comprises entre le 28 octobre et le 17 novembre 2021.

### <a name="stop-an-azure-synapse-session-from-within-azure-sentinel"></a>Arrêter une session de Synapse Azure à partir d’Azure Sentinel

Exécutez le code ci-dessous :

```python
%synapse stop
```

### <a name="switch-azure-synapse-workspaces-in-azure-sentinel"></a>Basculer entre les espaces de travail Azure Synapse dans Azure Sentinel

Pour gérer ou sélectionner un autre espace de travail Synapse que celui auquel vous êtes actuellement connecté, utilisez l’une des méthodes suivantes :

- **Si vous avez déjà créé un service lié entre Azure ML et le nouvel espace de travail Azure Synapse** :

    1. Saisissez le nom du paramètre `linkservice` dans la cellule de code suivante, puis réexécutez la cellule et les cellules suivantes :

        ```python
        amlworkspace = "<aml workspace name>"  # fill in your AML workspace name
        subscription_id = "<subscription id>" # fill in your subscription id
        resource_group = '<resource group of AML workspace>' # fill in your resource groups for AML workspace
        linkedservice = '<linked service name>' # fill in your linked service created to connect to synapse workspace
        ```

    1. Veillez à fournir le nom du pool Azure Synapse Spark qui a été enregistré et associé au service lié :

        ```python
        synapse_spark_compute = "<synapse spark compute>"
        ```

- **Si vous n’avez pas encore de service lié entre vos espaces de travail Azure ML et Azure Synapse**, veillez à exécuter le notebook **Azure Synapse – Configurer Azure ML et Azure Synapse Analytics** pour configurer le service lié avant d’exécuter **Azure Synapse - Détecter les balises de réseau potentielles à l’aide d’Apache Spark**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Utiliser des notebooks Jupyter pour repérer des menaces de sécurité](notebooks.md)
- [Didacticiel : Prise en main des notebooks Jupyter et de MSTICPy dans Azure Sentinel](notebook-get-started.md)
- [Lier des espaces de travail Azure Synapse Analytics et Azure Machine Learning et attacher des pools Apache Spark (préversion)](/azure/machine-learning/how-to-link-synapse-ml-workspaces)
