---
title: Confidentialité différentielle dans le Machine Learning (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez la confidentialité différentielle et comment des systèmes privés préservent de manière différentielle la confidentialité des données.
author: luisquintanilla
ms.author: luquinta
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.custom: responsible-ml, mktng-kw-nov2021
ms.openlocfilehash: fc3606a4d893152b87fc5f5f47d987c16ce65724
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842554"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Qu’est-ce que la confidentialité différentielle en matière d’apprentissage automatique (préversion) ?

Apprenez-en davantage sur la confidentialité différentielle dans le Machine Learning et son fonctionnement.

Les préoccupations liées à la confidentialité et à la sécurité augmentent à mesure qu’augmente la quantité de données qu’une organisation collecte et utilise à des fins d’analyse. Les analyses requièrent des données. En règle générale, plus il y a de données utilisées pour effectuer l’apprentissage de modèles Machine Learning, plus ceux-ci sont précis. Lorsque des informations personnelles sont utilisées pour ces analyses, il est particulièrement important que les données restent confidentielles tout au long de leur utilisation.

## <a name="how-differential-privacy-works"></a>Fonctionnement de la confidentialité différentielle

La confidentialité différentielle est un ensemble de systèmes et pratiques qui permettent de préserver la sécurité et la confidentialité des données des personnes. Dans les solutions d’apprentissage automatique, une confidentialité différentielle peut être requise en lien avec la conformité réglementaire.

> [!div class="mx-imgBorder"]
> ![Processus de Machine Learning avec confidentialité différentielle](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

Dans les scénarios traditionnels, les données brutes sont stockées dans des fichiers et des bases de données. Lorsque des utilisateurs analysent des données, ils utilisent généralement des données brutes. C’est problématique, car cela peut entraîner une violation de données à caractère personnel. La confidentialité différentielle tente de résoudre ce problème en ajoutant du « bruit » ou une composante aléatoire aux données afin que les utilisateurs ne puissent pas identifier des points de données individuels. Un tel système offre au moins une possibilité de « démenti raisonnable ». Par conséquent, la confidentialité des individus est préservée avec un impact limité sur la précision des données.

Dans les systèmes assortis d’une confidentialité différentielle, les données sont partagées via des demandes appelées **requêtes**. Quand un utilisateur soumet une requête de données, des opérations appelées **mécanismes de confidentialité** ajoutent du bruit aux données demandées. Ces mécanismes de confidentialité renvoient des *données approximatives* au lieu de données brutes. Le résultat de cette préservation de la confidentialité apparaît dans un **rapport**. Les rapports se composent de deux parties : les données réelles calculées et une description de la façon dont elles ont été créées.

## <a name="differential-privacy-metrics"></a>Métriques de confidentialité différentielle

La confidentialité différentielle vise à éviter qu’un utilisateur puisse produire un nombre indéfini de rapports pour révéler des données sensibles. Une valeur appelée **epsilon** mesure le niveau de bruit ou de confidentialité d’un rapport. La valeur epsilon est inversement proportionnelle au bruit ou à la confidentialité. Plus la valeur epsilon est basse, plus les données sont bruyantes (et confidentielles).

Les valeurs epsilon ne sont pas négatives. Les valeurs inférieures à 1 offrent une possibilité de « démenti raisonnable ». Toute valeur supérieure à 1 est associée à un risque plus élevé d’exposition des données réelles. Lorsque vous implémentez des solutions d’apprentissage automatique avec une confidentialité différentielle, vous souhaitez disposer de données avec des valeurs epsilon comprises entre 0 et 1.

Une autre valeur directement corrélée à la valeur epsilon est le valeur **delta**. La valeur delta indique la probabilité qu’un rapport ne soit pas entièrement confidentiel. La valeur delta est directement proportionnelle à la valeur epsilon. Ces valeurs étant corrélées, la valeur epsilon est utilisée plus fréquemment.

## <a name="limit-queries-with-a-privacy-budget"></a>Limiter les requêtes avec un budget de confidentialité

Pour garantir la confidentialité de systèmes où plusieurs requêtes sont autorisées, la confidentialité différentielle définit une limite de débit. Cette limite est appelée **budget de confidentialité**. Les budgets de confidentialité empêchent la recréation de données via plusieurs requêtes. Les budgets de confidentialité sont associés à un volume epsilon, généralement compris entre 1 et 3, pour limiter le risque de réidentification. Au fur et à mesure de la génération de rapports, les budgets de confidentialité assurent le suivi de la valeur epsilon des rapports individuels, ainsi que de l’agrégat de tous les rapports. Une fois le budget de confidentialité dépensé ou épuisé, les utilisateurs ne peuvent plus accéder aux données. 

## <a name="reliability-of-data"></a>Fiabilité des données

Même si la préservation de la confidentialité doit être l’objectif, il existe un compromis sur le plan de l’exploitabilité et de la fiabilité des données. En analytique données, l’exactitude peut être considérée comme une mesure de l’incertitude introduite par des erreurs d’échantillonnage. Cette incertitude tend s’inscrire dans certaines limites. L’**exactitude** du point de vue de la confidentialité différentielle, mesure plutôt la fiabilité des données, qui est affectée par l’incertitude qu’introduisent les mécanismes de confidentialité. En deux mots, un niveau élevé de bruit ou de confidentialité se traduit par des données dont la valeur epsilon, l’exactitude et la fiabilité sont moindres. 

## <a name="open-source-differential-privacy-libraries"></a>Bibliothèques de confidentialité différentielle open source

SmartNoise est un projet open source contenant des composants destinés à créer des solutions d’apprentissage automatique avec une confidentialité différentielle. SmartNoise est constitué des composants de niveau supérieur suivants :

- Bibliothèque principale de SmartNoise
- Bibliothèque du Kit de développement logiciel (SDK) SmartNoise

### <a name="smartnoise-core"></a>Cœur de SmartNoise

La bibliothèque principale comprend les mécanismes de confidentialité suivants pour l’implémentation d’un système assorti d’une confidentialité différentielle :

|Composant  |Description  |
|---------|---------|
|Analyse     | Description graphique des calculs arbitraires. |
|Validateur     | Bibliothèque Rust contenant un ensemble d’outils permettant de vérifier et dériver les conditions nécessaires pour qu’une analyse bénéficie d’une confidentialité différentielle.          |
|Runtime     | Moyen d’exécuter l’analyse. Le runtime de référence est écrit en Rust, mais vous pouvez écrire des runtimes à l’aide de n’importe quelle infrastructure de calcul telle que SQL ou Spark, en fonction de vos besoins en matière de données.        |
|Liaisons     | Liaisons de langage et bibliothèques d’assistance pour générer des analyses. Actuellement, SmartNoise fournit des liaisons Python. |

### <a name="smartnoise-sdk"></a>Kit de développement logiciel (SDK) SmartNoise

La bibliothèque système fournit les outils et services suivants pour travailler avec des données tabulaires et relationnelles :

|Composant  |Description  |
|---------|---------|
|Accès aux données     | Bibliothèque qui intercepte et traite des requêtes SQL, et génère des rapports. Cette bibliothèque est implémentée en Python et prend en charge les sources de données ODBC et DBAPI suivantes :<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Service     | Service d’exécution fournissant un point de terminaison REST pour traiter des demandes ou requêtes sur des sources de données partagées. Le service est conçu pour permettre la composition de modules de confidentialité différentielle qui opèrent sur des demandes contenant différentes valeurs delta et epsilon, également appelées demandes hétérogènes. Cette implémentation de référence explique l’impact supplémentaire des requêtes sur les données corrélées. |
|Évaluateur     | Évaluateur stochastique qui vérifie les violations de confidentialité, l’exactitude et le biais. L’évaluateur prend en charge les tests suivants : <ul><li>Test de confidentialité : détermine si un rapport respecte les conditions de la confidentialité différentielle.</li><li>Test d’exactitude : mesure si la fiabilité des rapports s’inscrit dans les limites supérieure et inférieure, compte tenu d’un niveau de confiance de 95 %.</li><li>Test d’utilitaire : détermine si les limites de confiance d’un rapport sont suffisamment proches des données tout en optimisant la confidentialité.</li><li>Test de biais : mesure la distribution des rapports pour des requêtes répétées afin de s’assurer qu’elles ne sont pas déséquilibrées.</li></ul> |

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la confidentialité différentielle dans l’apprentissage automatique : 

 - [Comment créer un système privé de manière différentielle](how-to-differential-privacy.md) dans Azure Machine Learning.

 - Pour en savoir plus sur les composants de SmartNoise, consultez les dépôts GitHub et pour le [Cœur de SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core), le [Kit de développement logiciel (SDK) SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-sdk) et les [Exemples SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples).