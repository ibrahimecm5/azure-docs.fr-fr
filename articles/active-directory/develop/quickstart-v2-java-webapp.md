---
title: 'Démarrage rapide : Ajouter la connexion Microsoft sur une application web Java | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez apprendre à ajouter une connexion avec Microsoft à une application web Java en utilisant OpenID Connect.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: fa85851bba852448550aa3a4c392a329af012eaf
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2022
ms.locfileid: "137804880"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application web Java

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application web Java peut connecter des utilisateurs et appeler l’API Microsoft Graph. Les utilisateurs de n’importe quelle organisation Azure Active Directory (Azure AD) peuvent se connecter à l’application.

 Pour obtenir une vue d’ensemble, consultez le [diagramme de fonctionnement de l’exemple](#how-the-sample-works).

## <a name="prerequisites"></a>Prérequis

Pour exécuter cet exemple, vous avec besoin de ce qui suit :

- [Kit de développement Java (JDK)](https://openjdk.java.net/) 8 ou ultérieur.
- [Maven](https://maven.apache.org/).


#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure

Pour utiliser l’exemple de code dans ce guide de démarrage rapide :

1. Ajouter les URL de réponse `https://localhost:8443/msal4jsample/secure/aad` et `https://localhost:8443/msal4jsample/graph/me`.
1. Créer un secret client.
> [!div class="nextstepaction"]
> [Apporter ces modifications pour moi]()

> [!div class="alert alert-info"]
> ![Déjà configuré](media/quickstart-v2-aspnet-webapp/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-code-sample"></a>Étape 2 : Télécharger l’exemple de code

Téléchargez le projet et extrayez le fichier .zip dans un dossier proche de la racine de votre lecteur. Par exemple, *C:\Azure-Samples*.

Pour utiliser HTTPS avec localhost, indiquez les propriétés `server.ssl.key`. Pour générer un certificat auto-signé, utilisez l’utilitaire keytool (inclus dans JRE).

Voici un exemple :
```
  keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

  server.ssl.key-store-type=PKCS12
  server.ssl.key-store=classpath:keystore.p12
  server.ssl.key-store-password=password
  server.ssl.key-alias=testCert
  ```
  Placez le fichier keystore généré dans le dossier *resources*.

> [!div class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip).

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div class="sxs-lookup"]

#### <a name="step-3-run-the-code-sample"></a>Étape 3 : Exécuter l’exemple de code

Pour exécuter le projet, effectuez l’une des étapes suivantes :

- Exécutez-le directement à partir de votre IDE à l’aide du serveur Spring Boot incorporé.
- Empaquetez-le dans un fichier WAR à l’aide de [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html), puis déployez-le sur une solution de conteneur J2EE comme [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Exécution du projet à partir d’un IDE

Si vous exécutez l’application web à partir d’un IDE, sélectionnez Exécuter, puis accédez à la page d’accueil du projet. Pour cet exemple, l’URL de la page d’accueil standard est https://localhost:8443.

1. Dans la première page, sélectionnez le bouton **Connexion** pour rediriger les utilisateurs vers Azure Active Directory et les inviter à entrer leurs informations d’identification.

1. Une fois les utilisateurs authentifiés, ils sont redirigés vers `https://localhost:8443/msal4jsample/secure/aad`. Ils sont désormais connectés, et la page affiche des informations sur le compte connecté. L’exemple d’interface utilisateur contient les boutons suivants :
    - **Se déconnecter** : déconnecte l’utilisateur actuel de l’application et le redirige vers la page d’accueil.
    - **Afficher les infos sur l’utilisateur** : acquiert un jeton pour Microsoft Graph et appelle Microsoft Graph avec une requête contenant le jeton, qui retourne des informations de base sur l’utilisateur connecté.

##### <a name="running-the-project-from-tomcat"></a>Exécution du projet à partir de Tomcat

Si vous souhaitez déployer l’exemple web sur Tomcat, apportez quelques modifications au code source.

1. Ouvrez *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Supprimez tout le code source et remplacez-le par ce code :

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

2.   Le port HTTP de Tomcat par défaut est 8080, mais vous avez besoin d’une connexion HTTPS sur le port 8443. Pour configurer ce paramètre :
        - Accédez à *tomcat/conf/server.xml*.
        - Recherchez l’étiquette `<connector>`, puis remplacez le connecteur existant par celui-ci :

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

3. Ouvrez une fenêtre d’invite de commandes. Accédez au dossier racine de cet exemple (où se trouve le fichier pom.xml), puis exécutez `mvn package` pour générer le projet.
    - Cette commande génère un fichier *msal-web-sample-0.1.0.war* dans votre répertoire */targets*.
    - Renommez ce fichier *msal4jsample.war*.
    - Déployez le fichier WAR à l’aide de Tomcat ou de toute autre solution de conteneur J2EE.
        - Pour déployer le fichier msal4jsample.war, copiez-le dans le répertoire */webapps/* de votre installation Tomcat, puis démarrez le serveur Tomcat.

4. Une fois le fichier déployé, accédez à https://localhost:8443/msal4jsample à l’aide d’un navigateur.

> [!IMPORTANT]
> Cette application de démarrage rapide utilise un secret client pour s’identifier en tant que client confidentiel. Le secret client étant ajouté en texte brut à vos fichiers projet, nous vous recommandons, pour des raisons de sécurité, d’utiliser un certificat au lieu d’un secret client avant d’utiliser l’application dans un environnement de production. Pour plus d’informations sur l’utilisation d’un certificat, consultez [Informations d’identification de certificat pour l’authentification d’application](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple
![Diagramme montrant le fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Obtenir MSAL

MSAL for Java (MSAL4J) est la bibliothèque Java utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft.

Ajoutez MSAL4J à votre application en utilisant Maven ou Gradle pour gérer vos dépendances. Pour cela, apportez les modifications suivantes au fichier pom.xml (Maven) ou build.gradle (Gradle) de votre application.

Dans pom.xml :

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

Dans build.gradle :

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>Initialiser MSAL

Ajoutez une référence à MSAL for Java en ajoutant le code suivant au début du fichier où vous allez utiliser MSAL4J :

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une présentation plus approfondie de la génération d’applications web qui connectent des utilisateurs à la plateforme d’identités Microsoft, consultez notre série de scénarios en plusieurs parties :

> [!div class="nextstepaction"]
> [Scénario : application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md?tabs=java)
