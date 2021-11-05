---
title: Bien comprendre le chargement de fichiers Azure IoT Hub | Microsoft Docs
description: Guide du développeur - utilisez la fonctionnalité de chargement de fichier de IoT Hub pour gérer le chargement de fichiers depuis un appareil vers un conteneur d’objets blob de stockage Azure.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/25/2021
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 991a55da8a4359192e499e464f034a8742055c99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005325"
---
# <a name="upload-files-with-iot-hub"></a>Chargement de fichiers avec IoT Hub

Dans de nombreux scénarios, vous ne pouvez pas facilement mapper les données que votre appareil dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple, l’envoi de fichiers multimédias volumineux ou l’envoi de lots de télémétrie volumineux sont téléchargés par des appareils connectés par intermittence ou qui ont été agrégés et compressés pour économiser de la bande passante.

S’il vous faut charger des fichiers volumineux à partir d’un appareil, vous pouvez toujours exploiter la sécurité et la fiabilité d’IoT Hub. Au lieu de distribuer les messages via lui-même cependant, IoT Hub joue le rôle de répartiteur vers un compte de stockage Azure associé. IoT Hub pouvez également fournir une notification aux services backend lorsqu’un appareil termine un chargement de fichier.

Si vous avez besoin d'aide pour décider quand utiliser les propriétés signalées, les messages appareil-à-cloud ou les téléchargements de fichiers, consultez la section [Recommandations sur les communications appareil-à-cloud](iot-hub-devguide-d2c-guidance.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="file-upload-overview"></a>Vue d’ensemble du chargement de fichier

Un hub IoT facilite les chargements de fichiers à partir d’appareils connectés en leur fournissant des URI de signature d’accès partagé (SAP) par téléchargement pour un conteneur d’objets BLOB et un compte de stockage Azure qui ont été préconfigurés avec le hub. Il existe trois parties à l’utilisation des chargements de fichiers avec IoT Hub : la préconfiguration d’un compte de stockage Azure et d’un conteneur d’objets BLOB sur votre hub IoT, le chargement de fichiers à partir d’appareils et, éventuellement, la notification des services principaux de chargements de fichiers terminés.

