---
title: Mettre à l’échelle automatiquement des points de terminaison en ligne gérés
titleSuffix: Azure Machine Learning
description: Découvrez comment effectuer un scale-up des points de terminaison gérés. Obtenez plus d’espace de disque, de mémoire et de processeur ainsi que des fonctionnalités supplémentaires.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: f979651909a2484f6bcdf7b0953c91874bea7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520652"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>Mise à l’échelle automatique d’un point de terminaison en ligne géré (préversion)

La mise à l’échelle automatique exécute automatiquement la quantité appropriée de ressources pour gérer la charge sur votre application. Les [points de terminaison gérés](concept-endpoints.md) prennent en charge la mise à l’échelle automatique via l’intégration à la fonctionnalité de mise à l’échelle automatique d’Azure Monitor.

La mise à l’échelle automatique d’Azure Monitor prend en charge un ensemble complet de règles. Vous pouvez configurer la mise à l’échelle basée sur les métriques (par exemple utilisation du processeur > 70 %), la mise à l’échelle basée sur la planification (par exemple les règles de mise à l’échelle pour les heures de pointe) ou une combinaison des deux. Pour plus d’informations, consultez [Vue d’ensemble de la mise à l’échelle automatique dans Microsoft Azure](/azure-monitor/autoscale/autoscale-overview.md).

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="Diagramme de l’ajout/suppression d’une instance pour la mise à l’échelle automatique en fonction des besoins":::

Aujourd’hui, vous pouvez gérer la mise à l’échelle automatique à l’aide de l’interface Azure CLI, avec REST, ARM ou le portail Azure basé sur un navigateur. D’autres kits SDK Azure ML, comme le kit SDK Python, seront pris en charge ultérieurement.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis 

* Point de terminaison déployé. [Déployez et effectuez le scoring d’un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne géré (préversion)](how-to-deploy-managed-online-endpoints.md). 

## <a name="define-an-autoscale-profile"></a>Définir un profil de mise à l’échelle automatique

Pour activer la mise à l’échelle automatique pour un point de terminaison, vous devez d’abord définir un profil de mise à l’échelle automatique. Ce profil définit les capacités par défaut, minimale et maximale du groupe identique. L’exemple suivant définit la capacité par défaut et la capacité minimale sous la forme de deux instances de machine virtuelle, ainsi que la capacité maximale sous la forme de cinq instances :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’extrait de code suivant définit les noms des points de terminaison et des déploiements :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

Ensuite, récupérez l’ID d’Azure Resource Manager du déploiement et du point de terminaison :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

