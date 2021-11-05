---
title: Configurer un projet d’étiquetage d’image
titleSuffix: Azure Machine Learning
description: Créez un projet pour étiqueter les images avec l’outil d’étiquetage des données. Pour faciliter cette tâche, activez l’étiquetage assisté par ML ou l’étiquetage par opérateur humain dans la boucle.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: data4ml, ignite-fall-2021
ms.openlocfilehash: b1fc133b02c667ae46d543da3fe020d116d6eb54
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068890"
---
# <a name="create-an-image-labeling-project-and-export-labels"></a>Créer un projet d’étiquetage d’image et exporter des étiquettes

Découvrez comment créer et exécuter des projets d’étiquetage des données de façon à étiqueter des images dans Azure Machine Learning.  Utilisez l’étiquetage des données assisté par Machine Learning ou l’étiquetage humain dans la boucle, pour faciliter la tâche.

Configurez des étiquettes pour la classification, la détection d’objets (cadre englobant) ou la segmentation d’instances (polygone).

Vous pouvez également utiliser l’outil d’étiquetage des données pour [créer un projet d’étiquetage de texte](how-to-create-text-labeling-projects.md).

## <a name="image-labeling-capabilities"></a>Fonctionnalités d’étiquetage des images

L’étiquetage des données Azure Machine Learning est un emplacement central pour créer, gérer et superviser les projets d’étiquetage des données :

- Coordonnez les données, les étiquettes et les membres de l’équipe pour gérer efficacement les tâches d’étiquetage.
- Suivez la progression et gérez la file d’attente des tâches d’étiquetage incomplètes.
- Démarrez et arrêtez le projet pour contrôler la progression de l’étiquetage.
- Passez en revue les données étiquetées et exportez-les en tant que jeu de données Azure Machine Learning.

> [!Important]
> Les images de données doivent être des fichiers disponibles dans un magasin de données d’objets blob Azure. (Si vous ne disposez pas d’un magasin de données, vous pouvez charger des fichiers pendant la création du projet.)

Les données image peuvent être des fichiers des types suivants : .jpg, .jpeg, .png, .jpe, .jfif, .bmp, .tif, .tiff, .dcm, .dicom Chaque fichier est un élément à étiqueter.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-an-image-labeling-project"></a>Créer un projet d’étiquetage d’images

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Pour créer un projet, sélectionnez **Ajouter un projet**. Donnez au projet un nom approprié. Le nom du projet ne peut pas être réutilisé, même si le projet est supprimé ultérieurement.

1. Sélectionnez **Image** pour créer un projet d’étiquetage d’image.

    :::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Création d’un projet d’étiquetage d’image":::

    * Choisissez **Classification d'images (plusieurs classes)** pour les projets où vous souhaitez appliquer uniquement une *seule étiquette* d’un ensemble d’étiquettes à une image.
    * Choisissez **Classification d’images (plusieurs étiquettes)** pour les projets où vous souhaitez appliquer *une ou plusieurs* étiquettes d’un ensemble d’étiquettes à une image. Par exemple, vous pouvez étiqueter la photo d’un chien avec *dog* (chien) et *daytime* (jour).
    * Choisissez **Identification d’objet (cadre englobant)** pour les projets où vous souhaitez affecter une étiquette et un cadre englobant à chaque objet d’une image.
    * Choisissez **Segmentation d’instance (polygone)** pour les projets quand vous voulez attribuer une étiquette et tracer un polygone autour de chaque objet dans une image.

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
1. Le **type de jeu de données** est défini sur fichier. Seuls les types de jeu de données de fichier sont pris en charge pour les images.
1. Sélectionnez le magasin de données.
1. Si vos données se trouvent dans un sous-dossier de votre Stockage Blob, choisissez **Parcourir** pour sélectionner le chemin.
    * Ajoutez « /** » au chemin pour inclure tous les fichiers des sous-dossiers du chemin sélectionné.
    * Ajoutez « * */* .* » pour inclure toutes les données figurant dans le conteneur actuel et ses sous-dossiers.
1. (Facultatif) Fournissez une description pour votre jeu de données.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

### <a name="create-a-dataset-from-uploaded-data"></a>Créer un jeu de données à partir des données chargées

Pour charger directement vos données :

