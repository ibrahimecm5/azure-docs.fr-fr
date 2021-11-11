---
title: Limiter le débit total approvisionné sur votre compte Azure Cosmos DB
description: Découvrez comment limiter le débit total approvisionné sur votre compte Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2021
ms.author: thweiss
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1423d4444c7ec53e40f77d951acb58a63538cfbf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557791"
---
# <a name="limit-the-total-throughput-provisioned-on-your-azure-cosmos-db-account"></a>Limiter le débit total approvisionné sur votre compte Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Lors de l’utilisation d’un compte Azure Cosmos DB en mode de [débit approvisionné](./set-throughput.md), la plupart de vos coûts proviennent généralement de la quantité de débit que vous avez approvisionnée dans votre compte. En particulier, ces coûts sont influencés directement par :

- Nombre de bases de données qui ont un débit partagé.
- Nombre de conteneurs qui ont un débit dédié.
- Quantité de débit approvisionnée sur chacune de ces ressources.
- Nombre de régions dans lesquelles votre compte est disponible.

Il peut être difficile d’effectuer le suivi de la quantité totale de débit que vous avez approvisionnée dans votre compte, en particulier lorsque vous vous familiarisez avec Azure Cosmos DB. Cela peut entraîner des frais imprévus lorsque ce montant se termine par un budget que vous ne pensiez pas dépasser. Pour vous aider à mieux contrôler vos coûts, Azure Cosmos DB vous permet de limiter le débit total approvisionné sur votre compte.

> [!NOTE]
> Cette fonctionnalité n’est pas disponible sur les comptes [serverless](./serverless.md).

Une fois que vous avez défini une limite au débit total de votre compte, les opérations suivantes qui entraînent le dépassement de cette limite sont bloquées et échouent de manière explicite :

- Création d’une nouvelle base de données avec un débit partagé.
- Créer un conteneur avec débit dédié
- L’amélioration du débit approvisionné sur une ressource configurée en mode standard (manuel).
- Augmentation du débit maximal approvisionné sur une ressource configurée en mode de mise à l’échelle automatique.
- Ajout d’une nouvelle région à votre compte.

> [!NOTE]
> Pour les ressources configurées en mode de mise à l’échelle automatique, il s’agit du débit maximal configuré sur la ressource qui compte dans le débit total de votre compte.

> [!IMPORTANT]
> Une fois qu’une limite de débit total est activée sur votre compte, vous devez passer une valeur de débit explicite lors de la création de conteneurs. Vous obtiendrez une erreur si vous tentez de créer un conteneur sans débit explicite.

## <a name="set-the-total-throughput-limit-from-the-azure-portal"></a>Définir la limite du débit total à partir du portail Azure

### <a name="new-account"></a>Nouveau compte

Lorsque vous créez un compte Azure Cosmos DB à partir du portail, vous avez la possibilité de limiter le débit total du compte :

:::image type="content" source="./media/limit-total-account-throughput/create-account.png" alt-text="Capture d’écran du portail Azure montrant comment limiter le débit total du compte lors de la création d’un nouveau compte" border="true":::

Le fait de cocher cette option limite le débit total de votre compte à 4 000 RU/s. Vous pouvez modifier cette valeur une fois que votre compte a été créé.

### <a name="existing-account"></a>Compte existant

Dans le Portail Azure, accédez à votre compte Azure Cosmos DB et sélectionnez **Gestion des coûts** dans le menu de gauche.

:::image type="content" source="./media/limit-total-account-throughput/existing-account.png" alt-text="Capture d’écran du portail Azure montrant comment mettre à jour le débit total du compte sur un compte existant" border="true":::

Cette section présente un résumé du débit total approvisionné sur votre compte et vous permet de configurer la limite de débit totale. Les trois options ci-dessous sont disponibles :

- **Limitez le débit total approvisionné du compte au montant inclus dans la remise de niveau gratuit**. Cette option est disponible uniquement sur les comptes de niveau gratuit et limite le débit total de votre compte à 1 000 RU/s. Lorsque vous activez cette option, vous vous assurez que vous n’êtes pas facturé pour le débit approvisionné.
- **Autoriser le débit total du compte à être approvisionné jusqu’à un montant personnalisé**. Cette option vous permet d’entrer le débit total approvisionné que vous ne souhaitez pas dépasser. Une estimation du coût mensuel correspondant à votre entrée est indiquée comme référence.
  > [!NOTE]
  > Cette limite personnalisée ne peut pas être inférieure au débit total actuellement approvisionné dans le compte.
- **Aucune limite, autoriser le débit total du compte à être approvisionné sur n’importe quel montant**. Cette option désactive la limite.

## <a name="set-the-total-throughput-limit-programmatically"></a>Définir la limite de débit totale par programmation

### <a name="using-azure-resource-manager-templates"></a>Utilisation de modèles Azure Resource Manager

Lors de la création ou de la mise à jour de votre compte Azure Cosmos DB avec Azure Resource Manager, vous pouvez configurer la limite du débit total en définissant la propriété `properties.capacity.totalThroughputLimit` :

```json
{
  "location": "West US",
  "kind": "DocumentDB",
  "properties": {
    "locations": [
      {
        "locationName": "West US",
        "failoverPriority": 0,
        "isZoneRedundant": false
      }
    ],
    "databaseAccountOfferType": "Standard",
    "capacity": {
        "totalThroughputLimit": 2000
    }
  }
}
```

Affectez à cette propriété la valeur `-1` pour désactiver la limite.

## <a name="frequently-asked-questions"></a>Forum aux questions

#### <a name="are-there-situations-where-the-total-provisioned-throughput-can-exceed-the-limit"></a>Existe-t-il des situations où le débit total approvisionné peut dépasser la limite ?

Azure Cosmos DB applique un débit minimal de 10 RU/s par Go de données stockées. Si vous ingérez des données alors que vous êtes déjà au débit minimum, le débit approvisionné sur vos ressources augmente automatiquement pour atteindre 10 RU/s par Go. Dans ce cas, et dans ce cas seulement, le débit total approvisionné peut dépasser la limite que vous avez définie.

## <a name="next-steps"></a>Étapes suivantes

- Prise en main de [la planification et de la gestion des coûts](./plan-manage-costs.md) sur Azure Cosmos DB.
- En savoir plus sur le [débit approvisionné](./set-throughput.md).
- Découvrez comment [optimiser les coûts de débit approvisionnés](./optimize-cost-throughput.md).
