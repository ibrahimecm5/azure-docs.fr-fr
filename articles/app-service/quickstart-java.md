---
title: 'Démarrage rapide : Créer une application Java dans Azure App Service'
description: Déployez votre premier programme Hello World Java dans Azure App Service en quelques minutes. Le plug-in Azure Web App pour Maven facilite le déploiement d’applications Java.
keywords: azure, app service, application web, windows, linux, java, maven, démarrage rapide
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: 09e49d2d642f80008fb5e1d0d36d4db55d56200a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307048"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Démarrage rapide : Créer une application Java dans Azure App Service

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment utiliser [Azure CLI](/cli/azure/get-started-with-azure-cli) avec le [plug-in Azure Web App pour Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) afin de déployer un fichier .jar ou .war. Utilisez les onglets pour basculer entre les instructions Java SE et Tomcat.

Si Maven n’est pas votre outil de développement préféré, consultez nos tutoriels similaires destinés aux développeurs Java :
+ [Gradle](./configure-language-java.md?pivots=platform-linux#gradle)
+ [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)
+ [Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)
+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-webapp)

![Exemple d’application en cours d’exécution dans Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Créer une application Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clonez l’exemple de projet [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```azurecli-interactive
git clone https://github.com/spring-guides/gs-spring-boot
```

Accédez au répertoire du projet terminé.

```azurecli-interactive
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Exécutez la commande Maven suivante dans l’invite Cloud Shell pour créer une application nommée `helloworld` :

```azurecli-interactive
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ensuite, changez votre répertoire de travail vers le dossier du projet :

```azurecli-interactive
cd helloworld
```

# <a name="jboss-eap"></a>[EAP JBoss](#tab/jbosseap)

::: zone pivot="platform-windows"

JBoss EAP est uniquement disponible dans la version Linux d’App Service. Sélectionnez le bouton **Linux** en haut de cet article pour consulter les instructions de démarrage rapide pour JBoss EAP.

::: zone-end
::: zone pivot="platform-linux"

Clonez l’application de démonstration Pet Store.

```azurecli-interactive
git clone https://github.com/agoncal/agoncal-application-petstore-ee7.git
```

Modifiez le répertoire vers le projet cloné.

```azurecli-interactive
cd agoncal-application-petstore-ee7
```

::: zone-end

---

## <a name="configure-the-maven-plugin"></a>Configurer le plug-in Maven

Le processus de déploiement sur Azure App Service utilise vos informations d’identification Azure dans Azure CLI automatiquement. Si Azure CLI n’est pas installé localement, le plug-in Maven effectue l’authentification avec OAuth ou la connexion de l’appareil. Pour plus d’informations, consultez [Authentification avec les plug-ins Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Exécutez la commande Maven ci-dessous pour configurer le déploiement. Cette commande vous permet de configurer le système d’exploitation App Service, la version de Java et la version de Tomcat.

```azurecli-interactive
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.1:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quand Lorsque vous êtes invité à choisir l’option **Abonnement**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
2. Quand vous êtes invité à choisir l’option **Application web**, sélectionnez l’option par défaut `<create>` en appuyant sur Entrée.
3. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Windows** en entrant `1`.
4. Quand vous êtes invité à choisir l’option **javaVersion**, sélectionnez **Java 8** en entrant `1`.
5. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **P1v2** en entrant `10`.
6. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    Le résumé généré ressemble à l’extrait de code ci-dessous.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quand Lorsque vous êtes invité à choisir l’option **Abonnement**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
2. Quand vous êtes invité à choisir l’option **Application web**, sélectionnez l’option par défaut `<create>` en appuyant sur Entrée.
3. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Windows** en entrant `1`.
4. Quand vous êtes invité à choisir l’option **javaVersion**, sélectionnez **Java 8** en entrant `1`.
5. Quand vous êtes invité à choisir l’option **webContainer**, sélectionnez **Tomcat 8.5** en entrant `1`.
6. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **P1v2** en entrant `10`.
7. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    Le résumé généré ressemble à l’extrait de code ci-dessous.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[EAP JBoss](#tab/jbosseap)

JBoss EAP est uniquement disponible dans la version Linux d’App Service. Sélectionnez le bouton **Linux** en haut de cet article pour consulter les instructions de démarrage rapide pour JBoss EAP.

---

::: zone-end
::: zone pivot="platform-linux"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quand vous êtes invité à choisir l’option **Abonnement**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, sélectionnez l’option par défaut `<create>` en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Linux** en appuyant sur Entrée.
2. Quand vous êtes invité à choisir l’option **javaVersion**, sélectionnez **Java 8** en entrant `1`.
3. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **P1v2** en entrant `9`.
4. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quand vous êtes invité à choisir l’option **Abonnement**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Quand vous êtes invité à choisir l’option **Application web**, sélectionnez l’option par défaut `<create>` en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Linux** en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **javaVersion**, sélectionnez **Java 8** en entrant `1`.
1. Quand vous êtes invité à choisir l’option **webcontainer**, sélectionnez **Tomcat 8.5** en entrant `3`.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **P1v2** en entrant `9`.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[EAP JBoss](#tab/jbosseap)

1. Quand Lorsque vous êtes invité à choisir l’option **Abonnement**, sélectionnez l’abonnement (`Subscription`) approprié en entrant le numéro indiqué au début de la ligne.
1. Lorsque l’option **Application web** apparaît, acceptez l’option par défaut `<create>` en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **Système d’exploitation**, sélectionnez **Linux** en appuyant sur Entrée.
1. Quand vous êtes invité à choisir l’option **javaVersion**, sélectionnez **Java 8** en entrant `1`.
1. Quand vous êtes invité à choisir l’option **webContainer**, sélectionnez **Jbosseap 7** en entrant `1`.
1. Quand vous êtes invité à choisir l’option **Niveau tarifaire**, sélectionnez **P1v3** en entrant `1`.
1. Enfin, appuyez sur Entrée dans la dernière invite pour confirmer vos sélections.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : petstoreee7-1623451825408
    ResourceGroup : petstoreee7-1623451825408-rg
    Region : centralus
    PricingTier : P1v3
    OS : Linux
    Java : Java 8
    Web server stack: Jbosseap 7
    Deploy to slot : false
    Confirm (Y/N) [Y]: y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:01 min
    [INFO] Finished at: 2021-06-11T15:52:25-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Vous pouvez modifier les configurations d’App Service directement dans votre `pom.xml` si nécessaire. Voici les plus courantes :

Propriété | Obligatoire | Description | Version
---|---|---|---
`<schemaVersion>` | false | Spécifiez la version du schéma de configuration. Les valeurs prises en charge sont les suivantes : `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Spécifiez l’ID d’abonnement. | 0.1.0+
`<resourceGroup>` | true | Groupe de ressources Azure pour votre application web. | 0.1.0+
`<appName>` | true | Le nom de votre application web. | 0.1.0+
`<region>` | false | Spécifie la région où votre application web sera hébergée ; la valeur par défaut est **centralus**. Toutes les régions valides dans la section [Régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | Le niveau tarifaire pour votre application web. La valeur par défaut est **P1v2** pour une charge de travail de production, tandis que **B2** est la valeur minimale recommandée pour le développement/test Java. [En savoir plus](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | false | La configuration de l’environnement de runtime, dont vous pouvez afficher les détails [ici](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | false | La configuration de déploiement, dont vous pouvez afficher les détails [ici](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Soyez attentif aux valeurs `<appName>` et `<resourceGroup>` (`helloworld-1590394316693` et `helloworld-1590394316693-rg` dans la démonstration). Elles seront utilisées ultérieurement.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Déployer l’application

Une fois que la configuration est prête dans votre fichier POM, vous pouvez déployer votre application Java sur Azure avec une commande unique.

```azurecli-interactive
mvn package azure-webapp:deploy
```

::: zone pivot="platform-linux"

> [!NOTE]
> Pour JBoss EAP, exécutez `mvn package azure-webapp:deploy -DskipTests` pour désactiver le test car il implique l’installation locale de Wildfly. 

::: zone-end

Une fois le déploiement terminé, votre application est prête à l’adresse `http://<appName>.azurewebsites.net/` (`http://helloworld-1590394316693.azurewebsites.net` dans la démonstration). Ouvrez l’URL avec votre navigateur web local. Vous devez voir l’écran suivant :

![Exemple d’application en cours d’exécution dans Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Félicitations !** Vous avez déployé votre première application Java sur App Service.

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources plus tard, supprimez le groupe de ressources du portail en exécutant la commande suivante dans Cloud Shell :

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L’exécution de cette commande peut prendre une minute.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à Azure DB pour PostgreSQL à l’aide de Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurer CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informations sur la tarification](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Agréger les journaux et les métriques](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Monter en puissance](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Ressources Azure pour les développeurs Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurer votre application Java](configure-language-java.md)
