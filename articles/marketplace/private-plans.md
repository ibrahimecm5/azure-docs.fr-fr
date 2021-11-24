---
title: Plans privés dans la place de marché commerciale de Microsoft
description: Plans privés dans la place de marché commerciale de Microsoft pour les éditeurs d’applications et de services (Place de marché Azure).
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: navits09
ms.author: navits
ms.reviewer: amhindma
ms.date: 11/10/2021
ms.openlocfilehash: d8f2435fe78465ee40e3cf6011db14d6daf7c04f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324647"
---
# <a name="private-plans-in-the-microsoft-commercial-marketplace"></a>Plans privés dans la place de marché commerciale de Microsoft

Les éditeurs utilisent des plans privés afin que seuls les clients ciblés les voient. Cet article présente les options et les avantages des plans privés.

> [!TIP]
> Pour voir l’affichage client des plans privés sur la Place de marché commerciale, consultez [Plans privés dans la Place de marché Azure](/marketplace/private-plans) ou [Plans privés dans Microsoft AppSource](/marketplace/appsource-private-plans).

## <a name="unlock-enterprise-deals-with-private-plans"></a>Élargir les possibilités de transactions B2B grâce aux plans privés

Les plans privés permettent aux éditeurs d’offrir des solutions privées et personnalisées aux clients ciblés avec les fonctionnalités dont les entreprises ont besoin :

- Les *tarifs négociés* permettent aux éditeurs d’étendre les remises et les prix hors catalogue par rapport aux offres publiques.
- Grâce aux *conditions générales privées*, ils peuvent personnaliser les conditions générales pour chaque client.
- Les *configurations spécialisées* aident les éditeurs à adapter leur solution Machines virtuelles, Azure Application ou SaaS aux besoins de chaque client. Cette option leur permet également de proposer un accès en préversion aux nouvelles fonctionnalités des produits, avant de les lancer pour l’ensemble des clients.

Les plans privés permettent aux éditeurs de tirer parti de l’envergure et de la disponibilité mondiale d’une place de marché commerciale publique, avec la flexibilité et le contrôle nécessaires pour négocier et proposer des configurations et des offres personnalisées. Les entreprises ont ainsi la liberté de gérer leurs achats/ventes comme elles le souhaitent.

## <a name="create-private-plans"></a>Créer des plans privés

À partir d’*offres nouvelles ou existantes avec plans*, les éditeurs peuvent ajouter sans peine des variantes privées en créant de nouveaux plans (anciennement les « références SKU ») et en les marquant comme privés. Chaque offre peut avoir jusqu’à 45 plans privés.

<!--- [Private SKUs]() --->

Des plans privés sont disponibles pour les types d’offre suivants :

- Machine virtuelle Azure
- Azure Application (implémenté comme modèle de solution ou application gérée)
- Service managé
- Offres SaaS

Les plans privés sont des composants d’une offre que seuls les clients ciblés peuvent voir et acquérir. Seuls les clients ciblés peuvent voir et acheter les plans privés. Des plans privés peuvent être mis à la disposition des clients dans Azure Global et Azure Government.

Ils peuvent réutiliser les images de base et offrir des métadonnées déjà publiées pour un plan public. Cette option permet aux éditeurs de créer plusieurs variantes privées d’une offre publique sans avoir à publier plusieurs versions de la même image de base ou à proposer des métadonnées. Dans le cas des offres Machines virtuelles Azure et Azure Application uniquement, quand un plan privé partage une image de base avec un plan public, les modifications apportées à l’image de base de l’offre se propagent sur tous les plans, publics et privés, qui utilisent cette image de base.

Pour les *nouvelles offres qui comportent uniquement des plans privés*, les éditeurs peuvent créer des offres classiques, puis marquer les plans comme privés. Les offres qui ne possèdent que des plans privés ne sont pas détectables ni accessibles dans le [portail Azure](https://azure.microsoft.com/features/azure-portal/) ou [Microsoft AppSource](https://appsource.microsoft.com/) par les clients qui ne sont pas associés à l’offre.

>[!NOTE]
>Une offre qui contient uniquement des plans privés ne sera pas visible dans la Place de marché Azure publique.

## <a name="target-customers-with-private-plans"></a>Cibler des clients avec des plans privés

Avec les plans privés, qu’ils soient anciens ou nouveaux, les éditeurs ont la possibilité de cibler des clients avec leurs identificateurs d’abonnement. Pour les offres Azure Application et Services gérés, les éditeurs peuvent limiter la disponibilité d’un plan privé à des ID d’abonnement Azure individuels. Pour les offres SaaS, les éditeurs peuvent associer un ID de locataire Azure Active Directory pour limiter la disponibilité d’un plan privé. Pour les offres de machines virtuelles, les éditeurs peuvent attribuer le public qui aura accès à un plan privé à l’aide d’ID de locataire Azure, d’ID d’abonnement ou des deux. Pour chacun de ces types d’offre, jusqu’à 10 ID peuvent être ajoutés manuellement ou une feuille de calcul CSV peut être importée si plus de 10 ID sont requis.

Une fois qu’une offre a été certifiée et publiée, il devient possible de mettre à jour les clients ou de les supprimer du plan grâce à la fonctionnalité de synchronisation des audiences privées. Cette capacité permet aux éditeurs de mettre à jour rapidement et facilement la liste des clients auxquels le plan privé est présenté, sans avoir à certifier ou publier de nouveau l’offre.

## <a name="deploying-a-private-plan"></a>Déploiement d’un plan privé

Une fois connectés au portail Azure, les clients peuvent suivre ces étapes pour sélectionner vos plans privés.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/).
1. Sous **Services Azure**, sélectionnez **Créer une ressource**.
1. Sur la page **Nouveau**, à côté de **Place de marché Azure**, sélectionnez **Afficher tout**. La page Marketplace s’affiche.
1. Dans le volet de navigation de gauche, sélectionnez **Produits privés**.

> [!NOTE]
> Les plans privés sont uniquement découvrables dans le [portail Azure](https://azure.microsoft.com/features/azure-portal/) ou [Microsoft AppSource](https://appsource.microsoft.com/). Ils ne sont pas visibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com). Pour en savoir plus sur la publication sur les différents magasins en ligne du marketplace commercial, consultez [Présentation des options de référencement](./determine-your-listing-type.md).

Les plans privés apparaissent également dans les résultats de recherche et peuvent être déployés par le biais d’une ligne de commande et de modèles Azure Resource Manager, comme n’importe quelle autre offre.

[![[Offres privées apparaissant dans les résultats de la recherche.]](media/marketplace-publishers-guide/private-product.png)](media/marketplace-publishers-guide/private-product.png#lightbox)

>[!Note]
>Les plans privés ne sont pas pris en charge par les abonnements souscrits via un revendeur participant au programme des fournisseurs de solutions cloud (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
