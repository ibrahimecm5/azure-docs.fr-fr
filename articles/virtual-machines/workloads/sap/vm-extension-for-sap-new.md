---
title: Nouvelle version de l’extension de machine virtuelle Azure pour les solutions SAP | Microsoft Docs
description: Découvrez comment déployer la nouvelle extension de machine virtuelle pour SAP.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: ce34b313661106e903a92aaf1a3b2f65213a1634
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577257"
---
# <a name="new-version-of-azure-vm-extension-for-sap-solutions"></a>Nouvelle version de l’extension de machine virtuelle Azure pour les solutions SAP 
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[std-extension]:vm-extension-for-sap-standard.md (Version standard de l’Extension Azure VM pour les solutions SAP)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Configurer la nouvelle extension de machine virtuelle Azure pour les solutions SAP avec PowerShell)
[troubleshoot-windows]:vm-extension-for-sap-new.md#dee9099b-7b8a-4cdd-86a2-3f6ee964266f (Résolution des problèmes pour Windows)
[troubleshoot-linux]:vm-extension-for-sap-new.md#02783aa4-5443-43f5-bc11-7af19ebf0c36 (Résolution des problèmes pour Linux)
[deployment-guide-4.1]:vm-extension-for-sap-new.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Déploiement d’applets de commande Azure PowerShell)
[azure-cli-2]:/cli/azure/install-azure-cli
[configure-linux]:vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481 (Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell)
[health-check]:vm-extension-for-sap-new.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Contrôles d'intégrité)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[readiness-check]:vm-extension-for-sap-new.md#5774c1db-1d3c-4b34-8448-3afd0b0f18ab (Vérification de la préparation)

## <a name="prerequisites"></a>Prérequis

> [!NOTE]
> Instruction générale de support : La prise en charge de l’extension Azure pour SAP est assurée par le biais des canaux de support SAP.
> Si vous avez besoin d’aide sur l’extension de machine virtuelle Azure pour solutions SAP, veuillez ouvrir un dossier de support auprès du support SAP
  
> [!NOTE]
> Veillez à désinstaller l’extension de machine virtuelle avant de basculer entre la version standard et la nouvelle version de l’extension Azure pour SAP.

> [!NOTE]
> L’extension de machine virtuelle se présente sous deux versions. Cet article traite de la **nouvelle** version de l’extension de machine virtuelle Azure pour SAP. Pour obtenir des conseils sur l’installation de la version standard, consultez [Version standard de l’extension de machine virtuelle Azure pour les solutions SAP][std-extension].

* Veillez à utiliser la version 7.21 PL 47 ou une version ultérieure de l’agent hôte SAP.
* Assurez-vous que l’ordinateur virtuel sur lequel l’extension est activée a accès à management.azure.com.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Déploiement d’applets de commande Azure PowerShell

Suivez la procédure décrite dans l’article [Installation du module Azure PowerShell](/powershell/azure/install-az-ps).

Recherchez régulièrement les mises à jour des applets de commande PowerShell, qui sont généralement publiées une fois par mois. Suivez la procédure décrite dans [cet](/powershell/azure/install-az-ps#update-the-azure-powershell-module) article. Sauf indication contraire dans les notes SAP [1928533] ou [2015553], nous vous recommandons d’utiliser la dernière version des applets de commande Azure PowerShell.

Pour vérifier la version des applets de commande Azure PowerShell installées sur votre ordinateur, exécutez cette commande PowerShell :

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Déploiement de l’interface de ligne de commande Azure

Suivez la procédure décrite dans l’article [Installation d’Azure CLI](/cli/azure/install-azure-cli).

Recherchez régulièrement les mises à jour de l’interface de ligne de commande Azure, qui sont généralement publiées une fois par mois.

Pour vérifier la version de l’interface de ligne de commande Azure installée sur votre ordinateur, exécutez cette commande :

```console
az --version
```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="a876ee7b-43b4-4782-aa5f-73753b6af0ea"></a>Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell
 
La nouvelle extension de machine virtuelle pour SAP utilise une identité managée affectée à la machine virtuelle pour accéder aux données de surveillance et de configuration de la machine virtuelle. Pour installer la nouvelle extension à l’aide de PowerShell, vous devez d’abord attribuer à la machine virtuelle une identité de ce type, à laquelle vous accordez l’accès à toutes les ressources utilisées par cette machine virtuelle, par exemple les disques et les interfaces réseau.

> [!NOTE]
> Des privilèges de propriétaire sur le groupe de ressources ou certaines ressources (machine virtuelle, disques de données et interfaces réseau) sont nécessaires pour suivre les étapes ci-dessous)

