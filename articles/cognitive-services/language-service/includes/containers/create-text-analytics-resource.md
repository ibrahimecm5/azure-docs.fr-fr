---
title: Création d’une ressource Langue Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource Cognitive Services Language.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d21f76e73e9ef4581355890b56876ff2e71a954
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097124"
---
## <a name="create-a-cognitive-services-language-resource"></a>Création d’une ressource Langue Cognitive Services

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**, puis accédez à **IA + Machine Learning** > **Langue**.
   Vous pouvez également accéder à [Créer une ressource Langue](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Nom|Entrez un nom (2-64 caractères).|
    |Abonnement|Sélectionnez l’abonnement approprié.|
    |Emplacement|Sélectionnez un emplacement proche.|
    |Niveau tarifaire| Entrez **S**, le niveau tarifaire standard.|
    |Resource group|Sélectionnez un groupe de ressources disponible.|

1. Sélectionnez **Créer** et attendez que la ressource soit créée. Votre navigateur vous redirige automatiquement vers la page de la ressource créée.
1. Collectez `endpoint` configuré et une clé d’API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0`.|
    |**Clés**|Clé de l’API|Copiez l’une des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
