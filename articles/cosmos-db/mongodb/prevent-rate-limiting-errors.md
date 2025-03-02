---
title: Empêcher les erreurs de limitation de débit pour les opérations de l’API Azure Cosmos DB pour MongoDB.
description: Découvrez comment empêcher les opérations de votre API Azure Cosmos DB pour MongoDB de déclencher des erreurs de limitation de débit avec la fonctionnalité SSR (Server Side Retry).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9067a87887c47229b8bba687535e2730c2ed1b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030901"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Empêcher les erreurs de limitation de débit pour les opérations de l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Les opérations de l’API Azure Cosmos DB pour MongoDB peuvent échouer avec des erreurs de limitation de débit (16500/429) si elles dépassent la limite de débit d’une collection (RU). 

Vous pouvez activer la fonctionnalité de nouvelle tentative côté serveur (Server Side Retry, SSR) et laisser le serveur réessayer automatiquement ces opérations. Les demandes font l’objet d’une nouvelle tentative après un bref délai pour toutes les collections dans votre compte. Cette fonctionnalité est une alternative pratique à la gestion des erreurs de limitation de débit dans l’application cliente.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre API Azure Cosmos DB pour le compte MongoDB.

1. Accédez au volet **Fonctionnalités** dans la section **Paramètres**.

1. Sélectionnez **Server Side Retry** (Nouvelle tentative côté serveur).

1. Cliquez sur **Activer** pour activer cette fonctionnalité pour tous les collections dans votre compte.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Capture d’écran de la fonctionnalité de nouvelle tentative côté serveur pour l’API Azure Cosmos DB pour MongoDB":::

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

1. Vérifiez si la fonctionnalité SSR est déjà activée pour votre compte :

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

1. **Activez la fonctionnalité SSR** pour toutes les collections dans votre compte de base de données. Jusqu’à quinze minutes peuvent être nécessaires pour que cette modification soit prise en compte.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
   ```

1. La commande suivante **désactive** la nouvelle tentative côté serveur pour toutes les collections dans votre compte de base de données en supprimant `DisableRateLimitingResponses` de la liste des capacités. Jusqu’à quinze minutes peuvent être nécessaires pour que cette modification soit prise en compte.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
   ```

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-are-requests-retried"></a>Comment les demandes font-elles l’objet d’une nouvelle tentative ?

Les demandes font l’objet d’une nouvelle tentative en continu (de façon répétée) jusqu’à ce qu’un délai de 60 secondes soit atteint. Si le délai est atteint, le client reçoit une [exception ExceededTimeLimit (50)](error-codes-solutions.md).

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>Comment puis-je superviser les effets d’une nouvelle tentative côté serveur ?

Vous pouvez afficher les erreurs de limitation de débit (429) qui font l’objet d’une nouvelle tentative côté serveur dans le volet Métriques de Cosmos DB. N’oubliez pas que ces erreurs ne sont pas dirigées vers le client quand la fonctionnalité SSR est activée, car elles sont gérées et font l’objet d’une nouvelle tentative côté serveur.

Vous pouvez rechercher les entrées de journal contenant *estimatedDelayFromRateLimitingInMilliseconds* dans vos [journaux de ressources Cosmos DB](../cosmosdb-monitor-resource-logs.md).

### <a name="will-server-side-retry-affect-my-consistency-level"></a>La nouvelle tentative côté serveur a-t-elle une incidence sur mon niveau de cohérence ?

La nouvelle tentative côté serveur n’affecte pas la cohérence d’une demande. Les demandes font l’objet d’une nouvelle tentative côté serveur si elles subissent une limitation de débit (avec une erreur 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>La nouvelle tentative côté serveur affecte-t-elle tout type d’erreur que mon client peut recevoir ?

Non, la nouvelle tentative côté serveur affecte uniquement les erreurs de limitation de débit (429) en effectuant une nouvelle tentative côté serveur. Cette fonctionnalité vous évide d’avoir à gérer les erreurs de limitation de débit dans l’application cliente. Toutes les [autres erreurs](error-codes-solutions.md) sont dirigées vers le client.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la résolution des erreurs courantes, consultez cet article :

* [Résoudre des problèmes courants dans l’API Azure Cosmos DB pour MongoDB](error-codes-solutions.md)

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)
