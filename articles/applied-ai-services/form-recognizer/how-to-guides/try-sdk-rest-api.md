---
title: Utiliser des kits SDK de bibliothèque de client Form Recognizer ou l’API REST
titleSuffix: Azure Applied AI Services
description: Utilisez un kit de développement logiciel (SDK) de bibliothèque de client Form Recognizer ou l’API REST afin de créer une application de traitement des formulaires qui extrait des paires clé/valeur et des données de table de vos documents personnalisés.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0402041121e34902d9801307dad8a759f8fe9516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096453"
---
# <a name="use-form-recognizer-sdks-or-rest-api"></a>Utiliser des kits de développement logiciel (SDK) Form Recognizer ou l’API REST

 Dans ce guide pratique, vous apprendrez à ajouter le module Form Recognizer à vos applications et flux de travail à l’aide d’un kit de développement logiciel (SDK), dans un langage de programmation de votre choix, ou l’API REST. Azure Form Recognizer est un service Azure Applied AI Service qui utilise le machine learning pour extraire et analyser les champs de formulaire, le texte et les tableaux de vos documents. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

Vous allez utiliser les API suivantes pour extraire des données structurées de formulaires et de documents :

* [Authentifier le client](#authenticate-the-client)
* [Analyser la disposition](#analyze-layout)
* [Analyser les reçus](#analyze-receipts)
* [Analyser les cartes de visite](#analyze-business-cards)
* [Analyser les factures](#analyze-invoices)
* [Analyser des documents d’identité](#analyze-id-documents)
* [Entraîner un modèle personnalisé](#train-a-custom-model)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Gérer des modèles personnalisés](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/how-to-guides/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/how-to-guides/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/how-to-guides/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/how-to-guides/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/how-to-guides/rest-api.md)]

::: zone-end
