---
title: Migrer des applications Java vers Azure App Service
description: Découvrez les ressources de migration Java disponibles pour Azure App Service.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f876445673d0237af33f10e3e5b599032ba28bd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097594"
---
# <a name="java-migration-resources-for-azure-app-service"></a>Ressources de migration Java pour Azure App Service

Azure App Service fournit des outils permettant de découvrir les applications web déployées sur des serveurs web locaux. Vous pouvez évaluer la préparation de ces applications, puis les migrer vers App Service. Le contenu de l’application web et la configuration prise en charge peuvent être migrés vers App Service. Ces outils sont développés pour prendre en charge un large éventail de scénarios axés sur la découverte, l’évaluation et la migration.

## <a name="java-tomcat-migration-linux"></a>Migration de Java Tomcat (Linux)

[Téléchargez l’Assistant](https://azure.microsoft.com/services/app-service/migration-assistant/) pour migrer une application Java exécutée sur un serveur web Apache Tomcat. Vous pouvez également utiliser Azure Container Registry pour migrer des conteneurs Docker Linux locaux vers App Service.

| Ressources |
|-----------|
| **Procédures** |
| [Wiki de l’Assistant Migration Java App Service](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information) |
| [Azure/App-Service-Migration-Assistant Wiki](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Linux-Notes) |
| **Vidéos** |
|[Pointer sur des applications Java et les migrer vers Azure App Service en utilisant le système de migration](https://www.youtube.com/watch?v=Mpxa0KE0X9k) |

## <a name="standalone-tomcat-web-app-migration-windows-os"></a>Migration d’applications web Tomcat autonomes (système d’exploitation Windows)

Téléchargez cet [outil en préversion](https://azure.microsoft.com/services/app-service/migration-assistant/) pour migrer une application web Java sur Apache Tomcat vers App Service sur Windows. Pour plus d’informations, regardez cette [vidéo](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service) et consultez ce [guide pratique](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information).

## <a name="containerize-standalone-tomcat-web-app"></a>Conteneuriser l’application web Tomcat autonome

La conteneurisation d’applications constitue une approche simple pour repackager les applications en tant que conteneurs avec des modifications de code minimales. L’outil prend actuellement en charge la conteneurisation d’applications web Java sur Apache Tomcat et ASP.NET. Pour plus d’informations, consultez ce [blog](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) et ce [guide pratique](../migrate/tutorial-app-containerization-java-app-service.md).

## <a name="next-steps"></a>Étapes suivantes

[Migrer des applications Tomcat vers Tomcat sur Azure App Service](/azure/developer/java/migration/migrate-tomcat-to-tomcat-app-service)
