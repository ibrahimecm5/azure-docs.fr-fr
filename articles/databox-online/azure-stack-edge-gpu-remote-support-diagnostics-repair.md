---
title: Activer le support, les diagnostics et la correction à distance pour les appareils Azure Stack Edge
description: Décrit l’activation du support à distance sur vos appareils Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 364845dc046664a7af4d9f7ac6fbb965a818430a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098201"
---
# <a name="remote-support-and-diagnostics-for-azure-stack-edge"></a>Support et diagnostics à distance pour Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]


> [!IMPORTANT]
> Le support à distance est en préversion publique. Il s’applique à Azure Stack Edge version 2110 ou ultérieure.

Sur votre appareil Azure Stack Edge, vous pouvez activer le support à distance pour permettre aux ingénieurs Microsoft de diagnostiquer et de corriger les problèmes en accédant à distance à l’appareil. Quand vous activez cette fonctionnalité, vous donnez votre consentement sur le niveau d’accès et la durée de l’accès. 

Cet article décrit la fonctionnalité de support à distance, notamment quand l’utiliser et comment l’activer. Il fournit également une liste des opérations autorisées que les ingénieurs Microsoft peuvent exécuter à distance sur votre appareil.


## <a name="about-remote-support"></a>À propos du support à distance

Si vous rencontrez un problème avec Azure Stack Edge, vous devez généralement collecter un package de support et le fournir au Support Microsoft. Dans certains cas, les journaux ne contiennent pas suffisamment d’informations pour diagnostiquer et corriger le problème. Le Support Microsoft vous contacte ensuite afin d’obtenir votre consentement pour un support à distance.

Voici quelques avantages du support à distance :

- Vous pouvez résoudre le problème plus rapidement, car le Support Microsoft n’a pas besoin d’organiser une rencontre physique.
- Vous pouvez voir la transcription de la session de support à distance, notamment toutes les opérations exécutées sur l’appareil.
- Vous avez le contrôle total de vos données, et vous pouvez révoquer votre consentement à tout moment. Si vous oubliez de quitter la session, l’accès à celle-ci est automatiquement désactivé une fois le délai expiré.

## <a name="how-remote-support-works"></a>Fonctionnement du support à distance

L’illustration suivante montre le fonctionnement du support à distance.

![Illustration du support à distance sur Azure Stack Edge](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/remote-support-flow-1.png)

1. Vous donnez votre consentement pour le support à distance de votre appareil via le portail Azure. Vous pouvez également configurer le niveau et la durée de l’accès à votre appareil.
1. Le consentement et la demande de support à distance sont envoyés du portail Azure au fournisseur de ressources Azure Stack Edge, qui à son tour les envoie à l’appareil. L’agent Smart Device demande au fournisseur de ressources les informations d’identification de connexion hybride.
1. Les informations d’identification de connexion hybride sont créées et utilisées pour établir une connexion hybride Azure. Cette connexion :
    
    - Est spécifique à l’appareil. Chaque appareil a sa propre connexion, et celle-ci n’est pas partagée.
    - Permet au Support Microsoft d’avoir un accès JIT (juste-à-temps) à l’appareil via un canal sécurisé, audité et conforme.  
    - Cette connexion utilise un protocole *https* sur le port 443, et le trafic est chiffré avec TLS 1.2.
     
1. L’agent Smart Device commence à écouter sur cette connexion hybride toutes les requêtes provenant de l’interface du navigateur pour le Centre d’aide et de support Azure. 
1. Le navigateur demande l’accès à la connexion hybride, puis la requête est envoyée à l’appareil pour l’ouverture d’une session PowerShell restreinte. Si le consentement existe, la requête est acceptée. Si l’appareil ne dispose pas du consentement nécessaire, il rejette simplement cette connexion. 

Une fois la connexion établie, toutes les communications ont lieu via cette connexion sécurisée. 

