---
title: Concepts de distribution de données et de scale-out avec un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Concepts de distribution de données avec un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: aee8700274d074d94b6f6f8e1e153f256cb6e158
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294197"
---
# <a name="concepts-for-distributing-data-with-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Concepts de distribution de données avec un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc

Cet article décrit les concepts importants pour tirer le meilleur parti de PostgreSQL Hyperscale avec Azure Arc.
Les articles liés ci-dessous pointent vers les concepts expliqués pour Azure Database pour PostgreSQL Hyperscale (Citus). Comme il s’agit de la même technologie que PostgreSQL Hyperscale avec Azure Arc, les mêmes concepts et perspectives s’appliquent.

**Quelle est la différence entre eux ?**
- _Hyperscale (Citus) - Azure Database pour PostgreSQL_

Facteur de forme Hyperscale du moteur de base de données Postgres disponible en qualité de base de données en tant que service dans Azure (PaaS). Il s’appuie sur l’extension Citus qui permet l’expérience Hyperscale. Dans ce facteur de forme, le service s’exécute dans les centres de données Microsoft et est exploité par Microsoft.

- _PostgreSQL Hyperscale avec Azure Arc_

Il s’agit du facteur de forme hyperscale du moteur de base de données Postgres disponible avec les services de données compatibles avec Azure Arc. Dans ce facteur de forme, nos clients fournissent l’infrastructure qui héberge les systèmes et les exploitent.

Les concepts clés relatifs à PostgreSQL Hyperscale avec Azure Arc sont résumés ci-dessous :

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Nœuds et tables
Il est important de connaître les concepts suivants pour tirer le meilleur parti de Postgres Hyperscale avec Azure Arc :
- Nœuds Postgres spécialisés dans PostgreSQL Hyperscale avec Azure Arc : coordinateur et workers
- Types de tables : tables distribuées, tables de référence et tables locales
- Partitions

Pour plus d’informations, consultez [Nœuds et tables dans Azure Database pour PostgreSQL – Hyperscale (Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Déterminer le type d’application
Il est important d’identifier clairement le type d’application que vous créez. Pourquoi ? Parce que l’exécution de requêtes efficaces sur un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc exige que les tables soient correctement réparties entre les serveurs. La distribution recommandée varie selon le type d’application et ses modèles de requête. Il existe grosso modo deux types d’applications qui fonctionnent bien sur Postgres Hyperscale avec Azure Arc :
- Applications multilocataires
- Applications en temps réel

La première étape de la modélisation des données consiste à identifier celle qui ressemble le plus à votre application.

Pour plus d’informations, consultez [Détermination du type d’application](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Choisir une colonne de distribution
Pourquoi choisir une colonne distribuée ?

C’est l’une des décisions de modélisation les plus importantes que vous allez prendre. PostgreSQL Hyperscale avec Azure Arc stocke des lignes dans des partitions en fonction de la valeur de la colonne de distribution des lignes. Le choix correct permet de regrouper les données sur les mêmes nœuds physiques, ce qui a pour effet d’accélérer les requêtes et d’ajouter la prise en charge toutes les fonctionnalités SQL. Un choix incorrect ralentit le système et ne permet pas de prendre en charge toutes les fonctionnalités SQL sur les nœuds. Cet article donne des conseils sur la colonne de distribution pour les deux scénarios Hyperscale les plus courants.

Pour plus d’informations, consultez [Choisir des colonnes de distribution](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Colocalisation de table

La colocalisation a trait au stockage ensemble d’informations connexes sur les mêmes nœuds. Les requêtes peuvent s'exécuter rapidement lorsque toutes les données nécessaires sont disponibles sans aucun trafic réseau. La colocation de données associées sur différents nœuds permet aux requêtes de s’exécuter efficacement en parallèle sur chaque nœud.

Pour plus d’informations, consultez [Colocalisation de tables](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Étapes suivantes
- [Découvrir la création de PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)
- [Découvrir le scale-out de groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans votre contrôleur de données Arc](scale-out-in-postgresql-hyperscale-server-group.md)
- [Découvrir les services de données avec Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Découvrir Azure Arc](https://aka.ms/azurearc)
