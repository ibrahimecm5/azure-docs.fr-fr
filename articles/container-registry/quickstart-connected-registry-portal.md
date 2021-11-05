---
title: 'Démarrage rapide : Créer un registre connecté à l’aide du portail'
description: Utilisez le portail Azure pour créer une ressource de registre de conteneurs Azure connectée qui peut synchroniser des images et d’autres artefacts avec le registre cloud.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6181f0fbdea8c03741f28dc9023a6761971cf2e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028971"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-portal"></a>Démarrage rapide : Créer un registre connecté à l’aide du portail Azure

Dans ce guide de démarrage rapide, vous allez utiliser le portail Azure pour créer une ressource de [registre connecté](intro-connected-registry.md) dans Azure. La fonctionnalité de registre connecté d’Azure Container Registry vous permet de déployer un registre à distance ou localement, et de synchroniser des images et d’autres artefacts avec le registre cloud. 

Ici, vous créez deux ressources de registre connecté pour un registre cloud : un registre connecté autorise les fonctionnalités en lecture et écriture (pull et push d’artefacts) et l’autre autorise les fonctionnalités en lecture seule. 

Après avoir créé un registre connecté, vous pouvez suivre d’autres guides pour le déployer et l’utiliser sur votre infrastructure locale ou distante.

## <a name="prerequisites"></a>Prérequis

* Registre de conteneurs Azure : si vous n’avez pas encore de registre de conteneurs, [créez-en un](container-registry-get-started-portal.md) (niveau Premium requis) dans une [région](intro-connected-registry.md#available-regions) qui prend en charge les registres connectés. 

Pour importer des images dans le registre de conteneurs, utilisez Azure CLI : [!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Activer le point de terminaison de données dédié pour le registre cloud

Activez le [point de terminaison de données dédié](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) pour le registre de conteneurs Azure dans le cloud. Cette étape est nécessaire pour qu’un registre connecté communique avec le registre cloud.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre registre de conteneurs.
1. Sélectionnez **Mise en réseau > Accès public**.
Activez la case à cocher **Activer le point de terminaison de données dédié**.
1. Sélectionnez **Enregistrer**.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Créer une ressource de registre connecté pour les fonctionnalités en lecture et écriture

Les étapes suivantes créent un registre connecté en [mode ReadWrite](intro-connected-registry.md#modes) lié au registre cloud.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre registre de conteneurs.
1. Sélectionnez **Registres connectés (préversion) > + Créer**.
1. Entrez ou sélectionnez les valeurs dans le tableau suivant, puis sélectionnez **Enregistrer**.


|Élément  |Description  |
|---------|---------|
|Parent     | Sélectionnez **Aucun parent** pour un registre connecté lié au registre cloud.        |
|Mode     | Sélectionnez **ReadWrite**.         |
|Nom     | Le nom du registre connecté doit commencer par une lettre et contenir uniquement des caractères alphanumériques. Il doit comprendre de 5 à 40 caractères, et être unique dans la hiérarchie de ce registre de conteneurs Azure.       |
|Propriétés de journalisation     | Acceptez les paramètres par défaut.       |
|Propriétés de synchronisation    | Acceptez les paramètres par défaut. Étant donné qu’aucune planification de synchronisation n’est définie par défaut, les référentiels sont synchronisés sans interruption entre le registre cloud et le registre connecté.      |
|Référentiels     | Sélectionnez ou entrez les noms des référentiels que vous avez importés à l’étape précédente. Les référentiels spécifiés seront synchronisés entre le registre cloud et le registre connecté une fois celui-ci déployé.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readwrite-connected-registry.png" alt-text="Créer un registre connecté en mode ReadWrite":::


> [!IMPORTANT]
> Pour prendre en charge des scénarios imbriqués où des couches inférieures n’ont pas accès à Internet, vous devez toujours autoriser la synchronisation du référentiel `acr/connected-registry`. Ce référentiel contient l’image du runtime du registre connecté.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Créer une ressource de registre connecté pour les fonctionnalités en lecture seule

Les étapes suivantes créent un registre connecté en [mode ReadOnly](intro-connected-registry.md#modes) dont le parent est le registre connecté que vous avez créé dans la section précédente. Une fois déployé, ce registre connecté active les fonctionnalités en lecture seule (pull d’artefacts).

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre registre de conteneurs.
1. Sélectionnez **Registres connectés (préversion) > + Créer**.
1. Entrez ou sélectionnez les valeurs dans le tableau suivant, puis sélectionnez **Enregistrer**.


|Élément  |Description  |
|---------|---------|
|Parent     | Sélectionnez le registre connecté que vous avez créé précédemment.        |
|Mode     | Sélectionnez **ReadOnly**.         |
|Nom     | Le nom du registre connecté doit commencer par une lettre et contenir uniquement des caractères alphanumériques. Il doit comprendre de 5 à 40 caractères, et être unique dans la hiérarchie de ce registre de conteneurs Azure.      |
|Propriétés de journalisation     | Acceptez les paramètres par défaut.       |
|Propriétés de synchronisation    | Acceptez les paramètres par défaut. Étant donné qu’aucune planification de synchronisation n’est définie par défaut, les référentiels sont synchronisés sans interruption entre le registre cloud et le registre connecté.      |
|Référentiels     | Sélectionnez ou entrez les noms des référentiels que vous avez importés à l’étape précédente. Les référentiels spécifiés seront synchronisés entre le registre parent et le registre connecté une fois celui-ci déployé.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readonly-connected-registry.png" alt-text="Créer un registre connecté en mode ReadOnly":::

## <a name="view-connected-registry-properties"></a>Afficher les propriétés du registre connecté

Sélectionnez un registre connecté dans le portail pour afficher ses propriétés, telles que son état de connexion (Hors connexion, En ligne ou Non sain) et sont état d’activation (déployée localement). Dans l’exemple suivant, le registre connecté n’est pas déployé. Son état de connexion « Hors connexion » indique qu’il est actuellement déconnecté du cloud.

:::image type="content" source="media/quickstart-connected-registry-portal/connected-registry-properties.png" alt-text="Afficher les propriétés du registre connecté":::

À partir de cet affichage, vous pouvez également générer une chaîne de connexion, voire des mots de passe pour le [jeton de synchronisation](overview-connected-registry-access.md#sync-token). Une chaîne de connexion contient des paramètres de configuration utilisés pour le déploiement d’un registre connecté et la synchronisation du contenu avec un registre parent.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé le portail Azure pour créer deux ressources de registre connecté dans Azure. Ces nouvelles ressources de registre connecté sont liées à votre registre cloud et permettent la synchronisation d’artefacts avec le registre cloud.

Passez aux guides de déploiement de registre connecté pour savoir comment déployer et utiliser un registre connecté sur votre infrastructure de IoT Edge.

> [!div class="nextstepaction"]
> [Démarrage rapide : Déployer un registre connecté sur IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
