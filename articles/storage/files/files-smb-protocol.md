---
title: Partages de fichiers SMB dans Azure Files
description: En savoir plus sur les partages de fichiers hébergés dans Azure Files à l’aide du protocole SMB (Server Message Block).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 23146fecfcb27e8f468a693804fff5e559866b38
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522020"
---
# <a name="smb-file-shares-in-azure-files"></a>Partages de fichiers SMB dans Azure Files
Azure Files propose deux protocoles standard pour le montage d’un partage de fichiers Azure : le protocole [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) et le protocole [NFS](https://en.wikipedia.org/wiki/Network_File_System). Azure Files vous permet de choisir le protocole de système de fichiers le mieux adapté à votre charge de travail. Les partages de fichiers Azure ne prennent pas en charge l’accès à un partage de fichiers Azure individuel avec les protocoles SMB et NFS, même si vous pouvez créer des partages de fichiers SMB et NFS au sein du même compte de stockage. Pour tous les partages de fichiers, Azure Files propose des partages de fichiers d’entreprise qui peuvent être mis à l’échelle pour répondre à vos besoins de stockage et accessibles simultanément par des milliers de clients.

Cet article traite des partages de fichiers Azure SMB. Pour plus d’informations sur les partages de fichiers NFS Azure, consultez [partages de fichiers NFS dans Azure Files](files-nfs-protocol.md).

## <a name="common-scenarios"></a>Scénarios courants
Les partages de fichiers SMB sont utilisés pour diverses applications, notamment les partages de fichiers et les partages de fichiers de l’utilisateur final qui stockent les bases de données et les applications. Les partages de fichiers SMB sont souvent utilisés dans les scénarios suivants :

- Les partages de fichiers des utilisateurs finaux tels que les partages d’équipe, les répertoires de base, etc.
- Sauvegarde du stockage des applications Windows, telles que des bases de données SQL Server ou des applications métier écrites pour des api de système de fichiers local Win32 ou .net. 
- Nouveau développement d’applications et de services, en particulier si cette application ou ce service est requis pour les e/s aléatoires et le stockage hiérarchique.

## <a name="features"></a>Fonctionnalités
Azure Files prend en charge les principales fonctionnalités de SMB et Azure nécessaires pour les déploiements de production de partages de fichiers SMB :

- Jonction de domaine Active Directory et listes de contrôle d’accès discrétionnaire (DACL).
- Sauvegarde serverless intégrée avec la Sauvegarde Azure.
- Isolement réseau avec points de terminaison privés Azure.
- Débit réseau élevé utilisant SMB Multichannel (partages de fichiers Premium uniquement).
- Chiffrement de canal SMB, notamment AES-256-GCM, AES-128-GCM et AES-128-CCM.
- Prise en charge des versions précédentes via les captures instantanées de partage intégrées VSS.
- Suppression réversible automatique sur les partages de fichiers Azure pour empêcher les suppressions accidentelles.
- Partage de fichiers éventuellement accessible sur Internet avec SMB 3.0 + sécurisé pour Internet.

Les partages de fichiers SMB peuvent être montés directement localement ou peuvent également être [mis en cache localement avec Azure File Sync](../file-sync/file-sync-introduction.md).  

## <a name="security"></a>Sécurité
Toutes les données stockées dans Azure Files sont chiffrées au repos avec Azure Storage Service Encryption (SSE). Cette fonctionnalité fonctionne de la même façon que BitLocker sur Windows : les données sont chiffrées sous le niveau du système de fichiers. Comme les données sont chiffrées sous le système de fichiers du partage de fichiers Azure, car codées sur le disque, il n’est pas nécessaire d’avoir accès à la clé sous-jacente sur le client pour lire ou écrire sur le partage de fichiers Azure. Le chiffrement au repos s’applique aux protocoles SMB et NFS.

Par défaut, le chiffrement en transit est activé pour tous les comptes de stockage Azure. Cela signifie que, quand vous montez un partage de fichiers sur SMB (ou y accédez en utilisant le protocole FileREST), Azure Files autorise la connexion uniquement si elle est établie à l’aide des protocoles SMB 3.x avec chiffrement ou HTTPS. Les clients qui ne prennent pas en charge le protocole SMB 3.x avec le chiffrement SMB ne pourront pas monter le partage de fichiers Azure si le chiffrement en transit est activé. 

Azure Files prend en charge AES-256-GCM avec SMB 3.1.1 lorsqu’il est utilisé avec Windows Server 2022 ou Windows 11. SMB 3.1.1 prend également en charge AES-128-GCM et SMB 3.0 prend en charge AES-128-CCM. AES-128-GCM est négocié par défaut sur Windows 10, version 21H1 pour des raisons de performances.

Vous pouvez désactiver le chiffrement en transit pour un compte de stockage Azure. Quand le chiffrement est désactivé, Azure Files autorise également les protocoles SMB 2.1 et SMB 3.x sans chiffrement. La principale raison justifiant de désactiver le chiffrement en transit est la nécessité de prendre en charge une application héritée devant être exécutée sur un système d’exploitation plus ancien, tel que Windows Server 2008 R2 ou une distribution Linux non récente. Azure Files n’autorise que les connexions SMB 2.1 au sein de la même région Azure que le partage de fichiers Azure. Ainsi, un client SMB 2.1 situé en dehors de la région Azure dans laquelle se trouve le partage de fichiers Azure, par exemple, localement ou dans une autre région Azure, ne peut pas accéder au partage de fichiers.

## <a name="smb-protocol-settings"></a>Paramètres de protocole SMB
Azure Files offre plusieurs paramètres qui affectent le comportement, les performances et la sécurité du protocole SMB. Ceux-ci sont configurés pour tous les partages de fichiers Azure dans un compte de stockage.

### <a name="smb-multichannel"></a>SMB Multichannel
SMB Multichannel permet à un client SMB 3.x d’établir plusieurs connexions réseau à un partage de fichiers SMB. Azure Files prend en charge SMB Multichannel sur des partages de fichiers Premium (partages de fichiers dans le type de compte de stockage FileStorage). L’activation de SMB Multichannel dans Azure Files n’occasionne aucun coûts supplémentaires. Par défaut, SMB Multichannel est désactivé.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour afficher le statut de SMB Multichannel, naviguez vers le compte de stockage qui contient vos partages de fichiers premium et sélectionnez **Partages de fichiers** sous l’en-tête **Stockage de données** dans la table de contenus du compte de stockage. Le statut de SMB Multichannel apparaît dans la section **Paramètres de partage de fichiers**.

![Capture d’écran de la section des partages de fichiers avec dans le compte de stockage mettant en évidence le paramètre SMB Multichannel](./media/files-smb-protocol/1-smb-multichannel-enable.png)

Pour activer ou désactiver SMB Multichannel, sélectionnez le statut actuel (**Activé** ou **Désactivé** selon l’état). La boîte de dialogue qui s’affiche contient un bouton bascule pour activer ou désactiver SMB Multichannel. Sélectionnez le statut souhaité, puis sélectionnez **Enregistrer**.

:::image type="content" source="media/files-smb-protocol/2-smb-multichannel-enable.png" alt-text="Capture d’écran de la boîte de dialogue permettant d’activer/désactiver la fonctionnalité SMB Multichannel":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour obtenir le statut de SMB Multichannel, utilisez la cmdlet `Get-AzStorageFileServiceProperty`. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement préalablement à l’exécution de ces commandes PowerShell.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$defaultSmbMultichannelEnabled = $false

# Get the current value for SMB Multichannel
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $defaultSmbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }
```

Pour activer/désactiver SMB Multichannel, utilisez la cmdlet `Update-AzStorageFileServiceProperty`.

```PowerShell
Update-AzStorageFileServiceProperty `
    -StorageAccount $storageAccount `
    -EnableSmbMultichannel $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour obtenir le statut de SMB Multichannel, utilisez la commande `az storage account file-service-properties show`. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement avant d’exécuter ces commandes Bash.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 

