---
title: Mettre à niveau le contrôleur de données Azure Arc en mode indirect à l’aide de l’interface CLI
description: Mettre à niveau le contrôleur de données Azure Arc en mode indirect à l’aide de l’interface CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: dd5122f123dae93ea07cab65983183b1ffd8b463
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495379"
---
# <a name="upgrade-indirect-mode-azure-arc-data-controller-using-the-cli"></a>Mettre à niveau le contrôleur de données Azure Arc en mode indirect à l’aide de l’interface CLI

Cet article explique comment mettre à niveau un contrôleur de données Azure Arc connecté indirectement à l’aide de l’interface de ligne de commande Azure (`az`).

> [!IMPORTANT]
> Cet article ne s’applique pas à un contrôleur de données Azure Arc directement connecté. Pour obtenir les informations les plus récentes sur la mise à niveau d’un contrôleur de données connecté directement, consultez les [notes de publication](/azure/azure-arc/data/release-notes#data-controller-upgrade).

## <a name="prerequisites"></a>Prérequis

Vous aurez besoin d’un contrôleur de données en mode indirect avec imageTag v1.0.0_2021-07-30 ou une version ultérieure.

Pour vérifier la version, exécutez :

```console
kubectl get datacontrollers -n -o custom-columns=BUILD:.spec.docker.imageTag
```

## <a name="install-tools"></a>Installer des outils

Avant de pouvoir effectuer les tâches de cet article, vous devez disposer des éléments suivants :

- L’interface [Azure CLI (az)](/cli/azure/install-azure-cli)
- L’[`arcdata`extension pour Azure CLI](install-arcdata-extension.md)

[!INCLUDE [azure-arc-angle-bracket-example](../../../includes/azure-arc-angle-bracket-example.md)]

## <a name="view-available-images-and-chose-a-version"></a>Afficher les images disponibles et choisir une version

Extrayez la liste des images disponibles pour le contrôleur de données à l’aide de la commande suivante :

   ```azurecli
   az arcdata dc list-upgrades --k8s-namespace <namespace> –-use-k8s
   ```

La commande ci-dessus renvoie une sortie similaire à l’exemple suivant :

```output
Found 2 valid versions.  The current datacontroller version is v1.0.0_2021-07-30.
v1.1.0_2021-11-02
v1.0.0_2021-07-30
```

## <a name="upgrade-data-controller"></a>Mettre à niveau le contrôleur de données

Cette section montre comment mettre à niveau un contrôleur de données en mode indirect.

> [!NOTE]
> Certains des niveaux et modes des services de données sont généralement disponibles (GA), tandis que d’autres sont en préversion.
> Si vous installez les services disponibles en GA et en préversion sur le même contrôleur de données, vous ne pouvez pas opérer de mise à niveau sur place.
> Pour mettre à niveau, supprimez toutes les instances de base de données non GA. Vous trouverez la liste des services disponibles en GA et en préversion dans les [notes de publication](/azure/azure-arc/data/release-notes).

### <a name="indirect-mode"></a>Mode indirect

Vous devez vous connecter et vous authentifier auprès d’un cluster Kubernetes et avoir sélectionné un contexte Kubernetes existant avant de commencer la mise à niveau du contrôleur de données Azure Arc.

Vous pouvez effectuer d’abord un test. Le test valide l’existence du registre, le schéma de version et le jeton d’autorisation du référentiel privé (le cas échéant). Pour effectuer un test, utilisez le paramètre `--dry-run` dans la commande `az arcdata dc upgrade`. Par exemple :

```azurecli
az arcdata dc upgrade --desired-version <version> --k8s-namespace <namespace> --dry-run --use-k8s
```

La sortie de la commande précédente est la suivante :

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
****Dry Run****
Arcdata Control Plane would be upgraded to: 20211024.1
```

Pour mettre à niveau le contrôleur de données, exécutez la commande `az arcdata dc upgrade`. Si vous ne spécifiez pas d’image cible, le contrôleur de données est mis à niveau vers la version la plus récente. L’exemple suivant utilise une variable locale (`$version`) pour utiliser la version que vous avez sélectionnée précédemment ([Afficher les images disponibles et choisir une version](#view-available-images-and-chose-a-version)).

```azurecli
az arcdata dc upgrade --desired-version $version --k8s-namespace <namespace> --use-k8s
```

La sortie de la commande précédente indique l’état des étapes :

```output
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Preparing to upgrade dc arcdc in namespace arc to version 20211024.1.
Creating service account: arc:cr-upgrade-worker
Creating cluster role: arc:cr-upgrade-worker
Creating cluster role binding: arc:crb-upgrade-worker
Cluster role binding: arc:crb-upgrade-worker created successfully.
Cluster role: arc:cr-upgrade-worker created successfully.
Service account arc:cr-upgrade-worker has been created successfully.
Creating privileged job arc-elevated-bootstrapper-job
```

## <a name="monitor-the-upgrade-status"></a>Surveiller l’état de la mise à niveau

Vous pouvez surveiller la progression de la mise à niveau à l’aide de kubectl ou de l’interface CLI.

### <a name="kubectl"></a>kubectl

```console
kubectl get datacontrollers --namespace <namespace>
kubectl get monitors --namespace <namespace>
```

La mise à niveau est un processus en deux parties. D’abord, le contrôleur est mis à niveau, puis la pile d’analyse est mise à niveau. Pendant la mise à niveau, utilisez ```kubectl get monitors -n <namespace> -w``` pour afficher l’état. La sortie se présente comme suit :

```output
NAME           STATUS     AGE
monitorstack   Updating   36m
monitorstack   Updating   36m
monitorstack   Updating   39m
monitorstack   Updating   39m
monitorstack   Updating   41m
monitorstack   Ready      41m
```

### <a name="cli"></a>Interface de ligne de commande

```azurecli
 az arcdata dc status show --k8s-namespace <namespace> --use-k8s
```

La mise à niveau est un processus en deux parties. D’abord, le contrôleur est mis à niveau, puis la pile d’analyse est mise à niveau. Une fois la mise à niveau terminée, la sortie est la suivante :

```output
Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Résoudre les problèmes de mise à niveau

Si vous rencontrez des problèmes avec la mise à niveau, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).
