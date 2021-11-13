---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 153f5c71925e3f4fe1078f014684a12c60a054b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479047"
---
- Un abonnement Azure. Créez un [compte Azure gratuit](https://azure.microsoft.com/free/).

- Créez un pool Data Explorer en utilisant [Synapse Studio](../data-explorer-create-pool-studio.md) ou [le portail Azure](../data-explorer-create-pool-portal.md)
- Créez une base de données Data Explorer.
    1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Données**.
    1. Sélectionnez **&plus;** (Ajouter une nouvelle ressource) > **Pool Data Explorer** et utilisez les informations suivantes :

        | Paramètre | Valeur suggérée | Description |
        |--|--|--|
        | Nom du pool | *contosodataexplorer* | Nom du pool Data Explorer à utiliser |
        | Nom | *TestDatabase* | Ce nom de base de données doit être unique dans le cluster. |
        | Période de conservation par défaut | *365* | Intervalle de temps (en jours) pendant lequel vous avez la garantie d’avoir les données à disposition pour les interroger. Cet intervalle se mesure à partir du moment où les données sont ingérées. |
        | Période de cache par défaut | *31* | Intervalle de temps (en jours) pendant lequel les données fréquemment interrogées restent disponibles dans le stockage SSD ou la RAM, plutôt que dans un stockage à plus long terme. |

    1. Sélectionnez **Créer** pour créer la base de données. La création prend généralement moins d’une minute.
