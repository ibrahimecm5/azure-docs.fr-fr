---
title: Limites - réponses aux questions
description: Les réponses aux questions présentent des limites de métadonnées pour certaines parties de la base de connaissances et du service. Il est important de maintenir votre base de connaissances à l’intérieur de ces limites pour les tests et la publication.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 185a76aeb4aeeadf4fea9b0b316b5905b1f598a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479123"
---
# <a name="project-limits-and-boundaries"></a>Limites du projet

Les limites des réponses aux questions fournies ci-dessous combinent les [limites du niveau tarifaire de Recherche cognitive Azure](../../../../search/search-limits-quotas-capacity.md) et les limites des réponses aux questions. Les deux ensembles de limites affectent le nombre de bases de connaissances que vous pouvez créer par ressource et la taille de chaque base de connaissances.

## <a name="knowledge-bases"></a>Bases de connaissances

Le nombre maximal de bases de connaissances est basé sur les [limites de niveau de Recherche cognitive Azure](../../../../search/search-limits-quotas-capacity.md).

|**Niveau de Recherche cognitive Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de bases de connaissances publiées autorisées|2|14|49|199|199|2 999|

 Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances (un index par base de connaissances publiée). Le 15e index, `testkb`, est utilisé pour toutes les bases de connaissances à des fins de création et de test.

## <a name="extraction-limits"></a>Limites d’extraction

### <a name="file-naming-constraints"></a>Contraintes d’affectation de noms de fichiers

Les noms de fichiers ne peuvent pas inclure les caractères suivants :

|Ne pas utiliser le caractère|
|--|
|Apostrophe `'`|
|Guillemet `"`|

### <a name="maximum-file-size"></a>Taille maximale du fichier

|Format|Taille maximale des fichiers (Mo)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Nombre maximal de fichiers

> [!NOTE]
> Les réponses aux questions n’ont actuellement aucune limite quant au nombre de sources pouvant être ajoutées. Le débit est actuellement limité à 10 transactions par seconde pour à la fois les API de gestion et les API de prédiction.

### <a name="maximum-number-of-deep-links-from-url"></a>Nombre maximal de liens ciblés à partir de l’URL

Le nombre maximal de liens ciblés pouvant être analysés pour l’extraction de paires question-réponse à partir d’une page URL est de **20**.

## <a name="metadata-limits"></a>Limites de métadonnées

Les métadonnées sont présentées comme `key:value` textuelle, telle que `product:windows 10`. Elles sont stockées et comparées en minuscules. Le nombre maximal de champs de métadonnées est basé sur les **[limites du niveau de Recherche cognitive Azure](../../../../search/search-limits-quotas-capacity.md)** .

Si vous choisissez des projets avec plusieurs langues dans une seule ressource de langue, il existe un index de test dédié par projet/base de connaissances. La limite est donc appliquée par projet/base de connaissances dans le service de langage.

|**Niveau de Recherche cognitive Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de champs de métadonnées par service de langage (par base de connaissances)|1 000|100*|1 000|1 000|1 000|1 000|

Si vous ne choisissez pas l’option permettant d’avoir des projets avec plusieurs langues, les limites sont appliquées à toutes les bases de connaissances dans le service de langage.

|**Niveau de Recherche cognitive Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de champs de métadonnées par service de langage (dans toutes les bases de connaissances)|1 000|100*|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Par nom et valeur

La longueur et les caractères acceptables pour le nom et la valeur des métadonnées sont listés dans le tableau suivant.

|Élément|Caractères autorisés|Modèle d’expression régulière|Caractères max.|
|--|--|--|--|
|Nom (clé)|Sont autorisés<br>Les caractères alphanumériques (lettres et chiffres)<br>`_` (trait de soulignement)<br> Ne doit pas contenir d’espaces.|`^[a-zA-Z0-9_]+$`|100|
|Valeur|Tous sauf<br>`:` (deux points)<br>`|` (barre verticale)<br>Une seule valeur autorisée.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de contenu de la base de connaissances
Limites globales sur le contenu de la base de connaissances :
* Longueur du texte de réponse : 25 000 caractères
* Longueur du texte de question : 1 000 caractères
* Longueur du texte de clé des métadonnées : 100 caractères
* Longueur du texte de valeur des métadonnées : 500 caractères
* Caractères pris en charge pour le nom des métadonnées : lettres, chiffres et `_`
* Caractères pris en charge pour la valeur des métadonnées : Tous sauf `:` et `|`
* Longueur de nom de fichier : 200
* Formats de fichier pris en charge : « .tsv », « .pdf », « .txt », « .docx », « .xlsx ».
* Nombre maximal de questions alternatives : 300
* Nombre maximal de paires question-réponse : Varie en fonction du **[niveau de Recherche cognitive Azure](../../../../search/search-limits-quotas-capacity.md#document-limits)** choisi. Une paire question-réponse est mappée à un document dans l'index Recherche cognitive Azure.
* Page HTML/URL : 1 million de caractères

## <a name="create-project-call-limits"></a>Créer des limites d’appels de projet :

Elles représentent les limites de chaque action Créer un projet/une base de connaissances ; autrement dit, en sélectionnant *Créer un projet* ou en appelant l’API REST pour créer un projet/une base de connaissances.

* Nombre maximal recommandé de questions alternatives par réponse : 300
* Nombre maximal d’URL : 10
* Nombre maximal de fichiers : 10
* Nombre maximal de questions/réponses autorisées par appel : 1 000

## <a name="update-knowledge-base-call-limits"></a>Mettre à jour les limites d’appels de la base de connaissances

Elles représentent les limites pour chaque action de mise à jour, autrement dit, en sélectionnant *Enregistrer* ou en appelant l’API REST avec une requête de mise à jour.
* Longueur de chaque nom de source : 300
* Nombre maximal recommandé de questions alternatives ajoutées ou supprimées : 300
* Nombre maximal de champs de métadonnées ajoutés ou supprimés : 10
* Nombre maximal d’URL pouvant être actualisées : 5
* Nombre maximal de questions/réponses autorisées par appel : 1 000

## <a name="add-unstructured-file-limits"></a>Ajouter des limites de fichiers non structurés

> [!NOTE]
> * Si vous devez utiliser des fichiers plus volumineux que la limite autorisée, vous pouvez scinder le fichier en fichiers plus petits avant de les envoyer à l’API. 

Elles représentent les limites lors de l’utilisation de fichiers non structurés pour *Créer un projet* ou appeler l’API REST pour créer une base de connaissances :
* Longueur du fichier : Nous allons extraire les 32 000 premiers caractères
* Trois réponses maximum par fichier.

## <a name="prebuilt-question-answering-limits"></a>Limites de réponse aux questions prédéfinies

> [!NOTE]
> * Si vous devez utiliser des documents plus volumineux que la limite autorisée, vous pouvez scinder le texte en blocs de texte plus petits avant de les envoyer à l’API. 
> * Un document est une chaîne unique de caractères de texte.  

Elles représentent les limites lorsque l’API REST est utilisée pour répondre à une question sans avoir à créer un projet ou une base de connaissances :
* Nombre de documents : 5
* Taille maximale d’un seul document : 5 120 caractères
* Trois réponses maximum par document.