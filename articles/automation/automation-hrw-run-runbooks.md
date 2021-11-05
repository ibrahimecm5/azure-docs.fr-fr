---
title: Exécuter des runbooks Azure Automation sur un Runbook Worker hybride
description: Cet article explique comment exécuter des runbooks sur des machines de votre centre de données local ou d’un autre fournisseur de cloud avec le Runbook Worker hybride.
services: automation
ms.subservice: process-automation
ms.date: 11/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71f13679a1f19672368a7b72987e28813232f846
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465552"
---
# <a name="run-automation-runbooks-on-a-hybrid-runbook-worker"></a>Exécuter des runbooks Automation sur un Runbook Worker hybride

Les runbooks qui s’exécutent sur un [Runbook Worker hybride](automation-hybrid-runbook-worker.md) gèrent généralement les ressources sur l’ordinateur local ou par rapport aux ressources de l’environnement local dans lequel le Worker est déployé. Les runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure. Même s’ils sont utilisés différemment, les runbooks qui s’exécutent dans Azure Automation et les runbooks qui s’exécutent sur un runbook Worker hybride ont une structure identique.

Quand vous créez un runbook pour l’exécuter sur un runbook Worker hybride, vous devez le modifier et le tester sur la machine qui héberge le Worker. La machine hôte dispose de tous les modules PowerShell, ainsi que de l’accès réseau nécessaire à la gestion des ressources locales. Une fois que vous avez testé le runbook sur la machine du runbook Worker hybride, vous pouvez le charger dans l’environnement Azure Automation, où il peut être exécuté sur le Worker.

## <a name="plan-for-azure-services-protected-by-firewall"></a>Planifier les services Azure protégés par un pare-feu

L’activation du Pare-feu Azure sur [Stockage Microsoft Azure](../storage/common/storage-network-security.md), [Azure Key Vault](../key-vault/general/network-security.md) ou [Azure SQL](../azure-sql/database/firewall-configure.md) bloque l’accès à partir des runbooks Azure Automation pour ces services. L’accès sera bloqué même lorsque l’exception de pare-feu pour autoriser les services Microsoft approuvés est activée, car Automation ne fait pas partie de la liste des services approuvés. Avec un pare-feu activé, l’accès ne peut être effectué qu’à l’aide d’un Runbook Worker hybride et d’un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="plan-runbook-job-behavior"></a>Planifier le comportement d’un travail de runbook

