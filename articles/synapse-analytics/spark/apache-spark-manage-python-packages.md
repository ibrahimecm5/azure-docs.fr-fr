---
title: Gérer des bibliothèques Python pour Apache Spark
description: Découvrez comment ajouter et gérer des bibliothèques Python utilisées par Apache Spark dans Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.custom: has-adal-ref
ms.openlocfilehash: c696105fee677e8e8dca71d5515e0dd2374960b0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228446"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gérer des bibliothèques Python pour Apache Spark dans Azure Synapse Analytics

Les bibliothèques fournissent du code réutilisable que vous pouvez inclure dans vos programmes ou projets. 

Plusieurs raisons peuvent vous amener à mettre à jour l’environnement de votre pool Apache Spark serverless. Par exemple, il se peut que :
- l’une de vos dépendances de base vienne de publier une nouvelle version.
- vous ayez besoin d’un autre package pour entraîner votre modèle Machine Learning ou préparer vos données.
- vous ayez trouvé un package plus performant et n’ayez plus besoin de l’ancien.

Pour que vos applications disposent d’un code tiers ou généré en local, vous pouvez installer une bibliothèque sur l’un de vos pools Apache Spark serverless ou sur votre session de notebook. Dans cet article, vous découvrirez comment gérer les bibliothèques Python dans votre pool Apache Spark serverless.

## <a name="default-installation"></a>Installation par défaut
Apache Spark dans Azure Synapse Analytics possède un ensemble complet de bibliothèques pour les tâches courantes d’engineering données, de préparation des données, d’apprentissage automatique et de visualisation des données. Pour la liste complète des bibliothèques, consultez la [prise en charge des versions d’Apache Spark](apache-spark-version-support.md). 

Lors du démarrage d’une instance Spark, ces bibliothèques sont automatiquement incluses. Vous pouvez ajouter des packages Python et des packages personnalisés supplémentaires au niveau du pool Spark et de la session.

## <a name="pool-libraries"></a>Bibliothèques de pools
Une fois que vous avez identifié les bibliothèques Python que vous souhaitez utiliser pour votre application Spark, vous pouvez les installer dans un pool Spark. Les bibliothèques de pools sont disponibles pour tous les notebooks et toutes les tâches qui s’exécutent sur le pool.

Vous pouvez installer une bibliothèque sur un cluster de deux manières principales :
-  Installer une bibliothèque d’espace de travail qui a été chargée en tant que package d’espace de travail.
-  Fournir une spécification d’environnement *requirements.txt* ou *Conda environment.yml* pour installer des packages à partir de référentiels tels que PyPI, Conda-Forge et bien plus encore.

> [!IMPORTANT]
> - Si le package que vous installez est volumineux ou si son installation prend beaucoup de temps, cela affecte le temps de démarrage de l’instance Spark.
> - La modification de la version de PySpark, Python, Scala/Java, .NET ou Spark n’est pas prise en charge.
> - L’installation de packages à partir de dépôts externes comme PyPI, Conda-Forge ou des canaux Conda par défaut n’est pas prise en charge dans les espaces de travail avec la protection contre l’exfiltration de données activée.

### <a name="install-python-packages"></a>Installer des packages Python
Pour installer les packages Python à partir de référentiels tels que PyPI et Conda-Forge, vous devez fournir un fichier de spécification d’environnement. 

#### <a name="environment-specification-formats"></a>Formats des spécifications d’environnement

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
Un fichier *requirements.txt* (sortie de la commande `pip freeze`) permet de mettre à niveau l’environnement. Lorsqu’un pool est mis à jour, les packages répertoriés dans ce fichier sont téléchargés à partir de PyPI. Les dépendances complètes sont ensuite mises en cache et enregistrées pour une réutilisation ultérieure du pool. 

