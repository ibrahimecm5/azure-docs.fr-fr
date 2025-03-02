---
title: Contrôle d’accès aux classeurs Azure Monitor
description: Créer des rapports complexes en toute simplicité grâce à des classeurs paramétrables prédéfinis et personnalisés à l’aide du contrôle d’accès en fonction du rôle
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: e1f5b06d40ca6883092193493143f668ec999b1c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452452"
---
# <a name="access-control"></a>Contrôle d’accès

Dans les classeurs, le contrôle d’accès fait référence à deux éléments :

* Accès requis pour lire les données dans un classeur. Cet accès est contrôlé par les rôles [Azure standard](../../role-based-access-control/overview.md) sur les ressources utilisées dans le classeur. Les classeurs ne spécifient pas ou ne configurent pas l’accès à ces ressources. Les utilisateurs obtiennent généralement cet accès à ces ressources en utilisant le rôle [Lecteur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-reader) sur ces ressources.

* Accès requis pour enregistrer des classeurs

    - L’enregistrement des classeurs requiert des privilèges d’écriture dans un groupe de ressources pour enregistrer le classeur. Ces privilèges sont généralement spécifiés par le rôle [Contributeur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-contributor), mais peuvent également être définis via le rôle *Contributeur de classeurs*.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Rôles standard avec privilèges associés à un classeur

[Lecteur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-reader) inclut les privilèges standard /read qui seraient utilisés par les outils de supervision (y compris les classeurs) pour lire les données des ressources.

[Contributeur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-contributor) inclut des privilèges `/write` généraux utilisés par divers outils de surveillance pour l’enregistrement d’éléments (y compris le privilège `workbooks/write` pour l’enregistrement des classeurs partagés).
« Collaborateur Workbooks » ajoute des privilèges « workbooks/écriture » à un objet pour enregistrer les classeurs partagés.

Pour les rôles personnalisés :

Ajouter `microsoft.insights/workbooks/write` pour enregistrer les classeurs. Pour plus d’informations, consultez le rôle [Collaborateur du workbook](../../role-based-access-control/built-in-roles.md#monitoring-contributor).

## <a name="next-steps"></a>Étapes suivantes

* [Commencez](./workbooks-overview.md#visualizations) à en apprendre davantage sur les nombreuses options pour les visualisations enrichies des classeurs.