## Search strings
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"

# Replacement values for null parameters. 
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"

# Print returned settings
echo $protocolSettings
```

Pour activer/désactiver SMB Multichannel, utilisez la commande `az storage account file-service-properties update`.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-smb-multichannel "true"
```
---

### <a name="smb-security-settings"></a>Paramètres de sécurité SMB
Azure Files expose les paramètres qui vous permettent d’activer/désactiver le protocole SMB pour qu’il soit plus compatible ou plus sécurisé, selon les besoins de votre organisation. Par défaut, Azure Files est configuré pour une compatibilité maximale, gardez à l’esprit que la restriction de ces paramètres peut empêcher certains clients de se connecter.

Azure Files expose les paramètres suivants :

- **Versions SMB** : Quelles sont les versions SMB autorisées ? Les versions de protocole prises en charge sont SMB 3.1.1, SMB 3.0 et SMB 2.1. Par défaut, toutes les versions SMB sont autorisées, même si SMB 2.1 n’est pas autorisée si l’option « exiger un transit sécurisé » est activée, car SMB 2.1 ne prend pas en charge le chiffrement en transit.
- **Méthodes d’authentification** : Quelles sont les méthodes d’authentification SMB autorisées ? Les méthodes d’authentification prises en charge sont NTLMv2 et Kerberos. Par défaut, toutes les méthodes d’authentification sont autorisées. La suppression de NTLMv2 interdit l’utilisation de la clé de compte de stockage pour monter le partage de fichiers Azure.
- **Chiffrement de ticket Kerberos** : Quels sont les algorithmes de chiffrement autorisés ? Les algorithmes de chiffrement pris en charge sont RC4-HMAC et AES-256.
- **Chiffrement de canal SMB** : les algorithmes de chiffrement de canal SMB sont autorisés. Les algorithmes de chiffrement pris en charge sont AES-256-GCM, AES-128-GCM et AES-128-CCM.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Les paramètres de sécurité du protocole SMB peuvent être affichés et modifiés à l’aide de PowerShell ou de l’interface CLI. Sélectionnez l’onglet de votre choix pour connaître les étapes à suivre pour obtenir et définir les paramètres de sécurité SMB.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour obtenir les paramètres de protocole SMB, utilisez l’applet de commande `Get-AzStorageFileServiceProperty`. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement préalablement à l’exécution de ces commandes PowerShell.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

