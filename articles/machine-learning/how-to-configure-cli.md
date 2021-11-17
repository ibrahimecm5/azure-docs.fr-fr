---
title: Installer et configurer l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Découvrez comment installer et configurer l’extension Azure CLI pour Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 778c58c2a1408ea7beb2a756d5c1700810f4bb63
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137589"
---
# <a name="install-and-set-up-the-cli-v2"></a>Installer et configurer l’interface CLI (v2)

L’extension `ml` (préversion) d’[Azure CLI](/cli/azure/) est l’interface améliorée d’Azure Machine Learning. Elle permet d’entraîner et de déployer des modèles à partir de la ligne de commande, avec des fonctionnalités qui accélèrent le scale-up et le scale-out pour la science des données tout en assurant le suivi du cycle de vie des modèles.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

- Pour utiliser l'interface de ligne de commande, vous devez disposer d'un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui.
- Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](/cli/azure/install-azure-cli).

## <a name="installation"></a>Installation

La nouvelle extension du machine learning **nécessite Azure CLI version `>=2.15.0`** . Vérifiez que cette condition est respectée :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

Si ce n’est pas le cas, [mettez à niveau votre interface de ligne de commande Azure](/cli/azure/update-azure-cli).

Vérifiez les extensions Azure CLI que vous avez installées :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

Vérifiez qu’aucune extension en conflit utilisant l’espace de noms `ml` n’est installée, notamment l’extension `azure-cli-ml` :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

À présent, installez l’extension `ml` :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

Exécutez la commande help pour vérifier votre installation et voir les sous-commandes disponibles :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

Vous pouvez mettre à niveau l’extension vers la dernière version :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>Installation sur Linux

Si vous utilisez Linux, le moyen le plus rapide d’installer la version requise de l’interface de ligne de commande et l’extension Machine Learning est le suivant :

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

Pour plus de détails, consultez [Installer Azure CLI pour Linux](/cli/azure/install-azure-cli-linux).

## <a name="set-up"></a>Configurer

Connexion :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

Si vous avez accès à plusieurs abonnements Azure, vous pouvez définir votre abonnement actif :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

Si vous le souhaitez, configurez des variables communes dans votre interpréteur de commandes pour utilisation dans les commandes suivantes :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="set_variables":::

> [!WARNING]
> Cela a pour effet d’utiliser la syntaxe Bash pour définir les variables : ajustez-les si nécessaire pour votre interpréteur de commandes. Vous pouvez également remplacer les valeurs dans les commandes ci-dessous en les incluant plutôt qu’en utilisant des variables.

S’il n’existe pas déjà, vous pouvez créer le groupe de ressources Azure :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="az_group_create":::

Et créer un espace de travail d’apprentissage automatique :

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="az_ml_workspace_create":::

Les sous-commandes de machine learning nécessitent les paramètres `--workspace/-w` et `--resource-group/-g`. Pour éviter d’avoir à les taper plusieurs fois, configurez des valeurs par défaut :

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> La plupart des exemples de code partent du principe que vous avez défini un espace de travail et un groupe de ressources par défaut. Vous pouvez les remplacer sur la ligne de commande.

Vous pouvez afficher vos valeurs par défaut actuelles à l’aide de `--list-defaults/-l` :

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="list_defaults":::

> [!TIP]
> La combinaison avec `--output/-o` permet d’obtenir des formats de sortie plus lisibles.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer l’apprentissage des modèles à l’aide de CLI (v2)](how-to-train-cli.md)
- [Configurer l’extension Visual Studio Code d’Azure Machine Learning](how-to-setup-vs-code.md)
- [Entraîner un modèle de classification d’images TensorFlow à l’aide de l’extension Visual Studio Code d’Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md)
