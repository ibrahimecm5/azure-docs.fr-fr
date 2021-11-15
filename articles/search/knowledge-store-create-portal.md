---
title: 'Démarrage rapide : Créer une base de connaissances dans le portail Azure'
titleSuffix: Azure Cognitive Search
description: Utilisez l’Assistant Importation de données pour créer une base de connaissances où conserver le contenu enrichi persistant. Connectez-vous à une base de connaissances pour l’analyse à partir d’autres applications, ou envoyez du contenu enrichi aux processus en aval.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/28/2021
ms.openlocfilehash: 6e9c09cb5407747c325b696570cd3eabdeeb4ed7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563717"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>Démarrage rapide : Créer une base de connaissances dans le portail Azure

[Base de connaissances](knowledge-store-concept-intro.md) est une fonctionnalité de Recherche cognitive Azure qui accepte la sortie d’un [pipeline d’enrichissement par IA](cognitive-search-concept-intro.md) et la rend disponible dans Stockage Azure pour les applications et les charges de travail en aval. Les enrichissements créés par le pipeline (tels que le texte traduit, le texte OCR, les images étiquetées et les entités reconnues) sont projetés dans des tables ou des blobs, où les applications ou charges de travail qui se connectent à Stockage Azure peuvent y accéder.

Dans ce guide de démarrage rapide, vous allez configurer vos données, puis exécuter l’Assistant **Importation de données** pour créer un pipeline d’enrichissement qui génère aussi une base de connaissances. La base de connaissances contient le contenu texte d’origine extrait de la source (évaluations d’un hôtel par les clients), plus le contenu généré par l’IA, qui comprend une étiquette de sentiment, l’extraction d’expressions clés et la traduction textuelle des commentaires des clients non francophones.

> [!NOTE]
> Ce guide de démarrage rapide vous montre le chemin le plus rapide vers une base de connaissances finalisée dans le Stockage Azure. Pour obtenir des explications plus détaillées de chaque étape, consultez plutôt [Créer une base de connaissances dans REST](knowledge-store-create-rest.md).

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide utilise les services suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre compte. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ Stockage Azure. [Créez un compte](../storage/common/storage-account-create.md) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Le type de compte doit être **StorageV2 (v2 universel)** .

+ Exemples de données. Ce démarrage rapide utilise des données d’évaluation d’hôtels enregistrées dans un fichier CSV (issues de Kaggle.com). Il rassemble 19 commentaires de clients relatifs à un seul hôtel.

  [Téléchargez le fichier HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D), puis [chargez-le dans un conteneur de blobs](../storage/blobs/storage-quickstart-blobs-portal.md) dans Stockage Azure.

