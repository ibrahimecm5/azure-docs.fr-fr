---
title: Migration d’applications .NET vers Azure App Service
description: Découvrez les ressources de migration .NET disponibles pour Azure App Service.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8918c32e03c6e2c27621dd5c05b9abe976ded511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097495"
---
# <a name="net-migration-cases-for-azure-app-service"></a>Cas de migration.NET pour Azure App Service

Azure App Service fournit des outils faciles à utiliser pour découvrir rapidement les applications web .NET locales, évaluer la préparation et migrer à la fois le contenu et les configurations prises en charge vers App Service.

Ces outils sont développés pour prendre en charge différents types de scénarios axés sur la découverte, l’évaluation et la migration. Voici la liste des outils de migration .NET et des cas d’usage.

## <a name="migrate-from-multiple-servers-at-scale-preview"></a>Migration à grande échelle à partir de plusieurs serveurs (préversion)

<!-- Intent: discover how to assess and migrate at scale. -->

Azure Migrate a récemment annoncé la découverte et l’évaluation sans agent et à grande échelle des applications web ASP.NET. À présent, vous pouvez facilement découvrir les applications web ASP.NET exécutées sur des serveurs Internet Information Services (IIS) dans un environnement VMware et les évaluer en vue d’une migration vers Azure App Service. Ces évaluations vous aideront à déterminer la préparation à la migration d’application web et les points bloquants, et vous indiqueront les conseils de correction, la référence SKU recommandée et les coûts d’hébergement. Vous trouverez ci-dessous des ressources de migration à grande échelle.

### <a name="at-scale-migration-resources"></a>Ressources relatives à la migration à grande échelle

| Procédures |
|----------------|
| [Découverte des applications web et des instances SQL Server](../migrate/how-to-discover-sql-existing-project.md)                              |
| [Créer une évaluation Azure App Service](../migrate/how-to-create-azure-app-service-assessment.md)                            |
| [Tutoriel pour expliquer comment évaluer des applications web en vue de leur migration vers Azure App Service](../migrate/tutorial-assess-webapps.md)                       |
| [Découvrir l’inventaire logiciel sur des serveurs locaux avec Azure Migrate](../migrate/how-to-discover-applications.md)           |
| **Blog** |
| [Découverte et évaluation des applications ASP.NET à grande échelle avec Azure Migrate](https://azure.microsoft.com/blog/discover-and-assess-aspnet-apps-atscale-with-azure-migrate/) |
| **FORUM AUX QUESTIONS** |
| [Évaluations d’Azure App Service dans l’outil de détection et d’évaluation d’Azure Migrate Discovery](../migrate/concepts-azure-webapps-assessment-calculation.md) |
| **Bonnes pratiques** |
| [Évaluations des meilleures pratiques dans l’outil de découverte et d’évaluation d’Azure Migrate](../migrate/best-practices-assessment.md) |
| **Vidéo** |
| [Découverte et évaluation à grande échelle pour la migration d’application ASP.NET avec Azure Migrate](https://channel9.msdn.com/Shows/Inside-Azure-for-IT/At-scale-discovery-and-assessment-for-ASPNET-app-migration-with-Azure-Migrate) |

## <a name="migrate-from-an-iis-server"></a>Migration à partir d’un serveur IIS

<!-- Intent: discover how to assess and migrate from a single IIS server  -->

Il est possible de migrer des applications web ASP.NET à partir d’un même serveur IIS découvert par le biais de l’expérience de découverte à grande échelle d’Azure Migrate avec des [scripts PowerShell](https://github.com/Azure/App-Service-Migration-Assistant/wiki/PowerShell-Scripts) [(télécharger)](https://appmigration.microsoft.com/api/download/psscriptpreview/AppServiceMigrationScripts.zip). Pour [plus d’informations sur la migration vers Azure App Service](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service), regardez la vidéo.

## <a name="aspnet-web-app-migration"></a>Migration d’application web ASP.NET
<!-- Intent: migrate a single web app -->

L’Assistant Migration App Service vous permet de [migrer votre application web ASP.NET locale autonome sur Azure App Service](https://www.youtube.com/watch?v=9LBUmkUhmXU). App Service Migration Assistant est destiné à simplifier votre parcours vers le cloud grâce à une solution gratuite, simple et rapide conçue pour migrer des applications locales vers le cloud. Pour plus d’informations sur l’outil Assistant Migration, consultez la [FAQ](https://github.com/Azure/App-Service-Migration-Assistant/wiki).

## <a name="containerize-an-aspnet-web-app"></a>Conteneurisation d’une application web ASP.NET

Certaines applications web .NET Framework peuvent présenter des dépendances à des bibliothèques et à d’autres fonctionnalités non disponibles dans Azure App Service. Il peut arriver qu’elles reposent sur d’autres composants du Global Assembly Cache. Auparavant, elles ne pouvaient s’exécuter que sur des machines virtuelles. Vous pouvez maintenant les exécuter dans des conteneurs Windows Azure App Service.

[L’outil de conteneurisation d’applications](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) peut repackager les applications sous forme de conteneurs avec des modifications minimes. Il prend actuellement en charge la conteneurisation d’applications ASP.NET et Java Apache Tomcat. Pour plus d’informations sur la conteneurisation et la migration, consultez le [guide pratique](../migrate/tutorial-app-containerization-aspnet-app-service.md).

## <a name="next-steps"></a>Étapes suivantes

[Migration d’une application web locale vers Azure App Service](/learn/modules/migrate-app-service-migration-assistant/)
