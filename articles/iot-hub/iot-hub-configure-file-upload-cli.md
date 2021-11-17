---
title: Configurer le chargement de fichiers vers IoT Hub à l’aide d’Azure CLI | Microsoft Docs
description: Découvrez comment configurer le chargement des fichiers vers Azure IoT Hub à l’aide de l’interface Azure CLI multiplateforme.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.openlocfilehash: c3aa2d496151d8c9d9f6ef5acfea6dee55548b2b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555600"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurer les chargements de fichiers IoT Hub à l’aide d’Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Cet article explique comment configurer des chargements de fichiers sur votre hub IoT à l’aide d’Azure CLI. 

Pour utiliser la [fonctionnalité de chargement de fichiers dans IoT Hub](iot-hub-devguide-file-upload.md), vous devez d’abord associer un compte de stockage Azure et un conteneur blob à votre hub IoT. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers. En plus du compte de stockage et du conteneur blob, vous pouvez définir la durée de vie de l’URI SAS et le type d’authentification que IoT Hub utilise avec le stockage Azure. Vous pouvez également configurer les paramètres des notifications de chargement de fichiers facultatives que IoT Hub peut remettre aux services principaux.

## <a name="prerequisites"></a>Prérequis

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

* Un IoT Hub Azure. Si vous n’avez pas de hub IoT, vous pouvez utiliser la [commande `az iot hub create`](/cli/azure/iot/hub#az_iot_hub_create) pour en créer un, ou vous pouvez [Créer un IoT Hub à l’aide du portail](iot-hub-create-through-portal.md).

* Un compte de stockage Azure. Si vous n’avez pas de compte Stockage Azure, vous pouvez utiliser Azure CLI pour en créer un. Pour plus d’informations, consultez la rubrique [Création d’un compte de stockage](../storage/common/storage-account-create.md) .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Vous connecter à votre compte Azure et sélectionner votre abonnement. Si vous utilisez Azure Cloud Shell, vous devez être déjà connecté. Toutefois, vous devrez peut-être sélectionner votre abonnement Azure si vous avez plusieurs abonnements.

1. Dans l’invite de commande, exécutez la [commande login](/cli/azure/get-started-with-azure-cli) :

    ```azurecli
    az login
    ```

    Suivez les instructions pour vous authentifier à l’aide du code et vous connecter à votre compte Azure via un navigateur web.

2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la [commande pour répertorier les comptes Azure](/cli/azure/account) ci-dessous :

    ```azurecli
    az account list
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d’exécuter les commandes pour créer votre hub IoT. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Récupérez vos détails du compte de stockage

Les étapes suivantes supposent que vous avez créé votre compte de stockage à l’aide du modèle de déploiement de **Resource Manager** et non à partir du modèle de déploiement **classique**.

Pour configurer les chargements de fichiers en provenance de vos appareils, vous avez besoin de la chaîne de connexion d’un compte de stockage Azure. Le compte de stockage doit être situé dans le même abonnement que votre IoT Hub. Vous avez également besoin du nom d’un conteneur d’objets blob dans le compte de stockage. Utilisez la commande suivante pour récupérer vos clés de compte de stockage :

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
La chaîne de connexion sera semblable à la sortie suivante :

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

Notez la valeur de `connectionString`. Vous en aurez besoin dans les étapes suivantes.

Pour le chargement des fichiers, vous pouvez soit utiliser un conteneur d’objets blob existant, soit en créer un nouveau :

* Pour répertorier les conteneurs d’objets blob existants dans votre compte de stockage, utilisez la commande suivante :

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Pour créer un conteneur d’objet blob dans votre compte de stockage, utilisez la commande suivante :

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>Configuration de votre IoT Hub

Vous pouvez désormais configurer votre hub IoT pour activer la [fonctionnalité de chargement des fichiers vers le hub IoT](iot-hub-devguide-file-upload.md) à l’aide des informations de votre compte de stockage.

La configuration requiert les valeurs suivantes :

* **Conteneur de stockage** : conteneur d’objets blob dans un compte de stockage Azure de votre abonnement Azure actuel à associer à votre hub IoT. Vous avez extrait les informations de compte de stockage nécessaires dans la section précédente. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

* **Recevoir des notifications pour les fichiers chargés** : activez ou désactivez les notifications de chargement de fichiers.

* **Durée de vie de SAS** : ce paramètre est la durée de vie des URI de signature d’accès partagé retournés à l’appareil par IoT Hub. Défini sur 1 heure par défaut.

* **TTL par défaut des paramètres de notification de fichiers** : la durée de vie d’une notification de chargement de fichiers avant son expiration. Défini sur 1 jour par défaut.

* **Nombre maximal de remises de notifications de fichier** : nombre de tentatives de remise d’une notification de chargement de fichier par le hub IoT. Défini sur 10 par défaut.

* **Durée du verrouillage des notifications de fichiers** : durée de verrouillage de la file d’attente de notifications de fichiers. Défini sur 60 secondes par défaut.

* **Type d’authentification** : type d’authentification pour IoT Hub à utiliser avec le stockage Azure. Ce paramètre détermine la manière dont votre hub IoT s’authentifie et autorise le stockage Azure. La valeur par défaut est l’authentification basée sur les clés ; toutefois, les identités managées affectées par le système et affectées par l’utilisateur peuvent également être utilisées. Les identités managées fournissent aux services Azure une identité managée automatiquement dans Azure AD, de manière sécurisée. Pour savoir comment configurer des identités managées sur votre hub IoT et votre compte de stockage Azure, consultez [Support IoT Hub des identités managées](./iot-hub-managed-identity.md). Une fois configuré, vous pouvez définir l’une de vos identités managées à utiliser pour l’authentification avec le stockage Azure.

    > [!NOTE]
    > Le paramètre du type d’authentification configure la manière dont votre hub IoT s’authentifie avec votre compte de stockage Azure. Les appareils s’authentifient toujours avec le stockage Azure à l’aide de l’URI SAS qu’ils obtiennent du hub IoT. 


Utilisez les commandes suivantes pour configurer les paramètres de chargement de fichiers sur votre hub IoT : Ces commandes sont affichées séparément pour plus de clarté, mais, en général, vous émettez une seule commande avec tous les paramètres requis pour votre scénario. Incluez les guillemets où ils apparaissent dans la ligne de commande. N’incluez pas les accolades. Vous trouverez plus d’informations sur chaque paramètre dans la documentation Azure CLI pour la commande [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update).

La commande suivante configure le compte de stockage et le conteneur blob.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

La commande suivante définit la durée de vie de l’URI SAS sur la valeur par défaut (une heure).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

La commande suivante active les notifications de fichiers et définit les propriétés de notification de fichiers à leurs valeurs par défaut. (La durée de vie de la notification de chargement de fichier est définie sur une heure et la durée de verrouillage est définie sur 60 secondes.)

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

La commande suivante configure l’authentification basée sur les clés :

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

La commande suivante configure l’authentification à l’aide de l’identité managée affectée par le système du hub IoT. Avant de pouvoir exécuter cette commande, vous devez activer l’identité managée affectée par le système pour votre hub IoT et lui accorder le rôle RBAC approprié sur votre compte de stockage Azure. Pour savoir comment, consultez [Support des identités managées par IoT Hub](./iot-hub-managed-identity.md).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

Les commandes suivantes récupèrent les identités managées affectées par l’utilisateur configurées sur votre hub IoT et configurent l’authentification avec l’une d’entre elles. Avant de pouvoir utiliser une identité managée affectée par l’utilisateur pour l’authentification, vous devez la configurer sur votre hub IoT et disposer d’un rôle RBAC approprié sur votre compte de stockage Azure. Pour plus d’informations et pour connaître les étapes à suivre, consultez [Support des identités managées par IoT Hub](./iot-hub-managed-identity.md).

Pour interroger les identités managées affectées à l’utilisateur sur votre hub IoT, utilisez la commande [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show).

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

La commande retourne une collection des identités managées affectées par l’utilisateur configurées sur votre hub IoT. La sortie suivante montre une collection qui contient une seule identité managée affectée par l’utilisateur.

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

La commande suivante configure l’authentification pour utiliser l’identité affectée par l’utilisateur ci-dessus.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

Vous pouvez réviser les paramètres sur votre hub IoT à l’aide de la commande suivante :

```azurecli
az iot hub show --name {your iot hub name}
```

Pour ne réviser que les paramètres de chargement de fichiers, utilisez la commande suivante :

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

Dans la plupart des cas, l’utilisation des paramètres nommés dans les commandes Azure CLI est plus simple. Toutefois, vous pouvez également configurer les paramètres de chargement de fichiers avec le paramètre `--set`. Les commandes suivantes peuvent vous aider à comprendre comment faire. 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>Étapes suivantes

* [Charger des fichiers à partir de l’aperçu d’un appareil](iot-hub-devguide-file-upload.md)
* [Prise en charge des identités managées par IoT Hub](./iot-hub-managed-identity.md)
* [Guides de procédures pour le chargement de fichiers](./iot-hub-csharp-csharp-file-upload.md)
* Commandes Azure CLI [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update), [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) et [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create)
