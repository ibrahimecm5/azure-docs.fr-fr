---
title: Exporter un modèle Azure Resource Manager de tâche Azure Stream Analytics
description: Cet article explique comment exporter un modèle Azure Resource Manager pour votre tâche Azure Stream Analytics.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: c67d6895fe4dde54992c7a79067a45dcdff18305
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586416"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Exporter un modèle Azure Resource Manager de tâche Azure Stream Analytics

Les [modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md) vous permettent d’implémenter une infrastructure en tant que code. Le modèle est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de vos ressources. Vous spécifiez les ressources à déployer ainsi que leurs propriétés.

Vous pouvez redéployer un tâche Azure Stream Analytics en exportant le modèle Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Ouvrir une tâche dans VS Code

Pour pouvoir exporter un modèle, vous devez d’abord ouvrir une tâche Stream Analytics existante dans Visual Studio Code. 

Pour exporter une tâche vers un projet local, recherchez la tâche que à exporter dans l’**Explorateur Stream Analytics** sur le portail Azure. Dans la page **Requête**, sélectionnez **Ouvrir dans Visual Studio**. Ensuite, sélectionnez **Visual Studio Code**.

![Ouvrir une tâche Stream Analytics dans Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Pour plus d’informations sur l’utilisation de Visual Studio Code pour gérer les tâches Stream Analytics, voir [Démarrage rapide de Visual Studio Code](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Compiler le script 

L’étape suivante consiste à compiler le script de la tâche dans un modèle Azure Resource Manager. Avant de compiler le script, assurez-vous que votre tâche a au moins une entrée et une sortie configurées. Si aucune entrée ou sortie n’est configurée, vous devez commencer par les configurer.

1. Dans Visual Studio Code, accédez au fichier de *Transformation.asaql* de votre tâche.

   ![Fichier Transformation.asaql dans Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Cliquez avec le bouton droit sur le fichier *transformation.asaql*, puis sélectionnez **ASA : compiler le script** dans le menu.

1. Notez qu’un dossier **Deploy** s’affiche dans votre espace de travail Stream Analytics.

1. Explorez le fichier *JobTemplate.json* qui est le modèle Azure Resource Manager utilisé pour le déploiement.

## <a name="complete-the-parameters-file"></a>Compléter le fichier de paramètres

Ensuite, complétez le fichier de paramètres du modèle Azure Resource Manager.

1. Ouvrez le fichier *JobTemplate.parameters.json* situé dans le dossier **Deploy** de votre espace de travail Stream Analytics dans Visual Studio Code.

1. Notez que les clés d’entrée et de sortie ont la valeur null. Remplacez les valeurs null par les clés d’accès réelles de vos ressources d’entrée et de sortie.

1. Enregistrez le fichier de paramètres.

## <a name="deploy-using-templates"></a>Déployer à l’aide de modèles

Vous êtes prêt à déployer votre tâche Azure Stream Analytics à l’aide des modèles Azure Resource Manager que vous avez générés dans la section précédente.

Dans une fenêtre PowerShell, exécutez la commande suivante. Veillez à remplacer *ResourceGroupName*, *TemplateFile* et *TemplateParameterFile* par le nom de votre groupe de ressources réel, et à renseigner les chemins d’accès complets des fichiers *JobTemplate.json* et *JobTemplate.parameters.json* dans le dossier **Deploy** de l’espace de travail de votre tâche.

Si Azure PowerShell n’est pas configuré, procédez de la manière décrite dans [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Étapes suivantes

* [Tester des travaux Azure Stream Analytics localement avec une entrée en direct à l’aide de Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Explorer des travaux Azure Stream Analytics avec Visual Studio Code (préversion)](visual-studio-code-explore-jobs.md)
