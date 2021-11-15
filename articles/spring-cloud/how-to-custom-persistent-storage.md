---
title: Guide pratique pour activer votre propre stockage permanent dans Azure Spring Cloud | Microsoft Docs
description: Comment faire de votre propre stockage des stockages permanents dans Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2bf84684851b19665e33af0cbfe902145b8ea565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479430"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Guide pratique pour activer votre propre stockage permanent dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ✔️ C#

Cet article vous montre comment activer votre propre stockage permanent dans Azure Spring Cloud.

Lorsque vous utilisez le stockage permanent intégré dans Azure Spring Cloud, les artefacts générés par votre application sont téléchargés dans des comptes de stockage Azure. Microsoft contrôle les stratégies de gestion du chiffrement au repos et de la durée de vie de ces artefacts. 

Avec Bring Your Own Storage, ces artefacts sont téléchargés dans un compte de stockage que vous contrôlez. Cela signifie que vous contrôlez la stratégie de chiffrement au repos, la stratégie de gestion de la durée de vie et l’accès réseau. Toutefois, vous êtes responsable des coûts associés à ce compte de stockage.

## <a name="prerequisites"></a>Prérequis

* Un compte de stockage Azure existant et un partage de fichiers Azure créé au préalable. Si vous devez créer un compte de stockage et un partage de fichiers dans Azure, consultez [Créer un partage de fichiers Azure](../storage/files/storage-how-to-create-file-share.md).
* [Extension Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) pour l’interface de ligne de commande Azure (Azure CLI)

## <a name="use-the-azure-cli-to-enable-your-own-extra-persistent-storage"></a>Utiliser Azure CLI pour activer votre propre stockage permanent supplémentaire

Vous pouvez activer votre propre stockage avec Azure CLI en suivant les étapes suivantes.

1. Utilisez la commande suivante pour lier votre compte de stockage Azure en tant que ressource de stockage dans votre instance Azure Spring Cloud :

    ```azurecli
   az spring-cloud storage add --storage-type StorageAccount --account-name <account-name> --account-key <account-key>  -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
    ```

1. Utilisez la commande suivante pour créer une application avec votre propre stockage permanent.

    ```azurecli
    az spring-cloud app create -n <app-name> -g <resource-group-name> -s <spring-instance-name> --persistent-storage <path-to-JSON-file>
    ```

    Voici un exemple du fichier JSON qui est passé au paramètre `--persistent-storage` dans la commande de création :

    ```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
    ```

1. Si vous le souhaitez, ajoutez un stockage permanent supplémentaire à une application existante à l’aide de la commande suivante :

    ```azurecli
    az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
    ```

1. Si vous le souhaitez, vous pouvez répertorier tous les stockages permanents existants d’une ressource de stockage spécifique à l’aide de la commande suivante :

   ```azurecli
   az spring-cloud storage list-persistent-storage -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
   ```

## <a name="use-best-practices"></a>Utiliser les meilleures pratiques

Il s’agit des meilleures pratiques à utiliser lors de l’ajout de votre propre stockage permanent dans Azure Spring Cloud.

- Pour éviter les problèmes de latence potentiels, placez l’instance Azure Spring Cloud et le compte de stockage Azure dans la même région Azure.

- Dans le compte de stockage Azure, évitez de régénérer la clé de compte qui est utilisée. Le compte de stockage contient deux clés différentes. Utilisez une approche étape par étape pour vous assurer que votre propre stockage permanent reste disponible pour les applications lors de la régénération de la clé. 

   Par exemple, en supposant que vous avez utilisé key1 pour lier un compte de stockage à Azure Spring Cloud, procédez comme suit :

   1. Régénérer key2.
   1. Mettez à jour la clé de compte de la ressource de stockage pour utiliser la clé key2 régénérée.
   1. Redémarrez les applications qui montent le stockage permanent à partir de cette ressource de stockage. (Vous pouvez utiliser ```az spring-cloud storage list-persistent-storage``` pour répertorier toutes les applications associées.)
   1. Régénérer key1.

- Si vous supprimez un compte de stockage Azure ou un partage de fichiers Azure, supprimez la ressource de stockage correspondante ou le stockage permanent dans les applications pour éviter les erreurs éventuelles.

## <a name="faqs"></a>Foire aux questions

Les questions fréquemment posées (FAQ) relatives à l’utilisation de votre propre stockage permanent avec Azure Spring Cloud sont répertoriées ci-dessous.

- Si le stockage permanent intégré est activé et que j’ai activé mon propre stockage comme stockage permanent supplémentaire, mes données seront-elles migrées dans mon compte stockage ?

   *Non, mais nous allons prochainement vous fournir un document pour vous aider à effectuer la migration vous-même.*

- Quels sont les chemins de montage réservés ?

   *Ces chemins de montage sont réservés par le service Azure Spring Cloud :*
   - */tmp*
   - */persistent*
   - */secrets*
   - */app-insights/agents*
   - */etc/azure-spring-cloud/certs*
   - */app-insights/agents/settings*
   - */app-lifecycle/settings*

- Quelles sont les options de montage disponibles ?

   *Nous prenons actuellement en charge les options de montage suivantes :*
   - `uid`
   - `gid`
   - `file_mode`
   - `dir_mode`
   
   *La propriété `mountOptions` est facultative. Les valeurs par défaut pour les options de montage ci-dessus sont : ["uid=0", "gid=0", "file_mode=0777", "dir_mode=0777"]*

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [quotas d’applications et de services](./quotas.md).
* Découvrez comment [mettre à l’échelle manuellement votre application](./how-to-scale-manual.md).