# Gets the current values of the SMB security settings
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

Selon les exigences de sécurité, de performances et de compatibilité de votre organisation, vous souhaiterez peut-être modifier les paramètres du protocole SMB. La commande PowerShell suivante restreint vos partages de fichiers SMB aux options les plus sécurisées.

> [!Important]  
> La restriction des partages de fichiers Azure SMB aux options les plus sécurisées peut empêcher certains clients de se connecter s’ils ne satisfont pas à la configuration requise. Par exemple, AES-256-GCM a été introduit comme option pour le chiffrement de canal SMB dans Windows 2022 et Windows 11. Cela signifie que les clients plus anciens qui ne prennent pas en charge AES-256-GCM ne pourront pas se connecter.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour connaître l’état des paramètres de sécurité SMB, utilisez la commande `az storage account file-service-properties show`. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement avant d’exécuter ces commandes Bash.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values.

# Values to be replaced
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

Selon les exigences de sécurité, de performances et de compatibilité de votre organisation, vous souhaiterez peut-être modifier les paramètres du protocole SMB. La commande Azure CLI suivante restreint vos partages de fichiers SMB aux options les plus sécurisées.

> [!Important]  
> La restriction des partages de fichiers Azure SMB aux options les plus sécurisées peut empêcher certains clients de se connecter s’ils ne satisfont pas à la configuration requise. Par exemple, AES-256-GCM a été introduit comme option pour le chiffrement de canal SMB dans Windows 2022 et Windows 11. Cela signifie que les clients plus anciens qui ne prennent pas en charge AES-256-GCM ne pourront pas se connecter.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>Limites
Les partages de fichiers SMB dans Azure Files prennent en charge un sous-ensemble de fonctionnalités prises en charge par le protocole SMB et le système de fichiers NTFS. Bien que la plupart des cas d’utilisation et des applications ne requièrent pas ces fonctionnalités, certaines applications peuvent ne pas fonctionner correctement avec Azure Files si elles s’appuient sur des fonctionnalités non prises en charge. Les fonctionnalités suivantes ne sont pas prises en charge :

- [SMB Direct](/windows-server/storage/file-server/smb-direct)  
- Bail de répertoire SMB
- [VSS pour les partages de fichiers SMB](/archive/blogs/clausjor/vss-for-smb-file-shares) (cela permet aux fournisseurs VSS de vider leurs données sur le partage de fichiers SMB avant la prise d’une capture instantanée)
- Autres flux de données
- Attributs étendus
- Identificateurs d'objet
- [Liens physiques](/windows/win32/fileio/hard-links-and-junctions)
- [Liens virtuels](/windows/win32/fileio/creating-symbolic-links)  
- [Points d’analyse](/windows/win32/fileio/reparse-points)  
- [Fichiers partiellement alloués](/windows/win32/fileio/sparse-files)
- [Noms de fichier courts (alias 8.3)](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [Compression](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>Disponibilité régionale
Les partages de fichiers Azure SMB sont disponibles dans chaque région Azure, y compris toutes les régions publiques et souveraines. Les partages de fichiers SMB Premium sont disponibles dans [un sous-ensemble de régions](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Étapes suivantes
- [Planifier un déploiement Azure Files](storage-files-planning.md)
- [Crée un partage de fichiers Azure](storage-how-to-create-file-share.md)
- Montez les partages de fichiers SMB sur le système d’exploitation de votre choix :
    - [Montage des partages de fichiers SMB sur Windows](storage-how-to-use-files-windows.md)
    - [Montage des partages de fichiers SMB sur Linux](storage-how-to-use-files-linux.md)
    - [Montage des partages de fichiers SMB sur macOS](storage-how-to-use-files-mac.md)