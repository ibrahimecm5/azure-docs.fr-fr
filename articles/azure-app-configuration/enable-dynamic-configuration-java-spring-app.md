---
title: Utiliser la configuration dynamique dans une application Spring Boot
titleSuffix: Azure App Configuration
description: Découvrez comment mettre à jour dynamiquement les données de configuration pour les applications Spring Boot.
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 34225576d435f9ff275f74372afa2cc9f7d50b9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287245"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application Java Spring

App Configuration comporte deux bibliothèques pour Spring. `azure-spring-cloud-appconfiguration-config` nécessite Spring Boot et prend une dépendance envers `spring-cloud-context`. `azure-spring-cloud-appconfiguration-config-web` nécessite Spring Web ainsi que Spring Boot. Les deux bibliothèques prennent en charge le déclenchement manuel pour vérifier les valeurs de configuration actualisées. En outre, `azure-spring-cloud-appconfiguration-config-web` ajoute la prise en charge de la vérification automatique de l’actualisation de la configuration.

L’actualisation vous permet d’actualiser vos valeurs de configuration sans avoir à redémarrer votre application, même si cela entraîne la recréation de tous les beans dans le `@RefreshScope`. La bibliothèque de client met en cache un ID de code de hachage des configurations actuellement chargées afin d’éviter un trop grand nombre d’appels au magasin de configurations. Tant que la valeur mise en cache d’un paramètre n’a pas expiré, l’opération d’actualisation ne met pas à jour la valeur, même si celle-ci a été modifiée dans le magasin de configurations. Par défaut, le délai d’expiration de chaque requête est de 30 secondes. Au besoin, ce délai peut être remplacé.

L’actualisation automatisée de `azure-spring-cloud-appconfiguration-config-web` est déclenchée en fonction de l’activité, notamment de `ServletRequestHandledEvent` Spring Web. Si un `ServletRequestHandledEvent` n’est pas déclenché, l’actualisation automatisée de `azure-spring-cloud-appconfiguration-config-web` ne déclenche pas d’actualisation même si le délai d’expiration du cache a expiré.

## <a name="use-manual-refresh"></a>Utiliser l’actualisation manuelle

App Configuration expose `AppConfigurationRefresh`, qui peut être utilisé pour vérifier si le cache a expiré et, si tel est le cas, pour déclencher une actualisation.

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

La méthode `refreshConfigurations()` d’`AppConfigurationRefresh` retourne un `Future` qui a la valeur true si une actualisation a été déclenchée, et false dans le cas contraire. False signifie que l’heure d’expiration du cache n’a pas expiré, qu’il n’y a eu aucune modification ou qu’un autre thread est en train de vérifier l’existence d’une actualisation.

## <a name="use-automated-refresh"></a>Utiliser l’actualisation automatisée

Pour utiliser l’actualisation automatisée, démarrez par une application Spring Boot qui utilise App Configuration, telle que l’application que vous pouvez créer en suivant le [guide de démarrage rapide Spring Boot pour App Configuration](quickstart-java-spring-app.md).

Ensuite, ouvrez le fichier *pom.xml* dans un éditeur de texte, puis ajoutez une `<dependency>` pour `azure-spring-cloud-appconfiguration-config-web` à l’aide du code suivant.

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> Si vous avez besoin de la prise en charge de dépendances plus anciennes, consultez notre [bibliothèque précédente](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md).

1. Mettre à jour `bootstrap.properties` pour activer l’actualisation

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. Ouvrez le **portail Azure** et accédez à la ressource App Configuration associée à votre application. Sélectionnez **Explorateur de configurations** sous **Opérations** et créez une nouvelle paire clé-valeur en sélectionnant **+ Créer** > **Clé-valeur** pour ajouter les paramètres suivants :

    | Clé | Valeur |
    |---|---|
    | Sentinel | 1 |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

1. Sélectionnez **Appliquer**.

1. Générez votre application Spring Boot avec Maven, puis exécutez-la.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Ouvrez une fenêtre de navigateur, puis accédez à l’URL : `http://localhost:8080`.  Le message associé à votre clé s’affiche.

    Vous pouvez également utiliser *curl* pour tester votre application, par exemple :

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Pour tester la configuration dynamique, ouvrez le portail Azure App Configuration associé à votre application. Sélectionnez l’**Explorateur de configurations**, et mettez à jour la valeur de votre clé affichée, par exemple :

    | Clé | Valeur |
    |---|---|
    | /application/config.message | Hello – Mis à jour |

1. Mettez à jour la clé Sentinel que vous avez créée précédemment avec une nouvelle valeur. Ce changement déclenche l’application pour actualiser toutes les clés de configuration une fois l’intervalle d’actualisation passé.

    | Clé | Valeur |
    |---|---|
    | Sentinel | 2 |

1. Actualisez la page du navigateur pour afficher le nouveau message.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application Spring Boot d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
