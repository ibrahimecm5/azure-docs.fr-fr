---
title: Configurer Azure Virtual Desktop pour Azure Stack HCI (version préliminaire) - Azure
description: Comment configurer Azure Virtual Desktop pour Azure Stack HCI (version préliminaire)
author: Heidilohr
ms.topic: how-to
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: a80cdacaebe4cba2dceb1b29b2f512a6148a518b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096359"
---
# <a name="set-up-azure-virtual-desktop-for-azure-stack-hci-preview"></a>Configurer Azure Virtual Desktop pour Azure Stack HCI (version préliminaire)

> [!IMPORTANT]
> Azure Virtual Desktop pour Azure Stack HCI est actuellement en version préliminaire.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Avec Azure Virtual Desktop pour Azure Stack HCI (version préliminaire), vous pouvez utiliser des hôtes de session d’Azure Virtual Desktop dans votre infrastructure HCI Azure Stack locale. Pour plus d’informations, consultez [ Azure Virtual Desktop pour Azure Stack HCI (version préliminaire)](azure-stack-hci-overview.md).

## <a name="requirements"></a>Configuration requise

Pour utiliser Azure Virtual Desktop pour Azure Stack HCI, vous avez besoin des éléments suivants :

- Un [cluster Azure Stack HCI inscrit auprès d’Azure](/azure-stack/hci/deploy/register-with-azure).

- Un abonnement Azure pour la création d’un pool hôte de session d’Azure Virtual Desktop avec toutes les autorisations d’administration requises.

