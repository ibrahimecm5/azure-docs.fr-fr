---
title: Guide pratique pour préparer une application à un déploiement dans Azure Spring Cloud
description: Apprenez à préparer une application à un déploiement dans Azure Spring Cloud.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/06/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 913d750353053398015da9939ec35f0f9b0de1c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013704"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Préparer une application à un déploiement dans Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud fournit des services robustes pour héberger, surveiller, mettre à l'échelle et mettre à jour une application Steeltoe. Cet article explique comment préparer une application Steeltoe existante à un déploiement dans Azure Spring Cloud.

Cet article présente les dépendances, la configuration et le code requis pour exécuter une application .NET Core Steeltoe dans Azure Spring Cloud. Pour plus d’informations sur le déploiement d’une application sur Azure Spring Cloud, consultez [Déployer votre première application Spring Boot dans Azure Spring Cloud](./quickstart.md).

>[!Note]
> La prise en charge Steeltoe pour Azure Spring Cloud est actuellement disponible en préversion publique. Les offres en préversion publique permettent aux clients de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services en préversion publique ne sont pas destinés à une utilisation en contexte de production.  Pour plus d'informations sur le support offert dans le cadre des préversions, consultez notre [FAQ](https://azure.microsoft.com/support/faq/) ou déposez une [demande de support](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="supported-versions"></a>Versions prises en charge

Azure Spring Cloud prend en charge :

* .NET Core 3.1
* Steeltoe 2.4 et 3.0

## <a name="dependencies"></a>Dépendances

Pour Steeltoe 2.4, ajoutez le dernier package [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) au fichier projet :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Pour Steeltoe 3.0, ajoutez le dernier package [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) au fichier projet :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Mise à jour de Program.cs

Dans la méthode `Program.Main`, appelez la méthode `UseAzureSpringCloudService`.

Pour Steeltoe 2.4.4, appelez `UseAzureSpringCloudService` après `ConfigureWebHostDefaults` et après `AddConfigServer` s’il est appelé :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Pour Steeltoe 3.0.0, appelez `UseAzureSpringCloudService` avant `ConfigureWebHostDefaults` et avant tout code de configuration Steeltoe :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Activer la détection de service Eureka Server

Dans la source de configuration qui sera utilisée lorsque l'application s'exécutera dans Azure Spring Cloud, définissez `spring.application.name` sur le même nom que l'application Azure Spring Cloud dans laquelle le projet sera déployé.

Par exemple, si vous déployez un projet .NET nommé `EurekaDataProvider` dans une application Azure Spring Cloud nommée `planet-weather-provider`, le fichier *appSettings.json* doit inclure le code JSON suivant :

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Utiliser la détection de service

