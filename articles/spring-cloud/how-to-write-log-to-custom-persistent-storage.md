---
title: Guide pratique pour utiliser Logback afin d’écrire des journaux dans un stockage persistant personnalisé dans Azure Spring Cloud | Microsoft Docs
description: Guide pratique pour utiliser Logback afin d’écrire des journaux dans un stockage persistant personnalisé dans Azure Spring Cloud.
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: 558ca29f8faa03857d4debe760c4859ab43b17e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404354"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Guide pratique pour utiliser Logback afin d’écrire des journaux dans un stockage persistant personnalisé

**Cet article s’applique à :** ✔️ Java

Cet article explique comment charger Logback et écrire des journaux dans un stockage persistant personnalisé dans Azure Spring Cloud.

> [!NOTE]
> Quand un fichier dans le classpath de l’application a l’un des noms suivants, Spring Boot le charge automatiquement sur la configuration par défaut pour Logback :
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-spring.groovy*
> - *logback.groovy*

## <a name="prerequisites"></a>Prérequis

* Ressource de stockage existante liée à une instance Azure Spring Cloud. Si vous devez lier une ressource de stockage, consultez [Guide pratique pour activer votre propre stockage permanent dans Azure Spring Cloud](./how-to-custom-persistent-storage.md).
* Dépendance Logback incluse dans votre application. Pour plus d’informations sur Logback, consultez le [Guide Logback](https://www.baeldung.com/logback).
* [Extension Azure Spring Cloud](/cli/azure/azure-cli-extensions-overview) pour l’interface de ligne de commande Azure (Azure CLI)

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>Modifier la configuration Logback pour écrire des journaux dans un chemin spécifique

Vous pouvez définir le chemin où les journaux doivent être écrits à l’aide de l’exemple de fichier logback-spring.xml.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <!-- please feel free to customize the log layout -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 'LOGS' here is a value to be read from the application's environment variable -->
        <file>${LOGS}/spring-boot-logger.log</file>
        <!-- please feel free to customize the log layout pattern -->
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at the INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at the TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>
```

Dans l’exemple précédent, il existe deux espaces réservés nommés `{LOGS}` dans le chemin pour l’écriture des journaux de l’application. Une valeur doit être attribuée à la variable d’environnement `LOGS` pour que le journal soit écrit à la fois sur la console et sur votre stockage persistant. 

## <a name="use-the-azure-cli-to-create-and-deploy-logback-to-write-logs-to-persistent-storage"></a>Utiliser l’interface Azure CLI pour créer et déployer des Logback afin d’écrire des journaux dans un stockage persistant

1. Utilisez la commande suivante pour créer une application dans Azure Spring Cloud avec le stockage persistant activé et la variable d’environnement définie :

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```

    Vous devez monter votre stockage persistant sur le même chemin que celui utilisé dans votre application. Voici un exemple de fichier JSON qui est transmis au paramètre `--persistent-storage` dans la commande create. Veillez à transmettre la même valeur pour la variable d’environnement dans la commande CLI ci-dessus et dans la propriété `mountPath` ci-dessous : 

    ```json
    {
        "customPersistentDisks": [
            {
                "storageName": "<Storage-Resource-Name>",
                "customPersistentDiskProperties": {
                    "type": "AzureFileVolume",
                    "shareName": "<Azure-File-Share-Name>",
                    "mountPath": "/byos/logs",
                    "readOnly": false
                }
            }
        ]
    }
    ```
  
1. Utilisez la commande suivante pour déployer votre application :

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. Utilisez la commande suivante pour vérifier le journal de la console de votre application :

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    Accédez à la ressource de compte Stockage Azure que vous avez liée et recherchez le partage de fichiers Azure qui était attaché en tant que stockage persistant. Dans cet exemple, les journaux sont écrits dans le fichier *spring-boot-logger.log* en suivant le chemin de base de votre partage de fichiers Azure. Tous les fichiers journaux ayant subi une rotation sont stockés dans le dossier */archived* de votre partage de fichiers Azure.

## <a name="next-steps"></a>Étapes suivantes

* [Journal des applications structuré pour Azure Spring Cloud](./structured-app-log.md)
* [Analyser les journaux et les métriques avec les paramètres de diagnostic](./diagnostic-services.md)