L’extrait de code suivant montre le format du fichier de configuration requise. Le nom du package PyPI est mentionné avec une version exacte. Ce fichier est au format décrit dans la documentation de référence [pip freeze](https://pip.pypa.io/en/stable/cli/pip_freeze/). 

Cet exemple épingle une version spécifique. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format"></a>Format YML
Par ailleurs, vous pouvez également fournir un fichier *environment.yml* pour mettre à jour l’environnement du pool. Les packages répertoriés dans ce fichier sont téléchargés à partir des canaux Conda par défaut, Conda-Forge et PyPI. Vous pouvez spécifier d’autres canaux ou supprimer les canaux par défaut à l’aide des options de configuration.

Cet exemple spécifie les canaux et les dépendances Conda/PyPI. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Pour en savoir plus sur la création d’un environnement à partir du fichier environment.yml, consultez la section [Créer un environnement à partir d’un fichier environment.yml](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
).

#### <a name="update-python-packages"></a>Mettre à jour les packages Python
Une fois que vous avez identifié le fichier de spécification d’environnement ou l’ensemble de bibliothèques que vous souhaitez installer sur le pool Spark, accédez à Synapse Studio ou au Portail Azure pour mettre à jour les bibliothèques du pool Spark. Ici, vous pouvez fournir la spécification de l’environnement et sélectionner les bibliothèques d’espace de travail à installer. 

Une fois les modifications enregistrées, un travail Spark exécute l’installation et met en cache l’environnement obtenu pour une réutilisation ultérieure. Une fois le travail terminé, les nouveaux travaux Spark ou les nouvelles sessions de notebook utilisent les bibliothèques de pools mises à jour. 

##### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>Gérer les packages à partir de Synapse Studio ou du Portail Azure
Vous pouvez gérer les bibliothèques de pools Spark depuis Synapse Studio ou le Portail Azure. 

Pour mettre à jour un pool Spark ou y ajouter des bibliothèques :
1. Accédez à votre espace de travail Azure Synapse Analytics à partir du portail Azure.

    Si vous effectuez une mise à jour depuis le **Portail Azure** :

    - Sous la section **Ressources Synapse**, sélectionnez l’onglet **Pools Apache Spark** et sélectionnez un pool Spark dans la liste.
     
    - Sélectionnez les **packages** dans la section **Paramètres** du pool Spark.
  
    ![Capture d’écran mettant en évidence le bouton Charger le fichier config de l’environnement.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Ajouter des bibliothèques Python")
   
    Si vous effectuez une mise à jour depuis **Synapse Studio** :
    - Sélectionnez **Gérer** à partir du volet de navigation principal, puis sélectionnez **Pools Apache Spark**.

    - Sélectionnez la section **Packages** pour un pool Spark spécifique.
    ![Capture d’écran mettant en évidence l’option de téléchargement de la configuration de l’environnement depuis Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Ajouter des bibliothèques Python à partir de Studio")
   
2. Téléchargez le fichier config de l’environnement à l’aide du sélecteur de fichiers dans la section **Packages** de la page.
3. Vous pouvez également sélectionner des **packages d’espace de travail** supplémentaires pour les ajouter à votre pool. 
4. Une fois vos modifications enregistrées, une tâche système se déclenche pour installer et mettre en cache les bibliothèques spécifiées. Ce processus permet de réduire le temps de démarrage global de la session. 
5. Une fois la tâche terminée, toutes les nouvelles sessions récupèrent les bibliothèques de pool mises à jour.

> [!IMPORTANT]
> En sélectionnant l’option **Forcer les nouveaux paramètres**, vous terminez toutes les sessions actives pour le pool Spark sélectionné. Une fois les sessions terminées, vous devez attendre le redémarrage du pool. 
>
> Si ce paramètre est désactivé, vous devez attendre que la session Spark en cours se termine ou l’arrêter manuellement. Une fois la session terminée, vous devez laisser le pool redémarrer.


##### <a name="track-installation-progress"></a>Vérifier la progression de l’installation  
Un travail Spark réservé au système démarre chaque fois qu’un pool est mis à jour avec un nouvel ensemble de bibliothèques. Ce travail Spark permet de surveiller l’état de l’installation de la bibliothèque. En cas d’échec de l’installation suite à des conflits avec la bibliothèque ou à d’autres problèmes, l’état précédent ou l’état par défaut du pool Spark est rétabli. 

De plus, les utilisateurs peuvent consulter les journaux d’installation pour identifier les conflits de dépendance ou vérifier quelles bibliothèques ont été installées lors de la mise à jour du pool.

Pour afficher ces journaux :
1. Accédez à la liste des applications Spark dans l’onglet **Surveiller**. 
2. Sélectionnez la tâche de l’application Spark du système qui correspond à la mise à jour de votre pool. Ces tâches système s’exécutent sous le titre *SystemReservedJob-LibraryManagement*.
   ![Capture d’écran mettant en évidence la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Afficher la tâche de bibliothèque système")
3. Affichez les journaux du **pilote** et **stdout**. 
4. Parmi les résultats, vous verrez les journaux relatifs à l’installation de vos dépendances.
    ![Capture d’écran mettant en évidence les résultats de la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Afficher la progression de la tâche de bibliothèque système")

## <a name="install-wheel-files"></a>Installer les fichiers wheel
Les fichiers wheel Python sont fréquemment utilisés pour créer le package des bibliothèques Python. Dans Azure Synapse Analytics, les utilisateurs peuvent télécharger leurs fichiers wheel sur un emplacement connu, le compte Azure Data Lake Storage, ou les télécharger à l’aide de l’interface des packages d’espace de travail Azure Synapse.

### <a name="workspace-packages"></a>Packages d’espace de travail 
Les packages d’espace de travail peuvent être des fichiers wheel personnalisés ou privés. Vous pouvez télécharger ces packages dans votre espace de travail et les attribuer par la suite à un pool Spark spécifique.

Pour ajouter des packages d’espace de travail :
1. Accédez à l’onglet **Gérer** > **Packages d’espace de travail**.
2. Téléchargez vos fichiers wheel à l’aide du sélecteur de fichiers.
3. Une fois les fichiers téléchargés dans l’espace de travail Azure Synapse, vous pouvez ajouter ces packages à un pool Apache Spark donné.

![Capture d’écran mettant en évidence les packages d’espace de travail.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Afficher les packages d’espace de travail")

>[!WARNING]
>- Dans Azure Synapse, un pool Apache Spark peut tirer parti des bibliothèques personnalisées qui sont téléchargées en tant que packages d’espace de travail ou dans un chemin d’Azure Data Lake Storage bien connu. Toutefois, ces deux options ne peuvent pas être utilisées simultanément dans le même pool Apache Spark. Si les packages sont fournis à l’aide des deux méthodes, seuls les fichiers de roues spécifiés dans la liste des packages d’espace de travail sont installés. 
>
>- Une fois que les packages de l’espace de travail sont utilisés pour installer des packages sur un pool Apache Spark donné, vous ne pouvez plus spécifier de packages en utilisant le chemin du compte de stockage sur le même pool.  

### <a name="storage-account"></a>Compte de stockage
Les packages Wheel personnalisés peuvent être installés sur le pool Apache Spark en chargeant tous les fichiers Wheel dans le compte Azure Data Lake Storage (Gen2) qui est lié à l’espace de travail Synapse. 

Les fichiers doivent être téléchargés vers le chemin d’accès suivant dans le conteneur par défaut du compte de stockage : 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> - Dans certains cas, vous devrez peut-être créer le chemin d’accès basé sur la structure au-dessus s’il n’existe pas déjà. Vous devrez peut-être, par exemple, ajouter le dossier ```python``` dans le dossier ```libraries``` s’il ne s’y trouve pas déjà.
> - Cette méthode de gestion des fichiers wheels personnalisés n’est pas prise en charge sur le runtime Azure Synapse pour Apache Spark 3,0. Reportez-vous à la fonctionnalité des packages d’espace de travail pour gérer les fichiers wheels personnalisés.

> [!IMPORTANT]
> Pour installer des bibliothèques personnalisées à l’aide de la méthode Azure DataLake Storage, vous devez disposer des autorisations **Contributeur aux données Blob du stockage** ou **Propriétaire des données Blob du stockage** sur le compte de stockage Gen2 principal qui est lié à l’espace de travail Azure Synapse Analytics.


## <a name="session-scoped-packages"></a>Packages avec étendue de session
Outre les packages du niveau pool, vous pouvez spécifier des bibliothèques incluses dans l’étendue de la session au début d’une session de notebook.  Les bibliothèques avec étendue de session vous permettent de spécifier et d’utiliser des environnements Python personnalisés dans une session de notebook. 

Lorsque vous utilisez des bibliothèques avec étendue de session, il est important de garder à l’esprit les points suivants :
   - Lorsque vous installez des bibliothèques avec étendue de session, seul le notebook actif peut accéder aux bibliothèques spécifiées. 
   - Ces bibliothèques n’ont aucune incidence sur les autres sessions ou tâches qui utilisent le même pool Spark. 
   - Ces bibliothèques sont installées en plus du runtime de base et des bibliothèques de pools. 
   - Les bibliothèques de notebooks sont prioritaires.

Pour spécifier des packages avec étendue de session :
1.  Accédez au pool Spark sélectionné et vérifiez que vous avez activé les bibliothèques de la session.  Pour activer ce paramètre, accédez à l’onglet **Gérer** > **Pool Apache Spark** > **Packages**. ![Activez les packages de session.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Activer les packages de session")
2.  Une fois le paramètre appliqué, vous pouvez ouvrir un notebook et sélectionner **Configurer la session**> **Packages**.
  ![Spécifiez les packages de session.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Mettre à jour la configuration de session")
3.  Ici, vous pouvez télécharger un fichier Conda *environment.yml* pour installer ou mettre à niveau les packages d’une session. Une fois que vous avez démarré votre session, les bibliothèques spécifiées sont installées. Une fois votre session terminée, ces bibliothèques ne sont plus disponibles, car elles sont spécifiques à votre session.

## <a name="verify-installed-libraries"></a>Vérifier les bibliothèques installées
Pour vérifier si les bonnes versions des bibliothèques appropriées sont installées à partir de PyPI, exécutez le code suivant :
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Dans certains cas, il se peut que vous deviez examiner la version du package individuellement pour voir les versions du package de Conda.

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)
- Résoudre les erreurs d’installation de bibliothèque : [Résoudre les erreurs de bibliothèque](apache-spark-troubleshoot-library-errors.md)
- Créer un canal Conda privé à l’aide de votre compte Azure Data Lake Storage : [Canaux privés Conda](./spark/../apache-spark-custom-conda-channel.md)
