---
title: Monter un partage de fichiers SMB Azure sur Linux | Microsoft Docs
description: Découvrez comment monter un partage de fichiers Azure via SMB sur Linux. Consultez la liste des conditions requises. Passez en revue les considérations relatives à la sécurité SMB sur les clients Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 417955b723ef5b5d70583c7b101eb39fe29fa694
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179204"
---
# <a name="mount-smb-azure-file-share-on-linux"></a>Monter un partage de fichiers SMB Azure sur Linux
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans des distributions Linux à l’aide du [client SMB en mode noyau](https://wiki.samba.org/index.php/LinuxCIFS).

La méthode recommandée pour monter un partage de fichiers Azure sur Linux est d’utiliser SMB 3.1.1. Par défaut, Azure Files exige un chiffrement en transit, que SMB 3.0+ prend en charge. Azure Files prend aussi en charge SMB 2.1, qui ne prend pas en charge le chiffrement en transit, mais des impératifs de sécurité peuvent vous interdire de monter des partages de fichiers Azure avec SMB 2.1 à partir d’une autre région Azure ou de votre environnement local. À moins que votre application nécessite spécifiquement SMB 2.1, utilisez SMB 3.1.1.

| Distribution | SMB 3.1.1 | SMB 3.0 |
|-|-----------|---------|
| Version de noyau Linux | <ul><li>Prise en charge 3.1.1 de base :4.17</li><li>Montage par défaut : 5.0</li><li>Chiffrement AES-128-GCM : 5.3</li></ul> | <ul><li>Prise en charge 3.0 de base : 3.12</li><li>Chiffrement AES-128-CCM : 4.11</li></ul> |
| [Ubuntu](https://wiki.ubuntu.com/Releases) | Chiffrement AES-128-GCM : 18.04.5 LTS+ | Chiffrement AES-128-CCM : 16.04.4 LTS+ |
| [Red Hat Enterprise Linux (RHEL)](https://access.redhat.com/articles/3078) | <ul><li>De base : 8.0+</li><li>Montage par défaut : 8.2+</li><li>Chiffrement AES-128-GCM : 8.2+</li></ul> | 7.5+ |
| [Debian](https://www.debian.org/releases/) | De base : 10+ | Chiffrement AES-128-CCM : 10+ |
| [SUSE Linux Enterprise Server](https://www.suse.com/support/kb/doc/?id=000019587) | Chiffrement AES-128-GCM : 15 SP2+ | Chiffrement AES-128-CCM : 12 SP2+ |

Si votre distribution Linux n’est pas indiquée dans le tableau ci-dessus, vous pouvez vérifier la version du noyau Linux à l’aide de la commande `uname` :

```bash
uname -r
```

> [!Note]  
> La prise en charge de SMB 2.1 a été ajoutée à la version 3.7 du noyau Linux. Si vous utilisez une version du noyau Linux après la 3.7, elle devrait prendre en charge SMB 2.1.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Conditions préalables requises
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Vérifiez que le package cifs-utils est installé.**  
    Le package cifs-utils peut être installé à l’aide du gestionnaire de package sur la distribution Linux de votre choix. 

    Sur **Ubuntu** et **Debian**, utilisez le gestionnaire de packages `apt` :

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Sur **Red Hat Enterprise Linux 8 +** , utilisez le `dnf` Gestionnaire de package :

    ```bash
    sudo dnf install cifs-utils
    ```

    Sur les versions plus anciennes de **Red Hat Enterprise Linux**, utilisez le gestionnaire de packages `yum` :

    ```bash
    sudo yum install cifs-utils 
    ```

    Sur **SUSE Linux Enterprise Server**, utilisez le gestionnaire de package `zypper` :

    ```bash
    sudo zypper install cifs-utils
    ```

    Sur les autres distributions, utilisez le gestionnaire de packages approprié ou effectuez une [compilation à partir de la source](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **La dernière version de l’interface de ligne de commande Azure (CLI).** Pour plus d'informations sur l'installation d’Azure CLI, consultez [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli) et sélectionnez votre système d’exploitation. Si vous préférez utiliser le module Azure PowerShell dans PowerShell 6+, rien ne vous en empêche. Sachez cependant que les instructions de cet article s’appliquent à Azure CLI.

* **Vérifiez que le port 445 est ouvert** : SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 de la machine cliente.  Remplacez `<your-resource-group>` et `<your-storage-account>`, puis exécutez le script suivant :

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" --output tsv | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Si la connexion a été établie, vous devez obtenir une sortie semblable à la suivante :

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Si vous ne parvenez pas à ouvrir le port 445 sur votre réseau d’entreprise ou si vous n’y êtes pas autorisé par un fournisseur de services Internet, vous pouvez utiliser une connexion VPN ou ExpressRoute pour contourner le port 445. Pour plus d’informations, consultez [Considérations relatives à la mise en réseau pour un accès direct à un partage de fichiers Azure](storage-files-networking-overview.md).

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montage du partage de fichiers Azure à la demande avec mount
Lorsque vous montez un partage de fichiers sur un système d’exploitation Linux, votre partage de fichiers distant est représenté sous la forme d’un dossier dans votre système de fichiers local. Vous pouvez monter des partages de fichiers sur n’importe quel emplacement de votre système. L’exemple suivant monte sous le chemin d’accès `/mount`. Vous pouvez remplacer cela par le chemin d’accès par défaut que vous souhaitez en modifiant la variable `$mntRoot`.

Remplacez `<resource-group-name>`, `<storage-account-name>` et `<file-share-name>` par les informations correspondant à votre environnement :

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"
fileShareName="<file-share-name>"

mntRoot="/mount"
mntPath="$mntRoot/$storageAccountName/$fileShareName"

sudo mkdir -p $mntPath
```

Ensuite, montez le partage de fichiers à l’aide de la commande `mount`. Dans l’exemple suivant, la commande `$smbPath` est remplie à l’aide du nom de domaine complet pour le point de terminaison de fichier du compte de stockage et `$storageAccountKey` est renseigné avec la clé de compte de stockage. 

# <a name="smb-311"></a>[SMB 3.1.1](#tab/smb311)
> [!Note]  
> À partir de la version 5.0 du noyau Linux, SMB 3.1.1 est le protocole négocié par défaut. Si vous utilisez une version du noyau Linux antérieure à 5.0, spécifiez `vers=3.1.1` dans la liste d’options de montage.  

```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-30"></a>[SMB 3.0](#tab/smb30)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-21"></a>[SMB 2.1](#tab/smb21)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=2.1,username=$storageAccountName,password=$storageAccountKey,serverino
```

---

Vous pouvez utiliser `uid`/`gid` ou `dir_mode` et `file_mode` dans les options de montage de la commande `mount` pour définir des autorisations. Pour plus d’informations sur la façon de définir les autorisations, consultez la [notation numérique UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) sur Wikipédia.

Vous pouvez également monter le même partage de fichiers Azure sur plusieurs points de montage, si vous le souhaitez. Quand vous avez terminé d’utiliser le partage de fichiers Azure, utilisez `sudo umount $mntPath` pour démonter le partage.

## <a name="automatically-mount-file-shares"></a>Monter automatiquement les partages de fichiers
Lorsque vous montez un partage de fichiers sur un système d’exploitation Linux, votre partage de fichiers distant est représenté sous la forme d’un dossier dans votre système de fichiers local. Vous pouvez monter des partages de fichiers sur n’importe quel emplacement de votre système. L’exemple suivant monte sous le chemin d’accès `/mount`. Vous pouvez remplacer cela par le chemin d’accès par défaut que vous souhaitez en modifiant la variable `$mntRoot`.

```bash
mntRoot="/mount"
sudo mkdir -p $mntRoot
```

Pour monter un partage de fichiers Azure sur Linux, utilisez le nom du compte de stockage comme nom d’utilisateur du partage de fichiers et la clé du compte de stockage comme mot de passe. Étant donné que les informations d’identification du compte de stockage peuvent changer au fil du temps, vous devez stocker les informations d’identification du compte de stockage séparément de la configuration de montage. 

L’exemple suivant montre comment créer un fichier pour stocker les informations d’identification. N’oubliez pas de remplacer `<resource-group-name>` et `<storage-account-name>` par les informations correspondant à votre environnement.

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"

# Create a folder to store the credentials for this storage account and
# any other that you might set up.
credentialRoot="/etc/smbcredentials"
sudo mkdir -p "/etc/smbcredentials"

# Get the storage account key for the indicated storage account.
# You must be logged in with az login and your user identity must have 
# permissions to list the storage account keys for this command to work.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

# Create the credential file for this individual storage account
smbCredentialFile="$credentialRoot/$storageAccountName.cred"
if [ ! -f $smbCredentialFile ]; then
    echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
    echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
else 
    echo "The credential file $smbCredentialFile already exists, and was not modified."
fi

# Change permissions on the credential file so only root can read or modify the password file.
sudo chmod 600 $smbCredentialFile
```

Pour monter automatiquement un partage de fichiers, vous avez le choix entre l’utilisation d’un montage statique via l’utilitaire `/etc/fstab` ou l’utilisation d’un montage dynamique via l’utilitaire `autofs`. 

### <a name="static-mount-with-etcfstab"></a>Montage statique avec /etc/fstab
À l’aide de l’environnement précédent, créez un dossier pour votre compte de stockage/partage de fichiers dans votre dossier de montage. Remplacez `<file-share-name>` par le nom approprié de votre partage de fichiers Azure.

```bash
fileShareName="<file-share-name>"

mntPath="$mntRoot/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath
```

Enfin, créez un enregistrement dans le fichier `/etc/fstab` de votre partage de fichiers Azure. Dans la commande ci-dessous, le fichier Linux 0755 et les autorisations de dossier par défaut sont utilisés, ce qui signifie lecture, écriture et exécution pour le propriétaire (en fonction du propriétaire Linux des fichiers/répertoires), lecture et exécution pour les utilisateurs du groupe propriétaire, et lecture et exécution pour les autres utilisateurs du système. Vous souhaiterez peut-être des valeurs `uid` et `gid` alternatives ou les autorisations `dir_mode` et `file_mode` sur le montage. Pour plus d’informations sur la façon de définir les autorisations, consultez la [notation numérique UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) sur Wikipédia.

```bash
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
    echo "$smbPath $mntPath cifs nofail,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
else
    echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
fi

sudo mount -a
```

> [!Note]  
> À partir de la version 5.0 du noyau Linux, SMB 3.1.1 est le protocole négocié par défaut. Vous pouvez spécifier d’autres versions de protocole à l’aide de l’option de montage `vers` (les versions de protocole sont `3.1.1`, `3.0` et `2.1`).

### <a name="dynamically-mount-with-autofs"></a>Montage dynamique avec autofs
Pour monter un partage de fichiers de manière dynamique avec l’utilitaire `autofs`, installez-le à l’aide du gestionnaire de package sur la distribution Linux de votre choix.  

Sur les distributions **Ubuntu** et **Debian**, utilisez le gestionnaire de packages `apt` :

```bash
sudo apt update
sudo apt install autofs
```

Sur **Red Hat Enterprise Linux 8+** , utilisez le `dnf` Gestionnaire de package :
```bash
sudo dnf install autofs
```

Pour les versions plus anciennes de **Red Hat Enterprise Linux**, utilisez le gestionnaire de packages `yum` :

```bash
sudo yum install autofs 
```

Sur **SUSE Linux Enterprise Server**, utilisez le gestionnaire de package `zypper` :
```bash
sudo zypper install autofs
```

Ensuite, mettez à jour les fichiers de configuration `autofs`. 

```bash
fileShareName="<file-share-name>"

httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath" > /etc/auto.fileshares

echo "/fileshares /etc/auto.fileshares --timeout=60" > /etc/auto.master
```

La dernière étape consiste à redémarrer le service `autofs`.

```bash
sudo systemctl restart autofs
```

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files :

- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Supprimer SMB 1 de Linux](files-remove-smb1-linux.md)
- [Dépannage](storage-troubleshoot-linux-file-connection-problems.md)
