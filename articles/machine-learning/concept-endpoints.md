---
title: Que sont les points de terminaison (préversion) ?
titleSuffix: Azure Machine Learning
description: Découvrez comment les points de terminaison Azure Machine Learning (préversion) simplifient les déploiements de machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2, ignite-fall-2021
ms.date: 10/21/2021
ms.openlocfilehash: 7129286a0a91e27e80ebe4a6f3fc99e2b0e6c9b8
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135928"
---
# <a name="what-are-azure-machine-learning-endpoints-preview"></a>Que sont les points de terminaison Azure Machine Learning (préversion) ? 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Utilisez les points de terminaison Azure Machine Learning (préversion) pour simplifier les déploiements de modèle pour les déploiements d’inférence en temps réel et par lot. Les points de terminaison fournissent une interface unifiée pour appeler et gérer des déploiements de modèle pour différents types de calcul.

Cet article porte sur les points suivants :
> [!div class="checklist"]
> * Points de terminaison
> * Déploiements
> * Points de terminaison en ligne managés
> * Points de terminaison en ligne Kubernetes
> * Points de terminaison d’inférence par lot

## <a name="what-are-endpoints-and-deployments-preview"></a>Que sont les points de terminaison et les déploiements (préversion) ?

Après avoir entraîné un modèle Machine Learning, vous devez le déployer pour que d’autres personnes puissent l’utiliser afin d’effectuer l’inférence. Dans Azure Machine Learning, vous pouvez utiliser des **points de terminaison** (préversion) et des **déploiements** (préversion) à cette fin.

Un **point de terminaison** est un point de terminaison HTTPS que les clients peuvent appeler pour recevoir la sortie d’inférence (scoring) d’un modèle entraîné. Il offre : 
- Authentification basée sur la méthode « clé et jeton » 
- Arrêt SSL 
- Un URI de scoring stable (nom-point-de-terminaison.region.inference.ml.azure.com)


Un **déploiement** est un ensemble de ressources nécessaires pour héberger le modèle qui effectue l’inférence réelle. 

Un point de terminaison unique peut contenir plusieurs déploiements. Les points de terminaison et les déploiements sont des ressources Azure Resource Manager indépendantes présentées dans le portail Azure.