1. Sélectionnez **Créer un jeu de données** > **À partir de fichiers locaux**.
1. Affectez un **Nom** à votre jeu de données.
1. Le **type de jeu de données** est défini sur fichier. Seuls les types de jeu de données de fichier sont pris en charge pour les images.
1. (Facultatif) Fournissez une description pour votre jeu de données.
1. Sélectionnez **Suivant**.
1. (Facultatif) Sélectionnez ou créez un magasin de données. Ou conservez la sélection par défaut pour charger dans le magasin d’objets blob par défaut (« workspaceblobstore ») de votre espace de travail Machine Learning.
1. Sélectionnez **Parcourir** pour choisir les fichiers ou dossiers locaux à charger.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Configurer l’actualisation incrémentielle

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Spécifier des classes d’étiquettes

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-image-labeling-task"></a>Décrire la tâche d’étiquetage d’image

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

Pour les cadres englobants, les questions importantes sont les suivantes :

* Comment le rectangle englobant est-il défini pour cette tâche ? Doivent-ils être entièrement à l’intérieur des objets, ou doivent-il être à l’extérieur ? Doivent-ils être rognés le plus fidèlement possible, ou une certaine approximation est-elle acceptable ?
* Quel niveau de soin et de cohérence attendez-vous des étiqueteurs dans la définition des cadres englobants ?
* Comment étiqueter l’objet qui est partiellement affiché dans l’image ? 
* Comment étiqueter l’objet qui est partiellement recouvert par un autre objet ?

>[!NOTE]
> Notez bien que les étiqueteurs peuvent sélectionner les 9 premières étiquettes à l’aide des touches numériques allant de 1 à 9.

## <a name="use-ml-assisted-data-labeling"></a>Utiliser l’étiquetage des données assisté par ML

La page **Étiquetage assisté par ML** vous permet de déclencher des modèles Machine Learning automatiques pour accélérer les tâches d’étiquetage. Les images médicales (« .dcm ») ne sont pas incluses dans l’étiquetage assisté.

Au début de votre projet d’étiquetage, les éléments sont mélangés dans un ordre aléatoire pour réduire le biais potentiel. Cependant, le biais éventuellement présent dans le jeu de données se reflète dans le modèle entraîné. Par exemple, si 80 % de vos éléments appartiennent à une même classe, environ 80 % des données utilisées pour l’entraînement du modèle feront partie de cette classe.

Sélectionnez *Activer l’étiquetage assisté par ML* et spécifiez un GPU pour activer l’étiquetage assisté. Si votre espace de travail n’en contient pas, un cluster du GPU est créé pour vous et ajouté à votre espace de travail.   Le cluster est créé avec un minimum de 0 nœud, ce qui signifie qu’il ne coûte rien quand il n’est pas utilisé.


L’étiquetage ML est constitué de deux phases :

* Clustering
* Préétiquetage

Le nombre exact de données étiquetées nécessaires au démarrage de l’étiquetage assisté n’est pas un nombre fixe.  Il peut varier considérablement d’un projet d’étiquetage à un autre. Pour certains projets, il est parfois possible de voir des tâches de préétiquetage ou de clustering après l’étiquetage manuel de 300 éléments. L’étiquetage assisté par ML s’appuie sur une technique appelée *apprentissage par transfert* (ou « Transfer Learning »), qui utilise un modèle préentraîné pour accélérer le processus d’entraînement. Si les classes de votre jeu de données sont similaires à celles du modèle préentraîné, les préétiquettes peuvent être disponibles après seulement quelques centaines d’éléments étiquetés manuellement. Si votre jeu de données est très différent des données utilisées pour préentraîner le modèle, cela peut prendre beaucoup plus de temps.

Sachant que les étiquettes finales dépendent encore de l’entrée de l’étiqueteur, cette technologie d’étiquetage est parfois appelée *Opérateur humain dans la boucle*.

> [!NOTE]
> L’étiquetage des données assisté par ML ne prend pas en charge les comptes de stockage par défaut sécurisés derrière un [réseau virtuel](how-to-network-security-overview.md). Vous devez utiliser un compte de stockage autre que celui par défaut pour l’étiquetage des données assisté par ML. Le compte de stockage autre que celui par défaut peut être sécurisé derrière le réseau virtuel.

### <a name="clustering"></a>Clustering

Après l’envoi d’un certain nombre d’étiquettes, le modèle Machine Learning pour la classification commence à regrouper les éléments similaires.  Ces images similaires sont présentées aux étiqueteurs dans le même écran pour accélérer le balisage manuel. Le clustering est particulièrement utile quand l’étiqueteur affiche une grille de 4, 6 ou 9 images.

Une fois qu’un modèle Machine Learning a été entraîné sur vos données étiquetées manuellement, le modèle est tronqué à sa dernière couche entièrement connectée. Les images non étiquetées transitent ensuite par le modèle tronqué dans un processus appelé « incorporation » ou « featurization ». Chaque image est ainsi incorporée dans un espace hautement dimensionnel défini par cette couche de modèle. Les images qui correspondent aux voisins les plus proches dans l’espace sont utilisées pour les tâches de clustering. 