Pour pouvoir utiliser la fonctionnalité de chargement de fichiers, vous devez associer un [compte de stockage Azure](/azure/storage/common/storage-account-overview) et un [conteneur d’objets BLOB](/azure/storage/blobs/storage-blobs-introduction) à votre hub IoT. Vous pouvez également configurer des paramètres qui contrôlent la façon dont IoT Hub s’authentifie avec le stockage Azure, la durée de vie (TTL, Time-to-Live) des URI SAP que le hub IoT transmet aux appareils et les notifications de chargement de fichiers à vos services principaux. Pour en savoir plus, consultez [Association d’un compte de stockage Azure à IoT Hub](#associate-an-azure-storage-account-with-iot-hub).

Les appareils suivent un processus en trois étapes pour charger un fichier dans le conteneur d’objets BLOB associé :

1. L’appareil lance le chargement du fichier avec le hub IoT. Il transmet le nom d’un objet BLOB dans la requête et obtient un URI SAP et un ID de corrélation en retour. L’URI SAS contient un jeton SAP pour le stockage Azure qui accorde l’autorisation de lecture/écriture de l’appareil sur l’objet BLOB demandé dans le conteneur d’objets BLOB. Pour plus d’informations, consultez [Appareil : initialiser un chargement de fichier](#device-initialize-a-file-upload).

1. L’appareil utilise l’URI SAS pour appeler en toute sécurité les API de stockage d’objets BLOB Azure pour charger le fichier dans le conteneur d’objets BLOB. Pour plus d’informations, consultez [Appareil : charger un fichier à l’aide des API de stockage Azure](#device-upload-file-using-azure-storage-apis).

1. Une fois le chargement du fichier terminé, l’appareil avertit le hub IoT de l’état d’achèvement à l’aide de l’ID de corrélation qu’il a reçu du hub IoT lorsqu’il a initié le chargement. Pour plus d’informations, consultez [Appareil : notifier le hub IoT de la fin du chargement d’un fichier](#device-notify-iot-hub-of-a-completed-file-upload).

Les services principaux peuvent s’abonner à des notifications de chargement de fichier sur le point de terminaison de notification de chargement de fichier orienté vers le hub IoT. Si vous avez activé ces notifications sur votre hub IoT, il les remet sur ce point de terminaison chaque fois qu’un appareil notifie le hub qu’il a effectué un chargement de fichier. Les services peuvent utiliser ces notifications pour déclencher un traitement supplémentaire des données BLOB. Pour plus d’informations, consultez [Service : notifications de chargement de fichier](#service-file-upload-notifications).

Le chargement de fichiers est entièrement pris en charge par les kits de développement logiciel (SDK) d’appareil et de service Azure IoT. Pour plus d’informations, consultez [Charger des fichiers à l’aide d’un kit de développement logiciel (SDK)](#file-upload-using-an-sdk).

### <a name="file-upload-quotas-and-limits"></a>Quotas et limites de chargement de fichiers

IoT Hub impose des limites de limitation sur le nombre de chargements de fichiers qu’il peut lancer au cours d’une période donnée. Le seuil est basé sur la référence SKU et le nombre d’unités de votre hub IoT. En outre, chaque appareil est limité à 10 chargements de fichiers actifs simultanés à la fois. Pour en savoir plus, consultez [Limitations et quotas](iot-hub-devguide-quotas-throttling.md).

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Association d’un compte de stockage Azure à IoT Hub

Vous devez associer un compte de stockage Azure et un conteneur d’objets BLOB à votre hub IoT pour pouvoir utiliser les fonctionnalités de chargement de fichiers. Tous les chargements de fichiers à partir d’appareils inscrits auprès de votre hub IoT seront dirigés vers ce conteneur. Pour configurer un compte de stockage et un conteneur d’objets BLOB sur votre hub IoT, consultez [Configurer les chargements de fichiers avec le Portail Azure](iot-hub-configure-file-upload.md), [Configurer les chargements de fichiers avec Azure CLI](iot-hub-configure-file-upload-cli.md) ou [Configurer les chargements de fichiers avec PowerShell](iot-hub-configure-file-upload-powershell.md). Vous pouvez également utiliser les API de gestion IoT Hub pour configurer les chargements de fichiers par programme.

Si vous utilisez le portail, vous pouvez créer un compte de stockage et un conteneur lors de la configuration. Sinon, pour créer un compte de stockage, consultez [Créer un compte de stockage](../storage/common/storage-account-create.md) dans la documentation de stockage Azure. Une fois que vous avez un compte de stockage, vous pouvez voir comment créer un conteneur d’objets BLOB dans les [Démarrages rapides du stockage d’objets BLOB Azure](/azure/storage/blobs/storage-quickstart-blobs-portal).

Il existe plusieurs autres paramètres qui contrôlent le comportement des chargements de fichiers et des notifications de chargement de fichiers. Les sections suivantes répertorient tous les paramètres disponibles. Selon que vous utilisez le Portail Azure, Azure CLI, PowerShell ou les API de gestion pour configurer les chargements de fichiers, certains de ces paramètres peuvent ne pas être disponibles. Veillez à définir le paramètre **enableFileUploadNotifications** si vous souhaitez que les notifications soient envoyées à vos services principaux quand un chargement de fichier se termine.

### <a name="iot-hub-storage-and-authentication-settings"></a>Paramètres de stockage et d’authentification IOT Hub

Les paramètres suivants associent un compte de stockage et un conteneur à votre hub IoT et contrôlent la façon dont votre hub s’authentifie auprès du stockage Azure. Ces paramètres n’affectent pas la manière dont les appareils s’authentifient auprès du stockage Azure. Les appareils s’authentifient toujours avec le jeton SAS présenté dans l’URI SAS récupéré à partir d’IoT Hub.

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| **storageEndpoints.$default.authenticationType** | Contrôle la façon dont IoT Hub s’authentifie auprès du stockage Azure. | Les valeurs possibles sont keyBased et identityBased. Valeur par défaut : keyBased. |
| **storageEndpoints.$default.connectionString** | Chaîne de connexion au compte de stockage Azure à utiliser pour les chargements de fichiers. | Valeur par défaut : chaîne vide. |
| **storageEndpoints.$default.containerName** | Nom du conteneur dans lequel charger les fichiers. | Valeur par défaut : chaîne vide. |
| **storageEndpoints.$default.identity** | Identité managée à utiliser pour l’authentification basée sur l’identité. | Les valeurs possibles sont `[system]` pour l’identité managée affectée par le système ou un ID de ressource pour une identité managée affectée par l’utilisateur. La valeur n’est pas utilisée pour l’authentification basée sur les clés. Valeur par défaut : null. |

### <a name="file-upload-settings"></a>Paramètres de chargement de fichier

Les paramètres suivants contrôlent les chargements de fichiers à partir de l’appareil.

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| **storageEndpoints.$default.ttlAsIso8601** | Durée de vie par défaut des URI SAS générés par IoT Hub. | Intervalle ISO_8601 jusqu’à 48 heures (minimum 1 minute). Valeur par défaut : 1 heure. |

### <a name="file-upload-notification-settings"></a>Paramètres de notification de chargement de fichier

Les paramètres suivants contrôlent les notifications de chargement de fichier vers les services principaux.

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| **enableFileUploadNotifications** |Indique si les notifications de téléchargement de fichier sont écrites dans le point de terminaison de notification de fichier. |Valeur booléenne. Valeur par défaut : False. |
| **fileNotifications.ttlAsIso8601** |Durée de vie par défaut des notifications de téléchargement de fichier. |Intervalle ISO_8601 jusqu’à 48 heures (minimum 1 minute). Valeur par défaut : 1 heure. |
| **fileNotifications.lockDuration** |Durée de verrouillage de la file d’attente des notifications de téléchargement de fichiers. |5 à 300 secondes. Valeur par défaut : 60 secondes. |
| **fileNotifications.maxDeliveryCount** |Nombre maximal de diffusions pour la file d’attente de notification de téléchargement de fichier. |1 à 100. Valeur par défaut : 100. |

## <a name="file-upload-using-an-sdk"></a>Charger des fichiers à l’aide d’un kit de développement logiciel (SDK)

Les guides pratiques suivants fournissent des instructions pas à pas complètes pour charger des fichiers à l’aide des kits de développement logiciel (SDK) d’appareils et de services Azure IoT. Ils vous montrent comment utiliser le Portail Azure pour associer un compte de stockage à un hub IoT, et contiennent des extraits de code ou font référence à des exemples qui vous guident tout au long d’un chargement.

| Guide pratique | Exemple de SDK d’appareil | Exemple de SDK de service |
|---------|--------|---------|
| [.NET](iot-hub-csharp-csharp-file-upload.md) | Oui | Oui |
| [Java](iot-hub-java-java-file-upload.md) | Oui | Oui |
| [Node.JS](iot-hub-node-node-file-upload.md) | Oui | Oui |
| [Python](iot-hub-python-python-file-upload.md) | Oui | Non (non pris en charge) |

> [!NOTE]
> Le Kit de développement logiciel (SDK) d’appareil C utilise un seul appel sur le client d’appareil pour effectuer des chargements de fichiers. Pour plus d’informations, consultez [IoTHubDeviceClient_UploadToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadtoblobasync) et [IoTHubDeviceClient_UploadMultipleBlocksToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadmultipleblockstoblobasync). Ces fonctions effectuent tous les aspects du chargement de fichier en un seul appel : lancer le chargement, charger le fichier dans le stockage Azure et avertir IoT Hub lorsqu’il se termine. Cela signifie que, en plus du protocole que l’appareil utilise pour communiquer avec IoT Hub, il doit également être en mesure de communiquer via HTTPS avec le stockage Azure, car ces fonctions effectuent des appels aux API de stockage Azure.

## <a name="device-initialize-a-file-upload"></a>Appareil : initialiser un chargement de fichier

L’appareil appelle l’API REST de [création de fichier de chargement d’URI SAS](/rest/api/iothub/device/create-file-upload-sas-uri) ou l’API équivalente dans l’un des Kits de développement logiciel (SDK) d’appareil pour lancer un chargement de fichier.

**Protocoles pris en charge** : AMQP, AMQP-WS, MQTT, MQTT-WS et HTTPS <br/>
**Point de terminaison** : {iot hub}.azure-devices.net/devices/{deviceId}/files <br/>
**Méthode** : POST

```json
{
    "blobName":"myfile.txt"
}

```

| Propriété | Description |
|----------|-------------|
| blobName | Nom de l’objet BLOB pour lequel générer l’URI SAS. |

 IoT Hub répond avec un ID de corrélation et les éléments d’un URI SAS que l’appareil peut utiliser pour s’authentifier auprès du stockage Azure. Cette réponse est soumise aux limites de limitation et aux limites de chargement par appareil du hub IoT cible.

```json
{
    "correlationId":"MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "hostName":"contosostorageaccount.blob.core.windows.net",
    "containerName":"device-upload-container",
    "blobName":"mydevice/myfile.txt",
    "sasToken":"?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw"
}

```

| Propriété | Description |
|----------|-------------|
| correlationId | Identificateur de l’appareil à utiliser lors de l’envoi de la notification de téléchargement de fichier complet à IoT Hub. |
| hostName | Nom d’hôte du compte de stockage Azure pour le compte de stockage configuré sur le hub IoT |
| containerName | Nom du conteneur d’objets BLOB configuré sur le hub IoT. |
| blobName | Emplacement où l’objet BLOB sera stocké dans le conteneur. Ce nom respecte le format suivant : `{device ID of the device making the request}/{blobName in the request}` |
| sasToken | Jeton SAP qui accorde l’accès en lecture-écriture à l’objet BLOB avec le stockage Azure. Le jeton est généré et signé par IoT Hub. |

Lorsqu’il reçoit la réponse, l’appareil :

* Enregistre l’ID de corrélation à inclure dans la notification de fin de téléchargement de fichier dans le hub IoT lorsqu’il effectue le téléchargement.

* Utilise les autres propriétés pour construire un URI SAP pour l’objet BLOB qu’il utilise pour s’authentifier auprès du stockage Azure. L’URI SAP contient l’URI de la ressource pour l’objet BLOB demandé et le jeton SAP. Elle prend la forme suivante : `https://{hostName}/{containerName}/{blobName}{sasToken}` (la propriété `sasToken` dans la réponse contient un caractère «  ? » de début). Les accolades ne sont pas incluses.

    Par exemple, pour les valeurs retournées dans l’exemple ci-dessus, l’URI SAP est, `https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw`

    Pour plus d’informations sur l’URI SAP et le jeton SAP, consultez [Créer une SAP de service](/rest/api/storageservices/create-service-sas) dans la documentation sur le stockage Azure.

## <a name="device-upload-file-using-azure-storage-apis"></a>Appareil : charger un fichier à l’aide des API de stockage Azure

L’appareil utilise les [API REST de stockage BLOB Azure](/rest/api/storageservices/blob-service-rest-api) ou les API du Kit de développement logiciel (SDK) Azure Storage équivalentes pour charger le fichier vers l’objet BLOB dans le stockage Azure.

**Protocoles pris en charge** : HTTPS

L’exemple suivant montre une requête [Put Blob](/rest/api/storageservices/put-blob) pour créer ou mettre à jour un objet blob de petits blocs. Notez que l’URI utilisé pour cette demande est l’URI SAP retourné par IoT Hub dans la section précédente. L'en-tête `x-ms-blob-type` indique que cette requête concerne un objet blob de blocs. Si la requête réussit, le stockage Azure retourne un `201 Created`.

```http
PUT https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw HTTP/1.1
Content-Length: 11
Content-Type: text/plain; charset=UTF-8
Host: contosostorageaccount.blob.core.windows.net
x-ms-blob-type: BlockBlob

hello world
```

L’utilisation des API de stockage Azure sort du cadre de cet article. Outre les API REST de stockage BLOB Azure liées précédemment dans cette section, vous pouvez explorer la documentation suivante pour vous aider à commencer :

* Pour en savoir plus sur l’utilisation des objets blob dans le stockage Azure, consultez la documentation [Stockage BLOB Azure](/azure/storage/blobs/).

* Pour plus d’informations sur l’utilisation des Kits de développement logiciel (SDK) client de stockage Azure pour charger des objets blob, consultez [Informations de référence sur les API Stockage Blob Azure](/azure/storage/blobs/reference).  

## <a name="device-notify-iot-hub-of-a-completed-file-upload"></a>Appareil : notifier IoT Hub de la fin du chargement d’un fichier

L’appareil appelle l’API REST de [mise à jour de fichier de chargement des fichiers](/rest/api/iothub/device/update-file-upload-status) ou l’API équivalente dans l’un des Kits de développement logiciel (SDK) d’appareil lorsqu’il effectue le chargement de fichier. L’appareil doit mettre à jour l’état de chargement du fichier avec IoT Hub que le chargement réussisse ou échoue.

**Protocoles pris en charge** : AMQP, AMQP-WS, MQTT, MQTT-WS et HTTPS <br/>
**Point de terminaison** : {iot hub}.azure-devices.net/devices/{deviceId}/files/notifications <br/>
**Méthode** : POST 

```json
{
    "correlationId": "MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "isSuccess": true,
    "statusCode": 200,
    "statusDescription": "File uploaded successfully"
}

```

 Propriété | Description |
|----------|-------------|
| correlationId | ID de corrélation reçu dans la requête d’URI SAP initiale. |
| isSuccess | Valeur booléenne qui indique si le chargement du fichier a réussi. |
| statusCode | Entier qui représente le code d’état du chargement de fichier. Généralement trois chiffres ; par exemple, 200 ou 201. |
| statusDescription | Description de l’état du chargement du fichier. |

Lorsqu’il reçoit une notification de fin de chargement de fichier de l’appareil, IoT Hub :

* Déclenche une notification de chargement de fichier pour les services principaux si les notifications de chargement de fichier sont configurées.

* Libère les ressources associées au chargement de fichier. Si vous ne recevez pas de notification, IoT Hub conserve les ressources jusqu’à ce que la durée de vie (TTL) de l’URI SAP associée au chargement expire.

## <a name="service-file-upload-notifications"></a>Service : notifications de chargement de fichier

Si les notifications de chargement de fichier sont activées sur votre hub IoT, il génère un message de notification pour les services principaux lorsqu’il reçoit une notification d’un appareil indiquant qu’un chargement de fichier est terminé. IoT Hub remet ces notifications de chargement de fichier via un point de terminaison côté service. La sémantique de réception des notifications de chargement de fichiers est identique à celle des messages cloud-à-appareil et présente le même [cycle de vie des messages](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Les SDK de service exposent des API pour gérer les notifications de chargement de fichier.

**Protocoles pris en charge** : AMQP, AMQP-WS <br/>
**Point de terminaison** : {iot hub}.azure-devices.net/messages/servicebound/fileuploadnotifications <br/>
**Méthode** GET

Chaque message récupéré à partir du point de terminaison de notification de chargement de fichier est un enregistrement JSON :

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt",
    "blobName":"mydevice/myfile.txt",
    "lastUpdatedTime":"2021-07-31T00:26:50+00:00",
    "blobSizeInBytes":11,
    "enqueuedTimeUtc":"2021-07-31T00:26:51.5134008Z"
}
```

| Propriété | Description |
| --- | --- |
| enqueuedTimeUtc | Horodatage indiquant la date et l’heure de création de la notification. |
| deviceId | ID de l’appareil qui a chargé le fichier. |
| blobUri | URI du fichier chargé. |
| blobName | Nom du fichier chargé. Ce nom respecte le format suivant : `{device ID of the device}/{name of the blob}`|
| lastUpdatedTime |Horodatage indiquant la date et l’heure de dernière mise à jour du fichier. |
| blobSizeInBytes | Entier qui représente la taille du fichier chargé en octets. |

Les services peuvent utiliser des notifications pour gérer les chargements. Par exemple, ils peuvent déclencher leur propre traitement des données d’objet BLOB, déclencher le traitement des données BLOB à l’aide d’autres services Azure ou consigner la notification de chargement de fichier pour consultation ultérieure.

## <a name="next-steps"></a>Étapes suivantes

* [Guides pratiques sur le chargement de fichiers](iot-hub-csharp-csharp-file-upload.md)

* [Comment configurer les chargements de fichiers sur IoT Hub](iot-hub-configure-file-upload.md)

* [Comment configurer des identités managées sur IoT Hub](iot-hub-managed-identity.md)

* [Documentation sur Stockage Blob Azure](/azure/storage/blobs/)

* La rubrique [SDK des services et appareils Azure IoT](iot-hub-devguide-sdks.md), qui répertorie les SDK en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
