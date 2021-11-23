---
title: 'Tutoriel : Détection d’anomalie avec Cognitive Services'
description: Découvrez comment utiliser Cognitive Services pour la détection d’anomalie dans Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 07/01/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 505bef004acb688a1fdb1ccc87d261b1a12c6009
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318291"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services"></a>Tutoriel : Détection d’anomalie avec Cognitive Services

Dans ce tutoriel, vous allez apprendre à enrichir facilement vos données dans Azure Synapse Analytics avec [Azure Cognitive Services](../../cognitive-services/index.yml). Vous utiliserez le [Détecteur d’anomalies](../../cognitive-services/anomaly-detector/index.yml) pour détecter les anomalies. Dans Azure Synapse, un utilisateur peut simplement sélectionner une table à enrichir pour la détection d’anomalie.

Ce didacticiel contient les sections suivantes :

> [!div class="checklist"]
> - Étapes à effectuer pour obtenir un jeu de données de table Spark qui contient des données de série chronologique.
> - Utilisation d’une expérience d’assistant dans Azure Synapse pour enrichir les données à l’aide du Détecteur d’anomalies dans Cognitive Services.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Pool Spark dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Avoir effectué les étapes de préconfiguration indiquées dans le tutoriel [Configurer Cognitive Services dans Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Créer une table Spark

Vous avez besoin d’une table Spark pour ce tutoriel.

1. Téléchargez le fichier de notebook suivant qui contient du code pour générer une table Spark : [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Chargez le fichier dans votre espace de travail Azure Synapse.

   ![Capture d’écran montrant les sélections pour le chargement d’un notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Ouvrez le fichier de notebook, puis sélectionnez **Exécuter tout** pour exécuter toutes les cellules.

   ![Capture d’écran montrant les sélections pour la création d’une table Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Une table Spark nommée **anomaly_detector_testing_data** doit maintenant apparaître dans la base de données Spark par défaut.

## <a name="open-the-cognitive-services-wizard"></a>Ouvrir l’Assistant Cognitive Services

1. Cliquez avec le bouton droit sur la table Spark créée à l’étape précédente. Sélectionnez **Machine Learning** > **Prédire avec un modèle** pour ouvrir l’Assistant.

   ![Capture d’écran montrant les sélections permettant d’ouvrir l’Assistant de scoring.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Un panneau de configuration s’affiche et vous êtes invité à sélectionner un modèle Cognitive Services. Sélectionnez le **Détecteur d’anomalies**.

   ![Capture d’écran montrant la sélection du Détecteur d’anomalies en tant que modèle](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="configure-anomaly-detector"></a>Configurer le Détecteur d’anomalies

Fournissez les informations suivantes pour configurer le Détecteur d’anomalies :

- **Service lié Azure Cognitive Services** : dans le cadre des étapes préalables, vous avez créé un service lié à votre instance [Cognitive Services](tutorial-configure-cognitive-services-synapse.md). Sélectionnez-le ici.

- **Fréquence** : fréquence d’échantillonnage de vos données. Choisissez **mensuelle**. 

- **Colonne timestamp** : colonne qui représente l’heure de la série. Choisissez **timestamp (chaîne)** .

- **Colonne de valeur de série chronologique** : colonne qui représente la valeur de la série au moment spécifié par la colonne Horodatage. Choisissez **value (double)** .

- **Colonne de regroupement** : colonne qui regroupe les séries. Autrement dit, toutes les lignes qui ont la même valeur dans cette colonne doivent former une série chronologique. Choisissez **group (chaîne)** .

Quand vous avez terminé, sélectionnez **Ouvrir le notebook**. Cette opération génère automatiquement un notebook avec le code PySpark qui utilise Azure Cognitive Services pour détecter les anomalies.

![Capture d’écran montrant les détails de la configuration du Détecteur d’anomalies](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-config.png)

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

Le notebook que vous venez d’ouvrir utilise la bibliothèque [SynapseML]](https://github.com/microsoft/SynapseML) pour se connecter à Cognitive Services. Le service lié Azure Cognitive Services que vous avez fourni vous permet de référencer en toute sécurité votre service cognitif à partir de cette expérience sans divulguer de secrets.

Vous pouvez maintenant exécuter toutes les cellules pour effectuer une détection d’anomalie. Sélectionnez **Exécuter tout**. [Découvrez-en plus sur le Détecteur d’anomalies dans Cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Capture d’écran montrant la détection d’anomalie.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-notebook.png)

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Analyse des sentiments avec Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutoriel : Scoring de modèle Machine Learning dans des pools SQL dédiés Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
