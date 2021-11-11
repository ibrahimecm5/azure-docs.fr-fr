---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aa4ddb316efa3ea4c1bb12d4c6d7b541628bb0e
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576758"
---
### <a name="device-registration"></a>Enregistrement de l’appareil

Vous avez le choix d’utiliser le **portail Azure**, **Visual Studio Code** ou **Azure CLI** pour inscrire votre appareil. Chaque utilitaire possède sa propre configuration requise :

# <a name="portal"></a>[Portail](#tab/azure-portal)

Un [hub IoT](../articles/iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Un [hub IoT](../articles/iot-hub/iot-hub-create-through-portal.md) gratuit ou standard dans votre abonnement Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pour Visual Studio Code.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Un [hub IoT](../articles/iot-hub/iot-hub-create-using-cli.md) gratuit ou standard dans votre abonnement Azure.
* [Azure CLI](/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.70 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack.

---
