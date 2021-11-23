---
title: Créer une instance d’Apache Kafka pour Confluent Cloud avec Azure PowerShell - Solutions partenaires Azure
description: Cet article explique comment utiliser Azure PowerShell pour créer une instance d’Apache Kafka pour Confluent Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 11/03/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 696e5138d80b1ba799f96fe60da3be0f4f1d2548
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485874"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-powershell"></a>Démarrage rapide : Bien démarrer avec Apache Kafka pour Confluent Cloud - Azure PowerShell

Dans ce guide de démarrage rapide, vous allez utiliser la Place de marché Azure et Azure PowerShell afin de créer une instance d’Apache Kafka pour Confluent Cloud.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/).
- Vous devez disposer du rôle _Propriétaire_ ou _Contributeur_ pour votre abonnement Azure. L’intégration entre Azure et Confluent ne peut être configurée que par les utilisateurs ayant un accès _Propriétaire_ ou _Contributeur_. Avant de commencer, [veillez à disposer de l’accès approprié](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Rechercher une offre

Utilisez le portail Azure pour rechercher l’application Apache Kafka pour Confluent Cloud.

1. Dans un navigateur web, accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte.

1. Si vous avez visité la **Place de marché** dans une session récente, sélectionnez l’icône parmi les options disponibles. Dans le cas contraire, recherchez _Place de marché_.

    :::image type="content" source="media/marketplace.png" alt-text="Icône de la Place de marché.":::

1. Dans la page **Place de marché**, vous disposez de deux options en fonction du type de plan souhaité. Vous pouvez vous inscrire à un plan de paiement à l’utilisation ou un plan d’engagement. Le paiement à l’utilisation est disponible pour tout le monde. Le plan d’engagement est disponible pour les clients qui ont été approuvés pour une offre privée.

   - Pour les clients inscrits à un plan de **paiement à l’utilisation**, recherchez _Apache Kafka sur Confluent Cloud_. Sélectionnez l’offre pour Apache Kafka sur Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="rechercher parmi les offres de la Place de marché Azure.":::

   - Pour les clients inscrits à un plan d’**engagement**, sélectionnez le lien vers **Voir les offres privées**. L’engagement vous oblige à vous inscrire pour un montant minimal de dépense. Utilisez cette option uniquement lorsque vous savez que vous avez besoin du service pendant une période prolongée.

     :::image type="content" source="media/view-private-offers.png" alt-text="voir les offres privées.":::

     Recherchez _Apache Kafka sur Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="sélectionner une offre privée.":::

## <a name="create-resource"></a>Créer une ressource

Une fois que vous avez sélectionné l’offre pour Apache Kafka sur Confluent Cloud, vous êtes prêt à configurer l’application.

Commencez par préparer votre environnement pour Azure PowerShell :

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Tant que le module PowerShell **Az.Confluent** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`.

```azurepowershell
Install-Module -Name Az.Confluent -Scope CurrentUser -Repository PSGallery -Force
```

Après vous être connecté, utilisez l’applet de commande [New-AzConfluentOrganization](/powershell/module/az.confluent/new-azconfluentorganization) pour créer la ressource d’organisation :

```azurepowershell
$ConfluentOrgParams = @{
    Name = 'myOrganization'
    ResourceGroupName = 'myResourceGroup'
    Location = 'my location'
    OfferDetailId = 'string'
    OfferDetailPlanId = 'string'
    OfferDetailPlanName = 'string'
    OfferDetailPublisherId = 'string'
    OfferDetailTermUnit = 'string'
    UserDetailEmailAddress = 'contoso@microsoft.com'
    UserDetailFirstName = 'string'
    UserDetailLastName = 'string'
    Tag = @{Environment='Dev'}
}

New-AzConfluentOrganization @ConfluentOrgParams
```

> [!NOTE]
> Si vous souhaitez que le résultat de la commande soit retourné avant la fin de l’opération de création, ajoutez le paramètre facultatif `-NoWait`. L’opération continue à s’exécuter jusqu’à ce que l’organisation Confluent soit créée.

Pour voir la liste des organisations existantes, utilisez l’applet de commande [Get-AzConfluentOrganization](/powershell/module/az.confluent/get-azconfluentorganization).

Vous pouvez afficher toutes les organisations de votre abonnement :

```azurepowershell
Get-AzConfluentOrganization
```

Vous pouvez également afficher les organisations d’un groupe de ressources :

```azurepowershell
Get-AzConfluentOrganization -ResourceGroupName myResourceGroup
```

Pour voir les propriétés d’une organisation spécifique, utilisez l’applet de commande `Get-AzConfluentOrganization` avec les paramètres `Name` et `ResourceGroupName`.

Vous pouvez afficher l’organisation à l’aide de son nom :

```azurepowershell
Get-AzConfluentOrganization -Name myOrganization -ResourceGroupName myResourceGroup
```

Si vous obtenez une erreur, consultez [Résolution des problèmes des solutions Apache Kafka pour Confluent Cloud](troubleshoot.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer la ressource Confluent Cloud](manage.md)
