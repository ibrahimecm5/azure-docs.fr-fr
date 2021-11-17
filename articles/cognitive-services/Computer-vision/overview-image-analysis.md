---
title: Présentation du service Analyse d'images
titleSuffix: Azure Cognitive Services
description: Le service Analyse d'images utilise des modèles IA dont l'apprentissage a déjà été effectué pour extraire de nombreuses caractéristiques visuelles différentes des images.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
keywords: vision par ordinateur, applications de vision par ordinateur, service vision par ordinateur
ms.openlocfilehash: 46e01af8fb0022e2e003565a07dca6e7e2713dda
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372565"
---
# <a name="what-is-image-analysis"></a>Présentation du service Analyse d'images

Le service Analyse d'images de Vision par ordinateur peut extraire un large éventail de caractéristiques visuelles à partir de vos images. Par exemple, il peut déterminer si une image contient du contenu pour adultes, identifier des marques ou des objets spécifiques, ou trouver des visages humains.

Vous pouvez utiliser le service Analyse d'images via un kit SDK de bibliothèque de client ou en appelant directement l'[REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005). Pour vous lancer, suivez le [guide de démarrage rapide](quickstarts-sdk/image-analysis-client-library.md).

> [!div class="nextstepaction"]
> [Prise en main](quickstarts-sdk/image-analysis-client-library.md)

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./quickstarts-sdk/image-analysis-client-library.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps. 
* Les [guides patiques](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [articles conceptuels](concept-tagging-images.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.
* Les [tutoriels](./tutorials/storage-lab-tutorial.md) sont des guides plus longs qui montrent comment utiliser ce service en tant que composant dans des solutions métier plus larges.

## <a name="image-analysis-features"></a>Fonctionnalités d'Analyse d'images

Vous pouvez analyser des images pour fournir des insights sur leurs caractéristiques visuelles. Toutes les fonctionnalités répertoriées dans la liste ci-dessous sont fournies par l'API [Analyse d'images](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b). Pour bien démarrer, suivez un [guide de démarrage rapide](./quickstarts-sdk/image-analysis-client-library.md).


### <a name="tag-visual-features"></a>Identifier les composants visuels à l’aide de balises

Identifier les composants visuels d’une image et les marquer à l’aide de balises à partir d’un ensemble de milliers d’objets, d’êtres vivants, de scènes et d’actions reconnaissables. Quand les étiquettes sont ambigües ou inhabituelles, la réponse de l’API fournit des conseils pour clarifier le contexte de l’étiquette. Le balisage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc. [Identifier les composants visuels à l’aide de balises](concept-tagging-images.md)

:::image type="content" source="Images/tagging.png" alt-text="Image d’un skateur avec étiquettes répertoriées à droite":::


### <a name="detect-objects"></a>Détecter des objets

La détection d’objets est similaire au balisage, mais l’API retourne les coordonnées de cadre englobant pour chaque balise appliquée. Par exemple, si une image contient un chien, un chat et une personne, l’opération de détection liste ces objets ainsi que leurs coordonnées dans l’image. Vous pouvez utiliser cette fonctionnalité pour traiter d’autres relations entre les objets dans une image. Elle vous permet également de savoir quand il existe plusieurs instances de la même balise dans une image. [Détecter des objets](concept-object-detection.md)

:::image type="content" source="Images/detect-objects.png" alt-text="Image d’un bureau avec un rectangle autour d’un ordinateur portable":::


### <a name="detect-brands"></a>Détecter les marques

Identifiez les marques commerciales dans les images ou vidéos à partir d’une base de données de milliers de logos internationaux. Vous pouvez utiliser cette fonctionnalité, par exemple, pour déterminer quelles marques sont les plus populaires sur les réseaux sociaux, et lesquelles prédominent dans les médias. [Détecter les marques](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Catégoriser une image

Identifier et catégoriser une image dans son ensemble en utilisant une [taxonomie des catégories](Category-Taxonomy.md) comprenant des hiérarchies héréditaires parent/enfant. Les catégories peuvent être utilisées seules ou avec nos nouveaux modèles de balisage.<br/>Actuellement, l’anglais est la seule langue prise en charge pour le balisage et la catégorisation des images. [Catégoriser une image](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Décrire une image

Générer une description de l’intégralité d’une image dans un langage lisible utilisant des phrases complètes. Les algorithmes du service Vision par ordinateur génèrent différentes descriptions selon les objets identifiés dans l’image. Chacune des descriptions est évaluée, et un score de confiance est généré. Une liste est ensuite renvoyée, classée du score de confiance plus élevé au plus bas. [Décrire une image](concept-describing-images.md)

:::image type="content" source="Images/description.png" alt-text="Image de vaches avec description simple à droite":::

### <a name="detect-faces"></a>Détecter des visages

Détecter les visages dans une image et fournir des informations sur chaque visage détecté. Le service Vision par ordinateur indique les coordonnées, le rectangle, le sexe et l’âge pour chaque visage détecté.<br/>Le service Vision par ordinateur fournit un sous-ensemble des fonctionnalités du service [Visage](../face/index.yml). Utilisez le service Visage pour effectuer une analyse plus détaillée (reconnaissance faciale, détection de la posture, etc.). [Détecter des visages](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Détecter les types d’images

Détecter les caractéristiques relatives à une image, par exemple si une image est un dessin au trait ou s’il pourrait s’agir d’une image clipart. [Détecter les types d’images](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Détecter le contenu spécifique à un domaine

Utiliser des modèles de domaine pour détecter et identifier le contenu spécifique à un domaine dans une image, notamment pour reconnaître des célébrités ou des éléments géographiques. Par exemple, si une image contient des célébrités, le service Vision par ordinateur peut utiliser un modèle de domaine pour célébrités afin de déterminer si les personnes détectées dans l’image correspondent à des célébrités connues. [Détecter le contenu spécifique à un domaine](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Détecter le jeu de couleurs

Analyser l’utilisation des couleurs dans une image. Le service Vision par ordinateur peut déterminer si une image est en noir et blanc ou en couleur. Pour les images en couleur, il peut également identifier les couleurs dominantes et d’accentuation. [Détecter le jeu de couleurs](concept-detecting-color-schemes.md)



### <a name="generate-a-thumbnail"></a>Générer une miniature

Analyser le contenu d’une image pour en générer une miniature. Le service Vision par ordinateur commence par générer une miniature de haute qualité, puis analyse les objets contenus dans l’image pour déterminer la *zone d’intérêt*. Il rogne ensuite l’image pour conserver uniquement la zone d’intérêt. La miniature générée peut être présentée à l’aide de proportions différentes de celles de l’image d’origine selon les besoins de chacun. [Générer une miniature](concept-generating-thumbnails.md)

:::image type="content" source="Images/thumbnail-demo.png" alt-text="Image d’une personne sur un sommet, avec les versions rognées à droite":::


### <a name="get-the-area-of-interest"></a>Obtenir la zone d’intérêt

Analyser le contenu d’une image pour retourner les coordonnées de la *zone d’intérêt*. Au lieu de rogner l’image et de générer une miniature, le service Vision par ordinateur retourne les coordonnées de cadre englobant de la région pour que l’application appelante puisse modifier l’image d’origine de la manière souhaitée. [Obtenir la zone d’intérêt](concept-generating-thumbnails.md#area-of-interest)

### <a name="moderate-content-in-images"></a>Modérer du contenu dans les images

Vous pouvez utiliser Vision par ordinateur pour [détecter des contenus pour adultes](concept-detecting-adult-content.md) dans une image et retourner des scores de confiance pour différentes classifications. Le seuil de marquage du contenu peut être défini sur une échelle pour prendre en compte vos préférences.

## <a name="image-requirements"></a>Exigences des images

Analyse d'images fonctionne sur les images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG, GIF ou BMP
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être supérieures à 50 x 50 pixels

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision par ordinateur doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec Analyse d'images, suivez le guide de démarrage rapide correspondant à votre langage de développement préféré :

- [Démarrage rapide : API REST ou bibliothèques de client Vision par ordinateur](./quickstarts-sdk/image-analysis-client-library.md)
