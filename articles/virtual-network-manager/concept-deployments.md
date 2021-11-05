---
title: Déploiements de configuration dans Azure Virtual Network Manager (préversion)
description: En savoir plus sur les déploiements de configuration dans Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bbf38eff6ffc5d24ae566ec94167a1c0c6ff9263
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097813"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Déploiements de configuration dans Azure Virtual Network Manager (préversion)

Dans cet article, vous allez découvrir comment les configurations sont appliquées aux ressources de votre réseau. Vous allez également découvrir comment la mise à jour d’un déploiement de configuration est différent pour chaque type d’appartenance. Ensuite, nous allons entrer dans les détails de l’*état du déploiement* et du *modèle d’état de l’objectif*.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deployment"></a>Déploiement

Le *déploiement* est la méthode utilisée par Azure Virtual Network Manager pour appliquer des configurations à vos réseaux virtuels dans des groupes de réseaux. Les modifications apportées aux groupes réseau, à la connectivité et à la configuration de l’administrateur de sécurité ne prendront effet que si un déploiement a été validé. Lors de la validation d’un déploiement, vous sélectionnez la ou les régions auxquelles la configuration sera appliquée. Quand une demande de déploiement est envoyée à Azure Virtual Network Manager, l’[état de l’objectif](#goalstate) des ressources réseau est calculé et les modifications nécessaires à votre infrastructure sont demandées. Les modifications peuvent prendre environ 15-20 minutes en fonction de la taille de la configuration.

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>Déploiement et types d’appartenance à un groupe de réseaux

Les mises à jour de déploiement sont différentes pour les membres de groupe statiques et dynamiques dans un groupe réseau. Quand vous avez des membres de groupe dynamiques, un modèle d’état d’objectif est utilisé. Azure Virtual Network Manager détermine automatiquement si les membres dynamiques répondent aux exigences de la configuration et s’ajustent sans que vous ayez à redéployer la configuration. Toutefois, si vous avez des réseaux virtuels qui ont été ajoutés en tant que membres statiques, vous devez redéployer la configuration pour appliquer les modifications. Par exemple, si vous ajoutez un nouveau réseau virtuel en tant que membre statique, vous devez redéployer la configuration pour qu’elle prenne effet.

## <a name="deployment-status"></a>état du déploiement

Lorsque vous validez un déploiement de configuration, l’API effectue une opération de publication et vous ne voyez pas l’achèvement du déploiement par la suite. Une fois la demande de déploiement faite, Azure Virtual Network Manager calcule l’état de l’objectif de vos réseaux et demande à l’infrastructure sous-jacente d’effectuer les modifications. Vous pouvez voir l’état du déploiement sur la page *déploiement* de Virtual Network Manager.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Capture d’écran du déploiement en cours dans la liste des déploiements.":::

## <a name="goal-state-model"></a><a name = "goalstate"></a> Modèle d’état de l’objectif

Lorsque vous validez un déploiement de configuration, vous décrivez l’état de l’objectif de la configuration que vous souhaitez comme résultat final. Par exemple, lorsque vous validez les configurations *Config1* et *Config2* dans une région, ces deux configurations sont appliquées. Si vous avez décidé de valider les configuration *Config1* et *Config3* dans la même région, *Config2* sera supprimée et *Config3* sera ajoutée. Pour supprimer toutes les configurations, vous devez déployer une configuration **None** sur la ou les régions pour lesquelles vous ne souhaitez plus appliquer de configurations.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à [créer une instance Azure Virtual Network Manager](create-virtual-network-manager-portal.md).
