---
title: Notes de publication de CLI (v2)
titleSuffix: Azure Machine Learning
description: En savoir plus sur les dernières mises à jour de l’interface CLI Azure Machine Learning (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: 9619b98ad824de757273cbe38567b5f9418686a3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131567205"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Notes de publication de l’interface CLI Azure Machine Learning (v2)

Dans cet article, découvrez les versions de l’interface CLI Azure Machine Learning (v2).

__Flux RSS__ : Recevez une notification quand cette page est mise à jour en copiant et collant l’URL suivante dans votre lecteur de flux : `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v201a6"></a>CLI Azure Machine Learning (v2) v2.0.1a6

- `az ml workspace`
  - Mise à jour du [schéma YAML de l’espace de travail](reference-yaml-workspace.md)
- `az ml compute`
  - Mise à jour des schémas YAML pour [AmlCompute](reference-yaml-compute-aml.md) et [Instance de calcul](reference-yaml-compute-instance.md)
  - Suppression de la prise en charge de l’attachement AKS hérité via `az ml compute attach`. L’attachement Kubernetes avec Azure ARC sera pris en charge dans la prochaine version
- `az ml datastore`
  - Mise à jour des schémas YAML pour [Objet blob Azure](reference-yaml-datastore-blob.md), [Fichier Azure](reference-yaml-datastore-files.md), et les magasins de données [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) et [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md)
  - Ajout de la prise en charge de la création des magasins de données Azure Data Lake Storage Gen1 et Gen2
- `az ml job`
  - Mise à jour des schémas YAML pour le [travail de commande](reference-yaml-job-command.md) et le [travail de balayage](reference-yaml-job-sweep.md)
  - Ajout de la prise en charge de l’exécution des travaux de pipeline ([schéma YAML de travail de pipeline](reference-yaml-job-pipeline.md))
  - Ajout de la prise en charge des littéraux d’entrée de travail et des URI de données d’entrée pour tous les types de travaux
  - Ajout de la prise en charge des sorties de travail pour tous les types de tâches
  - Modification de la syntaxe d’expression de `{ <expression> }` en `${{ <expression> }}`. Pour plus d’informations, consultez [Syntaxe d’expression pour la configuration des travaux Azure ML](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components)
- `az ml environment`
  - Mise à jour du [schéma YAML de l’environnement](reference-yaml-environment.md)
  - Ajout de la prise en charge de la création d’environnements à partir du contexte de génération Docker
- `az ml model`
  - Mise à jour du [schéma YAML du modèle](reference-yaml-model.md)
  - Ajout d’une nouvelle propriété `model_format` au modèle pour les scénarios de déploiement sans code
- `az ml dataset`
  - Renommage du sous-groupe `az ml data` en `az ml dataset`
  - Mise à jour du [schéma YAML du jeu de données](reference-yaml-dataset.md)
- `az ml component`
  - Ajout des commandes `az ml component` pour la gestion des composants Azure ML
  - Ajout de la prise en charge des composants de commande ([schéma YAML du composant de commande](reference-yaml-component-command.md))
- `az ml online-endpoint`
  - Sous-groupe `az ml endpoint` divisé en deux groupes distincts : `az ml online-endpoint` et `az ml batch-endpoint`
  - Mise à jour du [schéma YAML d’un point de terminaison en ligne](reference-yaml-endpoint-managed-online.md)
  - Ajout de la prise en charge des points de terminaison locaux pour les scénarios de développement/test
  - Ajout de la prise en charge du débogage VSCode interactif pour les points de terminaison locaux (ajout de l’indicateur `--vscode-debug` à `az ml batch-endpoint create/update`)
- `az ml online-deployment`
  - Sous-groupe `az ml deployment` divisé en deux groupes distincts : `az ml online-deployment` et `az ml batch-deployment`
  - Mise à jour du [schéma YAML de déploiement en ligne géré](reference-yaml-endpoint-managed-online.md)
  - Ajout de la prise en charge de la mise à l’échelle automatique via l’intégration avec Azure Monitor Autoscale
  - Ajout de la prise en charge de la mise à jour de plusieurs propriétés de déploiement en ligne dans la même opération de mise à jour
  - Ajout de la prise en charge de l’exécution d’opérations simultanées sur les déploiements sous le même point de terminaison
- `az ml batch-endpoint`
  - Sous-groupe `az ml endpoint` divisé en deux groupes distincts : `az ml online-endpoint` et `az ml batch-endpoint`
  - Mise à jour du [schéma YAML du point de terminaison de traitement de lots](reference-yaml-endpoint-batch.md)
  - Suppression de la propriété `traffic` ; remplacée par une propriété de déploiement par défaut configurable
  - Ajout de la prise en charge des URI de données d’entrée pour `az ml batch-endpoint invoke`
  - Ajout de la prise en charge de l’entrée de réseau virtuel (lien privé)
- `az ml batch-deployment`
  - Sous-groupe `az ml deployment` divisé en deux groupes distincts : `az ml online-deployment` et `az ml batch-deployment`
  - Mise à jour du [schéma YAML de déploiement par lots](reference-yaml-deployment-batch.md)

## <a name="2021-05-25"></a>2021-05-25

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Annonce de l’interface CLI (v2) (préversion) pour Azure Machine Learning

L’extension `ml` d’Azure CLI est l’interface de nouvelle génération d’Azure Machine Learning. Elle permet d’entraîner et de déployer des modèles à partir de la ligne de commande, avec des fonctionnalités qui accélèrent le scale-up et le scale-out pour la science des données tout en assurant le suivi du cycle de vie des modèles. [Installez l’extension et commencez](how-to-configure-cli.md).
