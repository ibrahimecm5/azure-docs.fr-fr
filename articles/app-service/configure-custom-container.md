---
title: Configurer un conteneur personnalisé
description: Découvrez comment configurer un conteneur personnalisé dans Azure App Service. Cet article présente les tâches de configuration les plus courantes.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: bfcd178a43e7a21ea6ef35d4462956bd49037e6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006864"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Configurer un conteneur personnalisé pour Azure App Service

Cet article montre comment configurer un conteneur personnalisé à exécuter sur Azure App Service.

::: zone pivot="container-windows"

Ce guide fournit les principaux concepts et instructions pour la conteneurisation d’applications Windows dans App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [démarrage rapide](quickstart-custom-container.md) et le [tutoriel](tutorial-custom-container.md) sur le conteneur personnalisé.

::: zone-end

::: zone pivot="container-linux"

Ce guide fournit les principaux concepts et des instructions pour la mise en conteneur d’applications Linux dans App Service. Si vous n’avez jamais utilisé Azure App Service, commencez par suivre le [démarrage rapide](quickstart-custom-container.md) et le [tutoriel](tutorial-custom-container.md) sur le conteneur personnalisé. Il existe également un [démarrage rapide](quickstart-multi-container.md) et un [tutoriel](tutorial-multi-container-app.md) pour les applications à conteneurs multiples.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Images parentes prises en charge

