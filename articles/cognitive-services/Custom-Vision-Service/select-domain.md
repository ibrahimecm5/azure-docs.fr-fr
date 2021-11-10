---
title: Sélectionner un domaine pour un projet Custom Vision - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Cet article vous explique comment sélectionner un domaine pour votre projet dans le service Custom Vision.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 5838185328920587b11bb66d21c9402ce93e9b6b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458244"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Sélectionner un domaine pour un projet Custom Vision

Sous l’onglet Paramètres de votre projet Custom Vision, vous pouvez sélectionner un domaine pour votre projet. Choisissez le domaine qui se rapproche le plus de votre scénario. Si vous accédez à Custom Vision par le biais d’une bibliothèque cliente ou d’une API REST, vous devez spécifier un ID de domaine lors de la création du projet. Pour obtenir la liste des ID de domaine, consultez la page [Obtenir des domaines](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab) ou utilisez le tableau ci-dessous.

## <a name="image-classification"></a>Classification d’image

|Domain|Objectif|
|---|---|
|__Généralités__| Optimisé pour un large éventail de tâches de classification d’images. Si aucun autre domaine spécifique n’est approprié, ou si vous hésitez sur le choix du domaine, sélectionnez un des domaines généraux. ID : `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__General [A1]__| Optimisé pour une meilleure précision avec un temps d’inférence comparable à celui du domaine General. Recommandé pour les jeux de données volumineux ou les scénarios utilisateur plus difficiles. Ce domaine nécessite un entraînement plus long. ID : `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Général [A2]__| Optimisé pour une meilleure précision avec un temps d’inférence plus rapide que les domaines Général [A1] et Général. Recommandé pour la plupart des jeux de données. Ce domaine nécessite moins de temps d’apprentissage que les domaines Général et Général [A1]. ID : `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Food__ (Nourriture)|Optimisé pour les photographies de plats, tels que vous pouvez les voir dans un menu de restaurant. Si vous souhaitez classer des photographies de fruits ou de légumes distincts, utilisez le domaine Food (Nourriture). ID : `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Landmarks__ (Monuments et sites)|Optimisé pour les monuments et sites reconnaissables, naturels et artificiels. Ce domaine fonctionne mieux lorsque le monument ou le site est clairement visible dans la photographie. Ce domaine fonctionne même si le monument ou le site est légèrement masqué par des personnes placées devant lui. ID : `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail__ (Commerce)|Optimisé pour les images qui se trouvent dans des catalogues de vente ou sur des site Web commerciaux. Si vous souhaitez un classement de grande précision pour des robes, des pantalons et des chemises, utilisez ce domaine. ID : `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Compact Domains__ (Domaines compacts)| Optimisés en fonction des contraintes liées à la classification en temps réel sur les périphériques.|


> [!NOTE]
> Les domaines Général[A1] et Général[A2] peuvent être utilisés pour un large éventail de scénarios et sont optimisés pour la précision. Utilisez le modèle Général[A2] pour améliorer la vitesse d’inférence et avoir un temps d’apprentissage réduit. Pour les jeux de données plus volumineux, vous souhaiterez peut-être utiliser Général[A1] pour obtenir une meilleure précision que Général[A2], bien que cela nécessite davantage de temps d’apprentissage et d’inférence. Le modèle Général requiert plus de temps d’inférence que Général[A1] et Général[A2].

## <a name="object-detection"></a>Détection d’objets

|Domain|Objectif|
|---|---|
|__Généralités__| Optimisé pour un large éventail de tâches de détection d’objets. Si aucun autre domaine n’est approprié, ou si vous hésitez sur le choix du domaine, sélectionnez le domaine Général. ID : `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__General [A1]__| Optimisé pour une meilleure justesse avec un temps d’inférence comparable à celui du domaine General. Recommandé pour des besoins de localisation de région plus précis, des jeux de données plus importants ou des scénarios utilisateur plus difficiles. Ce domaine nécessite un entraînement plus long, et les résultats ne sont pas déterministes : attendez-vous à une différence au niveau de la moyenne des précisions moyennes (mAP) de +-1 % avec les mêmes données d’entraînement fournies. ID : `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Optimisé pour rechercher des logos de marque dans les images. ID : `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Products on shelves__ (Produits en rayon)|Optimisé pour la détection et la classification des produits en rayon. ID : `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Compact Domains__ (Domaines compacts)| Optimisés en fonction des contraintes liées à la détection d'objets en temps réel sur les périphériques.|

## <a name="compact-domains"></a>Domaines compacts

Les modèles générés par les domaines compacts sont exportables pour s’exécuter localement. Dans l’API Custom Vision 3.4 en préversion publique, vous pouvez obtenir la liste des plateformes exportables pour les domaines compacts en appelant l’API GetDomains.

Tous les domaines suivants prennent en charge l’exportation aux formats ONNX, TensorFlow, TensorFlowLite, TensorFlow.js, CoreML et VAIDK, à l’exception du domaine **Object Detection General (compact)** qui ne prend pas en charge le format VAIDK.

Les performances du modèle varient selon le domaine sélectionné. Le tableau ci-dessous spécifie la taille du modèle et le temps d'inférence sur processeur Intel Desktop et GPU NVidia \[1\]. Ces chiffres n'incluent pas les délais de prétraitement et de post-traitement.

|Tâche|Domain|id|Taille des modèles|Temps d'inférence sur processeur|Temps d'inférence sur GPU|
|---|---|---|---|---|---|
|classification ;|General (compact) (Général (compact))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 Mo|10 ms|5 ms|
|classification ;|General (compact) [S1] (Général (compact) [S1])|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 Mo|50 ms|5 ms|
|Détection d’objets|General (compact) (Général (compact))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 Mo|35 ms|5 ms|
|Détection d’objets|General (compact) [S1] (Général (compact) [S1])|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 Mo|27 ms|7 ms|

>[!NOTE]
>Le domaine __General (compact)__ (Général (compact)) disponible pour la détection d'objets requiert une logique de post-traitement particulière. Pour plus d'informations, un exemple de script est disponible dans le package zip exporté. Si vous avez besoin d'un modèle sans logique de post-traitement, utilisez __General (compact) [S1]__ (Général (compact) [S1]).

>[!IMPORTANT]
>Rien ne garantit que les modèles exportés donneront exactement les mêmes résultats que l'API de prédiction du cloud. Une légère différence en termes de plateforme exécutée ou d'implémentation du prétraitement peut donner des résultats très différents avec les modèles. Pour plus d'informations sur la logique de prétraitement, consultez [ce document](quickstarts/image-classification.md).

\[1\] Processeur Intel Xeon E5-2690 et GPU NVIDIA Tesla M60
