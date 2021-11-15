---
title: Tester et recycler un modèle - Service Vision personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez comment tester une image, puis l’utiliser pour reformer votre modèle dans le service Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: b326a610d3ac946d8fd610b072e882df3a4b5281
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450454"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Tester et recycler un modèle avec Service Vision personnalisée

Une fois votre modèle formé, vous pouvez le tester rapidement à l’aide d’une image en ligne ou stockée en local. Le test utilise l’itération la plus récemment formée de votre modèle.

## <a name="test-your-model"></a>Tester votre modèle

1. Sur la [page web Service Vision personnalisée](https://customvision.ai), sélectionnez votre projet. Sélectionnez **Quick Test** (Test rapide) à droite de la barre de menus supérieure. Cette action ouvre une fenêtre intitulée **Quick Test** (Test rapide).

    ![Le bouton Quick Test (Test rapide) s’affiche dans le coin supérieur droit de la fenêtre.](./media/test-your-model/quick-test-button.png)

2. Dans la fenêtre **Quick Test** (Test rapide), cliquez sur le champ **Submit Image** (Envoyer une image) et entrez l’URL de l’image que vous souhaitez utiliser pour votre test. Si vous souhaitez utiliser une image stockée localement à la place, cliquez sur le bouton **Parcourir les fichiers locaux** et sélectionnez un fichier d’image local.

    ![Image de la page d’envoi d’image](./media/test-your-model/submit-image.png)

L’image que vous sélectionnez apparaît au milieu de la page. Les résultats s’affichent ensuite sous l’image, sous la forme d’un tableau avec deux colonnes, intitulées **Balises** et **Confiance**. Après avoir consulté les résultats, vous pouvez fermer la fenêtre **Quick Test** (Test rapide).

Vous pouvez maintenant ajouter cette image de test à votre modèle, puis le recycler.

## <a name="use-the-predicted-image-for-training"></a>Utiliser l’image prévue pour former le modèle

Pour utiliser l’image précédemment envoyée pour former le modèle, procédez comme suit :

1. Pour voir les images soumises au classifieur, ouvrez la [page web Custom Vision](https://customvision.ai) et sélectionnez l’onglet __Predictions__.

    ![Image de l’onglet des prédictions](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > La vue par défaut montre des images à partir de l’itération active. Vous pouvez utiliser la zone déroulante __Iteration__ pour voir les images envoyées au cours des précédentes itérations.

2. Pointez sur une image pour lire les mots clés qui ont été prédits par le classifieur.

    > [!TIP]
    > Les images étant classées par ordre de priorité, celles pouvant apporter des avantages plus importants pour le classifieur sont placées en haut. Pour sélectionner un tri différent, utilisez la section __Trier__.

    Pour ajouter une image à vos données pour former le modèle, sélectionnez l’image, sélectionnez la balise, puis __Save and close__ (Enregistrer et fermer). L’image est supprimée des __prédictions__ et ajoutée aux images d’entraînement. Vous pouvez la voir en sélectionnant l’onglet __Training Images__ (Images d’entraînement).

    ![Image de la page d’étiquetage](./media/test-your-model/tag-image.png)

3. Utilisez le bouton __Train__ (Entraîner) pour ré-entraîner le classifieur.

## <a name="next-steps"></a>Étapes suivantes

[Améliorer un modèle](getting-started-improving-your-classifier.md)
