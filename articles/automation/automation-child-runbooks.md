---
title: Créer des runbooks modulaires dans Azure Automation
description: Cet article explique comment créer un runbook qu’un autre runbook appelle.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4eff80637886901644b36b19f74d79ae83e7cfe3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455489"
---
# <a name="create-modular-runbooks-in-automation"></a>Créer des runbooks modulaires dans Automation

Dans Azure Automation, il est de bonne pratique d’écrire des runbooks modulaires réutilisables avec une fonction discrète que d’autres runbooks appellent. Un runbook parent appelle souvent un ou plusieurs runbooks enfants pour exécuter la fonctionnalité requise. 

Il existe deux façons d’appeler un runbook enfant : inline ou via une cmdlet. Le tableau suivant résume les différences qui vous aident à déterminer la meilleure méthode pour vos scénarios.

|  | En ligne | Applet de commande |
|:--- |:--- |:--- |
| **Travail** |Les Runbooks enfants s’exécutent dans la même tâche que le parent. |Une tâche distincte est créée pour le Runbook enfant. |
| **Exécution** |Le runbook parent attend que le runbook enfant se termine avant de continuer. |Le runbook parent continue immédiatement après le démarrage du runbook enfant, *ou* attend que le travail enfant se termine. |
| **Sortie** |Le runbook parent peut obtenir directement à la sortie du runbook enfant. |Le runbook parent doit récupérer la sortie du travail du runbook enfant *ou* peut obtenir directement la sortie du runbook enfant. |
| **Paramètres** |Les valeurs des paramètres du Runbook enfant sont spécifiées séparément et peuvent utiliser n’importe quel type de données. |Les valeurs des paramètres du runbook enfant doivent être combinées dans une table de hachage unique. Cette table de hachage ne peut inclure que des types de données simples, tableau et objet qui utilisent une sérialisation JSON. |
| **Compte Automation** |Le runbook parent ne peut qu’un runbook enfant du même compte Automation. |Les runbooks parents peuvent utiliser un runbook enfant de n’importe quel compte Automation, du même abonnement Azure ou d’un autre abonnement auquel vous avez accès. |
| **Publication** |Un runbook enfant doit être publié avant la publication du runbook parent. |Le runbook enfant est à tout moment avant le démarrage du runbook parent. |

## <a name="call-a-child-runbook-by-using-inline-execution"></a>Appeler un runbook enfant à l’aide d’une exécution incluse

Pour appeler un runbook inclus à partir d’un autre runbook, utilisez le nom du runbook et fournissez des valeurs pour ses paramètres de la même façon qu’avec une activité ou une cmdlet. Tous les runbooks dans le même compte Automation peuvent être utilisés de cette manière. Le runbook parent attend la fin de l’exécution du runbook enfant avant de passer à la ligne suivante, et toute sortie est retournée directement au parent.

Quand vous appelez un runbook inclus, il s’exécute dans le même travail que le runbook parent. L’historique des travaux du runbook enfant ne contient aucune indication. Toutes les exceptions et sorties de flux issues du runbook enfant sont associées au parent. Ce comportement réduit le nombre de tâches et facilite leur suivi et la résolution des problèmes.

Lorsqu’un Runbook est publié, les Runbooks enfants qu’il appelle doivent déjà être publiés. En effet, Azure Automation crée une association avec tous les runbooks enfants lorsqu’il compile un runbook. Si les runbooks enfants n’ont pas encore été publiés, le runbook parent semble être publié correctement, mais il génère une exception lors de son démarrage. 

Si vous recevez une exception, vous pouvez republier le runbook parent pour référencer correctement les runbooks enfants. Il est inutile de republier le runbook parent si des runbooks enfants sont modifiés, car l’association a déjà été créée.

