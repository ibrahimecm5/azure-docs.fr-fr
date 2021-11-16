---
title: 'Démarrage rapide : Créer une connexion de service dans App Service avec Azure CLI'
description: Guide de démarrage rapide montrant comment créer une connexion de service dans App Service avec Azure CLI
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a960bc3064b970fbfd463d0a7f1577354ca7adf1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850277"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>Démarrage rapide : Créer une connexion de service dans App Service avec Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Ce démarrage rapide vous présente les options permettant de créer une instance Azure Web PubSub avec Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.30.0 ou ultérieure d’Azure CLI est exigée pour ce guide de démarrage rapide. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Ce guide de démarrage rapide part du principe que vous disposez déjà d’au moins une instance App Service s’exécutant dans Azure. Si vous n’avez pas encore d’instance App Service, [créez-en une](../app-service/quickstart-dotnetcore.md).

## <a name="view-supported-target-service-types"></a>Afficher les types de services cibles pris en charge

Utilisez la commande Azure CLI [az webapp connection](/cli/azure/webapp/connection) pour créer et gérer les connexions de service à App Service. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>Créer une connexion de service

#### <a name="using-access-key"></a>[Utilisation d’une clé d’accès](#tab/Using-access-key)

Utilisez la commande Azure CLI [az webapp connection](/cli/azure/webapp/connection) pour créer une connexion de service à un stockage d’objets blob avec une clé d’accès, en indiquant les informations suivantes :

- **Nom du groupe de ressources du service de calcul source** : nom du groupe de ressources de l’instance App Service.
- **Nom App Service** : nom de votre instance App Service qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az webapp connection create storage-blob --new --secret` provisionner un nouveau et vous connecter directement à votre service d'applications.

#### <a name="using-managed-identity"></a>[Utilisation d’une identité managée](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> Pour utiliser une identité managée, vous devez être autorisé à [attribuer des rôles Azure AD](/active-directory/managed-identities-azure-resources/howto-assign-access-portal). Sans cette autorisation, la création de la connexion échoue. Vous pouvez demander à votre propriétaire d’abonnement l’autorisation d’utiliser une clé d’accès pour créer la connexion.

Utilisez la commande Azure CLI [az webapp connection](/cli/azure/webapp/connection) pour créer une connexion de service à un stockage d’objets blob avec une identité managée affectée par le système, en indiquant les informations suivantes :

- **Nom du groupe de ressources du service de calcul source** : nom du groupe de ressources de l’instance App Service.
- **Nom App Service** : nom de votre instance App Service qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az webapp connection create storage-blob --new --system-identity` provisionner un nouveau et vous connecter directement à votre service d'applications.

---

## <a name="view-connections"></a>Afficher les connexions

Utilisez la commande Azure CLI [az webapp connection](/cli/azure/webapp/connection) pour lister les connexions à votre instance App Service, en fournissant les informations suivantes :

- **Nom du groupe de ressources du service de calcul source** : nom du groupe de ressources de l’instance App Service.
- **Nom App Service** : nom de votre instance App Service qui se connecte au service cible.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Tutoriel : WebApp + Stockage avec Azure CLI](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutoriel : WebApp + PostgreSQL avec Azure CLI](./tutorial-django-webapp-postgres-cli.md)
