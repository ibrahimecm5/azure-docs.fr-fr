---
title: Paramètres de diagnostic des options JVM pour le dépannage avancé dans Azure Spring Cloud
description: Plusieurs bonnes pratiques de configuration de JVM pour définir les journaux de vidage de tas, JFR et GC.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: fd29b9b7356bc16132d46f125d3f3cf2792c1654
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000611"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>Tutoriel : Paramètres de diagnostic des options JVM pour le dépannage avancé dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ❌ C#

Il existe plusieurs paramètres de démarrage d’application basés sur JVM liés aux journaux de vidage de tas, JFR et GC. Dans Azure Spring Cloud, nous prenons en charge la configuration de JVM à l’aide de jvm-options. Dans cet article, nous fournissons plusieurs exemples qui peuvent être utiles à nos clients.

## <a name="prerequisites"></a>Prérequis
Pour effectuer cet exercice, vous avez besoin des éléments suivants :

* Une instance du service Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application via Azure CLI](./quickstart.md).
* Au moins une application déjà créée dans votre instance de ce service.
* Au moins un [stockage persistant est déjà lié à votre application](how-to-built-in-persistent-storage.md) pour enregistrer les fichiers de diagnostic générés.

Les utilisateurs peuvent suivre le [document de déploiement](https://docs.microsoft.com/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest) pour configurer les paramètres de démarrage de l’application JVM. Nous allons fournir plusieurs exemples dans la section suivante. **Pour ce faire, vous devez ajouter le paramètre dans jvm-options.**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>Générer un vidage du tas quand la mémoire est insuffisante
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
Il est également possible de fournir le nom spécifique du fichier. Mais il est vivement recommandé de fournir uniquement le chemin d’accès au dossier. Une fois le nom de fichier donné, il génère uniquement un vidage de tas lors de la première occurrence de mémoire insuffisante. En raison du fait que le fichier au format hprof ne peut pas être couvert, les utilisateurs ne seront pas en mesure d’accéder au vidage de tas lors de la prochaine occurrence de mémoire insuffisante. Si les utilisateurs fournissent uniquement le chemin d’accès au dossier, ils obtiendront des vidages de tas de toutes les occurrences de mémoire insuffisante avec un nom généré automatiquement.

Tout le chemin d’accès indiqué ci-dessous doit se trouver sous le chemin de montage du stockage persistant de l’utilisateur qui est déjà lié à l’application du client.

## <a name="gc-logs"></a>Journaux GC
Nous fournissons simplement un exemple d’utilisation des options JVM relatives aux journaux GC. Pour plus d’informations, vous pouvez accéder à la documentation JVM officielle.
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>JFR lors d’une sortie
Nous fournissons simplement un exemple d’utilisation des options JVM relatives aux journaux JFR. Pour plus d’informations, vous pouvez accéder à la documentation JVM officielle.
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>Chemin d’accès du fichier généré.
Vérifiez que le chemin d’accès cible de votre fichier généré se trouve dans votre stockage persistant sur votre application. Cela vous permettra de récupérer le fichier dans votre stockage.
Par exemple, les clients donnent un fichier JSON quand les utilisateurs créent tout d’abord un stockage persistant dans Azure Spring Cloud.
```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
```
Ou le client peut utiliser les commandes ci-dessous pour ajouter le stockage persistant.
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   Si les clients veulent avoir le vidage sous leur propre fichier Azure indiqué ci-dessus, définissez <PATH_TO_HEAP_DUMP_FOLDER> sur « /test/Path ». Si les clients souhaitent utiliser le chemin d’accès du montage, vérifiez que le sous-chemin d’accès a déjà été créé.