Les paramètres d’un runbook enfant appelé inlined peuvent être de n’importe quel type de données, y compris des objets complexes. Il n’y a pas de [sérialisation JSON](start-runbooks.md#work-with-runbook-parameters), comme quand vous démarrez le runbook à l’aide du portail Azure ou de la cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

### <a name="runbook-types"></a>Types de runbook

Actuellement, PowerShell 5.1 et 7.1 (préversion) sont pris en charge et seuls certains types de runbook peuvent s’appeler les uns les autres :

* Un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) et un [runbook graphique](automation-runbook-types.md#graphical-runbooks) peuvent s’appeler mutuellement de manière incluse, car tous deux sont basés sur PowerShell.
* Un [runbook de flux de travail PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) et un runbook de flux de travail PowerShell graphique peuvent s’appeler mutuellement de manière incluse, car tous deux sont basés sur PowerShell.
* Les types PowerShell et les types de flux de travail PowerShell ne peuvent pas s’appeler mutuellement de manière incluse. Ils doivent utiliser `Start-AzAutomationRunbook`.

L’ordre de publication des runbooks importe uniquement pour les runbooks de flux de travail PowerShell et de flux de travail PowerShell graphique.

Lorsque votre runbook appelle un runbook enfant de flux de travail PowerShell ou graphique avec une exécution incluse, il utilise le nom du runbook. Ce nom doit commencer par `.\\` pour spécifier que le script se trouve dans le répertoire local.

### <a name="example"></a>Exemple

L’exemple suivant démarre un runbook enfant de test qui accepte un objet complexe, une valeur entière et une valeur booléenne. La sortie du Runbook enfant est affectée à une variable. Dans ce cas, le runbook enfant est un runbook de workflow PowerShell.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Voici le même exemple utilisant un runbook PowerShell comme enfant.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-by-using-a-cmdlet"></a>Démarrer un runbook enfant à l’aide d’une cmdlet

> [!IMPORTANT]
> Si votre runbook appelle un runbook enfant à l’aide de la cmdlet `Start-AzAutomationRunbook` avec le paramètre `Wait`, et que le runbook enfant produit un objet en sortie, l’opération peut rencontrer une erreur. Pour contourner cette erreur, consultez [Runbooks enfants avec une sortie objet](troubleshoot/runbooks.md#child-runbook-object). Cet article montre comment implémenter la logique d’interrogation des résultats à l’aide de la cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord).

Vous pouvez utiliser `Start-AzAutomationRunbook` pour démarrer un runbook, comme décrit dans [Démarrer un runbook avec Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Il existe deux modes d’utilisation de cette cmdlet :

- La cmdlet retourne l’ID du travail lors de la création de celui-ci pour le runbook enfant. 
- La cmdlet attend que le travail enfant se termine et retourne la sortie du runbook enfant. Votre script active ce mode en spécifiant le paramètre `Wait`.

Le tâche d’un runbook enfant démarrée avec une applet de commande s’exécute séparément de celle du runbook parent. Ce comportement génère davantage de tâches que le démarrage de runbook inclus, et rend leur suivi plus complexe. Le parent peut démarrer plusieurs runbooks enfants de façon asynchrone, sans attendre la fin de leur exécution. Pour cette exécution parallèle qui appelle des runbooks enfants inlined, le runbook parent doit utiliser le [mot clé parallèle](automation-powershell-workflow.md#use-parallel-processing).

La sortie des runbooks enfants n’est pas retournée au runbook parent de manière fiable en raison du minutage. De plus, il se peut que des variables `$VerbosePreference`, `$WarningPreference` et autres ne soient pas propagées aux runbooks enfants. Pour éviter ces problèmes, vous pouvez démarrer les runbooks enfants en tant que travaux Automation distincts en utilisant `Start-AzAutomationRunbook` avec le paramètre `Wait`. Cette technique bloque le runbook parent jusqu’à ce que l’exécution du runbook enfant soit terminée.

Si vous ne voulez pas que le runbook parent soit bloqué durant l’attente, vous pouvez démarrer le runbook enfant en utilisant `Start-AzAutomationRunbook` sans le paramètre `Wait`. Dans ce cas, votre runbook doit utiliser [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) pour attendre la fin de la tâche. Il doit également utiliser [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) et [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) pour récupérer les résultats.

Les paramètres d’un runbook enfant démarré avec une applet de commande sont fournis sous forme de table de hachage, comme décrit dans [Paramètres de runbook](start-runbooks.md#work-with-runbook-parameters). Vous ne pouvez utiliser que des types de données simples. Si le runbook possède un paramètre avec un type de données complexe, il doit être appelé de manière incluse.

Il se peut que le contexte d’abonnement soit perdu quand vous démarrez les runbooks enfants en tant que travaux distincts. Pour que le runbook enfant exécute des applets de commande de modules Az sur un abonnement Azure spécifique, il doit s’authentifier dans cet abonnement indépendamment du runbook parent.

Si des tâches au sein du même compte Automation utilisent plusieurs abonnements, la sélection d’un abonnement dans une tâche peut changer le contexte d’abonnement actuellement sélectionné pour d’autres tâches. Pour éviter cette situation, utilisez `Disable-AzContextAutosave -Scope Process` au début de chaque runbook. Cette action enregistre le contexte uniquement pour l’exécution de ce runbook.

### <a name="example"></a>Exemple

L’exemple suivant démarre un runbook enfant avec des paramètres, puis attend que son exécution se termine, en utilisant la cmdlet `Start-AzAutomationRunbook` avec le paramètre `Wait`. Une fois l’exécution du runbook enfant terminée, l’exemple collecte la sortie de la cmdlet à partir de celui-ci. Pour utiliser `Start-AzAutomationRunbook`, le script doit s'authentifier auprès de votre abonnement Azure.

```powershell
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    -Parameters $params -Wait
```

Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
1. À la ligne 5, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
1. Entrez l'ID client.

## <a name="next-steps"></a>Étapes suivantes

* Pour exécuter votre runbook, consultez [Démarrer un runbook dans Azure Automation](start-runbooks.md).
* Pour surveiller l’opération runbook, consultez [Sortie et messages de runbook dans Azure Automation](automation-runbook-output-and-messages.md).
