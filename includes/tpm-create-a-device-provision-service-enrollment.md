---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bc968f7763131c4c12e7d523619029698e4ac09
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579334"
---
## <a name="create-a-device-provisioning-service-enrollment"></a>Créer une inscription dans le service Device Provisioning

Utilisez les informations de provisionnement de votre TPM pour créer une inscription individuelle dans le service Device Provisioning.

Lorsque vous créez une inscription dans le service Device Provisioning, vous avez la possibilité de déclarer un **état initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des différentes métriques utilisées dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](../articles/iot-edge/how-to-deploy-at-scale.md).

> [!TIP]
> Les étapes décrites dans cet article concernent le portail Azure, mais vous pouvez également créer des inscriptions individuelles en utilisant Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle**, puis suivez cette procédure pour configurer l’inscription :

   1. Pour **Mécanisme**, sélectionnez **TPM**.

   1. Spécifiez la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription** que vous avez copiés sur votre machine virtuelle ou votre appareil physique.

   1. Fournissez un ID pour votre appareil si vous le souhaitez. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **True** pour déclarer que votre machine virtuelle ou votre appareil physique est un appareil IoT Edge.

   1. Choisissez le hub IoT lié auquel vous voulez connecter votre appareil, ou sélectionnez **Lier à un nouveau hub IoT**. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’attribution sélectionnée.

   1. Ajoutez une valeur d’étiquette à l’**état initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Pour plus d’informations, consultez [Déploiement de modules IoT Edge à grande échelle](../articles/iot-edge/how-to-deploy-at-scale.md).

   1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.
