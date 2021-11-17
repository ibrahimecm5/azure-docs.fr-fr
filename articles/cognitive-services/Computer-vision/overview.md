---
title: Qu’est-ce que le service Vision par ordinateur ?
titleSuffix: Azure Cognitive Services
description: Le service Vision par ordinateur vous donne accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: vision par ordinateur, applications de vision par ordinateur, service vision par ordinateur
ms.openlocfilehash: 3165dc875c89618476284aaaff3e5884ab3fe6b1
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370742"
---
# <a name="what-is-computer-vision"></a>Qu’est-ce que le service Vision par ordinateur ?

Le service Vision par ordinateur d’Azure vous donne accès à des algorithmes avancés permettant de traiter des images et de retourner des informations en fonction des caractéristique visuelles qui vous intéressent. 

| Service|Description|
|---|---|
| [Reconnaissance optique des caractères (OCR)](overview-ocr.md)|Le service Reconnaissance optique de caractères (OCR) extrait le texte des images. Vous pouvez utiliser la nouvelle API Read pour extraire du texte imprimé et manuscrit à partir d'images et de documents. Elle utilise des modèles basés sur le Deep Learning et fonctionne avec du texte sur une variété de surfaces et d'arrière-plans. Ces derniers comprennent les documents commerciaux, les factures, les reçus, les affiches, les cartes de visite, les lettres et les tableaux blancs. Les API de reconnaissance optique de caractères prennent en charge l’extraction de texte imprimé en [plusieurs langues](./language-support.md). Pour vous lancer, suivez le [guide de démarrage rapide consacré au service Reconnaissance optique de caractères](quickstarts-sdk/client-library.md).|
|[Analyse d’image](overview-image-analysis.md)| Le service Analyse d'images extrait de nombreuses caractéristiques visuelles à partir d'images, telles que des objets, des visages, du contenu pour adultes et des descriptions de texte générées automatiquement. Pour vous lancer, suivez le [guide de démarrage rapide consacré au service Analyse d'images](quickstarts-sdk/image-analysis-client-library.md).|
| [Analyse spatiale](intro-to-spatial-analysis-public-preview.md)| Le service Analyse spatiale détecte la présence de personnes et analyse leurs déplacements sur un flux vidéo, et produit des événements auxquels d'autres systèmes peuvent répondre. Pour démarrer, installez le [conteneur Analyse spatiale](spatial-analysis-container.md).|

## <a name="computer-vision-for-digital-asset-management"></a>Vision par ordinateur pour la gestion des ressources numériques

Vision par ordinateur peut contribuer à de nombreux scénarios de gestion des actifs numériques (DAM). La gestion des actifs numériques est le processus métier d’organisation, de stockage et de récupération des éléments multimédias enrichis, et de gestion des droits et autorisations numériques. Par exemple, une entreprise peut décider de regrouper et d’identifier des images sur la base de logos, de visages, d’objets, de couleurs, ou d’autres éléments visibles. Vous pouvez également [générer automatiquement des légendes pour des images](./Tutorials/storage-lab-tutorial.md) et y associer des mots clés afin qu’elles puissent faire l’objet d’une recherche. Pour une solution gestion des actifs numériques tout-en-un utilisant Cognitive Services, Recherche cognitive Azure et une fonctionnalité de création de rapports intelligents, consultez le [Guide de l’accélérateur de solution d’exploration des connaissances](https://github.com/Azure-Samples/azure-search-knowledge-mining) sur GitHub. Pour d’autres exemples de Vision par ordinateur, consultez le dépôt [Modèles de solution Vision par ordinateur](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates).

## <a name="image-requirements"></a>Exigences des images

Le service Vision par ordinateur peut analyser des images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG, GIF ou BMP
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être supérieures à 50 x 50 pixels
  - Pour l’API Read, la taille de l’image doit être comprise entre 50 x 50 et 10 000 x 10 000 pixels.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision par ordinateur doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Suivez un guide de démarrage rapide pour implémenter et exécuter un service dans votre langage de développement préféré.

* [Démarrage rapide : Reconnaissance optique de caractères (OCR)](quickstarts-sdk/client-library.md)
* [Démarrage rapide : Analyse d'images](quickstarts-sdk/image-analysis-client-library.md)
* [Démarrage rapide : Conteneur Analyse spatiale](spatial-analysis-container.md)
