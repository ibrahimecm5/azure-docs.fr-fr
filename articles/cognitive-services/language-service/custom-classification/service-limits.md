---
title: Limites de la classification de texte personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les données et les limites de débit lors de l’utilisation de la classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, references_regions, ignite-fall-2021
ms.openlocfilehash: 06db585531cb3a73b291f2c8d45de18a3d298fb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484080"
---
# <a name="custom-text-classification-limits"></a>Limites de la classification de texte personnalisée

Utilisez cet article pour en savoir plus sur les données et les limites de débit lors de l’utilisation de la classification de texte personnalisée

## <a name="file-limits"></a>Limites de fichiers

* Vous pouvez utiliser uniquement des fichiers `.txt` pour la classification de texte personnalisée. Si vos données sont dans un autre format, vous pouvez utiliser la [commande CLUtils parse](https://aka.ms/CognitiveServicesLanguageUtilities) pour extraire votre document et extraire le texte.

* Tous les fichiers téléchargés dans votre conteneur doivent contenir des données, aucun fichier vide n’est autorisé pour l’apprentissage.

* Tous les fichiers doivent être disponibles à la racine de votre conteneur.

* Votre [jeu de données d’entraînement](how-to/train-model.md#data-splits) doit avoir entre 10 et 1 000 000 fichiers.

## <a name="api-limits"></a>Limites de l’API

**Création d’API**

* Vous pouvez envoyer un maximum de 10 requêtes POST et de 100 requêtes GET par minute.

**Analyser l’API**

* Vous pouvez envoyer un maximum de 20 requêtes GET ou POST par minute.

* La taille maximale des fichiers par demande est de 125 000 caractères. Vous pouvez envoyer jusqu’à 25 fichiers, tant que la taille collective de ces derniers ne dépasse pas 125 000 caractères.

> [!NOTE]
> Si vous devez envoyer des documents qui dépassent la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. Vous pouvez utiliser la [commande Chunk de CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ChunkCommand/README.md) pour ce processus.

## <a name="azure-resource-limits"></a>Limites de ressources Azure

* Vous pouvez connecter 1 seul compte de stockage par ressource. Ce processus est irréversible. Si vous connectez un compte de stockage à votre ressource, vous ne pouvez pas le déconnecter par la suite.

* Vous pouvez avoir jusqu’à 500 projets par ressource.

* Les noms de project doivent être uniques au sein de la même ressource, à la fois dans la reconnaissance d’entité nommée personnalisée (NER) et dans les fonctionnalités de classification de texte personnalisées.

## <a name="regional-availability"></a>Disponibilité régionale 

La classification de texte personnalisée est disponible seulement dans certaines régions Azure. Quand vous créez une [ressource Azure](how-to/create-project.md), elle doit être déployée dans l’une des régions suivantes :
* **USA Ouest 2**
* **Europe Ouest**

## <a name="project-limits"></a>Limites de projet

* Vous pouvez connecter 1 seul conteneur de stockage pour chaque projet. Ce processus est irréversible. Si vous connectez un conteneur à votre projet, vous ne pouvez plus le déconnecter ultérieurement.

* Vous ne pouvez avoir qu’un seul fichier de balises par projet. Vous ne pouvez pas modifier le fichier de balises ultérieurement, vous pouvez uniquement mettre à jour les balises dans votre projet.

* Vous ne pouvez pas renommer votre projet après sa création.

* Vous devez avoir au moins 10 fichiers dans votre projet, avec un maximum de 1 000 000 fichiers.

* Vous pouvez avoir jusqu’à 10 modèles entraînés par projet.

* Les noms de modèle doivent être uniques au sein du même projet.

* Vous ne pouvez pas renommer votre modèle après sa création.

* Vous pouvez entraîner un seul modèle à la fois par projet.

## <a name="classes-limits"></a>Limites des classes

* Vous devez avoir au moins 2 classes dans votre projet. <!-- The maximum is 200 classes. -->

* Nous vous recommandons d’avoir environ 200 instances étiquetées par classe. Le minimum est de 10 instances étiquetées par classe.

## <a name="naming-limits"></a>Limites de nommage

| Attribut | limites |
|--|--|
| Nom du projet |  Vous pouvez utiliser seulement des lettres `(a-z, A-Z)` et des chiffres `(0-9)`, sans espace. |
| Nom du modèle |  Vous pouvez utiliser seulement des lettres `(a-z, A-Z)`, des chiffres `(0-9)` et les symboles `@ # _ . , ^ \ [ ]` |
| Noms d’entité| Vous pouvez utiliser seulement des lettres `(a-z, A-Z)`, des chiffres `(0-9)` et les symboles `@ # _ . , ^ \ [ ]` |
| Noms de fichiers | Vous pouvez utiliser seulement des lettres `(a-z, A-Z)` et des chiffres `(0-9)`, sans espace. |
