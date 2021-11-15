---
title: Documentation Swagger - Service Speech
titleSuffix: Azure Cognitive Services
description: La documentation Swagger peut être utilisée pour générer automatiquement des SDK pour plusieurs langages de programmation. Toutes les opérations de notre service sont prises en charge par Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: edc01f30ea3090f5c95a1da16e4d62feecbb18cc
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989277"
---
# <a name="swagger-documentation"></a>Documentation Swagger

Le service Speech offre une spécification Swagger pour interagir avec quelques API REST utilisées pour importer des données, créer des modèles, tester le niveau de justesse des modèles, créer des points de terminaison personnalisés, mettre en file d’attente des transcriptions par lots et gérer des abonnements. La plupart des opérations disponibles par le biais de [la zone Custom Speech de Speech Studio](https://aka.ms/speechstudio/customspeech) peuvent être effectuées par programme à l’aide de ces API.

> [!NOTE]
> Le service Speech fournit quelques API REST pour la [reconnaissance vocale](rest-speech-to-text.md) et la [synthèse vocale](rest-text-to-speech.md).  
>
> Toutefois, seules les [API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) et v2.0 sont documentées dans la spécification Swagger. Consultez les documents référencés dans le paragraphe précédent pour obtenir des informations sur toutes les autres API REST des services Speech.

## <a name="generating-code-from-the-swagger-specification"></a>Génération de code à partir de la spécification Swagger

La [spécification Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) comporte des options qui vous permettent de tester rapidement divers parcours. En revanche, il est parfois souhaitable de générer du code pour tous les parcours, en créant une bibliothèque unique d’appels sur laquelle vous pouvez baser les futures solutions. Intéressons-nous au processus permettant de générer une bibliothèque Python.

Vous devez définir Swagger sur la région de votre ressource Speech. Vous pouvez confirmer la région dans la partie **Vue d’ensemble** de vos paramètres de ressource Speech dans le portail Azure. La liste complète des régions prises en charge est disponible [ici](regions.md#speech-to-text).

1. Dans un navigateur, accédez à la spécification Swagger de votre [région](regions.md#speech-to-text) :  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Sur cette page, cliquez sur **Définition de l'API**, puis sur **Swagger**. Copiez l'URL de la page qui s'affiche.
1. Dans un nouveau navigateur, accédez à [https://editor.swagger.io](https://editor.swagger.io)
1. Cliquez sur **Fichier**, sur **Importer l'URL**, collez l'URL, puis cliquez sur **OK**.
1. Cliquez sur **Générer un client** et sélectionnez **python**. La bibliothèque de client est téléchargée sur votre ordinateur sous forme de fichier `.zip`.
1. Extrayez l'ensemble du téléchargement. Vous pouvez utiliser `tar -xf` pour tout extraire.
1. Installez le module extrait dans votre environnement Python :  
      `pip install path/to/package/python-client`
1. Le package installé porte le nom suivant : `swagger_client`. Vérifiez que l’installation s’est bien déroulée :  
       `python -c "import swagger_client"`

Vous pouvez utiliser la bibliothèque Python que vous avez générée avec les [exemples du service Speech sur GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Documents de référence

* [Swagger : API REST de reconnaissance vocale v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [API REST de reconnaissance vocale](rest-speech-to-text.md)
* [API REST de synthèse vocale](rest-text-to-speech.md)

## <a name="next-steps"></a>Étapes suivantes

* [Exemples Speech sur GitHub](https://aka.ms/csspeech/samples)
* [Obtenir gratuitement une clé d’abonnement au service Speech](overview.md#try-the-speech-service-for-free)
