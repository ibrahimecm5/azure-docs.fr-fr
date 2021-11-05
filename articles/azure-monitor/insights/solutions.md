---
title: Solutions de monitoring dans Azure Monitor | Microsoft Docs
description: Obtenez des informations sur les collections préemballées de règles de logique, de visualisation et d’acquisition de données pour différents domaines problématiques.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 10/16/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 57f5c3f3dace57b42ed5ac32e48c5d070517e03a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059943"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Solutions de monitoring dans Azure Monitor

> [!CAUTION]
> Les solutions d’analyse ne sont plus en cours de développement actif.  La technologie de remplacement est nommée [Azure Monitor Insights](/azure/azure-monitor/monitor-reference#insights). Nous vous suggérons d’utiliser les insights et de ne pas déployer de nouvelles instances de ces solutions.  

Les solutions de monitoring dans Azure Monitor fournissent une analyse du fonctionnement d’une application ou d’un service Azure. Cet article offre une vue d’ensemble des solutions de monitoring Azure et des informations sur leur utilisation et leur installation. 

Vous pouvez ajouter des solutions de monitoring à Azure Monitor pour toutes les applications et tous les services que vous utilisez. Elles sont généralement disponibles gratuitement, mais elles collectent des données susceptibles d’occasionner des frais d’utilisation.

## <a name="use-monitoring-solutions"></a>Utiliser des solutions de supervision

La page **Vue d’ensemble** dans Azure Monitor affiche un titre pour chaque solution installée dans un espace de travail Log Analytics. Pour ouvrir cette page, accédez à **Azure Monitor** dans le [portail Azure](https://ms.portal.azure.com). Dans le menu **Insights**, sélectionnez **Plus** pour ouvrir **Hub Insights**, puis cliquez sur **Espaces de travail log Analytics**.

[![Capture d’écran montrant les sélections permettant d’ouvrir l’Insights Hub.](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Utilisez les zones de liste déroulante en haut de l’écran pour modifier l’espace de travail ou l’intervalle de temps utilisé pour les vignettes. Sélectionnez la vignette d’une solution pour ouvrir la vue correspondante incluant une analyse plus détaillée des données collectées par la solution.

[![Capture d’écran montrant les statistiques relatives aux solutions de surveillance du Portail Azure.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Les solutions de surveillance peuvent contenir plusieurs types de ressources Azure. Vous pouvez visualiser les ressources incluses dans une solution comme n'importe quelle autre ressource. Par exemple, toutes les requêtes de journal incluses dans la solution sont listées sous **Requêtes de solution** dans l’[Explorateur de requêtes](../logs/log-analytics-tutorial.md). Vous pouvez utiliser ces requêtes quand vous effectuez une analyse ad hoc avec des [requêtes de journal](../logs/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lister les solutions de monitoring installées

### <a name="portal"></a>[Portail](#tab/portal)

Pour lister les solutions de monitoring installées dans votre abonnement :

1. Accédez au [portail Azure](https://ms.portal.azure.com). Recherchez et sélectionnez **Solutions**.

   Vous obtenez la liste des solutions installées dans l’ensemble de vos espaces de travail. Le nom de la solution est suivi du nom de l’espace de travail dans lequel elle est installée.
1. Utilisez les zones de liste déroulante en haut de l’écran pour filtrer les solutions par abonnement ou par groupe de ressources.

![Capture d'écran affichant la liste des solutions.](media/solutions/list-solutions-all.png)

Sélectionnez le nom d’une solution pour ouvrir la page de résumé de cette dernière. Cette page affiche toutes les vues incluses dans la solution et fournit différentes options pour la solution proprement dite et pour son espace de travail.

![Capture d’écran qui affiche des informations récapitulatives pour une solution.](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) pour lister les solutions de supervision installées dans votre abonnement. Avant d’exécuter la commande , assurez-vous que les prérequis indiqués dans la section [Installer une solution de supervision](#install-a-monitoring-solution) sont respectés.

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utilisez l’applet de commande [AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) pour répertorier les solutions de surveillance installées dans votre abonnement. Avant d’exécuter la cmdlet, assurez-vous que les prérequis indiqués dans la section [Installer une solution de supervision](#install-a-monitoring-solution) sont respectés.

```azurepowershell-interactive
# List all Log Analytics solutions in the current subscription
Get-AzMonitorLogAnalyticsSolution

# List all Log Analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all Log Analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Installer une solution de supervision

### <a name="portal"></a>[Portail](#tab/portal)

Les solutions de monitoring de Microsoft et de ses partenaires sont disponibles sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Vous pouvez rechercher des solutions disponibles et les installer à l’aide de la procédure suivante. Lorsque vous installez une solution, vous devez sélectionner un [espace de travail Log Analytics](../logs/manage-access.md) dans lequel elles seront installées et où leurs données seront collectées.

1. Dans la [liste des solutions de votre abonnement](#list-installed-monitoring-solutions), cliquez sur **Ajouter**.
1. Parcourez ou recherchez une solution. Vous pouvez également utiliser [ce lien de recherche](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Recherchez la solution de supervision qui vous intéresse et lisez sa description.
1. Cliquez sur **Créer** pour démarrer le processus d’installation.
1. Une invite s’affiche, spécifiez l’espace de travail Log Analytics et fournissez la configuration exigée pour la solution.

![Capture d’écran qui montre les solutions sur la Place de marché Azure.](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installer une solution proposée par la communauté

Les membres de la communauté peuvent soumettre des solutions de gestion aux modèles de démarrage rapide Azure. Vous pouvez installer ces solutions directement ou en télécharger les modèles à des fins d’installation ultérieure.

1. Suivez la procédure décrite dans [Espace de travail Log Analytics et compte Automation](#log-analytics-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Accédez à [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).
3. Recherchez une solution qui vous intéresse.
4. Sélectionnez la solution dans les résultats pour en afficher les détails.
5. Sélectionnez le bouton **Déployer sur Azure**.
6. Vous êtes invité à fournir des informations comme le groupe de ressources et l’emplacement, en plus des valeurs des paramètres de la solution.
7. Cliquez sur **Achat** pour installer la solution.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Préparation de votre environnement

1. Installez Azure CLI.

   Vous devez [installer l’interface Azure CLI](/cli/azure/install-azure-cli) avant d’exécuter les commandes de référence CLI. Si vous préférez, vous pouvez également utiliser Azure Cloud Shell pour effectuer les étapes décrites dans cet article. Azure Cloud Shell est un environnement d’interpréteur de commandes interactif que vous utilisez dans votre navigateur. Démarrez Cloud Shell à l’aide de l’une des méthodes suivantes :

   - Accédez à [la page web Cloud Shell](https://shell.azure.com).

   - Sélectionnez le bouton [Cloud Shell](https://portal.azure.com) dans la barre de menus en haut à droite du **portail Azure**. 

1. Connectez-vous.

   Si vous utilisez une installation locale de l’interface CLI, connectez-vous à l’aide de la commande [az login](/cli/azure/reference-index#az_login).  Suivez les étapes affichées dans votre terminal pour effectuer le processus d’authentification.

    ```azurecli
    az login
    ```

1. Installer l’extension `log-analytics-solution`.

   La commande `log-analytics-solution` est une extension expérimentale de l’interface Azure CLI de base. Découvrez-en plus sur les références des extensions dans [Utiliser des extensions avec Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   L’avertissement suivant doit s’afficher.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Installer une solution avec l’interface Azure CLI

Lorsque vous installez une solution, vous devez sélectionner un [espace de travail Log Analytics](../logs/manage-access.md) dans lequel elles seront installées et où leurs données seront collectées.  Avec l’interface Azure CLI, vous gérez les espaces de travail à l’aide des commandes de référence [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace).  Suivez la procédure décrite dans [Espace de travail Log Analytics et compte Automation](#log-analytics-workspace-and-automation-account) pour lier un compte et un espace de travail.

Utilisez la commande [az monitor log-analytics solution create](/cli/azure/monitor/log-analytics/solution) pour installer une solution de supervision.  Les paramètres entre crochets sont facultatifs.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Voici un exemple de code qui crée une solution Log Analytics pour le produit de plan d’OMSGallery/Containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Préparation de votre environnement

1. Installez Azure PowerShell.

   Vous devez [installer Azure PowerShell](/powershell/azure/install-az-ps) avant d’exécuter des commandes de référence Azure PowerShell. Si vous préférez, vous pouvez également utiliser Azure Cloud Shell pour effectuer les étapes décrites dans cet article. Azure Cloud Shell est un environnement d’interpréteur de commandes interactif que vous utilisez dans votre navigateur. Démarrez Cloud Shell à l’aide de l’une des méthodes suivantes :

   - Accédez à [la page web Cloud Shell](https://shell.azure.com).

   - Sélectionnez le bouton [Cloud Shell](https://portal.azure.com) dans la barre de menus en haut à droite du **portail Azure**.

   > [!IMPORTANT]
   > Tant que le module PowerShell **Az.MonitoringSolutions** est en préversion, vous devez l’installer séparément à l’aide de la cmdlet `Install-Module`. Une fois que ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module PowerShell Az et sera disponible par défaut dans Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Connectez-vous.

   Si vous utilisez une installation locale de PowerShell, connectez-vous à l’aide de l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Suivez les étapes affichées PowerShell pour effectuer le processus d’authentification.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Installer une solution avec Azure PowerShell

Lorsque vous installez une solution, vous devez sélectionner un [espace de travail Log Analytics](../logs/manage-access.md) dans lequel elles seront installées et où leurs données seront collectées. Avec Azure PowerShell, vous gérez les espaces de travail à l’aide des applets de commande du module PowerShell [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions). Suivez la procédure décrite dans [Espace de travail Log Analytics et compte Automation](#log-analytics-workspace-and-automation-account) pour lier un compte et un espace de travail.

Utilisez l’applet de commande [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) pour installer une solution de surveillance. Les paramètres entre crochets sont facultatifs.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

L’exemple suivant crée une solution de supervision pour l’espace de travail Log Analytics.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Espace de travail Log Analytics et compte Automation

Toutes les solutions de supervision ont besoin d’un [espace de travail Log Analytics](../logs/manage-access.md) pour stocker les données collectées et héberger leurs recherches dans les journaux, ainsi que leurs vues. Certaines solutions nécessitent également un [compte Automation](../../automation/automation-security-overview.md) destiné à contenir les runbooks et les ressources associées. L’espace de travail et le compte doivent répondre aux exigences suivantes :

* Chaque installation d’une solution ne peut utiliser qu’un seul espace de travail Log Analytics et un seul compte Automation. Vous pouvez installer la solution séparément dans plusieurs espaces de travail.
* Si une solution nécessite un compte Automation, l’espace de travail Log Analytics et le compte Automation doivent être liés l’un à l’autre. Un espace de travail Log Analytics ne peut être lié qu’à un seul compte Automation, et un compte Automation ne peut être lié qu’à un seul espace de travail Log Analytics.

Quand vous installez une solution par le biais de Place de marché Azure, vous êtes invité à choisir un espace de travail et un compte Automation. Un lien est automatiquement créé entre les deux s’il n’existe pas déjà.

Vérification du lien entre un espace de travail Log Analytics et un compte Automation.

1. Sélectionnez le compte Automation dans le Portail Azure.
1. Faites défiler jusqu’à la section **Ressources associées** du menu, puis sélectionnez **Espace de travail lié**.
1. Si l’espace de travail est lié à un compte Automation, cette page répertorie l’espace de travail auquel il est lié. Si vous sélectionnez le nom de l’espace de travail répertorié, vous êtes redirigé vers la page de vue d’ensemble de cet espace de travail.

## <a name="remove-a-monitoring-solution"></a>Supprimer une solution de supervision

### <a name="portal"></a>[Portail](#tab/portal)

Pour supprimer une solution installée à l’aide du portail, recherchez-la dans la [liste des solutions installées](#list-installed-monitoring-solutions). Sélectionnez le nom de la solution pour ouvrir la page de résumé correspondante, puis sélectionnez **Supprimer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour supprimer une solution installée à l’aide de l’interface Azure CLI, utilisez la commande [az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete).

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pour supprimer une solution installée à l’aide d’Azure PowerShell, utilisez la cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution).

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Étapes suivantes

* Accédez à la [liste des solutions de monitoring de Microsoft](../monitor-reference.md).
* Apprenez à [créer des requêtes](../logs/log-query-overview.md) pour analyser les données collectées par les solutions de monitoring.
* Consultez toutes les [commandes Azure CLI pour Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).
