---
title: Guide pratique pour effectuer l’entraînement de votre modèle de classification personnalisé - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment entraîner votre modèle pour la classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 466ddd800c70c27787a0a8ebcd8c14a4392e6c2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061936"
---
# <a name="how-to-train-a-text-classification-model"></a>Guide pratique pour entraîner un modèle de classification de texte


L’entraînement est le processus par lequel le modèle apprend à partir de vos [données étiquetées](tag-data.md). Une fois l’entraînement effectué, vous pouvez [utiliser les métriques d’évaluation du modèle](../how-to/view-model-evaluation.md) pour déterminer si vous devez [améliorer votre modèle](../how-to/improve-model.md).

## <a name="prerequisites"></a>Prérequis

Avant d’entraîner votre modèle, vous avez besoin des éléments suivants :

* [Un projet correctement créé](create-project.md) avec un compte Stockage Blob Azure configuré 
* Des données texte qui [ont été chargées](create-project.md#prepare-training-data) sur votre compte de stockage
* [Des données étiquetées](tag-data.md)

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#project-development-lifecycle).

## <a name="data-splits"></a>Fractionnements de données

Avant de commencer le processus d’entraînement, les fichiers de votre jeu de données sont divisés en trois groupes aléatoires :

* Le **jeu d’entraînement** contient 80 % des fichiers de votre jeu de données. Il s’agit du jeu principal utilisé pour entraîner le modèle.

* Le **jeu de test** contient 20 % des fichiers disponibles dans votre jeu de données. Ce jeu est utilisé pour fournir une [évaluation](../how-to/view-model-evaluation.md) non biaisée du modèle. Il n’est pas introduit dans le modèle pendant l’entraînement. Les détails des prédictions correctes et incorrectes pour ce jeu ne sont pas affichés, afin que vous ne réajustiez pas vos données d’entraînement et ne modifiiez pas les résultats.

## <a name="train-model-in-language-studio"></a>Entraîner le modèle dans Language Studio

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/LanguageStudio).

2. Dans le menu de gauche, sélectionnez **Entraîner**.

3. Pour entraîner un nouveau modèle, sélectionnez **Entraîner un nouveau modèle** et tapez le nom du modèle dans la zone de texte ci-dessous. Vous pouvez **remplacer un modèle existant** en sélectionnant cette option et le modèle de votre choix dans la liste déroulante ci-dessous.

    :::image type="content" source="../media/train-model.png" alt-text="Créer un modèle" lightbox="../media/train-model.png":::

4. Sélectionnez le bouton **Train** en bas de la page.

La durée d’entraînement d’un modèle varie selon le jeu de données et peut prendre plusieurs heures. Vous pouvez uniquement entraîner un modèle à la fois, et vous ne pouvez pas créer ou entraîner d’autres modèles si un entraînement est déjà en cours dans le même projet. 


Une fois l’entraînement terminé, gardez ceci à l’esprit :

* [Afficher les détails de l’évaluation du modèle](../how-to/view-model-evaluation.md) Après l’entraînement du modèle, l’évaluation du modèle est effectuée par rapport au [jeu de test](../how-to/train-model.md#data-splits), qui n’a pas été introduit dans le modèle au cours de l’entraînement. En affichant l’évaluation, vous pouvez avoir une idée de la façon dont le modèle s’exécute dans les scénarios réels.

* [Examiner la distribution de données](../how-to/improve-model.md#examine-data-distribution-from-language-studio) Assurez-vous que toutes les classes sont bien représentées et que vous disposez d’une distribution de données équilibrée pour être certain que toutes vos classes sont correctement représentées. Si une classe spécifique est étiquetée beaucoup moins fréquemment que les autres, cette classe est probablement sous-représentée et la plupart des occurrences ne seront probablement pas reconnues correctement par le modèle au moment de l’exécution. Dans ce cas, ajoutez d’autres fichiers appartenant à cette classe à votre jeu de données.

* [Améliorer les performances (facultatif)](../how-to/improve-model.md) En dehors de la modification des [données étiquetées](tag-data.md) en fonction de l’analyse des erreurs, vous souhaiterez peut-être augmenter le nombre d’étiquettes pour les types d’entités aux performances non satisfaisantes ou améliorer la diversité de vos données étiquetées. Cela permettra à votre modèle d’apprendre à donner des prédictions correctes, sur des phénomènes linguistiques potentiels qui provoquent une défaillance.

<!-- * Define your own test set: If you are using a random split option and the resulting test set was not comprehensive enough, consider defining your own test to include a variety of data layouts and balanced tagged classes.
 -->


## <a name="next-steps"></a>Étapes suivantes

Une fois l’entraînement effectué, vous pouvez utiliser les [métriques d’évaluation du modèle](../how-to/view-model-evaluation.md) pour éventuellement [améliorer votre modèle](../how-to/improve-model.md). Dès que vous êtes satisfait de votre modèle, vous pouvez le déployer et le rendre disponible pour la [classification de texte](call-api.md).
