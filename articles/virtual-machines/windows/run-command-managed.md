---
title: Exécuter des scripts sur une machine virtuelle Windows dans Azure à l’aide de commandes d’exécution managées (préversion)
description: Cette rubrique explique comment exécuter des scripts au sein d’une machine virtuelle Windows Azure à l’aide de la fonctionnalité mise à jour Run Command.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: cynthn
ms.author: cynthn
ms.date: 10/28/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a1617fea3ea7de71a040852f4906ecffe1b0432
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557886"
---
# <a name="preview-run-scripts-in-your-windows-vm-by-using-managed-run-commands"></a>Aperçu : Exécuter des scripts dans votre machine virtuelle Windows à l’aide de commandes d’exécution managées

**S’applique à :** :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles 

> [!IMPORTANT]
> La fonctionnalité **Run Command managée** est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité Run Command utilise l’agent de machine virtuelle pour exécuter des scripts au sein d’une machine virtuelle Windows Azure. Vous pouvez utiliser ces scripts pour la gestion générale des ordinateurs ou des applications. Ils peuvent vous aider à diagnostiquer et corriger rapidement les problèmes de réseau et d’accès aux machines virtuelles et à rétablir l’état de la machine virtuelle.

La commande Run Command managée *mise à jour* utilise le même canal d’agent de machine virtuelle pour exécuter les scripts et fournit les améliorations suivantes par rapport à la [commande Run Command orientée vers l’action d’origine](run-command.md) : 
- Prise en charge des commandes Run Command mises à jour via le modèle de déploiement ARM 
- Exécution parallèle de plusieurs scripts 
- Exécution séquentielle de scripts 
- Le script RunCommand peut être annulé  
- Délai d’expiration du script spécifié par l’utilisateur 
- Prise en charge des scripts de longue durée (heures/jours) 
- Transmission de secrets (paramètres, mots de passe) de manière sécurisée


## <a name="register-for-preview"></a>S’inscrire pour la préversion

Vous devez inscrire votre abonnement pour pouvoir utiliser la commande Run Command managée pendant la phase de préversion publique. Accédez à [Configurer les fonctionnalités en version préliminaire dans l’abonnement Azure](../../azure-resource-manager/management/preview-features.md) pour obtenir des instructions d’inscription et utilisez le nom de la fonctionnalité `RunCommandPreview`.

## <a name="azure-cli"></a>Azure CLI 

Les exemples suivants utilisent la commande [az vm run-command](/cli/azure/vm/run-command) pour exécuter un script d’environnement d’exécution sur une machine virtuelle Windows Azure.

### <a name="execute-a-script-with-the-vm"></a>Exécuter un script avec la machine virtuelle
Cette commande remet le script à la machine virtuelle, l’exécute et retourne la sortie capturée.

```azurecli-interactive
az vm run-command create --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" --script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Répertorier toutes les ressources RunCommand déployées sur une machine virtuelle 
Cette commande retourne une liste complète des commandes Run Command précédemment déployées, ainsi que leurs propriétés. 

```azurecli-interactive
az vm run-command list --name "myVM" --resource-group "myRG"
```

### <a name="get-execution-status-and-results"></a>Obtenir l’état et les résultats de l’exécution 
Cette commande permet de récupérer la progression de l’exécution actuelle, y compris la dernière sortie, l’heure de début/fin, le code de sortie et l’état terminal de l’exécution.

```azurecli-interactive
az vm run-command show --name "myRunCommand" --vm-name "myVM" --resource-group "myRG" –expand
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Supprimer la ressource RunCommand de la machine virtuelle
Supprimez la ressource RunCommand précédemment déployée sur la machine virtuelle. Si l’exécution du script est toujours en cours, l’exécution se termine. 

```azurecli-interactive
az vm run-command delete --name "myRunCommand" --vm-name "myVM" --resource-group "myRG"
```


## <a name="powershell"></a>PowerShell 

### <a name="execute-a-script-with-the-vm"></a>Exécuter un script avec la machine virtuelle
Cette commande remet le script à la machine virtuelle, l’exécute et retourne la sortie capturée.

```powershell-interactive
Set-AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" – Script "Write-Host Hello World!"
```

