---
title: Transformation d’union dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez-en davantage sur la transformation Nouvelle branche du flux de données de mappage dans les pipelines Azure Data Factory et Synapse Analytics
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 14cf10c51ffe470ff249ff4953f0edd8990aab1b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059679"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Transformation d’union dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

L'union combinera plusieurs flux de données en un, avec l'union SQL de ces flux en tant que nouvelle sortie de la transformation d’union. Tous les schémas de chaque flux d’entrée sont combinés à l’intérieur de votre flux de données, sans avoir à disposer d’une clé de jointure.

Vous pouvez combiner des n flux dans le tableau de paramètres en sélectionnant l’icône « + » en regard de chaque ligne configurée, notamment la source de données et des flux de transformations existantes dans votre flux de données.

Voici une brève vidéo illustrant la transformation d’union dans le flux de données de mappage :

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

:::image type="content" source="media/data-flow/union.png" alt-text="Transformation d’union":::

Dans ce cas, vous pouvez associer des métadonnées disparates provenant de plusieurs sources (dans cet exemple, trois fichiers sources différents) et les combiner en un seul flux :

:::image type="content" source="media/data-flow/union111.png" alt-text="Vue d’ensemble de la transformation d’union":::

Pour ce faire, ajoutez des lignes supplémentaires dans les paramètres d’union, en incluant toutes les sources que vous souhaitez ajouter. Il n’est pas nécessaire d’avoir une clé de recherche ou de jointure commune :

:::image type="content" source="media/data-flow/unionsettings.png" alt-text="Paramètres de transformation d’union":::

Si vous définissez une transformation Sélectionner après l’union, vous ne pourrez pas renommer des champs qui se chevauchent ou ceux qui n’étaient pas nommés à partir de sources sans en-tête. Cliquez sur « Inspecter » pour afficher les métadonnées combinées avec les 132 colonnes de cet exemple à partir de trois sources différentes :

:::image type="content" source="media/data-flow/union333.png" alt-text="Transformation d’union finale":::

## <a name="name-and-position"></a>Nom et position

Lorsque vous choisissez une « union par nom », chaque valeur de colonne est supprimée dans la colonne correspondante de chaque source, avec un nouveau schéma de métadonnées concaténées.

Si vous choisissez une « union par position », chaque valeur de colonne est supprimée à la position d’origine de chaque source correspondante, ce qui entraîne un nouveau flux combiné de données où les données de chaque source sont ajoutées au même flux :

:::image type="content" source="media/data-flow/unionoutput.png" alt-text="Sortie d’union":::

## <a name="next-steps"></a>Étapes suivantes

Explorez les transformations similaires, y compris [Joindre](data-flow-join.md) et [Existe](data-flow-exists.md).
