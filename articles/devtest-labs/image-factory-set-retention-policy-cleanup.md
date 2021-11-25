---
title: Définir la stratégie de rétention
description: Découvrez comment configurer une stratégie de rétention, nettoyer la fabrique et mettre hors service des images anciennes à partir de DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4cb4a343aacda48791293507a95c4c9dfa0f5f47
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397658"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Configurer une stratégie de rétention dans Azure DevTest Labs
Cet article couvre la définition d’une stratégie de conservation, le nettoyage de la fabrique et la suppression des anciennes images dans tous les autres DevTest Labs de l’organisation. 

## <a name="prerequisites"></a>Prérequis
Veillez à suivre les articles avant de continuer :

- [Créer une fabrique d’images](image-factory-create.md)
- [Exécuter une fabrique d’images depuis Azure DevOps](image-factory-set-up-devops-lab.md)
- [Enregistrer les images personnalisées et les distribuer à plusieurs laboratoires](image-factory-save-distribute-custom-images.md)

Les éléments suivants doivent déjà être en place :

- Un labo pour la fabrique d’images dans Azure DevTest Labs
- Un ou plusieurs Azure DevTest Labs cibles où la fabrique va distribuer des images golden
- Un projet Azure DevOps utilisé pour automatiser la fabrique d’images.
- Emplacement du code source contenant les scripts et la configuration (dans notre exemple, dans le même projet DevOps que celui utilisé ci-dessus)
- Une définition de build pour orchestrer les tâches Azure PowerShell
 
## <a name="setting-the-retention-policy"></a>Définition de la stratégie de conservation
Avant de configurer les étapes de nettoyage, définissez le nombre d’images de l’historique à conserver dans les DevTest Labs. Quand vous avez suivi l’article [Exécuter une fabrique d’images à partir d’Azure DevOps](image-factory-set-up-devops-lab.md), vous avez configuré différentes variables de génération. L’une d’entre elles était **ImageRetention**. Vous définissez cette variable sur `1`, ce qui signifie que les DevTest Labs ne conservent pas d’historique des images personnalisées. Seules les dernières images distribuées seront disponibles. Si vous changez cette variable sur `2`, la dernière image distribuée plus les précédentes sont conservées. Vous pouvez spécifier cette valeur pour définir le nombre d’images de l’historique que vous voulez conserver dans vos DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Nettoyage de la fabrique
La première étape du nettoyage de la fabrique consiste à supprimer les machines virtuelles de l’image golden dans la fabrique d’images. Il existe un script pour effectuer cette tâche tout comme nos scripts précédents. La première étape consiste à ajouter une autre tâche **Azure PowerShell** à la définition de build, comme illustré dans l’image suivante :

![Capture d’écran montrant une étape PowerShell.](./media/set-retention-policy-cleanup/powershell-step.png)

Une fois que vous avez la nouvelle tâche dans la liste, sélectionnez l’élément, puis renseignez tous les détails comme indiqué dans l’image suivante :

![Capture d’écran montrant la tâche PowerShell de nettoyage des images anciennes.](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Les paramètres du script sont : `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Supprimer les anciennes images 
Cette tâche supprime les anciennes images, en conservant seulement un historique correspondant à la variable de génération **ImageRetention**. Ajoutez une autre tâche de build **Azure Powershell** à notre définition de build. Une fois qu’elle est ajoutée, sélectionnez la tâche, puis renseignez les détails comme indiqué dans l’image suivante : 

![Capture d’écran qui montre la tâche PowerShell de mise hors service des images anciennes.](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Les paramètres du script sont : `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Mettre la build en file d’attente
Maintenant que vous avez terminé la définition de build, placez en file d’attente un nouveau build pour vérifier que tout fonctionne. Une fois le build terminé, les nouvelles images personnalisées s’affichent dans le labo de destination. Si vous examinez le labo de fabrique d’images, vous constatez qu’il ne contient aucune machine virtuelle approvisionnée. Si vous mettez en file d’attente des builds supplémentaires, vous voyez que les tâches de nettoyage mettent hors service les anciennes images personnalisées dans DevTest Labs. L’accord de mise hors service avec la valeur de rétention définie dans les variables de build.

> [!NOTE]
> Si vous avez exécuté le pipeline de build à la fin du dernier article de la série, supprimez manuellement les machines virtuelles qui ont été créées dans le labo de fabrique d’images avant de placer le nouveau build en file d’attente.  L’étape de nettoyage manuel est nécessaire seulement pendant que vous configurez le tout et que vous vérifiez que cela fonctionne.



## <a name="summary"></a>Résumé
Vous disposez maintenant d’une fabrique d’images en cours d’exécution, qui peut générer et distribuer à la demande des images personnalisées à vos labos. À ce stade, il s’agit simplement d’obtenir vos images correctement configurées et d’identifier les labos cibles. Comme mentionné dans l’article précédent, le fichier **Labs.json** qui se trouve dans votre dossier **Configuration** spécifie quelles images doivent être rendues accessibles dans chacun des labos cibles. Quand vous ajoutez d’autres DevTest Labs à votre organisation, vous devez simplement ajouter une entrée dans le fichier Labs.json pour le nouveau labo.

L’ajout d’une nouvelle image à votre fabrique est également simple. Lorsque vous souhaitez inclure une nouvelle image dans votre fabrique, ouvrez le [Portail Azure](https://portal.azure.com) et accédez à votre labo de fabrique. Sélectionnez le bouton pour ajouter une machine virtuelle, puis choisissez l’image du marketplace et les artefacts de votre choix. Au lieu de sélectionner le bouton **Créer** pour générer la nouvelle machine virtuelle, sélectionnez **Afficher le modèle Azure Resource Manager**. Enregistrez le modèle en tant que fichier .json dans le dossier **GoldenImages** de votre référentiel. La prochaine fois que vous exécutez votre fabrique d’images, elle va créer votre image personnalisée.


## <a name="next-steps"></a>Étapes suivantes
1. [Planifier votre génération/mise en production](/azure/devops/pipelines/build/triggers?tabs=designer) pour exécuter périodiquement la fabrique d’images. Elle actualise vos images générées par la fabrique régulièrement.
2. Créez d’autres images golden pour votre fabrique. Vous pouvez également envisager de [créer des artefacts](devtest-lab-artifact-author.md) pour générer un script avec des éléments supplémentaires de vos tâches de configuration de machine virtuelle et inclure les artefacts dans vos images de fabrique.
4. Créez une [génération/mise en production distincte](/azure/devops/pipelines/overview) pour exécuter le script **DistributeImages** séparément. Vous pouvez exécuter ce script quand vous apportez des modifications à Labs.json et obtenir ainsi des images copiées vers les labos cibles sans devoir recréer toutes les images.