- [Active Directory local (AD) synchronisé avec Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

- Une connexion stable à Azure à partir de votre réseau local.

## <a name="configure-azure-virtual-desktop-for-azure-stack-hci"></a>Configurer Azure Virtual Desktop pour Azure Stack HCI

Pour configurer Azure Virtual Desktop pour Azure Stack HCI :

1. Créez un pool hôte sans ordinateur virtuel en suivant les instructions de [la procédure de démarrage du processus d’installation du pool hôte](create-host-pools-azure-marketplace.md#begin-the-host-pool-setup-process). À la fin de cette section, revenez à cet article et commencez à l’étape 2.

2. Suivez les instructions fournies dans informations sur l'[espace de travail](create-host-pools-azure-marketplace.md#workspace-information) pour créer un espace de travail pour vous-même.

3. Déployez un nouvel ordinateur virtuel sur votre infrastructure Azure Stack HCI en suivant les instructions fournies dans [Créer une machine virtuelle](/azure-stack/hci/manage/vm#create-a-new-vm). Déployez une machine virtuelle avec un système d’exploitation pris en charge et la joindre à un domaine.

   >[!NOTE]
   >Installez le rôle Hôte de session Bureau à distance (RDSH) si la machine virtuelle exécute un système d’exploitation Windows Server.

4. Activez Azure pour gérer la nouvelle machine virtuelle par le biais d’Azure Arc en y installant l’agent de l’ordinateur connecté. Suivez les instructions de [Connecter machines hybrides avec des serveurs Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) pour installer l’agent Windows sur la machine virtuelle.

5. Ajoutez l’ordinateur virtuel au pool d’hôtes Azure Virtual Desktop que vous avez créé précédemment en installant l’[agent Azure Virtual Desktop](agent-overview.md). Après cela, suivez les instructions de la procédure [Inscrire les machines virtuelles au pool d’hôtes d’Azure Virtual Desktop](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool) pour inscrire la machine virtuelle auprès du service d’Azure Virtual Desktop.

6. Suivez les instructions de [Créer des groupes d'applications et gérer les affectations des utilisateurs](manage-app-groups.md) pour créer un groupe d’applications à des fins de test et assigner l’accès des utilisateurs à celui-ci.

7. Accédez au [client Web](./user-documentation/connect-web.md) et accordez à vos utilisateurs l’accès au nouveau déploiement.

## <a name="optional-configurations"></a>Configurations facultatives

Maintenant que vous avez configuré Azure Virtual Desktop pour Azure Stack HCI, voici quelques opérations supplémentaires que vous pouvez effectuer en fonction des besoins de votre déploiement.

### <a name="create-a-profile-container-using-a-file-share-on-azure-stack-hci"></a>Créer un conteneur de profils à l’aide d’un partage de fichiers sur Azure Stack HCI

Pour créer un conteneur de profils à l’aide d’un partage de fichiers :

1. Déployez un partage de fichiers sur un déploiement de machine virtuelle Windows Server ou en cluster unique. Les machines virtuelles Windows Server avec le rôle de serveur de fichiers peuvent également être colocalisées sur le même cluster que celui où sont déployées les machines virtuelles hôtes de session.

2. Connectez-vous à la machine virtuelle avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.

3. Sur la machine virtuelle, lancez le **Panneau de configuration** et sélectionnez **Système**.

4. Sélectionnez Nom de l’ordinateur, **Modifier les paramètres** et **Modifier…** .

5. Sélectionnez **Domaine**, puis entrez le domaine Active Directory sur le réseau virtuel.

6. Authentifiez-vous avec un compte de domaine qui dispose de privilèges d’accès sur les machines de jonction de domaine.

7. Suivez les instructions de [Préparer l’ordinateur virtuel pour qu’il agisse en tant que partage de fichiers](create-host-pools-user-profile.md#prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles) afin de préparer votre machine virtuelle pour le déploiement.

8. Suivez les instructions dans [Configurer le conteneur de profil FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) pour configurer votre conteneur de profil à utiliser.

### <a name="download-supported-os-images-from-azure-marketplace"></a>Télécharger des images de système d’exploitation prises en charge à partir d’Azure Marketplace

Vous pouvez exécuter toutes les images de système d’exploitation qui prennent en charge Azure Virtual Desktop et Azure Stack HCI sur votre déploiement. Pour connaître les systèmes d’exploitation pris en charge par Azure Virtual Desktop, consultez [Images de système d’exploitation de machine virtuelle prises en charge](overview.md#supported-virtual-machine-os-images).

Vous avez le choix entre deux options pour télécharger une image :

- Déployez une machine virtuelle avec votre image de système d’exploitation par défaut, puis suivez les instructions dans [Télécharger un VHD Windows à partir d’Azure](../virtual-machines/windows/download-vhd.md).
- Téléchargez un disque dur virtuel (VHD) Windows à partir d’Azure sans déployer une machine virtuelle.

Le téléchargement d’un disque dur virtuel Windows sans déployer une machine virtuelle présente plusieurs étapes supplémentaires. Pour télécharger un disque dur virtuel à partir d’Azure sans déployer une machine virtuelle, vous devez suivre les instructions des sections suivantes dans l’ordre.

### <a name="requirements-to-download-a-vhd-without-a-vm"></a>Conditions requises pour télécharger un disque dur virtuel sans machine virtuelle

Avant de commencer, assurez-vous que vous êtes connecté à Azure et que vous exécutez [Azure Cloud Shell](../cloud-shell/quickstart.md) dans une invite de commandes ou dans l’environnement bash. Vous pouvez également exécuter des commandes de référence dans l’interface de ligne de commande (CLI) Azure.

Si vous utilisez une installation locale, exécutez la commande [az login](/cli/azure/reference-index#az_login) pour vous connecter à Azure :

Après cela, suivez les autres invites qui s’affichent pour terminer la connexion. Pour connaître les autres options de connexion, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli).

Si c’est la première fois que vous utilisez Azure CLI, installez les extensions requises en suivant les instructions de la procédure [Utiliser des extensions avec le Azure CLI](/cli/azure/azure-cli-extensions-overview).

Enfin, exécutez la commande [az version](/cli/azure/reference-index?#az_version) pour vous assurer que votre client est à jour. Si ce n’est pas le cas, exécutez la commande [az upgrade](/cli/azure/reference-index?#az_upgrade) pour effectuer une mise à niveau vers la version la plus récente.

### <a name="search-azure-marketplace-for-azure-virtual-desktop-images"></a>Rechercher dans la place de marché Azure pour les images d’Azure Virtual Desktop

Vous pouvez trouver l’image que vous recherchez à l’aide de la fonction de **Recherche** dans la place de marché Azure du portail Azure. Pour rechercher des images spécifiquement pour Azure Virtual Desktop, vous pouvez exécuter l’un des exemples de requêtes suivants.

Si vous recherchez Windows 10 multisession, vous pouvez lancer une recherche avec ce critère :

```azure
az vm image list --all --publisher "microsoftwindowsdesktop" --offer "windows-10" --sku "21h1-evd-g2"
```

Cette commande doit retourner l’URN suivant :

```azure
MicrosoftWindowsDesktop:Windows-10:21h1-evd-g2:latest
```

Si vous recherchez Windows Server 2019 datacenter, vous pouvez exécuter les critères suivants dans votre Azure CLI :

```azure
az vm image list --all --publisher "microsoftwindowsserver" --offer "WindowsServer" --sku "2019-Datacenter-gen2"
```

Cette commande doit retourner l’URN suivant :

```azure
MicrosoftWindowsServer:windowsserver-gen2preview:2019-datacenter-gen2:latest
```

>[!IMPORTANT]
>Veillez à utiliser uniquement des images de génération 2 (« Gen »). Azure Virtual Desktop pour Azure Stack HCI ne prend pas en charge la création d’une machine virtuelle avec une image de première génération (« Gen1 »). Évitez les références SKU avec un suffixe « -g1 ».

### <a name="create-a-new-azure-managed-disk-from-the-image"></a>Créer un disque géré par Azure à partir d’une image

Ensuite, vous devez créer un disque géré par Azure à partir de l’image que vous avez téléchargée dans la place de marché Azure.

Pour créer un disque géré par Azure :

1. Exécutez les commandes suivantes dans une invite de ligne de commande Azure pour définir les paramètres de votre disque géré. Veillez à remplacer les éléments entre crochets par les valeurs correspondant à votre scénario.

```azure
$urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
$diskName = <disk name> #Name for new disk to be created
$diskRG = <resource group> #Resource group that contains the new disk
```

2. Exécutez les commandes suivantes pour créer le disque et générer une URL d’accès SAS (Serial Attached SCSI).

```azure
az disk create -g $diskRG -n $diskName --image-reference $urn
$sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
$diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
```

### <a name="export-a-vhd-from-the-managed-disk-to-azure-stack-hci-cluster"></a>Exporter un disque dur virtuel à partir du disque géré vers Azure Stack cluster HCI

Après cela, vous devez exporter le disque dur virtuel que vous avez créé à partir du disque géré vers votre cluster Azure Stack HCI, ce qui vous permet de créer de nouvelles machines virtuelles. Vous pouvez utiliser la méthode suivante dans un navigateur web normal ou Explorateur Stockage.

Pour exporter le disque dur virtuel :

1. Ouvrez un navigateur et accédez à l’URL SAS du disque géré que vous avez généré dans [Créer un nouveau disque géré par Azure à partir de l’image](#create-a-new-azure-managed-disk-from-the-image). Vous pouvez télécharger l’image de disque dur virtuel pour l’image que vous avez téléchargée sur la place de marché Azure à cette URL.

2. Téléchargez l’image de disque dur virtuel. Le processus de téléchargement peut durer plusieurs minutes, donc soyez patient. Assurez-vous que l’image a été entièrement téléchargée avant de passer à la section suivante.

### <a name="clean-up-the-managed-disk"></a>Nettoyez le disque managé

Lorsque vous avez terminé avec votre disque dur virtuel, vous devez libérer de l’espace en supprimant le disque géré.

Pour supprimer le disque managé que vous avez créé, procédez comme suit :

```azure
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes
```

Cette commande peut prendre quelques minutes, donc soyez patient.

## <a name="next-steps"></a>Étapes suivantes

Si vous devez actualiser votre mémoire à propos des informations de base ou de tarification, accédez à [Azure Virtual Desktop pour Azure Stack HCI](azure-stack-hci-overview.md).

Si vous avez d’autres questions, consultez notre [FAQ](azure-stack-hci-faq.yml).
