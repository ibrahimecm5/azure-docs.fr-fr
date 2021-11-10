---
title: Hub de gestion
description: Gérez vos connexions, la configuration du contrôle de code source et les propriétés de création globales dans le hub de gestion d’Azure Data Factory
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: joshuha-msft
ms.author: joowen
ms.date: 04/27/2021
ms.openlocfilehash: 9c25ccdababc228be0f3509084fdba8090a1b649
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844617"
---
# <a name="management-hub-in-azure-data-factory"></a>Hub de gestion dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le hub de gestion, accessible via l’onglet *Gérer* dans l’expérience utilisateur Azure Data Factory, est un portail qui héberge des actions de gestion globale pour votre fabrique de données. Ici, vous pouvez gérer vos connexions aux banques de données, aux calculs externes, à la configuration du contrôle de code source et aux paramètres de déclencheur.

## <a name="manage-connections"></a>Gérer des connexions

### <a name="linked-services"></a>Services liés

Les services liés définissent les informations de connexion pour Azure Data Factory pour se connecter à des magasins de données et à des environnements de calcul externes. Pour plus d’informations, consultez [Concepts des services liés](concepts-linked-services.md). La création, la modification et la suppression de services liés s’effectuent dans le hub de gestion.

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="Gérer des services liés":::

### <a name="integration-runtimes"></a>Runtimes d’intégration

Un runtime d’intégration est une infrastructure de calcul qu’Azure Data Factory utilise pour fournir des fonctionnalités d’intégration de données dans différents environnements réseau. Pour plus d’informations, consultez [Concepts de runtime d’intégration](concepts-integration-runtime.md). Le hub de gestion vous permet de créer, supprimer et surveiller vos runtimes d’intégration.

:::image type="content" source="media/author-management-hub/management-hub-integration-runtime.png" alt-text="Runtimes d’intégration":::

## <a name="manage-source-control"></a>Gérer le contrôle de code source

### <a name="git-configuration"></a>Configuration Git

Vous pouvez afficher/modifier toutes les informations relatives à Git sous les paramètres de configuration Git dans le hub de gestion. 

Les informations sur la dernière validation publiée sont également listées et peuvent aider à comprendre la validation exacte dernièrement publiée/déployée dans tous les environnements. Elles peuvent également être utiles lors de l’application de correctifs à chaud en production.

Pour plus d’informations, découvrez le [contrôle de code source dans Azure Data Factory](source-control.md).

:::image type="content" source="media/author-management-hub/management-hub-git.png" alt-text="Gérer le dépôt git":::

### <a name="parameterization-template"></a>Modèle de paramétrage

Pour remplacer les paramètres modèle Resource Manager générés lors de la publication à partir de la branche de collaboration, vous pouvez générer ou modifier un fichier de paramètres personnalisés. Pour plus d’informations, découvrez comment [utiliser des paramètres personnalisés dans le modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md). Le modèle de paramétrage est disponible uniquement lorsque vous travaillez dans un dépôt git. Si le fichier *arm-template-parameters-definition.json* n’existe pas dans la branche de travail, la modification du modèle par défaut a pour effet de le générer.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="Gérer les paramètres personnalisés":::

## <a name="manage-authoring"></a>Gérer la création

### <a name="triggers"></a>Déclencheurs

Les déclencheurs déterminent à quel moment l’exécution d’un pipeline doit être lancée. Actuellement, les déclencheurs peuvent figurer dans une planification d’horloge, opérer à intervalles réguliers ou dépendre d’un événement. Pour plus d’informations, découvrez comme [déclencher une exécution](concepts-pipeline-execution-triggers.md#trigger-execution). Le hub de gestion vous permet de créer, modifier, supprimer ou afficher l’état actuel d’un déclencheur.

:::image type="content" source="media/author-management-hub/management-hub-triggers.png" alt-text="Capture d’écran montrant où créer, modifier, supprimer ou afficher l’état actuel d’un déclencheur.":::

### <a name="global-parameters"></a>Paramètres globaux

Les paramètres globaux représentent des constantes d’une fabrique de données, qui peuvent être consommées par un pipeline dans n’importe quelle expression. Pour plus d’informations, consultez [Paramètres globaux](author-global-parameters.md).

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="Créer des paramètres globaux":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer un dépôt git](source-control.md) pour votre ADF


