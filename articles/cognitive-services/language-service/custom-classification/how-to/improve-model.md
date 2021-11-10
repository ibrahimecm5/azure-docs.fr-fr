---
title: Guide pratique pour améliorer les performances d’un modèle de classification de texte personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez comment améliorer un modèle de classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 73513e90f37a3d003351604d119612306ba66277
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062735"
---
# <a name="improve-model-performance"></a>Améliorer les performances du modèle

Une fois que vous avez entraîné votre modèle et que vous avez passé en revue les détails de son évaluation, vous pouvez décider si vous devez en améliorer les performances. Dans cet article, vous allez passer en revue les incohérences entre les classes prédites et les classes étiquetées par le modèle, et examiner la distribution des données.

## <a name="prerequisites"></a>Prérequis

Pour améliorer un modèle, vous devez :

* Avoir [un projet de classification personnalisée](create-project.md) avec un compte Stockage Blob Azure configuré. 
* Avoir des données texte qui ont [été chargées](create-project.md#prepare-training-data) sur votre compte de stockage.
* Avoir [des données étiquetées](tag-data.md) pour [entraîner correctement un modèle](train-model.md)
* Avoir passé en revue les [détails de l’évaluation du modèle](view-model-evaluation.md) pour déterminer les performances de votre modèle.
* Vous être familiarisé avec les [métriques d’évaluation](../concepts/evaluation.md) utilisées pour l’évaluation

Pour plus d’informations, consultez [cycle de vie du développement d’applications](../overview.md#project-development-lifecycle).

## <a name="review-test-set-predictions"></a>Passer en revue les prédictions du jeu de test

En utilisant Language Studio, vous pouvez passer en revue les performances de votre modèle et les comparer aux performances attendues. Vous pouvez passer en revue les classes prédites et les classes étiquetées côte à côte pour chaque modèle que vous avez entraîné.

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio)).
    1. Recherchez la section de Language Studio intitulée **Classifier du texte**.
    2. Sélectionnez **Classification de texte personnalisée**. 

2. Dans le menu de gauche, sélectionnez **Améliorer le modèle**.

3. Sélectionnez **Passer en revue le jeu de test**.

4. Sélectionnez votre modèle entraîné dans la liste déroulante **Modèle**.

5. Pour faciliter l’analyse, vous pouvez passer à **Montrer les prédictions incorrectes uniquement** pour voir seulement les erreurs.

    :::image type="content" source="../media/review-validation-set.png" alt-text="Passer en revue le jeu de validation" lightbox="../media/review-validation-set.png":::

6. Si un fichier qui devrait appartenir à la classe `X` est toujours classifié en tant que classe `Y`, cela signifie qu’il existe une ambiguïté entre ces classes et que vous devez reconsidérer votre schéma.

## <a name="examine-data-distribution-from-language-studio"></a>Examiner la distribution de données à partir de Language Studio

En examinant la distribution des données dans vos fichiers, vous pouvez décider si une classe est sous-représentée. Un déséquilibre des données se produit quand les fichiers utilisés pour l’entraînement ne sont pas distribués de façon égale entre les classes et il introduit un risque sur les performances du modèle. Par exemple, si la *classe 1* a 50 fichiers étiquetés alors que la *classe 2* a seulement 10 fichiers étiquetés, il existe un déséquilibre des données, où la *classe 1* est sur-représentée et la *classe 2* est sous-représentée. 

Dans ce cas, le modèle est biaisé en faveur de la classification de votre fichier en tant que *classe 1* et peut avoir ignoré *classe 2*. Un problème plus complexe peut résulter du déséquilibre des données si le schéma est ambigu. Si les deux classes n’ont pas de distinction claire entre elles et que la *classe 2* est sous-représentée, le modèle va probablement classifier le texte en tant que *classe 1*.

Dans les [métriques d’évaluation](../concepts/evaluation.md), quand une classe est sur-représentée, elle a tendance à avoir un rappel supérieur aux autres classes, tandis que les classes sous-représentées ont un rappel inférieur.

Pour examiner la distribution des données dans votre jeu de données :

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio).
    1. Recherchez la section de Language Studio intitulée **Classifier du texte**.
    2. Sélectionnez **Classification de texte personnalisée**. 

2. Dans le menu de gauche, sélectionnez **Améliorer le modèle**.

3. Sélectionnez **Examiner la distribution des données**.

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="Examiner la distribution des données" lightbox="../media/examine-data-distribution.png":::

4. Revenez à la page **Étiqueter les données** et apportez des ajustements une fois que vous avez une idée de la façon dont vous devez étiqueter vos données différemment.

## <a name="next-steps"></a>Étapes suivantes

* Une fois que vous êtes satisfait du fonctionnement de votre modèle, vous pouvez commencer à [envoyer des demandes de classification de texte](call-api.md) en utilisant l’API d’exécution.
