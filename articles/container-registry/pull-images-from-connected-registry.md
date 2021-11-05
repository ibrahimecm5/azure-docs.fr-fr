---
title: Extraire des images à partir d’un registre connecté
description: Utilisez des commandes de l’interface de ligne de commande Azure Container Registry pour configurer un jeton client et extraire des images à partir d’un registre connecté sur un appareil IoT Edge.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5409afea3ab1ab1ebedaed87adbafd2b15fe9418
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028866"
---
# <a name="pull-images-from-a-connected-registry-on-iot-edge-device"></a>Extraire des images d’un registre connecté sur un appareil IoT Edge

Pour extraire des images d’un [registre connecté](intro-connected-registry.md), configurez un [jeton client](overview-connected-registry-access.md#client-tokens) et transmettez les informations d’identification du jeton pour accéder au contenu du registre.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
* Ressource de registre connecté dans Azure. Pour les étapes de déploiement, consultez [Démarrage rapide : Créer un registre connecté à l’aide d’Azure CLI][quickstart-connected-registry-cli].
* Instance de registre connecté déployée sur un appareil IoT Edge. Pour les étapes de déploiement, consultez [Démarrage rapide : déployer un registre connecté sur un appareil IoT Edge](quickstart-deploy-connected-registry-iot-edge-cli.md) ou [Didacticiel : Déployer un registre connecté sur des appareils IoT Edge imbriqués](tutorial-deploy-connected-registry-nested-iot-edge-cli.md). Dans les commandes décrites dans cet article, le nom du registre connecté est stocké dans la variable d’environnement *$CONNECTED _REGISTRY_RW*.

## <a name="create-a-scope-map"></a>Créer un mappage d’étendue

Utilisez la commande [az acr scope-map create][az-acr-scope-map-create] afin de créer un mappage d’étendue pour l’accès en lecture au référentiel `hello-world` :

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr scope-map create \
  --name hello-world-scopemap \
  --registry $REGISTRY_NAME \
  --repository hello-world content/read \
  --description "Scope map for the connected registry."
```

## <a name="create-a-client-token"></a>Créer un jeton client

Utilisez la commande [az acr token create][az-acr-token-create] pour créer un jeton client et l’associer au mappage d’étendue nouvellement créé :

```azurecli
az acr token create \
  --name myconnectedregistry-client-token \
  --registry $REGISTRY_NAME \
  --scope-map hello-world-scopemap
```

La commande retourne des détails sur le jeton nouvellement généré, notamment les mots de passe.

  > [!IMPORTANT]
  > Veillez à enregistrer les mots de passe générés. Il s’agit de mots de passe à usage unique qui ne peuvent pas être récupérés. Vous pouvez générer de nouveaux mots de passe à l’aide de la commande [az acr token credential generate][az-acr-token-credential-generate].

## <a name="update-the-connected-registry-with-the-client-token"></a>Mettre à jour le registre connecté avec le jeton client

Utilisez la commande [az acr connected-registry update][az-acr-connected-registry-update] pour mettre à jour le registre connecté avec le jeton client nouvellement créé. 

```azurecli
az acr connected-registry update \
  --name $CONNECTED_REGISTRY_RW \
  --registry $REGISTRY_NAME \
  --add-client-token myconnectedregistry-client-token
```

## <a name="pull-an-image-from-the-connected-registry"></a>Extraire une image du registre connecté

Sur un ordinateur ayant accès à l’appareil IoT Edge, utilisez l’exemple de commande suivant pour vous connecter au registre connecté en utilisant les informations d’identification du jeton client. Pour connaître les meilleures pratiques de gestion des informations d'identification, consultez la référence de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/).

> [!CAUTION]
> Si vous configurez votre registre connecté en tant que registre non sécurisé, mettez à jour la liste des registres non sécurisés dans la configuration du démon Docker afin d’inclure l’adresse IP (ou le nom de domaine complet) et le port de votre registre connecté sur l’appareil IoT Edge. Cette configuration ne doit être utilisée qu’à des fins de test. Pour plus d’informations, consultez [Tester un registre non sécurisé](https://docs.docker.com/registry/insecure/).

```
docker login --username myconnectedregistry-client-token \
  --password <token_password> <IP_address_or_FQDN_of_connected_registry>:<port>
```

Pour les scénarios de IoT Edge, veillez à inclure le port utilisé pour accéder au registre connecté sur l’appareil. Exemple :

```
docker login --username myconnectedregistry-client-token \
  --password xxxxxxxxxxx 192.0.2.13:8000
```

Ensuite, utilisez la commande suivante pour extraire l’image `hello-world` :

```
docker pull <IP_address_or_FQDN_of_connected_registry>:<port>/hello-world
```

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur les [jetons délimités par le référentiel](container-registry-repository-scoped-permissions.md).
* Apprenez-en davantage sur l’[accès à un registre connecté](overview-connected-registry-access.md).

<!-- LINKS - internal -->
[az-acr-scope-map-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential#az_acr_token_credential_generate
[az-acr-connected-registry-update]: /cli/azure/acr/connect-registry#az_acr_connected_registry_update] 
[container-registry-intro]: container-registry-intro.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