Azure Automation gère les travaux exécutés sur des Runbooks Workers hybrides différemment des travaux exécutés dans des bacs à sable Azure. Si vous avez un runbook de longue durée, assurez-vous qu’il est résilient aux possibles redémarrages. Pour obtenir des informations détaillées sur le comportement des travaux, consultez [Travaux Runbook Worker hybride](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

## <a name="service-accounts"></a>Comptes de service

### <a name="windows"></a>Windows 

Les travaux des Runbooks Worker hybrides sont exécutés sous le compte local **Système**.
>[!NOTE]
>  Pour exécuter PowerShell 7.x sur un Runbook Worker hybride Windows, consultez [Installation de PowerShell sur Windows](/powershell/scripting/install/installing-powershell-on-windows).
> Actuellement, nous prenons uniquement en charge l’intégration basée sur l’extension de Worker hybride, comme indiqué [ici.](/azure/automation/extension-based-hybrid-runbook-worker-install) 

Vérifiez que le chemin où se trouve l’exécutable *pwsh.exe* est ajouté à la variable d’environnement PATH. Redémarrez le Runbook Worker hybride une fois l’installation effectuée.

### <a name="linux"></a>Linux

>[!NOTE]
> Pour exécuter PowerShell 7.x sur un Runbook Worker hybride Linux, consultez [Installation de PowerShell sur Linux](/powershell/scripting/install/installing-powershell-on-linux).
> Actuellement, nous prenons uniquement en charge l’intégration basée sur l’extension de Worker hybride, comme indiqué [ici.](/azure/automation/extension-based-hybrid-runbook-worker-install)


Les comptes de service **nxautomation** et **omsagent** sont créés. Le script de création et d'attribution des autorisations peut être consulté à l'adresse [https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/installer/datafiles/linux.data](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/installer/datafiles/linux.data). Les comptes, avec les autorisations sudo correspondantes, doivent être présents lors de l’[installation d’un runbook Worker hybride Linux](automation-linux-hrw-install.md). Si vous essayez d’installer le Worker et que le compte n’est pas présent ou ne dispose pas des autorisations appropriées, l’installation échoue. Ne modifiez pas les autorisations du dossier `sudoers.d` ni sa propriété. L’autorisation Sudo est requise pour les comptes, et les autorisations ne doivent pas être supprimées. Limiter ce principe à certains dossiers ou à certaines commandes peut entraîner une modification critique. L’utilisateur **nxautomation** activé en lien avec Update Management exécute uniquement des runbooks signés.

Pour vous assurer que les comptes de service ont accès aux modules de runbook stockés :

- Lorsque vous utilisez `pip install`, `apt install` ou une autre méthode pour installer des packages sur Linux, assurez-vous que le package est installé pour tous les utilisateurs. Par exemple, `sudo -H pip install <package_name>`.
- Si vous utilisez [PowerShell sur Linux](/powershell/scripting/whats-new/what-s-new-in-powershell-70), lorsque vous utilisez la cmdlet [Install-Module](/powershell/module/powershellget/install-module), assurez-vous de spécifier `AllUsers` comme paramètre `Scope`.

Le journal du Worker Automation se trouve dans `/var/opt/microsoft/omsagent/run/automationworker/worker.log`.

Les comptes de service sont supprimés lorsque la machine est supprimée en tant que Runbook Worker hybride.

## <a name="configure-runbook-permissions"></a>Configurer des autorisations de runbook

Définissez les autorisations qui permettent à votre runbook de s’exécuter sur le Runbook Worker hybride des différentes manières suivantes :

* Faire en sorte que le runbook fournisse sa propre authentification aux ressources locales.
* Configurer l’authentification au moyen des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Spécifier un compte d’identification afin de fournir un contexte utilisateur à tous les runbooks.

### <a name="use-runbook-authentication-to-local-resources"></a>Utiliser l’authentification du runbook auprès des ressources locales

Si vous préparez un runbook qui fournit sa propre authentification aux ressources, utilisez les ressources [Informations d’identification](./shared-resources/credentials.md) et [Certificat](./shared-resources/certificates.md) dans votre runbook. Il existe plusieurs applets de commande vous permettant de spécifier des informations d’identification pour que le runbook puisse s’authentifier auprès de différentes ressources. L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur. Il récupère des informations d’identification à partir d’une ressource d’informations d’identification, ainsi que le nom de l’ordinateur à partir d’une ressource de variable, puis il utilise ces valeurs avec l’applet de commande `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Vous pouvez également utiliser une activité [InlineScript](automation-powershell-workflow.md#use-inlinescript). `InlineScript` vous permet d’exécuter des blocs de code sur un autre ordinateur avec des informations d’identification.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Utiliser l’authentification du runbook avec les identités managées

Les runbooks Workers hybrides sur les machines virtuelles Azure peuvent utiliser des identités managées pour s’authentifier auprès des ressources Azure. L’utilisation d’identités managées de ressources Azure au lieu de comptes d’identification offre des avantages, car vous n’avez pas besoin d’effectuer les tâches suivantes :

* Exporter le certificat d’identification et l’importer dans le runbook Worker hybride
* Renouveler le certificat utilisé par le compte d’identification
* Gérer l’objet de connexion d’identification dans le code de votre runbook

Effectuez les étapes suivantes pour utiliser une identité managée pour des ressources Azure sur un Runbook Worker hybride :

1. Créez une machine virtuelle Azure.
1. Configurez des identités managées pour les ressources Azure sur la machine virtuelle. Consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Accordez à votre machine virtuelle l’accès à un groupe de ressources dans Resource Manager. Reportez-vous à [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Installez le Runbook Worker hybride sur la machine virtuelle. Voir [Déployer un Runbook Worker hybride Windows](automation-windows-hrw-install.md) ou [Déployer un Runbook Worker hybride Linux](automation-linux-hrw-install.md).
1. Mettez à jour le runbook pour utiliser l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) avec le paramètre `Identity` pour l’authentification auprès des ressources Azure. Cette configuration réduit la nécessité d’utiliser un compte d’identification et d’effectuer la gestion de comptes associée.

    ```powershell
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

    # Get all VM names from the subscription
    Get-AzVM -DefaultProfile $AzureContext | Select Name
    ```

    Si vous souhaitez que le runbook s’exécute avec l’identité managée affectée par le système, laissez le code tel quel. Si vous préférez utiliser une identité managée affectée par l’utilisateur, procédez comme suit :
    1. À la ligne 5, supprimez `$AzureContext = (Connect-AzAccount -Identity).context`,
    1. Remplacez-la par `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` et
    1. Entrez l'ID client.

### <a name="use-runbook-authentication-with-run-as-account"></a>Utiliser l’authentification du runbook avec un compte d’identification

Plutôt que de laisser votre runbook fournir sa propre authentification auprès des ressources locales, vous pouvez spécifier un compte d’identification pour un groupe de runbooks Workers hybrides. Pour spécifier un compte d’identification, vous devez définir une [ressource d’informations d’identification](./shared-resources/credentials.md) qui a accès aux ressources locales. Ces ressources incluent des magasins de certificats et tous les runbooks s’exécutent sous ces informations d’identification sur un runbook Worker hybride dans le groupe.

- Le nom d’utilisateur doit utiliser l’un des formats suivants :

   * domaine\nom d’utilisateur
   * username@domain
   * nom d’utilisateur (pour les comptes locaux sur l’ordinateur local)

- Pour utiliser le runbook PowerShell **Export-RunAsCertificateToHybridWorker**, vous devez installer les modules Az pour Azure Automation sur l’ordinateur local.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Utiliser une ressource d’informations d’identification pour spécifier un compte d’identification

Utilisez la procédure suivante afin de spécifier un compte d’identification pour un groupe de Runbooks Workers hybrides :

1. Créez une [ressource d’information d’identification](./shared-resources/credentials.md) ayant accès aux ressources locales.
1. Dans le portail Azure, ouvrez le compte Automation.
1. Sélectionnez **Groupes de Workers hybrides**, puis sélectionnez le groupe.
1. Sélectionnez **Tous les paramètres**, puis **Paramètres du groupe Worker hybride**.
1. Remplacez la valeur **Par défaut** du compte d’**identification** par **Personnalisé**.
1. Sélectionnez les informations d’identification et cliquez sur **Enregistrer**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installer le certificat du compte d’identification

Dans le cadre de votre processus de génération automatisé pour le déploiement de ressources dans Azure, vous pouvez avoir besoin d’accéder aux systèmes locaux afin de prendre en charge une tâche ou un ensemble d’étapes de votre séquence de déploiement. Pour fournir l’authentification auprès d’Azure avec le compte d’identification, vous devez installer le certificat du compte d’identification.

>[!NOTE]
>Ce runbook PowerShell ne s’exécute pas actuellement sur des machines Linux. Il ne s’exécute que sur des machines Windows.
>

Le runbook PowerShell suivant, appelé **Export-RunAsCertificateToHybridWorker**, exporte le certificat d’identification à partir de votre compte Azure Automation. Le runbook télécharge le certificat et l’importe dans le magasin de certificats de la machine locale sur un runbook Worker hybride connecté au même compte. Une fois cette étape terminée, le runbook vérifie que le Worker peut s’authentifier auprès d’Azure avec le compte d’identification.

>[!NOTE]
>Ce runbook PowerShell n’est pas conçu ni destiné à être exécuté en dehors de votre compte Automation en tant que script sur la machine cible.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Pour les runbooks PowerShell, `Add-AzAccount` et `Add-AzureRMAccount` sont des alias de `Connect-AzAccount`. Quand vous effectuez une recherche dans vos éléments de bibliothèque, si vous ne voyez pas `Connect-AzAccount`, vous pouvez utiliser `Add-AzAccount` ou mettre à jour vos modules dans votre compte Automation.

Pour terminer la préparation du compte d’identification :

1. Enregistrez le runbook **Export-RunAsCertificateToHybridWorker** sur votre ordinateur avec une extension **.ps1**.
1. Importez-le dans votre compte Automation.
1. Modifiez le runbook en remplaçant la valeur de la variable `Password` par votre propre mot de passe.
1. Publier le runbook.
1. Exécutez le runbook en ciblant le groupe de runbooks Workers hybrides qui exécute et authentifie les runbooks avec le compte d’identification. 
1. Examinez le flux des travaux et notez qu’il signale la tentative d’importation du certificat dans le magasin de l’ordinateur local, puis complète par plusieurs lignes. Ce comportement dépend du nombre de comptes Automation définis dans votre abonnement et du résultat de l’authentification.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Utiliser des runbooks signés sur un runbook Worker hybride Windows

Vous pouvez configurer un runbook Worker hybride Windows pour exécuter uniquement des runbooks signés.

> [!IMPORTANT]
> Une fois que vous avez configuré un Runbook Worker hybride pour exécuter uniquement des runbooks signés, les runbooks non signés ne pourront pas s’exécuter sur le Worker.

> [!NOTE]
>  PowerShell 7.x ne prend pas en charge les runbooks signés pour les Runbooks Worker hybrides Windows et Linux.  

### <a name="create-signing-certificate"></a>Créer un certificat de signature

L’exemple suivant crée un certificat auto-signé qui peut être utilisé pour signer des runbooks. Ce code crée le certificat et l’exporte afin que le runbook Worker hybride puisse l’importer ultérieurement. L’empreinte est également retournée à des fins d’utilisation ultérieure pour référencer le certificat.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importer un certificat et configurer des Workers pour la validation de signature

Copiez le certificat que vous avez créé sur chacun des Runbooks Worker hybride d’un groupe. Exécutez le script suivant pour importer le certificat et configurer les Workers de façon à utiliser la validation de signature sur les runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signer vos runbooks avec le certificat

Si les runbooks Workers hybrides sont configurés pour utiliser uniquement des runbooks signés, vous devez signer les runbooks qui doivent être utilisés par les runbooks Workers hybrides. Utilisez l’exemple de code PowerShell suivant pour signer ces runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quand un runbook a été signé, vous devez l’importer dans votre compte Automation et le publier avec le bloc de signature. Pour savoir comment importer des runbooks, consultez [Importer un runbook](manage-runbooks.md#import-a-runbook).

>[!NOTE]
>Utilisez uniquement des caractères de texte brut dans votre code de runbook, y compris les commentaires. L’utilisation de caractères avec des signes diacritiques, comme á ou ñ, génère une erreur. Lorsqu’Azure Automation télécharge votre code, les caractères sont remplacés par un point d’interrogation et la signature échoue avec un message « Échec de validation du hachage de la signature ».

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Utiliser des runbooks signés sur un runbook Worker hybride Linux

Pour pouvoir utiliser des runbooks signés, un runbook Worker hybride Linux doit avoir l’exécutable [GPG](https://gnupg.org/index.html) exécutable sur la machine locale.

> [!IMPORTANT]
> Une fois que vous avez configuré un Runbook Worker hybride pour exécuter uniquement des runbooks signés, les runbooks non signés ne pourront pas s’exécuter sur le Worker.

Pour terminer cette configuration, procédez comme suit :

* Créer un porte-clés et une paire de clés GPG
* Rendre le porte-clés disponible pour le runbook Worker hybride
* Vérifier que la validation de signature est activée
* Signer un runbook

> [!NOTE]
>  PowerShell 7.x ne prend pas en charge les runbooks signés pour les Runbooks Worker hybrides Windows et Linux.

### <a name="create-a-gpg-keyring-and-keypair"></a>Créer un porte-clés et une paire de clés GPG

Pour créer un porte-clés et une paire de clés GPG, utilisez le [compte nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) du Runbook Worker hybride.

1. Utilisez l’application sudo pour vous connecter en tant que compte **nxautomation**.

    ```bash
    sudo su - nxautomation
    ```

1. Une fois que vous utilisez **nxautomation**, générez la paire de clés GPG. GPG vous guide tout au long des étapes. Vous devez fournir un nom, une adresse e-mail, un délai d’expiration et une phrase secrète. Ensuite, patientez jusqu’à ce que l’entropie soit suffisante sur la machine pour que la clé soit générée.

    ```bash
    sudo gpg --generate-key
    ```

1. Étant donné que le répertoire GPG a été généré avec sudo, vous devez remplacer son propriétaire par **nxautomation** à l’aide de la commande suivante.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendre le porte-clés disponible pour le runbook Worker hybride

Une fois le porte-clés créé, vous devez le rendre disponible au runbook Worker hybride. Modifiez le fichier de paramètres **home/nxautomation/state/worker.conf** pour inclure l’exemple de code suivant sous la section de fichier `[worker-optional]`.

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Vérifier que la validation de signature est activée

Si la validation de signature a été désactivée sur la machine, vous devez l’activer en exécutant la commande sudo suivante. Remplacez `<LogAnalyticsworkspaceId>` par l’ID de votre espace de travail.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signer un runbook

Une fois que vous avez configuré la validation de signature, utilisez la commande GPG suivante pour signer le runbook.

```bash
gpg --clear-sign <runbook name>
```

Le runbook signé est appelé **\<runbook name>.asc**.

Vous pouvez maintenant charger le runbook signé sur Azure Automation et l’exécuter comme un runbook normal.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Démarrer un runbook sur un runbook Worker hybride

[Démarrer un runbook dans Azure Automation](start-runbooks.md) décrit différentes méthodes de démarrage d’un runbook. Le démarrage d’un runbook sur un Runbook Worker hybride utilise une option **Exécuter sur** qui vous permet de spécifier le nom d’un groupe de Runbooks Workers hybrides. Quand un groupe est spécifié, l’un des Workers de ce groupe récupère et exécute le runbook. Si votre runbook ne spécifie pas cette option, Azure Automation exécute le runbook comme d’habitude.

Quand vous démarrez un runbook dans le portail Azure, une option **Exécuter sur** vous permet de sélectionner **Azure** ou **Worker hybride**. Si vous sélectionnez **Worker hybride**, vous pouvez choisir le groupe de runbooks Workers hybrides dans une liste déroulante.

Quand vous démarrez un runbook à l’aide de PowerShell, utilisez le paramètre `RunOn` avec l’applet de commande [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). L’exemple suivant utilise Windows PowerShell pour démarrer un runbook nommé **Test-Runbook** sur un groupe de runbooks Workers hybrides nommé MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Journalisation

Pour aider à résoudre les problèmes liés à votre runbooks s’exécutant sur un Runbook Worker hybride, les journaux sont stockés localement dans l’emplacement suivant :

* Sur Windows dans le chemin d’accès `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` pour une journalisation détaillée du processus de runtime du travail. Les événements d’état de travail de runbook de haut niveau sont écrits dans le journal des événements **Application and Services Logs\Microsoft-Automation\Operations**.

* Sur Linux, les journaux du Worker hybride de l’utilisateur se trouvent dans le chemin d’accès `/home/nxautomation/run/worker.log`, et les journaux du Runbook Worker système dans le chemin d’accès `/var/opt/microsoft/omsagent/run/automationworker/worker.log`.

## <a name="next-steps"></a>Étapes suivantes

* Si l’exécution de vos runbooks ne se termine pas correctement, consultez le guide de résolution des problèmes liés aux [échecs d’exécution des runbooks](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Pour plus d’informations sur PowerShell, notamment les références sur le langage et les modules d’apprentissage, consultez [Documentation PowerShell](/powershell/scripting/overview).
* En savoir plus sur [l'utilisation d'Azure Policy pour gérer l'exécution de runbooks](enforce-job-execution-hybrid-worker.md) avec des Runbook Workers hybrides
* Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](/powershell/module/az.automation).
