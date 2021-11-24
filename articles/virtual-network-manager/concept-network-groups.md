---
title: Qu’est-ce qu’un groupe de réseaux dans le gestionnaire Azure Virtual Network Manager (version préliminaire) ?
description: Découvrez comment les groupes de réseaux peuvent vous aider à gérer vos réseaux virtuels.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5da75fba68e285ea4b01feee514bd38f6ceae036
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493592"
---
# <a name="what-is-a-network-group-in-azure-virtual-network-manager-preview"></a>Qu’est-ce qu’un groupe de réseaux dans le gestionnaire Azure Virtual Network Manager (version préliminaire) ?

Dans cet article, vous découvrirez les *groupes de réseaux* et comment ils peuvent vous aider à regrouper des réseaux virtuels pour faciliter la gestion. Vous en apprendrez également plus sur l’*appartenance à un groupe statique* et l’*appartenance à un groupe dynamique*, ainsi que sur l’utilisation de chaque type d’appartenance.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="network-group"></a>Groupe de réseaux

Un *groupe de réseaux* est un ensemble de réseaux virtuels sélectionnés manuellement ou à l’aide d’instructions conditionnelles. Lorsque vous sélectionnez des réseaux virtuels manuellement, on parle de « *membres statiques* ». Les réseaux virtuels sélectionnés à l’aide d’instructions conditionnelles sont appelés des « *membres dynamiques* ». 

## <a name="static-membership"></a>Appartenance statique

Lorsque vous créez un groupe de réseaux, vous pouvez ajouter des réseaux virtuels à un groupe en sélectionnant manuellement des réseaux virtuels individuels à partir d’une liste fournie. La liste des réseaux virtuels dépend de l’étendue (groupe d’administration ou abonnement) définie au moment du déploiement d’Azure Virtual Network Manager. Cette méthode est utile lorsque vous possédez plusieurs réseaux virtuels que vous souhaitez ajouter au groupe de réseaux. Les mises à jour des configurations contenant des membres statiques devront être à nouveau déployées pour appliquer les nouvelles modifications.

## <a name="dynamic-membership"></a>Adhésion dynamique

L’appartenance dynamique vous donne la possibilité de sélectionner plusieurs réseaux virtuels à la fois s’ils remplissent les instructions conditionnelles que vous avez définies. Cette méthode est utile pour les scénarios où vous avez des centaines ou des milliers de réseaux virtuels dans un ou plusieurs abonnements et que vous devez en sélectionner une poignée par nom, ID ou étiquette. Chaque condition est traitée dans l’ordre indiqué et les configurations sont appliquées aux réseaux virtuels pour répondre à ces conditions. Pour savoir comment configurer des instructions conditionnelles, consultez [Exclure des éléments de l’appartenance dynamique](how-to-exclude-elements.md).

## <a name="network-group-and-azure-policy"></a>Groupe de réseaux et stratégie Azure

Lorsque vous créez un groupe de réseaux, une stratégie Azure est créée pour que le gestionnaire Azure Virtual Network Manager soit averti des modifications apportées à l’appartenance du réseau virtuel. Les stratégies définies sont disponibles, mais elles ne sont actuellement pas modifiables par les utilisateurs. La création, la modification et la suppression des définitions et des affectations de stratégies Azure pour les groupes de réseaux sont possibles uniquement via le gestionnaire Azure Network Manager. 

Pour créer une définition et une attribution d’initiative de stratégie Azure pour les ressources Azure Network Manager, créez et déployez un groupe de réseaux avec les configurations nécessaires. Pour mettre à jour une définition d’initiative de stratégie Azure existante ou une attribution correspondante, vous devez modifier et déployer les modifications apportées au groupe de réseaux dans la ressource Azure Virtual Network Manager. Pour supprimer une définition et une attribution d’initiative de stratégie Azure, vous devez annuler le déploiement et supprimer les ressources Azure Virtual Network Manager associées à votre stratégie. Cela peut inclure l’annulation du déploiement d’une configuration, la suppression d’une configuration et la suppression d’un groupe de réseaux. Pour plus d’informations sur la suppression, consultez la [liste de vérification du gestionnaire Azure Virtual Network Manager pour la suppression de composants](concept-remove-components-checklist.md).  

## <a name="next-steps"></a>Étapes suivantes

- Créez une instance [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) en utilisant le Portail Azure.
- Découvrez comment créer une [topologie Hub and Spoke](how-to-create-hub-and-spoke.md) avec Azure Virtual Network Manager.
- Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
