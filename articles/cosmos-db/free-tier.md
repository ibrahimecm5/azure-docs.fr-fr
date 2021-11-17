---
title: Niveau gratuit d’Azure Cosmos DB
description: Utilisez le niveau gratuit d’Azure Cosmos DB pour démarrer, développer et tester vos applications. Avec le niveau gratuit, vous obtiendrez gratuitement 1 000 RU/s et 25 Go de stockage dans le compte.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: sngun
ms.openlocfilehash: ddc09c428a7779f5e059ead02dbd869a592813e5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554770"
---
# <a name="azure-cosmos-db-free-tier"></a>Niveau gratuit d’Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Le niveau gratuit d’Azure Cosmos DB facilite le démarrage, le développement et le test de vos applications, ou même l’exécution gratuite de petites charges de travail de production. Quand le niveau gratuit est activé sur un compte, vous obtenez gratuitement 1 000 RU/s et 25 Go de stockage dans le compte. Le débit et le stockage consommés au-delà de ces limites sont facturés au prix normal. Le niveau gratuit est disponible pour tous les comptes d’API avec un débit provisionné, un débit de mise à l’échelle automatique, et une seule ou plusieurs régions d’écriture.

Le niveau gratuit dure indéfiniment pendant la durée de vie du compte et il est fourni avec tous les [avantages et fonctionnalités](introduction.md#key-benefits) d’un compte Azure Cosmos DB standard. Ces avantages incluent un stockage et un débit illimités (RU/s), des contrats SLA, une haute disponibilité, une distribution mondiale clé en main dans toutes les régions Azure, et bien plus encore.

Vous pouvez avoir un seul compte Azure Cosmos DB de niveau gratuit par abonnement Azure et vous devez y souscrire lors de la création du compte. Si vous ne voyez pas l’option permettant d’appliquer la remise de niveau gratuit, cela signifie qu’un autre compte dans l’abonnement a déjà été activé avec le niveau gratuit. Si vous créez un compte avec le niveau gratuit, puis que vous le supprimez, vous pouvez appliquer le niveau gratuit à un nouveau compte. Lors de la création d’un nouveau compte, il est recommandé d’activer la remise du niveau gratuit si elle est disponible.

> [!NOTE]
> Le niveau gratuit n’est actuellement pas disponible pour les comptes serverless.

## <a name="free-tier-with-shared-throughput-database"></a>Niveau gratuit pour une base de données avec débit partagé

Dans le modèle de débit partagé, quand vous provisionnez le débit sur une base de données, il est partagé entre tous les conteneurs de la base de données. Quand vous utilisez le niveau gratuit, vous pouvez provisionner gratuitement une base de données partagée avec un maximum de 1 000 RU/s. Tous les conteneurs de la base de données vont partager le débit. 

Tout comme le compte normal, dans le compte de niveau gratuit, une base de données avec débit partagé peut avoir un maximum de 25 conteneurs. Les bases de données supplémentaires avec un débit partagé ou des conteneurs avec un débit dédié au-delà de 1 000 RU/s sont facturées au prix normal.

## <a name="free-tier-with-azure-discount"></a>Niveau gratuit avec remise Azure

Le niveau gratuit d’Azure Cosmos DB est compatible avec le [compte gratuit Azure](optimize-dev-test.md#azure-free-account). Pour y souscrire, créez un compte Azure Cosmos DB de niveau gratuit dans votre abonnement de compte gratuit Azure. Pendant les 12 premiers mois, vous aurez une remise combinée de 1 400 RU/s (1 000 RU/s pour le niveau gratuit d’Azure Cosmos DB et 400 RU/s pour le compte gratuit Azure) et de 50 Go de stockage (25 Go pour le niveau gratuit d’Azure Cosmos DB et 25 Go pour le compte gratuit Azure). Une fois les 12 mois expirés, vous continuerez de disposer de 1 000 RU/s et de 25 Go du niveau gratuit d’Azure Cosmos DB pendant la durée de vie du compte Azure Cosmos DB. Pour obtenir un exemple de la façon dont les frais sont cumulés, consultez [Exemples de facturation avec des comptes de niveau gratuit](understand-your-bill.md#azure-free-tier).

> [!NOTE]
> Le niveau Gratuit d’Azure Cosmos DB est différent du compte gratuit Azure. Le compte gratuit Azure offre des crédits et des ressources Azure gratuitement pendant une durée limitée. Lorsque vous utilisez Azure Cosmos DB dans le cadre de ce compte gratuit, vous bénéficiez d’un stockage de 25 G  et de 400 RU/s de débit approvisionné pendant 12 mois.

## <a name="best-practices-to-keep-your-account-free"></a>Bonnes pratiques pour que votre compte reste gratuit

Quand vous utilisez le niveau gratuit d’Azure Cosmos DB, pour que votre compte reste entièrement gratuit, il ne doit pas faire une consommation supplémentaire de RU/s ou de stockage autre que celle offerte par le niveau gratuit.

Par exemple, voici quelques options qui n’entraînent aucuns frais mensuels :

* Une seule base de données avec un débit provisionné de 1 000 RU/s au maximum.
* Deux conteneurs, un avec un maximum de 400 RU/s et l’autre avec un débit provisionné de 600 RU/s.
* Un compte avec 2 régions, une seule région ayant un seul conteneur avec un débit provisionné de 500 RU/s au maximum.

## <a name="create-an-account-with-free-tier"></a>Créer un compte avec un niveau gratuit

Vous pouvez créer un compte de niveau gratuit en utilisant le portail Azure, PowerShell, CLI ou des modèles Azure Resource Manager (ARM). Vous pouvez choisir le niveau gratuit lors de la création du compte, et vous ne pouvez pas le définir une fois le compte créé.

### <a name="azure-portal"></a>Portail Azure

Lors de la création du compte avec le portail Azure, définissez l’option **Appliquer la remise de niveau gratuit** sur **Appliquer**. Pour obtenir des instructions pas à pas, consultez l’article [Créer un compte avec un niveau gratuit](create-cosmosdb-resources-portal.md).

### <a name="arm-template"></a>Modèle ARM

Pour créer un compte de niveau gratuit en utilisant un modèle ARM, définissez la propriété `"enableFreeTier": true`. Pour le modèle complet, consultez [Déployer un exemple de modèle ARM avec un niveau gratuit](manage-with-templates.md#free-tier).

### <a name="cli"></a>Interface de ligne de commande

Pour créer un compte avec un niveau gratuit en utilisant l’interface CLI, définissez le paramètre `--enable-free-tier` sur true :

```azurecli-interactive
# Create a free tier account for SQL API
az cosmosdb create \
    -n "Myaccount" \
    -g "MyResourcegroup" \
    --enable-free-tier true \
    --default-consistency-level "Session"
    
```

### <a name="powershell"></a>PowerShell

Pour créer un compte avec un niveau gratuit en utilisant PowerShell, définissez le paramètre `-EnableFreeTier` sur true :

```powershell-interactive
# Create a free tier account for SQL API. 
New-AzCosmosDBAccount -ResourceGroupName "MyResourcegroup" `
    -Name "Myaccount" `
    -ApiKind "sql" `
    -EnableFreeTier true `
    -DefaultConsistencyLevel "Session" `
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un compte de niveau gratuit, vous pouvez commencer à créer des applications utilisant Azure Cosmos DB avec les articles suivants :

* [Créer une application console en utilisant le SDK .NET V4](create-sql-api-dotnet-v4.md) pour gérer des ressources Azure Cosmos DB.
* [Générer une application web .NET à l’aide de l’API d’Azure Cosmos DB pour MongoDB](mongodb/create-mongodb-dotnet.md)
* [Téléchargez un notebook à partir de la galerie](publish-notebook-gallery.md#download-a-notebook-from-the-gallery) et analysez vos données.
* En savoir plus sur les [factures Azure Cosmos DB](understand-your-bill.md)