### <a name="list-all-deployed-runcommand-resources-on-a-vm"></a>Répertorier toutes les ressources RunCommand déployées sur une machine virtuelle 
Cette commande retourne une liste complète des commandes Run Command précédemment déployées, ainsi que leurs propriétés.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM"
```

### <a name="get-execution-status-and-results"></a>Obtenir l’état et les résultats de l’exécution 
Cette commande permet de récupérer la progression de l’exécution actuelle, y compris la dernière sortie, l’heure de début/fin, le code de sortie et l’état terminal de l’exécution.

```powershell-interactive
Get-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName" -Status
```

### <a name="delete-runcommand-resource-from-the-vm"></a>Supprimer la ressource RunCommand de la machine virtuelle
Supprimez la ressource RunCommand précédemment déployée sur la machine virtuelle. Si l’exécution du script est toujours en cours, l’exécution se termine. 

```powershell-interactive
Remove-AzVMRunCommand AzVMRunCommand -ResourceGroupName "myRG" -VMName "myVM" -Name "RunCommandName"
```
 

## <a name="rest-api"></a>API REST 

Pour déployer une nouvelle commande Run Command, exécutez une commande PUT directement sur la machine virtuelle et spécifiez un nom unique pour l’instance Run Command. 

```rest
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01
```

```json
{ 
"location": "<location>", 
"properties": { 
    "source": { 
        "script": "Write-Host Hello World!", 
        "scriptUri": "<URI>",  
        "commandId": "<Id>"  
        }, 
    "parameters": [ 
        { 
            "name": "param1",
            "value": "value1" 
            }, 
        { 
            "name": "param2", 
            "value": "value2" 
            } 
        ], 
    "protectedParameters": [ 
        { 
            "name": "secret1", 
            "value": "value1" 
            }, 
        { 
            "name": "secret2", 
            "value": "value2" 
            } 
        ], 
    "runAsUser": "userName",
    "runAsPassword": "userPassword", 
    "timeoutInSeconds": 3600, 
    "outputBlobUri": "<URI>", 
    "errorBlobUri": "<URI>"  
    }
}
```

### <a name="notes"></a>Notes
 
- Vous pouvez fournir un script inline, un URI de script ou un [ID de commande](run-command.md#available-commands) de script intégré comme source d’entrée
- Un seul type d’entrée source est pris en charge pour une exécution de commande 
- La commande Run Command prend en charge la sortie vers le Stockage d’objets blob, qui peuvent être utilisés pour stocker des sorties de script volumineuses
- La commande Run Command prend en charge la sortie d’erreur vers le Stockage d’objets blob 


### <a name="list-running-instances-of-run-command-on-a-vm"></a>Répertorier les instances en cours d’exécution de la commande Run Command sur une machine virtuelle 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands?api-version=2019-12-01
``` 

### <a name="get-output-details-for-a-specific-run-command-deployment"></a>Obtenir les détails de sortie d’un déploiement Run Command spécifique 

```rest
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?$expand=instanceView&api-version=2019-12-01 
```

### <a name="cancel-a-specific-run-command-deployment"></a>Annuler un déploiement Run Command spécifique 

Pour annuler un déploiement en cours d’exécution, vous pouvez exécuter PUT ou PATCH sur l’instance Run Command en cours d’exécution et spécifier un script vide dans le corps de la requête. L’exécution en cours sera annulée. 

Vous pouvez également supprimer l’instance Run Command.  

```rest
DELETE /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/runcommands/<runCommandName>?api-version=2019-12-01 
```

### <a name="deploy-scripts-in-an-ordered-sequence"></a>Déployer des scripts dans une séquence ordonnée 

Pour déployer des scripts de manière séquentielle, utilisez un modèle de déploiement, en spécifiant une relation `dependsOn` entre les scripts séquentiels. 

```json
{ 
    "type": "Microsoft.Compute/virtualMachines/runCommands", 
    "name": "secondRunCommand", 
    "apiVersion": "2019-12-01", 
    "location": "[parameters('location')]", 
    "dependsOn": <full resourceID of the previous other Run Command>, 
    "properties": { 
        "source": {  
            "script": "Write-Host Hello World!"  
        }, 
        "timeoutInSeconds": 60  
    }
} 
```

### <a name="execute-multiple-run-commands-sequentially"></a>Exécuter plusieurs commandes Run Command de manière séquentielle 

Par défaut, si vous déployez plusieurs ressources RunCommand à l’aide du modèle de déploiement, elles sont exécutées simultanément sur la machine virtuelle. Si vous avez une dépendance sur les scripts et un ordre d’exécution par défaut, vous pouvez utiliser la propriété `dependsOn` pour les faire s’exécuter de manière séquentielle. 

Dans cet exemple, **secondRunCommand** s’exécute après **firstRunCommand**. 

```json
{
   "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "resources":[
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/firstRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
         ],
         "properties":{
            "source":{
               "script":"Write-Host First: Hello World!"
            },
            "parameters":[
               {
                  "name":"param1",
                  "value":"value1"
               },
               {
                  "name":"param2",
                  "value":"value2"
               }
            ],
            "timeoutInSeconds":20
         }
      },
      {
         "type":"Microsoft.Compute/virtualMachines/runCommands",
         "name":"[concat(parameters('vmName'),'/secondRunCommand')]",
         "apiVersion":"2019-12-01",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'),'runcommands/firstRunCommand')]"
         ],
         "properties":{
            "source":{
               "scriptUrl":"http://github.com/myscript.ps1"
            },
            "timeoutInSeconds":60
         }
      }
   ]
}
```


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les autres méthodes pour exécuter à distance des commandes et des scripts dans votre machine virtuelle, consultez [Exécuter des scripts dans votre machine virtuelle Windows](run-scripts-in-vm.md).
