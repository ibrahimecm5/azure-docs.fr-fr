---
title: Qu’est-ce que l’Analyse de texte pour l’intégrité dans Azure Cognitive Services pour la langue ?
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble d’Analyse de texte pour l’intégrité dans Azure Cognitive Services, qui vous permet d’extraire des informations médicales à partir de texte non structuré, tel que des documents cliniques.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 609d60d2094372cd5c1e476afd3abde1df7f68af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096624"
---
# <a name="what-is-text-analytics-for-health-in-azure-cognitive-service-for-language"></a>Qu’est-ce que l’Analyse de texte pour l’intégrité dans Azure Cognitive Services pour la langue ?

Analyse de texte est l’une des fonctionnalités offertes par [Azure Cognitive Service for Language](../overview.md), un ensemble d’algorithmes de Machine Learning et IA dans le cloud pour le développement d’applications intelligentes qui impliquent du langage écrit. 

L’Analyse de texte pour la santé extrait et étiquette les informations médicales pertinentes à partir de textes non structurés, tels que les notes du médecin, les bilans de sortie d’hospitalisation, les documents cliniques et les enregistrements d’intégrité électroniques.

Cette documentation contient les types d’articles suivants :

* Les [**Démarrages rapides**](quickstart.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](how-to/call-api.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [**articles conceptuels**](concepts/health-entity-categories.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Fonctionnalités

[!INCLUDE [Text Analytics for health](includes/features.md)]

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Déployer localement en utilisant des conteneurs Docker

Utilisez le conteneur Docker disponible pour [déployer cette fonctionnalité localement](how-to/use-containers.md). Ces conteneurs Docker vous donnent la possibilité de rapprocher le service plus près de vos données, ce qui peut être souhaitable pour des raisons de conformité, de sécurité ou opérationnelles.

## <a name="responsible-ai"></a>Intelligence artificielle responsable 

Un système IA comprend non seulement la technologie, mais aussi les personnes qui l’utiliseront, les personnes qui seront affectées par lui et l’environnement dans lequel il est déployé. Pour en savoir plus sur l’utilisation et le déploiement de l’intelligence artificielle responsable dans vos systèmes, consultez la note relative à la [transparence pour Analyse de texte pour l’intégrité](/legal/cognitive-services/language-service/transparency-note-health?context=/azure/cognitive-services/language-service/context/context). Vous pouvez également consulter les articles suivants pour plus d’informations :

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Étapes suivantes

Il existe deux façons de commencer à utiliser la fonctionnalité de liaison d’entités :
* [Language Studio](../language-studio.md), qui est une plateforme basée sur le web qui vous permet d’essayer plusieurs fonctionnalités Azure Cognitive Service for Language sans avoir à écrire de code.
* L’[article de démarrage rapide](quickstart.md) pour obtenir des instructions sur la création de requêtes au service à l’aide de l’API REST et du kit SDK de la bibliothèque de client.  
