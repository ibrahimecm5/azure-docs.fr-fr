---
title: Appeler l’API de liaison d’entités
titleSuffix: Azure Cognitive Services
description: Découvrez comment identifier et lier des entités trouvées dans du texte avec l’API de liaison d’entités.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: a7edc27898c1af9fcb8f7bc72698d2d6c3a63e68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096278"
---
# <a name="how-to-use-entity-linking"></a>Comment utiliser la liaison d’entités

La fonctionnalité de liaison d’entités peut être utilisée pour identifier et lever l’ambiguïté de l’identité d’une entité trouvée dans le texte (par exemple, pour déterminer si une occurrence du mot « *mars* » fait référence à la planète ou au Dieu roman de guerre). Elle renverra les entités dans le texte avec des liens vers [Wikipédia](https://www.wikipedia.org/) en tant que base de connaissances.

> [!TIP]
> Si vous souhaitez commencer à utiliser cette fonctionnalité, vous pouvez suivre l’[article de démarrage rapide](../quickstart.md). Vous pouvez également créer des exemples de requêtes à l’aide de [Language Studio](../../language-studio.md) sans avoir à écrire de code.

## <a name="determine-how-to-process-the-data-optional"></a>Déterminer le mode de traitement des données (facultatif)

### <a name="specify-the-entity-linking-model"></a>Spécifier le modèle de liaison d’entités

Par défaut, les liaisons d’entités utilisent le dernier modèle d’IA disponible sur votre texte. Vous pouvez également configurer vos demandes d’API pour utiliser une version de modèle spécifique. Le modèle que vous spécifiez sera utilisé pour effectuer des opérations de liaison d’entité.

| Versions prises en charge | version la plus récente |
|--|--|
| `2019-10-01`, `2020-02-01` | `2020-02-01` |

### <a name="input-languages"></a>Langues de saisie

Lorsque vous soumettez des documents à traiter par une liaison d’entités, vous pouvez spécifier les [langues prises en charge](../language-support.md) dans lesquelles ils sont écrits. Si vous ne spécifiez pas de langue, la liaison d'entités prendra par défaut la valeur anglais. En raison de la [prise en charge multilingue et des émojis](../../concepts/multilingual-emoji-support.md), la réponse peut contenir des décalages de texte. 

## <a name="submitting-data"></a>Envoi de données

La liaison d’entités produit un résultat de meilleure qualité lorsque vous lui donnez des quantités de texte plus petites à utiliser. C’est l’inverse pour certaines fonctionnalités comme l’extraction de phrases clés qui sont plus performantes sur de plus grands blocs de texte. Pour obtenir les meilleurs résultats lors des deux opérations, envisagez de restructurer les entrées en conséquence.

Pour envoyer une demande d’API, vous avez besoin du point de terminaison et de la clé d’une ressource de langue.

> [!NOTE]
> Vous trouverez la clé et le point de terminaison de votre ressource Language dans le portail Azure. Ils sont dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

L’analyse est effectuée à la réception de la demande. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez les limites de données ci-dessous.

L’utilisation d’une liaison d’entité de façon synchrone est sans état. Aucune donnée n’est stockée dans votre compte, et les résultats sont retournés immédiatement dans la réponse.

Lors de l’utilisation de cette fonctionnalité de manière asynchrone, les résultats de l’API sont disponibles pendant 24 heures à partir du moment où la requête a été ingérée, et sont indiqués dans la réponse. Après cette période, les résultats sont purgés et ne sont plus disponibles pour récupération.

### <a name="getting-entity-linking-results"></a>Obtention des résultats de liaison d’entités  

Vous pouvez streamer les résultats vers une application ou enregistrer la sortie dans un fichier sur le système local.

## <a name="data-limits"></a>Limites de données

> [!NOTE]
> * Si vous avez besoin d’analyser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

| Limite | Valeur |
|------------------------|---------------|
| Taille maximale de document | 5 120 caractères mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Nombre maximal de caractères par demande (asynchrone)  | 125 000 caractères pour tous les documents envoyés, tels que mesurés par [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Taille maximale d’une demande | 1 Mo |
| Nombre maximal de documents par demande | 5 |

Si un document dépasse la limite de caractères, l’API se comporte différemment selon que vous l’utilisez de façon synchrone ou asynchrone :

* Asynchrone : l’API rejette l’intégralité de la requête et retourne une erreur `400 bad request` si un document qu’elle contient dépasse la taille maximale.
* Synchrone : l’API ne traite pas un document qui dépasse la taille maximale et retourne une erreur de document non valide. Si une demande d’API a plusieurs documents, l’API continue de les traiter s’ils sont dans la limite de caractères.

### <a name="rate-limits"></a>Limites du taux de transfert

Votre limite de débit varie en fonction de votre [niveau tarifaire](https://aka.ms/unifiedLanguagePricing).

| Niveau          | Demandes par seconde | Requêtes par minute |
|---------------|---------------------|---------------------|
| S / Multiservice | 1 000                | 1 000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de la liaison d’entités](../overview.md)
