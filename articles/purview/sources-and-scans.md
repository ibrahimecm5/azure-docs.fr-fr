---
title: Sources de données et types de fichiers pris en charge
description: Cet article fournit des informations conceptuelles relatives aux sources de données et types de fichiers pris en charge dans Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: b29896a9ca047b05c3313b716b2a4df1b0e72f6c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517175"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Sources de données et types de fichiers pris en charge dans Azure Purview

Cet article traite des sources de données et types de fichiers pris en charge, ainsi que des concepts d’analyse dans Purview.

## <a name="supported-data-sources"></a>Sources de données prises en charge

Purview prend en charge toutes les sources de données répertoriées [ici](purview-connector-overview.md).

## <a name="file-types-supported-for-scanning"></a>Types de fichiers pris en charge pour l’analyse

Les types de fichiers suivants sont pris en charge pour l’analyse, ainsi que pour l’extraction et la classification des schémas, le cas échéant :

- Formats de fichiers structurés pris en charge par l’extension : AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
 > [!Note]
 > * L’analyseur Purview prend uniquement en charge l’extraction de schémas pour les types de fichiers structurés listés ci-dessus.
 > * Pour les types de fichiers AVRO, ORC et PARQUET, l’analyseur Purview ne prend pas en charge l’extraction de schémas pour les fichiers contenant des types de données complexes (par exemple MAP, LIST, STRUCT). 
 > * L’analyseur Purview prend en charge l’analyse des types PARQUET compressés avec Snappy pour l’extraction et la classification des schémas. 
 > * Pour les types de fichiers GZIP, le fichier GZIP doit être mappé à un seul fichier CSV. 
 > Les fichiers Gzip sont soumis à des règles système et personnalisées de classification. Actuellement, nous ne prenons pas en charge l’analyse d’un fichier gzip mappé à plusieurs fichiers contenus, ou à n’importe quel autre type de fichier qui n’est pas csv. 
- Formats de fichiers de document pris en charge par l’extension : DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Purview prend également en charge les extensions de fichiers personnalisées et les analyseurs personnalisés.

## <a name="sampling-within-a-file"></a>Échantillonnage dans un fichier

Dans la terminologie Purview,
- Analyse L1 : Extrait des informations et des métadonnées de base, telles que le nom de fichier, la taille et le nom complet
- Analyse L2 : Extrait le schéma pour les types de fichiers structurés et les tables de base de données
- Analyse L3 : Extrait le schéma, le cas échéant, et soumet le fichier échantillonné au système et aux règles de classification personnalisées

Pour tous les formats de fichiers structurés, l’analyseur Purview échantillonne les fichiers comme suit :

- Pour les types de fichiers structurés, il échantillonne les 128 premières lignes dans chaque colonne ou le premier Mo, selon ce qui est le plus petit.
- Pour les formats de fichiers de document, il échantillonne les 20 premiers Mo de chaque fichier.
    - Un fichier de document dont la taille est supérieure à 20 Mo n’est pas soumis à une analyse détaillée (sous réserve de classification). Dans ce cas, Purview capture uniquement les métadonnées de base, telles que le nom de fichier et le nom complet.
- Pour les **sources de données tabulaires (SQL, CosmosDB)** , il échantillonne les 128 premières lignes. 

## <a name="resource-set-file-sampling"></a>Échantillonnage de fichiers de jeu de ressources

Dans Purview, un dossier ou un groupe de fichiers de partition est détecté en tant qu’*ensemble de ressources* s’il correspond à une stratégie d’ensemble de ressources système ou à une stratégie d’ensemble de ressources définie par le client. Si un ensemble de ressources est détecté, Purview échantillonne chaque dossier qu’il contient. Apprenez-en davantage sur les ensembles de ressources [ici](concept-resource-sets.md).

Échantillonnage de fichiers pour les ensembles de ressources par types de fichiers :

- **Fichiers délimités (CSV, PSV, SSV, TSV)** - 1 fichier sur 100 est échantillonné (analyse L3) dans un dossier ou groupe de fichiers de partition considérés comme « ensemble de ressources »
- **Types de fichiers Data Lake (Parquet, Avro, Orc)** - 1 fichier des 18446744073709551615 (longueur maximale) est échantillonné (analyse L3) dans un dossier ou groupe de fichiers de partition considérés comme *ensemble de ressources*
- **Autres types de fichiers structurés (JSON, XML, TXT)** - 1 fichier sur 100 est échantillonné (analyse L3) dans un dossier ou groupe de fichiers de partition considérés comme « ensemble de ressources »
- **Objets SQL et entités CosmosDB** - chaque fichier est soumis à une analyse L3.
- **Types de fichiers de document** - chaque fichier est soumis à un analyse L3. Les modèles d’ensembles de ressources ne s’appliquent pas à ces types de fichiers.

## <a name="classification"></a>classification ;

Toutes les 206 règles de classification système s’appliquent aux formats de fichiers structurés. Seules les règles de classification MCE s’appliquent aux types de fichiers de document (et non aux modèles regex natifs d’analyse des données, de détection basée sur les filtres). Pour plus d’informations sur les classifications prises en charge, consultez [Classifications prises en charge dans Azure Purview](supported-classifications.md).

## <a name="next-steps"></a>Étapes suivantes

- [Analyses et ingestion dans Purview](concept-scans-and-ingestion.md)
- [Gérer des sources de données dans Azure Purview](manage-data-sources.md)
