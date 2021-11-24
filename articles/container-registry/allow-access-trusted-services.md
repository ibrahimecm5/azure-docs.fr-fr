---
title: Accéder à un registre ayant un accès réseau restreint à l’aide du service Azure de confiance
description: Activer une instance de service Azure de confiance pour accéder en toute sécurité à un registre de conteneurs ayant un accès réseau restreint pour tirer (pull) ou envoyer (push) des images
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 8d4379c8ea9935c366c94828e58bc236d9e4310f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327364"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Permettre à des services de confiance d’accéder en toute sécurité à un registre de conteneurs ayant un accès réseau restreint (préversion)

Azure Container Registry peut permettre à certains services Azure de confiance d’accéder à un registre configuré avec des règles d’accès réseau. Lorsque les services de confiance sont autorisés, une instance de service de confiance peut contourner en toute sécurité les règles de réseau du registre et effectuer des opérations telles que le tirage (pull) ou l’envoi (push) d’images. Cet article explique comment activer et utiliser les services approuvés avec un registre de conteneurs Azure limité au réseau.

Pour exécuter les exemples de commandes fournis dans cet article, utilisez Azure Cloud Shell ou une installation locale d’Azure CLI. Si vous souhaitez l’utiliser en local, la version 2.18 ou une version ultérieure est requise. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

L’autorisation d’accès au registre par des services Azure de confiance est une fonctionnalité d’**évaluation**.

## <a name="limitations"></a>Limites

* Pour les scénarios d’accès au registre qui ont besoin d’une identité gérée, seule une identité affectée par le système peut être utilisée. Les identités managées affectées par l’utilisateur ne sont pas prises en charge actuellement.
* L’autorisation des services de confiance ne s’applique pas à un registre de conteneurs configuré avec un [point de terminaison de service](container-registry-vnet.md). Cette fonctionnalité ne concerne que les registres dont l’accès est restreint par un [point de terminaison privé](container-registry-private-link.md) ou qui appliquent des [règles d’accès d’IP publiques](container-registry-access-selected-networks.md). 

## <a name="about-trusted-services"></a>À propos des services de confiance

Azure Container Registry dispose d’un modèle de sécurité à plusieurs niveaux, prenant en charge plusieurs configurations de réseau qui restreignent l’accès à un registre, notamment :

* [Un point de terminaison privé avec Azure Private Link](container-registry-private-link.md). Lorsqu’il est configuré, le point de terminaison privé d’un registre est accessible uniquement aux ressources du réseau virtuel, à l’aide d’adresses IP privées.  
* [Des règles de pare-feu de registre](container-registry-access-selected-networks.md), qui autorisent l’accès au point de terminaison public du registre uniquement à partir d’IP publiques ou de plages d’IP publiques spécifiques. Vous pouvez également configurer le pare-feu pour bloquer tout accès au point de terminaison public quand des points de terminaison privés sont utilisés.

Lorsqu’il est déployé dans un réseau virtuel ou configuré avec des règles de pare-feu, un registre refuse l’accès aux utilisateurs ou services extérieurs à ces sources. 

Plusieurs services Azure multilocataires fonctionnent à partir de réseaux qui ne peuvent pas être inclus dans ces paramètres réseau du registre, ce qui les empêche d’effectuer des opérations (par exemple, tirer (pull) ou envoyer (push) des images depuis et vers le registre). En désignant certaines instances de service comme étant « de confiance », le propriétaire du registre peut autoriser certaines ressources Azure à contourner en toute sécurité les paramètres réseau du registre pour effectuer des opérations de registre. 

### <a name="trusted-services"></a>Services approuvés

Les instances des services suivants peuvent accéder à un registre de conteneurs ayant un accès réseau restreint si le paramètre **Autoriser les services approuvés** du registre est activé (valeur par défaut). D’autres services seront ajoutés au fil du temps.