La phase de clustering n’apparaît pas pour les modèles de détection d’objets ni pour la classification de texte.

### <a name="prelabeling"></a>Préétiquetage

Une fois qu’un nombre suffisant d’étiquettes a été envoyé, un modèle de classification est utilisé pour prédire les étiquettes. Ou un modèle de détection d’objets est utilisé pour prédire les cadres englobants. L’étiqueteur voit dès lors les pages qui contiennent les étiquettes prédites déjà présentes dans chaque élément. Pour la détection d’objets, des zones prédites sont également affichées. La tâche doit ensuite examiner ces prédictions et corriger les images mal étiquetées avant d’envoyer la page.  

Une fois qu’un modèle Machine Learning a été entraîné sur vos données étiquetées manuellement, le modèle est évalué sur un ensemble d’éléments de test étiquetés manuellement pour déterminer sa précision à plusieurs seuils de confiance. Ce processus d’évaluation sert à déterminer le seuil de confiance au-dessus duquel le modèle est suffisamment précis pour afficher des préétiquettes. Le modèle est ensuite évalué par rapport aux données non étiquetées. Les éléments dont les prédictions ont un niveau de confiance supérieur à ce seuil sont utilisés pour le préétiquetage.

## <a name="initialize-the-image-labeling-project"></a>Initialiser le projet d’étiquetage d’image

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Exécuter et surveiller le projet

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>tableau de bord

L’onglet **Tableau de bord** affiche la progression de la tâche d’étiquetage.

:::image type="content" source="./media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Tableau de bord d’étiquetage des données":::

Le graphique de progression indique le nombre d’éléments étiquetés, ignorés ou non encore terminés.  Pointez sur le graphique pour afficher le nombre d’éléments dans chaque section.

La section centrale affiche la file d’attente des tâches à affecter. Lorsque l’étiquetage assisté par ML est désactivé, cette section indique le nombre de tâches manuelles à affecter. Lorsque l’étiquetage assisté par ML est activé, les éléments suivants s’affichent également :

* Tâches contenant des éléments en cluster dans la file d’attente
* Tâches contenant des éléments pré-étiquetés dans la file d’attente

En outre, lorsque l’étiquetage assisté par ML est activé, une petite barre de progression s’affiche lors de la prochaine exécution de l’apprentissage.  Les sections Expériences fournissent des liens pour chaque exécution Machine Learning.

* Apprentissage : effectue l’apprentissage d’un modèle pour prédire les étiquettes
* Validation : détermine si la prédiction de ce modèle est utilisée pour l’étiquetage préalable des éléments 
* Inférence : exécution de prédiction pour les nouveaux éléments
* Caractérisation : éléments de clusters (uniquement pour les projets de classification d’images)

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
* Modifier les détails de l’étiquetage assisté par ML, notamment l’activation/la désactivation

### <a name="access-for-labelers"></a>Accès pour les étiqueteurs

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Ajouter une nouvelle classe d’étiquette à un projet

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exporter les étiquettes

Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage. Vous pouvez exporter les données d’étiquette pour Machine Learning - Expérimentation à tout moment. 

* Vous pouvez exporter des étiquettes d’image sous forme de :
    * [Fichier au format COCO](http://cocodataset.org/#format-data) créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé dans *Labeling/export/coco*. 
    * [Jeu de données Azure Machine Learning comportant des étiquettes](how-to-use-labeled-dataset.md). 

Accédez aux jeux de données Azure Machine Learning exportés dans la section **Jeux de données** de Machine Learning. La page de détails du jeu de données fournit également un exemple de code pour accéder aux étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

Une fois que vous avez exporté vos données étiquetées vers un jeu de données Azure Machine Learning, vous pouvez utiliser AutoML pour créer des modèles de vision par ordinateur formés sur vos données étiquetées. En savoir plus : [Configurer l’AutoML pour effectuer l’apprentissage de modèles de vision par ordinateur avec Python (préversion)](how-to-auto-train-image-models.md)

## <a name="troubleshooting"></a>Résolution des problèmes

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]

### <a name="object-detection-troubleshooting"></a>Résolution de détection d’objets

|Problème  |Résolution  |
|---------|---------|
|Appuyer sur la touche Échap lors de l’étiquetage pour la détection d’objet crée une étiquette de taille zéro dans l’angle supérieur gauche. L’envoi d’étiquettes dans cet état échoue.     |   Pour supprimer l’étiquette, cliquez sur la croix en regard de celle-ci.  |

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Créer votre premier projet d’étiquetage de classification d’images](tutorial-labeling.md).
* [Comment étiqueter des images](how-to-label-data.md)
