---
title: Préparer des données et définir un schéma
titleSuffix: Azure Cognitive Services
description: En savoir plus sur la sélection des données, la préparation et la création d’un schéma pour les projets de classification personnalisés.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 69846496c05716cc00ca782c979bc3c970537b0b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028910"
---
# <a name="how-to-prepare-data-and-define-a-schema"></a>Préparer des données et définir un schéma

Pour créer un modèle de classification personnalisée, vous avez besoin de l’entraîner avec des données de qualité. Cet article explique comment vous devez aborder la sélection et la préparation de vos données ainsi que la définition d’un schéma. Un schéma définit les classes dont vous avez besoin pour que votre modèle classe votre texte au moment de l’exécution et constitue la première étape du [développement d’une application de classification personnalisée](../overview.md#application-development-lifecycle).


## <a name="data-selection"></a>Sélection de données

La qualité des données avec lesquelles vous entraînez votre modèle affecte considérablement ses performances.

* Utilisez des données réelles qui reflètent l’espace des problèmes de votre domaine pour entraîner efficacement votre modèle. Vous pouvez utiliser des données synthétiques pour accélérer le processus d’entraînement initial du modèle. Toutefois, elles diffèrent probablement de vos données réelles et rendent le modèle moins efficace quand il est utilisé.

* Équilibrez autant que possible la distribution de vos données sans trop vous éloigner de leur distribution réelle.

* Utilisez des données diversifiées chaque fois que cela est possible pour éviter le surajustement de votre modèle. Une diversité moindre des données d’entraînement peut conduire votre modèle à apprendre de fausses corrélations qui n’existent peut-être pas dans les données réelles. 
 
* Évitez les fichiers dupliqués dans vos données. Les données dupliquées ont un effet négatif sur le processus d’entraînement ainsi que sur les métriques et les performances du modèle. 

* Tenez compte de la provenance de vos données. Si vous collectez les données auprès d’une personne, d’un service ou dans le cadre de votre scénario, le modèle risque probablement de manquer de la diversité nécessaire. 

> [!NOTE]
> Si vos fichiers sont en plusieurs langues, sélectionnez l’option **plusieurs langues** durant la [création du projet](../quickstart.md), puis affectez à l’option **langue** la langue dans laquelle se trouve la majorité de vos fichiers.

## <a name="data-preparation"></a>Préparation des données

En guise de prérequis à la création d’un projet de classification de texte personnalisée, vos données d’entraînement doivent être chargées sur un conteneur d’objets blob dans votre compte de stockage. Vous pouvez créer et charger des fichiers d’entraînement directement à partir d’Azure ou via l’outil Explorateur Stockage Azure. L’outil Explorateur Stockage Azure vous permet de charger plus de données rapidement.  

* [Créer et charger des fichiers à partir d’Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Créer et charger des fichiers en utilisant l’Explorateur Stockage Azure](/azure/vs-azure-tools-storage-explorer-blobs)

Vous ne pouvez utiliser que des fichiers `.txt` fichiers de texte personnalisé. Si vos données sont dans un autre format, vous pouvez utiliser la [commande d’analyse CLUtils](https://github.com/microsoft/CogSLanguageUtilities/blob/main/CLUtils/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) pour changer de format de fichier.

 Vous pouvez charger un jeu de données annoté, ou charger un jeu non annoté et [étiqueter vos données](../how-to/tag-data.md) dans Language Studio. 
 
## <a name="schema-design"></a>Conception du schéma

Le schéma définit les classes dont vous avez besoin pour que votre modèle classe votre texte au moment de l’exécution.

* **Examiner et identifier** : passez en revue les fichiers de votre jeu de données pour connaître leur structure et leur contenu, puis identifiez la manière dont vous souhaitez classer vos données. 

    Par exemple, si vous classez des tickets de support, vous pouvez avoir besoin des classes suivantes : *problème de connexion*, *problème matériel*, *problème de connectivité* et *demande d’équipement neuf*.

* **Éviter toute ambiguïté dans les classes** : une ambiguïté se produit lorsque les classes que vous spécifiez partagent une signification similaire. Plus votre schéma est ambigu, plus vous devez entraîner votre modèle avec des données étiquetées.  

    Par exemple, si vous classez des recettes alimentaires, elles peuvent être similaires à une étendue. Pour faire la différence entre une *recette de dessert* et une *recette de plat principal*, vous devrez peut-être baliser d’autres exemples pour aider votre modèle à faire la distinction entre les deux classes. Le fait de lever les ambiguïtés permet de gagner du temps et d’obtenir de meilleurs résultats. 

* **Données hors de l’étendue** : quand vous utilisez votre modèle en production, pensez à ajouter une classe *hors de portée* à votre schéma si vous pensez que des fichiers n’appartiennent à aucune de vos classes. Ajoutez ensuite quelques fichiers à votre jeu de données à baliser comme étant *hors de portée*. Le modèle peut apprendre à reconnaître les fichiers non pertinents et à prédire leurs balises en conséquence.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas encore fait, créez un projet de classification personnalisé. Si vous utilisez une classification personnalisée pour la première fois, suivez le [guide de démarrage rapide](../quickstart.md) afin de créer un exemple de projet. Vous pouvez également voir les [spécifications du projet](../how-to/create-project.md) pour plus d’informations sur ce dont vous avez besoin pour créer un projet. 
