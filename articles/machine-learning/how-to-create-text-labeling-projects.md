---
title: Configurer un projet d’étiquetage de texte
titleSuffix: Azure Machine Learning
description: Créez un projet pour étiqueter le texte à l’aide de l’outil d’étiquetage des données. Spécifiez une seule étiquette ou plusieurs étiquettes à appliquer à chaque texte.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: data4ml, ignite-fall-2021
ms.openlocfilehash: e3097c6b00d97287526015836c44ddcaeb08177a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468704"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>Créer un projet d’étiquetage de texte et exporter des étiquettes (préversion)

Découvrez comment créer et exécuter des projets d'étiquetage de données pour étiqueter des données textuelles dans Azure Machine Learning.  Spécifiez une ou plusieurs étiquettes à appliquer à chaque élément de texte.

Vous pouvez également utiliser l’outil d’étiquetage des données pour [créer un projet d’étiquetage d’image](how-to-create-image-labeling-projects.md).

> [!IMPORTANT]
> L’étiquetage de texte est actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="text-labeling-capabilities"></a>Fonctionnalités d’étiquetage textuel

L’étiquetage des données Azure Machine Learning est un emplacement central pour créer, gérer et superviser les projets d’étiquetage des données :

- Coordonnez les données, les étiquettes et les membres de l’équipe pour gérer efficacement les tâches d’étiquetage. 
- Suivez la progression et gérez la file d’attente des tâches d’étiquetage incomplètes.
- Démarrez et arrêtez le projet pour contrôler la progression de l’étiquetage.
- Passez en revue les données étiquetées et exportez-les en tant que jeu de données Azure Machine Learning.

> [!Important]
> Les données textuelles doivent être disponibles dans un magasin de données d’objets blob Azure. (Si vous ne disposez pas d’un magasin de données, vous pouvez charger des fichiers pendant la création du projet.)

Formats de données disponibles pour les données de texte :

* **.txt** : chaque fichier représente un seul élément à étiqueter.
* **.csv** ou **.tsv** : chaque ligne représente un élément présenté à l’étiqueteur.  Vous décidez quelles colonnes l’étiqueteur peut voir pour étiqueter la ligne.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>Pour un projet d’étiquetage textuel

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Pour créer un projet, sélectionnez **Ajouter un projet**. Donnez au projet un nom approprié. Le nom du projet ne peut pas être réutilisé, même si le projet est supprimé ultérieurement.

1. Sélectionnez **Texte** pour créer un projet d’étiquetage de texte.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="Création d’un projet d’étiquetage de texte":::

    * Choisissez **Classification de texte multiclasse (préversion)** pour les projets quand vous voulez appliquer *une seule étiquette* issue d’un ensemble d’étiquettes à chaque morceau de texte.
    * Choisissez **Classification de texte multiétiquette (préversion)** pour les projets quand vous voulez appliquer *une ou plusieurs* étiquettes issue d’un ensemble d’étiquettes à chaque morceau de texte.

1. Quand vous êtes prêt à continuer, sélectionnez **Suivant**.

## <a name="add-workforce-optional"></a>Ajouter du personnel (facultatif)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>Spécifier les données à étiqueter

Si vous avez déjà créé un jeu de données qui contient vos données, sélectionnez-le dans la liste déroulante **Sélectionner un jeu de données existant**. Sinon, sélectionnez **Créer un jeu de données** pour utiliser un magasin de données Azure existant ou pour charger des fichiers locaux.

> [!NOTE]
> Un projet ne peut pas contenir plus de 500 000 fichiers.  Si votre jeu de données en contient davantage, seuls les 500 000 premiers fichiers sont chargés.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Créer un jeu de données à partir d’un magasin de données Azure

Dans de nombreux cas, il suffit de charger des fichiers locaux. Toutefois, l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) constitue un moyen plus rapide et plus robuste de transférer une grande quantité de données. Nous recommandons l’Explorateur Stockage en tant que méthode par défaut pour déplacer des fichiers.

Pour créer un jeu de données à partir de données que vous avez déjà stockées dans le Stockage Blob Azure :

1. Sélectionnez **Créer un jeu de données** > **À partir du magasin de données**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez le **type de jeu de données** :
    * Sélectionnez **Tabulaire** si vous utilisez un fichier .csv ou .tsv, où chaque ligne contient une réponse.
    * Sélectionnez **Fichier** si vous utilisez des fichiers .txt distincts pour chaque réponse.
