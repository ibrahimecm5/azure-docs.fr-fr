---
title: Analyser des images conteneur à l’aide de GitHub Actions
description: Découvrez comment analyser les images de conteneur à l’aide de l’action Analyse de conteneur
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: a16c858008ce2233d0382e8a8f2a8e6224bbb6b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315802"
---
# <a name="scan-container-images-using-github-actions"></a>Analyser des images conteneur à l’aide de GitHub Actions

Démarrage de [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) avec la création d’un workflow pour générer et analyser une image de conteneur.

Avec GitHub Actions, vous pouvez accélérer votre processus d’intégration continue et de livraison continue en générant, en analysant et en envoyant des images vers un [registre de conteneurs](https://azure.microsoft.com/services/container-registry/) public ou privé à partir de vos flux de travail.

Dans cet article, nous allons utiliser l’[analyse d'images de conteneur](https://github.com/marketplace/actions/container-image-scan) à partir de la [Place de marché GitHub](https://github.com/marketplace).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Container Registry pour stocker toutes les images de conteneur générées et envoyées. Vous pouvez [créer un Azure Container Registry à partir du portail Azure](../container-registry/container-registry-get-started-portal.md).
- Compte GitHub avec un référentiel actif. Si vous n’en avez pas, inscrivez-vous [gratuitement](https://github.com/join). 
    - Cet exemple utilise l’[exemple d’application Java Spring PetClinic](https://github.com/spring-projects/spring-petclinic).

## <a name="workflow-file-overview"></a>Vue d’ensemble du fichier de workflow

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow.

Le fichier comporte trois sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Créez un registre de conteneurs sur Azure. <br /> 2. Créez un secret GitHub. <br /> 3. Connectez-vous au registre à l’aide de GitHub Actions. |
|**Créer** | 1. Configurez l’environnement. <br /> 2. Générer l’application. |
|**Générer, analyser et envoyer l’image au registre de conteneurs** | 1. Générez l’image. <br /> 2. Analysez l’image. <br /> 3. Envoyez l’image.|

## <a name="create-github-secrets"></a>Créer des secrets GitHub

Avant d’utiliser l’[action de connexion d’Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login), vous devez ajouter les détails de votre Container Registry en tant que secret à votre référentiel GitHub.

Dans cet exemple, vous allez créer trois secrets que vous pourrez utiliser pour vous authentifier auprès d’Azure.  

1. Ouvrez votre dépôt GitHub et accédez à **Settings** (Paramètres).

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="Sélectionnez Settings (Paramètres) dans le volet de navigation.":::

1. Sélectionnez **Secrets** puis **New Secret** (Nouveau secret).

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="Choisissez d’ajouter un secret.":::

1. Collez les valeurs suivantes pour chaque secret créé avec les valeurs suivantes à partir du portail Azure en accédant aux **clés d’accès** dans Container Registry. 

    | Nom du secret GitHub | Valeurs Azure Container Registry |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **Serveur de connexion** |
    |`REGISTRY_USERNAME` | **Nom d’utilisateur** |
    |`REGISTRY_PASSWORD` | **mot de passe** |
    
1. Enregistrez-le en sélectionnant **Add secret** (Ajouter un secret).

## <a name="add-a-dockerfile"></a>Ajouter un Dockerfile

Utilisez l’extrait de code suivant pour créer un `Dockerfile` et le valider dans le référentiel.

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>Utiliser l’action Connexion Docker

Utilisez votre secret avec l’[action Connexion Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login) pour vous authentifier auprès d’Azure.

Dans ce flux de travail, vous vous authentifiez à l’aide de la connexion Azure Container Registry avec le **serveur de connexion**, les détails de **nom d’utilisateur et de **mot de passe** du Registre stockés dans `secrets.ACR_LOGIN_SERVER`, `secrets.REGISTRY_USERNAME` et `secrets.REGISTRY_PASSWORD` respectivement dans l'[action dockr-login](https://github.com/Azure/docker-login). Pour plus d’informations sur le référencement des secrets GitHub dans un fichier de workflow, consultez [Utilisation de secrets chiffrés dans un workflow](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow) dans les documents GitHub.


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>Configurer Java

Configurez l’environnement Java avec l’[action du SDK d’installation Java](https://github.com/marketplace/actions/setup-java-jdk). Pour cet exemple, vous allez configurer l’environnement, générer avec Maven, puis générer, analyser et envoyer l’image vers le registre de conteneurs.

Les [artefacts GitHub](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts) sont un moyen de partager des fichiers dans un workflow entre des tâches. 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>Créer votre image 

Générez et marquez l’image à l’aide de l’extrait de code suivant dans le flux de travail. L’extrait de code suivant utilise l’interface de ligne de commande Docker pour générer et marquer l’image dans un terminal d’interpréteur de commandes. 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>Analyser l’image

Avant d’envoyer l’image générée dans le registre de conteneurs, veillez à analyser l’image et à y rechercher des vulnérabilités à l’aide de l'[action Analyse de l’image de conteneur](https://github.com/marketplace/actions/container-image-scan).

Ajoutez l’extrait de code suivant dans le flux de travail, qui analysera l’image à la recherche de ***vulnérabilités critiques*** de sorte que l’image qui sera envoyée soit sécurisée et conforme aux normes.

Vous pouvez également ajouter d’autres entrées avec cette action ou ajouter un fichier `allowedlist.yaml` à votre référentiel pour ignorer les vulnérabilités et les vérifications des meilleures pratiques. 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

Par exemple : `allowedlist.yaml`.

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>Envoyer l’image dans un registre privé

Une fois que l’image est analysée et qu’aucune vulnérabilité n’a été détectée, il est possible d’envoyer l’image générée dans le registre privé en toute sécurité. L’extrait de code suivant utilise l’interface de ligne de commande Docker dans un terminal d’interpréteur de commandes pour envoyer l’image.

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>Étapes suivantes
- Apprenez à [Déployer sur Azure Container Instances à partir d’Azure Container Registry](../container-instances/container-instances-using-azure-container-registry.md).
