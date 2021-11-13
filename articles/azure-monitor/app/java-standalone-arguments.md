---
title: Ajout de l’argument JVM – Azure Monitor Application Insights pour Java
description: Comment ajouter l’argument JVM qui active Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 88721d4545625d11614075db77ee61df46cbe9ef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470373"
---
# <a name="tips-for-updating-your-jvm-args---azure-monitor-application-insights-for-java"></a>Astuces pour la mise à jour de vos arguments JVM - Azure Monitor Application Insights pour Java

## <a name="azure-environments"></a>Environnements Azure

Configurez [App Services](../../app-service/configure-language-java.md#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Ajoutez l’argument JVM `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à l'emplacement de votre choix avant `-jar`, par exemple :

```
java -javaagent:path/to/applicationinsights-agent-3.2.2.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring Boot via le point d’entrée Docker

Si vous utilisez le formulaire *exec*, ajoutez le paramètre `"-javaagent:path/to/applicationinsights-agent-3.2.2.jar"` à la liste de paramètres quelque part avant le paramètre `"-jar"`, par exemple :

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.2.2.jar", "-jar", "<myapp.jar>"]
```

Si vous utilisez le formulaire *shell*, ajoutez l’argument JVM `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` quelque part avant `-jar`, par exemple :

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.2.2.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installé via `apt-get` ou `yum`

Si vous avez installé Tomcat via `apt-get` ou `yum`, vous devez disposer d’un fichier `/etc/tomcat8/tomcat8.conf`.  Ajoutez cette ligne à la fin de ce fichier :

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installé via le téléchargement et la décompression

Si vous avez installé Tomcat via le téléchargement et la décompression depuis [https://tomcat.apache.org](https://tomcat.apache.org), vous devez disposer d’un fichier `<tomcat>/bin/catalina.sh`.  Créez un fichier dans le même répertoire nommé `<tomcat>/bin/setenv.sh` avec le contenu suivant :

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

Si le fichier `<tomcat>/bin/setenv.sh` existe déjà, modifiez-le et ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à `CATALINA_OPTS`.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Exécution de Tomcat à partir de la ligne de commande

Localisez le fichier `<tomcat>/bin/catalina.bat`.  Créez un fichier dans le même répertoire nommé `<tomcat>/bin/setenv.bat` avec le contenu suivant :

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.2.2.jar
```

Les guillemets ne sont pas nécessaires, mais si vous souhaitez les inclure, le positionnement qui convient est le suivant :

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

Si le fichier `<tomcat>/bin/setenv.bat` existe déjà, modifiez-le et ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à `CATALINA_OPTS`.

### <a name="running-tomcat-as-a-windows-service"></a>Exécution de Tomcat en tant que service Windows

Localisez le fichier `<tomcat>/bin/tomcat8w.exe`.  Exécutez ce fichier exécutable et ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à `Java Options` sous l’onglet `Java`.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Serveur autonome

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à la variable d’environnement `JAVA_OPTS` existante dans le fichier `JBOSS_HOME/bin/standalone.conf` (Linux) ou `JBOSS_HOME/bin/standalone.conf.bat` (Windows) :

```java    ...
    JAVA_OPTS="-javaagent:path/to/applicationinsights-agent-3.2.2.jar -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Serveur de domaine

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à `jvm-options` dans `JBOSS_HOME/domain/configuration/host.xml` :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.2.2.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Si vous exécutez plusieurs serveurs gérés sur un hôte unique, vous devez ajouter `applicationinsights.agent.id` `system-properties` pour chaque `server` :

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

La valeur `applicationinsights.agent.id` spécifiée doit être unique. Elle est utilisée pour créer un sous-répertoire dans le répertoire applicationinsights, car chaque processus JVM requiert sa propre configuration applicationinsights locale et son propre fichier journal applicationinsights local. En outre, si les rapports sont destinés au collecteur central, le fichier `applicationinsights.properties` est partagé par plusieurs serveurs gérés et dès lors, `applicationinsights.agent.id` est nécessaire pour remplacer le paramètre `agent.id` dans ce fichier partagé. La valeur `applicationinsights.agent.rollup.id` peut être spécifiée de manière similaire dans l'élément `system-properties` du serveur s'il vous faut annuler le paramètre `agent.rollup.id` par serveur géré.


## <a name="jetty-9"></a>Jetty 9

Ajoutez ces lignes à `start.ini`.

```
--exec
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```


## <a name="payara-5"></a>Payara 5

Ajoutez `-javaagent:path/to/applicationinsights-agent-3.2.2.jar` à `jvm-options` dans `glassfish/domains/domain1/config/domain.xml` :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.2.2.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Ouvrez la console de gestion, accédez à **Serveurs > Serveurs d’applications WebSphere > Serveurs d’applications**, sélectionnez les serveurs d’applications qui conviennent, puis cliquez sur : 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Dans « Arguments Java génériques », ajoutez ce qui suit :
```
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```
Ensuite, enregistrez et redémarrez le serveur d’applications.


## <a name="openliberty-18"></a>OpenLiberty 18

Créez un fichier `jvm.options` dans le répertoire du serveur (par exemple `<openliberty>/usr/servers/defaultServer`), puis ajoutez la ligne suivante :
```
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```

## <a name="others"></a>Autres

Pour plus d’informations sur l’ajout d’arguments JVM, consultez la documentation de votre serveur d’applications.
