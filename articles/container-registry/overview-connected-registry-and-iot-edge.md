---
title: Utilisation du registre connecté avec Azure IoT Edge
description: Vue d’ensemble du registre de conteneurs Azure connecté dans les scénarios IoT Edge hiérarchiques
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 08/24/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f5be0a18c2185dffa685291121c8a730d60ef52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096618"
---
# <a name="using-connected-registry-with-azure-iot-edge"></a>Utilisation du registre connecté avec Azure IoT Edge

Dans cet article, vous découvrez comment utiliser un [registre connecté](intro-connected-registry.md) Azure dans des scénarios [IoT Edge](../iot-edge/about-iot-edge.md) hiérarchiques. Le registre de conteneurs connecté peut être déployé comme module IoT Edge et jouer un rôle essentiel dans le traitement des images conteneur demandées par les appareils de la hiérarchie.

## <a name="what-is-a-hierarchical-iot-edge-deployment"></a>Qu’est-ce qu’un déploiement IoT Edge hiérarchique ?

Azure IoT Edge vous permet de déployer des appareils IoT Edge sur des réseaux organisés en couches hiérarchiques. Chaque couche d’une hiérarchie est un [appareil de passerelle](../iot-edge/iot-edge-as-gateway.md) qui gère les messages et les demandes des appareils situés dans la couche du dessous. Vous pouvez structurer une hiérarchie d’appareils de sorte que seule la couche supérieure soit connectée au cloud, et que les couches inférieures puissent uniquement communiquer avec les couches Nord et Sud adjacentes. Cette structure en couches réseau est la base de la plupart des réseaux industriels, qui respectent la [norme ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Pour savoir comment créer une hiérarchie d’appareils IoT Edge, consultez [Tutoriel : Créer une hiérarchie d’appareils IoT Edge][tutorial-nested-iot-edge]

## <a name="how-do-i-use-connected-registry-in-hierarchical-iot-edge-scenarios"></a>Comment utiliser un registre connecté dans des scénarios IoT Edge hiérarchiques ?

L’illustration suivante montre comment le registre connecté peut être utilisé pour prendre en charge le déploiement hiérarchique d’IoT Edge. Les lignes grises continues montrent le flux réel du réseau, tandis que les lignes en pointillés montrent la communication logique entre les composants et les registres connectés.

![Registre connecté et déploiements IoT Edge hiérarchiques](media/overview-connected-registry-and-iot-edge/connected-registry-iot-edge-overview.svg)

### <a name="top-layer"></a>Couche supérieure

La couche supérieure de l’exemple d’architecture, *Layer 5: Enterprise Network*, est gérée par le service informatique et peut accéder au registre de conteneurs pour Contoso dans le cloud Azure. Le registre connecté est déployé comme un module IoT Edge sur la machine virtuelle IoT Edge, et peut communiquer directement avec le registre cloud pour tirer et pousser des images et des artefacts. 

Le registre connecté fonctionne en [mode ReadWrite](intro-connected-registry.md#modes) par défaut. Les clients de ce registre connecté peuvent y tirer et pousser des images et des artefacts. Les images poussées sont synchronisées avec le registre cloud. Si les poussages ne sont pas nécessaires dans cette couche, le registre connecté peut être changé pour fonctionner en [mode ReadOnly](intro-connected-registry.md#modes).

Pour connaître les étapes de déploiement du registre connecté comme un module IoT Edge à ce niveau, consultez [Démarrage rapide : Déployer un registre connecté sur un appareil IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli].

### <a name="nested-layers"></a>Couches imbriquées

La couche inférieure suivante, *Layer 4: Site Business Planning and Logistics*, est configurée pour communiquer uniquement avec la couche 5. Ainsi, pendant le déploiement de la machine virtuelle IoT Edge sur la couche 4, elle doit à la place tirer les images de module du registre connecté sur la couche 5. 

Vous pouvez également déployer un registre connecté fonctionnant en mode ReadOnly pour traiter les couches du dessous. Cela est illustré par la machine virtuelle IoT Edge sur *Layer 3: Industrial Security Zone*. Cette machine virtuelle doit tirer des images de module du registre connecté sur *Layer 4*. Si des clients sur des couches inférieures doivent être servis, un registre connecté en mode ReadOnly peut être déployé sur la couche 3, et ainsi de suite.

Dans cette architecture, les registres connectés déployés sur chaque couche sont configurés pour synchroniser les images avec le registre connecté de la couche du dessus. Les registres connectés sont déployés comme des modules IoT Edge et tirent parti des mécanismes IoT Edge pour le déploiement et le routage réseau.

Pour connaître les étapes de déploiement du registre connecté sur des appareils IoT Edge imbriqués, consultez [Démarrage rapide : Déployer le registre connecté sur des appareils IoT Edge imbriqués][tutorial-deploy-connected-registry-nested-iot-edge-cli].

## <a name="next-steps"></a>Étapes suivantes

Dans cette vue d’ensemble, vous avez découvert comment utiliser le registre connecté dans des scénarios IoT Edge hiérarchiques. Consultez les articles suivants pour savoir comment configurer et déployer un registre connecté sur votre appareil IoT Edge.

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un registre connecté à l’aide de l’interface CLI][quickstart-connected-registry-cli]

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un registre connecté à l’aide du portail][quickstart-connected-registry-portal]

> [!div class="nextstepaction"]
> [Démarrage rapide : Déployer un registre connecté sur un appareil IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

> [!div class="nextstepaction"]
> [Tutoriel : Déployer un registre connecté sur des appareils IoT Edge imbriqués][tutorial-deploy-connected-registry-nested-iot-edge-cli]

<!-- LINKS - internal -->
[quickstart-connected-registry-cli]:quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]:quickstart-connected-registry-portal.md
[quickstart-deploy-connected-registry-iot-edge-cli]:quickstart-deploy-connected-registry-iot-edge-cli.md
[tutorial-nested-iot-edge]:../iot-edge/tutorial-nested-iot-edge.md
[tutorial-deploy-connected-registry-nested-iot-edge-cli]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
