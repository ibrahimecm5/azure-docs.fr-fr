---
title: Tutoriel - Créer un runbook de workflow PowerShell dans Azure Automation
description: Ce tutoriel vous apprend à créer, tester et publier un runbook de workflow PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 10/28/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c677d7690acc3ab05c5d8df2ab516d396be2eb4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465602"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>Tutoriel : Créer un runbook de workflow PowerShell dans Automation

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](../automation-runbook-types.md#powershell-workflow-runbooks) dans Azure Automation. Les runbooks de workflow PowerShell sont des runbooks texte basés sur un workflow Windows PowerShell. Vous pouvez créer et modifier le code du runbook à l’aide de l’éditeur de texte du portail Azure.

>[!NOTE]
>  Cet article s’applique à PowerShell 5.1. PowerShell 7.1 (préversion) ne prend pas en charge les workflows.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un runbook de workflow PowerShell
> * Tester et publier le runbook
> * Exécuter le travail du runbook et en suivre l’état
> * Ajouter une authentification pour gérer les ressources Azure
> * Mettre à jour les paramètres du runbook pour démarrer une machine virtuelle Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure Automation avec au moins une identité managée affectée par l’utilisateur. Pour plus d’informations, consultez [Activer l’identité managée](../quickstarts/enable-managed-identity.md).
* Modules Az : `Az.Accounts` et `Az.Compute` importés dans le compte Automation. Pour plus d’informations, consultez [Importer des modules Az](../shared-resources/modules.md#import-az-modules).
* Au moins deux [machines virtuelles Azure](../../virtual-machines/windows/quick-create-powershell.md). Étant donné que vous arrêtez et démarrez ces machines, celles-ci ne doivent pas être des machines virtuelles de production.
* Le [module PowerShell Azure Az](/powershell/azure/new-azureps-module-az) installé sur votre ordinateur. Pour plus d'informations sur son installation ou sa mise à niveau, consultez [Installer le module PowerShell Azure Az](/powershell/azure/install-az-ps).

## <a name="assign-permissions-to-managed-identities"></a>Attribuer des autorisations aux identités managées

Affectez des autorisations à l’[identité managée](../automation-security-overview.md#managed-identities) appropriée pour lui permettre d’arrêter une machine virtuelle. Le runbook peut utiliser l’identité managée affectée par le système du compte Automation ou une identité managée affectée par l’utilisateur. Des étapes sont fournies pour affecter des autorisations à chaque identité. Les étapes ci-dessous utilisent le portail Azure. Si vous préférez utiliser PowerShell, consultez [Attribuer des rôles Azure avec Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre compte Automation.

1. Sous **Paramètres du compte**, sélectionnez **Identité (préversion)** .

1. Sous l’onglet **Affecté par le système**, sous **Autorisations**, sélectionnez **Attributions de rôles Azure** pour ouvrir la page **Attributions de rôles Azure**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="Sélection des attributions de rôles dans le portail.":::

1. Sélectionnez **+ Ajouter une attribution de rôle (préversion)** pour ouvrir la page **Ajouter une attribution de rôle (préversion)** . 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="Ajouter des attributions de rôles dans le portail.":::

1. Sélectionnez les valeurs appropriées.

   |Propriété |Description |
   |---|---|
   |Étendue| Une étendue est un ensemble de ressources auxquelles s’applique l’attribution de rôle. Dans la liste déroulante, sélectionnez **Groupe de ressources**.|
   |Abonnement|Ce champ devrait être renseigné automatiquement avec votre abonnement.|
   |Groupe de ressources|Dans la liste déroulante, sélectionnez le groupe de ressources sur lequel les autorisations d’identité doivent être accordées.|
   |Rôle|Dans la zone de liste déroulante, sélectionnez **Utilisateur DevTest Labs**.|

1. Sélectionnez **Enregistrer**, puis fermez la page **Attributions de rôles Azure** pour revenir à l’onglet **Affecté par le système**.

1. Sélectionnez l’onglet **Affecté(e) par l’utilisateur**.

1. Sélectionnez votre identité managée affectée par l’utilisateur dans la liste pour ouvrir la page **Identité managée**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="Sélection d’une identité managée affectée par l’utilisateur dans le portail.":::

1. Notez l’**ID client** pour l’utiliser plus tard.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="Affichage de l’ID client de l’identité managée dans le portail":::

1. Dans le menu de gauche, sélectionnez **Attributions de rôles Azure** et **+ Ajouter une attribution de rôle (préversion)** pour ouvrir la page **Ajouter une attribution de rôle (préversion)** . 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="Ajouter des attributions de rôles dans le portail pour l’identité affectée par l’utilisateur.":::

1. Sélectionnez les valeurs appropriées.

   |Propriété |Description |
   |---|---|
   |Étendue| Dans la liste déroulante, sélectionnez **Groupe de ressources**.|
   |Abonnement|Ce champ devrait être renseigné automatiquement avec votre abonnement.|
   |Groupe de ressources|Dans la liste déroulante, sélectionnez le groupe de ressources sur lequel les autorisations d’identité doivent être accordées.|
   |Rôle|Dans la zone de liste déroulante, sélectionnez **Utilisateur DevTest Labs**.|

1. Sélectionnez **Enregistrer**, puis fermez la page **Attributions de rôles Azure** pour revenir à l’onglet **Affecté par l’utilisateur**.

## <a name="create-new-runbook"></a>Créer un runbook

Commencez par créer un [runbook de workflow PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) simple. L'un des avantages des workflows Windows PowerShell est la possibilité d'exécuter un ensemble de commandes en parallèle, et non séquentiellement comme avec un script classique.

>[!NOTE]
> Avec cette version, la création de runbook a une nouvelle expérience dans le portail Azure. Quand vous sélectionnez le panneau **Runbooks** > **Créer un runbook**, une nouvelle page **Créer un runbook** s’ouvre avec les options applicables. 

1. Dans la page de votre compte Automation ouvert, sous **Automatisation de processus**, sélectionnez **Runbooks**

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="Créer un runbook de workflow PowerShell dans le portail":::

1. Sélectionnez **+ Créer un runbook**.
    1. Nommez le runbook. Par exemple, test.
    1. Dans le menu déroulant **Type de runbook**, sélectionnez **PowerShell**.
    1. Dans la liste déroulante **Version du runtime**, sélectionnez **5.1**.
    1. Entrez la **Description** applicable.
    1. Sélectionnez **Create** (Créer).
   
    :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook-options.png" alt-text="Options de runbook du workflow PowerShell dans le portail":::
   

## <a name="add-code-to-the-runbook"></a>Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le runbook, soit sélectionner des cmdlets, des runbooks et des ressources à partir du contrôle Bibliothèque, puis les ajouter au runbook avec tous les paramètres associés. Pour ce tutoriel, vous entrez le code directement dans le runbook.

Pour l’instant, votre runbook est vide, à l’exception du mot clé `Workflow` obligatoire, du nom de votre runbook et des accolades qui entourent la totalité du workflow.

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. Vous pouvez utiliser le mot clé `Parallel` pour créer un bloc de script avec plusieurs commandes qui seront exécutées simultanément. Entrez le code suivant *entre* accolades :

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. Enregistrez le runbook en sélectionnant **Enregistrer**.

## <a name="test-the-runbook"></a>Tester le Runbook

Avant de publier le runbook pour le rendre disponible en production, vous devez le tester pour vous assurer qu’il fonctionne correctement. Le test d’un runbook exécute sa version Brouillon et vous permet d’afficher sa sortie de manière interactive.

1. Sélectionnez **Volet de test** pour ouvrir la page **Test**.

1. Sélectionnez **Démarrer** pour démarrer le test.  Une [tâche de runbook](../automation-runbook-execution.md) est créée et son état est affiché dans le volet.

   L’état initial du travail est Mis en file d’attente pour indiquer qu’il attend qu’un Runbook Worker devienne disponible dans le cloud. L’état passe ensuite à Démarrage quand un Worker revendique le travail. Enfin, l’état passe à Exécution lorsque le runbook commence à s’exécuter.

1. Une fois le travail du runbook terminé, la page **Test** affiche sa sortie. Le résultat devrait être semblable à l’image suivante :

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="Sortie parallèle du runbook de workflow PowerShell":::

   Passez en revue la sortie. Tout ce qui se trouve dans le bloc `Parallel`, y compris la commande `Start-Sleep`, a été exécuté en même temps. Les mêmes commandes en dehors du bloc `Parallel` ont été exécutées de manière séquentielle, comme indiqué par les différents horodatages de date. 

1. Fermez la page **Test** pour revenir au canevas.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que vous avez créé est toujours en mode brouillon. Vous devez le publier pour pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans ce cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Sélectionnez l’option **Publier** pour publier le runbook, puis cliquez sur **Oui** quand vous y êtes invité.

1. Le champ **État** montre maintenant **Publié**. Passez en revue les options en haut qui vous permettent de démarrer le runbook maintenant, de planifier une heure de début à venir ou de créer un [webhook](../automation-webhooks.md) pour démarrer le runbook via un appel HTTP. Cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité pour démarrer le runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="Page Vue d’ensemble du runbook de workflow PowerShell":::

1. Une page **Travail** s’ouvre pour le travail du runbook qui a été créé. Dans le cas présent, laissez la page ouverte pour pouvoir suivre la progression du travail. Le champ **État** correspond aux états que vous avez vus lors du test du runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Capture d’écran de la page Travail du runbook.":::

1. Lorsque l’état du runbook est **Terminé**, sélectionnez **Sortie**. La sortie devrait ressembler à la sortie du test.

1. Fermez la page **Travail**, puis revenez à la page **Vue d’ensemble** du runbook.

1. Sous **Ressources**, sélectionnez **Travaux**. Cette page répertorie tous les travaux créés par votre runbook. Vous ne devriez en voir qu’un seul, car vous n’avez exécuté le travail qu’une seule fois.

1. Sélectionnez la tâche pour ouvrir la même page **Tâche** que celle consultée au démarrage du runbook. Utilisez cette page pour voir les détails de tout travail créé pour le runbook. Fermez la page **Travail**, puis revenez à la page **Vue d’ensemble** du runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Ajouter une authentification pour gérer les ressources Azure

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Il ne peut le faire que s’il s’authentifie à l’aide des informations d’identification de l’abonnement. Le runbook utilise l’identité managée affectée par le système du compte Automation pour s’authentifier auprès d’Azure afin d’effectuer l’action de gestion sur la machine virtuelle. Le runbook peut être facilement modifié pour utiliser une identité managée affectée par l’utilisateur.

1. Sélectionnez **Vue d’ensemble** et **Modifier** pour ouvrir l’éditeur de texte.

1. Remplacez tout le code existant par ce qui suit :

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   Modifiez la variable `$resourceGroup` avec une valeur valide représentant votre groupe de ressources.

1. Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
    1. À la ligne 9, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
    1. Entrez l’ID client que vous avez obtenu précédemment.

1. Sélectionnez **Enregistrer**, puis **volet Test**.

1. Sélectionnez **Démarrer** pour démarrer le test. Une fois le test terminé, une sortie semblable à celle illustrée ci-dessous devrait afficher des informations de base sur votre compte. Cette action confirme la validité des informations d’identification.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="Informations de base qui confirment les informations d’identification.":::

1. Fermez la page **Test** pour revenir au canevas.

## <a name="add-code-to-start-a-virtual-machine"></a>Ajouter du code pour démarrer une machine virtuelle

À présent que votre runbook s’authentifie auprès de l’abonnement Azure, vous pouvez gérer les ressources. Ajoutez une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle de votre abonnement Azure. Pour l’instant, vous allez coder ce nom en dur dans le runbook. 

1. Ajoutez le code ci-dessous comme dernière ligne, juste avant l’accolade fermante. Remplacez `VMName` par le nom de la machine virtuelle. 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Testez le runbook et confirmez que la machine virtuelle a démarré. Revenez ensuite au canevas.

## <a name="add-input-parameters-to-the-runbook"></a>Ajouter des paramètres d’entrée au runbook

Votre runbook démarre actuellement la machine virtuelle que vous avez codée en dur dans le runbook. Elle sera plus utile si vous pouvez la spécifier au démarrage du runbook. Ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Remplacez la ligne 3, `$resourceGroup = "resourceGroupName"`, par le code suivant :

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. Remplacez la commande `Start-AzVM` précédente par le code suivant :

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Testez le runbook et confirmez que la machine virtuelle a démarré. Revenez ensuite au canevas.

## <a name="manage-multiple-vms-simultaneously"></a>Gérer plusieurs machines virtuelles simultanément

Vous pouvez utiliser la construction `ForEach -Parallel` pour traiter simultanément les commandes de chaque élément d'une collection. Modifiez le code pour que le runbook :
- Accepte un regroupement de noms de machines virtuelles,
- Accepte un paramètre pour arrêter ou démarrer les machines virtuelles, et
- Exécute les actions en parallèle sur toutes les machines virtuelles

1. Remplacez tout le code existant par ce qui suit :

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
    1. À la ligne 13, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
    1. Entrez l’ID client que vous avez obtenu précédemment.

1. Sélectionnez **Enregistrer**, **Publier**, puis **Oui** lorsque vous y êtes invité.

1. À partir de la page **Vue d’ensemble**, sélectionnez **Démarrer**.

1. Renseignez les paramètres, puis sélectionnez **OK**.

   |Paramètre |Description |
   |---|---|
   |RESOURCEGROUP|Entrez le nom du groupe de ressources des machines virtuelles.|
   |Machines virtuelles|Entrez les noms des machines virtuelles à l’aide de la syntaxe suivante : `["VM1","VM2","VM3"]`|
   |Action|Entrez `stop` ou `start`.|

1. Accédez à la liste des machines virtuelles et actualisez la page toutes les quelques secondes. Notez que l’action pour chaque machine virtuelle s’effectue en parallèle. Sans le mot clé `-Parallel`, les actions auraient été effectuées de manière séquentielle. Tandis que les machines virtuelles démarrent de façon séquentielle, chaque machine virtuelle peut atteindre la phase **En cours d’éxecution** à des moments légèrement différents en fonction des caractéristiques de chaque machine virtuelle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser ce runbook, supprimez-le en effectuant les étapes suivantes :

1. Accédez à votre compte Automation.
1. Sous **Automatisation de processus**, sélectionnez **Runbooks**.
1. Sélectionnez le Runbook.
1. À partir de la page **Vue d’ensemble** du runbook, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un runbook de workflow PowerShell. Pour obtenir un aperçu des runbooks Python 3, consultez :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un runbook Python 3 (préversion)](automation-tutorial-runbook-textual-python-3.md)