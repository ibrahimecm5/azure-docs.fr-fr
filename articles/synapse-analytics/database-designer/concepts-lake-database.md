---
title: Concepts de base de données de lac Azure Synapse
description: Apprenez-en davantage sur le concept de base de données de lac et la façon dont il aide à structurer les données.
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: b0c288fe2f7b6ac9bf6c0a2a636131a1d89ffe5d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097079"
---
# <a name="lake-database"></a>Base de données de lac

La base de données de lac dans Azure Synapse Analytics permet aux clients de réunir la conception de la base de données et les méta-informations sur les données qui y sont stockées, et offre la possibilité de décrire comment et où les données doivent être stockées. Une base de données de lac relève le défi des lacs de données d’aujourd’hui où il est difficile de comprendre la manière dont les données sont structurées.  

![Vue d’ensemble d’une base de données de lac](./media/concepts-lake-database/lake-database-overview.png)


## <a name="database-designer"></a>Concepteur de bases de données

Le nouveau concepteur de base de données vous donne la possibilité de créer un modèle de données pour votre base de données de lac et d’y ajouter des informations. Chaque entité et attribut peut être décrit pour fournir des informations supplémentaires avec le modèle qui ne contient pas seulement des entités mais aussi des relations. En particulier, l’absence de relations de modèle est un défi pour l’interaction sur le lac de données. Ce défi est désormais relevé avec un concepteur intégré offrant des possibilités qui étaient disponibles dans les bases de données, mais pas sur le lac. En outre, la possibilité d’ajouter des descriptions et des valeurs de démonstration possibles au modèle permet aux utilisateurs qui interagissent avec celui-ci d’obtenir des informations là où ils en ont besoin pour mieux comprendre les données. 

## <a name="data-storage"></a>Stockage des données 

Les bases de données de lac utilisent un lac de données sur le compte Stockage Azure pour stocker les données de la base de données. Les données peuvent être stockées dans le fichier Parquet au format CSV, et différents paramètres peuvent être utilisés pour optimiser le stockage. Chaque base de données de lac utilise un service lié pour définir l’emplacement du dossier de données racine. Pour chaque entité, des dossiers distincts sont créés par défaut à l’intérieur de ce dossier de base de données sur le lac de données. Par défaut, toutes les tables à l’intérieur d’une base de données de lac utilisent le même format, mais les formats et l’emplacement des données peuvent être modifiés par entité si cela est demandé. 


## <a name="database-compute"></a>Calcul de base de données

La base de données de lac est exposée dans un pool SQL serverless Synapse SQL et Apache Spark, offrant aux utilisateurs la possibilité de dissocier le stockage du calcul. Les métadonnées associées à la base de données de lac facilitent pour différent moteurs de calcul, non seulement la fourniture d’une expérience intégrée, mais aussi l’utilisation d’informations supplémentaires (par exemple, des relations) qui n’étaient pas prises en charge à l’origine sur le lac de données. 

## <a name="next-steps"></a>Étapes suivantes

Continuez à explorer les fonctionnalités du concepteur de base de données en suivant les liens ci-dessous.
- [Démarrage rapide de la création d’une base de données de lac](quick-start-create-lake-database.md)
- [Concept de modèles de base de données](concepts-database-templates.md)