Lorsque cela est indiqué, l’accès par le service approuvé requiert une configuration supplémentaire d’une identité gérée dans une instance de service, l’attribution d’un [rôle RBAC](container-registry-roles.md) et l’authentification auprès du Registre. Pour obtenir des exemples d’étapes, consultez [Flux de travail de services approuvés](#trusted-services-workflow), plus loin dans cet article.

|Service approuvé  |Scénarios d’utilisation pris en charge  | Configurer une identité gérée avec le rôle RBAC
|---------|---------|------|
| Microsoft Defender pour le cloud | Analyse des vulnérabilités par [Microsoft Defender pour les registres de conteneurs](scan-images-defender.md) | No |
|ACR Tasks     | [Accéder au registre parent ou à un registre différent à partir d’une tâche ACR](container-registry-tasks-cross-registry-authentication.md)       | Yes |
|Machine Learning | [Déployer](../machine-learning/how-to-deploy-custom-container.md) un modèle ou [effectuer son apprentissage](../machine-learning/how-to-train-with-custom-image.md) dans un espace de travail Machine Learning à l’aide d’une image conteneur Docker personnalisée | Yes |
|Azure Container Registry | [Importer des images](container-registry-import-images.md) vers ou depuis un registre de conteneurs Azure avec des restrictions réseau | No |

> [!NOTE]
> Actuellement, l’activation du paramètre d’autorisation des services approuvés ne s’applique pas à certains autres services Azure gérés, y compris App Service et Azure Container Instances.

## <a name="allow-trusted-services---cli"></a>Autoriser les services approuvés – CLI

Par défaut, le paramètre Autoriser les services approuvés est activé dans un nouveau registre de conteneurs Azure. Désactivez ou activez le paramètre en exécutant la commande [az acr update](/cli/azure/acr#az_acr_update).

Pour désactiver :

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Pour activer le paramètre dans un registre existant ou dans un registre où il est déjà désactivé :

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Autoriser les services approuvés – Portail

Par défaut, le paramètre Autoriser les services approuvés est activé dans un nouveau registre de conteneurs Azure. 

Pour désactiver ou réactiver le paramètre dans le portail :

1. Dans le portail, accédez à votre registre de conteneurs.
1. Sous **Paramètres**, sélectionnez **Mise en réseau**. 
1. Dans **Autoriser l’accès au réseau public**, sélectionnez **Réseaux sélectionnés** ou **Désactivé**.
1. Effectuez l’une des opérations suivantes :
    * Pour désactiver l’accès des services approuvés, sous **Exception de pare-feu**, décochez **Autoriser les services Microsoft approuvés à accéder à ce registre de conteneurs**. 
    * Pour autoriser les services approuvés, sous **Exception de pare-feu**, cochez **Autoriser les services Microsoft approuvés à accéder à ce registre de conteneurs**.
1. Sélectionnez **Enregistrer**.

## <a name="trusted-services-workflow"></a>Workflow des services de confiance

Voici un workflow classique permettant à l’instance d’un service de confiance d’accéder à un registre de conteneurs ayant un accès réseau restreint. Ce flux de travail est nécessaire lorsque l’identité gérée d’une instance de service est utilisée pour contourner les règles de réseau du registre.

1. Activez une [identité managée affectée par le système pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) dans une instance de l’un des [services de confiance](#trusted-services) pour Azure Container Registry.
1. Attribuez à l’identité un [rôle Azure](container-registry-roles.md) dans votre registre. Par exemple, attribuez le rôle ACRPull pour tirer (pull) des images conteneur.
1. Dans le registre ayant un accès réseau restreint, configurez le paramètre pour autoriser l’accès des services de confiance.
1. Utilisez les informations d’identification de l’identité pour vous authentifier auprès du registre ayant un accès réseau restreint. 
1. Tirez (pull) des images du registre ou effectuer d’autres opérations autorisées par le rôle.

### <a name="example-acr-tasks"></a>Exemple : ACR Tasks

L’exemple suivant illustre l’utilisation d’ACR Tasks en tant que service de confiance. Pour plus d’informations sur les tâches, consultez [Authentification multiregistre dans une tâche ACR à l’aide d’une identité managée par Azure](container-registry-tasks-cross-registry-authentication.md).

1. Créez ou mettez à jour un conteneur de registre Azure.
[Créez](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) une tâche ACR. 
    * Activez une identité managée affectée par le système lors de la création de la tâche.
    * Désactivez le mode d’authentification par défaut (`--auth-mode None`) de la tâche.
1. Attribuez à l’identité de la tâche [un rôle Azure pour accéder au registre](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Par exemple, attribuez le rôle AcrPush, qui dispose des autorisations pour tirer (pull) et pousser (push) des images.
2. [Ajoutez les informations d’identification de l’identité managée pour le registre](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) à la tâche.
3. Pour confirmer que la tâche contourne les restrictions du réseau, [désactivez l’accès public](container-registry-access-selected-networks.md#disable-public-network-access) dans le registre.
4. Exécutez la tâche. Si le registre et la tâche sont correctement configurés, la tâche s’exécute correctement, car le registre autorise l’accès.

Pour tester la désactivation de l’accès par les services de confiance :

1. Désactivez le paramètre permettant l’accès par les services approuvés.
1. Réexécutez la tâche. Dans ce cas, l’exécution de la tâche échoue, car le registre n’autorise plus l’accès de la tâche.

## <a name="next-steps"></a>Étapes suivantes

* Pour restreindre l’accès à un registre à l’aide d’un point de terminaison privé dans un réseau virtuel, consultez [Configurer Azure Private Link pour un registre de conteneurs Azure](container-registry-private-link.md).
* Pour configurer des règles de pare-feu pour le registre, consultez [Configurer des règles de réseau IP public](container-registry-access-selected-networks.md).
