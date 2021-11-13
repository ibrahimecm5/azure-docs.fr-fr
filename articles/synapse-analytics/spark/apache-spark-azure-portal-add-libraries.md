---
title: Gestion des packages
description: Découvrez comment ajouter et gérer des bibliothèques utilisées par Apache Spark dans Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4ad54fe95bc646d7ed3c96ad901ae2ba1337ab7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251335"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gérer des bibliothèques pour Apache Spark dans Azure Synapse Analytics
Les bibliothèques fournissent du code réutilisable que vous pouvez inclure dans vos programmes ou projets. 

Plusieurs raisons peuvent vous amener à mettre à jour l’environnement de votre pool Apache Spark serverless. Par exemple, il se peut que :
- l’une de vos dépendances de base ait publié une nouvelle version.
- vous ayez besoin d’un autre package pour entraîner votre modèle Machine Learning ou préparer vos données.
- vous ayez trouvé un package plus performant et n’ayez plus besoin de l’ancien.
- votre équipe ait créé un package personnalisé et que vous en ayez besoin dans votre pool Apache Spark.

Pour que vos applications disposent d’un code tiers ou généré en local, vous pouvez installer une bibliothèque sur l’un de vos pools Apache Spark serverless ou sur votre session de notebook.
  
## <a name="default-installation"></a>Installation par défaut
Apache Spark dans Azure Synapse Analytics propose une installation complète d’Anacondas, ainsi que des bibliothèques supplémentaires. Pour la liste complète des bibliothèques, consultez la [prise en charge des versions d’Apache Spark](apache-spark-version-support.md). 

Lors du démarrage d’une instance Spark, ces bibliothèques sont automatiquement incluses. Vous pouvez ajouter d’autres packages au niveau du pool Spark ou de la session.

## <a name="workspace-packages"></a>Packages d’espace de travail
Lors du développement d’applications ou de modèles personnalisés, votre équipe peut créer différents artefacts de code comme des fichiers wheel ou jar pour créer le package de votre code. 

Dans Synapse, les packages d’espace de travail peuvent être des fichiers wheel ou jar personnalisés ou privés. Vous pouvez télécharger ces packages dans votre espace de travail et les attribuer par la suite à un pool Spark spécifique. Une fois attribués, ces packages d’espace de travail sont automatiquement installés sur toutes les sessions du pool Spark.

Pour en savoir plus sur la gestion des bibliothèques d’espace de travail, consultez les guides pratiques suivants :

- [Packages d’espace de travail Python : ](./apache-spark-manage-python-packages.md#install-wheel-files) Téléchargez des fichiers wheel Python en tant que package d’espace de travail et ajoutez ultérieurement ces packages à des pools Apache Spark serverless spécifiques.
- [Packages d’espace de travail Scala/Java : ](./apache-spark-manage-scala-packages.md#workspace-packages) Téléchargez des fichiers jar Scala et Java en tant que package d’espace de travail et ajoutez ultérieurement ces packages à des pools Apache Spark serverless spécifiques.

## <a name="pool-packages"></a>Packages de pools
Dans certains cas, il se peut que vous souhaitiez normaliser l’ensemble des packages utilisés sur un pool Apache Spark donné. Cette normalisation peut se révéler utile si les mêmes packages sont généralement installés par plusieurs personnes de votre équipe. 

Grâce aux fonctionnalités de gestion du pool Azure Synapse Analytics, vous pouvez configurer l’ensemble de bibliothèques par défaut que vous souhaitez installer sur un pool Apache Spark serverless donné. Ces bibliothèques sont installées en plus du [runtime de base](./apache-spark-version-support.md). 

La gestion des pools est uniquement prise en charge pour Python pour le moment. Pour Python, les pools Synapse Spark utilisent Conda pour installer et gérer les dépendances du package Python. Lorsque vous spécifiez vos bibliothèques au niveau du pool, vous pouvez désormais fournir un fichier requirements.txt ou environment.yml. Le fichier config de cet environnement est utilisé chaque fois qu’une instance Spark est créée à partir de ce pool Spark. 

Pour en savoir plus sur ces fonctionnalités, consultez la documentation sur la [gestion des pools Python](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Si le package que vous installez est volumineux ou si son installation prend beaucoup de temps, cela affecte le temps de démarrage de l’instance Spark.
> - La modification de la version de PySpark, Python, Scala/Java, .NET ou Spark n’est pas prise en charge.
> - L’installation de packages à partir de PyPI n’est pas prise en charge dans les espaces de travail DEP.

## <a name="session-scoped-packages"></a>Packages avec étendue de session
Souvent, lors de l’analyse interactive des données ou de l’apprentissage automatique, il se peut que vous souhaitiez essayer des packages plus récents ou que vous ayez besoin de packages qui ne sont pas déjà disponibles dans votre pool Apache Spark. Au lieu de mettre à jour la configuration du pool, les utilisateurs peuvent désormais utiliser des packages avec étendue de session pour ajouter, gérer et mettre à jour les dépendances de session.

Les packages avec étendue de session permettent aux utilisateurs de définir les dépendances des packages au démarrage de leur session. Lorsque vous installez un package avec étendue de session, seule la session active peut accéder aux packages spécifiés. Par conséquent, ces packages avec étendue de session n’ont aucune incidence sur les autres sessions ou tâches qui utilisent le même pool Apache Spark. Par ailleurs, ces bibliothèques sont installées en plus des packages du runtime de base et du niveau du pool. 

Pour en savoir plus sur la gestion des packages avec étendue de session, consultez les guides pratiques suivants :

- [Packages de session Python :](./apache-spark-manage-python-packages.md) Au démarrage d’une session, fournissez un fichier *environment.yml* Conda pour installer des packages Python supplémentaires à partir de référentiels courants. 
- [Packages de session Scala/Java : ](./apache-spark-manage-scala-packages.md) Au démarrage de votre session, fournissez une liste de fichiers jar à installer à l’aide de `%%configure`.

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)
