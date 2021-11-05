---
title: Comprendre l’accès à un registre connecté
description: Présentation de l’authentification basée sur un jeton et de l’autorisation pour les registres connectés dans Azure Container Registry
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a98772cced7b5f7e72c66d134e9ff652f64b4086
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096629"
---
# <a name="understand-access-to-a-connected-registry"></a>Comprendre l’accès à un registre connecté

Pour accéder à un [registre connecté](intro-connected-registry.md)et le gérer, seule l’[authentification basée sur un jeton](container-registry-repository-scoped-permissions.md) ACR est prise en charge pour le moment. Comme indiqué dans l’image suivante, deux types de jeton sont utilisés par chaque registre connecté :

* [**Jetons clients**](#client-tokens) : un ou plusieurs jetons que les clients locaux utilisent pour s’authentifier sur un registre connecté, et pour y pousser ou tirer des images et des artefacts.
* [**Jeton de synchronisation**](#sync-token) : jeton utilisé par chaque registre connecté pour accéder à son parent et synchroniser le contenu.

![Vue d’ensemble de l’authentification du registre connecté](media/overview-connected-registry-access/connected-registry-authentication-overview.svg)

> [!IMPORTANT]
> Stockez les mots de passe de jeton de chaque registre connecté en lieu sûr. Une fois créés, les mots de passe de jeton ne peuvent pas être récupérés. Vous pouvez regénérer les mots de passe de jeton à tout moment.

## <a name="client-tokens"></a>Jetons clients

Pour gérer l’accès client à un registre connecté, vous créez des jetons limités à des actions sur un ou plusieurs dépôts. Après avoir créé un jeton, configurez le registre connecté pour accepter le jeton en utilisant la commande [az acr connected-registry update](/cli/azure/acr/connected-registry#az_acr_connected_registry_update). Un client peut ensuite utiliser les informations d’identification du jeton pour accéder au point de terminaison d’un registre connecté, par exemple, afin d’utiliser les commandes Docker CLI pour tirer ou pousser des images dans le registre connecté.

Vos options de configuration des actions de jeton client varient selon que le registre connecté autorise à la fois les opérations de tirage ou de poussage, ou les fonctions sous forme de miroir de tirage uniquement. 
* Un registre connecté en [mode ReadWrite](intro-connected-registry.md#modes) par défaut autorise à la fois les opérations de tirage et de poussage. Vous pouvez donc créer un jeton qui autorise des actions pour *lire* et *écrire* le contenu du dépôt dans ce registre. 
* Pour un registre connecté en [mode ReadOnly](intro-connected-registry.md#modes), les jetons clients peuvent uniquement autoriser des actions pour *lire* le contenu du dépôt.

### <a name="manage-client-tokens"></a>Gérer les jetons clients

Mettez à jour les jetons clients, les mots de passe ou les mappages d’étendue en fonction de vos besoins avec les commandes [az acr token](/cli/az/acr#az_acr_token) et [az acr scope-map](/cli/az/acr#az_acr_scope-map). Les mises à jour de jeton client sont propagées automatiquement aux registres connectés qui acceptent le jeton.

## <a name="sync-token"></a>Jeton de synchronisation

Chaque registre connecté utilise un jeton de synchronisation pour s’authentifier sur son parent immédiat, qui peut être un autre registre connecté ou le registre cloud. Le registre connecté utilise automatiquement ce jeton pendant la synchronisation du contenu avec le parent ou l’exécution d’autres mises à jour. 

* Le jeton de synchronisation et les mots de passe sont générés automatiquement quand vous créez la ressource de registre connecté. Exécutez la commande [az acr connected-registry install renew-credentials][az-acr-connected-registry-install-renew-credentials] pour regénérer les mots de passe.
* Ajoutez les informations d’identification du jeton de synchronisation dans la configuration utilisée pour déployer le registre connecté local. 
* Par défaut, le jeton de synchronisation est autorisé à synchroniser les dépôts sélectionnés avec son parent. Vous devez fournir un jeton de synchronisation existant, ou un ou plusieurs dépôts à synchroniser quand vous créez la ressource de registre connecté.
* Il a également l’autorisation de lire et écrire des messages de synchronisation sur une passerelle utilisée pour communiquer avec le parent du registre connecté. Ces messages contrôlent la planification de la synchronisation et gèrent les autres mises à jour entre le registre connecté et son parent.

### <a name="manage-sync-token"></a>Gérer le jeton de synchronisation

Mettez à jour les jetons de synchronisation, les mots de passe ou les mappages d’étendue en fonction de vos besoins avec les commandes [az acr token](/cli/az/acr#az_acr_token) et [az acr scope-map](/cli/az/acr#az_acr_scope-map). Les mises à jour des jetons de synchronisation sont propagées automatiquement sur le registre connecté. Suivez les pratiques standard de permutation des mots de passe pendant la mise à jour du jeton de synchronisation.

> [!NOTE]
> Le jeton de synchronisation ne peut pas être supprimé tant que le registre connecté qui lui est associé n’a pas été supprimé. Vous pouvez désactiver un registre connecté en définissant l’état du jeton de synchronisation sur `disabled`. 

## <a name="registry-endpoints"></a>Points de terminaison de registre

Les informations d’identification de jeton pour les registres connectés sont limitées pour accéder à des points de terminaison de registre spécifiques :

* Un jeton client accède au point de terminaison du registre connecté. Le point de terminaison du registre connecté est l’URI du serveur de connexion, qui est généralement l’adresse IP du serveur ou de l’appareil qui l’héberge.

* Un jeton de synchronisation accède au point de terminaison du registre parent, qui est un autre point de terminaison de registre connecté ou le registre cloud lui-même. Quand le jeton de synchronisation est limité pour accéder au registre cloud, il doit accéder à deux points de terminaison de registre :

    - Le nom complet du serveur de connexion, par exemple, `contoso.azurecr.io`. Ce point de terminaison est utilisé pour l’authentification.
    - Un [point de terminaison de données](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) régional complet pour le registre cloud, par exemple, `contoso.westus2.data.azurecr.io`. Ce point de terminaison est utilisé pour échanger des messages avec le registre connecté à des fins de synchronisation. 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour en savoir plus sur les scénarios spécifiques où vous pouvez utiliser le registre connecté.

> [!div class="nextstepaction"]
> [Vue d’ensemble : registre connecté et IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[az-acr-connected-registry-update]: /cli/azure/acr/connected-registry#az_acr_connected_registry_update
[az-acr-connected-registry-install-renew-credentials]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_install_renew_credentials
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
