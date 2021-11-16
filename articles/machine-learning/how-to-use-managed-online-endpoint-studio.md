---
title: Utiliser des points de terminaison en ligne managés (préversion) dans le studio
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et utiliser des points de terminaison en ligne managés (préversion) avec le studio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2, studio
ms.author: ssambare
author: shivanissambare
ms.reviewer: laobri
ms.date: 10/21/2021
ms.openlocfilehash: fee2a8211b90c2b7dbc06a1e64f047e28031eaa6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560377"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio

Découvrez comment utiliser le studio pour créer et gérer vos points de terminaison en ligne managés (préversion) dans Azure Machine Learning. Utilisez des points de terminaison en ligne managés pour simplifier les déploiements en production. Pour plus d’informations sur les points de terminaison en ligne managés, consultez la [présentation des points de terminaison](concept-endpoints.md).

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un point de terminaison en ligne managé
> * Afficher des points de terminaison en ligne managés
> * Ajouter un déploiement à un point de terminaison en ligne managé
> * Mettre à jour des points de terminaison en ligne managés
> * Supprimer des points de terminaison en ligne managés et des déploiements

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis
- Un espace de travail Azure Machine Learning. Pour plus d’informations, voir la page [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).
- L’exemple de référentiel – Clonez l’[exemple de référentiel AzureML](https://github.com/Azure/azureml-examples). Cet article utilise les ressources figurant dans `/cli/endpoints/online`.

## <a name="create-a-managed-online-endpoint-preview"></a>Créer un point de terminaison en ligne managé (préversion)

Utilisez le studio pour créer un point de terminaison en ligne managé (préversion) directement dans votre navigateur. Quand vous créez un point de terminaison en ligne managé dans le studio, vous devez définir un déploiement initial. Vous ne pouvez pas créer un point de terminaison managé en ligne vide.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez **+ Créer (préversion)** .

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="Capture d’écran montrant la création d’un point de terminaison en ligne managé à partir de l’onglet Points de terminaison.":::

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" lightbox="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" alt-text="Capture d’écran d’un assistant Création de point de terminaison en ligne managé.":::

### <a name="follow-the-setup-wizard-to-configure-your-managed-online-endpoint"></a>Suivez l’Assistant Installation pour configurer votre point de terminaison en ligne managé.

1. Vous pouvez utiliser notre exemple de [modèle](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1/model) et le [script de scoring](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py) de [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1)
1. À l’étape **Environnement** de l’Assistant, vous pouvez sélectionner l’environnement organisé **AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference**.

Vous pouvez aussi créer un point de terminaison en ligne managé à partir de la page **Modèles** dans le studio. Il s’agit d’un moyen simple d’ajouter un modèle à un déploiement en ligne managé existant.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Modèles**.
1. Sélectionnez un modèle en cochant le cercle à côté de son nom.
1. Sélectionner **Déployer** > **Déployer sur le point de terminaison en temps réel (préversion)** .

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" alt-text="Capture d’écran montrant la création d’un point de terminaison en ligne managé à partir de l’interface utilisateur Modèles.":::

## <a name="view-managed-online-endpoints-preview"></a>Afficher des points de terminaison en ligne managés (préversion)

Vous pouvez afficher vos points de terminaison en ligne managés (préversion) dans la page **Points de terminaison**. Utilisez la page des détails du point de terminaison pour trouver des informations critiques, comme l’URI du point de terminaison, son état, les outils de test, les moniteurs d’activité, les journaux de déploiement et l’exemple de code de consommation :

1. Dans la barre de navigation gauche, sélectionnez **Points de terminaison**.
1. (Facultatif) Créez un **filtre** sur le **type de calcul** pour afficher uniquement les types de calcul **managés**.
1. Sélectionnez un nom de point de terminaison pour en afficher la page des détails.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" alt-text="Capture d’écran de l’affichage des détails du point de terminaison managé.":::

### <a name="test"></a>Test

Utilisez l’onglet **Test** de la page des détails du point de terminaison pour tester votre déploiement en ligne managé. Entrez l’exemple d’entrée et affichez les résultats.

1. Sélectionnez l’onglet **Test** dans la page des détails du point de terminaison.
1. Utilisez la liste déroulante pour sélectionner le déploiement à tester.
1. Entrez l’exemple d’entrée.
1. Sélectionnez **Test**.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" lightbox="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="Capture d’écran montrant un test de déploiement en fournissant des exemples de données directement dans votre navigateur.":::

### <a name="monitoring"></a>Surveillance

Utilisez l’onglet **Supervision** pour afficher les graphes généraux du moniteur d’activité pour votre point de terminaison en ligne managé.

Pour utiliser l’onglet Supervision, vous devez sélectionner **Activer la collecte de données et de diagnostics Application Insights** quand vous créez votre point de terminaison.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="Capture d’écran montrant la surveillance des métriques au niveau du point de terminaison dans le studio.":::

Pour plus d’informations sur la manière d’afficher d’autres moniteurs et alertes, consultez [Guide pratique pour superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md).

## <a name="add-a-deployment-to-a-managed-online-endpoint"></a>Ajouter un déploiement à un point de terminaison en ligne managé

Vous pouvez ajouter un déploiement à votre point de terminaison en ligne managé existant.

Dans la **page Détails du point de terminaison**

1. Sélectionnez le bouton **+ Ajouter un déploiement** dans la [page des détails du point de terminaison](#view-managed-online-endpoints-preview).
2. Suivez les instructions pour terminer le déploiement.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" alt-text="Capture d’écran montrant l’option Ajouter un déploiement de la page Détails du point de terminaison.":::

Vous pouvez aussi utiliser la page **Modèles** pour ajouter un déploiement :

1. Dans la barre de navigation gauche, sélectionnez la page **Modèles**.
1. Sélectionnez un modèle en cochant le cercle à côté de son nom.
1. Sélectionner **Déployer** > **Déployer sur le point de terminaison en temps réel (préversion)** .
1. Choisissez de déployer sur un point de terminaison en ligne managé existant.

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" lightbox="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" alt-text="Capture d’écran montrant l’option Ajouter un déploiement de la page Modèles.":::

> [!NOTE]
> Vous pouvez ajuster l’équilibre du trafic entre les déploiements dans un point de terminaison lors de l’ajout d’un nouveau déploiement.
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" lightbox="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="Capture d’écran montrant comment utiliser des curseurs pour contrôler la distribution du trafic entre plusieurs déploiements.":::

## <a name="update-managed-online-endpoints-preview"></a>Mettre à jour des points de terminaison en ligne managés (préversion)

Vous pouvez mettre à jour le pourcentage de trafic de déploiement et le nombre d’instances du studio Azure Machine Learning.

### <a name="update-deployment-traffic-allocation"></a>Mettre à jour l’allocation du trafic de déploiement

Utilisez l’**allocation du trafic de déploiement** pour contrôler le pourcentage de requêtes entrantes qui parviennent à chaque déploiement dans un point de terminaison.

1. Dans la page des détails du point de terminaison, sélectionnez **Mettre à jour le trafic**.
2. Ajustez votre trafic et sélectionnez **Mettre à jour**.

> [!TIP]
> Le **pourcentage de trafic total** doit être égal à 0 % (pour désactiver le trafic) ou à 100 % (pour activer le trafic).

### <a name="update-deployment-instance-count"></a>Mettre à jour le nombre d’instances de déploiement

Utilisez les instructions suivantes pour effectuer un scale-up ou un scale-down d’un déploiement individuel en ajustant le nombre d’instances :

1. Dans la page des détails du point de terminaison. Recherchez la carte du déploiement à mettre à jour.
1. Sélectionnez l’**icône de modification** dans la carte détaillée du déploiement.
1. Mettez à jour le nombre d’instances.
1. Sélectionnez **Mettre à jour**.

## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>Supprimer des points de terminaison en ligne managés et des déploiements (préversion)

Découvrez comment supprimer un point de terminaison en ligne managé dans son ensemble (préversion) et ses déploiements associés (préversion). Ou supprimez un déploiement individuel d’un point de terminaison en ligne managé.

### <a name="delete-a-managed-online-endpoint"></a>Supprimer un point de terminaison en ligne managé

La suppression d’un point de terminaison en ligne managé supprime également tous ses déploiements associés.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez un point de terminaison en cochant le cercle à côté de son nom.
1. Sélectionnez **Supprimer**.

Vous pouvez aussi supprimer un point de terminaison en ligne managé directement dans la [page des détails du point de terminaison](#view-managed-online-endpoints-preview). 

### <a name="delete-an-individual-deployment"></a>Supprimer un déploiement individuel

Effectuez les étapes suivantes pour supprimer un déploiement individuel d’un point de terminaison en ligne managé. Cette suppression affecte les autres déploiements inclus dans le point de terminaison en ligne managé :

> [!NOTE]
> Vous ne pouvez pas supprimer un déploiement qui a du trafic alloué. Vous devez d’abord [définir l’allocation du trafic](#update-deployment-traffic-allocation) de ce déploiement à 0 % pour pouvoir le supprimer.

1. Accédez au [studio Azure Machine Learning](https://ml.azure.com).
1. Dans la barre de navigation gauche, sélectionnez la page **Points de terminaison**.
1. Sélectionnez votre point de terminaison en ligne managé.
1. Dans la page des détails du point de terminaison, recherchez le déploiement à supprimer.
1. Sélectionnez l’**icône de suppression**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser des points de terminaison en ligne managés Azure Machine Learning. Consultez ces prochaines étapes :

- [Que sont les points de terminaison ?](concept-endpoints.md)
- [Guide pratique pour déployer des points de terminaison en ligne managés avec Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Guide pratique pour superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md)
- [Résolution des problèmes de déploiement et de scoring de points de terminaison en ligne managés (préversion)](./how-to-troubleshoot-online-endpoints.md)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)