Azure Machine Learning utilise le concept de points de terminaison et de déploiements pour l’implémentation de différents types de points de terminaison : les [points de terminaison en ligne](#what-are-online-endpoints-preview) et les [points de terminaison de traitement de lots](#what-are-batch-endpoints-preview).

### <a name="multiple-developer-interfaces"></a>Plusieurs interfaces développeur

Créez et gérez des points de terminaison de traitement de lots et en ligne avec plusieurs outils de développement :
- L’interface de ligne de commande Azure
- API Azure Resource Manager/REST
- Portail web du studio Azure Machine Learning
- Portail Azure (informatique/administrateur)
- Prise en charge des pipelines MLOps CI/CD avec l’interface Azure CLI et les interfaces REST/ARM

## <a name="what-are-online-endpoints-preview"></a>Que sont les points de terminaison en ligne (préversion) ?

Les **points de terminaison en ligne** (préversion) sont des points de terminaison utilisés pour l’inférence en ligne (en temps réel). À la différence des **points de terminaison de traitement de lots**, les **points de terminaison en ligne** contiennent des **déploiements** prêts à recevoir des données des clients et pouvant renvoyer des réponses en temps réel.

Le diagramme suivant montre un point de terminaison en ligne qui a deux déploiements : « bleu » et « vert ». Le déploiement bleu utilise des machines virtuelles avec une référence de processeur et exécute la v1 d’un modèle. Le déploiement vert utilise des machines virtuelles avec une référence de GPU et utilise la v2 du modèle. Le point de terminaison est configuré pour acheminer 90 % du trafic entrant vers le déploiement bleu, tandis que le vert reçoit les 10 % restants.

:::image type="content" source="media/concept-endpoints/endpoint-concept.png" alt-text="Schéma montrant un point de terminaison divisant le trafic en deux déploiements":::

### <a name="online-endpoints-requirements"></a>Exigences relatives aux points de terminaison en ligne

Pour créer un point de terminaison en ligne, vous devez spécifier les éléments suivants :
- Fichiers de modèle (ou spécifiez un modèle inscrit dans votre espace de travail) 
- Script de scoring : code nécessaire pour effectuer le scoring/l’inférence
- Environnement : une image Docker avec des dépendances Conda ou un Dockerfile 
- Paramètres de mise à l’échelle et d’instance de calcul 

Découvrez comment déployer des points de terminaison en ligne à partir de l’interface [CLI](how-to-deploy-managed-online-endpoints.md) et du [portail web du studio](how-to-use-managed-online-endpoint-studio.md).

### <a name="test-and-deploy-locally-for-faster-debugging"></a>Tester et déployer localement pour un débogage plus rapide

Déployez localement pour tester vos points de terminaison sans les déployer dans le cloud. Azure Machine Learning crée une image Docker locale qui reproduit l’image Azure ML. Azure Machine Learning générera et exécutera les déploiements pour vous localement et mettra l’image en cache pour des itérations rapides.

### <a name="native-bluegreen-deployment"></a>Déploiement bleu/vert natif 

Rappelez-vous qu’un seul point de terminaison peut avoir plusieurs déploiements. Le point de terminaison en ligne peut effectuer l’équilibrage de charge pour donner un pourcentage du trafic à chaque déploiement.

L’allocation du trafic peut être utilisée pour effectuer des déploiements bleus/verts sûrs en équilibrant les requêtes entre les différentes instances.

> [!TIP]
> Une requête peut contourner l’équilibrage de charge du trafic configuré en incluant un en-tête HTTP de `azureml-model-deployment`. Définissez la valeur d’en-tête sur le nom du déploiement auquel vous souhaitez que la requête soit acheminée.

:::image type="content" source="media/concept-endpoints/traffic-allocation.png" alt-text="Capture d’écran montrant l’interface à curseur permettant de définir l’allocation du trafic entre les déploiements":::

Découvrez comment effectuer un [déploiement sûr vers des points de terminaison en ligne](how-to-safely-rollout-managed-endpoints.md).

### <a name="application-insights-integration"></a>Intégration d’Application Insights

Tous les points de terminaison en ligne s’intègrent à Application Insights. Ils permettent ainsi de superviser les Contrats de niveau de service et de diagnostiquer les problèmes. 

Cependant, les [points de terminaison en ligne managés](#managed-online-endpoints-vs-kubernetes-online-endpoints-preview) incluent également l’intégration standard aux journaux et métriques Azure.

### <a name="security"></a>Sécurité

- Authentification : clé et jetons Azure ML
- Identité managée : affectée par l’utilisateur et par le système (point de terminaison en ligne managé uniquement)
- SSL par défaut pour l’appel de point de terminaison

### <a name="autoscaling"></a>Mise à l’échelle automatique

La mise à l’échelle automatique exécute automatiquement la quantité appropriée de ressources pour gérer la charge sur votre application. Les points de terminaison gérés prennent en charge la mise à l’échelle automatique via l’intégration à la fonctionnalité de mise à l’échelle automatique d’[Azure Monitor](/azure/azure-monitor/autoscale/autoscale-overview.md). Vous pouvez configurer la mise à l’échelle basée sur les métriques (par exemple, utilisation du processeur > 70 %), la mise à l’échelle basée sur la planification (par exemple, les règles de mise à l’échelle pour les heures de pointe) ou une combinaison des deux.

:::image type="content" source="media/concept-endpoints/concept-autoscale.png" alt-text="Capture d’écran montrant que la mise à l’échelle automatique offre une mise à l’échelle flexible entre les instances min et max, en fonction des règles":::

### <a name="visual-studio-code-debugging"></a>Débogage Visual Studio Code

Visual Studio Code vous permet de déboguer les points de terminaison de manière interactive.

:::image type="content" source="media/concept-endpoints/visual-studio-code-full.png" alt-text="Capture d’écran du débogage du point de terminaison dans VSCode." lightbox="media/concept-endpoints/visual-studio-code-full.png" :::

## <a name="managed-online-endpoints-vs-kubernetes-online-endpoints-preview"></a>Points de terminaison en ligne managés et points de terminaison en ligne Kubernetes (préversion)

Il existe deux types de points de terminaison en ligne : les **points de terminaison en ligne managés** (préversion) et les **points de terminaison en ligne Kubernetes** (préversion). Le tableau suivant met en évidence certaines différences importantes.

|  | Points de terminaison en ligne managés | Points de terminaison en ligne Kubernetes |
|-|-|-|
| **Utilisateurs concernés** | Utilisateurs qui souhaitent un déploiement de modèle managé et une expérience MLOps améliorée | Utilisateurs qui préfèrent Kubernetes et peuvent autogérer les exigences d’infrastructure |
| **Gestion de l’infrastructure** | Provisionnement de calcul, mise à l’échelle, mises à jour de l’image du système d’exploitation hôte et durcissement de la sécurité managés | Responsabilité de l’utilisateur |
| **Type de capacité de calcul** | Managé (AmlCompute) | Cluster Kubernetes (Kubernetes) |
| **Supervision standard** | [Surveillance Azure](how-to-monitor-online-endpoints.md) <br> (comprend des métriques clés telles que la latence et le débit) | Non pris en charge |
| **Journalisation standard** | [Journaux Azure et Log Analytics au niveau du point de terminaison](how-to-deploy-managed-online-endpoints.md#optional-integrate-with-log-analytics) | Prise en charge |
| **Application Insights** | Pris en charge | Pris en charge |
| **Identité gérée** | [Pris en charge](how-to-access-resources-from-endpoints-managed-identities.md) | Prise en charge |
| **Réseau virtuel (VNet)** | Pas encore pris en charge (nous travaillons dessus) | Prise en charge |
| **Visualisation des coûts** | [Au niveau du point de terminaison et du déploiement](how-to-view-online-endpoints-costs.md) | Au niveau du cluster |

### <a name="managed-online-endpoints"></a>Points de terminaison en ligne managés

Les points de terminaison en ligne managés peuvent contribuer à simplifier votre processus de déploiement. Les points de terminaison en ligne managés offrent les avantages suivants par rapport aux points de terminaison en ligne Kubernetes :

- Infrastructure managée
    - Provisionne automatiquement le calcul et héberge le modèle (vous devez simplement spécifier les paramètres de type de machine virtuelle et de mise à l’échelle) 
    - Applique automatiquement les mises à jour et patchs à l’image du système d’exploitation hôte sous-jacent
    - Récupération automatique de nœud en cas de défaillance du système

- Supervision et journaux
    - Supervisez la disponibilité, les performances et le SLA du modèle à l’aide de l’[intégration native à Azure Monitor](how-to-monitor-online-endpoints.md).
    - Déboguez les déploiements à l’aide des journaux et de l’intégration native à Azure Log Analytics.

    :::image type="content" source="media/concept-endpoints/log-analytics-and-azure-monitor.png" alt-text="Capture d’écran montrant un graphe Azure Monitor de la latence du point de terminaison":::

- Identité managée
    -  Utiliser des [identités managées pour accéder à des ressources sécurisées à partir d’un script de scoring](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)

- Voir les coûts 
    - Les points de terminaison en ligne managés vous permettent de [superviser le coût au niveau du point de terminaison et du déploiement](how-to-view-online-endpoints-costs.md)
    
    :::image type="content" source="media/concept-endpoints/endpoint-deployment-costs.png" alt-text="Capture d’écran montrant le graphique de coûts d’un point de terminaison et d’un déploiement":::

Pour obtenir un tutoriel pas à pas, consultez [Guide pratique pour déployer des points de terminaison en ligne managés](how-to-deploy-managed-online-endpoints.md).

## <a name="what-are-batch-endpoints-preview"></a>Que sont les points de terminaison de traitement de lots (préversion) ?

Les **points de terminaison de traitement de lots** (préversion) sont des points de terminaison utilisés pour effectuer l’inférence par lot sur de grands volumes de données, sur une période donnée.  Les **points de terminaison de traitement de lots** reçoivent des pointeurs vers les données et exécutent les tâches de façon asynchrone pour traiter les données en parallèle sur les clusters de calcul. Les points de terminaison de traitement de lots stockent les sorties dans un magasin de données en vue d’une analyse plus approfondie.

:::image type="content" source="media/concept-endpoints/batch-endpoint.png" alt-text="Diagramme montrant qu’un seul point de terminaison de lot peut acheminer les requêtes vers plusieurs déploiements, dont l’un est le choix par défaut.":::

### <a name="batch-deployment-requirements"></a>Exigences en matière de déploiement par lots

Pour créer un déploiement par lots, vous devez spécifier les éléments suivants :

- Fichiers de modèle (ou spécifiez un modèle inscrit dans votre espace de travail)
- Calcul
- Script de scoring : code nécessaire pour effectuer le scoring/l’inférence
- Environnement : une image Docker avec des dépendances Conda

Si vous déployez des [modèles MLFlow](how-to-use-mlflow.md), il n’est pas nécessaire de fournir un script de scoring et un environnement d’exécution, car les deux sont générés automatiquement.

Découvrez comment [Déployer et utiliser des points de terminaison de traitement de lots avec Azure CLI](how-to-use-batch-endpoint.md) et le [portail web de Studio](how-to-use-batch-endpoints-studio.md)

### <a name="managed-cost-with-autoscaling-compute"></a>Coût maîtrisé avec le scaling automatique des ressources de calcul

L’appel d’un point de terminaison de traitement de lots déclenche un travail d’inférence par lot asynchrone. Les ressources de calcul sont automatiquement provisionnées au démarrage du travail et désallouées à la fin du travail. Ainsi, vous payez pour le calcul uniquement quand vous l’utilisez.

Vous pouvez [remplacer les paramètres de ressource de calcul](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings) (comme le nombre d’instances) et les paramètres avancés (tels que la taille des mini-lots, le seuil d’erreur, etc.) pour chaque travail d’inférence par lot afin d’accélérer l’exécution et de réduire les coûts.

### <a name="flexible-data-sources-and-storage"></a>Sources de données et stockage flexibles

Vous pouvez utiliser les options suivantes pour les données d’entrée quand vous appelez un point de terminaison de traitement de lots :

- Jeux de données Azure Machine Learning inscrits : pour plus d’informations, consultez [Créer des jeux de données Azure Machine Learning](how-to-train-with-datasets.md)
- Données cloud : URI de données public ou chemin de données dans un magasin de données. Pour plus d’informations, consultez [Se connecter aux données avec le studio Azure Machine Learning](how-to-connect-data-ui.md)
- Données stockées localement

Définissez l’emplacement de sortie de stockage sur n’importe quel magasin de données et chemin. Par défaut, les points de terminaison de traitement de lots stockent leur sortie dans le magasin d’objets blob par défaut de l’espace de travail. Le stockage est organisé selon le nom du travail (GUID généré par le système).

### <a name="security"></a>Sécurité

- Authentification : jetons Azure Active Directory
- SSL par défaut pour l’appel de point de terminaison

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour déployer des points de terminaison en ligne managés avec Azure CLI](how-to-deploy-managed-online-endpoints.md)
- [Guide pratique pour déployer des points de terminaison de traitement de lots avec Azure CLI](how-to-use-batch-endpoint.md)
- [Guide pratique pour utiliser des points de terminaison en ligne managés avec le studio](how-to-use-managed-online-endpoint-studio.md)
- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Guide pratique pour superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md)
- [Guide pratique pour voir les coûts des points de terminaison en ligne](how-to-view-online-endpoints-costs.md)
- [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)