Pour appeler un service à l'aide de la détection de service Eureka Server, envoyez des requêtes HTTP à `http://<app_name>`, sachant que `app_name` correspond à la valeur de la propriété `spring.application.name` de l'application cible. Par exemple, le code suivant appelle le service `planet-weather-provider` :

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```

::: zone-end

::: zone pivot="programming-language-java"
Cette rubrique montre comment préparer une application Java Spring existante à son déploiement dans Azure Spring Cloud. S’il est correctement configuré, Azure Spring Cloud fournit des services robustes pour superviser, mettre à l’échelle et mettre à jour votre application Java Spring Cloud.

Avant d’exécuter cet exemple, vous pouvez essayer le [démarrage rapide de base](./quickstart.md).

D’autres exemples expliquent comment déployer une application sur Azure Spring Cloud quand le fichier POM est configuré.

* [Lancer votre première application](./quickstart.md)
* [Créer et exécuter des microservices](./quickstart-sample-app-introduction.md)

Cet article explique les dépendances nécessaires et comment les ajouter au fichier POM.

## <a name="java-runtime-version"></a>Version du runtime Java

Azure Spring Cloud prend en charge Java 8 et Java 11. En général, Azure PaaS prend uniquement en charge les versions Java LTS, et Azure Spring Cloud prendra en charge Java 17 LTS. L’environnement d’hébergement contient la dernière version d’Azul Zulu OpenJDK pour Azure. Pour plus d’informations sur Azul Zulu OpenJDK pour Azure, consultez [Installer le JDK](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versions de Spring Boot et de Spring Cloud

Pour préparer une application Spring Boot existante pour un déploiement sur Azure Spring Cloud, incluez les dépendances Spring Boot et Spring Cloud dans le fichier POM de l’application comme indiqué dans les sections suivantes.

Azure Spring Cloud prendra en charge la toute dernière version de Spring Boot ou Spring Boot dans un délai d’un mois après sa mise en production. Vous pouvez obtenir des versions de Spring Boot prises en charge à partir des [Versions Spring Boot](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases) et des versions de Spring Cloud à partir de [Versions Spring Cloud](https://github.com/spring-projects/spring-boot/wiki/Supported-Versions#releases). 

Le tableau ci-dessous liste les combinaisons prises en charges de Spring Boot et Spring Cloud :

Version de Spring Boot | Version de Spring Cloud
---|---
2.3.x | Hoxton.SR8+
2.4.x, 2.5.x | 2020,0 alias Ilford +

> [!NOTE]
> - Veuillez mettre à niveau Spring Boot vers 2.5.2 et 2.4.8 pour apporter une réponse au rapport CVE suivant [CVE-2021-22119 : attaque par déni de service avec spring-security-oauth2-client](https://tanzu.vmware.com/security/cve-2021-22119). Si vous utilisez la sécurité Spring, veuillez la mettre à niveau vers 5.5.1, 5.4.7, 5.3.10 ou 5.2.11.
> - Nous avons identifié un problème avec Spring Boot 2.4.0 sur l’authentification TLS entre les applications et le Registre Spring Cloud Service, veuillez utiliser la version 2.4.1 ou une version ultérieure. Référez-vous à la [FAQ](./faq.md?pivots=programming-language-java#development) pour connaitre une solution si vous insistez ou à l’aide de 2.4.0.

### <a name="dependencies-for-spring-boot-version-23"></a>Dépendances pour Spring Boot version 2.3

Pour Spring Boot version 2.3, ajoutez les dépendances suivantes au fichier POM de l’application.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-2425"></a>Dépendances pour Spring Boot version 2.4/2.5

Pour Spring Boot version 2.4/2.5, ajoutez les dépendances suivantes au fichier POM de l’application.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.8</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Ne spécifiez pas `server.port` dans votre configuration. Azure Spring Cloud remplacera ce paramètre par un numéro de port fixe. Respectez également ce paramètre et ne spécifiez pas le port du serveur dans votre code.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Autres dépendances recommandées pour activer les fonctionnalités Azure Spring Cloud

Pour activer les fonctionnalités intégrées d’Azure Spring Cloud à partir du registre de services dans le traçage distribué, vous devez également inclure les dépendances suivantes dans votre application. Vous pouvez supprimer certaines de ces dépendances si vous n’avez pas besoin de fonctionnalités correspondantes pour les applications spécifiques.

### <a name="service-registry"></a>Registre de service

Pour utiliser le service managé Azure Service Registry, ajoutez la dépendance `spring-cloud-starter-netflix-eureka-client` dans le fichier pom.xml, comme indiqué ici :

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Le point de terminaison du serveur Service Registry est injecté automatiquement sous la forme de variables d’environnement avec votre application. Les applications peuvent alors s’inscrire automatiquement auprès du serveur Service Registry et découvrir d’autres microservices dépendants.

#### <a name="enablediscoveryclient-annotation"></a>Annotation EnableDiscoveryClient

Ajoutez l’annotation suivante au code source de l’application.

```java
@EnableDiscoveryClient
```

Par exemple, regardez l’application piggymetrics provenant des exemples précédents :

```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Configuration distribuée

Pour activer la configuration distribuée, ajoutez la dépendance `spring-cloud-config-client` suivante dans la section des dépendances de votre fichier pom.xml :

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

> [!WARNING]
> Ne spécifiez pas `spring.cloud.config.enabled=false` dans votre configuration de démarrage. Si vous le faites, votre application cessera de fonctionner avec le serveur de configuration.

### <a name="metrics"></a>Mesures

Ajoutez la dépendance `spring-boot-starter-actuator` dans la section des dépendances de votre fichier pom.xml, comme indiqué ici :

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Les métriques sont tirées (pull) périodiquement des points de terminaison JMX. Vous pouvez visualiser les métriques dans le portail Azure.

 > [!WARNING]
 > Spécifiez `spring.jmx.enabled=true` dans votre propriété de configuration. Dans le cas contraire, les métriques ne peuvent pas être visualisées dans Portail Azure.

## <a name="see-also"></a>Voir aussi

* [Analyser les journaux et les métriques des applications](./diagnostic-services.md)
* [Configurer votre serveur de configuration](./how-to-config-server.md)
* [Guide de démarrage rapide Spring](https://spring.io/quickstart)
* [Documentation Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez découvert comment configurer votre application Java Spring en vue de la déployer dans Azure Spring Cloud. Pour savoir comment configurer une instance Config Server, consultez [Configurer une instance Config Server](./how-to-config-server.md).

D’autres exemples sont disponibles sur GitHub : [Exemples Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end
