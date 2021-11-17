---
title: Visualiser une évaluation du modèle de classification personnalisée - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment visualiser les scores d’évaluation pour un modèle de classification personnalisée
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: d0b708f3b06eaeb458df4e4c9da73e86b451e137
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469646"
---
# <a name="view-the-model-evaluation"></a>Visualiser l’évaluation du modèle

Passer en revue l’évaluation du modèle est une étape importante dans le développement d’un modèle de classification personnalisée. Elle vous aide à découvrir l’efficacité de votre modèle et vous donne une idée de la façon dont il va fonctionner en production. 


## <a name="prerequisites"></a>Prérequis

Avant d’entraîner votre modèle, vous avez besoin des éléments suivants :
* [Un projet de classification personnalisée](create-project.md) avec un compte Stockage Blob Azure configuré. 
* Des données texte qui [ont été chargées](create-project.md#prepare-training-data) sur votre compte de stockage
* [Des données étiquetées](tag-data.md)
* Un [modèle correctement entraîné](train-model.md)

Pour plus d’informations, consultez [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).

## <a name="model-evaluation"></a>Évaluation du modèle

Le processus d’évaluation utilise le modèle entraîné pour prédire les classes définies par l’utilisateur pour les fichiers du jeu de test et les compare aux étiquettes de données fournies. Le jeu de test est constitué de données qui n’ont pas été introduites dans le modèle lors du processus d’entraînement. 

## <a name="view-the-model-details-using-language-studio"></a>Visualiser les détails du modèle en utilisant Language Studio

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio).
    1. Recherchez la section de Language Studio intitulée **Classifier du texte**.
    2. Sélectionnez **Classification de texte personnalisée**. 

2. Sélectionnez **Visualiser les détails du modèle** dans le menu du côté gauche.

3. Visualisez l’état de l’entraînement de votre modèle dans la colonne **État** et le score F1 du modèle dans la colonne **Score F1**.

    :::image type="content" source="../media/model-details-1.png" alt-text="Bouton Visualiser les détails du modèle" lightbox="../media/model-details-1.png":::

1. Cliquez sur le nom du modèle pour plus de détails.

2. Vous pouvez trouver les métriques d’évaluation **au niveau du modèle** sous la section **Vue d’ensemble** et les métriques d’évaluation **au niveau des classes** sous la section **Métriques de performances des entités**. Pour plus d’informations, consultez [Métriques d’évaluation](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics).

    :::image type="content" source="../media/model-details-2.png" alt-text="Métriques de performances du modèle" lightbox="../media/model-details-2.png":::

> [!NOTE]
> Si vous ne trouvez pas toutes les classes affichées ici, cela est dû au fait qu’il n’y avait aucun fichier étiqueté de cette classe dans le jeu de test.

Sous la **matrice de confusion du jeu de test**, vous pouvez trouver la matrice de confusion pour le modèle.

> [!NOTE]
> La matrice de confusion n’est actuellement pas prise en charge pour plusieurs projets de classification d’étiquette.

**Classification avec une seule étiquette**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="Matrice de confusion pour une classification avec une seule classe" lightbox="../media/conf-matrix-single.png":::

<!-- **Multiple Label Classification**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="Confusion matrix for multiple class classification" lightbox="../media/conf-matrix-multi.png"::: -->

## <a name="next-steps"></a>Étapes suivantes

Au moment où vous passez en revue les performances de votre modèle, découvrez les [métriques d’évaluation](../concepts/evaluation.md) utilisées. Une fois que vous savez si les performances de votre modèle doivent être améliorées, vous pouvez commencer à [améliorer le modèle](improve-model.md).
