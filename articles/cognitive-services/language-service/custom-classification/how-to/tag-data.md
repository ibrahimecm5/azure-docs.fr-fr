---
title: Comment étiqueter vos données pour une classification personnalisée - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment étiqueter vos données pour une utilisation avec l’API de classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 390e5fabe5c23c3b258e898eab0691f88c1828c0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057173"
---
# <a name="tag-text-data-for-training-your-model"></a>Étiqueter les données texte pour entraîner votre modèle 

Avant de créer un modèle de classification de texte personnalisé, vous devez d’abord avoir étiqueté les données. Si vos données ne sont pas déjà étiquetées, vous pouvez les étiqueter dans Language Studio. Les données étiquetées informent le modèle de la façon d’interpréter le texte et sont utilisées pour l’entraînement et l’évaluation.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir étiqueter des données, vous avez besoin de :
* [Un projet correctement créé](create-project.md) avec un compte Stockage Blob Azure configuré. 
* Des données texte qui ont [été chargées](create-project.md#prepare-training-data) sur votre compte de stockage.

Pour plus d’informations, consultez [cycle de vie du développement d’applications](../overview.md#project-development-lifecycle).

<!--Tagging your data will let you [train your model](train-model.md), [evaluate it](train-model.md), and use it to [classify text](call-api.md).-->

## <a name="tag-your-data"></a>Étiqueter vos données

Une fois les données d’entraînement chargées sur votre compte Stockage Azure, vous devez les étiqueter pour que votre modèle sache quels mots seront associés aux classes dont vous avez besoin. Quand vous étiquetez des données dans Language Studio (ou quand vous étiquetez manuellement vos données), ces étiquettes sont stockées au [format JSON](../concepts/data-formats.md) que votre modèle va utiliser pendant l’entraînement. 

Quand vous étiquetez vos données, gardez ceci à l’esprit :

* En général, une plus grande quantité de données étiquetées conduit à de meilleurs résultats, à condition que les données soient étiquetées avec précision.

* Bien qu’il soit recommandé d’avoir environ 50 fichiers étiquetés par classe, il n’existe pas de nombre fixe pouvant garantir le fonctionnement optimal de votre modèle, car les performances du modèle dépendent également des ambiguïtés possibles dans votre [schéma](design-schema.md) et de la qualité de vos données étiquetées.

Utilisez les étapes suivantes pour étiqueter vos données.

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/custom-classification).

1. Dans le menu de gauche, sélectionnez **Étiqueter des données**

3. Vous trouverez à gauche une liste de tous les fichiers .txt disponibles dans vos projets. Vous pouvez sélectionner le fichier que vous souhaitez commencer à étiqueter, ou vous pouvez utiliser le bouton Précédent et Suivant en bas de la page pour naviguer.  
    
4.  Vous pouvez afficher tous les fichiers ou seulement les fichiers étiquetés en modifiant la vue dans le menu déroulant **Affichage**. 

    > [!NOTE]
    > Si vous avez activé plusieurs langues pour votre projet, vous voyez un menu déroulant supplémentaire **Langue**. Sélectionnez la langue de chaque document.

5. Avant de commencer à étiqueter, ajoutez des classes à votre projet à partir du coin supérieur droit.


    :::image type="content" source="../media/tag-1.png" alt-text="Capture d’écran montrant l’écran d’étiquetage des données" lightbox="../media/tag-1.png":::

6. Commencez à étiqueter vos fichiers.

    * **Classification avec une seule étiquette** : votre fichier ne peut être étiqueté qu’avec une seule classe. Vous pouvez faire cela en activant une des cases d’option en regard de la classe avec laquelle vous souhaitez étiqueter ce fichier.

      :::image type="content" source="../media/tag-single.png" alt-text="Capture d’écran montrant le menu de classification avec une seule étiquette" lightbox="../media/tag-single.png":::

    * **Classification avec plusieurs étiquettes** : votre fichier peut être étiqueté avec plusieurs classes. Vous pouvez faire cela en activant toutes les cases d’option en regard des classes avec lesquelles vous souhaitez étiqueter ce fichier.

      :::image type="content" source="../media/tag-multi.png" alt-text="Capture d’écran montrant le menu de classification avec plusieurs étiquettes" lightbox="../media/tag-multi.png":::

Lors de l’étiquetage, vos modifications sont synchronisées périodiquement ; si elles n’ont pas encore été enregistrées, un avertissement figure en haut de votre page. Si vous souhaitez enregistrer manuellement, cliquez sur le bouton Enregistrer les étiquettes en haut de la page.

## <a name="remove-tags"></a>Supprimer des étiquettes

Si vous souhaitez supprimer une étiquette, décochez le bouton en regard de la classe.

## <a name="delete-or-classes"></a>Supprimer des classes

Pour supprimer/renommer une classe :

1. Sélectionnez la classe que vous souhaitez modifier dans le menu de droite.
2. Cliquez sur les trois points et sélectionnez l’option souhaitée dans le menu déroulant.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez étiqueté vos données, vous pouvez commencer l’[entraînement d’un modèle](train-model.md) qui va apprendre à partir de vos données.
