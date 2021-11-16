---
title: Intégration du Machine Learning open source
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser les infrastructures de Machine Learning open source Python pour entraîner, déployer et gérer des solutions de Machine Learning de bout en bout dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.custom: mktng-kw-nov2021
ms.date: 11/04/2021
ms.openlocfilehash: 9809169a8da6965c8d1fdbb741ddd4817f8d2af7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850182"
---
# <a name="use-open-source-machine-learning-libraries-and-platforms-with-azure-machine-learning"></a>Utiliser des bibliothèques et plateformes d’apprentissage automatique open source avec Azure Machine Learning

Cet article traite des les bibliothèques et plateformes d’apprentissage automatique Python open source que vous pouvez utiliser avec Azure Machine Learning. Formez, déployez et gérez le processus d’apprentissage automatique de bout en bout à l’aide de projets open source de votre choix.  Utilisez les outils de développement, tels que Jupyter Notebooks et Visual Studio Code, pour tirer parti de vos modèles et scripts existants dans Azure Machine Learning.  

## <a name="train-open-source-machine-learning-models"></a>Effectuer l’apprentissage des modèles Machine Learning open source

Le processus de formation Machine Learning implique l’application d’algorithmes à vos données afin d’obtenir une tâche ou de résoudre un problème. Selon le problème, vous pouvez choisir différents algorithmes mieux adaptés à la tâche et à vos données. Pour plus d’informations sur les problématiques que vous pouvez résoudre avec l’apprentissage automatique, consultez l’article [Apprentissage profond et apprentissage automatique](./concept-deep-learning-vs-machine-learning.md) et l’aide-mémoire sur l’[algorithme d’apprentissage automatique](algorithm-cheat-sheet.md).

### <a name="classical-machine-learning-scikit-learn"></a>Machine Learning classique : scikit-learn