Pour votre image Windows personnalisée, vous devez choisir l’[image parente (image de base)](https://docs.docker.com/develop/develop-images/baseimages/) appropriée pour l’infrastructure souhaitée :

- Pour déployer des applications .NET Framework, utilisez une image parente basée sur la version Windows Server 2019 Core [Long-Term Servicing Channel (LTSC)](/windows-server/get-started/servicing-channels-comparison#long-term-servicing-channel-ltsc). 
- Pour déployer des applications .NET Core, utilisez une image parente basée sur la version Windows Server 2019 Nano [Semi-Annual Servicing Channel (SAC)](/windows-server/get-started/servicing-channels-comparison#semi-annual-channel). 

Le téléchargement d’une image parente lors du démarrage de l’application peut prendre un certain temps. Toutefois, vous pouvez réduire le temps de démarrage en utilisant l’une des images parentes suivantes déjà mises en cache dans Azure App Service :

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):20H2
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-20H2
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/runtime](https://hub.docker.com/_/microsoft-dotnet-runtime/):5.0-nanoserver-20H2
- [mcr.microsoft.com/dotnet/runtime](https://hub.docker.com/_/microsoft-dotnet-runtime/):5.0-nanoserver-1809
- [mcr.microsoft.com/dotnet/aspnet](https://hub.docker.com/_/microsoft-dotnet-aspnet/):5.0-nanoserver-20H2
- [mcr.microsoft.com/dotnet/aspnet](https://hub.docker.com/_/microsoft-dotnet-aspnet/):5.0-nanoserver-1809
- [mcr.microsoft.com/dotnet/runtime](https://hub.docker.com/_/microsoft-dotnet-runtime/):3.1-nanoserver-20H2
- [mcr.microsoft.com/dotnet/runtime](https://hub.docker.com/_/microsoft-dotnet-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/aspnet](https://hub.docker.com/_/microsoft-dotnet-aspnet/):3.1-nanoserver-20H2
- [mcr.microsoft.com/dotnet/aspnet](https://hub.docker.com/_/microsoft-dotnet-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Modifier l’image Docker d’un conteneur personnalisé

Pour modifier une application de conteneur personnalisée existante de l’image Docker actuelle par une nouvelle image, utilisez la commande suivante :

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Utiliser une image à partir d’un registre privé

Pour utiliser une image à partir d’un registre privé, comme Azure Container Registry, exécutez la commande suivante :

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Pour *\<username>* et *\<password>* , fournissez les informations d’identification de connexion pour votre compte de registre privé.

## <a name="use-managed-identity-to-pull-image-from-azure-container-registry"></a>Utilisation d’une identité managée pour extraire une image d’Azure Container Registry

Suivez la procédure suivante pour configurer votre application web pour une extraction à partir d’ACR à l’aide d’une identité managée. La procédure utilise une identité managée affectée par le système, mais vous pouvez également utiliser une identité managée affectée par l’utilisateur.

1. Activez l’[identité managée affectée par le système](./overview-managed-identity.md) pour l’application web à l’aide de la commande [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign) :

    ```azurecli-interactive
    az webapp identity assign --resource-group <group-name> --name <app-name> --query principalId --output tsv
    ```
    Remplacez `<app-name>` par le nom que vous avez utilisé à l’étape précédente. La sortie de la commande (filtrée par les arguments --query et --output) est l’ID du principal de service de l’identité affectée, que vous utiliserez sous peu.
1. Récupérez l’ID de ressource de votre Azure Container Registry :
    ```azurecli-interactive
    az acr show --resource-group <group-name> --name <registry-name> --query id --output tsv
    ```
    Remplacez `<registry-name>` par le nom de votre registre. La sortie de la commande (filtrée par les arguments --query et --output) est l’ID de ressource d’Azure Container Registry.
1. Accordez à l’identité managée l’autorisation d’accéder au registre de conteneurs :

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope <registry-resource-id> --role "AcrPull"
    ```

    Remplacez les valeurs suivantes :
    - `<principal-id>` par l’ID de principal du service récupéré à partir de la commande `az webapp identity assign`
    - `<registry-resource-id>` avec l’ID de votre registre de conteneurs à partir de la commande `az acr show`

    Pour plus d’informations sur ces autorisations, consultez [Présentation du contrôle d’accès en fonction du rôle Azure](../role-based-access-control/overview.md).

1. Configurez votre application pour qu’elle utilise l’identité managée pour effectuer une extraction à partir d’Azure Container Registry.

    ```azurecli-interactive
    az webapp config set --resource-group <group-name> --name <app-name> --generic-configurations '{"acrUseManagedIdentityCreds": true}'
    ```

    Remplacez les valeurs suivantes :
    - `<app-name>` avec le nom de votre application web.
    >[!Tip]
    > Si vous utilisez la console PowerShell pour exécuter les commandes, vous devrez placer les chaînes dans une séquence d’échappement dans l'argument `--generic-configurations` dans cette étape et la suivante. Par exemple : `--generic-configurations '{\"acrUseManagedIdentityCreds\": true'`
1. (Facultatif) Si votre application utilise une [identité managée affectée par l’utilisateur](overview-managed-identity.md#add-a-user-assigned-identity), assurez-vous qu’elle est configurée sur l’application web, puis définissez une propriété `acrUserManagedIdentityID` supplémentaire pour spécifier son ID client :
    
    ```azurecli-interactive
    az identity show --resource-group <group-name> --name <identity-name> --query clientId --output tsv
    ```
    Remplacez l’`<identity-name>` de votre identité managée affectée par l’utilisateur et utilisez la sortie `<client-id>` pour configurer l’ID de l’identité managée affectée par l’utilisateur.

    ```azurecli-interactive
    az  webapp config set --resource-group <group-name> --name <app-name> --generic-configurations '{"acrUserManagedIdentityID": "<client-id>"}'
    ```

Vous êtes prêt, et l’application web utilise désormais l’identité managée pour effectuer une extraction à partir d’Azure Container Registry. 

::: zone pivot="container-linux"

## <a name="use-an-image-from-a-network-protected-registry"></a>Utiliser une image à partir d’un registre protégé par un réseau

Pour vous connecter et effectuer une extraction à partir d’un registre à l’intérieur d’un réseau virtuel ou d’un réseau local, votre application doit être connectée à un réseau virtuel à l’aide de la fonctionnalité d’intégration au réseau virtuel. Cela est également nécessaire pour Azure Container Registry avec un point de terminaison privé. Une fois votre réseau et la résolution DNS configurés, vous activez le routage de l’extraction de l’image à travers le réseau virtuel en définissant le paramètre d’application `WEBISTE_PULL_IMAGE_OVER_VNET=true` :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBISTE_PULL_IMAGE_OVER_VNET=true
```

::: zone-end

## <a name="i-dont-see-the-updated-container"></a>Je ne vois pas le conteneur mis à jour

Si vous modifiez vos paramètres de conteneur Docker pour qu’ils pointent vers un nouveau conteneur, l’application peut prendre quelques minutes avant de servir les requêtes HTTP du nouveau conteneur. Pendant l’extraction et le démarrage du nouveau conteneur, App Service continue de servir les requêtes de l’ancien conteneur. Ce n’est qu’une fois le nouveau conteneur démarré et prêt à recevoir des requêtes qu’App Service commence à lui envoyer des requêtes.

## <a name="how-container-images-are-stored"></a>Stockage d’images de conteneur

La première fois que vous exécutez une image Docker personnalisée dans App Service, App Service effectue une opération `docker pull` et extrait toutes les couches d’image. Ces couches sont stockées sur disque, comme si vous utilisiez Docker localement. À chaque redémarrage de l’application, App Service effectue une opération `docker pull`, mais extrait uniquement les couches qui ont changé. Si aucune modification n’a été apportée, App Service utilise les couches existantes sur le disque local.

Si l’application modifie des instances de calcul pour une raison quelconque, telle que la mise à l’échelle des niveaux tarifaires, App Service doit de nouveau extraire toutes les couches. Il en va de même si vous effectuez un scale-out pour ajouter des instances. Il existe également de rares cas où les instances d’application peuvent changer sans opération de mise à l’échelle.

## <a name="configure-port-number"></a>Configurer le numéro de port

Par défaut, App Service suppose que votre conteneur personnalisé écoute sur le port 80. Si votre conteneur écoute un port différent, définissez le paramètre d’application `WEBSITES_PORT` dans votre application App Service. Vous pouvez le définir via le [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service autorise actuellement votre conteneur à exposer un seul port pour les requêtes HTTP. 

## <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Votre conteneur personnalisé peut utiliser des variables d’environnement qui doivent être fournies en externe. Vous pouvez les transmettre via le [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Lorsque votre application s’exécute, les paramètres d’application App Service sont automatiquement injectés dans le processus en tant que variables d’environnement. Vous pouvez vérifier les variables d’environnement de conteneur avec l’URL `https://<app-name>.scm.azurewebsites.net/Env)`.

Si votre application utilise des images à partir d’un registre privé ou d’un Docker Hub, les informations d’identification permettant d’accéder au référentiel sont enregistrées dans les variables d’environnement : `DOCKER_REGISTRY_SERVER_URL`, `DOCKER_REGISTRY_SERVER_USERNAME` et `DOCKER_REGISTRY_SERVER_PASSWORD`. En raison des risques de sécurité, aucun de ces noms de variables réservés n’est exposé à l’application.

::: zone pivot="container-windows"
Pour des conteneurs basés sur IIS ou .NET Framework (version 4.0 ou ultérieure), ils sont automatiquement injectés par App Service dans `System.ConfigurationManager` en tant que paramètres d’application .NET et chaînes de connexion. Pour toute autre langage ou infrastructure, ils sont fournis en tant que variables d’environnement pour le processus, avec l’un des préfixes correspondants suivants :

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Cette méthode fonctionne à la fois pour les applications à conteneur unique et à plusieurs conteneurs, où les variables d’environnement sont spécifiées dans le fichier *docker-compose.yml*.

::: zone-end

## <a name="use-persistent-shared-storage"></a>Utiliser le stockage partagé persistant

::: zone pivot="container-windows"

Vous pouvez utiliser le répertoire *C:\home* dans le système de fichiers de votre application pour conserver les fichiers au fil des redémarrages, et les partager entre des instances. Le répertoire `C:\home` dans votre application est fourni pour permettre à votre application de conteneur d’accéder au stockage persistant.

Lorsque le stockage persistant est désactivé, les écritures dans le répertoire `C:\home` ne sont pas conservées. Les [journaux d’hôte de conteneur Docker](#access-diagnostic-logs) sont enregistrés dans un stockage partagé persistant par défaut, qui n’est pas attaché au conteneur. Lorsque le stockage persistant est activé, toutes les écritures dans le répertoire `C:\home` sont conservées et accessibles par toutes les instances d’une application ayant fait l’objet d’une montée en charge parallèle, et les journaux sont accessibles dans `C:\home\LogFiles`.

::: zone-end

::: zone pivot="container-linux"

Vous pouvez utiliser le répertoire */home* dans le système de fichiers de votre application pour rendre persistants les fichiers entre les redémarrages et les partager entre plusieurs instances. Le répertoire `/home` dans votre application est fourni pour permettre à votre application de conteneur d’accéder au stockage persistant.

Lorsque le stockage persistant est désactivé, les écritures dans le répertoire `/home` ne sont pas rendues persistantes entre les redémarrages d’applications ou entre plusieurs instances. La seule exception concerne le répertoire `/home/LogFiles`, qui est utilisé pour stocker les journaux Docker et de conteneurs. Lorsque le stockage persistant est activé, toutes les écritures dans le répertoire `/home` sont rendues persistantes et sont accessibles par toutes les instances d’une application avec scale-out.

::: zone-end

Par défaut, le stockage persistant est désactivé, et le paramètre n’est pas exposé dans les paramètres d’application. Pour l’activer, définissez le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE` via le [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Vous pouvez également [configurer votre propre stockage persistant](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Détecter une session HTTPS

App Service termine les requêtes TLS/SSL au niveau des serveurs frontaux. Cela signifie que les requêtes TLS/SSL n’atteignent jamais votre application. Vous n’avez pas besoin et devez vous abstenir d’implémenter la prise en charge des protocoles TLS/SSL dans votre application. 

Les serveurs frontaux se trouvent dans des centres de données Azure. Si vous utilisez les protocoles TLS/SSL avec votre application, votre trafic sur Internet sera toujours chiffré en toute sécurité.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Personnaliser l’injection de clé machine ASP.NET

 Pendant le démarrage du conteneur, des clés générées automatiquement sont injectées dans le conteneur en tant que clés machine pour les routines de chiffrement ASP.NET. Vous pouvez [rechercher ces clés dans votre conteneur](#connect-to-the-container) en recherchant les variables d’environnement suivantes : `MACHINEKEY_Decryption`, `MACHINEKEY_DecryptionKey`, `MACHINEKEY_ValidationKey`, `MACHINEKEY_Validation`. 

Les nouvelles clés générées à chaque redémarrage peuvent réinitialiser l’authentification par formulaires ASP.NET et l’état d’affichage, si votre application en dépend. Pour empêcher la régénération automatique des clés, [définissez-les manuellement en tant que paramètres d’application App Service](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Connexion au conteneur

Vous pouvez vous connecter directement à votre conteneur Windows pour les tâches de diagnostic en accédant à `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Fonctionnement de l’opération :

- La console de débogage vous permet d’exécuter des commandes interactives, telles que le démarrage de sessions PowerShell, l’inspection de clés de Registre et la navigation dans l’ensemble du système de fichiers du conteneur.
- Elle fonctionne séparément du navigateur graphique, au-dessus de celui-ci, et n’affiche que les fichiers de votre [stockage partagé](#use-persistent-shared-storage).
- Dans une application ayant fait l’objet d’une montée en charge parallèle, la console de débogage est connectée à l’une des instances de conteneur. Vous pouvez sélectionner une autre instance dans la liste déroulante **Instance** dans le menu supérieur.
- Les modifications apportées au conteneur à partir de la console ne sont *pas* conservée après un redémarrage de votre application (à l’exception des modifications apportées dans le stockage partagé), car elles ne font pas partie de l’image Docker. Pour conserver vos modifications, telles que des paramètres du Registre et l’installation de logiciels, intégrez-les dans le fichier Dockerfile.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

App Service journalise les actions de l’hôte Docker, ainsi que les activités en provenance du conteneur. Les journaux de l’hôte Docker (journaux de plateforme) sont livrés par défaut, mais les journaux d’application ou de serveur web en provenance du conteneur doivent être activés manuellement. Pour plus d’informations, consultez [Activer la journalisation des applications](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) et [Activer la journalisation du serveur web](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Il existe plusieurs moyens d’accéder aux journaux du Docker :

- [Dans le portail Azure](#in-azure-portal)
- [À partir de la console Kudu](#from-the-kudu-console)
- [Avec l’API Kudu](#with-the-kudu-api)
- [Envoyer des journaux à Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Dans le portail Azure

Les journaux du Docker sont affichés sur le portail, dans la page **Paramètres du conteneur** de votre application. Les journaux sont tronqués, mais vous pouvez les télécharger tous en cliquant sur **Télécharger**. 

### <a name="from-the-kudu-console"></a>À partir de la console Kudu

Accédez à `https://<app-name>.scm.azurewebsites.net/DebugConsole`, puis cliquez sur le dossier **LogFiles** pour afficher les fichiers journaux individuels. Pour télécharger l’intégralité du répertoire **LogFiles**, cliquez sur l’icône **Télécharger** à gauche du nom du répertoire. Vous pouvez également accéder à ce dossier à l’aide d’un client FTP.

Dans le terminal de la console, vous ne pouvez pas accéder au dossier `C:\home\LogFiles` par défaut, car le stockage partagé persistant n’est pas activé. Pour activer ce comportement dans le terminal de la console, vous devez [activer le stockage partagé persistant](#use-persistent-shared-storage).

Si vous tentez de télécharger le journal de Docker actuellement utilisé à l’aide d’un client FTP, vous risquez d’obtenir une erreur en raison d’un verrou de fichier.

### <a name="with-the-kudu-api"></a>Avec l’API Kudu

Accédez directement à `https://<app-name>.scm.azurewebsites.net/api/logs/docker` pour afficher les métadonnées des journaux de Docker. Il se peut que plusieurs fichiers journaux soient répertoriés. La propriété `href` vous permet de télécharger le fichier journal directement. 

Pour télécharger tous les journaux dans un fichier ZIP, accédez à `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`.

## <a name="customize-container-memory"></a>Personnaliser la mémoire du conteneur

Par défaut, tous les conteneurs Windows déployés dans Azure App Service sont limités à 1 Go de RAM. Vous pouvez modifier cette valeur en fournissant le paramètre d’application `WEBSITE_MEMORY_LIMIT_MB` à l’aide du [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

La valeur est définie en Mo et doit être inférieure ou égale à la mémoire physique totale de l’hôte. Par exemple, dans un plan de App Service avec 8 Go de RAM, le total cumulé de `WEBSITE_MEMORY_LIMIT_MB` pour toutes les applications ne doit pas dépasser 8 Go. Pour plus d’informations sur la quantité de mémoire disponible pour chaque niveau tarifaire, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/), dans la section **Plan de service Premium v3**.

## <a name="customize-the-number-of-compute-cores"></a>Personnaliser le nombre de cœurs de calcul

Par défaut, un conteneur Windows s’exécute avec tous les cœurs disponibles pour le niveau tarifaire choisi. Vous pouvez par exemple réduire le nombre de cœurs qu’utilise votre emplacement de préproduction. Pour réduire le nombre de cœurs qu’utilise un conteneur, définissez le paramètre d’application `WEBSITE_CPU_CORES_LIMIT` sur le nombre préféré de cœurs. Vous pouvez le définir via le [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> La mise à jour du paramètre d’application déclenche un redémarrage automatique qui entraîne un temps d’arrêt minimal. Pour une application de production, envisagez de la permuter dans un emplacement de préproduction, modifiez le paramètre d’application dans l’emplacement de préproduction, puis re-permutez-la en production.

Vérifiez votre nombre ajusté en accédant à la console Kudu (`https://<app-name>.scm.azurewebsites.net`) et en tapant les commandes suivantes à l’aide de PowerShell. Chaque commande génère un nombre.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Les processeurs peuvent être de type multicœur ou hyperthreading. Pour plus d’informations sur le nombre de cœurs disponibles pour chaque niveau tarifaire, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/), dans la section **Plan de service Premium v3**.

## <a name="customize-health-ping-behavior"></a>Personnaliser le comportement de test Ping d’intégrité

App Service considère que le démarrage d’un conteneur est correct quand le conteneur démarre et répond à un test Ping HTTP. La requête Ping d’intégrité contient l’en-tête `User-Agent= "App Service Hyper-V Container Availability Check"`. Si le conteneur démarre mais ne répond pas à un test Ping après un certain temps, App Service journalise un événement dans le journal de Docker, indiquant que le conteneur n’a pas démarré. 

Si votre application est gourmande en ressources, il se peut que le conteneur ne réponde à temps pas à la requête Ping HTTP. Pour contrôler les actions en cas d’échec des tests Ping HTTP, définissez le paramètre d’application `CONTAINER_AVAILABILITY_CHECK_MODE`. Vous pouvez le définir via le [Cloud Shell](https://shell.azure.com). Dans Bash :

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

Dans PowerShell :

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

Le tableau suivant présente les valeurs possibles :

| Valeur | Descriptions |
| - | - |
| **Repair** | Redémarrer le conteneur après trois vérifications de disponibilité consécutives |
| **ReportOnly** | Valeur par défaut. Ne pas redémarrer le conteneur mais signaler dans les journaux de Docker du conteneur après trois vérifications de disponibilité consécutives. |
| **Désactivé** | Ne pas vérifier la disponibilité. |

## <a name="support-for-group-managed-service-accounts"></a>Prise en charge des comptes de service géré par un groupe

Les comptes de service géré de groupe (GMSA) ne sont actuellement pas pris en charge dans les conteneurs Windows dans App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Activation de SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour qu’un conteneur personnalisé prenne en charge SSH, vous devez l’ajouter à votre image Docker même.

> [!TIP]
> Tous les conteneurs Linux intégrés dans App Service ont ajouté les instructions SSH dans leurs dépôts d’images. Vous pouvez parcourir les instructions suivantes avec le [dépôt Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) afin de déterminer comment il y est activé. La configuration dans l’image Node.js intégrée est légèrement différente, mais suit les mêmes principes.

- Ajoutez [un fichier sshd_config](https://man.openbsd.org/sshd_config) à votre référentiel, comme dans l’exemple suivant.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Ce fichier configure OpenSSH et doit inclure les éléments suivants :
    > - `Port` être défini sur 2222.
    > - `Ciphers` doit inclure au moins un élément dans cette liste : `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` doit inclure au moins un élément dans cette liste : `hmac-sha1,hmac-sha1-96`.

- Ajoutez un fichier de script ssh_setup pour créer les clés SSH [en utilisant ssh-keygen](https://man.openbsd.org/ssh-keygen.1) sur votre référentiel.

    ```
    #!/bin/sh

    if [ ! -f "/etc/ssh/ssh_host_rsa_key" ]; then
        # generate fresh rsa key
        ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa
    fi

    if [ ! -f "/etc/ssh/ssh_host_dsa_key" ]; then
        # generate fresh dsa key
        ssh-keygen -f /etc/ssh/ssh_host_dsa_key -N '' -t dsa
    fi

    if [ ! -f "/etc/ssh/ssh_host_ecdsa_key" ]; then
        # generate fresh ecdsa key
        ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key -N '' -t dsa
    fi

    if [ ! -f "/etc/ssh/ssh_host_ed25519_key" ]; then
        # generate fresh ecdsa key
        ssh-keygen -f /etc/ssh/ssh_host_ed25519_key -N '' -t dsa
    fi

    #prepare run dir
        if [ ! -d "/var/run/sshd" ]; then
        mkdir -p /var/run/sshd
    fi
    ```

- Dans votre fichier Dockerfile, ajoutez les commandes suivantes :

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Copy and configure the ssh_setup file
    RUN mkdir -p /tmp
    COPY ssh_setup.sh /tmp
    RUN chmod +x /tmp/ssh_setup.sh \
        && (sleep 1;/tmp/ssh_setup.sh 2>&1 > /dev/null)

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    > [!NOTE] 
    > Le mot de passe racine doit être exactement `Docker!` car il est utilisé par App Service pour vous permettre d’accéder à la session SSH avec le conteneur. Cette configuration n’autorise pas les connexions externes avec le conteneur. Le port 2222 du conteneur est accessible uniquement au sein du réseau de pont d’un réseau virtuel privé et n’est pas accessible à un attaquant sur Internet.

- Dans le script de démarrage pour votre conteneur, démarrez le serveur SSH.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurer des applications à plusieurs conteneurs

- [Utiliser le stockage persistant dans Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitations de la version préliminaire](#preview-limitations)
- [Options Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Utiliser le stockage persistant dans Docker Compose

Les applications multiconteneurs telles que WordPress nécessitent un stockage persistant pour fonctionner correctement. Pour l’activer, votre configuration Docker Compose doit pointer vers un emplacement de stockage *à l’extérieur* de votre conteneur. Les emplacements de stockage à l’intérieur de votre conteneur ne rendent pas les modifications persistantes au-delà du redémarrage de l’application.

Activez le stockage persistant en définissant le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE`, à l’aide de la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) dans le [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Dans votre fichier *docker-compose.yml*, mappez l’option `volumes` sur `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` est une variable d’environnement d’App Service mappée sur le stockage persistant de votre application. Par exemple :

```yaml
wordpress:
  image: <image name:tag>
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitations de la version préliminaire

La fonctionnalité multiconteneurs est actuellement en préversion. Les fonctionnalités suivantes de la plateforme App Service ne sont pas prises en charge :

- Authentification / Autorisation
- Identités managées
- CORS
- L’intégration au réseau virtuel n’est pas prise en charge pour les scénarios Docker Compose.
- Docker Compose sur Azure App Services a actuellement une limite de 4000 caractères.

### <a name="docker-compose-options"></a>Options Docker Compose

Les listes suivantes présentent des options de configuration Docker Compose prises en charge et non prises en charge :

#### <a name="supported-options"></a>Options prises en charge

- command
- entrypoint
- Environnement
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Options non prises en charge

- build (non autorisée)
- [depends_on](faq-app-service-linux.yml#how-do-i-use-depends-on-) (ignorée)
- networks (ignorée)
- secrets (ignorée)
- ports autres que 80 et 8080 (ignorées)

> [!NOTE]
> Les autres options qui ne sont pas explicitement appelées sont ignorées dans la préversion publique.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Migrer des logiciels personnalisés vers Azure App Service à l’aide d’un conteneur personnalisé](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Tutoriel : Application WordPress avec plusieurs conteneurs](tutorial-multi-container-app.md)

::: zone-end

Ou bien, consultez les ressources supplémentaires :

- [Informations de référence sur les variables d’environnement et les paramètres d’application](reference-app-settings.md)
- [Charger le certificat dans les conteneurs Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
