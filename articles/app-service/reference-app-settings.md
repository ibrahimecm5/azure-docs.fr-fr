---
title: Informations de référence sur les variables d’environnement et les paramètres d’application
description: Décrit les variables d’environnement couramment utilisées et celles qui peuvent être modifiées à l’aide des paramètres de l’application.
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: e315a51e7d160bcc2d8864cfa954924f0fe4c094
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025749"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Variables d’environnement et paramètres d’application dans Azure App Service

Dans [Azure App Service](overview.md), certains paramètres sont disponibles pour le déploiement ou l’environnement d’exécution en tant que variables d’environnement. Certains de ces paramètres peuvent être personnalisés quand vous les définissez manuellement en tant que [paramètres d’application](configure-common.md#configure-app-settings). Ce document de référence présente les variables que vous pouvez utiliser ou personnaliser.

## <a name="app-environment"></a>Environnement d’application

Les variables d’environnement suivantes sont liées à l’environnement de l’application en général.

| Nom du paramètre| Description | Exemple |
|-|-|-|
| `WEBSITE_SITE_NAME` | Lecture seule. Nom de l’application. ||
| `WEBSITE_RESOURCE_GROUP` | Lecture seule. Nom du groupe de ressources Azure qui contient la ressource d’application. ||
| `WEBSITE_OWNER_NAME` | Lecture seule. Contient l’ID d’abonnement Azure qui possède l’application, le groupe de ressources et l’espace web. ||
| `REGION_NAME` | Lecture seule. Nom de la région de l’application. ||
| `WEBSITE_PLATFORM_VERSION` | Lecture seule. Version de la plateforme App Service. ||
| `HOME` | Lecture seule. Chemin d’accès au répertoire de base (par exemple, `D:\home` pour Windows). ||
| `SERVER_PORT` | Lecture seule. Port que l’application doit écouter. | |
| `WEBSITE_WARMUP_PATH`  | Chemin d’accès relatif pour effectuer un test ping afin de précharger l’application, commençant par une barre oblique. La valeur par défaut est `/`, ce qui effectue un test ping sur le chemin racine. Un test ping peut être effectué sur le chemin d’accès spécifique par un client non authentifié, tel qu’Azure Traffic Manager, même si l’[authentification App Service](overview-authentication-authorization.md) est configurée pour rejeter les clients non authentifiés. (REMARQUE : Ce paramètre d’application ne modifie pas le chemin d’accès utilisé par Always On.) ||
| `WEBSITE_COMPUTE_MODE` | Lecture seule. Indique si l’application fonctionne sur des machines virtuelles dédiées (`Dedicated`) ou partagées (`Shared`). ||
| `WEBSITE_SKU` | Lecture seule. SKU de l’application. Les valeurs possibles sont `Free`, `Shared`, `Basic` et `Standard`. ||
| `SITE_BITNESS` | Lecture seule. Indique si l’application est 32 bits (`x86`) ou 64 bits (`AMD64`). ||
| `WEBSITE_HOSTNAME` | Lecture seule. Nom d’hôte principal de l’application. Les noms d’hôte personnalisés ne sont pas pris en compte ici. ||
| `WEBSITE_VOLUME_TYPE` | Lecture seule. Affiche le type de volume de stockage en cours d’utilisation. ||
| `WEBSITE_NPM_DEFAULT_VERSION` | Version par défaut de npm utilisée par l’application. ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | Lecture seule. Limite pour les demandes simultanées de WebSocket. Pour les niveaux **Standard** et supérieurs, la valeur est `-1`, mais il y a toujours une limite de machine virtuelle basée sur la taille de votre machine virtuelle (voir [Cross VM Numerical Limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) [Limites numériques des machines virtuelles]). ||
| `WEBSITE_PRIVATE_EXTENSIONS` | Définissez la valeur `0` pour désactiver l’utilisation des extensions de sites privées. ||
| `WEBSITE_TIME_ZONE` | Par défaut, le fuseau horaire de l’application est toujours UTC. Vous pouvez le remplacer par l’une des valeurs valides répertoriées dans [TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). Si la valeur spécifiée n’est pas reconnue, l’utilisation d’UTC prévaut. | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | En cas de basculement ou de reconfiguration d’un volume de stockage, votre application est basculée sur un volume de stockage de secours. Le paramètre par défaut `1` empêche le recyclage de votre processus Worker lorsque l’infrastructure de stockage change. Si vous exécutez une application Windows Communication Foundation (WCF), désactivez le paramètre en le définissant sur `0`. Ce paramètre étant spécifique à l’emplacement, vous devez le définir dans tous les emplacements. ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | Par défaut, une instance de machine virtuelle est « autoréparée » de manière proactive lorsqu’elle utilise plus de 90 % de la mémoire allouée pendant plus de 30 secondes ou lorsque 80 % du nombre total des requêtes des 2 dernières minutes durent plus de 200 secondes. Si une instance de machine virtuelle a déclenché l’une de ces règles, le processus de récupération consiste en un redémarrage superposé de l’instance. Définissez la valeur `false` pour désactiver ce comportement de récupération. Par défaut, il s’agit de `true`. Pour plus d’informations, consultez [Proactive Auto Heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html) [Répartition automatique proactive]. ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | Chaque fois que le processus w3wp.exe sur une instance de machine virtuelle de votre application se bloque en raison d’une exception non prise en charge plus de 3 fois en 24 heures, un processus débogueur est attaché au processus Worker principal de cette instance et collecte une image mémoire lorsque le processus Worker se bloque à nouveau. Cette image mémoire est ensuite analysée et la pile des appels du thread à l’origine de la panne est consignée dans vos journaux App Service. Définissez la valeur `false` pour désactiver ce comportement de surveillance automatique. Par défaut, il s’agit de `true`. Pour plus d’informations, consultez [Proactive Crash Monitoring](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html) [Surveillance proactive des pannes]. ||
| `WEBSITE_DAAS_STORAGE_SASURI` | Lors de la surveillance des pannes (proactive ou manuelle), les images mémoire sont supprimées par défaut. Pour enregistrer les images mémoire dans un conteneur de blobs de stockage, spécifiez l’URI SAS.  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | Définissez la valeur `true` pour activer la [surveillance des pannes](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) manuellement. Vous devez également définir `WEBSITE_DAAS_STORAGE_SASURI` et `WEBSITE_CRASHMONITORING_SETTINGS`. Par défaut, il s’agit de `false`. Ce paramètre n’a aucun effet si le débogage à distance est activé. De même, si ce paramètre est défini sur `true`, la [surveillance proactive des pannes](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) est désactivée. ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | JSON au format suivant : `{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}`. Requis pour configurer la [surveillance des pannes](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) si `WEBSITE_CRASHMONITORING_ENABLED` est spécifié. Pour enregistrer uniquement la pile des appels sans sauvegarder le vidage sur incident dans le compte de stockage, ajoutez `,"UseStorageAccount":"false"` dans le JSON. ||
| `REMOTEDEBUGGINGVERSION` | Version de débogage à distance. ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | Par défaut, App Service crée un stockage partagé pour vous au moment de la création de l’application. Pour utiliser un compte de stockage personnalisé à la place, définissez la chaîne de connexion de votre compte de stockage. Pour les fonctions, consultez [Informations de référence sur les paramètres d’application de Functions](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring). | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | Lorsque vous spécifiez un compte de stockage personnalisé avec `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`, App Service crée un partage de fichiers dans ce compte de stockage pour votre application. Pour utiliser un nom personnalisé, définissez cette variable sur le nom de votre choix. Si un partage de fichiers avec le nom spécifié n’existe pas, App Service le crée pour vous. | `myapp123` |
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | Lecture seule. Indique si la solution Always On est activée (`1`) ou non (`0`). ||
| `WEBSITE_SCM_SEPARATE_STATUS` | Lecture seule. Indique si l’application Kudu s’exécute dans un processus distinct (`1`) ou non (`0`). ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>Préfixes des variables

Le tableau suivant répertorie les préfixes de variables d’environnement qu’App Service utilise à différentes fins.

| Nom du paramètre | Description |
|-|-|
| `APPSETTING_` | Signifie qu’une variable est définie par le client en tant que paramètre d’application dans la configuration de l’application. Elle est injectée dans une application .NET en tant que paramètre d’application. |
| `MAINSITE_` | Signifie qu’une variable est spécifique à l’application elle-même. |
| `SCMSITE_` | Signifie qu’une variable est spécifique à l’application Kudu. |
| `SQLCONNSTR_` | Désigne une chaîne de connexion SQL Server dans la configuration de l’application. Elle est injectée dans une application .NET en tant que chaîne de connexion. |
| `SQLAZURECONNSTR_` | Désigne une chaîne de connexion Azure SQL Database dans la configuration de l’application. Elle est injectée dans une application .NET en tant que chaîne de connexion. |
| `POSTGRESQLCONNSTR_` | Désigne une chaîne de connexion PostgreSQL dans la configuration de l’application. Elle est injectée dans une application .NET en tant que chaîne de connexion. |
| `CUSTOMCONNSTR_` | Désigne une chaîne de connexion personnalisée dans la configuration de l’application. Elle est injectée dans une application .NET en tant que chaîne de connexion. |
| `MYSQLCONNSTR_` | Désigne une chaîne de connexion Azure SQL Database dans la configuration de l’application. Elle est injectée dans une application .NET en tant que chaîne de connexion. |
| `AZUREFILESSTORAGE_` | Chaîne de connexion à un partage personnalisé pour une application de conteneur dans Azure Files. |
| `AZUREBLOBSTORAGE_` | Chaîne de connexion à un compte de stockage personnalisé pour une application de conteneur dans le Stockage Blob Azure. |
| `NOTIFICATIONHUBCONNSTR_` | Désigne une chaîne de connexion à un hub de notification dans Azure Notification Hubs. |
| `SERVICEBUSCONNSTR_` | Désigne une chaîne de connexion à une instance d’Azure Service Bus. |
| `EVENTHUBCONNSTR_` | Désigne une chaîne de connexion à un Event Hub dans Azure Event Hubs. |
| `DOCDBCONNSTR_` | Désigne une chaîne de connexion à une base de données dans Azure Cosmos DB. |
| `REDISCACHECONNSTR_` | Désigne une chaîne de connexion à un cache dans Azure Cache pour Redis. |
| `FILESHARESTORAGE_` | Désigne une chaîne de connexion à un partage de fichiers personnalisé. |

## <a name="deployment"></a>Déploiement

Les variables d’environnement suivantes sont liées au déploiement d’applications. Pour les variables liées à l’automatisation de build App Service, consultez [Automatisation de build](#build-automation).

| Nom du paramètre| Description |
|-|-|
| `DEPLOYMENT_BRANCH`| Pour un déploiement [Git local](deploy-local-git.md) ou [Git cloud](deploy-continuous-deployment.md) (par exemple GitHub), définissez-le sur la branche dans Azure sur laquelle vous souhaitez effectuer le déploiement. Par défaut, il s’agit de `master`. |
| `WEBSITE_RUN_FROM_PACKAGE`| Définissez la valeur `1` pour exécuter l’application à partir d’un package ZIP local ou sur une URL externe pour exécuter l’application à partir d’un package ZIP distant. Pour plus d’informations, consultez [Exécuter votre application dans Azure App Service directement à partir d’un package ZIP](deploy-run-package.md). |
| `WEBSITE_USE_ZIP` | Action déconseillée. Utiliser `WEBSITE_RUN_FROM_PACKAGE`. |
| `WEBSITE_RUN_FROM_ZIP` | Action déconseillée. Utiliser `WEBSITE_RUN_FROM_PACKAGE`. | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | Définissez la valeur `false` pour que WebDeploy cesse d’utiliser le moteur de déploiement Kudu. Par défaut, il s’agit de `true`. Pour déployer des applications Linux à l’aide de Visual Studio (WebDeploy/MSDeploy), définissez la valeur `false`. |
| `MSDEPLOY_RENAME_LOCKED_FILES` | Définissez la valeur `1` pour tenter de renommer les bibliothèque de liens dynamiques si elles ne peuvent pas être copiées pendant un déploiement WebDeploy. Ce paramètre n’est pas applicable si `WEBSITE_WEBDEPLOY_USE_SCM` est défini sur `false`. |
| `WEBSITE_DISABLE_SCM_SEPARATION` | Par défaut, l’application principale et l’application Kudu s’exécutent dans des bacs à sable différents. Lorsque vous arrêtez l’application, l’application Kudu est toujours en cours d’exécution, et vous pouvez continuer à utiliser Git deploy et MSDeploy. Chaque application a ses propres fichiers locaux. La désactivation de cette séparation (paramètre défini sur `true`) est un mode hérité qui n’est plus entièrement pris en charge. |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | Définissez la valeur `1` pour garantir que les appels de l’API REST visant à mettre à jour `site` et `siteconfig` sont complètement appliqués à toutes les instances avant de revenir. La valeur par défaut est `1` si le déploiement est basé sur un modèle ARM, afin d’éviter des conditions de concurrence avec les appels ARM suivants. |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | Dans un déploiement avec un modèle ARM, définissez la valeur `1` dans le modèle ARM pour prédémarrer l’application Kudu dans le cadre de la création de l’application. |
| `WEBSITE_START_SCM_WITH_PRELOAD` | Pour les applications Linux, définissez la valeur `true` pour forcer le préchargement de l’application Kudu lorsque la solution Always On est activée par un test Ping sur son URL. Par défaut, il s’agit de `false`. Pour les applications Windows, l’application Kudu est toujours préchargée. |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>Automatisation de la génération

# <a name="kudu-windows"></a>[Kudu (Windows)](#tab/kudu)

La configuration de build Kudu s’applique aux applications Windows natives et permet de contrôler le comportement des déploiements basés sur Git (ou ZIP).

| Nom du paramètre| Description | Exemple |
|-|-|-|
| `SCM_BUILD_ARGS` | Ajoutez des éléments à la fin de la ligne de commande de MSBuild, de telle sorte qu’ils remplacent toutes les parties précédentes de la ligne de commande par défaut. | Pour une build propre : `-t:Clean;Compile`|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu utilise la commande `azure site deploymentscript` décrite [ici](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2) pour générer un script de déploiement. Il détecte automatiquement le type d’infrastructure du langage et détermine les paramètres à transmettre à la commande. Ce paramètre remplace les paramètres générés automatiquement. | Pour traiter votre référentiel comme des fichiers de contenu ordinaires : `--basic -p <folder-to-deploy>` |
| `SCM_TRACE_LEVEL` | Niveau de trace de la build. Par défaut, il s’agit de `1`. Définissez des valeurs plus élevées, jusqu’à 4, pour plus de traçage. | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | Délai d’attente en secondes pour chaque commande lancée par le processus de build avant toute génération de sortie. Après cela, la commande est considérée comme inactive et tuée. La valeur par défaut est `60` (une minute). Dans Azure, il existe également un délai d’attente général pour les requêtes inactives qui déconnecte les clients après 230 secondes. Toutefois, la commande continue de s’exécuter côté serveur. | |
| `SCM_LOGSTREAM_TIMEOUT` | Délai d’inactivité en secondes avant l’arrêt du streaming de journaux. La valeur par défaut est `1800` (30 minutes).| |
| `SCM_SITEEXTENSIONS_FEED_URL` | URL de la galerie d’extensions de site. Par défaut, il s’agit de `https://www.nuget.org/api/v2/`. L’URL de l’ancien flux est `http://www.siteextensions.net/api/v2/`. | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | Définissez la valeur `0` pour utiliser git.exe au lieu de libgit2sharp pour les opérations git. | |
| `WEBSITE_LOAD_USER_PROFILE` | En cas d’erreur `The specified user does not have a valid profile.` pendant l’automatisation de build d’ASP.NET (comme pendant le déploiement de Git), définissez cette variable sur `1` pour charger un profil utilisateur complet dans l’environnement de build. Ce paramètre est uniquement applicable lorsque `WEBSITE_COMPUTE_MODE` est `Dedicated`. | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | Délai d’expiration en minutes pour le site SCM (Kudu). Par défaut, il s’agit de `20`. | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | Avec un [déploiement de ZIP](deploy-zip.md), le moteur de déploiement suppose qu’un fichier ZIP est prêt à s’exécuter en l’état et n’effectue aucune automatisation de build. Pour activer la même automatisation de build que dans le [déploiement de Git](deploy-local-git.md), définissez la valeur `true`. |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx (Linux)](#tab/oryx)

La configuration de build Oryx s’applique aux applications Linux et permet de contrôler le comportement des déploiements basés sur Git (ou ZIP). Voir la [configuration d’Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

-----

## <a name="language-specific-settings"></a>Paramètres spécifiques au langage

Cette section présente les paramètres de runtime configurables pour chaque infrastructure de langage prise en charge. Des paramètres supplémentaires sont disponibles pendant l’[automatisation de build](#build-automation) au moment du déploiement.

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| Nom du paramètre | Description |
|-|-|
| `PORT` | Lecture seule. Pour les applications Linux, port que le runtime .NET écoute dans le conteneur. |
| `WEBSITE_ROLE_INSTANCE_ID` | Lecture seule. ID de l’instance actuelle. |
| `HOME` | Lecture seule. Répertoire qui pointe vers le stockage partagé (`/home`). |
| `DUMP_DIR` | Lecture seule. Répertoire pour les vidages sur incident (`/home/logs/dumps`). |
| `APP_SVC_RUN_FROM_COPY` | Applications Linux uniquement. Par défaut, l’application est exécutée à partir de `/home/site/wwwroot`, un répertoire partagé par toutes les instances avec Scale-out. Définissez cette variable sur `true` pour copier l’application dans un répertoire local de votre conteneur et l’exécuter à partir de là. Lorsque vous utilisez cette option, veillez à ne pas coder en dur toute référence à `/home/site/wwwroot`. Utilisez plutôt un chemin d’accès relatif vers `/home/site/wwwroot`. |
| `MACHINEKEY_Decryption` | Pour les applications natives Windows ou les applications conteneurs Windows, cette variable est injectée dans l’environnement d’applications ou le conteneur pour activer les routines de chiffrement d’ASP.NET (voir [Élément machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Pour remplacer la valeur `decryption` par défaut, configurez-la en tant que paramètre d’application App Service ou définissez-la directement dans l’élément `machineKey` du fichier *Web.config*. |
| `MACHINEKEY_DecryptionKey` | Pour les applications natives Windows ou les applications conteneurs Windows, cette variable est injectée dans l’environnement d’applications ou le conteneur pour activer les routines de chiffrement d’ASP.NET (voir [Élément machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Pour remplacer la valeur `decryptionKey` générée automatiquement, configurez-la en tant que paramètre d’application App Service ou définissez-la directement dans l’élément `machineKey` du fichier *Web.config*.|
| `MACHINEKEY_Validation` | Pour les applications natives Windows ou les applications conteneurs Windows, cette variable est injectée dans l’environnement d’applications ou le conteneur pour activer les routines de chiffrement d’ASP.NET (voir [Élément machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Pour remplacer la valeur `validation` par défaut, configurez-la en tant que paramètre d’application App Service ou définissez-la directement dans l’élément `machineKey` du fichier *Web.config*.|
| `MACHINEKEY_ValidationKey` | Pour les applications natives Windows ou les applications conteneurs Windows, cette variable est injectée dans l’environnement d’applications ou le conteneur pour activer les routines de chiffrement d’ASP.NET (voir [Élément machineKey](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Pour remplacer la valeur `validationKey` générée automatiquement, configurez-la en tant que paramètre d’application App Service ou définissez-la directement dans l’élément `machineKey` du fichier *Web.config*.|
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051 " --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| Nom du paramètre | Description | Exemple |
|-|-|-|
| `JAVA_HOME` | Chemin d’accès du répertoire d’installation de Java. ||
| `JAVA_OPTS` | Pour les applications Java SE, variables d’environnement à transmettre dans la commande `java`. Peut contenir des variables système. Pour Tomcat, utilisez `CATALINA_OPTS`. | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | Une variable d’environnement peut être utilisée par des scripts personnalisés afin que ces derniers disposent d’un emplacement pour app.jar après sa copie en local. | |
| `SKIP_JAVA_KEYSTORE_LOAD` | Valeur de 1 pour désactiver le chargement automatique des certificats dans le magasin de clés. ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | Fichier .jar à utiliser. Ajoute « .jar » si la chaîne ne se termine pas par « .jar ». La valeur par défaut est app.jar. ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | Fichier .war à utiliser. Ajoute « .war » si la chaîne ne se termine pas par « .war ». La valeur par défaut est app.war. ||
| `JAVA_ARGS` | Options Java requises par un autre serveur web Java. La valeur par défaut est `-noverify -Djava.net.preferIPv4Stack=true` ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | Variables d’environnement utilisées par les principales infrastructures web Java pour le port du serveur. Voici quelques-unes des infrastructures incluses : Spring, Micronaut, Grails, MicroProfile Thorntail, Helidon, Ratpack et Quarkus. ||
| `JAVA_TMP_DIR` | Ajouté aux arguments Java en tant que `-Dsite.tempdir`. La valeur par défaut est `TEMP`. ||
| `WEBSITE_SKIP_LOCAL_COPY` | Par défaut, le fichier app.jar déployé est copié depuis `/home/site/wwwroot` vers un emplacement local. Pour désactiver ce comportement et charger app.jar directement depuis `/home/site/wwwroot`, définissez cette variable sur `1` ou `true`. Ce paramètre n’a aucun effet si le cache local est activé. | |
| `TOMCAT_USE_STARTUP_BAT` | Applications Windows natives uniquement. Par défaut, le serveur Tomcat est démarré avec son fichier `startup.bat`. Pour le lancer en utilisant son fichier `catalina.bat` à la place, définissez la valeur `0` ou `False`. | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | Pour les applications Tomcat, variables d’environnement à transmettre dans la commande `java`. Peut contenir des variables système. | |
| `CATALINA_BASE` | Pour utiliser une installation personnalisée de Tomcat, définissez l’emplacement de l’installation. | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Tas maximal de Java en Mo. Ce paramètre est effectif uniquement lorsqu’une version expérimentale de Tomcat est utilisée. | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | Désactivez `WEBSITE_JAVA_MAX_HEAP_MB` manuellement en définissant cette variable sur `true` ou `1`. | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | Par défaut, l’[interface HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) de Tomcat qui suit est alimentée lorsque vous activez l’[authentification](overview-authentication-authorization.md) intégrée : `isSecure`, `getRemoteAddr`, `getRemoteHost`, `getScheme`, `getServerPort`. Pour le désactiver, définissez la valeur `1`.  | |
| `WEBSITE_SKIP_FILTERS` | Pour désactiver tous les filtres de servlet ajoutés par App Service, définissez la valeur `1`. ||
| `IGNORE_CATALINA_BASE` | Par défaut, App Service vérifie si la variable Tomcat `CATALINA_BASE` est définie. Si ce n’est pas le cas, il recherche l’existence de `%HOME%\tomcat\conf\server.xml`. Si le fichier existe, il définit `CATALINA_BASE` sur `%HOME%\tomcat`. Pour désactiver ce comportement et supprimer `CATALINA_BASE`, définissez cette variable sur `1` ou `true`. ||
| `PORT` | Lecture seule. Pour les applications Linux, port que le runtime Java écoute dans le conteneur. | |
| `WILDFLY_VERSION` | Lecture seule. Pour les applications JBoss (Linux), version de WildFly. | |
| `TOMCAT_VERSION` | Lecture seule. Pour les applications Linux Tomcat, version de Tomcat. ||
| `JBOSS_HOME` | Lecture seule. Pour les applications JBoss (Linux), chemin d’accès de l’installation de WildFly. | |
| `AZURE_JETTY9_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Jetty 9. | |
| `AZURE_JETTY9_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Jetty 9.| |
| `AZURE_JETTY93_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Jetty 9.3. | |
| `AZURE_JETTY93_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Jetty 9.3. | |
| `AZURE_TOMCAT7_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Tomcat 7. | |
| `AZURE_TOMCAT7_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Tomcat 7. | |
| `AZURE_TOMCAT8_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Tomcat 8. | |
| `AZURE_TOMCAT8_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Tomcat 8. | |
| `AZURE_TOMCAT85_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Tomcat 8.5. | |
| `AZURE_TOMCAT85_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Tomcat 8.5. | |
| `AZURE_TOMCAT90_CMDLINE` | Lecture seule. Pour les applications Windows natives, arguments de ligne de commande pour démarrer Tomcat 9. | |
| `AZURE_TOMCAT90_HOME` | Lecture seule. Pour les applications Windows natives, chemin d’accès à l’installation de Tomcat 9. | |
| `AZURE_SITE_HOME` | Valeur ajoutée aux arguments Java en tant que `-Dsite.home`. La valeur par défaut correspond à la valeur de `HOME`. | |
| `HTTP_PLATFORM_PORT` | Ajouté aux arguments Java en tant que `-Dport.http`. Les variables d’environnement suivantes utilisées par différentes infrastructures web Java sont également définies sur cette valeur : `SERVER_PORT`, `MICRONAUT_SERVER_PORT`, `THORNTAIL_HTTP_PORT`, `RATPACK_PORT`, `QUARKUS_HTTP_PORT`, `PAYARAMICRO_PORT`. ||
| `AZURE_LOGGING_DIR` | Applications Windows natives uniquement. Ajouté aux arguments Java en tant que `-Dsite.logdir`. Par défaut, il s’agit de `%HOME%\LogFiles\`. ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.JS](#tab/node)

| Nom du paramètre | Description |
|-|-|
| `PORT` | Lecture seule. Pour les applications Linux, port que l’application Node.js écoute dans le conteneur. |
| `WEBSITE_ROLE_INSTANCE_ID` | Lecture seule. ID de l’instance actuelle. |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | Pour les applications Windows natives, version de nœud par défaut utilisée par l’application. Toutes les [versions de Node.js prises en charge](configure-language-nodejs.md#show-nodejs-version) peuvent être utilisées ici. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| Nom du paramètre | Description |
|-|-|
| `APPSVC_VIRTUAL_ENV` | Lecture seule. |
| `PORT` | Lecture seule. Pour les applications Linux, port que l’application Python écoute dans le conteneur. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| Nom du paramètre | Description | Exemple|
|-|-|-|
| `PHP_Extensions` | Liste d’extensions PHP séparées par des virgules. | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | Pour les applications natives de Windows, définissez la valeur sur le chemin d’accès de l’extension XDebug, par exemple `D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll`. Pour les applications Linux, définissez la valeur `xdebug` pour utiliser la version XDebug du conteneur PHP. ||
| `PHP_VERSION` | Lecture seule. Version de PHP sélectionnée. ||
| `PORT` | Lecture seule. Port que le serveur Apache écoute dans le conteneur. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Lecture seule. ID de l’instance actuelle. ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | Définissez la valeur `TRUE` pour ajouter `xdebug.profiler_enable_trigger=1` et `xdebug.profiler_enable=0` au `php.ini` par défaut. ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | Définissez la valeur `TRUE` pour consigner les requêtes adressées au serveur (`CustomLog \dev\stderr combined` est ajouté à `/etc/apache2/apache2.conf`). ||
| `APACHE_SERVER_LIMIT` | Variable spécifique à Apache. Par défaut, il s’agit de `1000`. ||
| `APACHE_MAX_REQ_WORKERS` | Variable spécifique à Apache. Par défaut, il s’agit de `256`. ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| Nom du paramètre | Description | Exemple |
|-|-|-|
| `PORT` | Lecture seule. Port que l’application Rails écoute dans le conteneur. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Lecture seule. ID de l’instance actuelle. ||
| `RAILS_IGNORE_SPLASH` | Par défaut, une splash page s’affiche lorsqu’aucun Gemfile n’est trouvé. Définissez cette variable sur n’importe quelle valeur pour désactiver la splash page. ||
| `BUNDLE_WITHOUT` | Pour ajouter des options `--without` à `bundle install`, définissez cette variable sur les groupes que vous souhaitez exclure, séparés par des espaces. Par défaut, tous les gems sont installés. | `test development` |
| `BUNDLE_INSTALL_LOCATION` | Répertoire d’installation des gems. Par défaut, il s’agit de `/tmp/bundle`. ||
| `RUBY_SITE_CONFIG_DIR` | Répertoire de configuration du site. Par défaut, il s’agit de `/home/site/config`. Le conteneur recherche les gems zippés dans ce répertoire. ||
| `SECRET_KEY_BASE` | Par défaut, une base de clé secrète aléatoire est générée. Pour utiliser une base de clé secrète personnalisée, définissez cette variable sur la base de clé souhaitée. ||
| `RAILS_ENV` | Environnement Rails. Par défaut, il s’agit de `production`. ||
| `GEM_PRISTINE` | Définissez cette variable sur une valeur quelconque pour exécuter `gem pristine --all`. ||

-----

## <a name="domain-and-dns"></a>Domaine et DNS

| Nom du paramètre| Description | Exemple |
|-|-|-|
| `WEBSITE_DNS_SERVER` | Adresse IP du serveur DNS principal pour les connexions sortantes (par exemple, vers un service principal). Le serveur DNS par défaut pour App Service est Azure DNS, dont l’adresse IP est `168.63.129.16`. Si votre application utilise l’[intégration au réseau virtuel](./overview-vnet-integration.md) ou se trouve dans un [environnement App Service](environment/intro.md), elle hérite par défaut de la configuration du serveur DNS du réseau virtuel. | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | Adresse IP du serveur DNS de secours pour les connexions sortantes. Consultez `WEBSITE_DNS_SERVER`. | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tlsssl"></a>TLS/SSL

Pour plus d’informations, consultez [Utiliser un certificat TLS/SSL dans votre code dans Azure App Service](configure-ssl-certificate-in-code.md).

| Nom du paramètre| Description |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | Valeurs d’empreinte séparées par des virgules dans le certificat que vous souhaitez charger dans votre code, ou `*` pour autoriser le chargement de tous les certificats dans le code. Seuls les [certificats ajoutés à votre application](configure-ssl-certificate.md) peuvent être chargés. |
| `WEBSITE_PRIVATE_CERTS_PATH` | Lecture seule. Chemin d’accès d’un conteneur Windows aux certificats privés chargés. |
| `WEBSITE_PUBLIC_CERTS_PATH` | Lecture seule. Chemin d’accès d’un conteneur Windows aux certificats publics chargés. |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | Lecture seule. Chemin d’accès d’un conteneur Windows aux certificats intermédiaires chargés. |
| `WEBSITE_ROOT_CERTS_PATH` | Lecture seule. Chemin d’accès d’un conteneur Windows aux certificats racine chargés. |

## <a name="deployment-slots"></a>Emplacements de déploiement 

Pour plus d’informations sur les emplacements de déploiement, consultez [Configurer des environnements intermédiaires dans Azure App Service](deploy-staging-slots.md).

| Nom du paramètre| Description | Exemple |
|-|-|-|
|`WEBSITE_SLOT_NAME`| Lecture seule. Nom de l’emplacement de déploiement actuel. Le nom de l’emplacement de production est `Production`. ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| Par défaut, les versions des extensions de site sont spécifiques à chaque emplacement. Cela permet d’éviter un comportement inattendu de l’application en raison de la modification des versions d’extension après un échange. Si vous voulez que les versions des extensions soient également échangées, définissez la valeur `1` sur *tous les emplacements*. ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| Désigne certains paramètres comme [temporaires ou non échangeables par défaut](deploy-staging-slots.md#which-settings-are-swapped). La valeur par défaut est `true`. Définissez ce paramètre sur `false` ou `0` pour *tous les emplacements de déploiement* pour les rendre interchangeables. Il n’existe pas de contrôle précis pour les types de paramètres spécifiques. ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| Chemin d’accès pour effectuer un test ping afin de précharger l’emplacement cible dans un échange, commençant par une barre oblique. La valeur par défaut est `/`, ce qui effectue un test ping via HTTP sur le chemin racine. | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| Codes de réponse HTTP valides pour l’opération d’initialisation pendant un échange. Si le code d’état retourné ne figure pas dans la liste, les opérations d’initialisation et d’échange sont arrêtées. Par défaut, tous les codes de réponse sont valides. | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | Lors d’un échange d’emplacement, nombre maximum de délais d’attente après lesquels nous forçons le redémarrage du site sur une instance de machine virtuelle spécifique. Par défaut, il s’agit de `3`. ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | Lors d’un échange d’emplacement, nombre maximum de demandes de délai d’attente pour une URL unique à effectuer avant d’abandonner. Par défaut, il s’agit de `5`. ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | Définissez la valeur `true` ou `1` pour ignorer toutes les liaisons dans `applicationHost.config`. Par défaut, il s’agit de `false`. Si votre application déclenche un redémarrage parce que `applicationHost.config` est mis à jour avec les noms d’hôte échangés des emplacements, définissez cette variable sur `true` pour éviter un tel redémarrage. Si vous exécutez une application Windows Communication Foundation (WCF), ne définissez pas cette variable. ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>Conteneurs personnalisés

Pour plus d’informations sur les conteneurs personnalisés, consultez [Exécuter un conteneur personnalisé dans Azure](quickstart-custom-container.md).

| Nom du paramètre| Description | Exemple |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | Définissez la valeur `true` pour permettre au répertoire `/home` d’être partagé entre les instances mises à l’échelle. La valeur par défaut est `false` pour les conteneurs personnalisés. ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | Durée en secondes à attendre que le conteneur termine son démarrage avant de le redémarrer. La valeur par défaut est `230`. Vous pouvez l’augmenter jusqu’à la valeur maximale de `1800`. ||
| `DOCKER_REGISTRY_SERVER_URL` | URL du serveur de registre, lors de l’exécution d’un conteneur personnalisé dans App Service. Pour des raisons de sécurité, cette variable n’est pas transmise au conteneur. | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | Nom d’utilisateur permettant de s’authentifier auprès du serveur de registre à l’adresse `DOCKER_REGISTRY_SERVER_URL`. Pour des raisons de sécurité, cette variable n’est pas transmise au conteneur. ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | Mot de passe permettant de s’authentifier auprès du serveur de registre à l’adresse `DOCKER_REGISTRY_SERVER_URL`. Pour des raisons de sécurité, cette variable n’est pas transmise au conteneur. ||
| `WEBSITES_WEB_CONTAINER_NAME` | Dans une application Docker Compose, un seul des conteneurs peut être accessible via Internet. Définissez le nom du conteneur défini dans le fichier config pour remplacer la sélection de conteneur par défaut. Par défaut, le conteneur accessible sur Internet est le premier conteneur à définir le port 80 ou 8080, ou, si aucun conteneur n’est trouvé, le premier conteneur défini dans le fichier config. |  |
| `WEBSITES_PORT` | Pour un conteneur personnalisé, numéro de port personnalisé du conteneur pour App Service vers lequel acheminer les requêtes. Par défaut, App Service tente de détecter automatiquement les ports 80 et 8080. Ce paramètre n’est *pas* injecté dans le conteneur en tant que variable d’environnement. ||
| `WEBSITE_CPU_CORES_LIMIT` | Par défaut, un conteneur Windows s’exécute avec tous les cœurs disponibles pour le niveau tarifaire choisi. Pour réduire le nombre de cœurs, définissez la limite du nombre de cœurs souhaitée. Pour plus d’informations, consultez [Personnaliser le nombre de cœurs de calcul](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores).||
| `WEBSITE_MEMORY_LIMIT_MB` | Par défaut, tous les conteneurs Windows déployés dans Azure App Service sont limités à 1 Go de RAM. Définissez la limite de mémoire souhaitée en Mo. Le total cumulé de ce paramètre pour toutes les applications du même plan ne doit pas dépasser la quantité autorisée par le niveau tarifaire choisi. Pour plus d’informations, consultez [Personnaliser la mémoire des conteneurs](configure-custom-container.md?pivots=container-windows#customize-container-memory). ||
| `CONTAINER_WINRM_ENABLED` | Pour une application de conteneur Windows, définissez la valeur `1` pour activer Windows Remote Management (WinRM). ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>Mise à l'échelle

| Nom du paramètre| Description |
|-|-|
| `WEBSITE_INSTANCE_ID` | Lecture seule. ID unique de l’instance de machine virtuelle actuelle, lorsque l’application est étendue à plusieurs instances. |
| `WEBSITE_IIS_SITE_NAME` | Action déconseillée. Utiliser `WEBSITE_INSTANCE_ID`. |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | Le recyclage avec chevauchement fait en sorte qu’avant l’arrêt de l’instance de machine virtuelle actuelle d’une application, une nouvelle instance de machine virtuelle démarre. Dans certains cas, cela peut causer des problèmes de verrouillage de fichiers. Vous pouvez essayer de le désactiver en le définissant sur `1`. |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | Par défaut, les applications sont autorisées à s’étendre sur plusieurs unités d’échelle si elles utilisent Azure Files ou un conteneur Docker. Définissez la valeur `1` ou `true` pour désactiver la mise à l’échelle sur plusieurs unités d’échelle dans la région de l’application. Par défaut, il s’agit de `0`. Les conteneurs Docker personnalisés qui définissent `WEBSITES_ENABLE_APP_SERVICE_STORAGE` sur `true` ou `1` ne peuvent pas mettre à l’échelle sur plusieurs unités d’échelle, car leur contenu n’est pas complètement encapsulé dans le conteneur Docker. |

## <a name="logging"></a>Journalisation

| Nom du paramètre| Description | Exemple |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | Lecture seule. Indique si la journalisation du serveur web pour les applications natives de Windows est activée (`1`) ou non (`0`). ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | Période de rétention, en jours, des journaux du serveur web pour les applications natives de Windows, si les journaux du serveur web sont activés. | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | URL SAP du conteneur de stockage de blobs pour stocker les journaux du serveur web pour les applications natives de Windows, si les journaux du serveur web sont activés. Si la valeur n’est pas définie, les journaux du serveur web sont stockés dans le système de fichiers de l’application (stockage partagé par défaut). | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | Période de rétention, en jours, des journaux d’application pour les applications natives de Windows, si les journaux d’application sont activés. | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | URL SAP du conteneur de stockage de blobs pour stocker les journaux d’application pour les applications natives de Windows, si les journaux d’application sont activés. | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | Niveau minimum des journaux à envoyer à Log Analytics pour le type de journal [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types). | |
| `DIAGNOSTICS_LASTRESORTFILE` | Nom de fichier à créer ou chemin d’accès relatif au répertoire des journaux pour la journalisation des erreurs internes permettant de dépanner l’écouteur. Par défaut, il s’agit de `logging-errors.txt`. ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | Chemin d’accès au fichier de paramètres du journal, relatif à `D:\home` ou `/home`. Par défaut, il s’agit de `site\diagnostics\settings.json`. | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | Dossier du journal, relatif à la racine de l’application (`D:\home\site\wwwroot` ou `/home/site/wwwroot`). | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | Taille maximale du fichier journal, en octets. La valeur par défaut est `131072` (128 ko). ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | Taille maximale du dossier du journal, en octets. La valeur par défaut est `1048576` (1 Mo). ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | Nombre maximal de fichiers journaux à conserver. Par défaut, il s’agit de `20`. | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | Délai d’attente, en millisecondes, pour maintenir la journalisation des applications activée. La valeur par défaut est `43200000` (12 heures). ||
| `WEBSITE_LOG_BUFFERING` | Par défaut, la mise en mémoire tampon des journaux est activée. Définissez la valeur `0` pour la désactiver. ||
| `WEBSITE_ENABLE_PERF_MODE` | Pour les applications Windows natives, définissez la valeur `TRUE` pour désactiver les entrées du journal IIS pour les requêtes réussies renvoyées dans les dix secondes. Il s’agit d’un moyen rapide de définir des points de référence du niveau de performance en supprimant la journalisation étendue. ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>Compteurs de performance

Les variables suivantes sont de « fausses » variables d’environnement qui n’existent pas si vous les énumérez, mais qui renvoient leur valeur si vous les recherchez individuellement. La valeur est dynamique et peut changer à chaque recherche.

| Nom du paramètre| Description |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | Objet JSON contenant les compteurs de performance ASP.NET. |
| `WEBSITE_COUNTERS_APP` | Objet JSON contenant des compteurs de bac à sable. |
| `WEBSITE_COUNTERS_CLR` | Objet JSON contenant des compteurs CLR. |
| `WEBSITE_COUNTERS_ALL` | Objet JSON contenant la combinaison des trois autres variables. |

## <a name="caching"></a>Mise en cache

| Nom du paramètre| Description |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | Indique si le cache local est activé. Options disponibles : <br/>- `Default` : Hérite du paramètre global au niveau de l’unité d’échelle.<br/>- `Always` : Activer pour l’application.<br/>- OnStorageUnavailability<br/>- `Disabled` : Désactivé pour l’application. |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | Options de lecture-écriture du cache local. Options disponibles : <br/>- `ReadOnly` : Le cache est en lecture seule.<br/>- `WriteWithCopyBack` : Autorise les écritures dans le cache local et les copie régulièrement dans le stockage partagé. S’applique uniquement aux applications à instance unique, car le site SCM pointe vers le cache local.<br/>- `WriteButDiscardChanges` : Autorise les écritures dans le cache local, mais rejette les modifications apportées localement. |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | Taille du cache local en Mo. La valeur par défaut est `1000` (1 Go). |
| `WEBSITE_LOCALCACHE_READY` | Indicateur en lecture seule indiquant si l’application utilise le cache local. |
| `WEBSITE_DYNAMIC_CACHE` | En raison de la nature partagée des fichiers réseau pour autoriser l’accès à plusieurs instances, le cache dynamique améliore les performances en mettant en cache les fichiers consultés récemment et localement sur une instance. Le cache devient non valide lorsque le fichier est modifié. L’emplacement du cache est `%SYSTEMDRIVE%\local\DynamicCache` (le même quota `%SYSTEMDRIVE%\local` est appliqué). Par défaut, la mise en cache complète du contenu est activée (définie sur `1`), qui comprend à la fois le contenu du fichier et les métadonnées du répertoire/fichier (timestamps, taille, contenu du répertoire). Pour économiser l’utilisation du disque local, définissez la valeur `2` pour mettre en cache uniquement les métadonnées du répertoire/fichier (timestamp, taille, contenu du répertoire). Pour désactiver la mise en cache, définissez la valeur `0`. |
| `WEBSITE_READONLY_APP` | Lorsque vous utilisez le cache dynamique, vous pouvez désactiver l’accès en écriture à la racine de l’application (`D:\home\site\wwwroot` ou `/home/site/wwwroot`) en définissant cette variable sur `1`. À l’exception du répertoire `App_Data`, aucun verrou exclusif n’est autorisé, afin que les déploiements ne soient pas bloqués par des fichiers verrouillés. |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>Mise en réseau

Les variables d’environnement suivantes sont liées aux [connexions hybrides](app-service-hybrid-connections.md) et à l’[intégration au réseau virtuel](./overview-vnet-integration.md).

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_RELAYS` | Lecture seule. Données nécessaires à la configuration de la connexion hybride, notamment les points de terminaison et les données du bus de service. |
| `WEBSITE_REWRITE_TABLE` | Lecture seule. Utilisé lors de l’exécution pour effectuer les recherches et réécrire les connexions de manière appropriée. | 
| `WEBSITE_VNET_ROUTE_ALL` | Par défaut, si vous utilisez l’[intégration au réseau virtuel régional](./overview-vnet-integration.md#regional-virtual-network-integration), votre application achemine uniquement le trafic RFC1918 vers votre réseau virtuel. Définissez la valeur `1` pour acheminer tout le trafic sortant vers votre réseau virtuel et être soumis aux mêmes groupes de sécurité réseau et itinéraires définis par l’utilisateur. Ce paramètre vous permet d’accéder à des points de terminaison non RFC1918 via votre réseau virtuel, de sécuriser tout le trafic sortant quittant votre application et de forcer le tunneling de tout le trafic sortant vers une appliance réseau de votre choix. |
| `WEBSITE_PRIVATE_IP` | Lecture seule. Adresse IP associée à l’application lorsqu’elle est [intégrée à un réseau virtuel](./overview-vnet-integration.md). Pour l’intégration au réseau virtuel régional, la valeur est une adresse IP de la plage d’adresses du sous-réseau délégué et, pour l’intégration au réseau virtuel avec passerelle obligatoire, la valeur est une adresse IP de la plage d’adresses du pool d’adresses point à site configuré sur la passerelle de réseau virtuel. Cette adresse IP est utilisée par l’application pour se connecter aux ressources via le réseau virtuel. Elle peut également changer dans la plage d’adresses décrite. |
| `WEBSITE_PRIVATE_PORTS` | Lecture seule. Dans l’intégration au réseau virtuel, indique les ports que l’application peut utiliser pour communiquer avec d’autres nœuds. |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>Références des coffres de clés

Les variables d’environnement suivantes sont liées aux [références des coffres de clés](app-service-key-vault-references.md).

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | Lecture seule. Contient des informations (dont les états) pour toutes les références Key Vault qui sont actuellement configurées dans l’application. |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | Si vous définissez la connexion de stockage partagé de votre application (à l’aide de `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`) sur une référence Key Vault, l’application ne peut pas résoudre la référence du coffre de clés lors de la création ou mise à jour de l’application si l’une des conditions suivantes est vraie : <br/>– L’application accède au coffre de clés avec une identité affectée par le système.<br/>– L’application accède au coffre de clés avec une identité affectée par l’utilisateur, et le coffre de clés est [verrouillé par un réseau virtuel](../key-vault/general/overview-vnet-service-endpoints.md).<br/>Pour éviter les erreurs au moment de la création ou de la mise à jour, définissez cette variable sur `1`. |
| `WEBSITE_DELAY_CERT_DELETION` | Cette variable d’environnement peut être définie sur la valeur 1 par les utilisateurs afin de s’assurer qu’un certificat dont dépend un processus Worker n’est pas supprimé tant qu’il n’est pas fermé. |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

Les variables d’environnement suivantes sont liées à la configuration du mécanisme CORS (Cross-Origin Resource Sharing).

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | Lecture seule. Indique les origines autorisées pour CORS. |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | Lecture seule. Indique si la définition de l’en-tête `Access-Control-Allow-Credentials` sur `true` est activée (`True`) ou non (`False`). |

## <a name="authentication--authorization"></a>Authentification et autorisation

Les variables d’environnement suivantes sont liées à l’[authentification App Service](overview-authentication-authorization.md).

| Nom du paramètre| Description|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | Lorsque définie sur la valeur `true`, la variable désactive l’attribution de l’identité principale du thread dans les applications web basées sur ASP.NET (y compris les applications de fonction v1). Cela est conçu pour permettre aux développeurs de protéger l’accès à leur site avec l’authentification, tout en continuant à utiliser un mécanisme de connexion distinct dans leur logique d’application. Par défaut, il s’agit de `false`. |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` ou `false`. La valeur par défaut est `false`. Il s’agit d’un paramètre pour l’intégration héritée d’Azure Mobile Apps pour Azure App Service. La définition de cette variable sur `true` résout un problème où le SID (ID de sécurité) généré pour les utilisateurs authentifiés peut changer si l’utilisateur modifie ses informations de profil. La modification de cette valeur peut entraîner la modification des identifiants d’utilisateur Azure Mobile Apps existants. La plupart des applications n’ont pas besoin d’utiliser ce paramètre. |
| `WEBSITE_AUTH_NONCE_DURATION`| Valeur d’_intervalle de temps_ au format `_hours_:_minutes_:_seconds_`. La valeur par défaut est `00:05:00` (5 minutes). Ce paramètre contrôle la durée de vie du [nonce de chiffrement](https://en.wikipedia.org/wiki/Cryptographic_nonce) généré pour toutes les connexions par navigateur. Si une connexion n’aboutit pas dans le délai spécifié, le flux de connexion est automatiquement relancé. Ce paramètre d’application est destiné à être utilisé avec l’expérience de configuration v1 (classique). Si vous utilisez le schéma de configuration d’authentification v2, vous devez plutôt utiliser la valeur de configuration `login.nonce.nonceExpirationInterval`. |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | Lorsque la valeur `true` est définie et que les utilisateurs cliquent sur des liens d’application qui contiennent des fragments d’URL, le processus de connexion s’assure que la partie fragment d’URL de votre URL ne se perd pas dans le processus de redirection de la connexion. Pour plus d’informations, consultez [Personnaliser la connexion et la déconnexion dans l’authentification Azure App Service](configure-authentication-customize-sign-in-out.md#preserve-url-fragments). |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | Pour assurer la compatibilité descendante entre les mises à niveau, le module d’authentification utilise le mappage hérité des revendications des noms courts aux noms longs dans l’API `/.auth/me`, de sorte que certains mappages sont exclus (par exemple, « rôles »). Pour obtenir la version plus moderne des mappages de revendications, définissez cette variable sur `False`. Dans la cas de « rôles », il serait mappé au nom de revendication long « http://schemas.microsoft.com/ws/2008/06/identity/claims/role  ». |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` ou `false`. La valeur par défaut est `false`. Lorsqu’il est défini sur `true`, il supprime l’en-tête de réponse HTTP [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate) des réponses HTTP 401 générées par le module. Ce paramètre d’application est destiné à être utilisé avec l’expérience de configuration v1 (classique). Si vous utilisez le schéma de configuration d’authentification v2, vous devez plutôt utiliser la valeur de configuration `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate`. |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | Chemin d’accès au répertoire du système de fichiers local dans lequel les jetons sont stockés lorsque le magasin de jetons basé sur les fichiers est activé. La valeur par défaut est `%HOME%\Data\.auth`. Ce paramètre d’application est destiné à être utilisé avec l’expérience de configuration v1 (classique). Si vous utilisez le schéma de configuration d’authentification v2, vous devez plutôt utiliser la valeur de configuration `login.tokenStore.fileSystem.directory`. |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | URL de conteneur de blobs complet. Indique au module d’authentification de stocker et de charger tous les jetons chiffrés dans le conteneur de stockage de blobs spécifié au lieu d’utiliser le système de fichiers local par défaut.  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | Tout nombre décimal positif. La valeur par défaut est `72` (heures). Ce paramètre contrôle la durée qui s’écoule après l’expiration d’un jeton de session avant que l’API `/.auth/refresh` puisse être utilisée pour l’actualiser. Il est principalement destiné à une utilisation avec Azure Mobile Apps, qui s’appuie sur des jetons de session. Les tentatives d’actualisation après cette période échoueront et les utilisateurs finaux devront se reconnecter. Ce paramètre d’application est destiné à être utilisé avec l’expérience de configuration v1 (classique). Si vous utilisez le schéma de configuration d’authentification v2, vous devez plutôt utiliser la valeur de configuration `login.tokenStore.tokenRefreshExtensionHours`. |
| `WEBSITE_AUTH_TRACE_LEVEL`| Contrôle la verbosité des traces d’authentification écrites dans [Application Logging](troubleshoot-diagnostic-logs.md#enable-application-logging-windows). Les valeurs acceptables sont : `Off`, `Error`, `Warning`, `Information` et `Verbose`. La valeur par défaut est `Verbose`. |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` ou `false`. La valeur par défaut est `true`. Cette valeur ne doit jamais être définie sur `false`, sauf pour déboguer temporairement les échecs de validation du [nonce de chiffrement](https://en.wikipedia.org/wiki/Cryptographic_nonce) qui se produisent lors des connexions interactives. Ce paramètre d’application est destiné à être utilisé avec l’expérience de configuration v1 (classique). Si vous utilisez le schéma de configuration d’authentification v2, vous devez plutôt utiliser la valeur de configuration `login.nonce.validateNonce`. |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | Cette variable d’environnement est automatiquement renseignée par la plateforme Azure App Service et est utilisée pour configurer le module d’authentification intégrée. La valeur de cette variable d’environnement correspond à la configuration d’authentification v2 (non classique) pour l’application actuelle dans Azure Resource Manager. Elle n’est pas destinée à être configurée explicitement. |
| `WEBSITE_AUTH_ENABLED` | Lecture seule. Injecté dans une application Windows ou Linux pour indiquer si l’authentification App Service est activée. |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | Par défaut, la clé générée automatiquement est utilisée comme clé de chiffrement. Pour la remplacer, définissez la clé de votre choix. Cette option est recommandée si vous souhaitez partager des jetons ou des sessions entre plusieurs applications. Si elle est spécifiée, il remplace le paramètre `MACHINEKEY_DecryptionKey`. |
| `WEBSITE_AUTH_SIGNING_KEY` | Par défaut, la clé générée automatiquement est utilisée comme clé de signature. Pour la remplacer, définissez la clé de votre choix. Cette option est recommandée si vous souhaitez partager des jetons ou des sessions entre plusieurs applications. Si elle est spécifiée, il remplace le paramètre `MACHINEKEY_ValidationKey`. |

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>Identité managée

Les variables d’environnement suivantes sont liées aux [identités managées](overview-managed-identity.md).

|Nom du paramètre | Description |
|-|-|
|`IDENTITY_ENDPOINT` | Lecture seule. URL permettant de récupérer le jeton pour l’[identité managée](overview-managed-identity.md) de l’application. |
| `MSI_ENDPOINT` | Action déconseillée. Utiliser `IDENTITY_ENDPOINT`. |
| `IDENTITY_HEADER` | Lecture seule. Valeur qui doit être ajoutée à l’en-tête `X-IDENTITY-HEADER` lors d’une requête HTTP GET adressée à `IDENTITY_ENDPOINT`. La plateforme effectue la rotation de la valeur. |
| `MSI_SECRET` | Action déconseillée. Utiliser `IDENTITY_HEADER`. |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | Disabled by default? TODO | -->

## <a name="health-check"></a>Contrôle d’intégrité

Les variables d’environnement suivantes sont liées aux [contrôles d’intégrité](monitor-instances-health-check.md).

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | Nombre maximal d’échecs de tests Ping avant la suppression de l’instance. Définissez une valeur comprise entre `2` et `100`. Si vous effectuez un scale-up ou un scale-out, App Service effectue un test ping sur le chemin de contrôle d’intégrité pour vérifier que de nouvelles instances sont prêtes. Pour plus d’informations, consultez [Contrôle d’intégrité](monitor-instances-health-check.md).|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | Pour éviter de submerger les instances saines, pas plus de la moitié des instances est exclue. Par exemple, si un plan App Service est mis à l’échelle vers quatre instances et que trois d’entre elles ne sont pas saines, seules deux sont exclues au maximum. Les deux autres instances (une saine et une non saine) continuent de recevoir des requêtes. Dans le pire des cas, où aucune instance n’est saine, aucune ne sera exclue. Pour remplacer ce comportement, définissez une valeur comprise entre `0` et `100`. Plus la valeur est élevée, plus le nombre d’instances non saines supprimées est élevé. La valeur par défaut est `50` (50 %). |

## <a name="push-notifications"></a>Notifications push

Les variables d’environnement suivantes sont liées à la fonctionnalité [Notification Push](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub).

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_PUSH_ENABLED` | Lecture seule. Ajouté lorsque les notifications Push sont activées. |
| `WEBSITE_PUSH_TAG_WHITELIST` | Lecture seule. Contient les balises dans l’inscription de notification. |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | Lecture seule. Contient une liste de balises dans l’inscription de notification qui requiert une authentification de l’utilisateur. |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | Lecture seule. Contient une liste de balises dans l’inscription de notification qui ont été ajoutées automatiquement. | 

>[!NOTE]
> Cet article contient des références au terme *liste verte*, un terme que Microsoft n’utilise plus. Lorsque le terme sera supprimé du logiciel, nous le supprimerons de cet article.

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>WebJobs

Les variables d’environnement suivantes sont liées à [WebJobs](webjobs-create.md).

| Nom du paramètre| Description |
|-|-|
| `WEBJOBS_RESTART_TIME`|Pour les travaux continus, délai en secondes lorsque le processus d’un travail s’arrête pour une raison quelconque avant de le relancer. |
| `WEBJOBS_IDLE_TIMEOUT`| Pour les travaux déclenchés, délai d’expiration en secondes, au terme duquel le travail est abandonné s’il est inactif, n’a pas de temps processeur ni de sortie. |
| `WEBJOBS_HISTORY_SIZE`| Pour les travaux déclenchés, nombre maximal d’exécutions conservées dans le répertoire d’historique par travail. Par défaut, il s’agit de `50`. |
| `WEBJOBS_STOPPED`| Définissez la valeur `1` pour désactiver l’exécution d’un travail et arrêter tous les travaux en cours d’exécution. |
| `WEBJOBS_DISABLE_SCHEDULE`| Définissez la valeur `1` pour désactiver tous les déclenchements planifiés. Les travaux peuvent toujours être appelés manuellement. |
| `WEBJOBS_ROOT_PATH`| Chemin d’accès absolu ou relatif des fichiers de travail web. Pour un chemin d’accès relatif, la valeur est associée au chemin d’accès racine par défaut (`D:/home/site/wwwroot/` ou `/home/site/wwwroot/`). |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| Définissez la valeur « true »pour envoyer la sortie des travaux web déclenchés au pipeline des journaux d’application (qui prend en charge le système de fichiers, les blobs et les tables). |
| `WEBJOBS_SHUTDOWN_FILE` | Fichier créé par App Service lorsqu’une demande d’arrêt est détectée. Il incombe au processus de travail web de détecter la présence de ce fichier et de lancer l’arrêt. Lorsque vous utilisez le Kit de développement logiciel (SDK) WebJobs, cette partie est gérée automatiquement. |
| `WEBJOBS_PATH` | Lecture seule. Chemin d’accès racine du travail en cours d’exécution (se trouve dans un répertoire temporaire). |
| `WEBJOBS_NAME` | Lecture seule. Nom du travail en cours. |
| `WEBJOBS_TYPE` | Lecture seule. Type de travail en cours (`triggered` ou `continuous`). |
| `WEBJOBS_DATA_PATH` | Lecture seule. Chemin d’accès aux métadonnées du travail en cours, contenant les journaux, l’historique et tout autre artefact du travail. |
| `WEBJOBS_RUN_ID` | Lecture seule. Pour les travaux déclenchés, ID d’exécution actuel du travail. |

## <a name="functions"></a>Fonctions

| Nom du paramètre | Description |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | Définissez la valeur `0` pour désactiver le cache des fonctions. |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Informations de référence sur les paramètres d’application d’Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | Définissez la valeur `0` pour désactiver l’optimisation des fonctions d’espace réservé sur le plan de consommation. L’espace réservé est une optimisation qui [améliore le démarrage à froid](../azure-functions/functions-scale.md#cold-start-behavior). |
| `WEBSITE_PLACEHOLDER_MODE` | Lecture seule. Indique si l’application de fonction s’exécute sur un hôte d’espace réservé (`generalized`) ou sur son propre hôte (`specialized`). |
| `WEBSITE_DISABLE_ZIP_CACHE` | Lorsque votre application s’exécute à partir d’un [package ZIP](deploy-run-package.md) (`WEBSITE_RUN_FROM_PACKAGE=1`), les cinq derniers packages ZIP déployés sont mis en cache dans le système de fichiers de l’application (D:\home\data\SitePackages). Définissez cette variable sur `1` pour désactiver ce cache. Pour les applications de consommation Linux, le cache des packages ZIP est désactivé par défaut. |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->