Pour les tâches de formation impliquant des algorithmes de Machine Learning classiques, tels que la classification, le clustering et la régression, vous pouvez utiliser un exemple comme Scikit-learn. Pour savoir comment effectuer l’apprentissage d’un modèle de classification fleur, consultez [l’article sur l’apprentissage avec scikit-learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Réseaux neuronaux : PyTorch, TensorFlow, Keras

Les algorithmes de Machine Learning open source appelés réseaux neuronaux, un sous-ensemble du Machine Learning, sont utiles pour la formation des modèles de Deep Learning dans Azure Machine Learning.

Les infrastructures de Deep Learning open source et les guides pratiques incluent :

 *  [PyTorch](https://github.com/pytorch/pytorch) : [Effectuer l'apprentissage d’un modèle de classification d’images Deep Learning à l’aide de l’apprentissage de transfert](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow) : [Reconnaître les chiffres manuscrits à l’aide de TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras) : [Générer un réseau neuronal pour analyser des images à l’aide de Keras](how-to-train-keras.md)

### <a name="transfer-learning"></a>Transfer learning

La formation d’un modèle Deep Learning à partir de zéro nécessite souvent de grandes quantités de temps, de données et de ressources de calcul. L’apprentissage de transfert peut vous permettre de raccourcir le processus de formation. L’apprentissage de transfert est une technique qui applique les connaissances acquises lors de la résolution d’un problème à un problème différent, mais connexe. Cela signifie que vous pouvez utiliser un modèle existant et le réaffecter. Pour en savoir plus sur l’apprentissage de transfert, consultez l’article [Deep Learning et Machine Learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning).

### <a name="reinforcement-learning-ray-rllib"></a>Apprentissage par renforcement : Ray RLLib

L’apprentissage par renforcement est une technique d’intelligence artificielle qui effectue l’apprentissage des modèles à l’aide d’actions, d’états et de récompenses : Les agents d’apprentissage par renforcement apprennent à suivre un ensemble d’actions prédéfinies qui optimisent les récompenses spécifiées en fonction de l’état actuel de leur environnement. 

Le projet [Ray RLLib](https://github.com/ray-project/ray) comporte une série de fonctionnalités qui permettent une scalabilité élevée tout au long du processus d’apprentissage. Le processus itératif est à la fois fastidieux et gourmand en ressources, car les agents d’apprentissage par renforcement essaient d’apprendre la meilleure façon d’effectuer une tâche.  Ray RLLib prend également en charge en mode natif des infrastructures de Deep Learning telles que TensorFlow et PyTorch.  

Pour savoir comment utiliser Ray RLLib avec Azure Machine Learning, consultez [Comment effectuer l'apprentissage d’un modèle d’apprentissage par renforcement](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Surveiller les performances d'un modèle : TensorBoard

La formation d’un seul ou de plusieurs modèles nécessite la visualisation et l’inspection des métriques souhaitées pour s’assurer que le modèle fonctionne comme prévu. Vous pouvez [utiliser TensorBoard dans Azure Machine Learning pour suivre et visualiser les métriques d’essai](./how-to-monitor-tensorboard.md).

## <a name="responsible-ml-privacy-and-fairness"></a>Apprentissage automatique responsable : confidentialité et impartialité

### <a name="preserve-data-privacy-with-differential-privacy"></a>Préserver la confidentialité des données avec la confidentialité différentielle

Pour effectuer l’apprentissage d’un modèle Machine Learning, vous devez disposer de données. Parfois, ces données sont sensibles et il est important de s’assurer qu’elles sont sécurisées et privées. La confidentialité différentielle est une technique qui consiste à préserver la confidentialité des informations contenues dans un jeu de données. Pour plus d’informations, consultez l’article sur la [préservation de la confidentialité des données](concept-differential-privacy.md). 

Les kits de ressources sur la confidentialité différentielle open source (comme [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python)) vous aident à [assurer la confidentialité des données](how-to-differential-privacy.md) dans les solutions Azure Machine Learning.

### <a name="frameworks-for-interpretable-and-fair-models"></a>Infrastructures pour les modèles interprétables et équitables

Les systèmes de Machine Learning sont utilisés dans différents domaines de la société, tels que les services bancaires, l’éducation et la santé. Par conséquent, il est important que ces systèmes soient responsables des prédictions et des recommandations qu’ils effectuent pour empêcher des conséquences inattendues.

Les infrastructures open source comme [InterpretML](https://github.com/interpretml/interpret/) et Fairlearn (https://github.com/fairlearn/fairlearn) ) fonctionnent avec Azure Machine Learning et créent des modèles Machine Learning plus transparents et équitables.

Pour plus d’informations sur la création de modèles équitables et interprétables, consultez les articles suivants :

- [Interprétabilité des modèles dans Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpréter et expliquer les modèles Machine Learning](how-to-machine-learning-interpretability-aml.md)
- [Expliquer les modèles AutoML](how-to-machine-learning-interpretability-automl.md)
- [Atténuer l’impartialité dans les modèles Machine Learning](concept-fairness-ml.md)
- [Utiliser Azure Machine Learning pour l’équité du modèle de ressources](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Déploiement de modèle

Une fois que les modèles sont formés et prêts pour la production, vous devez choisir leur mode de déploiement. Azure Machine Learning propose différentes cibles de déploiement. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Normaliser les formats de modèle avec ONNX

Après la formation, le contenu du modèle, tel que les paramètres appris, est sérialisé et enregistré dans un fichier. Chaque infrastructure a son propre format de sérialisation. Lorsque vous travaillez avec des infrastructures et des outils différents, cela signifie que vous devez déployer des modèles en fonction des exigences de l’infrastructure. Pour normaliser ce processus, vous pouvez utiliser le format ONNX (Open Neural Network Exchange). ONNX est un format open source pour les modèles d’intelligence artificielle. ONNX prend en charge l’interopérabilité entre les frameworks. Cela signifie que vous pouvez effectuer l’apprentissage d’un modèle dans l’un des nombreux frameworks de Machine Learning populaires comme PyTorch, le convertir au format ONNX et consommer le modèle ONNX dans un autre framework comme ML.NET.

Pour plus d'informations sur ONNX et sur l’utilisation des modèles ONNX, consultez les articles suivants :

- [Créer et accélérer des modèles ML avec ONNX](concept-onnx.md)
- [Utiliser des modèles ONNX dans des applications .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Créer des packages de modèles et les déployer en tant que conteneurs

Les technologies de conteneur telles que Docker constituent une manière de déployer des modèles en tant que services web. Les conteneurs fournissent une plateforme et des ressources indépendantes pour créer et orchestrer des environnements logiciels reproductibles. Grâce à ces technologies de base, vous pouvez utiliser [des environnements préconfigurés](./how-to-use-environments.md), [des images de conteneur préconfigurées](./how-to-deploy-custom-container.md) ou des images personnalisées pour déployer vos modèles Machine Learning sur des [clusters Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python), par exemple. Pour les flux de travail gourmands en GPU, vous pouvez utiliser des outils tels que le serveur d’inférence NVIDIA Triton pour [effectuer des prédictions à l’aide des GPU](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Déploiements sécurisés avec chiffrement homomorphe

La sécurisation des déploiements est un élément important du processus de déploiement. Pour [déployer des services d’inférences chiffrés](how-to-homomorphic-encryption-seal.md), utilisez la bibliothèque Python open source `encrypted-inference`. Le package `encrypted inferencing` fournit des liaisons basées sur [Microsoft SEAL](https://github.com/Microsoft/SEAL), une bibliothèque de chiffrement homomorphe.

## <a name="machine-learning-operations-mlops"></a>Opérations de Machine Learning (MLOps)

Les opérations de Machine Learning (MLOps), généralement considérées comme des DevOps pour le Machine Learning, vous permettent de créer des flux de travail de Machine Learning plus transparents, résilients et reproductibles. Pour en savoir plus, consultez l’article [consacré à MLOps](./concept-model-management-and-deployment.md). 

À l’aide de pratiques DevOps comme l’intégration continue (CI) et le déploiement continu (CD), vous pouvez automatiser le cycle de vie de Machine Learning de bout en bout et capturer les données de gouvernance connexes. Vous pouvez définir votre [pipeline CI/CD de Machine Learning dans des actions GitHub](./how-to-github-actions-machine-learning.md) pour exécuter les tâches de formation et de déploiement Azure Machine Learning. 

La capture des dépendances logicielles, des métriques, des métadonnées, des données et du contrôle de version des modèles constitue une partie importante du processus MLOps, afin de générer des pipelines transparents, reproductibles et pouvant être audités. Pour cette tâche, vous pouvez [utiliser MLFlow dans Azure Machine Learning](how-to-use-mlflow.md), ainsi que lorsque vous [effectuez l’apprentissage de modèles Machine Learning dans Azure Databricks](./how-to-use-mlflow-azure-databricks.md). Vous pouvez également [déployer des modèles MLflow sous forme de service web Azure](how-to-deploy-mlflow-models.md).