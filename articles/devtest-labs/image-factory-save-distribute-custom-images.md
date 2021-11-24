---
title: Enregistrer et distribuer des images
description: Cet article décrit les étapes à suivre pour enregistrer des images personnalisées à partir de machines virtuelles existantes dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0c2ecd3f64fbc6292563446e701e306815714d1e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397279"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Enregistrer les images personnalisées et les distribuer à plusieurs laboratoires
Cet article décrit les étapes à suivre pour enregistrer des images personnalisées à partir de machines virtuelles existantes. Il explique également comment distribuer ces images personnalisées dans d’autres labs DevTest Labs de l’organisation.

## <a name="prerequisites"></a>Prérequis
Les éléments suivants doivent déjà être en place :

- Un lab pour la fabrique d’images dans Azure DevTest Labs.
- Un projet Azure DevOps utilisé pour automatiser la fabrique d’images.
- Un emplacement pour le code source, contenant les scripts et la configuration (dans notre exemple, il se trouve dans le même projet DevOps que celui indiqué à l’étape précédente).
- Une définition de build pour orchestrer les tâches Azure PowerShell.

Si vous devez créer ou configurer un ou plusieurs de ces éléments, effectuez les étapes décrites dans [Exécuter une fabrique d’images à partir d’Azure DevOps](image-factory-set-up-devops-lab.md). 

## <a name="save-vms-as-generalized-vhds"></a>Enregistrer des machines virtuelles en tant que disques durs virtuels généralisés
Enregistrez les machines virtuelles existantes en tant que disques durs virtuels généralisés.  Vous pouvez utiliser l’exemple de script PowerShell fourni pour enregistrer les machines virtuelles existantes en tant que disques durs virtuels généralisés. Pour l’utiliser, ajoutez d’abord une autre tâche **Azure PowerShell** à la définition de build, comme illustré ci-dessous :

![Ajouter une étape Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Quand vous voyez la nouvelle tâche dans la liste, sélectionnez-la, puis renseignez tous les détails comme indiqué dans l’image suivante : 

![Paramètres PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Images personnalisées généralisées et spécialisées
Dans le [portail Azure](https://portal.azure.com), quand vous créez une image personnalisée à partir d’une machine virtuelle, vous avez le choix de créer une image personnalisée généralisée ou spécialisée.

- **Image personnalisée spécialisée :** Sysprep/Deprovision n’a PAS été exécuté sur la machine. L’image est donc une copie exacte du disque du système d’exploitation sur la machine virtuelle existante (un instantané).  Le nouvel ordinateur a les mêmes fichiers, applications, comptes d’utilisateur et nom d’ordinateur que cette image personnalisée.
- **Image personnalisée généralisée :** Sysprep/Deprovision a été exécuté sur la machine.  Ce processus supprime les comptes d’utilisateur, le nom de l’ordinateur et les ruches du registre de l’utilisateur. L’objectif est de généraliser l’image afin de pouvoir la personnaliser lors de la création d’une autre machine virtuelle.  Lorsque vous généralisez une machine virtuelle en exécutant Sysprep, le processus détruit la machine virtuelle actuelle. La machine virtuelle actuelle n’est plus fonctionnelle.

Le script d’alignement des images personnalisées dans la fabrique d’images enregistre les disques durs virtuels pour toutes les machines virtuelles créées à l’étape précédente. Le script identifie les disques durs virtuels en fonction d’une étiquette sur la ressource dans Azure.

## <a name="update-configuration-for-distributing-images"></a>Mettre à jour la configuration pour distribuer les images
L’étape suivante du processus consiste à envoyer (push) les images personnalisées du lab de la fabrique d’images aux autres labs qui en ont besoin. L’élément principal de ce processus est le fichier de configuration **labs.json**. Ce fichier se trouve dans le dossier **Configuration** inclus dans la fabrique d’images.

Le fichier de configuration labs.json contient deux éléments clés :

- L’identifiant unique d’un lab de destination, composé de l’ID de l’abonnement et du nom du lab.
- L’ensemble des images à envoyer au lab sous forme de chemins relatifs à la racine de la configuration. Vous pouvez également spécifier un dossier entier (pour prendre toutes les images présentes dans ce dossier).

Voici un exemple de fichier labs.json contenant deux laboratoires. Dans ce cas, vous distribuez des images à deux laboratoires différents.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Créer une tâche de génération
En suivant les mêmes étapes que celles décrites plus haut dans cet article, ajoutez une tâche de build **Azure PowerShell** à votre définition de build. Renseignez les détails comme indiqué dans l’image suivante : 

![Capture d’écran d’une tâche de build pour distribuer des images.](./media/save-distribute-custom-images/second-build-task-powershell.png)

Les paramètres sont : `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Cette tâche prend les images personnalisées présentes dans la fabrique d’images et les envoie (push) aux labs définis dans le fichier Labs.json.

## <a name="queue-the-build"></a>Mettre la build en file d’attente
Une fois que la tâche de build de la distribution est terminée, mettez en file d’attente une nouvelle build pour vérifier que tout fonctionne correctement. Une fois que la build est terminée, les nouvelles images personnalisées apparaissent dans le laboratoire de destination que vous avez spécifié dans le fichier config Labs.json.

## <a name="next-steps"></a>Étapes suivantes
Dans l’article suivant de la série, vous mettez à jour la fabrique d’images avec une stratégie de rétention et un processus de nettoyage : [Définir la stratégie de conservation et exécuter des scripts de nettoyage](image-factory-set-retention-policy-cleanup.md).
