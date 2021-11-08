---
title: Créer une base de données de lac vide
description: Découvrez comment créer dans Azure Synapse Analytics une base de données de lac vide à laquelle il est facile d’effectuer des ajouts.
author: aamerril
ms.author: aamerril
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9612fabc94e3425c42bb30e3075a56fb3f789626
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510451"
---
# <a name="how-to-create-an-empty-lake-database"></a>Guide pratique : Créer une base de données de lac vide

Cet article explique comment créer une [base de données de lac](./concepts-lake-database.md) vide dans Azure Synapse Analytics à l’aide du concepteur de base de données. Le concepteur de bases de données vous permet de créer et déployer facilement une base de données sans écrire de code. 

## <a name="prerequisites"></a>Prérequis

- Au minimum, des autorisations de rôle utilisateur Synapse sont requises pour explorer un modèle de base de données de lac à partir de la Galerie.
- Des autorisations d’administrateur, de contributeur ou d’éditeur d’artefact Synapse sont requises sur l’espace de travail Synapse pour créer une base de données de lac.
- Des autorisations de contributeur de données de blob de stockage sont requises sur le lac de données.

## <a name="create-lake-database-from-database-template"></a>Créer un base de données de lac à partir d’un modèle de base de données
1. Dans le hub **Accueil** de l’espace de travail Azure Synapse Analytics, sélectionnez l’onglet **Données** à gauche. L’onglet **Données** s’ouvre, affichant la liste des bases de données figurant dans votre espace de travail.
2. Pointez sur le bouton **+** et sélectionnez-le, puis choisissez **Base de données de lac (préversion)** .
![Capture d’écran montrant la création d’une base de données de lac vide](./media/create-empty-lake-database/create-empty-lakedb.png)
3. L’onglet du concepteur de base de données s’ouvre, affichant une base de données vide.
4. Le concepteur de base de données comprend des **Propriétés** à droite, qui doivent être configurées.
    - **Nom** : donnez un nom à votre base de données. Les noms ne pouvant pas être modifiés après la publication de la base de données, veillez à choisir le nom correct.
    - **Description** : l’attribution d’une description à votre base de données est facultative, mais elle permet aux utilisateurs de comprendre l’objectif de la base de données.
    - **Paramètres de stockage pour la base de données** : cette section contient les informations de stockage par défaut pour les tables de la base de données. Cette valeur par défaut est appliquée à chaque table de la base de données, sauf si elle est remplacée sur la table.
    - **Service lié** : service lié par défaut utilisé pour stocker vos données dans Azure Data Lake Storage.  Le **service lié** par défaut associé à l’espace de travail Synapse s’affiche, mais vous pouvez le remplacer par n’importe quel compte de stockage ADLS de votre choix. 
    - **Dossier d’entrée** : utilisé pour définir le chemin d’accès par défaut du conteneur et du dossier au sein de ce service lié à l’aide de l’explorateur de fichiers.
    - **Format de données** : les bases de données de lac dans Azure Synapse prennent en charge les formats de fichier Parquet et de texte délimité comme formats de stockage pour les données.

> [!NOTE]
> Vous pouvez toujours remplacer les paramètres de stockage par défaut sur une table par la base de la table, et la valeur par défaut reste personnalisable. Si vous n’êtes pas sûr de votre choix, vous pouvez y revenir ultérieurement.
 
5. Pour ajouter une table à la base de données, cliquez sur le bouton **+ Table**. 
    - **Personnalisée** Ajoute une nouvelle table au canevas.
    - **À partir d’un modèle** Ouvre la galerie et vous permet de sélectionner un modèle de base de données à utiliser lors de l’ajout d’une nouvelle table. Pour plus d’informations, consultez [Créer une base de données de lac à partir d’un modèle de base de données](./create-lake-database-from-lake-database-templates.md).
    - **À partir d’un lac de données** Vous permet d’importer un schéma de table en utilisant des données figurant déjà dans votre lac.
6. Sélectionnez **Personnalisé**. Une nouvelle table s’affiche sur le canevas, nommée Table_1.
7. Vous pouvez ensuite personnaliser la table Table_1 en modifiant son nom, sa description, ses paramètres de stockage, ses colonnes et ses relations. Pour plus d’informations, consultez [Modifier une base de données de lac](./modify-lake-database.md).
8. Ajoutez une nouvelle table à partir du lac de données en sélectionnant **+ Table**, puis **À partir d’un lac de données**.
9. Le volet **Créer une table externe à partir d’un lac de données** s’affiche. Remplissez le volet avec les détails ci-dessous, puis sélectionnez **Continuer**.
    - **Nom de la table externe** Nom que vous voulez attribuer à la table que vous êtes en train de créer.
    - **Service lié** Service lié contenant l’emplacement Azure Data Lake Storage dans lequel se trouve votre fichier de données.
    - **Fichier ou dossier d’entrée** Utilisez l’explorateur de fichiers pour accéder à un fichier de votre lac à l’aide duquel vous souhaitez créer une table, et le sélectionner.
![Capture d’écran montrant les options du volet Créez une table externe à partir d’un lac de données](./media/create-empty-lake-database/create-from-lake.png)
    - Dans l’écran suivant, Azure Synapse affiche un aperçu du fichier et détecte le schéma.
    - Vous êtes dirigé vers la page **Nouvelle table externe** dans laquelle vous pouvez mettre à jour tous les paramètres liés au format de données et afficher un **Aperçu des données** pour vérifier si Azure Synapse a identifié le fichier correctement.
    - Lorsque vous êtes satisfait des paramètres, sélectionnez **Créer**.
    - Une nouvelle table portant le nom que vous avez sélectionné est ajoutée au canevas, et la section **Paramètres de stockage pour la table** affiche le fichier que vous avez spécifié.
    
10. Avec la base de données personnalisée, il est maintenant temps de le publier. Si vous utilisez une intégration Git avec votre espace de travail Synapse, vous devez valider vos modifications et les fusionner dans la branche Collaboration. [En savoir plus sur le contrôle de code source dans Azure Synapse](././cicd/../../cicd/source-control.md). Si vous utilisez le mode dynamique Synapse, vous pouvez sélectionner « publier ».
    - Les erreurs sont validées dans votre base de données avant sa publication. Toutes les erreurs détectées s’affichent sous l’onglet Notifications, avec des instructions sur la façon de les corriger.
    
       ![Capture d’écran du volet de validation montrant des erreurs de validation dans la base de données](./media/create-empty-lake-database/validation-error.png)
    - La publication a pour effet de créer votre schéma de base de données dans le metastore Azure Synapse. Après publication, la base de données et les objets de table sont visibles par d’autres services Azure, et autorisent la circulation des métadonnées de votre base de données dans des applications telles que Power BI ou Purview.

11. Vous avez maintenant créé une base de données de lac vide dans Azure Synapse, et y avez ajouté des tables à l’aide des options **Personnalisée** et **À partir du lac de données**.

## <a name="next-steps"></a>Étapes suivantes

Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous. 
- [Modifier une base de données de lac](./modify-lake-database.md)
- [En savoir plus sur les bases de données de lac](./concepts-lake-database.md)
- [Créer une base de données de lac à partir d’un modèle de base de données de lac](./create-lake-database-from-lake-database-templates.md)
