---
title: Qu’est-ce qu’Azure Lighthouse ?
description: Azure Lighthouse permet aux fournisseurs de services de fournir des services managés à leurs clients avec une automatisation et une efficacité accrues à grande échelle.
ms.date: 11/02/2021
ms.topic: overview
ms.openlocfilehash: 6279ef69c60c7a6d76fe0dfbea68934394c23c06
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343113"
---
# <a name="what-is-azure-lighthouse"></a>Qu’est-ce qu’Azure Lighthouse ?

Azure Lighthouse permet une gestion multi-locataire avec de la scalabilité, une automatisation plus poussée et une gouvernance renforcée des ressources.

Grâce à Azure Lighthouse, les fournisseurs de services peuvent fournir des services gérés à l’aide d’[outils complets et robustes intégrés à la plateforme Azure](concepts/architecture.md). Les clients conservent le contrôle des personnes autorisées à accéder à leur locataire, des ressources auxquelles elles peuvent accéder et des actions qu’elles peuvent entreprendre. Les [entreprises](concepts/enterprise.md) qui gèrent des ressources sur plusieurs locataires peuvent également utiliser Azure Lighthouse pour simplifier les tâches de gestion.

Les [expériences de gestion inter-locataire](concepts/cross-tenant-management-experience.md) vous permettent de travailler plus efficacement avec des services Azure tels qu’[Azure Policy](how-to/policy-at-scale.md), [Microsoft Sentinel](how-to/manage-sentinel-workspaces.md), [Azure Arc](how-to/manage-hybrid-infrastructure-arc.md) et bien plus encore. Les utilisateurs peuvent voir quelles modifications ont été apportées et par qui [dans le journal d’activité](how-to/view-service-provider-activity.md), qui est stocké dans le locataire du client et peut être consulté par les utilisateurs dans le locataire gérant.

![Schéma de présentation d’Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Avantages

Azure Lighthouse aide les fournisseurs de services à créer et à fournir des services managés de manière efficace. Voici les avantages :

- **Gestion à grande échelle** : les opérations d’engagement client et de gestion du cycle de vie des ressources des clients sont plus simples et plus évolutives. Les API, outils de gestion et flux de travail existants peuvent être utilisés avec les ressources déléguées, y compris des machines hébergées en dehors d’Azure, quelles que soient les régions dans lesquelles elles se trouvent.
- **Visibilité et contrôle accrus pour les clients** : Les clients ont un contrôle précis des étendues qu’ils délèguent pour la gestion et des autorisations qui sont autorisées. Ils peuvent [auditer les actions des fournisseurs de services](how-to/view-service-provider-activity.md) et supprimer complètement l’accès à tout moment.
- **Outillage de plateforme complet et unifié** : Azure Lighthouse opère avec les outils et API existants, des [applications managées Azure](concepts/managed-applications.md) et des programmes de partenaires tels que le [programme Fournisseur de solutions Cloud (CSP)](concepts/cloud-solution-provider.md). Cette flexibilité prend en charge les principaux scénarios des fournisseurs de services, notamment plusieurs modèles de licence tels que Contrat Entreprise, CSP et Paiement à l’utilisation. Vous pouvez intégrer Azure Lighthouse à vos workflows et applications existants, et suivre votre impact sur les engagements client en [liant votre ID partenaire](how-to/partner-earned-credit.md).

## <a name="capabilities"></a>Fonctionnalités

Azure Lighthouse offre plusieurs moyens de simplifier l’engagement et la gestion :

- **Gestion des ressources déléguées Azure** : [gérez les ressources Azure de vos clients de façon sécurisée à partir de votre propre locataire](concepts/architecture.md), sans avoir à changer de contexte ou de plan de contrôle. Les abonnements et les groupes de ressources des clients peuvent être délégués à des utilisateurs et des rôles spécifiés dans le locataire gérant, avec la possibilité de supprimer l’accès si besoin.
- **Nouvelles expériences du portail Azure** : Affichez les informations multilocataires dans la page [**Mes clients** du](how-to/view-manage-customers.md) portail Azure. Une page [**Fournisseurs de services** correspondante](how-to/view-manage-service-providers.md) permet à vos clients de voir et gérer l’accès du fournisseur de services.
- **Modèles Microsoft Azure Resource Manager** : Utilisez les modèles ARM pour [intégrer des ressources client déléguées](how-to/onboard-customer.md) et [effectuer des tâches de gestion multilocataires](samples/index.md).
- **Offres de services managés de la Place de marché Azure** : [Offrez vos services aux clients](concepts/managed-services-offers.md) via des offres privées ou publiques et intégrez-les automatiquement à Azure Lighthouse.

> [!TIP]
> Une offre similaire, [Microsoft 365 Lighthouse](/microsoft-365/lighthouse/m365-lighthouse-overview), aide les fournisseurs de services à intégrer, superviser et gérer leurs clients Microsoft 365 à grande échelle. Microsoft 365 Lighthouse est actuellement en préversion.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Aucun coût supplémentaire n’est associé à l’utilisation d’Azure Lighthouse pour gérer les ressources Azure. Tout client ou partenaire Azure peut utiliser Azure Lighthouse.

## <a name="cross-region-and-cloud-considerations"></a>Considérations liées au cloud et interrégions

Azure Lighthouse n’est pas un service régional. Vous pouvez gérer des ressources déléguées situées dans différentes [régions](../availability-zones/az-overview.md#regions). Toutefois, la délégation d’abonnements sur un [cloud national](../active-directory/develop/authentication-national-cloud.md) et le cloud public Azure, ou sur deux clouds nationaux distincts, n’est pas prise en charge.

## <a name="support-for-azure-lighthouse"></a>Prise en charge d’Azure Lighthouse

Pour obtenir de l’aide sur l’utilisation d’Azure Lighthouse, [ouvrez une demande de support](..//azure-portal/supportability/how-to-create-azure-support-request.md) dans le portail Azure. Pour **Type de problème**, choisissez **Technique**. Sélectionnez un abonnement, puis **Lighthouse** (sous **Surveillance et gestion**).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur le [fonctionnement technique d’Azure Lighthouse](concepts/architecture.md).
- Explorez les [expériences de la gestion multilocataire](concepts/cross-tenant-management-experience.md).
- Découvrez comment [utiliser Azure Lighthouse au sein d’une d’entreprise](concepts/enterprise.md).
- Affichez les détails de la [disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) et de [l’étendue d’audit du FedRAMP et du DoD SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) pour Azure Lighthouse.
