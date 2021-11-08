---
title: Installer et exécuter des conteneurs Docker pour Form Recognizer v2.1
titleSuffix: Azure Applied AI Services
description: Utilisez les conteneurs Docker pour Form Recognizer localement afin d’identifier et d’extraire les paires clé-valeur, les marques de sélection, les tables et la structure des formulaires et des documents.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: local, Docker, conteneur, identifier
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdd04a42ba97721d5d5dafd23f9e8e7b5b661f4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026996"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>Installer et exécuter des conteneurs Form Recognizer v2.1 en préversion

> [!IMPORTANT]
>
> * Les conteneurs Form Recognizer sont en préversion contrôlée. Pour les utiliser, vous devez envoyer une [demande en ligne](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) et recevoir une approbation. Consultez [**Demande d’approbation pour l’exécution du conteneur**](#request-approval-to-run-the-container) ci-dessous pour plus d’informations.
>
> * Pour le formulaire de demande en ligne, vous devez fournir une adresse e-mail valide appartenant à l’organisation propriétaire de l’ID d’abonnement Azure et disposer ou bénéficier d’un accès à cet abonnement.

Azure Form Recognizer est un service IA appliqué qui vous permet de générer des logiciels de traitement de données automatisé à l’aide de la technologie Machine Learning. Form Recognizer vous permet d’identifier et d’extraire du texte, des paires clé/valeur, des marques de sélection, des données de tableau et plus encore de vos formulaires, et de générer des données structurées qui incluent les relations dans le fichier d’origine.

Dans cet article, vous allez apprendre à télécharger, installer et exécuter des conteneurs Form Recognizer. Les conteneurs vous permettent d’exécuter le service Form Recognizer dans votre propre environnement. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données. Les fonctionnalités de Form Recognizer sont prises en charge par six conteneurs de fonctionnalités Form Recognizer : **Disposition**, **Carte de visite**, **Document d’ID**, **Reçu**, **Facture** et **Personnalisé** (pour les conteneurs Reçu, Carte de visite et Document d’ID, vous aurez également besoin du conteneur OCR **Lire**).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin d’un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

Vous avez également besoin des prérequis suivants pour pouvoir utiliser les conteneurs Form Recognizer :

| Obligatoire | Objectif |
|----------|---------|
| **Bonne connaissance de Docker** | Vous devez comprendre les concepts de base liés à Docker (comme les registres, les référentiels, les conteneurs et les images conteneurs), et connaître les principaux [termes et commandes](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology) utilisés pour `docker`. |
| **Moteur Docker installé** | <ul><li>Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#host-computer-requirements). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).</li><li> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. </li><li> Sur **Windows**, vous devez également configurer Docker pour la prise en charge des conteneurs **Linux**.</li></ul>  |
|**Ressource Form Recognizer** | Une ressource [**Azure Form Recognizer à service unique**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ou [**Cognitive Services multiservice**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) dans le portail Azure. Pour utiliser ces conteneurs, vous devez disposer de la clé API et de l’URI de point de terminaison associés. Ces deux valeurs sont disponibles dans la page **Clés et point de terminaison** de Form Recognizer dans le portail Azure : <ul><li>**{FORM_RECOGNIZER_API_KEY}**  : une des deux clés de ressource disponibles.<li>**{FORM_RECOGNIZER_ENDPOINT_URI}**  : point de terminaison de la ressource utilisée pour effectuer le suivi des informations de facturation.</li></li></ul>|
| **Ressource API Vision par ordinateur** | **Pour traiter des cartes de visite, des documents d’ID ou des reçus, vous avez besoin d’une ressource Vision par ordinateur.** <ul><li>Vous pouvez accéder à la fonctionnalité Reconnaître le texte en tant que ressource Azure (API REST ou SDK) ou [conteneur](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) **cognitive-services-Recognize-Text**. Les frais de [facturation](#billing) habituels s’appliquent.</li> <li>Si vous utilisez le conteneur **cognitive-services-recognize-text**, assurez-vous que votre clé Vision par ordinateur pour le conteneur Form Recognizer est la clé spécifiée dans la commande `docker run` ou `docker compose` de Vision par ordinateur pour le conteneur **cognitive-services-recognize-text** et que votre point de terminaison de facturation est le point de terminaison du conteneur (par exemple, `http://localhost:5000`). Si vous utilisez le conteneur Vision par ordinateur et le conteneur Form Recognizer ensemble sur le même hôte, ceux-ci ne peuvent pas être démarrés tous les deux avec le port par défaut *5000*. </li></ul></br>Transmettez la clé API et les points de terminaison pour votre conteneur Cognitive Services ou cloud Azure Vision par ordinateur :<ul><li>**{COMPUTER_VISION_API_KEY}**  : une des deux clés de ressource disponibles.</li><li> **{COMPUTER_VISION_ENDPOINT_URI}**  : point de terminaison de la ressource utilisée pour effectuer le suivi des informations de facturation.</li></ul> |

|Facultatif|Objectif|
|---------|----------|
|**Azure CLI (interface de ligne de commande)** | L’interface [Azure CLI](/cli/azure/install-azure-cli) vous permet d’utiliser un ensemble de commandes en ligne pour créer et gérer des ressources Azure. Elle peut s’installer dans les environnements Windows, macOS et Linux, et s’exécuter dans un conteneur Docker et Azure Cloud Shell. |
|||

## <a name="request-approval-to-run-the-container"></a>Demande d’approbation pour l’exécution du conteneur

Complétez et envoyez le [formulaire d’application pour les services contrôlés](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) pour demander l’approbation d’exécuter ce conteneur.

Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois le formulaire envoyé, l’équipe Azure Cognitive Services Azure l’examinera et vous informera de sa décision par courrier électronique.

Dans le formulaire, vous devez utiliser une adresse e-mail associée à un ID d’abonnement Azure. La ressource Azure que vous utilisez pour exécuter le conteneur doit avoir été créée avec l’ID d’abonnement Azure approuvé. Vérifiez votre adresse e-mail (boîtes de réception et dossiers de courrier indésirable) pour obtenir des mises à jour sur l’état de votre application auprès de Microsoft. Une fois approuvé, vous pourrez exécuter le conteneur après l’avoir téléchargé à partir de Microsoft Container Registry, décrit plus loin dans cet article.

## <a name="host-computer-requirements"></a>Ordinateur hôte requis

L’hôte est un ordinateur x64 qui exécute le conteneur Docker. Il peut s’agir d’un ordinateur local ou d’un service d’hébergement Docker dans Azure, comme :

* [Azure Kubernetes Service](../../../aks/index.yml).
* [Azure Container Instances](../../../container-instances/index.yml).
* Un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure-stack/operator). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

#### <a name="required-containers"></a>Conteneurs requis

Le tableau suivant liste le ou les conteneurs de prise en charge supplémentaires pour chaque conteneur Form Recognizer que vous téléchargez. Pour plus d’informations, reportez-vous à la section [Facturation](#billing).

| Conteneur de fonctionnalités | Conteneur(s) de prise en charge |
|---------|-----------|
| **Disposition** | Aucune |
| **Carte de visite** | **Vision par ordinateur – Lire**|
| **Document d’ID** | **Vision par ordinateur – Lire** |
| **Facture**   | **Disposition** |
| **Réception** |**Vision par ordinateur – Lire** |
| **Personnalisée** | **API personnalisée**, **Supervisé personnalisé**, **Disposition**|

#### <a name="recommended-cpu-cores-and-memory"></a>Cœurs de processeur et mémoire recommandés

> [!Note]
> Les valeurs minimale et recommandée sont basées sur les limites de Docker, *pas* sur les ressources de la machine hôte.

##### <a name="read-layout-and-prebuilt-containers"></a>Conteneurs Lire, Disposition et Prédéfinis

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
| Read 3.2 | 8 cœurs, 16 Go de mémoire | 8 cœurs, 24 Go de mémoire|
| Disposition 2.1 (préversion) | 8 cœurs, 16 Go de mémoire | 8 cœur, 24 Go de mémoire |
| Carte de visite 2.1 (préversion) | 2 cœurs, 4 Go de mémoire | 4 cœurs, 4 Go de mémoire |
| Document d’ID 2.1 (préversion) | 1 cœur, 2 Go de mémoire |2 cœurs, 2 Go de mémoire |
| Facture 2.1 (préversion) | 4 cœurs, 8 Go de mémoire | 8 cœurs, 8 Go de mémoire |
| Reçu 2.1 (préversion) |  4 cœurs, 8 Go de mémoire | 8 cœurs, 8 Go de mémoire  |

##### <a name="custom-containers"></a>Conteneurs personnalisés

Les ordinateurs hôtes requis suivants sont applicables aux demandes **d’entraînement et d’analyse** :

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
| API personnalisée| 0,5 cœur, 0,5 Go de mémoire| 1 cœur, 1 Go de mémoire |
|Supervisé personnalisé | 4 cœurs, 2 Go de mémoire | 8 cœurs, 4 Go de mémoire|

Si vous effectuez uniquement des appels d’analyse, les ordinateurs hôtes requis sont les suivants :

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
|Supervisé personnalisé (Analyser) | 1 cœur, 0,5 Go | 2 cœurs, 1 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory`, qui sont utilisés dans le cadre de la commande `docker compose` ou `docker run`.

> [!TIP]
> Vous pouvez utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour lister vos images conteneurs téléchargées. Par exemple, la commande suivante liste l’ID, le référentiel et la balise de chaque image conteneur téléchargée dans un tableau :
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>Exécuter le conteneur à l’aide de la commande **docker-compose up**

* Remplacez les valeurs {ENDPOINT_URI} et {API_KEY} par votre URI de point de terminaison de ressource et la clé API dans la page de ressources Azure.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : Page des clés et des points de terminaison du portail Azure":::

* Assurez-vous que la valeur CLUF est définie sur « accepter ».

* Vous devez spécifier les valeurs `EULA`, `Billing` et `ApiKey` ; sinon, le conteneur ne démarrera pas.

> [!IMPORTANT]
> Les clés d’abonnement sont utilisées pour accéder à votre ressource Form Recognizer. Ne partagez pas vos clés. Stockez-les en toute sécurité, par exemple, à l’aide de Azure Key Vault. Nous vous recommandons également de régénérer ces clés régulièrement. Une seule clé est nécessaire pour effectuer un appel d’API. Lors de la régénération de la première clé, vous pouvez utiliser la deuxième clé pour un accès continu au service.

### <a name="layout"></a>[Disposition](#tab/layout)

Vous trouverez ci-dessous un exemple `docker compose` autonome permettant d’exécuter le conteneur Disposition de Form Recognizer.  Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration. Entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre instance de conteneur Disposition.

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

Maintenant, vous pouvez démarrer le service à l’aide de la commande [**docker compose**](https://docs.docker.com/compose/) :

```bash
docker-compose up
```

### <a name="business-card"></a>[Carte de visite](#tab/business-card)

Vous trouverez ci-dessous un exemple `docker compose` autonome permettant d’exécuter ensemble les conteneurs Carte de visite et Lire de Form Recognizer. Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration. Entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre instance de conteneur Carte de visite. Entrez {COMPUTER_VISION_ENDPOINT_URI} et {COMPUTER_VISION_API_KEY} pour votre conteneur Lire de Vision par ordinateur.

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Maintenant, vous pouvez démarrer le service à l’aide de la commande [**docker compose**](https://docs.docker.com/compose/) :

```bash
docker-compose up
```

### <a name="id-document"></a>[Document d’ID](#tab/id-document)

Vous trouverez ci-dessous un exemple `docker compose` autonome permettant d’exécuter ensemble les conteneurs Document d’ID et Lire de Form Recognizer. Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration. Entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre conteneur Document d’ID. Entrez les valeurs {COMPUTER_VISION_ENDPOINT_URI} et {COMPUTER_VISION_API_KEY} pour votre conteneur Lire de Vision par ordinateur.

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Maintenant, vous pouvez démarrer le service à l’aide de la commande [**docker compose**](https://docs.docker.com/compose/) :

```bash
docker-compose up
```

### <a name="invoice"></a>[Facture](#tab/invoice)

Vous trouverez ci-dessous un exemple `docker compose` autonome permettant d’exécuter ensemble les conteneurs Facture et Disposition de Form Recognizer. Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration.  Entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour vos conteneurs Facture et Disposition.

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Maintenant, vous pouvez démarrer le service à l’aide de la commande [**docker compose**](https://docs.docker.com/compose/) :

```bash
docker-compose up
```

### <a name="receipt"></a>[Réception](#tab/receipt)

Vous trouverez ci-dessous un exemple `docker compose` autonome permettant d’exécuter ensemble les conteneurs Reçu et Lire de Form Recognizer. Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration. Entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre conteneur Reçu. Entrez les valeurs {COMPUTER_VISION_ENDPOINT_URI} et {COMPUTER_VISION_API_KEY} pour votre conteneur Lire de Vision par ordinateur.

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

Maintenant, vous pouvez démarrer le service à l’aide de la commande [**docker compose**](https://docs.docker.com/compose/) :

```bash
docker-compose up
```

### <a name="custom"></a>[Personnalisée](#tab/custom)

Outre les [prérequis](#prerequisites) mentionnés ci-dessus, vous devrez effectuer les opérations suivantes pour traiter un document personnalisé :

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; Créer un dossier pour stocker les fichiers suivants :

  1. [ **.env**](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; Créer un dossier pour stocker vos données d’entrée

  1. Nommez ce dossier **shared**.
  1. Nous référencerons le chemin du fichier pour ce dossier en tant que **{SHARED_MOUNT_PATH}** .
  1. Copiez le chemin d’accès du fichier à un emplacement pratique, tel que *Microsoft Bloc-notes*. Vous devez l’ajouter à votre fichier **.env**, ci-dessous.

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; Créer un dossier pour stocker les journaux écrits par le service Form Recognizer sur votre ordinateur local

  1. Nommez ce dossier **output**.
  1. Nous référencerons le chemin du fichier pour ce dossier en tant que **{OUTPUT_MOUNT_PATH}** .
  1. Copiez le chemin du fichier à un emplacement pratique, tel que *Microsoft Bloc-notes*. Vous devez l’ajouter à votre fichier **.env**, ci-dessous.

#### <a name="bullet-create-an-environment-file"></a>&bullet; Créer un fichier d’environnement

  1. Nommez ce fichier **.env**.

  1. Déclarez les variables d’environnement suivantes :

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; Créer un fichier **nginx**

  1. Nommez ce fichier **nginx.conf**.

  1. Entrez la configuration suivante :

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/;

        }

        location /status {
            proxy_pass http://docker-api/status;

        }

        location /ready {
            proxy_pass http://docker-api/ready;

        }

        location /swagger {
            proxy_pass http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* Rassemblez au moins six formulaires du même type. Vous allez utiliser ces données pour entraîner le modèle et tester un formulaire. Vous pouvez utiliser un [exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) (téléchargez et extrayez *sample_data.zip*). Téléchargez les fichiers d’entraînement dans le dossier **shared** que vous avez créé ci-dessus.

* Si vous souhaitez étiqueter vos données, téléchargez l’[outil d’étiquetage des exemples Form Recognizer pour Windows](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga). Le téléchargement importera le fichier .exe d’outil d’étiquetage que vous utiliserez pour étiqueter les données présentes sur votre système de fichiers local. Vous pouvez ignorer les avertissements qui se produisent pendant le processus de téléchargement.

#### <a name="create-a-new-sample-labeling-tool-project"></a>Créer un nouveau projet d’outil d’étiquetage des exemples

* Ouvrez l’outil d’étiquetage en double-cliquant sur le fichier .exe de l’outil d’étiquetage des exemples.
* Dans le volet gauche de l’outil, sélectionnez l’onglet Connexions.
* Sélectionnez la création d’un nouveau projet et attribuez-lui un nom et une description.
* Pour le fournisseur, choisissez l’option du système de fichiers local. Pour le dossier local, veillez à entrer le chemin d’accès au dossier où vous avez stocké les exemples de fichiers de données.
* Revenez à l’onglet d’accueil et sélectionnez l’option « Utiliser un modèle personnalisé pour entraîner un modèle avec des étiquettes et des paires clé/valeur ».
* Sélectionnez le bouton Entraîner dans le volet gauche pour entraîner le modèle étiqueté.
* Enregistrez cette connexion et utilisez-la pour étiqueter vos demandes.
* Vous pouvez choisir d’analyser le fichier de votre choix par rapport au modèle entraîné.

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; Créer un fichier **docker compose**

1. Nommez ce fichier **docker-compose.yml**

2. Voici un exemple `docker compose` autonome pour exécuter ensemble les conteneurs Disposition, Outil d’étiquetage, API personnalisée et Supervisé personnalisé de Form Recognizer. Avec `docker compose`, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec la commande `docker-compose up`, vous créez et démarrez tous les services à partir de votre configuration.

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>Veiller à ce que le service soit en cours d’exécution

Pour vous assurer que le service fonctionne correctement, exécutez ces commandes dans un interpréteur Ubuntu.

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>Créer une nouvelle connexion

* Dans le volet gauche de l’outil, sélectionnez l’onglet **Connexions**.
* Sélectionnez **créer un nouveau projet** et attribuez-lui un nom et une description.
* Pour le fournisseur, choisissez l’option **système de fichiers local**. Pour le dossier local, veillez à entrer le chemin d’accès au dossier où vous avez stocké les fichiers d’**exemples de données**.
* Revenez à l’onglet d’accueil et sélectionnez **Utiliser un modèle personnalisé pour entraîner un modèle avec des étiquettes et des paires clé/valeur**.
* Sélectionnez le **bouton Entraîner** dans le volet gauche pour entraîner le modèle étiqueté.
* **Enregistrez** cette connexion et utilisez-la pour étiqueter vos demandes.
* Vous pouvez choisir d’analyser le fichier de votre choix par rapport au modèle entraîné.

---

## <a name="validate-that-the-service-is-running"></a>Vérifier que le serveur est en cours d’exécution

Il existe plusieurs façons de vérifier que le conteneur est exécuté :

* Le conteneur fournit une page d’accueil au niveau `\`, qui est une validation visuelle que le conteneur est en cours d’exécution.

* Vous pouvez ouvrir votre navigateur web habituel, puis accéder à l’adresse IP externe et au port exposé du conteneur en question. Utilisez les différentes URL de requête ci-dessous pour vérifier que le conteneur est en cours d’exécution. Les exemples d’URL de requête listés ci-dessous sont `http://localhost:5000`, mais votre conteneur spécifique peut varier. N’oubliez pas que vous accédez à l’**adresse IP externe** et au port exposé de votre conteneur.

  URL de la demande    | Objectif
  ----------- | --------
  |**http://<span></span>localhost:5000/** | Le conteneur fournit une page d’hébergement.
  |**http://<span></span>localhost:5000/ready**     | Cette demande, qui utilise GET, permet de vérifier que le conteneur est prêt à accepter une requête sur le modèle. Cette requête peut être utilisée pour les probes liveness et readiness de Kubernetes.
  |**http://<span></span>localhost:5000/status** | Cette demande, qui utilise GET, permet de vérifier si la clé API servant à démarrer le conteneur est valide sans provoquer de requête de point de terminaison. Cette requête peut être utilisée pour les probes liveness et readiness de Kubernetes.
  |**http://<span></span>localhost:5000/swagger** | Le conteneur fournit un ensemble complet de documentation pour les points de terminaison et une fonctionnalité Essayer. Avec cette fonctionnalité, vous pouvez entrer vos paramètres dans un formulaire HTML basé sur le web, et constituer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer les en-têtes HTTP, et le format du corps qui est nécessaire.
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="Capture d’écran : Page d’accueil du conteneur Azure":::

## <a name="stop-the-containers"></a>Arrêter les conteneurs

Pour arrêter les conteneurs, utilisez la commande suivante :

```console
docker-compose down
```

## <a name="billing"></a>Facturation

Les conteneurs Form Recognizer envoient des informations de facturation à Azure en utilisant une ressource Form Recognizer sur votre compte Azure.

Les requêtes sur le conteneur sont facturées au niveau tarifaire de la ressource Azure utilisée pour `ApiKey`. Chaque instance de conteneur utilisée pour traiter vos documents et vos images vous sera facturée. Par conséquent, si vous utilisez la fonctionnalité de carte de visite, les instances de conteneur `BusinessCard` et `Compuer Vision Read` de Form Recognizer vous seront facturées. Pour la fonctionnalité de facture, les instances de conteneur `Invoice` et `Layout` de Form Recognizer vous seront facturées. *Consultez* la facturation des conteneurs [Form Recognizer](https://azure.microsoft.com/pricing/details/form-recognizer/) et des fonctionnalités [Lire](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) de Vision par ordinateur.

Les conteneurs Azure Cognitive Services ne sont pas concédés sous licence dans le but de s’exécuter sans être connectés au point de terminaison de mesure/facturation. Vous devez configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au point de terminaison de facturation à tout moment. Les conteneurs Cognitive Services n’envoient pas de données client, telles que l’image ou le texte analysé, à Microsoft.

### <a name="connect-to-azure"></a>Connexion à Azure

Le conteneur a besoin des valeurs d’arguments de facturation pour s’exécuter. Ces valeurs permettent au conteneur de se connecter au point de terminaison de facturation. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes. Si le conteneur ne se connecte pas à Azure dans la fenêtre de temps imparti, il continue de s’exécuter, mais ne traite pas les requêtes tant que le point de terminaison de facturation n’est pas restauré. Une tentative de connexion est effectuée 10 fois, toutes les 10 à 15 minutes. S’il ne parvient pas à se connecter au point de terminaison de facturation au cours de ces 10 essais, le conteneur cesse de traiter les demandes. Pour obtenir un exemple des informations envoyées à Microsoft en lien avec la facturation, consultez le [Forum aux questions sur le conteneur Cognitive Services](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work).

### <a name="billing-arguments"></a>Arguments de facturation

La commande [**docker-compose up**](https://docs.docker.com/engine/reference/commandline/compose_up/) démarre le conteneur quand des valeurs valides sont attribuées aux trois options suivantes :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé API de la ressource Cognitive Services utilisée pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur une clé API pour la ressource provisionnée spécifiée dans `Billing`. |
| `Billing` | Point de terminaison de la ressource Cognitive Services utilisé pour le suivi des informations de facturation.<br/>La valeur de cette option doit être définie sur l’URI de point de terminaison d’une ressource Azure provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être définie sur **accepter**. |

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](form-recognizer-container-configuration.md).

## <a name="summary"></a>Résumé

Et voilà ! Dans cet article, vous avez découvert les concepts et les workflows de téléchargement, d’installation et d’exécution des conteneurs Form Recognizer. En résumé :

* Form Recognizer fournit sept conteneurs Linux pour Docker.
* Les images conteneur sont téléchargées à partir de mcr.
* Les images conteneurs s’exécutent dans Docker.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas de données relatives aux clients (par exemple, l’image ou le texte en cours d’analyse) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [Paramètres de configuration des conteneurs Form Recognizer](form-recognizer-container-configuration.md) 
