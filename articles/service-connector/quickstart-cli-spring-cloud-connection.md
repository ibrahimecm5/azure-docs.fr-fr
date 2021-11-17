---
title: Démarrage rapide - Créer une connexion de service dans Spring Cloud avec le Azure CLI
description: Démarrage rapide montrant comment créer une connexion de service dans Spring Cloud avec l'Azure CLI
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 13f2cf7abc24c2f84ea95f0d3779c015680ca01d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554576"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>Démarrage rapide : Créer une connexion de service dans Spring Cloud avec l'Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Ce démarrage rapide vous présente les options permettant de créer une instance Azure Web PubSub avec Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.30.0 ou ultérieure d’Azure CLI est exigée pour ce guide de démarrage rapide. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Ce guide de démarrage rapide part du principe que vous disposez déjà d’au moins une application Cloud Spring s’exécutant sur Azure. Si vous n’avez pas d’application Spring Cloud, [créez-en une](../spring-cloud/quickstart.md).


## <a name="view-supported-target-service-types"></a>Afficher les types de services cibles pris en charge

Utilisez la commande Azure CLI [az spring-cloud connection]() pour créer et gérer les connexions de service à votre application Spring Cloud. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az spring-cloud connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>Créer une connexion de service

#### <a name="using-access-key"></a>[Utilisation d’une clé d’accès](#tab/Using-access-key)

Utilisez la commande Azure CLI [az spring-cloud connection]() pour créer une connexion de service à un stockage d’objets blob avec une clé d’accès, en indiquant les informations suivantes :

- **Nom du groupe de ressources de Spring Cloud :**  le nom du groupe de ressources de Spring Cloud.
- **Nom du Spring Cloud :**  le nom de votre Spring Cloud.
- **Nom de l'application Spring Cloud :**  le nom de votre application Spring Cloud qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az spring-cloud connection create storage-blob --secret
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az spring-cloud connection create storage-blob --new --secret` provisionner un nouveau et vous connecter directement à votre service d'applications.

#### <a name="using-managed-identity"></a>[Utilisation d’une identité managée](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> Pour utiliser une identité managée, vous devez être autorisé à [attribuer des rôles Azure AD](/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal). Sans cette autorisation, la création de la connexion échoue. Vous pouvez demander à votre propriétaire d’abonnement l’autorisation d’utiliser une clé d’accès pour créer la connexion.

Utilisez la commande Azure CLI [az spring-cloud connection]() pour créer une connexion de service à un stockage d’objets blob avec une identité managée affectée par le système, en indiquant les informations suivantes :

- **Nom du groupe de ressources de Spring Cloud :**  le nom du groupe de ressources de Spring Cloud.
- **Nom du Spring Cloud :**  le nom de votre Spring Cloud.
- **Nom de l'application Spring Cloud :**  le nom de votre application Spring Cloud qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az spring-cloud connection create storage-blob --system-identity
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az spring-cloud connection create --system-identity --new --secret` provisionner un nouveau et vous connecter directement à votre service d'applications.

---

## <a name="view-connections"></a>Afficher les connexions

Utilisez la commande Azure CLI [az spring-cloud connection ]()pour répertorier la connexion à votre application Spring Cloud, en fournissant les informations suivantes :

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + Apache Kafka sur Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)
