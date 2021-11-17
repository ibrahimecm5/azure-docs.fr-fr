---
title: Guide pratique pour capturer manuellement un heap dump, un thread dump et JFR dans Azure Spring Cloud
description: Découvrez comment capturer manuellement un heap dump, un thread dump ou démarrer JFR.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5f34dce5e177650b11858cabf770b7d63dc9b593
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000621"
---
# <a name="how-to-manually-capture-heap-dump-thread-dump-and-jfr-in-azure-spring-cloud"></a>Guide pratique pour capturer manuellement un heap dump, un thread dump et JFR dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ❌ C#

Une résolution des problèmes efficace, qui est essentielle pour que nos clients résolvent les problèmes dans leur environnement de production, garantit la mise en ligne permanente de leurs activités. Aujourd’hui dans Azure Spring Cloud, nous avons déjà fourni des requêtes et le streaming des journaux des applications, l’émission de métriques riches et d’alertes, le suivi distribué, etc. pour aider nos clients dans ce domaine. Toutefois, lorsque les clients reçoivent des alertes relatives à la demande de latence élevée, à une fuite de segment de mémoire JVM ou à une utilisation élevée du processeur, il n’existe pas de solution précise. Par conséquent, nous avons activé les opérations manuelles de génération d’un head dump/thread dump ou de démarrage d’un JFR.

## <a name="prerequisites"></a>Prérequis
Pour effectuer cet exercice, vous avez besoin des éléments suivants :

* Une instance du service Azure Spring Cloud déployée. Pour bien démarrer, suivez notre [guide de démarrage rapide sur le déploiement d’une application via Azure CLI](./quickstart.md).
* Au moins une application déjà créée dans votre instance de ce service.
* Au moins un [stockage persistant est déjà lié à votre application](how-to-built-in-persistent-storage.md) pour enregistrer les fichiers de diagnostic générés.

**Si les clients souhaitent utiliser le chemin d’accès du montage, vérifiez que le sous-chemin d’accès a déjà été créé**.

## <a name="generate-a-heap-dump"></a>Générer un head dump
Générez un head dump de notre application dans Azure Spring Cloud.
```heap dump command
   az spring-cloud app deployment generate-heap-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="generate-a-thread-dump"></a>Générer un thread dump
Générez un thread dump de notre application dans Azure Spring Cloud.
```thread dump command
   az spring-cloud app deployment generate-thread-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="start-jfr"></a>Démarrer JFR
Démarrez un JFR de notre application dans Azure Spring Cloud.
```JFR command
   az spring-cloud app deployment start-JFR -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path> --duration <duration-of-JFR>
```
La valeur de durée par défaut est de 60 s.
## <a name="get-your-diagnostic-files"></a>Récupérer vos fichiers de diagnostic
Accédez au chemin d’accès du fichier cible dans votre stockage persistant et recherchez votre dump/JFR. Vous pouvez les télécharger sur votre ordinateur local. Le nom du fichier généré ressemble à '{appInstance}\_heapdump\_{timeStamp}.hprof' pour heap dump, '{appInstance}\_threaddump\_{timeStamp}.txt' pour le thread dump et '{appInstance}\_JFR\_{timeStamp}.jfr' pour JFR.