1. Veillez à utiliser la version 7.21 PL 47 ou une version ultérieure de l’agent hôte SAP.
1. Désinstallez la version standard de l’extension de machine virtuelle pour SAP. L’installation des deux versions de l’extension de machine virtuelle pour SAP sur la même machine virtuelle n’est pas prise en charge.
1. Vérifiez que vous avez installé la dernière version de la cmdlet Azure PowerShell (au minimum, la version 4.3.0). Pour plus d’informations, consultez [Déploiement d’applets de commande Azure PowerShell][deployment-guide-4.1].
1. Exécutez l’applet de commande PowerShell suivant.
    Pour afficher la liste des environnements disponibles, exécutez la cmdlet `Get-AzEnvironment` . Si vous voulez utiliser la version globale d’Azure, votre environnement est **AzureCloud**. Pour Azure China 21Vianet, sélectionnez **AzureChinaCloud**.

    L’extension de machine virtuelle pour SAP prend en charge la configuration d’un proxy que l’extension doit utiliser pour se connecter à des ressources externes, par exemple l’API Azure Resource Manager. Utilisez le paramètre -ProxyURI pour définir le proxy.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="fa4428b9-bed6-459a-9dfb-74cc27454481"></a>Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI
 
La nouvelle extension de machine virtuelle pour SAP utilise une identité managée affectée à la machine virtuelle pour accéder aux données de surveillance et de configuration de la machine virtuelle.

> [!NOTE]
> Des privilèges de propriétaire sur le groupe de ressources ou certaines ressources (machine virtuelle, disques de données, etc.) sont nécessaires pour suivre les étapes ci-dessous

1. Veillez à utiliser l’Agent Hôte SAP 7.21 PL 47 ou une version ultérieure.
1. Veillez à désinstaller la version actuelle de l’extension de machine virtuelle pour SAP. Vous ne pouvez pas installer les deux versions de l’extension de machine virtuelle pour SAP sur la même machine virtuelle n’est pas prise en charge. 
1. Installez la dernière version de [Azure CLI 2.0][azure-cli-2] (version 2.19.1 ou ultérieure).
1. Connectez-vous à votre compte Azure :

   ```azurecli
   az login
   ```

1. Installez l’extension AEM Azure CLI. Veillez à utiliser la version 0.2.2 ou une version ultérieure.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Activez la nouvelle extension :
  
   L’extension de machine virtuelle pour SAP prend en charge la configuration d’un proxy que l’extension doit utiliser pour se connecter à des ressources externes, par exemple l’API Azure Resource Manager. Utilisez le paramètre --proxy-uri pour définir le proxy.

   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```
 
 
## <a name="readiness-check"></a><a name="5774c1db-1d3c-4b34-8448-3afd0b0f18ab"></a>Vérification de la préparation

Cette vérification vous assure que tous les indicateurs de performance affichés au sein de votre application SAP sont fournis par l’extension Azure pour SAP sous-jacente.

### <a name="run-the-readiness-check-on-a-windows-vm"></a>Vérifier la disponibilité d’une machine virtuelle Windows

1. Connectez-vous à la machine virtuelle Azure (l’utilisation d’un compte Administrateur n’est pas nécessaire).
1. Ouvrez un navigateur web et accédez à http://127.0.0.1:11812/azure4sap/metrics.
1. Le navigateur affiche ou télécharge un fichier XML contenant les données d’analyse de votre machine virtuelle. Si ce n’est pas le cas, vérifiez que l’extension Azure pour SAP est installée.
1. Vérification du contenu du fichier XML. Le fichier XML accessible à l’adresse http://127.0.0.1:11812/azure4sap/metrics contient tous les compteurs de performances Azure remplis pour SAP. Il comporte également un résumé et un indicateur d’intégrité de l’état de l’extension Azure pour SAP.
1. Vérifiez la valeur de la description de l’élément **Description de l’intégrité du fournisseur**. Si la valeur n’est pas **OK**, suivez les instructions du chapitre [Contrôles d'intégrité][health-check].
 
### <a name="run-the-readiness-check-on-a-linux-vm"></a>Vérifier la disponibilité d’une machine virtuelle Linux

1. Connectez-vous à la machine virtuelle Azure à l’aide de SSH.
1. Vérifiez la sortie de la commande suivante :
   ```bash
   curl http://127.0.0.1:11812/azure4sap/metrics
   ```
   **Résultat attendu** : Retourne un document XML contenant les informations d’analyse de la machine virtuelle, de ses disques et de ses interfaces réseau.
   1. Connectez-vous à la machine virtuelle Azure à l’aide de SSH.

1. Vérifiez la sortie de la commande suivante :

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Résultat attendu** : Retourne un document XML contenant les informations d’analyse de la machine virtuelle, de ses disques et de ses interfaces réseau.

Si la vérification précédente a échoué, exécutez ces vérifications supplémentaires :

1. Assurez-vous que waagent est installé et démarré.

   a.  Exécutez `sudo ls -al /var/lib/waagent/`

     **Résultat attendu** : Répertorie le contenu du répertoire waagent.

   b.  Exécutez `ps -ax | grep waagent`

   **Résultat attendu** : Affiche une entrée similaire à : `python /usr/sbin/waagent -daemon`

1. Assurez-vous que l’extension Azure pour SAP est installée et en cours d’exécution.

   a.  Exécutez `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Résultat attendu** : Répertorie le contenu de l’annuaire de l’extension Azure pour SAP.

   b. Exécutez `ps -ax | grep AzureEnhanced`

   **Résultat attendu** : Affiche une entrée similaire à : `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Installez l’agent hôte SAP comme décrit dans la note SAP [1031096] et vérifiez le résultat de `saposcol`.

   a.  Exécutez `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Exécutez `dump ccm`

   c.  Vérifiez si la métrique **Virtualization_Configuration\Enhanced Monitoring Access** a la valeur **true**.

