---
title: Connecter des machines hybrides à Azure à l’aide de PowerShell
description: Cet article explique comment installer l’agent et connecter une machine à Azure à l’aide de serveurs compatibles avec Azure Arc. Vous pouvez pour cela utiliser PowerShell.
ms.date: 07/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1ca9528af5b529dd844e566905b6aa7f92429d2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122527738"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Connecter des machines hybrides à Azure à l’aide de PowerShell

Pour les serveurs compatibles avec Azure Arc, vous pouvez effectuer une procédure manuelle afin de les rendre compatibles avec au moins une machine Windows ou Linux dans votre environnement. Vous pouvez également utiliser la cmdlet PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) pour télécharger l’agent Connected Machine, installer l’agent et inscrire l’ordinateur auprès d’Azure Arc. La cmdlet télécharge le package de l’agent Windows (Windows Installer) à partir du centre de téléchargement de Microsoft et le package de l’agent Linux à partir du référentiel de packages de Microsoft.

Cette méthode nécessite que vous disposiez d’autorisations d’administrateur sur la machine pour installer et configurer l’agent. Sur Linux, vous utilisez le compte root et, sur Windows, vous êtes membre du groupe Administrateurs local. Vous pouvez effectuer ce processus de manière interactive ou à distance sur un serveur Windows à l’aide de la [communication à distance PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Avant de commencer, vérifiez les [conditions préalables](agent-overview.md#prerequisites) et vérifiez que votre abonnement et vos ressources répondent aux exigences. Pour plus d'informations sur les régions prises en charge et d'autres considérations connexes, consultez [Régions Azure prises en charge](overview.md#supported-regions).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Machine avec Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/).

PowerShell permet de gérer les extensions de machine virtuelle sur vos serveurs hybrides gérés par des serveurs compatibles avec Azure Arc. Avant d’utiliser PowerShell, installez le module `Az.ConnectedMachine`. Exécutez la commande suivante sur votre serveur compatible avec Azure Arc :

```powershell
Install-Module -Name Az.ConnectedMachine
```

Une fois l’installation terminée, le message suivant s’affiche :

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installer l’agent et le connecter à Azure

1. Ouvrez une console PowerShell avec des privilèges élevés.

2. Connectez-vous à Azure en exécutant la commande `Connect-AzAccount`.

3. Pour installer l’agent Connected Machine, utilisez `Connect-AzConnectedMachine` avec les paramètres `-Name`, `-ResourceGroupName`et `-Location`. Utilisez le paramètre `-SubscriptionId` pour remplacer l’abonnement par défaut à la suite du contexte Azure créé après la connexion. Exécutez l’une des commandes suivantes :

    * Pour installer l’agent Connected Machine sur la machine cible pouvant directement communiquer avec Azure, exécutez :

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Pour installer l’agent Connected Machine sur la machine cible communiquant directement via un serveur proxy, exécutez :
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

      À l’aide de cette configuration, l’agent communique par le biais du serveur proxy à l’aide du protocole HTTP.

Si le démarrage de l’agent échoue une fois l’installation terminée, recherchez des informations détaillées sur l’erreur dans les journaux. Sous Windows, consultez ce fichier : *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Sous Linux, consultez ce fichier : */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installer et se connecter à l’aide de la communication à distance PowerShell

Voici la procédure permettant de configurer au moins un serveur Windows avec des serveurs compatibles avec Azure Arc. Vous devez activer la communication à distance PowerShell sur chaque ordinateur distant. Pour ce faire, utilisez l’applet de commande `Enable-PSRemoting`.

1. Ouvrez une console PowerShell en tant qu’administrateur.

2. Connectez-vous à Azure en exécutant la commande `Connect-AzAccount`.

3. Pour installer l’agent Connected Machine, utilisez `Connect-AzConnectedMachine` avec les paramètres `-ResourceGroupName` et `-Location`. Les noms des ressources Azure utilisent automatiquement le nom d’hôte de chaque serveur. Utilisez le paramètre `-SubscriptionId` pour remplacer l’abonnement par défaut à la suite du contexte Azure créé après la connexion.

    * Pour installer l’agent Connected Machine sur la machine cible pouvant directement communiquer avec Azure, exécutez la commande suivante :
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Pour installer l’agent Connected Machine sur plusieurs ordinateurs distants en même temps, ajoutez la liste des noms d’ordinateurs distants, séparés par une virgule.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    L’exemple suivant illustre les résultats de la commande ciblant un seul ordinateur :
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Vérifier la connexion avec Azure Arc

Après avoir installé et configuré l’agent de manière à inscrire les serveurs compatibles avec Azure Arc, accédez au portail Azure pour vérifier que le serveur a déjà été correctement connecté. Affichez vos machines dans le [portail Azure](https://portal.azure.com).

![Capture d’écran du tableau de bord des serveurs, montrant une connexion serveur réussie.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

* Si nécessaire, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).

* Consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md) pour planifier le déploiement de serveurs avec Azure Arc à n’importe quelle échelle et implémenter la gestion et la supervision centralisées.

* Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md). Vous pouvez utiliser une [configuration invité](../../governance/policy/concepts/guest-configuration.md) de machine virtuelle, vérifier que l’ordinateur fait ses rapports auprès de l’espace de travail Log Analytics attendu et activer la surveillance avec [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md).
