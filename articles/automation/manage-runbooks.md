---
title: Gérer les runbooks dans Azure Automation
description: Cet article explique comment gérer des runbooks dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99f86065b9a33125e3ca4cf72af5b8ec442e3cc0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455454"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gérer les runbooks dans Azure Automation

Vous pouvez ajouter un runbook à Azure Automation en en créant un ou en en important un existant à partir d’un fichier ou de la [galerie de runbooks](automation-runbook-gallery.md). Cet article fournit des informations sur la gestion d’un runbook ainsi que des modèles recommandés et les bonnes pratiques en matière de conception de runbook. Vous trouverez toutes les informations sur l’accès aux runbooks et modules communautaires dans [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Créer un runbook

Créez un runbook dans Azure Automation à l’aide du portail Azure ou de PowerShell. Une fois le runbook créé, vous pouvez le modifier à l'aide des informations contenues dans :

* [Modifier un runbook textuel dans Azure Automation](automation-edit-textual-runbook.md)
* [Découvrez les concepts clés du flux de travail PowerShell pour les runbooks Automation](automation-powershell-workflow.md)
* [Gérer des packages Python 2 dans Azure Automation](python-packages.md)
* [Gérer des packages Python 3 (préversion) dans Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Créer un runbook dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. À partir du compte Automation, sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
1. Cliquez sur **Créer un runbook**.
    1. Nommez le runbook.
    1. Dans la liste déroulante **Type de runbook**, sélectionnez son [type](automation-runbook-types.md). Le nom du runbook doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets
    1. Sélectionnez la **Version du runtime**
    1. Entrez la **Description** applicable
1. Cliquez sur **Créer** pour créer le runbook.

### <a name="create-a-runbook-with-powershell"></a>Créer un Runbook avec PowerShell

Utilisez l’applet de commande [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) pour créer un runbook vide. Utilisez le paramètre `Type` pour spécifier l’un des types de runbook définis pour `New-AzAutomationRunbook`.

L’exemple suivant montre comment créer un runbook vide.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importer un runbook

Vous pouvez importer un script ou un workflow PowerShell ( **.ps1**), un runbook graphique ( **.graphrunbook**) ou un script Python 2 ou Python 3 ( **.py**) pour créer votre propre runbook. Vous devez spécifier le [type de runbook](automation-runbook-types.md) qui sera créé lors de l'importation et ce, en tenant compte des considérations suivantes.

* Vous pouvez importer un fichier **.ps1** qui ne contient pas de workflow dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) ou un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Si vous l’importez dans un runbook PowerShell Workflow, il est converti en workflow. Dans ce cas, les commentaires sont inclus dans le runbook pour décrire les modifications apportées.

* Vous ne pouvez importer un fichier **.ps1** contenant un workflow PowerShell que dans un [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Si le fichier contient plusieurs workflows PowerShell, l’importation échoue. Vous devez enregistrer chaque workflow dans son propre fichier, puis les importer séparément.

* N'importez pas de fichier **.ps1** contenant un workflow PowerShell dans un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) car le moteur de script PowerShell ne peut pas le reconnaître.

* Importez un fichier **.graphrunbook** que dans un nouveau [runbook graphique](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importer un runbook à partir du portail Azure

Vous pouvez utiliser la procédure suivante pour importer un fichier de script dans Azure Automation.

> [!NOTE]
> Vous pouvez uniquement importer un fichier **.ps1** dans un runbook PowerShell Workflow à l’aide du portail.

1. Sur le portail Azure, recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. À partir du compte Automation, sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
1. Cliquez sur **Importer un runbook**. Vous pouvez sélectionner l'une des options suivantes :
    1. **Rechercher un fichier** : sélectionne un fichier sur votre machine locale.
    1. **Rechercher dans la galerie** : vous pouvez rechercher et sélectionner un runbook existant à partir de la galerie.
1. Sélectionnez le fichier.
1. Si le champ **Nom** est activé, vous avez la possibilité de modifier le nom du runbook. Celui-ci doit commencer par une lettre et peut contenir des lettres, des chiffres, des traits de soulignement et des tirets.
1. Le [**Type de runbook**](automation-runbook-types.md) est rempli automatiquement, mais vous pouvez le changer après avoir pris en compte les restrictions applicables.
1. La **Version du runtime** est remplie automatiquement, mais vous pouvez sélectionner la version dans la liste déroulante.
1. Cliquez sur **Importer**. Le nouveau runbook apparaît dans la liste des runbooks pour le compte Automation.
1. Vous devez [publier le runbook](#publish-a-runbook) avant de pouvoir l'exécuter.

> [!NOTE]
> Après avoir importé un runbook graphique, vous pouvez en convertir le type. Toutefois, vous ne pouvez pas convertir un runbook graphique en runbook textuel.

### <a name="import-a-runbook-with-powershell"></a>Importer un Runbook avec PowerShell

Utilisez l’applet de commande [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) pour importer un fichier de script en tant que brouillon de runbook. Si le runbook existe déjà, l’importation échoue, sauf si vous utilisez le paramètre `Force` avec l’applet de commande.

L’exemple suivant montre comment importer un fichier de script dans un runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Gérer les ressources

Si votre runbook crée une [ressource](automation-runbook-execution.md#resources), le script doit vérifier qu’elle n’existe pas déjà avant d’essayer de la créer. Voici un exemple simple.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Récupérer les détails du journal d’activité

Vous pouvez récupérer les détails d’un runbook, comme le nom de la personne ou le compte qui a démarré le runbook, à partir du [journal d’activité](automation-runbook-execution.md#activity-logging) du compte Automation. L’exemple PowerShell suivant indique le dernier utilisateur à avoir exécuté le runbook spécifié.

```powershell-interactive
$rgName = 'MyResourceGroup'
$accountName = 'MyAutomationAccount'
$runbookName = 'MyRunbook'
$startTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $rgName
    StartTime         = $startTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $runbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $rgName
            AutomationAccountName = $accountName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $runbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName, $Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Suivre la progression

Il est recommandé de créer des runbooks de nature modulaire, avec une logique pouvant être réutilisée et redémarrée facilement. Le suivi de la progression d’un runbook garantit que la logique du runbook s’exécute correctement en cas de problème.

Vous pouvez suivre la progression d’un runbook en utilisant une source externe, notamment un compte de stockage, une base de données ou des fichiers partagés. Créez une logique dans votre runbook qui vérifie dans un premier temps l’état de la dernière action effectuée. Ensuite, selon les résultats de la vérification, la logique peut ignorer ou poursuivre certaines tâches du runbook.

## <a name="prevent-concurrent-jobs"></a>Prévention des travaux simultanés

Certains runbooks peuvent se comporter bizarrement quand ils exécutent plusieurs tâches en même temps. Dans ce cas, il est important qu’un runbook implémente une logique qui puisse déterminer si une tâche est déjà en cours d’exécution. Voici un exemple simple.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

# Check for already running or new runbooks
$runbookName = "runbookName"
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName `
    -AutomationAccountName $automationAccountName `
    -RunbookName $runbookName `
    -DefaultProfile $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook $runbookName is already running"
    exit 1
} else {
    # Insert Your code here
    Write-Output "Runbook $runbookName is not running"
}
```

Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :

1. À la ligne 5, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
1. Entrez l'ID client.

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Gérer les erreurs temporaires dans un script dépendant de l’heure

Vos runbooks doivent être robustes et capables de gérer les [erreurs](automation-runbook-execution.md#errors) temporaires, notamment les erreurs temporaires qui peuvent entraîner leur redémarrage ou une défaillance. Si un runbook rencontre une défaillance, Azure Automation effectue un nouvel essai.

Si votre runbook s’exécute normalement dans les limites d’une contrainte de temps, faites en sorte que le script implémente la logique pour vérifier la durée d’exécution. Cette vérification contrôle la bonne exécution de certaines opérations comme le démarrage, l’arrêt ou le scale-out, uniquement à des moment précis.

> [!NOTE]
> L’heure locale du processus de bac à sable Azure est définie sur le temps universel coordonné (UTC). Les calculs de date et d’heure dans vos runbooks doivent prendre cet élément en considération.

## <a name="work-with-multiple-subscriptions"></a>Utilisation de plusieurs abonnements

Votre runbook doit pouvoir utiliser des [abonnements](automation-runbook-execution.md#subscriptions). Par exemple, pour gérer plusieurs abonnements, le runbook utilise la cmdlet [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave). Cette cmdlet permet de s'assurer que le contexte d’authentification n’est pas récupéré à partir d’un autre runbook s'exécutant dans le même bac à sable. 

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

$childRunbookName = 'childRunbookDemo'
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$startParams = @{
    ResourceGroupName     = $resourceGroupName
    AutomationAccountName = $automationAccountName
    Name                  = $childRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :

1. À la ligne 5, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
1. Entrez l'ID client.

## <a name="work-with-a-custom-script"></a>Utilisation d’un script personnalisé

> [!NOTE]
> Normalement, vous ne pouvez pas exécuter des scripts personnalisés et des runbooks sur l’hôte sur lequel est installé un agent Log Analytics.

Pour utiliser un script personnalisé :

1. Créer un compte Automation.
2. Déployez un rôle [Runbook Worker hybride](automation-hybrid-runbook-worker.md).
3. Si vous utilisez un ordinateur Linux, vous devez disposer de privilèges élevés. Connectez-vous pour [désactiver les vérifications de signature](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Tester un runbook

Lorsque vous testez un runbook, la [version Brouillon](#publish-a-runbook) est exécutée et toutes les actions qu’il effectue sont finalisées. Aucun historique des travaux n'est créé, mais les flux [Résultat](automation-runbook-output-and-messages.md#use-the-output-stream) et [Avertissement et Erreur](automation-runbook-output-and-messages.md#working-with-message-streams) s'affichent dans le volet **Sortie de test**. Les messages destinés au [flux de commentaires](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) ne sont affichés dans le volet des résultats que si la variable [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) est définie sur `Continue`.

Bien que la version Brouillon soit lancée, le runbook s'exécute normalement et effectue toutes les actions sur les ressources de l'environnement. Pour cette raison, vous devez tester les runbooks uniquement sur des ressources hors production.

La procédure de test de chaque [type de runbook](automation-runbook-types.md) est identique. Il n’y a aucune différence de test entre l’éditeur de texte et l’éditeur graphique du portail Azure.

1. Ouvrez la version Brouillon du runbook dans l’[éditeur de texte](automation-edit-textual-runbook.md) ou l’[éditeur graphique](automation-graphical-authoring-intro.md).
1. Cliquez sur **Tester** pour ouvrir la page **Test**.
1. Si le runbook a des paramètres, ils figurent dans le volet gauche dans lequel vous pouvez fournir des valeurs à utiliser pour le test.
1. Si vous souhaitez exécuter le test sur un [runbook Worker hybride](automation-hybrid-runbook-worker.md), définissez les **Paramètres d’exécution** sur **Worker hybride** et sélectionnez le nom du groupe cible. Dans le cas contraire, conservez la valeur par défaut **Azure** pour exécuter le test dans le cloud.
1. Cliquez sur **Démarrer** pour commencer le test.
1. Vous pouvez utiliser les boutons situés sous le volet **Sortie** pour arrêter ou suspendre un [workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou un runbook [graphique](automation-runbook-types.md#graphical-runbooks) en cours de test. Lorsque vous interrompez le runbook, il termine l’activité en cours avant de s’interrompre. Lorsque le runbook est suspendu, vous pouvez l’arrêter ou le redémarrer.
1. Inspectez la sortie du runbook dans le volet **Sortie**.

## <a name="publish-a-runbook"></a>Publier un runbook

Lorsque vous créez ou importez un runbook, vous devez le publier avant de pouvoir l'exécuter. Dans Azure Automation, chaque runbook a une version brouillon et une version publiée. Seule la version publiée est disponible à l'exécution, et seul le brouillon peut être modifié. La version publiée n'est pas affectée par les modifications apportées à la version Bouillon. Quand la version brouillon doit être rendue disponible, vous la publiez, ce qui remplace la version publiée actuelle par la version brouillon.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publier un runbook dans le portail Azure

1. Sur le portail Azure, recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. Ouvrez le runbook dans votre compte Automation.
1. Cliquez sur **Modifier**.
1. Cliquez sur **Publier**, puis sélectionnez **Oui** en réponse au message de vérification.

### <a name="publish-a-runbook-using-powershell"></a>Publier un runbook à l’aide de PowerShell

Utilisez l’applet de commande [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) pour publier votre runbook. 

```azurepowershell-interactive
$accountName = "MyAutomationAccount"
$runbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $accountName
    ResourceGroupName     = $rgName
    Name                  = $runbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Planifier un runbook dans le portail Azure

Une fois votre runbook publié, vous pouvez le planifier en vue de l'utiliser :

1. Sur le portail Azure, recherchez et sélectionnez **Comptes Automation**.
1. Sur la page **Comptes Automation**, accédez à la liste et sélectionnez votre compte Automation.
1. Sélectionnez le runbook dans votre liste de runbooks.
1. Sélectionnez **Planifications** sous **Ressources**.
1. Sélectionnez **Ajouter une planification**.
1. Dans le volet **Planifier un runbook**, sélectionnez **Associer une planification à votre runbook**.
1. Choisissez **Créer une planification** dans le volet **Planification**.
1. Entrez un nom, une description et d’autres paramètres dans le volet **Nouvelle planification**.
1. Une fois la planification créée, mettez-la en surbrillance et cliquez sur **OK**. Elle doit maintenant être associée à votre runbook.
1. Accédez à votre boîte aux lettres et recherchez-y un e-mail qui vous informe de l'état du runbook.

## <a name="obtain-job-statuses"></a>Obtenir les états des tâches

### <a name="view-statuses-in-the-azure-portal"></a>Afficher les états sur le portail Azure

Les détails de la gestion des tâches dans Azure Automation apparaissent dans la section [Tâches](automation-runbook-execution.md#jobs). Lorsque vous êtes prêt à afficher vos tâches runbook, utilisez le portail Azure et accédez à votre compte Automation. À droite, vous pouvez voir un résumé de toutes les tâches runbook dans la section **Statistiques des tâches**.

![Vignette Statistiques des tâches](./media/manage-runbooks/automation-account-job-status-summary.png)

Le résumé affiche un nombre et une représentation graphique de l’état de chaque tâche exécutée.

Lorsque vous cliquez sur la vignette, la page Tâches s’affiche avec un récapitulatif de toutes les tâches exécutées. Cette page indique l’état, le nom du runbook, l’heure de début et l’heure de fin de chaque tâche.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Capture d’écran de la page Tâches.":::

Vous pouvez filtrer la liste des tâches en sélectionnant **Filtrer les tâches**. Filtrez en fonction d’un runbook spécifique, d’un état de tâche ou d’un choix dans la liste déroulante, puis indiquez un intervalle de temps pour la recherche.

![Filtrer en fonction de l’état des tâches](./media/manage-runbooks/automation-account-jobs-filter.png)

Vous pouvez aussi afficher un résumé détaillé des tâches d’un runbook en sélectionnant le runbook dans la page Runbooks de votre compte Automation, puis en sélectionnant **Tâches**. Cette action affiche la page Tâches. De là, vous pouvez cliquer sur un enregistrement de tâche pour en afficher les détails et la sortie.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Capture d’écran de la page Tâches avec le bouton Erreurs mis en surbrillance.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Récupérer des états de tâches avec PowerShell

Utilisez l’applet de commande [Get-AzureAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) pour récupérer les tâches créées pour un runbook ainsi que les détails d’une tâche particulière. Si vous démarrez un runbook à l’aide de `Start-AzAutomationRunbook`, la tâche obtenue est retournée. Utilisez [AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) pour récupérer la sortie de la tâche.

Les exemples suivants obtiennent la dernière tâche d’un exemple de runbook et affichent son état, les valeurs définies pour les paramètres du runbook et la sortie de la tâche.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

L’exemple suivant récupère la sortie d’une tâche spécifique et retourne chaque enregistrement. Si une [exception](automation-runbook-execution.md#exceptions) se produit pour l’un des enregistrements, le script écrit l’exception à la place de la valeur. Ce comportement est utile, car les exceptions peuvent fournir des informations supplémentaires qui ne sont pas nécessairement journalisées au moment de la sortie.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la gestion des runbooks, voir [Exécution d'un Runbook dans Azure Automation](automation-runbook-execution.md).
* Pour préparer un runbook PowerShell, consultez [Modifier des runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md).
* Pour résoudre les problèmes liés à l’exécution de runbooks, voir [Résoudre les erreurs avec les runbooks](troubleshoot/runbooks.md).
