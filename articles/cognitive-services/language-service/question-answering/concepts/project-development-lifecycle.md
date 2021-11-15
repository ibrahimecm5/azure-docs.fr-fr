---
title: Cycle de vie des projets - réponses aux questions
description: Les réponses aux questions apprennent mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de déploiement et de collecte des données à partir de requêtes du point de terminaison.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 193d01adac85681c39554ec1f6cab24dce9082f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479460"
---
# <a name="question-answering-project-lifecycle"></a>Cycle de vie du projet de réponses aux questions

Les réponses aux questions apprennent mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de déploiement et de collecte des données à partir de requêtes du point de terminaison.

## <a name="creating-a-project"></a>Création d’un projet

Les projets/bases de connaissances des réponses aux questions fournissent une réponse idéale à une requête de l’utilisateur en fonction du contenu du projet. La création d’un projet est une action ponctuelle destinée à créer un référentiel contenant des questions, des réponses et des métadonnées associées. Un projet peut être créé en analysant du contenu préexistant comme les sources suivantes :

- Pages de FAQ
- Manuels de produit
- Paires Q-A

Découvrez comment [créer une base de connaissances](../how-to/create-test-deploy.md).

## <a name="testing-and-updating-your-project"></a>Test et mise à jour de votre projet

Le projet peut être testé dès qu’il contient des données, qui ont été ajoutées manuellement ou par le biais d’une extraction automatique. Des tests interactifs peuvent être réalisés dans le portail Language Studio, dans le menu de réponses aux questions personnalisées via le volet **Test**. Vous entrez des requêtes utilisateur courantes. Ensuite, vous vérifiez que les réponses sont retournées avec la réponse correcte et un score de confiance suffisant.

* **Pour améliorer les scores de confiance faibles** : ajoutez des questions alternatives.
* **Si une requête retourne par erreur la [réponse par défaut](../How-to/change-default-answer.md)**  : ajoutez de nouvelles réponses à la bonne question.

Poursuivez cette boucle de test-mise à jour jusqu’à ce que vous soyez satisfait des résultats.

## <a name="deploy-your-project"></a>Déployez votre projet

Une fois que vous avez terminé de tester le projet, vous pouvez le déployer en production. Le déploiement envoie (push) la dernière version du projet testé à un index de Recherche cognitive Azure dédié, qui représente le projet/base de connaissances **publié(e)** . Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.

En raison de l’action de déploiement, toute autre modification apportée à la version de test de la base de connaissances n’affecte pas la version publiée. La version publiée peut être active dans une application de production.

Chacun de ces projets/bases de connaissances peut être ciblé(e) séparément pour le test.

## <a name="monitor-usage"></a>Surveiller l’utilisation

Pour être en mesure d’enregistrer les journaux de conversation de votre service et d’obtenir des analyses supplémentaires, vous devez activer les [journaux de diagnostic Azure Monitor](../how-to/analytics.md) après avoir créé votre ressource de langue.

Selon ce que vous apprenez de vos analyses, effectuez les mises à jour appropriées pour votre projet.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Contrôle de version des données de votre base de connaissances

Le contrôle de version pour les données est fourni par le biais des fonctionnalités d’importation/exportation de la page projet dans la section de réponses aux questions du portail Language Studio.

Vous pouvez sauvegarder un projet/base de connaissances en l’exportant au format `.tsv` ou `.xls`. Une fois l’exportation effectuée, incluez ce fichier dans le cadre de votre vérification de contrôle de code source standard.

Si vous avez besoin de revenir à une version spécifique, vous devez importer ce fichier à partir de votre système local. Un projet exporté **doit** uniquement être utilisé via l’importation sur la page du projet. Elle ne peut pas être utilisée comme source de données de fichier ou de document d’URL. Les questions et réponses qui se trouvent actuellement dans la base de connaissances sont remplacées par le contenu du fichier importé.

## <a name="test-and-production-project"></a>Projet de test et de production

Un projet/base de connaissances est le référentiel d’ensembles de questions-réponses créés, tenus à jour et utilisés par le biais des réponses aux questions. Chaque ressource de langue peut contenir plusieurs projets/bases de connaissances.

Un projet/base de connaissances a deux états : *test* et *publié(e)* .

### <a name="test-projectknowledge-base"></a>Projet/base de connaissances de test

La *base de connaissances de test* correspond à la version actuellement modifiée et enregistrée. La version de test a été testée pour garantir la précision et l’exhaustivité des réponses. Les modifications apportées à la base de connaissances de test n’affectent pas l’utilisateur final de votre application ou bot conversationnel. La base de connaissances de test est connue comme `test` dans la requête HTTP. La base de connaissances `test` est disponible dans le volet interactif **Test** du portail Language Studio.

### <a name="production-projectknowledge-base"></a>Projet/base de connaissances de production

La *base de connaissances publiée* est la version utilisée dans votre bot conversationnel ou application. La publication d’une base de connaissances place le contenu de sa version de test dans sa version publiée. La base de connaissances publiée est la version utilisée par l’application via le point de terminaison. Assurez-vous que le contenu est correct et bien testé. La base de connaissances de test est connue comme `prod` dans la requête HTTP.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Suggestions d’apprentissage actif](../tutorials/active-learning.md)