Si vous avez déjà installé un serveur d’applications ABAP SAP NetWeaver, ouvrez la transaction ST06 et regardez si l’analyse est activée.

Si l’une de ces vérifications échoue ou pour savoir comment redéployer l’extension, consultez [Résolution des problèmes pour Windows][troubleshoot-windows] ou [Résolution des problèmes pour Linux][troubleshoot-linux]
 
## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Contrôles d'intégrité

Si certaines des données d’infrastructure ne sont pas fournies correctement, comme indiqué par le test décrit dans [Vérification de la disponibilité][readiness-check], exécutez les contrôles d’intégrité repris dans ce chapitre pour tester si l’infrastructure Azure et l’extension Azure pour SAP sont correctement configurées.

### <a name="health-checks-using-powershell"></a>Contrôles d’intégrité à l’aide de PowerShell

1. Assurez-vous d’avoir installé la dernière version de l’applet de commande Azure PowerShell, comme décrit dans le chapitre [Déploiement d’applets de commande Azure PowerShell][deployment-guide-4.1].
1. Exécutez l’applet de commande PowerShell suivant. Pour afficher la liste des environnements disponibles, exécutez l’applet de commande `Get-AzEnvironment`. Pour utiliser la version globale d’Azure, sélectionnez l’environnement **AzureCloud**. Pour Azure China 21Vianet, sélectionnez **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```
1. Le script teste la configuration de la machine virtuelle sélectionnée.

Assurez-vous que le résultat de chaque vérification d’intégrité est **OK**. Si certaines vérifications n’affichent pas **OK**, exécutez l’applet de commande de mise à jour comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows]. Répétez les vérifications décrites dans [Contrôle d’intégrité][readiness-check] et ce chapitre. Si les vérifications indiquent encore un problème avec certains ou tous les compteurs, consultez [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].

### <a name="health-checks-using-azure-cli"></a>Contrôles d’intégrité à l’aide d’Azure CLI

Pour exécuter le contrôle d’intégrité de l’extension de machine virtuelle Azure pour SAP à l’aide d’Azure CLI :
 
1. Installez [Azure CLI 2.0][azure-cli-2]. Veillez à utiliser au moins la version 2.19.1 ou ultérieure (utilisez la version la plus récente). 
1. Connectez-vous à votre compte Azure :
   ```azurecli
   az login
   ```

1. Installez l’extension AEM Azure CLI. Veillez à utiliser la version 0.2.2 ou une version ultérieure.
   ```azurecli
   az extension add --name aem
   ```

1. Vérifiez l’installation de l’extension : 
   ```azurecli
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
Le script teste la configuration de la machine virtuelle sélectionnée.

