---
title: Guide pratique pour déployer l’outil d’étiquetage des exemples Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez les différentes façons de déployer l’outil d’étiquetage des exemples Form Recognizer pour faciliter l’apprentissage supervisé.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5a260a1f5c1ab6cd6232dbd0ea9c1035a110cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027015"
---
# <a name="deploy-the-sample-labeling-tool"></a>Déployer l’outil d’étiquetage d’exemples

L’outil d’étiquetage des exemples Form Recognizer est une application qui fournit une interface utilisateur simple, que vous pouvez utiliser pour étiqueter manuellement des formulaires (documents) pour un apprentissage supervisé. Dans cet article, nous allons vous fournir des liens et des instructions qui vous apprendront à :

* [Exécuter l’outil d’étiquetage des exemples localement](#run-the-sample-labeling-tool-locally)
* [Déployer l’outil d’étiquetage des exemples dans une instance Azure Container Instances (ACI)](#deploy-with-azure-container-instances-aci)
* [Utiliser et contribuer à l’outil open source OCR Form Labeling Tool](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Exécuter l’outil d’étiquetage des exemples localement

Le moyen le plus rapide pour commencer à étiqueter des données consiste à exécuter l’outil d’étiquetage des exemples localement. Le guide de démarrage rapide suivant utilise l’API REST Form Recognizer et l’outil d’étiquetage des exemples pour entraîner un modèle personnalisé avec des données étiquetées manuellement.

* [Bien démarrer avec Azure Form Recognizer](label-tool.md)

## <a name="deploy-with-azure-container-instances-aci"></a>Déployer avec Azure Container Instances (ACI)

Avant de commencer, il est important de noter qu’il existe deux façons de déployer l’outil d’étiquetage des exemples sur une instance Azure Container Instances (ACI). Les deux options sont utilisées pour exécuter l’outil d’étiquetage des exemples avec ACI :

* [À l’aide du portail Azure](#azure-portal)
* [Utilisation de l’interface de ligne de commande Azure (CLI)](#azure-cli)

### <a name="azure-portal"></a>Portail Azure

Suivez ces étapes pour créer une nouvelle ressource à partir du portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com/signin/index/).
2. Sélectionnez **Créer une ressource**.
3. Ensuite, sélectionnez **Application web**.

   > [!div class="mx-imgBorder"]
   > ![Sélectionner l’application web](./media/quickstarts/create-web-app.png)

4. Tout d’abord, assurez-vous que l’onglet **De base** est sélectionné. À présent, vous allez avoir besoin de fournir des informations :

   > [!div class="mx-imgBorder"]
   > ![Sélectionner De base](./media/quickstarts/select-basics.png)
   * Abonnement : sélectionnez un abonnement Azure existant
   * Groupe de ressources : vous pouvez réutiliser un groupe de ressources existant ou en créer un pour ce projet. Nous vous recommandons de créer un groupe de ressources.
   * Nom : donnez un nom à votre application web.
   * Publier : sélectionnez **Conteneur Docker**
   * Système d’exploitation : sélectionnez **Linux**
   * Région : choisissez une région pertinente pour vous.
   * Plan Linux : sélectionnez un niveau tarifaire/plan pour votre service d’application.

   > [!div class="mx-imgBorder"]
   > ![Configurer votre application web](./media/quickstarts/select-docker.png)

5. Ensuite, sélectionnez l’onglet **Docker**.

   > [!div class="mx-imgBorder"]
   > ![Sélectionner Docker](./media/quickstarts/select-docker.png)

6. Nous allons maintenant configurer votre conteneur Docker. Sauf indication contraire, tous les champs sont obligatoires :
<!-- markdownlint-disable MD025 -->

* Options : sélectionnez **Conteneur unique**
* Source d’image : sélectionnez **Registre privé**
* URL du serveur : spécifiez `https://mcr.microsoft.com`
* Nom d’utilisateur (facultatif) : créez un nom d’utilisateur.
* Mot de passe (facultatif) : créez un mot de passe sécurisé dont vous vous souviendrez.
* Image et étiquette : spécifiez `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1`
* Déploiement continu : spécifiez **Activé** si vous souhaitez recevoir des mises à jour automatiques lorsque l’équipe de développement apporte des modifications à l’outil d’étiquetage des exemples.
* Commande de démarrage : spécifiez `./run.sh eula=accept`

> [!div class="mx-imgBorder"]
> ![Configurer Docker](./media/quickstarts/configure-docker.png)

* Ensuite, sélectionnez **Vérifier + créer**, puis **Créer** pour déployer votre application web. Lorsque vous avez terminé, vous pouvez accéder à votre application web à l’URL fournie dans la **Vue d’ensemble** de votre ressource.

### <a name="continuous-deployment"></a>Déploiement continu

Une fois que vous avez créé votre application web, vous pouvez activer l’option de déploiement continu :

* Dans le volet gauche, choisissez **Paramètres du conteneur**.
* Dans la fenêtre principale, accédez à Déploiement continu et sélectionnez le bouton **Activé** ou **Désactivé** pour définir votre préférence :

:::image type="content" source="media/label-tool/continuous-deployment.png" alt-text="Capture d’écran : agrandissement des paramètres de conteneur pour le déploiement continu" lightbox="media/label-tool/continuous-deployment-bigger.png":::

> [!NOTE]
> Lorsque vous créez votre application web, vous pouvez également configurer l’autorisation/l’authentification. Cela n’est pas nécessaire au départ.

> [!IMPORTANT]
> Vous devrez peut-être activer TLS pour votre application web afin de l’afficher à son adresse `https`. Suivez les instructions figurant dans [Activer un point de terminaison TLS](../../container-instances/container-instances-container-group-ssl.md) pour configurer un conteneur sidecar qui active TLS/SSL pour votre application web.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

En guise d’alternative à l’utilisation du portail Azure, vous pouvez créer une ressource à l’aide d’Azure CLI. Avant de continuer, vous devez installer [Azure CLI](/cli/azure/install-azure-cli). Vous pouvez ignorer cette étape si vous utilisez déjà Azure CLI.

Voici quelques éléments que vous devez savoir à propos de cette commande :

* `DNS_NAME_LABEL=aci-demo-$RANDOM` génère un nom DNS aléatoire.
* Cet exemple suppose que vous disposez d’un groupe de ressources que vous pouvez utiliser pour créer une ressource. Remplacez `<resource_group_name>` par un groupe de ressources valide associé à votre abonnement.
* Vous devez spécifier l’emplacement auquel vous souhaitez créer la ressource. Remplacez `<region name>` par la région de votre choix pour l’application web.
* Cette commande accepte automatiquement le CLUF.

À partir d’Azure CLI, exécutez cette commande pour créer une ressource d’application web pour l’outil d’étiquetage des exemples :

<!-- markdownlint-disable MD024 -->

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

### <a name="connect-to-azure-ad-for-authorization"></a>Se connecter à Azure AD pour l’autorisation

Nous vous recommandons de connecter votre application web à Azure Active Directory (Azure AD). Cette connexion garantit que seuls les utilisateurs disposant d’informations d’identification valides pourront se connecter à votre application web et l’utiliser. Suivez les instructions qui figurent dans [Configurer votre application App Service ](../../app-service/configure-authentication-provider-aad.md) pour vous connecter à Azure Active Directory.

## <a name="open-source-on-github"></a>Open source sur GitHub

OCR Form Labeling Tool est également disponible en tant que projet open source sur GitHub. Cet outil est une application web créée à l’aide de React + Redux, et il est écrit en TypeScript. Pour en savoir plus ou pour contribuer, consultez [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Étapes suivantes

Utilisez le guide de démarrage rapide [Effectuer l’entraînement avec des étiquettes](label-tool.md) afin d’apprendre à utiliser l’outil pour étiqueter manuellement les données d’entraînement et effectuer un apprentissage supervisé.
