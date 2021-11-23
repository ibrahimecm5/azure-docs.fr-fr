---
title: 'Tutoriel : Analyse des sentiments avec Cognitive Services'
description: Découvrir comment utiliser Cognitive Services pour l’analyse des sentiments dans Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: a62feb0ea2aeb96a80002a40aa7bf905690e197c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318315"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services"></a>Tutoriel : Analyse des sentiments avec Cognitive Services

Dans ce tutoriel, vous allez apprendre à enrichir facilement vos données dans Azure Synapse Analytics avec [Azure Cognitive Services](../../cognitive-services/index.yml). Vous allez utiliser les fonctionnalités de l’[Analyse de texte](../../cognitive-services/text-analytics/index.yml) pour effectuer une analyse des sentiments. 

Dans Azure Synapse, un utilisateur peut simplement sélectionner une table qui contient une colonne de texte à enrichir avec des sentiments. Ces sentiments peuvent être positifs, négatifs, mixtes ou neutres. Une probabilité est également retournée.

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
> - Étapes à effectuer pour obtenir un jeu de données de table Spark qui contient une colonne de texte pour l’analyse des sentiments.
> - Utilisation d’une expérience d’assistant dans Azure Synapse pour enrichir les données en utilisant l’Analyse de texte dans Cognitive Services.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Pool Spark dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Avoir effectué les étapes de pré-configuration décrites dans le tutoriel [Configurer Cognitive Services dans Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Créer une table Spark

Vous aurez besoin d’une table Spark pour ce tutoriel.

1. Téléchargez le fichier [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv), qui contient un jeu de données pour l’analyse de texte. 

1. Chargez le fichier sur votre compte de stockage Azure Synapse dans Data Lake Storage Gen2.
  
   ![Capture d’écran montrant les sélections pour le chargement de données](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Créez une table Spark à partir du fichier .csv en cliquant avec le bouton droit sur le fichier, puis en sélectionnant **Nouveau notebook** > **Créer une table Spark**.

   ![Capture d’écran montrant les sélections pour la création d’une table Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Nommez la table dans la cellule de code, puis exécutez le notebook sur un pool Spark. Pensez à définir `header=True`.

   ![Capture d’écran illustrant l’exécution d’un notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Ouvrir l’Assistant Cognitive Services

1. Cliquez avec le bouton droit sur la table Spark créée dans la procédure précédente. Sélectionnez **Machine Learning** > **Prédire avec un modèle** pour ouvrir l’Assistant.

   ![Capture d’écran montrant les sélections permettant d’ouvrir l’Assistant de scoring.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Un panneau de configuration s’affiche et vous êtes invité à sélectionner un modèle Cognitive Services. Sélectionnez **Analyse des sentiments**.

   ![Capture d’écran qui montre la sélection d’un modèle Cognitive Services](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-choose.png)

## <a name="configure-sentiment-analysis"></a>Configurer l’analyse des sentiments

Ensuite, configurez l’analyse des sentiments. Sélectionnez les informations suivantes :
- **Service lié Azure Cognitive Services** : dans le cadre des étapes préalables, vous avez créé un service lié à votre instance [Cognitive Services](tutorial-configure-cognitive-services-synapse.md). Sélectionnez-le ici.
- **Langue** : Sélectionnez **Anglais** comme langue du texte sur lequel vous souhaitez effectuer une analyse des sentiments.
- **Colonne de texte** : Sélectionnez **comment (chaîne)** comme colonne de texte de votre jeu de données que vous voulez analyser pour déterminer le sentiment.

Quand vous avez terminé, sélectionnez **Ouvrir le notebook**. Cette opération génère automatiquement un notebook avec le code PySpark qui effectue l’analyse des sentiments avec Azure Cognitive Services.

![Capture d’écran montrant les sélections pour la configuration de l’analyse des sentiments](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-config.png)

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

Le notebook que vous venez d’ouvrir utilise la [bibliothèque SynapseML](https://github.com/microsoft/SynapseML) pour se connecter à Cognitive Services. Le service lié Azure Cognitive Services que vous avez fourni vous permet de référencer en toute sécurité votre service cognitif à partir de cette expérience sans divulguer de secrets.

 Vous pouvez maintenant exécuter toutes les cellules pour enrichir vos données avec des sentiments. Sélectionnez **Exécuter tout**. 

Les sentiments sont retournés comme étant **positifs**, **négatifs**, **neutres** ou **mixtes**. Vous recevez également des probabilités par sentiment. [Découvrez-en plus sur l’analyse des sentiments dans Cognitive Services](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Capture d’écran illustrant l’analyse des sentiments](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-notebook.png)

## <a name="next-steps"></a>Étapes suivantes
- [Tutoriel : Détection d’anomalie avec Azure Cognitive Services](tutorial-cognitive-services-anomaly.md)
- [Tutoriel : Scoring de modèle Machine Learning dans des pools SQL dédiés Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