Assurez-vous que le résultat de chaque vérification d’intégrité est **OK**. Si certaines vérifications n’affichent pas **OK**, exécutez l’applet de commande de mise à jour comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows]. Répétez les vérifications décrites dans [Contrôle d’intégrité][readiness-check] et ce chapitre. Si les vérifications indiquent encore un problème avec certains ou tous les compteurs, consultez [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].


## <a name="troubleshooting-for-windows"></a><a name="dee9099b-7b8a-4cdd-86a2-3f6ee964266f"></a>Résolution des problèmes pour Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Les compteurs de performances Azure ne s’affichent pas
Le processus AzureEnhancedMonitoring recueille les indicateurs de performance dans Azure. S’il ne s’exécute pas sur votre machine virtuelle, aucun indicateur de performance ne peut être collecté.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Le répertoire d’installation de l’extension Azure pour SAP est vide
##### <a name="issue"></a>Problème
Le répertoire d’installation C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;version> est vide.
##### <a name="solution"></a>Solution
L’extension n’est pas installée. Déterminez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer l’ordinateur ou réinstaller l’extension de machine virtuelle.
 
### <a name="some-azure-performance-counters-are-missing"></a>Certains compteurs de performances Azure sont manquants

Le processus Windows AzureEnhancedMonitoring recueille les indicateurs de performance dans Azure. Il récupère des données provenant de plusieurs sources. Certaines données de configuration sont collectées localement, certains indicateurs de performance sont lus à partir d’Azure Monitor.

Si la note SAP [1999351] ne permet pas de résoudre le problème, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux. Joignez le fichier journal C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;version>\\logapp.txt à l’incident.

## <a name="troubleshooting-for-linux"></a><a name="02783aa4-5443-43f5-bc11-7af19ebf0c36"></a>Résolution des problèmes pour Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Les compteurs de performances Azure ne s’affichent pas
Les indicateurs de performance dans Azure sont collectés par un démon. Si le démon ne fonctionne pas, aucun indicateur de performance ne peut être collecté.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Le répertoire d’installation de l’extension Azure pour SAP est vide
##### <a name="issue"></a>Problème
Le répertoire /var/lib/waagent/ ne contient pas de sous-répertoire pour l’extension Azure pour SAP.
##### <a name="solution"></a>Solution
L’extension n’est pas installée. Déterminez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer l’ordinateur ou réinstaller l’extension de machine virtuelle.
 
### <a name="some-azure-performance-counters-are-missing"></a>Certains compteurs de performances Azure sont manquants

Les indicateurs de performance sur Azure sont collectés par un démon, qui obtient des données de plusieurs sources. Certaines données de configuration sont collectées localement, certains indicateurs de performance sont lus à partir d’Azure Monitor.
Pour obtenir une liste complète et à jour des problèmes connus, consultez la note SAP [1999351], qui contient des informations de dépannage supplémentaires pour l’extension Azure pour SAP.
Si la note SAP [1999351] ne permet pas de résoudre le problème, réinstallez l’extension selon les modalités décrites dans [Configuration de l’extension Azure pour SAP][configure-windows]. Si le problème persiste, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux. Joignez le fichier journal /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;version>/logapp.txt à l’incident.


## <a name="azure-extension-error-codes"></a>Codes d’erreur de l’extension Azure
 
Tous les ID d’erreur ont une balise unique sous la forme a-#, où # est un nombre. Elle permet une recherche rapide d’une erreur spécifique et de solutions possibles.
 
| ID d'erreur | Description de l'erreur | Solutions |
|---|---|---|
| `a-0116` | aucun jeton d'authentification | Informations supplémentaires :<br />L’extension ne peut pas obtenir le jeton d’authentification pour accéder aux métriques de machine virtuelle dans Azure Monitor. Pour fournir des métriques de machine virtuelle, elle doit accéder à des ressources de machine virtuelle telles qu’une machine virtuelle, tous les disques et toutes les cartes réseau attachées à une machine virtuelle<br />Solution :<br />Activez l’identité gérée de machine virtuelle et donnez-lui un rôle de lecteur pour un groupe de ressources de machine virtuelle. Lorsque vous utilisez un script de configuration, le script s’en charge à votre place. Normalement, vous n’avez pas besoin d’activer ni d’attribuer manuellement une identité gérée par une machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes
* [Déploiement de machines virtuelles Azure pour SAP NetWeaver](./deployment-guide.md)
* [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](./planning-guide.md)
