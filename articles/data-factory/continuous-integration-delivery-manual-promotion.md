---
title: Promotion manuelle des modèles de Gestionnaire des ressources
description: Découvrez comment promouvoir manuellement un modèle de Gestionnaire des ressources dans plusieurs environnements avec intégration et remise continues dans Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485ace0d04375a406c8013d60d45635a596964a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319502"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>Promouvoir manuellement un modèle Resource Manager pour chaque environnement

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Utilisez les étapes ci-dessous pour promouvoir un modèle de Gestionnaire des ressources dans chaque environnement pour l’intégration et la remise continues dans Azure Data Factory.

## <a name="steps-to-manually-promote-a-template"></a>Étapes pour promouvoir manuellement un modèle

1. Accédez à **Gérer** le hub dans votre fabrique de données, puis sélectionnez **Modèle ARM** dans la section « Contrôle de code source ». Sous la section **Modèle ARM**, sélectionnez **Exporter un modèle ARM** pour exporter le modèle Resource Manager destiné à votre fabrique de données dans l’environnement de développement.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="Exporter un modèle Resource Manager":::

1. Dans vos fabriques de données de test et de production, sélectionnez **Importer un modèle ARM**. Cette action ouvre le portail Azure, dans lequel vous pouvez importer le modèle exporté. Sélectionnez **Créer votre propre modèle dans l’éditeur** pour ouvrir l’éditeur de modèle Resource Manager.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="Créer votre propre modèle"::: 

1. Sélectionnez **Charger le fichier**, puis sélectionnez le modèle Resource Manager généré. Il s’agit du fichier **arm_template.json** situé dans le fichier .zip exporté à l’étape 1.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="Modifier un modèle":::

1. Dans la section des paramètres, entrez les valeurs de configuration telles que les informations d’identification du service lié, requises pour le déploiement. Lorsque vous avez terminé, sélectionnez **Vérifier + créer** pour déployer le modèle Resource Manager.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="Section Paramètres":::

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’intégration et de la livraison continues](continuous-integration-delivery.md)
- [Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Utiliser des paramètres personnalisés avec un modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Modèles Resource Manager liés](continuous-integration-delivery-linked-templates.md)
- [Utilisation d’un environnement de production de correctif logiciel](continuous-integration-delivery-hotfix-environment.md)
- [Exemple de script de pré-déploiement et de post-déploiement](continuous-integration-delivery-sample-script.md)