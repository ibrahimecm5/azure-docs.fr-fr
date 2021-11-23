---
title: Guide pratique pour lancer votre application Spring Cloud à partir du code source
description: Dans ce guide de démarrage rapide, vous découvrirez comment lancer votre application dans Azure Spring Cloud directement à partir de votre code source.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/12/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0815f48fd9b194a84566138b25b4bedc97de1a83
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490323"
---
# <a name="how-to-deploy-spring-boot-applications-from-azure-cli"></a>Guide pratique pour déployer des applications Spring Boot à partir d’Azure CLI

**Cet article s’applique à :** ✔️ Java

Azure Spring Cloud active les applications Spring Boot sur Azure.

Vous pouvez lancer des applications directement à partir du code source Java ou d’un fichier JAR prédéfini. Cet article explique les procédures de déploiement.

Ce guide de démarrage rapide explique comment :

> [!div class="checklist"]
> * Provisionner une instance de service
> * Définir un serveur de configuration pour une instance
> * Générer une application localement
> * Déployer chaque application
> * Affecter un point de terminaison public pour votre application

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que votre abonnement Azure a les dépendances requises :

1. [Installez Git](https://git-scm.com/)
2. [Installez JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installez Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
4. [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli)
5. [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Pour en savoir plus sur Azure Cloud Shell, consultez [notre documentation](../cloud-shell/overview.md).

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante.

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Provisionner une instance de service à l’aide d’Azure CLI

Connectez-vous à Azure CLI et choisissez votre abonnement actif.

```azurecli
az login
az account list -o table
az account set --subscription
```

Créez un groupe de ressources destiné à contenir votre service dans Azure Spring Cloud. Vous pouvez en apprendre davantage sur les [groupes de ressources Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource-group-name>
```

Exécutez les commandes suivantes pour provisionner une instance d’Azure Spring Cloud. Préparez un nom pour votre service dans Azure Spring Cloud. Le nom doit comporter entre 4 et 32 caractères, et contenir uniquement des lettres minuscules, des chiffres et des traits d’union. Le premier caractère du nom du service doit être une lettre, et le dernier doit être une lettre ou un chiffre.

```azurecli
az spring-cloud create --resource-group <resource-group-name> --name <resource-name>
```

Le déploiement de l’instance de service prend environ cinq minutes.

Définissez le nom du groupe de ressources et le nom de l’instance Azure Spring Cloud par défaut en exécutant les commandes suivantes :

```azurecli
az config set defaults.group=<service-group-name>
az config set defaults.spring-cloud=<service-instance-name>
```

## <a name="create-the-application-in-azure-spring-cloud"></a>Créer l’application dans Azure Spring Cloud

La commande suivante crée une application dans Azure Spring Cloud, dans votre abonnement.  Elle crée un service vide dans lequel vous pouvez charger votre application.

```azurecli
az spring-cloud app create --name <app-name>
```

## <a name="deploy-your-spring-boot-application"></a>Déployer votre application Spring Boot

Vous pouvez déployer votre application à partir d’un fichier JAR prédéfini ou d’un référentiel Gradle ou Maven.  Vous trouverez ci-dessous des instructions pour chaque cas.

### <a name="deploy-a-pre-built-jar"></a>Déployer un fichier JAR prédéfini

Pour effectuer un déploiement à partir d’un fichier JAR généré sur votre ordinateur local, vérifiez que votre build produit un fichier [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Pour déployer le fichier fat-JAR dans un déploiement actif :

```azurecli
az spring-cloud app deploy --name <app-name> --jar-path <path-to-fat-JAR>
```

Pour déployer le fichier fat-JAR dans un déploiement spécifique :

```azurecli
az spring-cloud app deployment create --app <app-name> \
    --name <deployment-name> \
    --jar-path <path-to-fat-JAR>
```

### <a name="deploy-from-source-code"></a>Déployer à partir de code source

Azure Spring Cloud utilise [kpack](https://github.com/pivotal/kpack) pour générer votre projet.  Vous pouvez utiliser Azure CLI pour charger votre code source, générer votre projet à l’aide de kpack, et le déployer dans l’application cible.

> [!WARNING]
> Le projet ne doit produire qu’un seul fichier JAR avec une entrée `main-class` dans le `MANIFEST.MF` de `target` (pour les déploiements Maven) ou `build/libs` (pour les déploiements Gradle).  La présence de plusieurs fichiers JAR avec des entrées `main-class` entraînent l’échec du déploiement.

Pour les projets Maven/Gradle à un seul module :

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name>
```

Pour les projets Maven/Gradle avec plusieurs modules, répétez cette opération pour chaque module :

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name> \
    --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Afficher les journaux de déploiement

Passez en revue les journaux de génération kpack à l’aide de la commande suivante :

```azurecli
az spring-cloud app show-deploy-log --name <app-name>
```

> [!NOTE]
> Les journaux kpack affichent uniquement le dernier déploiement si celui-ci a été généré à partir de la source à l’aide de kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Affecter un point de terminaison public à la passerelle

1. Ouvrez la page **Tableau de bord de l’application**.
2. Sélectionnez l’application `gateway` pour afficher la page **Détails de l’application**.
3. Sélectionnez **Affecter un point de terminaison** pour affecter un point de terminaison public à la passerelle. Cette opération peut prendre quelques minutes.
4. Entrez l’adresse IP publique affectée dans votre navigateur pour voir votre application en cours d’exécution.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Provisionner une instance de service
> * Définir un serveur de configuration pour une instance
> * Générer une application localement
> * Déployer chaque application
> * Modifier les variables d’environnement pour les applications
> * Affecter une adresse IP publique à votre passerelle d’application

> [!div class="nextstepaction"]
> [Journaux, métriques et suivi Spring Cloud](./quickstart-logs-metrics-tracing.md)

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
