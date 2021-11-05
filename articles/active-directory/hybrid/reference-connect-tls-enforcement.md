---
title: 'Azure AD Connect : mise en œuvre du protocole TLS 1.2 pour Azure Active Directory Connect | Microsoft Docs'
description: Découvrez comment forcer votre serveur Azure AD Connect à utiliser uniquement le protocole TLS (Transport Layer Security) 1.2.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 9/14/2021
ms.subservice: hybrid
ms.author: rodejo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a869d4265de95001098068791d8b2adffd10347
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057454"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>Mise en œuvre du protocole TLS 1.2 pour Azure AD Connect

Le protocole TLS (Transport Layer Security) version 1.2 est un protocole de chiffrement conçu pour fournir des communications sécurisées. Le protocole TLS vise principalement à assurer la confidentialité et l’intégrité des données. Ce protocole a subi de nombreuses itérations avec la version 1.2 définie dans le document [RFC 5246](https://tools.ietf.org/html/rfc5246). Azure Active Directory Connect version 1.2.65.0 et versions ultérieures ne prend désormais entièrement en charge que le protocole TLS 1.2 pour les communications avec Azure. Cet article fournit des informations sur la façon de forcer votre serveur Azure AD Connect à utiliser uniquement TLS 1.2.

> [!NOTE]
> Toutes les versions de Windows Server qui sont prises en charge pour Azure AD Connect V2.0 utilisent déjà le protocole TLS 1.2 par défaut. Si votre serveur ne prend pas en charge TLS 1.2, vous devez l’activer avant de pouvoir déployer Azure AD Connect v2.0.

## <a name="update-the-registry"></a>Mettre à jour le registre
Pour forcer le serveur Azure AD Connect à utiliser uniquement le protocole TLS 1.2, le registre du serveur Windows doit être mis à jour. Définissez les clés de registre suivantes sur le serveur Azure AD Connect.

> [!IMPORTANT]
> Une fois que vous avez mis à jour le registre, vous devez redémarrer le serveur Windows pour que les modifications prennent effet.


### <a name="enable-tls-12"></a>Activer le protocole TLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:00000001
  - "SchUseStrongCrypto"=dword:0000001
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:00000001
  - "SchUseStrongCrypto"=dword:00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "Enabled"=dword:00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "DisabledByDefault"=dword:00000000 
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "Enabled"=dword:00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "DisabledByDefault"=dword:00000000

### <a name="powershell-script-to-check-tls-12"></a>Script PowerShell pour vérifier le protocole TLS 1.2
Vous pouvez utiliser le script PowerShell suivant pour vérifier les paramètres actuels du protocole TLS 1.2 sur votre serveur Azure AD Connect.

```powershell

Function Get-ADSyncToolsTls12RegValue
{
    [CmdletBinding()]
    Param
    (
        # Registry Path
        [Parameter(Mandatory=$true,
                   Position=0)]
        [string]
        $RegPath,

        # Registry Name
        [Parameter(Mandatory=$true,
                   Position=1)]
        [string]
        $RegName
    )
    $regItem = Get-ItemProperty -Path $RegPath -Name $RegName -ErrorAction Ignore
    $output = "" | select Path,Name,Value
    $output.Path = $RegPath
    $output.Name = $RegName

    If ($regItem -eq $null)
    {
        $output.Value = "Not Found"
    }
    Else
    {
        $output.Value = $regItem.$RegName
    }
    $output
}

$regSettings = @()
$regKey = 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SystemDefaultTlsVersions'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SchUseStrongCrypto'

$regKey = 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SystemDefaultTlsVersions'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'SchUseStrongCrypto'

$regKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'Enabled'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'DisabledByDefault'

$regKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'Enabled'
$regSettings += Get-ADSyncToolsTls12RegValue $regKey 'DisabledByDefault'

$regSettings

```
Exemple de sortie montrant une configuration TLS 1.2 correcte :

![image](https://user-images.githubusercontent.com/38323403/138769930-2cdd290b-8f18-4f52-8a80-c12e5f8a56ba.png)

### <a name="powershell-script-to-enable-tls-12"></a>Script PowerShell pour activer le protocole TLS 1.2
Vous pouvez utiliser le script PowerShell suivant pour activer le protocole TLS 1.2 sur votre serveur Azure AD Connect.

```powershell

If (-Not (Test-Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319'))
{
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319'))
{
    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server'))
{
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client'))
{
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord' -Force | Out-Null

Write-Host 'TLS 1.2 has been enabled. You must restart the Windows Server for the changes to take affect.' -ForegroundColor Cyan

```

### <a name="disable-tls-12"></a>Désactiver le protocole TLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:00000000
  - "SchUseStrongCrypto"=dword:0000000
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319]
  - "SystemDefaultTlsVersions"=dword:00000000
  - "SchUseStrongCrypto"=dword:00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "Enabled"=dword:00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
  - "DisabledByDefault"=dword:00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "Enabled"=dword:00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
  - "DisabledByDefault"=dword:00000001 

### <a name="powershell-script-to-disable-tls-12-not-recommended"></a>Script PowerShell pour désactiver le protocole TLS 1.2 (non recommandé)
Vous pouvez utiliser le script PowerShell suivant pour désactiver le protocole TLS 1.2 sur votre serveur Azure AD Connect.

```powershell

If (-Not (Test-Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319'))
{
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '0' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '0' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319'))
{
    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SystemDefaultTlsVersions' -Value '0' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name 'SchUseStrongCrypto' -Value '0' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server'))
{
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'Enabled' -Value '0' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Name 'DisabledByDefault' -Value '1' -PropertyType 'DWord' -Force | Out-Null

If (-Not (Test-Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client'))
{
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
}
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'Enabled' -Value '0' -PropertyType 'DWord' -Force | Out-Null
New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Name 'DisabledByDefault' -Value '1' -PropertyType 'DWord' -Force | Out-Null

Write-Host 'TLS 1.2 has been disabled. You must restart the Windows Server for the changes to take affect.' -ForegroundColor Cyan

```

## <a name="next-steps"></a>Étapes suivantes
* [Intégration des identités locales dans Azure Active Directory](whatis-hybrid-identity.md)