Les opérations que le Support Microsoft peut effectuer via cette connexion sont *restreintes* en fonction du niveau d’accès octroyé à l’aide de la fonctionnalité [JEA](/powershell/scripting/learn/remoting/jea/overview) (Just Enough Administration). Pour plus d’informations sur les applets de commande que le Support Microsoft peut exécuter durant une session de support à distance, consultez la [liste des opérations autorisées du Support Microsoft](#operations-allowed-in-remote-support) dans cet article.


## <a name="enable-remote-support"></a>Activer le support à distance

Pour configurer le support à distance de votre appareil Azure Stack Edge, suivez ces étapes :

1. Dans le portail Azure, accédez à la ressource Azure Stack Edge de votre appareil, puis accédez à **Diagnostic**.
1. Par défaut, Microsoft n’a pas accès à distance à votre appareil. L’état du support à distance indique **Non activé**. Pour activer cette fonctionnalité, sélectionnez **Configurer le support à distance**.

    ![Capture d’écran de l’activation du support à distance sur Azure Stack Edge avec mise en évidence de l’option de configuration du support à distance](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-1.png)

1. Sélectionnez le **Niveau d’accès** **Diagnostic** pour fournir un accès JIT au Support Microsoft et permettre la collecte à distance des informations de diagnostic. Si nécessaire, l’équipe du support technique vous recommande de sélectionner **Diagnostic et réparation** pour autoriser une action de correction à distance. 

    Chaque niveau d’accès active un ensemble différent de commandes à distance sur l’appareil et, donc, un ensemble différent d’opérations. 

    - Les diagnostics permettent principalement des opérations de lecture. Ainsi, la plupart des applets de commande `Get` sont disponibles.
    - La fonctionnalité Diagnostics et réparation permet un accès en lecture et en écriture. Ainsi, en plus des applets de commande `Get`, les applets de commande `Set`, `Add`, `Start`, `Restart`, `Stop`, `Invoke` et `Remove` sont également disponibles.
    
    Pour plus d’informations, consultez la [liste de toutes les opérations du Support autorisées pour chaque niveau d’accès](#operations-allowed-in-remote-support).

1. Sélectionnez la durée pendant laquelle l’accès à distance doit être fourni. La durée peut être de 7, 15, 21 ou 30 jours. À l’issue de cette durée, l’accès à distance à l’appareil est automatiquement désactivé. 

1. Pour révoquer l’accès à tout moment, choisissez **Ne pas autoriser l’accès**. Cette action met fin à toutes les sessions existantes et ne permet pas d’établir de nouvelles sessions.

1. Une fois que vous avez configuré le support à distance, sélectionnez **Appliquer** pour que les paramètres prennent effet. Quand vous sélectionnez **Appliquer**, vous donnez votre consentement à Microsoft pour collecter à distance les informations de diagnostic à partir d’un environnement sécurisé et contrôlé.

    ![Capture d’écran des paramètres de support à distance sur Azure Stack Edge avec mise en évidence du niveau d’accès et de sa durée](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-2.png)    

## <a name="remote-support-examples"></a>Exemples de support à distance

Les exemples de scénarios suivants vous montrent comment effectuer diverses opérations quand le support à distance est activé sur votre appareil. 

Pour effectuer les opérations de support à distance, vous devez d’abord vous [connecter à l’interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) de l’appareil, puis exécuter les applets de commande.

### <a name="list-existing-remote-sessions"></a>Lister les sessions à distance existantes

Utilisez l’applet de commande `Get-HcsRemoteSupportSession` pour lister toutes les sessions à distance créées sur l’appareil durant le nombre de jours spécifié.

```powershell
Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays <Number of days>
```
Voici un exemple de sortie pour toutes les sessions de support à distance configurées au cours des 10 derniers jours. 

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays 10

SessionId                : 3c135cba-f479-4fef-8dbb-a2b52b744504
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 10:41:03 PM +00:00
SessionEndTime           : 8/19/2021 10:44:31 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.Lp+Myohb.20210
                           819154101.txt
SessionTranscriptContent :

SessionId                : c0f2d002-66a0-4d54-87e4-4b1b949ad686
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 7:41:19 PM +00:00
SessionEndTime           : 8/19/2021 7:58:20 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.j0lCd5Tm.20210
                           819124117.txt
SessionTranscriptContent :

SessionId                : ca038e58-5344-4377-ab9c-c857a27c8b73
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/19/2021 10:49:39 PM +00:00
SessionEndTime           : 8/20/2021 12:49:40 AM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.YHmes94q.
                           20210819154937.txt
SessionTranscriptContent :

[10.126.76.20]: PS>
```


### <a name="get-details-on-a-specific-remote-session"></a>Obtenir des détails sur une session à distance spécifique

Utilisez l’applet de commande `Get-HcsRemoteSupportSession` pour obtenir les détails de la session à distance ayant l’ID *SessionID*.

```powershell
Get-HcsRemoteSupportSession -SessionId <SessionId> -IncludeSessionTranscript $true
```

Voici un exemple de sortie pour une session spécifique dans laquelle le support à distance a été configuré avec l’option **Diagnostics**. La valeur de `AccessLevel` dans ce cas est `ReadOnly`.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId 360706a2-c530-419f-932b-55403e19502e -IncludeTranscriptContents $true

SessionId                : 360706a2-c530-419f-932b-55403e19502e
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/26/2021 2:35:37 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.u7qF2J2d.
                           20210826073536.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826073536
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteDiagnostics
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 10976
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}



                           PS>CommandInvocation(Get-Command): "Get-Command"
                           CommandInvocation(Get-HcsApplianceInfo): "Get-HcsApplianceInfo"

[10.126.76.20]: PS>
```
Voici un exemple de sortie où l’option **Diagnostics et réparation** a été configurée pour le support à distance. La valeur de `AccessLevel` pour la session de support à distance est `ReadWrite`.

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId add360db-4593-4026-93d5-6d6d05d39046 -IncludeTranscriptContents $true

SessionId                : add360db-4593-4026-93d5-6d6d05d39046
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/26/2021 2:57:08 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.ZroHb8Un.20210
                           826075705.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826075705
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteRepair
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 21832
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}

                           PS>CommandInvocation(Get-Command): "Get-Command"
[10.126.76.20]: PS>
```
 
### <a name="collect-remote-session-transcripts"></a>Collecter les transcriptions des sessions à distance

En fonction de vos impératifs d’audit, vous devrez peut-être voir les transcriptions. Suivez ces étapes pour collecter les transcriptions des sessions à distance :

1. Dans l’IU locale, accédez à **Résolution des problèmes > Support**. Collectez un package de support.
1. Une fois le package de support collecté, téléchargez celui-ci. Extrayez le dossier compressé. Les transcriptions se trouvent dans le dossier **SupportTranscripts** du package de support. 

    ![Capture d’écran du dossier SupportTranscripts mis en évidence dans le package de support](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/transcript-folder-support-package-1.png)


 
## <a name="operations-allowed-in-remote-support"></a>Opérations autorisées dans le support à distance

Les sections suivantes listent les applets de commande autorisées que le Support Microsoft peut exécuter durant une session de support à distance. La disponibilité des applets de commande varie selon que le niveau d’accès défini est **Diagnostics** ou **Diagnostics et réparation**.

#### <a name="azure-stack-edge-cmdlets"></a>Applets de commande Azure Stack Edge

| Applets de commande                      | Diagnostics | Diagnostics et réparation | Description |
|-----------------------------------------------|-------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Add-HcsExternalVirtualSwitch                  |             | O                   | Crée un commutateur virtuel externe pour configurer Kubernetes sur votre appareil.  |
| Add-HcsVirtualNetwork                         |             | O                   | Crée un commutateur virtuel sur l’interface réseau spécifiée.|
| Get-AcsHealthStatus                           | O           | O                   | Obtient l’état d’intégrité des fournisseurs de services compatibles Azure. |
| Get-AzureDataBoxEdgeRole                      | O           | O                   | Obtient les journaux de calcul via l’interface PowerShell si le rôle de calcul est configuré sur votre appareil.|
| Get-HcsApplianceInfo                          | O           | O                   | Obtient des informations sur votre appareil, par exemple l’ID, le nom convivial, la version du logiciel ou l’état du système.|
| Get-HcsApplianceSupportPackage                | O           | O                   | Collecte le package de support de votre appareil.     |
| Get-HcsArpResponse                            | O           | O                   |   |
| Get-HcsControllerSetupInformation             | O           | O                   | Obtient l’objet Microsoft.Hcs.Setup.ControllerInfo. |
| Get-HcsDataBoxAccount                         | O           | O                   ||
| Get-HcsExternalVirtualSwitch                  | O           | O                   | Obtient le commutateur sur lequel Kubernetes doit être configuré.   |
| Get-HcsGpuNvidiaSmi                           | O           | O                   | Obtient les informations relatives au pilote du GPU de votre appareil.  |
| Get-HcsIPAddress                              | O           | O                   | Récupère la configuration des cartes réseau à partir du magasin de données ou du système.   |
| Get-HcsIPAddressPool                          |             | O                   | |
| Get-HcsKubeClusterInfo                        | O           | O                   | Obtient les informations de configuration du cluster Kubernetes.|
| Get-HcsKubeClusterNetworkInfo                 | O           | O                   | Obtient les sous-réseaux de pods et de services Kubernetes.                                                                                                                                                   |
| Get-HcsKubernetesAzureMonitorConfiguration    | O           | O                   | Obtient des informations sur l’instance d’Azure Monitor qui s’exécute sur le cluster Kubernetes de votre appareil.                                                                                                        |
| Get-HcsKubernetesContainerRegistryInfo        | O           | O                   | Obtient les détails du registre de conteneurs Edge sur votre appareil.                                                                                                                               |
| Get-HcsKubernetesDashboardToken               | O           | O                   | Obtient le jeton de tableau de bord Kubernetes permettant de voir le tableau de bord (vous pouvez faire de même via l’IU locale.                                                                                               |
| Get-HcsKubernetesNamespaces                   | O           | O                   | Obtient l’espace de noms Kubernetes que vous avez configuré.  |
| Get-HcsKubernetesUserConfig                   | O           | O                   | Obtient le `kubeconfig` correspondant à un espace de noms spécifique que vous avez configuré.  |
| Get-HcsLocalWebUICertificateHash              | O           | O                   | Obtient l’empreinte numérique du certificat d’IU web local configuré. |
| Get-HcsMacAddressPool                         |             | O                   | Obtient le pool d’adresses MAC des machines virtuelles Kubernetes.     |
| Get-HcsNetBmcInterface                        | O           | O                   | Obtient les propriétés de configuration réseau du contrôleur BMC (Baseboard Management Controller), par exemple l’adresse IPv4, la passerelle IPv4, le masque de sous-réseau IPv4 et l’activation ou non du protocole DHCP sur votre appareil. |
| Get-HcsNetInterface                           | O           | O                   | Récupère la configuration réseau souhaitée de l’appareil.    |
| Get-HcsNetRoute                               | O           | O                   | Recherche toutes les configurations de routage personnalisées que vous avez ajoutées sur votre appareil. Ces routes n’incluent pas toutes les routes système ou routes par défaut qui existent déjà sur l’appareil.            |
| Get-HcsNodeSecureEraseLogs                    | O           | O                   | à supprimer, vérifier auprès d’Ernie - Récupère les journaux qui confirment que les lecteurs de l’appareil ont été effacés de manière sécurisée durant la précédente restauration aux paramètres d’usine par défaut.     |
| Get-HcsNodeSupportPackage                     | O           | O                   | Collecte les journaux de votre appareil et les copie sur un réseau spécifique ou un partage local sous forme de package de support.   |
| Get-HcsRemoteSupportConsent                   | O           | O                   | Obtient le consentement du client pour activer le support à distance sur l’appareil.   |
| Get-HcsRestEndPoint                           | O           | O                   |  |
| Get-HcsSetupDesiredStateResult                | O           | O                   | Obtient les objets de résultat DSC (Desired State Configuration) de certaines exécutions DSC pour votre appareil. |
| Get-HcsSmbConfiguration                       | O           | O                   | <!-- to be removed--> |
| Get-HcsSupportedVpnRegions                    | O           | O                   |   |
| Get-HcsSupportPackageUploadJob                | O           | O                   | Obtient l’état d’un travail de chargement de package de support en cours d’exécution.  |
| Get-HcsUpdateConfiguration                    | O           | O                   | Obtient les paramètres du serveur de mise à jour, par exemple le type de serveur et le chemin d’URI du serveur, configurés pour votre appareil Azure Stack Edge.  |
| Get-HcsUpdateJob                              | O           | O                   | Récupère tous les travaux de mise à jour en cours d’exécution sur votre appareil ou l’état d’un travail de mise à jour donné quand l’ID de travail est passé. |
| Get-HcsVirtualNetwork                         |             | O                   | Identifie le réseau virtuel et le sous-réseau associés à un commutateur que vous avez créé sur votre appareil.   |
| Get-HcsVirtualSwitch                          | O           | O                   | Obtient le commutateur virtuel associé à l’interface réseau spécifiée.  |
| Get-VMInGuestLogs                             |             | O                   | Collecte les journaux de l’invité pour les machines virtuelles défaillantes sur votre appareil.  |
| Invoke-AzureDataBoxEdgeRoleReconcile          |             | O                   | Permet de mettre à jour la configuration du cluster Kubernetes. |
| Invoke-AzureDataBoxEdgeRoleReconfigure        |             | O                   | Permet de changer la configuration du cluster Kubernetes.  |
| Remove-HcsIPAddressPool                       |             |                     | Supprime le pool d’adresses MAC des machines virtuelles Kubernetes. |
| Remove-HcsKubeClusterNetworkInfo              |             | O                   | Permet de changer les sous-réseaux de pods ou de services Kubernetes.|
| Remove-HcsKubernetesAzureArcAgent             |             | O                   | Supprime l’agent Azure Arc du cluster Kubernetes sur votre appareil.  |
| Remove-HcsKubernetesAzureMonitorConfiguration |             | O                   | Supprime Azure Monitor du cluster Kubernetes sur votre appareil, et vous permet de collecter les journaux de conteneur ainsi que les métriques de processeur du cluster Kubernetes.  |
| Remove-HcsKubernetesContainerRegistry         |             | O                   | Supprime le registre de conteneurs Edge de votre appareil.   |
| Remove-HcsKubernetesNamespace                 |             | O                   | Supprime l’espace de noms Kubernetes spécifié. |
| Remove-HcsMacAddressPool                      |             | O                   | Supprime le pool d’adresses MAC des machines virtuelles Kubernetes.|
| Remove-HcsNetRoute                            |             | O                   | Supprime une configuration de routage que vous avez ajoutée sur votre appareil. |
| Remove-HcsVirtualNetwork                      |             | O                   | Supprime un réseau virtuel et le sous-réseau associés à un commutateur que vous avez créé sur votre appareil. |
| Remove-HcsVirtualSwitch                       |             | O                   | Supprime un commutateur virtuel associé à un port sur votre appareil.  |
| Restart-HcsNode                               |             | O                   | Redémarre un nœud sur votre appareil. Pour un appareil à un seul nœud, cela entraîne le redémarrage de l’appareil et un temps d’arrêt. |
| Set-AzureDataBoxEdgeRoleCompute               |             | O                   | Permet de configurer le calcul via le portail Azure, mais également de créer et configurer le cluster Kubernetes.   |
| Set-HcsCertificate                            |             | O                   | Vous permet d’apporter vos propres certificats. |
| Set-HcsClusterLevelSecurity                   |             | O                   | Configurer le niveau de sécurité du trafic de cluster. Cela inclut le trafic de cluster intranœud et le trafic provenant de volumes partagés de cluster (CSV).    |
| Set-HcsClusterWitness                         |             | O                   | Crée ou configure un témoin de cluster Windows. Un témoin de cluster permet d’établir un quorum quand un nœud tombe en panne dans un appareil à 2 nœuds.     |
| Set-HcsEastWestCsvEncryption                  |             | O                   | <!--can be removed, confirmed with Vibha/Deepan. Talk to Phani. -->  |
| Set-HcsExternalVirtualSwitch                  |             | O                   | Configure un commutateur virtuel externe sur le port pour lequel vous avez activé le calcul sur votre appareil.  |
| Set-HcsIPAddress                              |             | O                   | Met à jour les propriétés de l’interface réseau.                 |
| Set-HcsIPAddressPool                          |             |                     | Définit le pool d’adresses MAC des machines virtuelles Kubernetes.       |
| Set-HcsKubeClusterNetworkInfo                 |             | O                   | Change les sous-réseaux de pods et de services Kubernetes avant que vous ne configuriez le calcul à partir du portail Azure sur votre appareil.                                                                                  |
| Set-HcsKubernetesAzureArcAgent                |             | O                   | Active le cluster Azure Arc sur Kubernetes en installant l’agent Arc sur votre appareil. |
| Set-HcsKubernetesAzureArcDataController       |             | O                   | Crée un contrôleur de données sur le cluster Kubernetes de votre appareil. |
| Set-HcsKubernetesAzureMonitorConfiguration    |             | O                   | Active Azure Monitor sur le cluster Kubernetes de votre appareil, et vous permet de collecter les journaux de conteneur ainsi que les métriques de processeur du cluster Kubernetes.|
| Set-HcsKubernetesContainerRegistry            |             | O                   | Active le registre de conteneurs Edge en tant que module complémentaire pour votre appareil.                                                                                                                                  |
| Set-HcsMacAddressPool                         |             | O                   | Définit le pool d’adresses MAC des machines virtuelles Kubernetes. |
| Set-HcsNetBmcInterface                        |             | O                   | Active ou désactive la configuration DHCP pour le contrôleur BMC. |
| applet de commande Set-HcsNetInterface                           |             | O                   | Configure l’adresse IP, le masque de sous-réseau et la passerelle d’une interface réseau sur votre appareil Azure Stack Edge.|
| Set-HcsSmbServerEncryptionConfiguration       |             | O                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsSmbSigningConfiguration                |             | O                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsUpdateConfiguration                    |             | O                   | Définit les paramètres du serveur de mise à jour pour votre appareil, notamment le type de serveur et le chemin d’URI du serveur. Vous pouvez choisir d’installer les mises à jour à partir d’un serveur Microsoft Update ou d’un serveur WSUS (Windows Server Update Services).   |
| Set-HcsVirtualNetwork                         |             | O                   |Crée un réseau virtuel et le sous-réseau associés à un commutateur que vous avez créé sur votre appareil. |
| Set-HcsVpnS2SInterface                        |             | O                   | <!--VPN should be P2S-->  |
| Start-HcsGpuMPS                               |             | O                   | Active le service multiprocesseur (MPS) sur votre appareil.|
| Start-HcsSupportPackageUploadJob              | O           | O                   | Collecte un package de support avec tous les journaux et charge ce package pour que Microsoft puisse l’utiliser afin de déboguer les problèmes liés à votre appareil.|
| Start-HcsUpdateJob                            |             | O                   | Démarre le travail de mise à jour.|
| Stop-HcsGpuMPS                                |             | O                   | Désactive le service multiprocesseur (MPS) sur votre appareil. |
| Test-HcsKubernetesStatus                      | O           | O                   | Exécute le conteneur de diagnostic Kubernetes. |

#### <a name="generic-network-cmdlets"></a>Applets de commande réseau génériques

| Applets de commande   | Diagnostics | Diagnostics et réparation | Description                                                                                                                            |
|---------------------------|-------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Find-NetRoute             | O           | O                    | Recherche la meilleure adresse IP locale et la meilleure route pour atteindre une adresse distante.                                                          |
| Get-NetAdapter            | O           | O                    | Obtient les propriétés de base de la carte réseau.                                                                                             |
| Get-NetIPAddress          | O           | O                    | Obtient la configuration d’adresse IP complète de l’ordinateur.                                                                             |
| Get-NetNat                | O           | O                    | Obtient les objets NAT (traduction d’adresses réseau) configurés sur un ordinateur.                                                               |
| Get-NetNatExternalAddress | O           | O                    | Obtient une liste d’adresses externes configurées sur une instance de service NAT (traduction d’adresses réseau).                                          |
| Get-NetRoute              | O           | O                    | Obtient les informations de routage IP à partir de la table de routage IP, notamment les préfixes du réseau de destination, les adresses IP du tronçon suivant et les métriques de routage. |
| Get-NetCompartment        | O           | O                    | Obtient les compartiments réseau de la pile de protocoles                                                                                        |
| Get-NetNeighbor           | O           | O                    | Obtient les entrées du cache de voisins.                                                                                                           |
| Get-NetAdapterSriov       | O           | O                    | Obtient les propriétés SR-IOV de la carte réseau.                                                                                     |
| Resolve-DnsName           |             | O                    | Effectue une résolution de requête de nom DNS pour le nom spécifié.                                                                           |


####  <a name="multi-access-edge-computing-mec-cmdlets"></a>Applets de commande MEC (Multi-Access Edge Computing)

| Applets de commande | Diagnostics | Diagnostics et réparation | Description                                                                                                       |
|-------------------------------------------|-------------|----------------------|-------------------------------------------------------------------------------------------------------------------|
| Get-MecVnf                                | O           | O                    | Obtient une liste de toutes les machines virtuelles avec fonctions de réseau virtuel Multi-Access Edge Computing déployées sur votre instance d’Azure Stack Edge.       |
| Get-MecVirtualMachine                     | O           | O                    | Obtient une liste des machines virtuelles créées par les fonctions de réseau virtuel Multi-Access Edge Computing. fonctions.                    |
| Get-MecServiceConfig                      | O           | O                    | Obtient la configuration de service de l’architecture Multi-Access Edge Computing qui affecte les fonctions de réseau virtuel. workflow de cycle de vie. |
| Get-MecInformation                        | O           | O                    | Obtient des informations sur l’architecture Multi-Access Edge Computing. Par exemple, indique si votre appareil Azure Stack Edge est inscrit auprès du gestionnaire de fonctions réseau Azure.

####  <a name="general-purpose-os-cmdlets"></a>Applets de commande d’OS à usage général

| Applets de commande | Diagnostics | Diagnostics et réparation | Description                                                                                                                  |
|----------------------------|-------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Select-String              | O           | O                    | Recherche du texte dans des chaînes et des fichiers.                                                                                             |
| Write-Progress             | O           | O                    | Affiche une barre de progression dans une fenêtre Commande PowerShell.                                                                  |
| Get-Command                | O           | O                    | Obtient une liste de commandes qui peuvent être exécutées dans une session.                                                                        |
| Measure-Object             | O           | O                    | Calcule les propriétés numériques des objets et les caractères, les mots et les lignes dans les objets de chaîne, par exemple les fichiers de texte. |
| Select-Object              | O           | O                    | Sélectionne des objets ou des propriétés d'objet.                                                                                        |
| Out-Default                |             | O                    | Envoie la sortie au formateur par défaut et à l'applet de commande de sortie par défaut.                                                  |
| Get-WinEvent               | O           | O                    | Obtient les événements des journaux des événements et des fichiers journaux de suivi des événements.                                                                     |
| Get-Counter                | O           | O                    | Obtient les données de compteur de performances.                                                                                               |
| Get-Volume                 | O           | O                    | Obtient l'objet Volume spécifié ou tous les objets Volume si aucun filtre n'est fourni.                                            |
| Get-Service                | O           | O                    | Obtient les objets qui représentent les services.                                                                                    |

#### <a name="cluster-cmdlets"></a>Applets de commande de cluster 

| Applets de commande       | Diagnostics | Diagnostics et réparation | Description                                                                            |
|-----------------------|-------------|----------------------|----------------------------------------------------------------------------------------|
| Get-ClusterResource   | O           | O                    | Obtient des informations sur une ou plusieurs ressources d’un cluster de basculement.                    |
| Get-Cluster           | O           | O                    | Obtient des informations sur le cluster de basculement.                                               |
| Get-ClusterNode       | O           | O                    | Obtient des informations sur un ou plusieurs nœuds, ou serveurs, d’un cluster de basculement.           |
| Start-Cluster         |             | O                    | Démarre le service Cluster sur tous les nœuds du cluster où il n’a pas encore démarré. |
| Start-ClusterResource |             | O                    | Met une ressource en ligne dans un cluster de basculement.                                        |
| Stop-ClusterResource  |             | O                    | Met une ressource hors connexion dans un cluster de basculement.                                        |


#### <a name="hyper-v-cmdlets"></a>Applets de commande Hyper-V

| Applets de commande      | Diagnostics | Diagnostics et réparation | Description                                                                                                                                             |
|----------------------|-------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Get-Vm               | O           | O                    | Obtient les machines virtuelles de l’ordinateur.                                                                                                              |
| Get-VMNetworkAdapter | O           | O                    | Obtient les cartes réseau virtuelles d’une machine virtuelle, d’une capture instantanée, d’un système d’exploitation de gestion ou d’une machine virtuelle et d’un système d’exploitation de gestion. |
| Get-VMHardDiskDrive  | O           | O                    | Obtient les lecteurs de disque dur virtuel attachés à un ou plusieurs ordinateurs virtuels.                                                                             |
| Get-VMSwitch         | O           | O                    |  Obtient la liste des commutateurs virtuels.                                                                                                                     |

## <a name="next-steps"></a>Étapes suivantes

[Contactez le support Microsoft](azure-stack-edge-contact-microsoft-support.md).
