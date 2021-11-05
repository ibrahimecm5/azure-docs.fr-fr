---
title: Types de Runbooks Azure Automation
description: Cet article décrit les différents types de runbooks que vous pouvez utiliser dans Azure Automation, et énonce des considérations pour déterminer le type à utiliser.
services: automation
ms.subservice: process-automation
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0fe647d586887bcb2d0a897f57c75a5f0b141b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423929"
---
# <a name="azure-automation-runbook-types"></a>Types de Runbooks Azure Automation

La fonctionnalité d’automatisation des processus Azure Automation prend en charge plusieurs types de runbooks, conformément à la définition du tableau suivant. Pour en savoir plus sur l’environnement d’automatisation des processus, consultez [Exécution d’un runbook dans Azure Automation](automation-runbook-execution.md).

| Type | Description |
|:--- |:--- |
| [Graphique](#graphical-runbooks)|Runbook graphique basé sur Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [Graphique workflow PowerShell](#graphical-runbooks)|Runbook graphique basé sur le workflow Windows PowerShell et créé et modifié entièrement dans l'éditeur graphique du portail Azure. |
| [PowerShell](#powershell-runbooks) |Runbook textuel basé sur les scripts Windows PowerShell. |
| [Workflow PowerShell](#powershell-workflow-runbooks)|Runbook textuel basé sur les scripts Windows PowerShell Workflow. |
| [Python](#python-runbooks) |Runbook textuel basé sur les scripts Python. |

Prenez en compte les considérations suivantes pour déterminer le type à utiliser pour un runbook donné.

* Vous ne pouvez pas convertir de runbooks du type graphique au type texte ou vice versa.
* Il existe des limitations lorsque des runbooks de différents types sont utilisés comme runbooks enfants. Pour plus d’informations, consultez la page [Runbooks enfants dans Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Runbooks graphiques

Vous pouvez créer et modifier des runbooks graphiques de workflow PowerShell avec l’éditeur graphique du portail Azure. Vous ne pouvez cependant ni créer ni modifier ce type de runbook avec un autre outil. Principales fonctionnalités des runbooks graphiques :

* Ils sont exportés vers des fichiers de votre compte Automation, puis importés dans un autre compte Automation.
* Générer du code PowerShell.
* Ils sont convertis vers ou depuis les runbooks PowerShell Workflow graphiques pendant l’importation.

### <a name="advantages"></a>Avantages

* Utilisation d’un modèle de création visuel insert-link-configure.
* Concentration sur la circulation des flux de données dans tout le processus.
* Représentez visuellement les processus de gestion.
* Inclusion d’autres runbooks en tant que runbooks enfants pour créer des workflows de niveau élevé.
* Programmation modulaire favorisée.

### <a name="limitations"></a>Limites

* Impossible de créer ou de modifier en dehors du portail Azure.
* Peut nécessiter une activité de code contenant du code PowerShell pour exécuter une logique complexe.
* Impossible d’effectuer une conversion vers l’un des [formats de texte](automation-runbook-types.md) ou de convertir un runbook de texte au format graphique. 
* Impossible d'afficher ou de modifier directement du code PowerShell créé par le workflow graphique. Vous pouvez afficher le code créé dans toute activité de code.
* Impossible d’exécuter des runbooks sur un Runbook Worker hybride. Consultez [Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride](automation-hybrid-runbook-worker.md).
* Les runbooks graphiques ne peuvent pas être signés numériquement.

## <a name="powershell-runbooks"></a>Runbooks PowerShell

Les Runbooks PowerShell sont basés sur Windows PowerShell. Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation.


La version de PowerShell est déterminée par la **Version du runtime** spécifiée (c’est-à-dire la version 7.1 (préversion) ou 5.1). Le service Azure Automation prend en charge le dernier runtime PowerShell.
 
Les mêmes bac à sable Azure et Runbook Worker hybride peuvent exécuter des runbooks **PowerShell 5.1** et **PowerShell 7.1** côte à côte.   

> [!NOTE]
>  Au moment de l’exécution du runbook, si vous sélectionnez **7.1 (préversion)** comme **Version du runtime**, les modules PowerShell ciblant la version de runtime 7.1 sont utilisés, et si vous sélectionnez **5.1** comme **Version du runtime**, les modules PowerShell ciblant la version de runtime 5.1 sont utilisés.
 
Veillez à sélectionner la bonne version de runtime pour les modules.

Par exemple : si vous exécutez un runbook pour un scénario d’automatisation SharePoint dans la **version de runtime** *7.1 (préversion)* , importez le module dans la **version de runtime** **7.1 (préversion)** . Si vous exécutez un runbook pour un scénario d’automatisation SharePoint dans la **version de runtime** **5.1**, importez le module dans la **version de runtime** *5.1*. Dans ce cas, vous voyez deux entrées pour le module, une pour la **version de runtime** **7.1 (préversion)** et une autre pour **5.1**.

:::image type="content" source="./media/automation-runbook-types/runbook-types.png" alt-text="Types de runbook.":::


Actuellement, PowerShell 5.1 et 7.1 (préversion) sont pris en charge.


### <a name="advantages"></a>Avantages

* Implémentez toute la logique complexe avec le code PowerShell sans la complexité supplémentaire de PowerShell Workflow.
* Démarrez plus rapidement que les runbooks de workflow PowerShell dans la mesure où ils n’ont pas besoin d'être compilés avant l'exécution.
* Exécutez dans Azure et sur des Runbook Workers hybrides pour Windows et Linux.

### <a name="limitations---version-51"></a>Limitations - version 5.1

* Vous devez être familiarisé avec les scripts PowerShell.
* Les runbooks ne peuvent pas utiliser un [traitement en parallèle](automation-powershell-workflow.md#use-parallel-processing) pour effectuer plusieurs actions en parallèle.
* Les runbooks ne peuvent pas utiliser de [points de contrôle](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) pour reprendre le runbook en cas d'erreur.
* Vous pouvez uniquement inclure PowerShell, les runbooks de flux de travail PowerShell et les runbooks graphiques comme runbooks enfants à l’aide de la cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook), ce qui crée un travail.
* Les runbooks ne peuvent pas utiliser l’instruction PowerShell [#Requires](/powershell/module/microsoft.powershell.core/about/about_requires), car elle n’est pas prise en charge dans le bac à sable Azure ni sur les Runbooks Workers hybrides et peut entraîner l’échec de la tâche.

### <a name="known-issues---version-51"></a>Problèmes connus - version 5.1

Voici les problèmes connus actuels rencontrés avec les runbooks PowerShell :

* Les runbooks PowerShell ne peuvent pas récupérer une [ressource variable](./shared-resources/variables.md) non chiffrée avec une valeur null.
* Les runbooks PowerShell ne peuvent pas récupérer une ressource variable dont le nom contient le symbole `*~*`.
* Une opération [Get-Process](/powershell/module/microsoft.powershell.management/get-process) dans une boucle d’un runbook PowerShell peut se bloquer après environ 80 itérations.
* Un runbook PowerShell peut échouer s’il essaie d'écrire une grande quantité de données à la fois dans le flux de sortie. Vous pouvez généralement contourner ce problème. Pour ce faire, le runbook doit sortir uniquement les informations nécessaires pour travailler avec des objets volumineux. Par exemple, au lieu d’utiliser `Get-Process` sans aucune limitation, vous pouvez faire en sorte que l’applet de commande génère uniquement les paramètres requis, comme dans `Get-Process | Select ProcessName, CPU`.

### <a name="limitations---71-preview"></a>Limitations - 7.1 (préversion)

-  Les applets de commande PowerShell internes d’Azure Automation ne sont pas prises en charge sur un Runbook Worker hybride Linux. Vous devez importer le module `automationassets` au début de votre runbook Python pour accéder aux fonctions de ressources partagées du compte Automation. 
-  Dans la version de runtime PowerShell 7, les activités du module ne sont pas extraites pour les modules importés.
-  Le type de paramètre de runbook *PSCredential* n’est pas pris en charge dans la version de runtime PowerShell 7.
-  PowerShell 7.x ne prend pas en charge les workflows. Pour plus d’informations, consultez [cette page](/powershell/scripting/whats-new/differences-from-windows-powershell?view=powershell-7.1#powershell-workflow&preserve-view=true).
-  PowerShell 7.x ne prend pas en charge les runbooks signés pour le moment.

### <a name="known-issues---71-preview"></a>Problèmes connus - 7.1 (préversion)

-  L’exécution de scripts enfants à l’aide de `.\child-runbook.ps1` n’est pas prise en charge dans cette préversion. 
  **Solution de contournement** : utilisez `Start-AutomationRunbook` (applet de commande interne) ou `Start-AzAutomationRunbook` (à partir du module *Az.Automation*) pour démarrer un autre runbook à partir du runbook parent.
-  Les propriétés de runbook définissant la préférence de journalisation ne sont pas prises en charge dans le runtime PowerShell 7.  
  **Solution de contournement** : définissez explicitement la préférence au début du runbook comme indiqué ci-dessous :

      `$VerbosePreference = "Continue"`

      `$ProgressPreference = "Continue"`

-   Évitez d’importer le module `Az.Accounts` dans la version 2.4.0 du runtime PowerShell 7, car il peut avoir un comportement inattendu avec cette version dans Azure Automation. 
-    Vous pouvez rencontrer des problèmes de mise en forme avec des flux de sortie d’erreur pour les travaux qui s’exécutent dans le runtime PowerShell 7.

## <a name="powershell-workflow-runbooks"></a>Runbooks de workflow PowerShell

Les Runbooks de workflow PowerShell sont des Runbooks texte basés sur un [workflow Windows PowerShell](automation-powershell-workflow.md). Vous modifiez directement le code du Runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser n'importe quel éditeur de texte hors ligne et [importer le Runbook](manage-runbooks.md) dans Azure Automation. 

>[!NOTE]
> PowerShell 7.1 ne prend pas en charge les runbooks de workflow.

### <a name="advantages"></a>Avantages

* Implémentez tout type de logique complexe avec le code de workflow PowerShell.
* Utilisez des [points de contrôle](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) pour reprendre l’opération en cas d'erreur.
* Utilisez un [traitement en parallèle](automation-powershell-workflow.md#use-parallel-processing) pour mener plusieurs actions en parallèle.
* Peut inclure d'autres runbooks graphiques et des runbooks de workflow PowerShell en tant que runbooks enfants afin de créer des workflows de haut niveau.

### <a name="limitations"></a>Limites

* Vous devez être familiarisé avec les workflows PowerShell.
* Les runbooks doivent pouvoir gérer la complexité supplémentaire liée au workflow PowerShell, notamment les [objets désérialisés](automation-powershell-workflow.md#deserialized-objects).
* Les runbooks prennent plus de temps à démarrer que les runbooks PowerShell, car il doivent être compilés avant l'exécution.
* Vous pouvez inclure uniquement des runbooks PowerShell en tant que runbooks enfants à l’aide de l’applet de commande `Start-AzAutomationRunbook`.
* Les runbooks ne peuvent pas être exécutés sur un Runbook Worker hybride.

## <a name="python-runbooks"></a>Runbooks Python

Les runbooks Python sont compilés sous Python 2 et Python 3. Les runbooks Python 3 sont actuellement en préversion. Vous pouvez modifierz directement le code du runbook à l'aide de l'éditeur de texte du portail Azure. Vous pouvez également utiliser un éditeur de texte hors ligne et [importer le runbook](manage-runbooks.md) dans Azure Automation.

Les runbooks Python 3 sont pris en charge par les infrastructures globales Azure suivantes :

* Azure global
* Azure Government

### <a name="advantages"></a>Avantages

* Utilisation des bibliothèques Python robustes.
* Ils peuvent s’exécuter dans Azure ou sur des runbooks Worker hybrides.
* Pour Python 2, les runbooks Worker hybrides Windows sont pris en charge si [Python 2.7](https://www.python.org/downloads/release/latest/python2) est installé.
* Pour les tâches cloud Python 3, la version Python 3.8 est prise en charge. Les scripts et les packages de toute version 3.x peuvent fonctionner si le code est compatible entre les différentes versions.  
* Pour les tâches hybrides Python 3 sur des ordinateurs Windows, vous pouvez choisir d’installer une version 3.x quelconque que vous souhaitez utiliser.  
* Pour les travaux hybrides Python 3 sur des machines Linux, nous dépendons de la version Python 3 installée sur la machine pour exécuter DSC OMSConfig et le worker hybride Linux. Nous vous recommandons d’installer la version 3.6 sur les machines Linux. Toutefois, d’autres versions devraient également fonctionner si aucun changement cassant n’intervient dans les signatures de méthode ni les contrats entre les versions de Python 3.

### <a name="limitations"></a>Limites

* Vous devez être familiarisé avec les scripts Python.
* Pour utiliser des bibliothèques tierces, vous devez [importer les packages](python-packages.md) dans le compte Automation.
* L’utilisation de l’applet de commande **Start-AutomationRunbook** dans PowerShell/PowerShell Workflow pour démarrer un runbook Python 3 (préversion) ne fonctionne pas. Vous pouvez utiliser l’applet de commande  **Start-AzAutomationRunbook** issue du module Az.Automation ou l’applet de commande  **Start-AzureRmAutomationRunbook** issue du module AzureRm.Automation pour contourner cette limitation.  
* Azure Automation ne prend pas en charge  **sys.stderr**.

### <a name="multiple-python-versions"></a>Plusieurs versions de Python

Pour un Runbook Worker Windows, lors de l’exécution d’un Runbook Python 2, il recherche d’abord la variable d’environnement `PYTHON_2_PATH` et vérifie si elle pointe vers un fichier exécutable valide. Par exemple, si le dossier d’installation est `C:\Python2`, il vérifie si `C:\Python2\python.exe` correspond à un chemin d’accès valide. Si ce n’est pas le cas, il recherche la variable d’environnement `PATH` pour effectuer une vérification similaire.

Pour Python 3, il recherche d'abord la variable d’environnement `PYTHON_3_PATH`, puis revient à la variable d’environnement `PATH`.

Lorsque vous utilisez une seule version de Python, vous pouvez ajouter le chemin d’installation à la variable `PATH`. Si vous souhaitez utiliser les deux versions sur le Runbook Worker, définissez `PYTHON_2_PATH` et `PYTHON_3_PATH` sur l’emplacement du module correspondant à ces versions.

### <a name="known-issues"></a>Problèmes connus

Pour les tâches cloud, les travaux Python 3 échouent parfois avec un message d’exception `invalid interpreter executable path`. Vous pouvez voir cette exception si le travail est retardé, démarre en plus de 10 minutes ou utilise **Start-AutomationRunbook** pour démarrer les runbooks Python 3. Si le travail est retardé, le redémarrage du runbook doit être suffisant. Les tâches hybrides devraient fonctionner sans problème si vous procédez comme suit :

1. Créez une variable d’environnement nommée `PYTHON_3_PATH` et spécifiez le dossier d’installation. Par exemple, si le dossier d’installation est `C:\Python3`, ce chemin doit être ajouté à la variable.
1. Redémarrez l’ordinateur après avoir défini la variable d’environnement.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les runbooks PowerShell, consultez [Tutorial : Créer un runbook PowerShell](./learn/powershell-runbook-managed-identity.md).
* Pour découvrir les runbooks de flux de travail, consultez [Tutoriel : Créer un runbook de flux de travail PowerShell](learn/automation-tutorial-runbook-textual.md).
* Pour découvrir les runbooks graphiques, consultez [Futoriel : Créer un runbook graphique](./learn/powershell-runbook-managed-identity.md).
* Pour en savoir plus sur les runbooks Python, consultez [Tutorial : Créer un runbook Python](./learn/automation-tutorial-runbook-textual-python-3.md).