---
title: Présentation de l’API Détecteur d’anomalies
titleSuffix: Azure Cognitive Services
description: Utilisez les algorithmes de l’API Détecteur d’anomalies pour appliquer la détection d’anomalies à vos données de séries chronologiques.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 75a040b8c2b480d0c82ef2cab6a953d230f6ffb7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371103"
---
# <a name="what-is-the-anomaly-detector-univariate-api"></a>Qu’est-ce que l’API univariée Détecteur d’anomalies ?

L’API Détecteur d’anomalies vous permet de superviser et de détecter des anomalies dans vos données de séries chronologiques sans avoir à connaître le machine learning. Les algorithmes de l’API Détecteur d’anomalies s’adaptent en identifiant et en appliquant automatiquement les modèles les mieux adaptés à vos données, indépendamment du secteur d’activité, du scénario ou du volume de données. À l’aide de vos données de série chronologique, l’API détermine les limites pour la détection des anomalies, les valeurs attendues et les points de données qui constituent des anomalies.

![Détecter des modifications de modèle dans les demandes de service](./media/anomaly_detection2.png)

L’utilisation du Détecteur d’anomalies ne nécessite aucune expérience préalable en machine learning. L’API REST vous permet d’intégrer facilement le service dans vos applications et processus.

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./Quickstarts/client-libraries.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps. 
* Les [guides patiques](./how-to/identify-anomalies.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [articles conceptuels](./concepts/anomaly-detection-best-practices.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.
* Les [tutoriels](./tutorials/batch-anomaly-detection-powerbi.md) sont des guides plus longs qui montrent comment utiliser ce service en tant que composant dans des solutions métier plus larges.

## <a name="features"></a>Fonctionnalités

Grâce au détecteur d’anomalies, vous pouvez automatiquement détecter des anomalies, dans l’ensemble de vos données de série chronologique ou en temps réel.

|Fonctionnalité  |Description  |
|---------|---------|
|Détection d’anomalie en temps réel. | Détectez les anomalies dans vos données de diffusion en continu à l’aide des points de données préalablement vus pour déterminer si le dernier point est une anomalie. Cette opération génère un modèle à l’aide des points de données que vous envoyez et détermine si le point cible est une anomalie. En appelant l’API avec chaque nouveau point de données que vous générez, vous pouvez surveiller vos données au moment de leur création. |
|Détecter les anomalies tout au long de votre jeu de données par lots. | Utilisez votre série chronologique pour détecter d’éventuelles anomalies dans l’ensemble de vos données. Cette opération génère un modèle à l’aide de vos données de série chronologique complètes, chaque point étant analysé avec le même modèle.         |
|Détecter les points de changement tout au long de votre jeu de données dans un même lot. | Utilisez votre série chronologique pour détecter les points de changement de tendance présents dans vos données. Cette opération génère un modèle à l’aide de vos données de série chronologique complètes, chaque point étant analysé avec le même modèle.    |
| Obtenir des informations supplémentaires sur vos données. | Obtenez des détails utiles sur vos données et sur les anomalies constatées, notamment les valeurs attendues ainsi que les limites et les positions des anomalies. |
| Ajuster les limites de détection des anomalies. | L’API Détecteur d’anomalies crée automatiquement des limites pour la détection des anomalies. Ajustez ces limites pour augmenter ou diminuer la sensibilité de l’API aux anomalies de données et mieux l’adapter à vos données. |

## <a name="demo"></a>Démonstration

Consultez cette [démonstration interactive](https://aka.ms/adDemo) pour comprendre le fonctionnement du détecteur d’anomalies.
Pour exécuter la démonstration, vous devez créer une ressource Détecteur d’anomalies et récupérer la clé API ainsi que le point de terminaison.

## <a name="notebook"></a>Notebook

Pour savoir comment appeler l’API Détecteur d’anomalies, essayez ce [notebook](https://aka.ms/adNotebook). Ce notebook Jupyter montre comment envoyer une demande d’API et visualiser le résultat.

Pour exécuter le notebook, vous devez obtenir une **clé d’abonnement** d’API Détecteur d’anomalies valide et un **point de terminaison d’API**. Dans le notebook, ajoutez votre clé d’abonnement API Détecteur d’anomalies valide à la variable `subscription_key`, et remplacez la variable `endpoint` par votre point de terminaison.

## <a name="workflow"></a>Workflow

L’API Détecteur d'anomalies étant un service web RESTful, elle peut être facilement appelée à partir de n’importe quel langage de programmation capable d’exécuter des requêtes HTTP et d’analyser des réponses JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Après l’inscription :

1. Prenez vos données de série chronologique et convertissez-les en format JSON valide. Utilisez les [meilleures pratiques](concepts/anomaly-detection-best-practices.md) lors de la préparation de vos données pour obtenir les meilleurs résultats.
1. Envoyez une demande à l’API Détecteur d’anomalies avec vos données.
1. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="algorithms"></a>Algorithmes

* Pour plus d’informations sur les algorithmes utilisés, consultez les blogs techniques suivants :
    * [Présentation de l’API Détecteur d’anomalies](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Présentation de l’algorithme SR-CNN dans le Détecteur d’anomalies Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Pour plus d’informations sur les algorithmes SR-CNN de pointe développés par Microsoft, lisez le document [Time-Series Anomaly Detection Service at Microsoft](https://arxiv.org/abs/1906.03821) (accepté par KDD 2019) .

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Disponibilité et redondance du service

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Le service Détecteur d’anomalies est-il résilient au zones ?

Oui. Le service Détecteur d’anomalies est résilient aux zones par défaut.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Comment configurer le service Détecteur d’anomalies pour le rendre résilient aux zones ?

Aucune configuration client n’est nécessaire pour activer la résilience des zones. La résilience aux zones pour les ressources du Détecteur d’anomalies est disponible par défaut et gérée par le service lui-même.

## <a name="deploy-on-premises-using-docker-containers"></a>Déployer localement en utilisant des conteneurs Docker

[Utilisez les conteneurs Détecteur d'anomalies](anomaly-detector-container-howto.md) pour déployer localement des fonctionnalités d’API. Les conteneurs Docker vous permettent de rapprocher davantage le service de vos données, ce qui peut être souhaitable pour des raisons de conformité, de sécurité ou opérationnelles.

## <a name="join-the-anomaly-detector-community"></a>Rejoindre la communauté du détecteur d’anomalies

* Rejoindre le [groupe Anomaly Detector Advisors sur Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Détecter des anomalies dans vos données de séries chronologiques avec le Détecteur d’anomalies](quickstarts/client-libraries.md)
* [Démonstration en ligne](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook) de l’API Détecteur d’anomalies
* La [référence d’API REST](https://aka.ms/anomaly-detector-rest-api-ref) du Détecteur d'anomalies