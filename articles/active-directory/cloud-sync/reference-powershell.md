---
title: Module PowerShell AADCloudSyncTools pour la synchronisation cloud Azure AD Connect
description: Cet article explique comment installer l’agent d’approvisionnement Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/03/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d45c331c8af6dda32b7a47dbdb517adf1d8a4d93
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500295"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Module PowerShell AADCloudSyncTools pour la synchronisation cloud Azure AD Connect

Le module AADCloudSyncTools fournit une panoplie d’outils pratiques pour gérer vos déploiements de synchronisation cloud Azure AD Connect.

## <a name="prerequisites"></a>Prérequis
Les prérequis suivants sont obligatoires :

- Tous les composants requis pour ce module peuvent être installés automatiquement à l’aide de `Install-AADCloudSyncToolsPrerequisites`
- Ce module utilise l’authentification MSAL, donc le module MSAL.PS doit être installé. Pour vérifier cela, dans une fenêtre PowerShell, exécutez la commande `Get-module MSAL.PS -ListAvailable`. Si le module est correctement installé, vous obtenez une réponse. Vous pouvez utiliser `Install-AADCloudSyncToolsPrerequisites` pour installer la version la plus récente de MSAL.PS.
- Bien que le module Azure AD PowerShell ne soit requis pour aucune fonctionnalité de ce module, il est utile d’en disposer de façon à ce qu’il soit également installé automatiquement lors de l’utilisation de la commande `Install-AADCloudSyncToolsPrerequisites`. 
- L’installation de modules à partir de PowerShell Gallery requiert l’application du protocole TLS 1.2. La cmdlet `Install-AADCloudSyncToolsPrerequisites` définit l’application du protocole TLS 1.2 avant l’installation des éléments prérequis. Pour vérifier que vous pouvez installer les modules manuellement, définissez au préalable les éléments suivants dans la session PowerShell à l’aide de la commande `Install-Module` :
  ```
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installer le module PowerShell AADCloudSyncTools
Pour installer et utiliser le module AADCloudSyncTools, procédez comme suit :

1. Ouvrez Windows PowerShell avec des privilèges d’administrateur.
2. Tapez ou copiez-collez le code suivant : `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Appuyez sur Entrée.
4. Pour vérifier que le module a été importé, entrez ou copiez-collez la commande suivante : `Get-module AADCloudSyncTools`
5. Vous devez maintenant voir des informations sur le module.
6. Ensuite, pour installer les composants requis pour le module AADCloudSyncTools, exécutez la commande suivante : `Install-AADCloudSyncToolsPrerequisites`
7. Lors de la première exécution, le module PoweShellGet est installé s’il n’est pas présent. Pour charger le nouveau module PowershellGet, fermez la fenêtre PowerShell et ouvrez une nouvelle session PowerShell avec des privilèges administratifs. 
8. Importez de nouveau le module suivant l’étape 2.
9. Exécutez `Install-AADCloudSyncToolsPrerequisites` pour installer les modules MSAL et AzureAD.
11. Toutes les éléments prérequis doivent être correctement installés ![Installer le module](media/reference-powershell/install-1.png)
12. Chaque fois que vous souhaitez utiliser le module AADCloudSyncTools dans une nouvelle session PowerShell, entrez ou copiez et collez les éléments suivants :
```
Import-module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
```


## <a name="aadcloudsynctools--cmdlets"></a>Applets de commande AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Utilise le module MSAL.PS pour demander un jeton permettant à l’administrateur Azure AD d’accéder à Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporte et empaquette toutes les données de dépannage dans un fichier compressé, comme suit :
 1. Définit le suivi détaillé et démarre la collecte des données à partir de l’agent d’approvisionnement (identique à `Start-AADCloudSyncToolsVerboseLogs`)
 <br>Vous pouvez trouver ces journaux de suivi dans le dossier `C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace` </br>
 2. Arrête la collecte de données après 3 minutes et désactive le suivi détaillé (identique à `Stop-AADCloudSyncToolsVerboseLogs`)
 <br>Vous pouvez spécifier une durée différente avec la commande `-TracingDurationMins` ou ignorer totalement le suivi détaillé avec la commande `-SkipVerboseTrace` </br>
 3. Collecte les journaux de l’Observateur d’événements des dernières 24 heures.
 4. Compresse l’ensemble des journaux de l’agent, des journaux détaillés et des journaux de l’observateur d’événements dans un fichier zip dans le dossier Documents de l’utilisateur.
 <br>Vous pouvez spécifier un dossier de sortie différent avec la commande `-OutputPath <folder path>` </br>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Affiche les détails des locataires Azure AD et l’état des variables internes.

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne les informations de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne la planification de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne le schéma de la tâche de synchronisation.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Utilise Graph pour obtenir le schéma de la tâche de synchronisation pour l’ID de tâche de synchronisation fourni et fournit en sortie toutes les étendues du groupe de filtres.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne les paramètres de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne le statut de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Utilise Graph pour obtenir le ou les principaux de service pour AD2AAD et/ou SyncFabric.
Sans paramètres, retourne uniquement le ou les principaux de service AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Vérifie la présence de PowerShellGet v2.2.4.1 ou version ultérieure et des modules Azure AD et MSAL.PS, et installe ces derniers s’ils sont manquants.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Appelle une requête web pour l’URI, la méthode et le corps spécifiés en tant que paramètres

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Utilise Azure AD PowerShell pour supprimer le compte actuel (le cas échéant) et réinitialise l’authentification du compte de synchronisation avec un nouveau compte de synchronisation dans Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Redémarre une synchronisation complète.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Poursuit la synchronisation à partir du filigrane précédent.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modifie le fichier 'AADConnectProvisioningAgent.exe.config' pour activer le suivi détaillé et redémarre le service AADConnectProvisioningAgent. Vous pouvez utiliser -SkipServiceRestart pour empêcher le redémarrage du service, mais aucune modification de configuration ne prendra effet.  Vous pouvez trouver ces journaux de suivi dans le dossier C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modifie le fichier 'AADConnectProvisioningAgent.exe.config' pour désactiver le suivi détaillé et redémarre le service AADConnectProvisioningAgent. Vous pouvez utiliser -SkipServiceRestart pour empêcher le redémarrage du service, mais aucune modification de configuration ne prendra effet.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Suspend la synchronisation.

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
