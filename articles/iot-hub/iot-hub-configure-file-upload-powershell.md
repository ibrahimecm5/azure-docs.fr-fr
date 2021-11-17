---
title: Utilisation de Azure PowerShell pour configurer le chargement du fichier | Microsoft Docs
description: Comment utiliser les applets de commande Azure PowerShell pour configurer votre IoT Hub afin d’activer les téléchargements de fichiers à partir d’appareils connectés. Comprend des informations sur la configuration du compte de stockage Azure de destination.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f20236edd7a905f05d59ddafd3132569a851816a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553930"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurer les chargements de fichiers IoT Hub à l’aide de Powershell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Cet article explique comment configurer des chargements de fichiers sur votre hub IoT à l’aide de PowerShell. 

Pour utiliser la [fonctionnalité de chargement de fichiers dans IoT Hub](iot-hub-devguide-file-upload.md), vous devez d’abord associer un compte de stockage Azure et un conteneur de blobs à votre hub IoT. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers. En plus du compte de stockage et du conteneur de blobs, vous pouvez définir la durée de vie de l’URI SAP et configurer les paramètres des notifications facultatives de chargement de fichiers qu’IoT Hub peut livrer aux services principaux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

* Un IoT Hub Azure. Si vous n’avez pas de IoT Hub, vous pouvez utiliser [la cmdlet New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) afin d’en créer un ou utiliser le portail pour [créer un IoT Hub](iot-hub-create-through-portal.md).

* Un compte de stockage Azure. Si vous n’avez pas de compte de stockage Azure, vous pouvez utiliser [les cmdlets PowerShell de stockage Azure](/powershell/module/az.storage/) afin d’en créer un ou utiliser le portail pour [créer un compte de stockage](../storage/common/storage-account-create.md).

* Utilisez l’environnement PowerShell dans [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md).

   [![Lancer Cloud Shell dans une nouvelle fenêtre](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* Si vous préférez, [installez](/powershell/scripting/install/installing-powershell) PowerShell localement.

  * [Installez le module PowerShell Azure Az](/powershell/azure/install-az-ps). (Le module est installé par défaut dans l’environnement PowerShell d’Azure Cloud Shell.) 
  * Connectez-vous à PowerShell à l’aide de la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).  Pour finir le processus d’authentification, suivez les étapes affichées dans votre terminal.  Pour connaître les autres options de connexion, consultez [Se connecter avec Azure PowerShell](/powershell/azure/authenticate-azureps).


## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Vous connecter à votre compte Azure et sélectionner votre abonnement. Si vous utilisez Azure Cloud Shell, vous devez être déjà connecté. Toutefois, vous devrez peut-être sélectionner votre abonnement Azure si vous avez plusieurs abonnements.

1. À l’invite PowerShell, exécutez la cmdlet **Connect-AzAccount** :

    ```powershell
    Connect-AzAccount
    ```

2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la commande [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) pour répertorier les abonnements Azure que vous pouvez utiliser :

    ```powershell
    Get-AzSubscription
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d’exécuter les commandes pour gérer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > La commande **Select-AzSubscription** est un alias de la commande [Select-AzContext](/powershell/module/az.accounts/select-azcontext) qui vous permet d’utiliser le nom de l’abonnement (**Name**) ou l’ID d’abonnement (**Id**) renvoyé par la commande **Get-AzSubscription** plutôt que le nom de contexte plus complexe requis pour la commande **Select-AzContext**.

## <a name="retrieve-your-storage-account-details"></a>Récupérez vos détails du compte de stockage

Les étapes suivantes supposent que vous avez créé votre compte de stockage à l’aide du modèle de déploiement de **Resource Manager** et non à partir du modèle de déploiement **classique**.

Pour configurer les chargements de fichiers en provenance de vos appareils, vous avez besoin de la chaîne de connexion d’un compte de stockage Azure. Le compte de stockage doit être situé dans le même abonnement que votre IoT Hub. Vous avez également besoin du nom d’un conteneur d’objets blob dans le compte de stockage. Utilisez la commande [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) pour récupérer les clés du compte de stockage :

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Prenez note de la valeur de clé de compte de stockage **key1** . Vous en aurez besoin dans les étapes suivantes.

Vous pouvez utiliser un conteneur d’objets blob existant pour les chargements de fichiers ou en créer un nouveau :

* Pour répertorier les conteneurs de blobs existants dans votre compte de stockage, utilisez les commandes [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) et [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer) :

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Pour créer un conteneur de blobs dans votre compte de stockage, utilisez les commandes [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) et [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) :

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configuration de votre IoT Hub

Vous pouvez désormais configurer votre IoT Hub pour [charger des fichiers vers le IoT Hub](iot-hub-devguide-file-upload.md) à l’aide des informations de votre compte de stockage.

La configuration requiert les valeurs suivantes :

* **Conteneur de stockage** : conteneur d’objets blob dans un compte de stockage Azure de votre abonnement Azure actuel à associer à votre hub IoT. Vous avez extrait les informations de compte de stockage nécessaires dans la section précédente. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

* **Recevoir des notifications pour les fichiers chargés** : activez ou désactivez les notifications de chargement de fichiers.

* **Durée de vie de SAS** : ce paramètre est la durée de vie des URI de signature d’accès partagé retournés à l’appareil par IoT Hub. Défini sur 1 heure par défaut.

* **Durée de vie par défaut des paramètres de notification de fichiers** : durée de vie d’une notification de chargement avant son expiration. Défini sur 1 jour par défaut.

* **Nombre maximal de remises de notifications de fichier** : nombre de tentatives de remise d’une notification de chargement de fichier par le hub IoT. Défini sur 10 par défaut.

Utilisez la commande [Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) pour configurer les paramètres de chargement de fichiers sur votre hub IoT :

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> Par défaut, IoT Hub s’authentifie auprès de Stockage Azure en utilisant la clé de compte dans la chaîne de connexion. L’authentification à l’aide d’identités managées affectées par le système ou par l’utilisateur est également disponible. Les identités managées fournissent aux services Azure une identité managée automatiquement dans Azure AD, de manière sécurisée. Pour en savoir plus, consultez [Prise en charge des identités managées par IoT Hub](./iot-hub-managed-identity.md). Actuellement, la commande **Set-AzIotHub** ne comporte pas de paramètres permettant de définir le type d’authentification. En revanche, vous pouvez utiliser le [portail Azure](./iot-hub-configure-file-upload.md) ou [Azure CLI](./iot-hub-configure-file-upload-cli.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Charger des fichiers à partir de l’aperçu d’un appareil](iot-hub-devguide-file-upload.md)
* [Prise en charge des identités managées par IoT Hub](./iot-hub-managed-identity.md)
* [Guides pratiques sur le chargement de fichiers](./iot-hub-csharp-csharp-file-upload.md)