---
title: Qu’est-ce que Custom Vision ?
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser le service Azure Custom Vision pour créer des modèles AI personnalisés afin de détecter des objets ou de classifier des images.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 08/25/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconnaissance d’image, identificateur d’image, application de reconnaissance d’image, vision personnalisée
ms.openlocfilehash: 6bf2f9de103416bc9dd35216bf85c609be966a23
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372508"
---
# <a name="what-is-custom-vision"></a>Qu’est-ce que Custom Vision ?

Azure Custom Vision est un service de reconnaissance d’image qui vous permet de créer, de déployer et d’améliorer vos propres modèles d’identificateurs d’images. Un identificateur d’images applique des étiquettes (représentant des classifications ou des objets) à des images en fonction de leurs caractéristiques visuelles détectées. Contrairement au service [Vision par ordinateur](../computer-vision/overview.md), le service Custom Vision vous permet de spécifier vos propres étiquettes et d’entraîner des modèles personnalisés pour les détecter.

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./getting-started-build-a-classifier.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps.
* Les [guides patiques](./test-your-model.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [tutoriels](./iot-visual-alerts-tutorial.md) sont des guides plus longs qui montrent comment utiliser ce service en tant que composant dans des solutions métier plus larges.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.-->

## <a name="what-it-does"></a>Résultat

Le service Custom Vision utilise un algorithme de Machine Learning pour analyser les images. En tant que développeur, vous envoyez des groupes d’images présentant les caractéristiques en question ainsi que des groupes d’images n’en disposant pas. Vous étiquetez les images vous-même au moment de la soumission. L’algorithme s’entraîne ensuite avec ces données et calcule sa propre justesse en se testant lui-même à l’aide de ces mêmes images. Une fois que vous avez entraîné l’algorithme, vous pouvez le tester, le réentraîner, puis l’utiliser dans votre application de reconnaissance d’images pour [classifier des images](getting-started-build-a-classifier.md). Vous pouvez également [exporter le modèle](export-your-model.md) en question pour l’utiliser en mode hors connexion.

### <a name="classification-and-object-detection"></a>Classification et détection d’objet

Les fonctionnalités du service Vision personnalisée peuvent être divisées en deux fonctionnalités. La **[classification d’images](getting-started-build-a-classifier.md)** applique une ou plusieurs étiquettes à une image. La **[détection d’objet](get-started-build-detector.md)** est similaire, mais elle retourne également les coordonnées dans l’image où se trouvent la ou les étiquettes appliquées.

### <a name="optimization"></a>Optimization

Le service Custom Vision est optimisé pour reconnaître rapidement les principales différences entre les images. Vous pouvez ainsi commencer à prototyper votre modèle avec une petite quantité de données. Il est généralement conseillé de commencer avec 50 images par étiquette. Toutefois, le service n’est pas optimal pour la détection de différences subtiles au sein des images (par exemple, la détection de petites fissures ou de petits trous dans des scénarios d’assurance qualité).

En outre, vous pouvez choisir parmi plusieurs variantes d’algorithme de Custom Vision qui sont optimisées pour certains sujets (par exemple, des points de repère ou des articles vendus au détail). Pour plus d’informations, consultez [Sélectionner un domaine](select-domain.md).

## <a name="what-it-includes"></a>Contenu

Le service Custom Vision est disponible sous la forme d’un ensemble de kits SDK natifs ainsi que via une interface web sur le [site web du service Custom Vision](https://customvision.ai/). Vous pouvez créer, tester et entraîner un modèle avec une interface ou les deux.

![Site web du service Custom Vision dans une fenêtre du navigateur Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision personnalisée doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez le guide [Créer un classifieur](getting-started-build-a-classifier.md) pour découvrir comment utiliser le service Custom Vision sur le portail web. Sinon, vous pouvez suivre un [guide de démarrage rapide](quickstarts/image-classification.md) pour implémenter les scénarios de base dans le code.