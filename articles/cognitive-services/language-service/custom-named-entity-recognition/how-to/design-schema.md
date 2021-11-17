---
title: Préparation des données et conception d’un schéma pour une NER personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez comment sélectionner et préparer les données pour réussir à créer des projets de NER personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 35f57b29f20e4b9ca0105c2395a34376e25a8c8a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489111"
---
# <a name="how-to-prepare-data-and-define-a-schema-for-custom-ner"></a>Comment préparer des données et définir un schéma pour une NER personnalisée

Pour créer un modèle de NER personnalisée, vous avez besoin de l’entraîner avec des données de qualité. Cet article explique comment vous devez aborder la sélection et la préparation de vos données ainsi que la définition d’un schéma. Le schéma définit les types/catégories d’entité que votre modèle doit extraire du texte au moment de l’exécution. Il s’agit de la première étape du [développement d’un modèle d’extraction d’entité](../overview.md#application-development-lifecycle).

## <a name="schema-design"></a>Conception du schéma

Le schéma définit les types/catégories d’entité que votre modèle doit extraire du texte au moment de l’exécution.

* Passez en revue les fichiers de votre jeu de données pour vous familiariser avec leur format et leur structure.

* Identifiez les [entités](../glossary.md#entity) à extraire des données.

    Par exemple, si vous extrayez des entités à partir d’e-mails de support, vous devrez peut-être extraire le « Nom du client », le « Nom du produit », le « Problème du client », la « Date de demande » et les « Informations de contact ».

* Évitez l’ambiguïté des types d’entité.

    Une **ambiguïté** se produit quand les types que vous sélectionnez sont similaires entre eux. Plus votre schéma est ambigu, plus vous devez entraîner votre modèle avec des données étiquetées.

    Par exemple, si vous extrayez les données d’un contrat juridique, pour extraire le « Nom de la première partie » et le « Nom de la second partie », vous devez ajouter d’autres exemples afin de lever l’ambiguïté, car les noms des deux parties se ressemblent. Le fait de lever les ambiguïtés permet de gagner du temps, d’éviter les efforts inutiles et d’obtenir de meilleurs résultats.

* Évitez les entités complexes. Les entités complexes peuvent être difficiles à extraire du texte de manière précise. Divisez-les en plusieurs entités.

    Par exemple, le modèle a du mal à extraire « Adresse » s’il n’est pas décomposé en entités plus petites. Il existe tellement de variantes dans l’apparence des adresses qu’un grand nombre d’entités étiquetées est nécessaire pour apprendre au modèle à extraire une adresse, dans son ensemble, sans la décomposer. Toutefois, si vous remplacez « Adresse » par « Nom de rue », « Boîte postale », « Code postal », « Ville » et « Département », le modèle nécessite moins d’étiquettes par entité.

## <a name="data-selection"></a>Sélection de données

La qualité des données avec lesquelles vous entraînez votre modèle affecte considérablement ses performances.

* Utilisez des données réelles qui reflètent l’espace des problèmes de votre domaine pour entraîner efficacement votre modèle. Vous pouvez utiliser des données synthétiques pour accélérer le processus d’entraînement initial du modèle. Toutefois, elles diffèrent probablement de vos données réelles et rendent le modèle moins efficace quand il est utilisé.

* Équilibrez autant que possible la distribution de vos données sans trop vous éloigner de leur distribution réelle. Par exemple, si vous entraînez votre modèle pour extraire des entités de documents juridiques qui peuvent se présenter dans de nombreux formats et langues différents, vous devez fournir des exemples qui illustrent la diversité attendue dans la vie réelle.

* Utilisez des données diversifiées chaque fois que cela est possible pour éviter le surajustement de votre modèle. Une diversité moindre des données d’entraînement peut conduire votre modèle à apprendre de fausses corrélations qui n’existent peut-être pas dans les données réelles. 
 
* Évitez les fichiers dupliqués dans vos données. Les données dupliquées ont un effet négatif sur le processus d’entraînement ainsi que sur les métriques et les performances du modèle. 

* Tenez compte de la provenance de vos données. Si vous collectez les données auprès d’une personne, d’un service ou dans le cadre de votre scénario, le modèle risque probablement de manquer de la diversité nécessaire. 

> [!NOTE]
> Si vos fichiers sont en plusieurs langues, sélectionnez l’option **plusieurs langues** durant la [création du projet](../quickstart.md), puis affectez à l’option **langue** la langue dans laquelle se trouve la majorité de vos fichiers.

## <a name="data-preparation"></a>Préparation des données

En guise de prérequis pour la création d’un projet, vos données d’entraînement doivent être chargées vers un conteneur d’objets blob dans votre compte de stockage. Vous pouvez créer et charger des fichiers d’entraînement directement à partir d’Azure ou via l’outil Explorateur Stockage Azure. L’outil Explorateur Stockage Azure vous permet de charger plus de données rapidement.  

* [Créer et charger des fichiers à partir d’Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Créer et charger des fichiers à l’aide de l’outil Explorateur Stockage Azure](/azure/vs-azure-tools-storage-explorer-blobs)

Vous pouvez uniquement utiliser des fichiers `.txt`. Si vos données sont dans un autre format, vous pouvez utiliser la [commande d’analyse CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) pour changer de format de fichier.

 Vous pouvez charger un jeu de données annoté, ou charger un jeu non annoté et [étiqueter vos données](../how-to/tag-data.md) dans Language Studio. 
 
## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, créez un projet de NER personnalisée. Si vous utilisez une NER personnalisée pour la première fois, suivez le [guide de démarrage rapide](../quickstart.md) afin de créer un exemple de projet. Vous pouvez également consulter l’[article de guide pratique](../how-to/create-project.md) pour obtenir des informations supplémentaires sur les éléments nécessaires à la création d’un projet. 