L’extrait de code suivant crée le profil de mise à l’échelle automatique :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> Pour plus d’informations, consulter la [page de référence pour la mise à l’échelle automatique](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans [Azure Machine Learning studio](https://ml.azure.com), sélectionnez votre espace de travail, puis sélectionnez __Points de terminaison__ sur le côté gauche de la page. Une fois les points de terminaison répertoriés, sélectionnez celui que vous souhaitez configurer.

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="Capture d’écran d’une entrée de déploiement de point de terminaison dans le portail.":::

Dans l’onglet __Détails__ du point de terminaison, sélectionnez __Configurer la mise à l’échelle automatique__.

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="Capture d’écran du lien pour configurer la mise à l’échelle automatique dans les détails du point de terminaison.":::

Sous __Choisir comment mettre à l’échelle vos ressources__, sélectionnez __Mise à l’échelle automatique personnalisée__ pour commencer la configuration. Pour la condition de mise à l’échelle par défaut, utilisez les valeurs suivantes :

* Définissez le __Mode de mise à l’échelle__ sur __Mettre à l’échelle selon une métrique__.
* Définissez __Minimum__ sur __2__.
* Définissez __Maximum__ sur __5__.
* Définissez __Par défaut__ sur __2__.

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="Capture d’écran montrant le choix de mise à l’échelle automatique personnalisée.":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>Créer une règle pour le scale-out à l’aide de métriques

Une règle de scale-out courante consiste à augmenter le nombre d’instances de machine virtuelle quand la charge moyenne du processeur est élevée. L’exemple suivant alloue deux nœuds supplémentaires (jusqu’au maximum) si la charge moyenne du processeur est supérieure à 70 % pendant cinq minutes :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

La règle fait partie du profil `my-scale-settings` (`autoscale-name` correspond à `name` dans le profil). La valeur de son argument `condition` indique que la règle doit se déclencher quand « la consommation moyenne du processeur entre les instances de machine virtuelle dépasse 70 % pendant cinq minutes. » Quand cette condition est remplie, deux instances de machine virtuelle supplémentaires sont allouées. 

> [!NOTE]
> Pour plus d’informations sur la syntaxe de l’interface CLI, consultez [`az monitor autoscale`](/cli/azure/monitor/autoscale).

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans la section __Règles__, sélectionnez __+Ajouter une règle__. La page __Règle de mise à l’échelle__ s’affiche. Utilisez les informations suivantes pour remplir les champs de cette page :

* Définissez le __Nom de la métrique__ sur __Pourcentage d’utilisation du processeur__.
* Définissez __Opérateur__ sur __Supérieur à__ et définissez le __Seuil de métrique__ sur __70__.
* Définissez la __Durée (en minutes)__ sur 5. Laissez la __Statistique de grain de temps__ définie sur __Moyen__.
* Définissez __Opération__ sur __Augmenter le nombre de__ et définissez le __Nombre d’instances__ sur __2__.

Enfin, sélectionnez le bouton __Ajouter__ pour créer la règle.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="Capture d’écran montrant la règle de scale-out >70 % du processeur pendant 5 minutes.":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>Créer une règle pour le scale-in à l’aide de métriques

Quand la charge est faible, une règle de scale-in permet de réduire le nombre d’instances de machine virtuelle. L’exemple suivant permet de libérer un seul nœud, jusqu’à un minimum de 2, si la charge du processeur est inférieure à 30 % pendant 5 minutes :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans la section __Règles__, sélectionnez __+Ajouter une règle__. La page __Règle de mise à l’échelle__ s’affiche. Utilisez les informations suivantes pour remplir les champs de cette page :

* Définissez le __Nom de la métrique__ sur __Pourcentage d’utilisation du processeur__.
* Définissez __Opérateur__ sur __Inférieur à__ et définissez le __Seuil de métrique__ sur __30__.
* Définissez la __Durée (en minutes)__ sur __5__.
* Définissez __Opération__ sur __Diminuez le nombre de__ et définissez le __Nombre d’instances__ sur __1__.

Enfin, sélectionnez le bouton __Ajouter__ pour créer la règle.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="Capture d’écran montrant une règle de scale-in":::

Si vous disposez à la fois de règles de scale-out et de scale-in, vos règles ressemblent à la capture d’écran suivante. Vous avez spécifié que si la charge moyenne du processeur dépasse 70 % pendant 5 minutes, 2 nœuds supplémentaires doivent être alloués, jusqu’à la limite de 5. Si la charge du processeur est inférieure à 30% pendant 5 minutes, un seul nœud doit être relâché, jusqu’à la valeur minimale de 2. 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="Capture d’écran montrant les paramètres de mise à l’échelle automatique, notamment les règles.":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>Créer une règle de mise à l’échelle basée sur les métriques de point de terminaison

Règles précédentes appliquées au déploiement. Ajoutez maintenant une règle qui s’applique au point de terminaison. Dans cet exemple, si la latence des requêtes est supérieure à une moyenne de 70 millisecondes pendant 5 minutes, allouez un autre nœud.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[Portail](#tab/azure-portal)

En bas de la page, sélectionnez __+ Ajouter une condition de mise à l’échelle__.

Sélectionnez __Mise à l’échelle en fonction des métriques__, puis sélectionnez __Ajouter une règle__. La page __Règle de mise à l’échelle__ s’affiche. Utilisez les informations suivantes pour remplir les champs de cette page :

* Définissez __Source des métriques__ sur __Autre ressource__.
* Définissez __Type de ressource__ sur __Points de terminaison en ligne Machine Learning__.
* Définissez __Ressource__ sur votre point de terminaison.
* Définissez __Nom de métrique__ sur __Latence des requêtes__.
* Définissez __Opérateur__ sur __Supérieur à__ et définissez le __Seuil de métrique__ sur __70__.
* Définissez la __Durée (en minutes)__ sur __5__.
* Définissez __Opération__ sur __Augmenter le nombre de__ et définissez le __Nombre d’instances__ sur 1.

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="Capture d’écran montrant les règles de métriques de point de terminaison.":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>Créer des règles de mise à l’échelle selon une planification

Vous pouvez également créer des règles qui s’appliquent uniquement à certains jours ou à certaines heures. Dans cet exemple, le nombre de nœuds est défini sur 2 pour le week-end.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[Portail](#tab/azure-portal)

En bas de la page, sélectionnez __+ Ajouter une condition de mise à l’échelle__. Dans la nouvelle condition de mise à l’échelle, utilisez les informations suivantes pour remplir les champs :
 
* Sélectionnez __Mettre à l’échelle à un nombre d’instances spécifique__.
* Définissez le __Nombre d’instances__ sur __2__.
* Définissez la __planification__ sur __Répéter des jours spécifiques__.
* Définissez la planification sur __Répéter chaque__ __samedi__ et __dimanche__.

:::image type="content" source="media/how-to-autoscale-endpoints/schedule-rules.png" lightbox="media/how-to-autoscale-endpoints/schedule-rules.png" alt-text="Capture d’écran montrant les règles basées sur la planification.":::

---

## <a name="delete-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas d’utiliser vos déploiements, supprimez-les :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise à l’échelle automatique avec Azure Monitor, consultez les articles suivants :

- [Comprendre les paramètres de mise à l’échelle automatique](/azure-monitor/autoscale/autoscale-understand-settings)
- [Vue d’ensemble des modèles courants de mise à l’échelle automatique](/azure-monitor/autoscale/autoscale-common-scale-patterns)
- [Meilleures pratiques en matière de mise à l'échelle automatique](/azure-monitor/autoscale/autoscale-best-practices)
- [Résolution des problèmes de mise à l’échelle automatique Azure](/azure-monitor/autoscale/autoscale-troubleshoot)
