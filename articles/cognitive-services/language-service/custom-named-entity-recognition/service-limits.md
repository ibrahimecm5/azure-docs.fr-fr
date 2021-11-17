---
title: Limites de service de Reconnaissance d’entité nommée (NER) personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les limites de données et de service quand vous utilisez Reconnaissance d’entité nommée (NER) personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 255c0f5f85f1f7b385fed82c36a799004f03ed0b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488213"
---
# <a name="custom-named-entity-recognition-ner-service-limits"></a>Limites de service de Reconnaissance d’entité nommée (NER) personnalisée

Utilisez cet article pour en savoir plus sur les limites de données et de service quand vous utilisez la fonctionnalité NER personnalisée.

## <a name="file-limits"></a>Limites de fichiers

* Vous ne pouvez utiliser que des fichiers `.txt` . Si vos données sont dans un autre format, vous pouvez utiliser la [commande CLUtils parse](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) pour ouvrir votre document et extraire le texte.

* Tous les fichiers chargés dans votre conteneur doivent contenir des données. Les fichiers vides ne sont pas autorisés pour l’entraînement.

* Tous les fichiers doivent être disponibles à la racine de votre conteneur.

* La longueur maximale autorisée pour votre fichier est 128 000 caractères, soit environ 28 000 mots ou 56 pages.

* Votre [jeu de données d’entraînement](how-to/train-model.md#data-split) doit avoir entre 10 et 100 000 fichiers.


## <a name="apis-limits"></a>Limites d’API

* L’API de création est limitée à un maximum de 10 demandes POST et 100 demandes GET par minute.

* L’API d’analyse est limitée à un maximum de 20 demandes GET ou POST par minute.

* La taille de fichier maximale par demande est de 125 000 caractères. Vous pouvez envoyer jusqu’à 25 fichiers tant qu’ils ne dépassent pas 125 000 caractères cumulés.

> [!NOTE]
> Si vous devez envoyer des documents qui dépassent la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. Vous pouvez utiliser la [commande Chunk de CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) pour ce processus.

## <a name="azure-resource-limits"></a>Limites de ressources Azure

* Vous pouvez connecter 1 seul compte de stockage par ressource. Ce processus est irréversible. Si vous connectez un compte de stockage à votre ressource, vous ne pouvez pas le dissocier par la suite.

* Vous pouvez avoir jusqu’à 500 projets par ressource.

* Les noms de projet doivent être uniques dans la même ressource pour NER personnalisée et la [classification de texte personnalisée](../custom-classification/overview.md).

## <a name="regional-availability"></a>Disponibilité régionale 

La classification de texte personnalisée est disponible seulement dans certaines régions Azure. Quand vous créez une [ressource Azure](how-to/create-project.md), elle doit être déployée dans l’une des régions suivantes :
* **USA Ouest 2**
* **Europe Ouest**
    
## <a name="project-limits"></a>Limites de projet

* Vous pouvez connecter 1 seul conteneur de stockage pour chaque projet. Ce processus est irréversible. Si vous connectez un conteneur à votre projet, vous ne pouvez pas le déconnecter par la suite.

* Vous pouvez avoir seulement un [fichier d’étiquettes](how-to/tag-data.md) par projet. Vous ne pouvez pas changer de fichier d’étiquettes par la suite. Vous pouvez uniquement mettre à jour les étiquettes dans votre projet.

* Vous ne pouvez pas renommer votre projet après sa création.

* Vous devez avoir au moins 10 fichiers étiquetés dans votre projet, avec un maximum de 100 000 fichiers.

* Vous pouvez avoir jusqu’à 10 modèles entraînés par projet.

* Les noms de modèle doivent être uniques au sein du même projet.

* Vous ne pouvez pas renommer votre modèle après sa création.

* Vous pouvez entraîner un seul modèle à la fois par projet.

## <a name="entity-limits"></a>Limites de l’entité

* Nous vous recommandons de ne pas dépasser 10 mots pour votre entité étiquetée, mais la valeur maximale autorisée est de 100 caractères.

* Vous devez avoir au moins 1 type d’entité dans votre projet, sans dépasser 200 types d’entités.

* Nous vous recommandons d’avoir environ 200 instances étiquetées par entité. Le minimum est de 10 instances étiquetées par entité.

## <a name="naming-limits"></a>Limites de nommage

| Attribut | limites |
|--|--|
| Nom du projet |  Vous pouvez utiliser seulement des lettres `(a-z, A-Z)` et des chiffres `(0-9)`, sans espace. |
| Nom du modèle |  Vous pouvez utiliser seulement des lettres `(a-z, A-Z)` et des chiffres `(0-9)`, sans espace. |
| Noms d’entité| Vous pouvez utiliser seulement des lettres `(a-z, A-Z)`, des chiffres `(0-9)` et les symboles `@ # _ . , ^ \ [ ]` |
| Noms de fichiers | Vous pouvez utiliser seulement des lettres `(a-z, A-Z)` et des chiffres `(0-9)`, sans espace. |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de NER personnalisée](../overview.md)
