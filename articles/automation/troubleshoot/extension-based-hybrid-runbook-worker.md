---
title: Résoudre les problèmes de Runbook Worker hybride basés sur une extension dans Azure Automation
description: Cet article explique comment détecter et résoudre des problèmes liés aux Runbook Workers hybrides basés sur un extension d’Azure Automation.
services: automation
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cdb8f63c16c9479d27bcb44147d78d1a8e4a7aa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489886"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>Résoudre les problèmes de Runbook Worker hybride basés sur une extension de machine virtuelle dans Automation

Cet article fournit des informations sur la détection et la résolution de problèmes liés aux Runbook Workers hybrides basés sur une extension d’Azure Automation. Pour la résolution des problèmes des Workers basés sur un agent, consultez [Résoudre les problèmes de Runbook Workers hybrides basés sur un agent dans Automation](./hybrid-runbook-worker.md). Pour obtenir des informations générales, consultez [Vue d’ensemble des Runbook Worker hybrides](../automation-hybrid-runbook-worker.md).

## <a name="general-checklist"></a>Liste de contrôle générale

Pour aider à résoudre les problèmes liés aux Runbook Workers hybrides basés sur une extension :

- Vérifiez que le système d’exploitation est pris en charge et que les conditions préalables sont remplies.Consultez les [Conditions préalables](../extension-based-hybrid-runbook-worker-install.md#prerequisites).

- Vérifiez si l’identité managée affectée par le système est activée sur la machine virtuelle. Les machines virtuelles Azure et les machines Azure compatibles avec Arc doivent être activées avec une identité managée affectée par le système.

- Vérifiez si l’extension est activée avec les paramètres appropriés.Le fichier de paramètres doit avoir la valeur  `AutomationAccountURL` appropriée.Effectuez une vérification croisée de l’URL avec la propriété du compte Automation -  `AutomationHybridServiceUrl`.  
  - Pour Windows : vous trouverez le fichier de paramètres à l’adresse  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin`.
  - Pour Linux : vous trouverez le fichier de paramètres à l’adresse  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/`.

- Vérifiez le message d’erreur indiqué dans l’état de l’extension Worker hybride/état détaillé.Il contient le(s) message(s) d’erreur et les recommandations respectives pour résoudre le problème.

- Exécutez l’outil de résolution des problèmes sur la machine virtuelle pour générer un fichier de sortie. Ouvrez le fichier de sortie et vérifiez les erreurs identifiées par l’outil de résolution des problèmes.
  - Pour Windows : vous trouverez l’utilitaire de résolution des problèmes à l’adresse `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1`.
  - Pour Linux : vous trouverez l’utilitaire de résolution des problèmes à l’adresse `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py`.

- Pour les ordinateurs Linux, vérifiez que l’utilisateur `hweautomation` est configuré avec les autorisations appropriées.  

- Vérifiez si le processus Worker hybride est en cours d’exécution.
   - Pour Windows : vérifiez le service  `Hybrid Worker Service`.
   - Pour Linux : vérifiez le service  `hwd.`.

- Exécutez l’outil collecteur de journaux et passez en revue les journaux collectés.
   - Pour Windows : les journaux se trouvent dans `C:\HybridWorkerExtensionLogs`. L’outil est disponible ici : `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1`. Le journal de l’extension est `HybridWorkerExtensionHandler.log`. Le journal Worker est `SME.log`.
   - Pour Linux : les journaux se trouvent dans `/home/nxautomation/run`. L’outil est disponible ici : `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py`. Le journal Worker est `worker.log`. Le journal d’inscription de l’extension est `register_log`. Le journal de démarrage de l’extension est `startup_log`. Le journal de l’extension est `extension_out`.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas ici ou que vous ne pouvez pas le résoudre, utilisez l’un des canaux suivants pour obtenir une aide supplémentaire :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Le Support Azure fournit à la communauté Azure des réponses, un support technique et des experts.
* Signaler un incident au support Azure Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir de l’aide**.