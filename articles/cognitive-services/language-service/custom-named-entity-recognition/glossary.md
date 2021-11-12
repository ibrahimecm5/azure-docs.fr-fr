---
title: Définitions et termes utilisés pour la reconnaissance d’entités nommées (NER) personnalisées
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les définitions et les termes que vous pouvez rencontrer pendant l’utilisation de la reconnaissance d’entités nommées (NER) personnalisées
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c57d4efffc207759b8d3da343bda397ee885dc0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439079"
---
# <a name="custom-named-entity-recognition-ner-definitions-and-terms"></a>Définitions et termes de la reconnaissance d’entités nommées (NER) personnalisées

Utilisez cet article pour en savoir plus sur les définitions et les termes que vous pouvez rencontrer pendant l’utilisation de NER personnalisée.

## <a name="project"></a>Project

Un projet est une zone de travail dans laquelle vous créez vos modèles IA personnalisés à partir de vos données. Votre projet est accessible uniquement par vous et d’autres personnes qui ont un accès de contributeur à la ressource Azure utilisée.
Pour pouvoir créer un projet d’extraction d’entités personnalisées, vous devez connecter votre ressource à un compte de stockage avec votre jeu de données quand vous [créez un projet](how-to/create-project.md). Votre projet comprend automatiquement tous les fichiers `.txt` disponibles dans votre conteneur.

Dans votre projet, vous pouvez effectuer les opérations suivantes :

* **Étiqueter vos données** : processus d’étiquetage de vos données qui permet à votre modèle d’apprendre ce que vous voulez extraire dans le cadre de son entraînement.
* **Créer et entraîner votre modèle** : étape principale de votre projet, où votre modèle commence à apprendre à partir de vos données étiquetées. 
* **Voir les détails de l’évaluation du modèle** : consultez les performances de votre modèle pour déterminer si des améliorations sont possibles ou si vous êtes satisfait des résultats.
* **Améliorer le modèle** : quand vous savez ce qui ne va pas avec votre modèle et comment améliorer les performances. 
* **Déployer le modèle** : rendez votre modèle disponible pour qu’il puisse être utilisé. 
* **Tester le modèle** : testez votre modèle sur un jeu de données.

## <a name="model"></a>Modèle

Un modèle est un objet qui a été entraîné pour effectuer une tâche en particulier, dans ce cas une reconnaissance personnalisée d’entités nommées.

* L’**entraînement du modèle** est le processus qui consiste à apprendre à votre modèle ce qu’il faut extraire à partir de vos données étiquetées.
* L’**évaluation du modèle** est le processus qui se produit juste après l’entraînement pour savoir si le modèle est performant.
* Le **déploiement du modèle** est le processus qui permet de le rendre disponible pour pouvoir l’utiliser.

## <a name="entity"></a>Entité

Une entité est une étendue de texte qui indique un certain type d’informations. L’étendue de texte peut comporter un ou plusieurs mots (ou jetons). Dans la reconnaissance d’entités nommées (NER) personnalisées, les entités représentent les informations à extraire dans le texte. 

Par exemple, dans la phrase « *John a emprunté 25 000 USD à Fred.*  » les entités peuvent être : 

| Nom/type de l’entité | Entité |
| -- | -- |
| Nom de l’emprunteur | *John* |
| Nom du prêteur | *Fred* |
| Montant du prêt | *25 000 USD* |

## <a name="next-steps"></a>Étapes suivantes

* [Limites des données et du service](service-limits.md).
* [Vue d’ensemble de NER personnalisée](../overview.md).
