---
title: Introduction à Azure Spring Cloud
description: Découvrez les fonctionnalités et les avantages d’Azure Spring Cloud pour déployer et gérer des applications Spring Java dans Azure.
author: karlerickson
ms.service: spring-cloud
ms.topic: overview
ms.date: 03/09/2021
ms.author: karler
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: 8720a24e975ce7a73ed49f92460de301b5c8dfd8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485681"
---
# <a name="what-is-azure-spring-cloud"></a>Qu’est-ce qu’Azure Spring Cloud ?

Azure Spring Cloud permet de déployer facilement des applications de microservices Spring Boot sur Azure, et sans aucune modification du code.  Le service gère l’infrastructure des applications Spring Cloud, ce qui permet aux développeurs de se concentrer sur leur code.  Azure Spring Cloud assure la gestion du cycle de vie en utilisant des outils complets, tels que la supervision et les diagnostics, la gestion des configurations, la découverte de services, l’intégration CI/CD, les déploiements bleus-verts, etc.

La vidéo suivante montre une application composée d’applications Spring Boot s’exécutant sur Azure à l’aide d’Azure Spring Cloud.

<br>

> [!VIDEO https://www.youtube.com/embed/1jOXMFc1oRg]

## <a name="why-use-azure-spring-cloud"></a>Pourquoi utiliser Azure Spring Cloud ?

Le déploiement d’applications sur Azure Spring Cloud présente de nombreux avantages. Vous pouvez :

* Migrer efficacement les applications Spring existantes et gérer les coûts ainsi que la mise à l’échelle du cloud.
* Moderniser les applications à l’aide des modèles Spring Cloud pour améliorer l’agilité et la rapidité de livraison.
* Exécuter Java à l’échelle du cloud et augmenter l’utilisation sans infrastructure complexe.
* Développer et déployer rapidement sans dépendance de conteneurisation.
* Superviser efficacement et sans effort les charges de travail de production.

Azure Spring Cloud prend en charge les applications Java [Spring Boot](https://spring.io/projects/spring-boot) et ASP.NET Core [Steeltoe](https://steeltoe.io/). La prise en charge de Steeltoe est actuellement en préversion publique. Les offres en préversion publique vous permettent de tester les nouvelles fonctionnalités avant leur publication officielle.

## <a name="service-overview"></a>Présentation du service

Composant de l’écosystème Azure, Azure Spring Cloud permet une liaison facile avec d’autres services Azure, notamment le stockage, les bases de données, la supervision, etc.

![Présentation d’Azure Spring Cloud](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure Spring Cloud est un service entièrement managé pour les applications Spring Boot, ce qui vous permet de vous consacrer pleinement à la création et à l’exécution d’applications sans vous soucier de la gestion de l’infrastructure.

* Déployez simplement les fichiers JAR ou le code de votre application Spring Boot ou le fichier zip de votre application Steeltoe, et Azure Spring Cloud connectera automatiquement vos applications au runtime du service Spring et au cycle de vie des applications intégrées.

* La supervision est simple. Après le déploiement, vous pouvez superviser les performances des applications, corriger les erreurs et améliorer rapidement les applications.

* Intégration complète aux écosystèmes et services d’Azure.

* Azure Spring Cloud est adapté au monde de l’entreprise, avec son infrastructure entièrement managée, une gestion du cycle de vie intégrée et une facilité de supervision.

## <a name="documentation-overview"></a>Vue d’ensemble de la documentation

Cette documentation inclut des sections expliquant comment bien commencer et tirer parti des services Azure Spring Cloud.

* Bien démarrer

    * [Lancer votre première application](./quickstart.md)
    * [Provisionner un service Azure Spring Cloud](./quickstart-provision-service-instance.md)
    * [Configurer le serveur de configuration]()
    * [Créer et déployer des applications](./quickstart-deploy-apps.md)
    * [Utiliser les journaux, les métriques et le suivi](./quickstart-logs-metrics-tracing.md)

* Procédures

    * [Développement](how-to-prepare-app-deployment.md) : Préparez une application Java Spring existante pour le déploiement dans Azure Spring Cloud. Lorsqu’il est correctement configuré, Azure Spring Cloud fournit des services robustes pour superviser, mettre à l’échelle et mettre à jour des applications Java Spring Cloud.
    * [Deploy](./how-to-staging-environment.md) : Comment configurer un déploiement intermédiaire à l’aide du modèle de déploiement bleu-vert dans Azure Spring Cloud. Le déploiement bleu/vert est un modèle de livraison continue Azure DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d’une nouvelle version (verte).
    * [Configurez des applications](./how-to-start-stop-delete.md) : Démarrez, arrêtez et supprimez votre application dans Azure Spring Cloud. Changez l’état d’une application dans Azure Spring Cloud en utilisant le portail Azure ou l’interface Azure CLI.
    * [Mise à l’échelle](./how-to-scale-manual.md) : Mettez à l’échelle une application de microservices à partir du tableau de bord Azure Spring Cloud sur le portail Azure, ou à l’aide des paramètres de mise à l’échelle automatique. Les adresses IP publiques sont disponibles pour communiquer avec des ressources externes, telles que des bases de données, du stockage et des coffres de clés.
    * [Supervision des applications](./how-to-distributed-tracing.md) : Outils de suivi distribué pour déboguer et superviser facilement les problèmes complexes. Azure Spring Cloud intègre Azure Spring Cloud Sleuth au service Application Insights d’Azure. Cette intégration fournit une fonctionnalité puissante de suivi distribué qui est disponible dans le portail Azure.
    * [Applications sécurisées](./how-to-enable-system-assigned-managed-identity.md) : Les ressources Azure fournissent une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code.
    * [Intégration à d’autres services Azure](./how-to-bind-cosmos.md) : Au lieu de configurer manuellement vos applications Spring Boot, vous pouvez lier automatiquement à vos applications les services Azure sélectionnés, par exemple en liant votre application à une base de données Azure Cosmos DB.
    * [Automatisation](./how-to-cicd.md) : Les outils d’intégration continue et de livraison continue vous permettent de déployer rapidement des mises à jour d’applications existantes avec un minimum d’effort et de risque. Azure DevOps permet d’organiser et de contrôler ces tâches essentielles.
    * [Résoudre les problèmes](./how-to-self-diagnose-solve.md) : Les diagnostics Azure Spring Cloud offrent une expérience interactive permettant de résoudre les problèmes d’application. Aucune configuration n'est requise. Lorsque vous rencontrez des problèmes, la fonctionnalité de diagnostic Azure Spring Cloud identifie les incidents et vous guide vers les informations qui vous aident au dépannage et à la résolution des problèmes.
    * [Migration](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud) : Comment migrer une application Spring Cloud ou Spring Boot existante pour l’exécuter sur Azure Spring Cloud.

Pour commencer, consultez :

> [!div class="nextstepaction"]
> [Démarrage rapide de Spring Cloud](./quickstart.md)

Des exemples sont disponibles sur GitHub. [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
