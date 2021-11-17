---
title: Qu’est-ce que la liaison d’entités dans Azure Cognitive Service for Language ?
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble de la liaison d’entités dans Azure Cognitive Services, qui vous permet d’extraire des entités à partir de texte et fournit des liens vers une base de connaissances en ligne.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: bbd4483702055a4bebb05251606fb705b1e0deb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487468"
---
# <a name="what-is-entity-linking-in-azure-cognitive-service-for-language"></a>Qu’est-ce que la liaison d’entités dans Azure Cognitive Service for Language ?

La liaison d’entités est l’une des fonctionnalités offertes par [Azure Cognitive Service for Language](../overview.md), un ensemble d’algorithmes de Machine Learning et IA dans le cloud pour le développement d’applications intelligentes qui impliquent du langage écrit. La liaison d’entités identifie et lève l’ambiguïté relative à l’identité des entités trouvées dans le texte. Par exemple, dans la phrase « *Nous sommes allés à Seattle la semaine dernière.*  », le mot « *Seattle* » serait identifié, avec un lien vers des informations supplémentaires sur Wikipédia.

Cette documentation contient les types d’articles suivants :

* Les [**Démarrages rapides**](quickstart.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](how-to/call-api.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

Le résultat est une collection d’entités reconnues dans votre texte, avec des URL vers Wikipédia en tant que base de connaissances en ligne. 

## <a name="responsible-ai"></a>Intelligence artificielle responsable 

Un système IA comprend non seulement la technologie, mais aussi les personnes qui l’utiliseront, les personnes qui seront affectées par lui et l’environnement dans lequel il est déployé. Lisez la [Note de transparence pour la liaison d’entités](/legal/cognitive-services/language-service/transparency-note?context=/azure/cognitive-services/language-service/context/context) pour en savoir plus sur l’utilisation et le déploiement de l’IA responsable dans vos systèmes. Vous pouvez également consulter les articles suivants pour plus d’informations :

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Étapes suivantes

Il existe deux façons de commencer à utiliser la fonctionnalité de liaison d’entités :
* [Language Studio](../language-studio.md), qui est une plateforme basée sur le web qui vous permet d’essayer plusieurs fonctionnalités Azure Cognitive Service for Language sans avoir à écrire de code.
* L’[article de démarrage rapide](quickstart.md) pour obtenir des instructions sur la création de requêtes au service à l’aide de l’API REST et du kit SDK de la bibliothèque de client.  
