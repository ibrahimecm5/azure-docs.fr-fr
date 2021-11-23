---
title: Mapper les données dans Azure Synapse Analytics | Microsoft Docs
description: Découvrez comment utiliser l’outil Mapper les données dans Azure Synapse Analytics.
author: joshuha-msft
ms.author: joowen
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: 2b8a06fae81cf4c9a032d25699e3d907b498f516
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404346"
---
# <a name="map-data-in-azure-synapse-analytics"></a>Mapper les données dans Azure Synapse Analytics


## <a name="what-is-the-map-data-tool"></a>Qu’est-ce que l’outil Mapper les données ?


L’outil Mapper les données est un processus guidé qui aide les utilisateurs à créer des mappages ETL et des flux de données de mappage depuis leurs données sources vers des tables de bases de données de lac Synapse sans écrire de code. Ce processus commence par le choix des tables de destination par l’utilisateur dans les bases de données de lac Synapse, puis se poursuit par leur mappage à leurs données sources dans ces tables. 

Pour plus d’informations sur les bases de données de lac Synapse, consultez [Vue d’ensemble des modèles de base de données Azure Synapse – Azure Synapse Analytics | Microsoft Docs](overview-database-templates.md)

L’outil Mapper les données offre une expérience guidée qui permet à l’utilisateur de générer un flux de données de mappage sans avoir à démarrer avec un canevas vide ni à générer rapidement un flux de données de mappage scalable et exécutable dans des pipelines Synapse.


> [!NOTE] 
> La fonctionnalité Mapper les données dans les pipelines Synapse Analytics est actuellement en préversion publique.

## <a name="getting-started"></a>Prise en main

L’outil Mapper les données se démarre à partir de l’expérience de base de données de lac Synapse. À partir de là, vous pouvez sélectionner l’outil Mapper les données pour commencer le processus. 

![Capture d’écran montrant comment ouvrir l’outil Mapper les données](./media/overview-map-data/open-map-data.png)


L’outil Mapper les données a besoin du calcul disponible pour aider les utilisateurs à prévisualiser des données et à lire le schéma de leurs fichiers sources. Lors de la première utilisation de l’outil Mapper les données dans une session, vous avez besoin d’initialiser un cluster.
![Capture d’écran montrant les clusters de débogage](./media/overview-map-data/debug-map-data.png)

Pour commencer, choisissez la source de données à mapper à vos tables de base de données de lac. Les sources de données actuellement prises en charge sont les bases de données de lac Azure Data Lake Storage Gen 2 et Synapse.
![Capture d’écran montrant les sources](./media/overview-map-data/sources-map-data.png)

### <a name="file-type-options"></a>Options de type de fichier
Quand vous choisissez un magasin de fichiers comme Azure Data Lake Storage Gen 2, les types de fichiers suivants sont pris en charge :

* Common Data Model
* Texte délimité
* Parquet


## <a name="create-data-mapping"></a>Créer un mappage de données
Nommez votre mappage de données et sélectionnez la destination de la base de données de lac Synapse.
![Capture d’écran montrant le nommage et la destination](./media/overview-map-data/destination-map-data.png)

## <a name="source-to-target-mapping"></a>Mappage de la source à la cible
Choisissez une table source principale à mapper à la table de destination de la base de données de lac Synapse.
![Capture d’écran montrant les règles de l’outil Mapper les données](./media/overview-map-data/rules-map-data.png)

### <a name="new-mapping"></a>Nouveau mappage
Utilisez le bouton Nouveau mappage pour ajouter une méthode de mappage permettant de créer un mappage ou une transformation.

### <a name="additional-source"></a>Source supplémentaire
Utilisez le bouton Source supplémentaire pour rejoindre et ajouter une autre source à votre mappage.

### <a name="preview-data"></a>Aperçu des données
L’onglet **Aperçu des données**  vous donne une capture instantanée interactive des données de chaque transformation. Pour en savoir plus, consultez [Aperçu des données en mode de débogage](../../data-factory/concepts-data-flow-debug-mode.md#data-preview).

### <a name="mapping-methods"></a>Méthodes de mappage

Les méthodes de mappage suivantes sont prises en charge :

* [Direct](../../data-factory/data-flow-select.md)
* [Clé de substitution](../../data-factory/data-flow-surrogate-key.md)
* [Lookup](../../data-factory/data-flow-lookup.md)
* [Supprimer le tableau croisé dynamique](../../data-factory/data-flow-unpivot.md)
* [Agrégat](../../data-factory/data-flow-aggregate.md)
    * Sum
    * Minimum
    * Maximale
    * Premier
    * Dernier
    * Écart type
    * Moyenne
    * Moyenne
* [Colonne dérivée](../../data-factory/data-flow-derived-column.md)
    * SupprEspace
    * Upper
    * Moins grand
    * Avancé 


## <a name="create-pipeline"></a>Création d’un pipeline

Une fois que vous avez terminé vos transformations avec l’outil Mapper les données, sélectionnez le bouton Créer un pipeline pour générer un flux de données de mappage et un pipeline afin de déboguer et exécuter votre transformation.