Ce démarrage rapide utilise également [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’enrichissement par IA. Parce que la charge de travail est vraiment petite, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions. Cela signifie que vous pouvez effectuer cet exercice sans avoir à créer une ressource Cognitive Services supplémentaire.

## <a name="start-the-wizard"></a>Démarrer l’Assistant

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.

1. [Trouvez votre service de recherche](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Ensuite, dans la page Vue d’ensemble, cliquez sur **Importer des données** dans la barre de commandes pour créer une base de connaissances en quatre étapes.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Capture d’écran de la commande Importer des données" border="true":::

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

Comme les données sont constituées de plusieurs lignes dans un fichier CSV, définissez le *mode d’analyse* pour obtenir un document de recherche pour chaque ligne.

1. Dans **Se connecter à vos données**, choisissez **Stockage Blob Azure**, puis sélectionnez le compte et le conteneur que vous avez créés. 

1. Pour le **nom**, entrez « hotel-reviews-ds ».

1. Pour **Mode d’analyse**, sélectionnez **Texte délimité**, puis cochez la case **La première ligne contient l’en-tête**. Vérifiez que le **Caractère délimiteur** est une virgule (,).

1. Dans **Chaîne de connexion**, collez une chaîne de connexion à votre compte Stockage Azure. 

   Une chaîne de connexion a le format suivant : `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Dans **Conteneurs**, entrez le nom du conteneur de blobs contenant les données (« hotel-reviews »).

    Votre page doit ressembler à la capture d’écran suivante.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="Capture d’écran de la définition de la source de données" border="true":::

1. Passez à la page suivante.

### <a name="step-2-add-skills"></a>Étape 2 : Ajouter des compétences

Dans cette étape de l’Assistant, ajoutez des compétences pour l’enrichissement par IA. Les données sources sont constituées des évaluations des clients en anglais et en français. Les compétences pertinentes pour ce jeu de données incluent l’extraction d’expressions clés, la détection de sentiments et la traduction de texte. Dans une étape ultérieure, ces enrichissements seront « projetés » dans une base de connaissances en tant que tables Azure.

1. Développez **Attacher Cognitive Services**. **Gratuit (enrichissements limités)** est sélectionné par défaut. Vous pouvez utiliser cette ressource, car le nombre d’enregistrements dans HotelReviews-Free.csv est de 19, et cette ressource gratuite autorise jusqu’à 20 transactions par jour.

1. Développez **Ajouter des enrichissements**.

1. Pour **Nom de l’ensemble de compétences**, entrez « hotel-reviews-ss ».

1. Pour **Champ de données source**, sélectionnez **reviews_text**.

1. Pour **Niveau de précision d’enrichissement**, sélectionnez **Pages (segments de 5 000 caractères)** .

1. Pour **Compétences cognitives de texte**, sélectionnez les compétences suivantes :

    + **Extraire des expressions clés**
    + **Traduire le texte**
    + **Détection de la langue**
    + **Détecter le sentiment**

   Votre page doit ressembler à la capture d’écran suivante :

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="Capture d’écran de la définition des ensembles de compétences" border="true":::

1. Faites défiler vers le bas et développez **Enregistrer les enrichissements dans une base de connaissances**.

1. Sélectionnez **Choisir une connexion existante**, puis sélectionnez un compte Stockage Azure. La page Conteneurs s’affiche pour vous permettre de créer un conteneur pour les projections. Nous vous recommandons d’adopter une convention d’affectation de noms avec un préfixe, telle que « kstore-hotel-reviews », afin de faire la distinction entre le contenu source et le contenu de la base de connaissances.

1. En revenant à l’Assistant d’importation de données, sélectionnez les **projections de table Azure** suivantes. L’Assistant propose toujours la projection **Documents**. D’autres projections seront proposées en fonction des compétences que vous sélectionnez (comme **Expressions clés**) ou de la granularité d’enrichissement (**Pages**) :

    + **Documents**
    + **Pages**
    + **Phrases clés**

   La capture d’écran suivante montre les sélections de projections de table dans l’Assistant.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="Capture d’écran de la définition de la base de connaissances" border="true":::

1. Passez à la page suivante.

### <a name="step-3-configure-the-index"></a>Étape 3 : Configurer l’index

Dans cette étape de l’Assistant, configurez un index pour d’éventuelles requêtes de recherche en texte intégral. L’Assistant va échantillonner votre source de données pour en déduire des champs et des types de données. Il vous suffit de sélectionner les attributs correspondant au comportement souhaité. Par exemple, l’attribut **Récupérable** permet au service de recherche de retourner une valeur de champ, alors que l’attribut **Possibilité de recherche** active la recherche en texte intégral sur le champ.

1. Pour **Nom d’index**, entrez « hotel-reviews-idx ».

1. Pour les attributs, acceptez les sélections par défaut : **Récupérable** et **Possibilité de recherche** pour les champs que le pipeline crée.

    Votre index doit ressembler à l’image suivante. Dans la mesure où la liste est longue, tous les champs ne sont pas visibles dans l’image.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="Capture d’écran de la définition de l’index" border="true":::

1. Passez à la page suivante.

### <a name="step-4-configure-and-run-the-indexer"></a>Étape 4 : Configurer et exécuter l’indexeur

Dans cette étape de l’Assistant, configurez un indexeur qui doit rassembler la source de données, l’ensemble de compétences et l’index que vous avez définis dans les étapes précédentes de l’Assistant.

1. Pour **Nom**, entrez « hotel-reviews-idxr ».

1. Pour **Planification**, conservez la valeur par défaut **Une fois**.

1. Cliquez sur **Envoyer** pour exécuter l’indexeur. Les opérations d’extraction de données, d’indexation et d’application des compétences cognitives se produisent toutes à cette étape.

### <a name="step-5-check-status"></a>Étape 5 : Vérifier l’état

Dans la page **Vue d’ensemble**, ouvrez l’onglet **Indexeurs** au milieu de la page, puis sélectionnez **hotels-reviews-idxr**. Au bout d’une ou deux minutes, l’état doit passer de « En cours » à « Réussite », sans aucune erreur ni avertissement.

## <a name="check-tables-in-storage-browser"></a>Vérifier les tables dans Navigateur de stockage

Dans le portail Azure, basculez sur votre compte de stockage Azure et utilisez **Navigateur de stockage** pour visualiser les nouvelles tables. Vous devez voir trois tables, une pour chaque projection proposée dans la section « Enregistrer les enrichissements » de la page « Ajouter des enrichissements ».

+ « hotelReviewssDocuments » contient tous les nœuds de premier niveau de l’arborescence d’enrichissement d’un document, qui ne sont pas des collections. 

+ « hotelReviewssKeyPhrases » contient une longue liste des expressions clés extraites de toutes les évaluations. La sortie des compétences qui génèrent en sortie des collections (tableaux), comme les expressions clés et les entités, est envoyée vers une table autonome.

+ « hotelReviewssPages » contient des champs enrichis créés sur chaque page ayant été séparée du document. Dans cet ensemble de compétences et cette source de données, les enrichissements au niveau des pages sont constitués d’étiquettes de sentiment et de texte traduit. Une table de pages (ou une table de phrases si vous spécifiez ce niveau particulier de granularité) est créée lorsque vous choisissez la granularité « pages » dans la définition de l’ensemble de compétences. 

Toutes ces tables contiennent des colonnes d’ID pour prendre en charge les relations entre tables dans d’autres outils et applications. Quand vous ouvrez une table, faites défiler au-delà de ces champs pour voir les champs de contenu ajoutés par le pipeline.

Dans ce démarrage rapide, la table pour « hotelReviewssPages » doit ressembler à la capture d’écran suivante :

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Capture d’écran des tables générées dans Navigateur de stockage" border="true":::

## <a name="clean-up"></a>Nettoyage

Lorsque vous travaillez dans votre propre abonnement, il est judicieux à la fin d’un projet de déterminer si vous avez encore besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite.

> [!TIP]
> Si vous voulez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur **hotel-reviews-idxr** et les objets associés pour les recréer. La suppression de l’indexeur remet le compteur de transactions quotidiennes gratuites à zéro.

## <a name="next-steps"></a>Étapes suivantes

Maintenant qu’une base de données vous a été présentée, examinez plus en détail chaque étape en passant à la procédure pas à pas de l’API REST. Les tâches gérées en interne par l’Assistant sont expliquées dans la procédure pas à pas REST.

> [!div class="nextstepaction"]
> [Créer une base de connaissances à l’aide de REST et Postman](knowledge-store-create-rest.md)