1. (Facultatif) Fournissez une description pour votre jeu de données.
1. Sélectionnez **Suivant**.
1. Sélectionnez le magasin de données.
1. Si vos données se trouvent dans un sous-dossier de votre Stockage Blob, choisissez **Parcourir** pour sélectionner le chemin.
    * Ajoutez « /** » au chemin pour inclure tous les fichiers des sous-dossiers du chemin sélectionné.
    * Ajoutez « * */* .* » pour inclure toutes les données figurant dans le conteneur actuel et ses sous-dossiers.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

### <a name="create-a-dataset-from-uploaded-data"></a>Créer un jeu de données à partir des données chargées

Pour charger directement vos données :

1. Sélectionnez **Créer un jeu de données** > **À partir de fichiers locaux**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez le **type de jeu de données**.
    * Sélectionnez **Tabulaire** si vous utilisez un fichier .csv ou .tsv, où chaque ligne est une réponse.
    * Sélectionnez **Fichier** si vous utilisez des fichiers .txt distincts pour chaque réponse.
1. (Facultatif) Fournissez une description de votre jeu de données.
1. Sélectionnez **Suivant**.
1. (Facultatif) Sélectionnez ou créez un magasin de données. Ou conservez la sélection par défaut pour charger dans le magasin d’objets blob par défaut (« workspaceblobstore ») de votre espace de travail Machine Learning.
1. Sélectionnez **Charger** pour choisir les fichiers ou dossiers locaux à charger.
1. Sélectionnez **Suivant**.
1. Si vous chargez des fichiers .csv ou .tsv :
    * Confirmez les paramètres et l’aperçu, puis sélectionnez **Suivant**.
    * Incluez toutes les colonnes de texte que vous souhaitez que l’étiqueteur affiche lors de la classification de cette ligne.  Si vous utilisez l’étiquetage assisté par ML, l’ajout de colonnes numériques peut dégrader le modèle d’assistance ML.
    * Sélectionnez **Suivant**.
1.  Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Configurer l’actualisation incrémentielle

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

> [!NOTE]
> L’actualisation incrémentielle n’est pas disponible pour les projets qui utilisent une entrée de jeu de données tabulaire (.csv ou .tsv).

## <a name="specify-label-classes"></a>Spécifier des classes d’étiquettes

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>Décrivez la tâche d’étiquetage des données

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> Notez bien que les étiqueteurs peuvent sélectionner les 9 premières étiquettes à l’aide des touches numériques allant de 1 à 9.

## <a name="use-ml-assisted-data-labeling"></a>Utiliser l’étiquetage des données assisté par ML

La page **Étiquetage assisté par ML** vous permet de déclencher des modèles Machine Learning automatiques pour accélérer les tâches d’étiquetage. L’étiquetage assisté par ML est disponible pour les entrées de données de texte au format Fichier (.txt) ou Tabulaire (.csv).

Pour utiliser l’**étiquetage assisté par ML** :

* Sélectionnez **Activer l’étiquetage assisté par ML**.
* Sélectionnez la **Langue du jeu de données** pour le projet. Toutes les langues prises en charge par la [classe TextDNNLanguages](/python/api/azureml-automl-core/azureml.automl.core.constants.textdnnlanguages?view=azure-ml-py&preserve-view=true) figurent dans cette liste.
* Spécifiez une cible de calcul à utiliser. Si votre espace de travail n’en contient pas, un cluster de calcul est créé pour vous et ajouté à votre espace de travail.   Le cluster est créé avec un minimum de 0 nœud, ce qui signifie qu’il ne coûte rien quand il n’est pas utilisé.

### <a name="how-does-ml-assisted-labeling-work"></a>Comment fonctionne l’étiquetage assisté par ML ?

Au début de votre projet d’étiquetage, les éléments sont mélangés dans un ordre aléatoire pour réduire le biais potentiel. Cependant, le biais éventuellement présent dans le jeu de données se reflète dans le modèle entraîné. Par exemple, si 80 % de vos éléments appartiennent à une même classe, environ 80 % des données utilisées pour l’entraînement du modèle feront partie de cette classe. 

Pour entraîner le modèle DNN de texte utilisé par l’étiquetage assisté par ML, le texte d’entrée par exemple d’entraînement est limité environ aux 128 premiers mots du document.  Pour des entrées tabulaires, toutes les colonnes de texte sont d’abord concaténées avant l’application de cette limite. Cette limite pratique est imposée pour terminer l’entraînement du modèle dans un délai convenable. Le texte réel dans un document (entrées au format Fichier) ou un ensemble de colonnes de texte (entrées au format Tabulaire) peut dépasser 128 mots.  La limite ne concerne que ce qui est exploité en interne par le modèle durant le processus d’entraînement.

