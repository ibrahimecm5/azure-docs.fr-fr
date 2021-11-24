---
title: Authentification de service à service – Data Lake Storage Gen2 – SDK Java
description: Découvrez comment procéder à une authentification de service à service auprès de Data Lake Storage Gen2 en utilisant Azure Active Directory et Java
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: normesta
ms.openlocfilehash: 8f36eaef8c84afd1010d6e4ab2714b0d538028b9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494542"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen2-using-java"></a>Authentification de service à service auprès de Data Lake Storage Gen2 à l’aide de Java

> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Dans cet article, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Java pour effectuer une authentification de service à service auprès de Azure Data Lake Storage Gen2. L’authentification des utilisateurs finaux auprès de Data Lake Storage Gen2 avec le SDK Java n’est pas prise en charge.

## <a name="prerequisites"></a>Prérequis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « web » Azure Active Directory**. Vous devez avoir suivi la procédure indiquée dans [Authentification de service à service auprès de Data Lake Storage Gen2 à l’aide d’Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de build et de projet. Bien qu’il soit possible de ne pas les utiliser, les systèmes de build comme Maven ou Gradle facilitent considérablement la gestion des dépendances.

* (Facultatif) Un environnement IDE comme [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaire.

## <a name="service-to-service-authentication"></a>Authentification de service à service

1. Créez un projet Maven en utilisant [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) à partir de la ligne de commande ou à l’aide d’un environnement de développement intégré (IDE). Vous trouverez [ici](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) des instructions sur la création d’un projet Java à l’aide d’IntelliJ. Vous trouverez [ici](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) des instructions sur la création d’un projet à l’aide d’Eclipse.

2. Ajoutez les dépendances suivantes à votre fichier Maven **pom.xml**. Ajoutez l’extrait de code suivant avant la balise **\</project>** :

    ```xml
    <dependencies>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-storage-file-datalake</artifactId>
          <version>12.6.0</version>
      </dependency>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-identity</artifactId>
          <version>1.3.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    La première dépendance consiste à utiliser le kit de développement logiciel (SDK) Data Lake Storage Gen2 (`azure-storage-file-datalake`) à partir du référentiel Maven. La seconde dépendance consiste à spécifier le framework de journalisation (`slf4j-nop`) à utiliser pour cette application. Le SDK Data Lake Storage Gen2 utilise la façade de journalisation [slf4j](https://www.slf4j.org/). Cela vous permet de choisir parmi plusieurs frameworks de journalisation populaires, par exemple log4j, la journalisation Java, Logback. Vous pouvez aussi n’utiliser aucune journalisation. Pour cet exemple, nous désactivons la journalisation et, par conséquent, nous utilisons la liaison **slf4j-nop**. Pour utiliser d’autres options de journalisation dans votre application, consultez [déclaration des dépendances de projet pour la journalisation](https://www.slf4j.org/manual.html#projectDep).

3. Ajoutez les instructions import ci-après à votre application.

    ```java
    import com.azure.identity.ClientSecretCredential;
    import com.azure.identity.ClientSecretCredentialBuilder;
    import com.azure.storage.file.datalake.DataLakeDirectoryClient;
    import com.azure.storage.file.datalake.DataLakeFileClient;
    import com.azure.storage.file.datalake.DataLakeServiceClient;
    import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
    import com.azure.storage.file.datalake.DataLakeFileSystemClient;
    import com.azure.storage.file.datalake.models.ListPathsOptions;
    import com.azure.storage.file.datalake.models.PathAccessControl;
    import com.azure.storage.file.datalake.models.PathPermissions;
    ```

4. Utilisez l’extrait de code ci-après dans votre application Java afin d’obtenir un jeton pour l’application web Active Directory que vous avez créée précédemment à l’aide de l’une des classes de `StorageSharedKeyCredential` (l’exemple ci-après utilise `credential`). Le fournisseur de jetons met en cache les informations d’identification utilisées pour obtenir le jeton en mémoire, et renouvelle automatiquement le jeton s’il est sur le point d’expirer. Il est possible de créer vos propres sous-classes de `StorageSharedKeyCredential` afin que les jetons soient obtenus par votre code client. Toutefois, pour le moment, utilisons simplement le jeton fourni dans le Kit de développement logiciel (SDK).

    Remplacez **FILL-IN-HERE** (à remplir) par les valeurs réelles de l’application web Azure Active Directory.

    ```java
    private static String clienttId = "FILL-IN-HERE";
    private static String tenantId = "FILL-IN-HERE";
    private static String clientSecret = "FILL-IN-HERE";
   
    ClientSecretCredential credential = new ClientSecretCredentialBuilder().clientId(clientId).tenantId(tenantId).clientSecret(clientSecret).build();
    ```

Le kit de développement logiciel (SDK) Data Lake Storage Gen2 propose des méthodes pratiques pour gérer les jetons de sécurité nécessaires et ainsi communiquer avec le compte Data Lake Storage Gen2. Toutefois, le Kit de développement (SDK) n’impose pas d’utiliser ces seules méthodes. Vous pouvez utiliser tout autre moyen pour obtenir le jeton, par exemple à l’aide de [la bibliothèque de client Azure Identity](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity), ou de votre propre code personnalisé.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser l’authentification des utilisateurs finaux auprès de Data Lake Storage Gen2 à l’aide du Kit de développement logiciel (SDK) Java. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser le Kit de développement logiciel (SDK) Java pour travailler avec Data Lake Storage Gen2.

* [Opérations sur les données dans Data Lake Storage Gen2 à l’aide du kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)
