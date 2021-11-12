---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 2bd402b9905772b7a45be5ea433a214b6e5f76cf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553440"
---
Les informations de cet article sont basées sur des exemples de code contenus dans le référentiel [azureml-examples](https://github.com/azure/azureml-examples). Pour exécuter les commandes localement sans avoir à copier/coller le fichier YAML et d’autres fichiers, clonez le référentiel, puis remplacez les répertoires par le répertoire `cli` dans le référentiel :

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Si vous n’avez pas encore défini les paramètres par défaut pour l’interface CLI Azure, enregistrez vos paramètres par défaut. Pour éviter de transmettre plusieurs fois les valeurs de votre abonnement, de votre espace de travail et de votre groupe de ressources, utilisez les commandes suivantes. Remplacez les paramètres suivants par des valeurs pour votre configuration spécifique :

* Remplacez `<subscription>` par l’identifiant de votre abonnement Azure.
* Remplacez `<workspace>` par le nom de votre espace de travail Azure Machine Learning.
* Remplacez `<resource-group>` par le groupe de ressources Azure qui contient votre espace de travail.
* Remplacez `<location>` par la région Azure qui contient votre espace de travail.

> [!TIP]
> Vous pouvez afficher les valeurs par défaut actuelles à l’aide de la commande `az configure -l`.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

