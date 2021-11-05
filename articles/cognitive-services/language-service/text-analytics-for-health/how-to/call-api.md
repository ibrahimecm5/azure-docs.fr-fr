---
title: Appeler l’Analyse de texte pour l’intégrité
titleSuffix: Azure Cognitive Services
description: Découvrez comment extraire et étiqueter des informations médicales de texte clinique non structuré avec l’Analyse de texte pour la santé.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: d94e07b312bda98c6317e8a2b020510ffda800f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097581"
---
# <a name="how-to-use-text-analytics-for-health"></a>Comment utiliser l’Analyse de texte pour la santé

> [!IMPORTANT] 
> L’Analyse de texte pour la santé est une fonctionnalité fournie « en l’état » et « avec toutes les erreurs ». L’Analyse de texte pour l’intégrité n’est pas destinée à être utilisée en tant que dispositif médical, support clinique, outil de diagnostic ou autre technologie destinée à être utilisée dans le diagnostic, la guérison, l’atténuation, le traitement ou la prévention de maladies ou d’autres conditions, et aucune licence ou droit n’est accordé par Microsoft pour utiliser cette fonctionnalité à ces fins. Cette fonctionnalité n’est pas conçue ou destinée à être mise en œuvre ou déployée en remplacement de conseils médicaux professionnels ou d’avis de santé, de diagnostic, de traitement ou de jugement clinique d’un professionnel de la santé, et ne doit pas être utilisé en tant que tel. Le client est seul responsable de l’utilisation de l’Analyse de texte pour l’intégrité. Le client doit disposer séparément d’une licence pour tous les vocabulaires sources qu’il envisage d’utiliser selon les conditions définies dans cette [Annexe du Contrat de licence du métathésaurus de l’UMLS](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/license_agreement_appendix.html) ou tout lien équivalent futur. Il incombe au client de s’assurer de la conformité avec les termes du contrat de licence, y compris en ce qui concerne les restrictions géographiques ou autres restrictions applicables.


L’Analyse de texte pour la santé extrait et étiquette les informations médicales pertinentes à partir de textes non structurés, tels que les notes du médecin, les bilans de sortie d’hospitalisation, les documents cliniques et les enregistrements d’intégrité électroniques.  Il existe deux façons d’utiliser ce service : 

* API Web et bibliothèques clientes (asynchrones)
* Un [conteneur Docker](use-containers.md) (synchrone)

## <a name="features"></a>Fonctionnalités

Analyse de texte pour la santé effectue une reconnaissance d’entité nommée (NER), une extraction de relations, une négation d’entité et une liaison d’entité sur du texte en anglais pour découvrir des insights dans du texte clinique et biomédical non structuré. Consultez les [catégories d’entité](../concepts/health-entity-categories.md) retournées par l’Analyse de texte pour la santé pour obtenir un liste complète des entités prises en charge. Pour plus d’informations sur les scores de confiance, consultez la [note sur la transparence](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md). Vous pouvez également créer des exemples de requêtes à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-text-analytics-for-health-model"></a>Spécifier Analyse de texte pour le modèle d’intégrité

Par défaut, Analyse de texte pour l’intégrité utilisera le dernier modèle d’IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez sera utilisé pour effectuer les opérations fournies par le Analyse de texte pour l’intégrité.

| Versions prises en charge | version la plus récente |
|--|--|
| `2021-05-15` | `2021-05-15`   |

### <a name="text-analytics-for-health-container"></a>Analyse de texte pour le conteneur d’intégrité

Le conteneur [Analyse de texte pour l’intégrité](use-containers.md) utilise une gestion de versions de modèle distinct des points de terminaison d’API ci-dessus. Une seule version de modèle est disponible par image de conteneur.

| Point de terminaison                        | Étiquette de l’image conteneur                     | Version du modèle |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` (la plus récente)            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |

### <a name="input-languages"></a>Langues de saisie

Actuellement, Analyse de texte pour l’intégrité [prend uniquement en charge](../language-support.md) la langue anglaise. 

## <a name="submitting-data"></a>Envoi de données

Pour envoyer une demande d’API, vous avez besoin du point de terminaison et de la clé de votre ressource de langue.

> [!NOTE]
> Vous trouverez la clé et le point de terminaison de votre ressource Language dans le portail Azure. Ils sont dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez les limites de données ci-dessous.

Si vous envoyez une requête à l’aide de l’API REST ou de la bibliothèque cliente, les résultats sont retournés de façon asynchrone. Si vous utilisez le conteneur Docker, ils sont retournés de façon synchrone.  


## <a name="getting-results-from-the-feature"></a>Obtention des résultats de la fonctionnalité

En fonction de votre demande d’API et des données que vous envoyez au Analyse de texte pour l’intégrité, vous obtiendrez :

[!INCLUDE [Text Analytics for health features](../includes/features.md)]


Lors de l’utilisation de cette fonctionnalité de manière asynchrone, les résultats de l’API sont disponibles pendant 24 heures à partir du moment où la requête a été ingérée, et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. Pour obtenir les meilleurs résultats, divisez le texte entre les phrases.
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 10 pour l’API web, 1 000 pour le conteneur. |

Si un document dépasse la limite de caractères, l’API ne traitera pas un document qui dépasse la taille maximale et renverra une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing). Ces limites sont les mêmes pour les deux versions de l’API. Ces limites du taux de transfert ne s’appliquent pas à l’Analyse de texte pour le conteneur d’intégrité démuni de limite définie du taux d’intégrité.

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| F0         | 100                 | 300                 |

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de l’Analyse de texte pour la santé](../overview.md)
* [Analyse de texte pour l’intégrité des catégories d’entités](../concepts/health-entity-categories.md)
