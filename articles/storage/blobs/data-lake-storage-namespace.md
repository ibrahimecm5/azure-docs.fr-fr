---
title: Espace de noms hiérarchique d’Azure Data Lake Storage Gen2
description: Décrit le concept d’espace de noms hiérarchique pour Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 38a5298bcbefcacdd6869eba75d8accec207c821
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008747"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Espace de noms hiérarchique d’Azure Data Lake Storage Gen2

**L’espace de noms hiérarchique** est un mécanisme clé d’Azure Data Lake Storage Gen2 qui permet d’optimiser les performances de système de fichiers en termes de mise à l’échelle et de prix du stockage d’objets. La collection d’objets/de fichiers dans un compte peut alors être organisée en une hiérarchie de répertoires et sous-répertoires imbriqués, de la même façon que le système de fichiers sur votre ordinateur. Quand vous activez l’espace de noms hiérarchique, le compte de stockage peut fournir l’extensibilité et la rentabilité du stockage d’objets en plus de la sémantique de système de fichiers standard des moteurs et infrastructures d’analyse.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Avantages d’un espace de noms hiérarchique

Les avantages suivants sont associés aux systèmes de fichiers qui implémentent un espace de noms hiérarchique sur les données d’objet blob :

- **Manipulation de répertoires atomique :** les magasins d’objets se rapprochent d’une hiérarchie de répertoires en adoptant une convention de nommage des objets contenant des barres obliques (/) pour indiquer les segments de chemin. Bien que cette convention fonctionne pour organiser les objets, elle ne fournit aucune assistance pour les actions de déplacement, renommage ou suppression de répertoires. Sans répertoires réels, les applications doivent traiter potentiellement des millions d’objets blob individuels pour accomplir des tâches au niveau du répertoire. En revanche, un espace de noms hiérarchique traite ces tâches en mettant à jour une entrée unique (le répertoire parent).

    Cette optimisation spectaculaire est particulièrement significative pour de nombreux frameworks analytiques du Big Data. Des outils comme Hive, Spark, etc. écrivent souvent la sortie dans des emplacements temporaires, qu’ils renomment ensuite à la fin du travail. Sans espace de noms hiérarchique, ce renommage peut souvent durer plus longtemps que le processus analytique lui-même. Une latence de travail moindre équivaut à un coût total de possession inférieur pour les charges de travail analytiques.

- **Style d’interface familier :** les développeurs et les utilisateurs connaissent bien les systèmes de fichiers. Vous n’avez pas besoin d’apprendre un nouveau paradigme de stockage quand vous passez au cloud, car l’interface de système de fichiers exposée par Data Lake Storage Gen2 est le même paradigme que pour les ordinateurs, petits et grands.

Auparavant, les magasins d’objets ne prenaient pas en charge les espaces de noms hiérarchiques en raison de leur échelle limitée. Toutefois, l’espace de noms hiérarchique de Data Lake Storage Gen2 se met à l’échelle de manière linéaire et n’affecte ni la capacité de données ni les performances.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Choix de l’activation ou non d’un espace de noms hiérarchique

Une fois que vous avez activé un espace de noms hiérarchique sur votre compte, vous ne pouvez pas revenir à un espace de noms plat. Par conséquent, déterminez s’il est judicieux d’activer un espace de noms hiérarchique basé sur la nature de vos charges de travail de magasin d’objets. Pour évaluer l’impact de l’activation des espace de noms hiérarchique sur les charges de travail, les applications, les coûts, les intégrations de service, les outils, les fonctionnalités et la documentation, consultez [Mise à niveau du Stockage Blob Azure avec les fonctionnalités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2.md).

Certaines charges de travail peuvent ne pas tirer profit de l’activation d’un espace de noms hiérarchique. Ces charges de travail sont, par exemple, les sauvegardes, le stockage d’images et d’autres applications où l’organisation des objets est stockées séparément des objets eux-mêmes (dans une base de données distincte, par exemple).

En outre, bien que la prise en charge des fonctionnalités de stockage d’objets blob et de l’écosystème de service Azure continue de croître, certaines fonctionnalités et certains services Azure ne sont toujours pas encore pris en charge dans les comptes dotés d’un espace de noms hiérarchique. Voir [Problèmes connus](data-lake-storage-known-issues.md).

En règle générale, nous vous conseillons d’activer un espace de noms hiérarchique pour les charges de travail de stockage conçues pour des systèmes de fichiers qui manipulent des répertoires. Il s’agit de toutes les charges de travail destinées principalement au traitement analytique. Les jeux de données qui nécessitent un degré élevé d’organisation peuvent aussi tirer profit de l’activation d’un espace de noms hiérarchique.

Les raisons de l’activation d’un espace de noms hiérarchique sont déterminées par une analyse du coût TCO. De manière générale, la baisse de latence des charges de travail grâce à l’accélération du stockage signifie que les ressources de calcul sont utilisées moins longtemps. La latence de nombreuses charges de travail peut être améliorée grâce à une manipulation atomique des répertoires que permet un espace de noms hiérarchique. Dans de nombreuses charges de travail, les ressources de calcul représentent plus de 85 % du coût total, donc même une légère réduction de la latence de charge de travail équivaut à des économies importantes au niveau du coût TCO. Même dans les cas où l’activation de l’espace de noms hiérarchique augmente les coûts de stockage, le coût TCO diminue en raison de la réduction des coûts de calcul.

Pour analyser les différences de prix de stockage des données, de prix des transactions et de tarifs de réservation de capacité de stockage entre les comptes qui ont un espace de noms hiérarchique plat et un espace de noms hiérarchique ordinaire, voir [Tarifications d’Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Étapes suivantes

- Activez un espace de noms hiérarchique lorsque vous créez un compte de stockage. Consultez [Créer un compte de stockage](../common/storage-account-create.md).
- Activez un espace de noms hiérarchique sur un compte de stockage existant. Consultez [Mettre à niveau Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).