Le nombre exact d’éléments étiquetés nécessaires au démarrage de l’étiquetage assisté n’est pas un nombre fixe. Celui-ci peut varier considérablement d’un projet d’étiquetage à un autre en fonction de nombreux facteurs, notamment le nombre de classes d’étiquettes et la répartition des étiquettes.

Sachant que les étiquettes finales dépendent encore de l’entrée de l’étiqueteur, cette technologie d’étiquetage est parfois appelée *Opérateur humain dans la boucle*.

> [!NOTE]
> L’étiquetage des données assisté par ML ne prend pas en charge les comptes de stockage par défaut sécurisés derrière un [réseau virtuel](how-to-network-security-overview.md). Vous devez utiliser un compte de stockage autre que celui par défaut pour l’étiquetage des données assisté par ML. Le compte de stockage autre que celui par défaut peut être sécurisé derrière le réseau virtuel.

### <a name="pre-labeling"></a>Préétiquetage

Après l’envoi d’un nombre suffisant d’étiquettes à des fins d’entraînement, le modèle entraîné est utilisé pour prédire les étiquettes. L’étiqueteur voit dès lors les pages qui contiennent les étiquettes prédites déjà présentes dans chaque élément. La tâche doit ensuite examiner ces prédictions et corriger les éléments mal étiquetés avant d’envoyer la page.  

Une fois qu’un modèle Machine Learning a été entraîné sur vos données étiquetées manuellement, le modèle est évalué sur un ensemble d’éléments de test étiquetés manuellement pour déterminer sa précision à plusieurs seuils de confiance. Ce processus d’évaluation sert à déterminer le seuil de confiance au-dessus duquel le modèle est suffisamment précis pour afficher des préétiquettes. Le modèle est ensuite évalué par rapport aux données non étiquetées. Les éléments dont les prédictions ont un niveau de confiance supérieur à ce seuil sont utilisés pour le préétiquetage.

## <a name="initialize-the-text-labeling-project"></a>Initialisez le projet d’étiquetage des données

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Exécuter et surveiller le projet

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>tableau de bord


L’onglet **Tableau de bord** affiche la progression de la tâche d’étiquetage.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="Tableau de bord d’étiquetage des données textuelles":::


Le graphique de progression indique le nombre d’éléments étiquetés, ignorés ou non encore terminés.  Pointez sur le graphique pour afficher le nombre d’éléments dans chaque section.

La section centrale affiche la file d’attente des tâches à affecter. Si l’étiquetage assisté par ML est activé, vous verrez également le nombre d’éléments préétiquetés.


Dans la partie droite se trouve une distribution des étiquettes pour les tâches qui sont terminées.  Notez que, dans certains types de projets, un élément peut avoir plusieurs étiquettes, auquel cas le nombre total d’étiquettes peut être supérieur au nombre total d’éléments.

### <a name="data-tab"></a>Onglet Données

Sous l’onglet **Données**, vous pouvez voir votre jeu de données et passer en revue les données étiquetées. Faites défiler les données étiquetées pour afficher les étiquettes. Si vous voyez des données étiquetées de manière incorrecte, sélectionnez-les, puis choisissez **Rejeter**. Les étiquettes sont alors supprimées et les données replacées en file d’attente des données sans étiquette.

### <a name="details-tab"></a>Onglet Détails

Affichez les détails de votre projet.  Dans cet onglet, vous pouvez :

* Afficher les détails du projet et les jeux de données d’entrée
* Activer l’actualisation incrémentielle
* Afficher les détails du conteneur de stockage utilisé pour stocker les sorties étiquetées dans votre projet
* Ajouter des étiquettes à votre projet
* Modifier les instructions que vous donnez à vos étiquettes

### <a name="access-for-labelers"></a>Accès pour les étiqueteurs

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Ajouter une nouvelle classe d’étiquette à un projet

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exporter les étiquettes
 
Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage. Vous pouvez exporter les données d’étiquette pour Machine Learning - Expérimentation à tout moment.

Il est possible d’exporter différents éléments :

* Fichier CSV. Le fichier CSV est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé dans *Labeling/export/csv*. 
* [Jeu de données Azure Machine Learning comportant des étiquettes](how-to-use-labeled-dataset.md). 

Accédez aux jeux de données Azure Machine Learning exportés dans la section **Jeux de données** de Machine Learning. La page de détails du jeu de données fournit également un exemple de code pour accéder aux étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Dépannage

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>Étapes suivantes

* [Comment étiqueter du texte](how-to-label-data.md#label-text)
