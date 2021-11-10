---
title: Exécuter le Détecteur d’anomalies sur IoT Edge
titleSuffix: Azure Cognitive Services
description: Déployez le module Détecteur d’anomalies sur IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: fa2904768023120fb2b08a52a23bb33e71e4d2f6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219145"
---
# <a name="deploy-an-anomaly-detector-univariate-module-to-iot-edge"></a>Déployer un module Détecteur d’anomalies univarié sur IoT Edge

Découvrez comment déployer le module Cognitive Services [Détecteur d’anomalies](../anomaly-detector-container-howto.md) sur un appareil IoT Edge. Une fois déployé sur IoT Edge, ce module s’exécute dans IoT Edge en tant qu’instance de conteneur, au même titre que d’autres modules. Il expose exactement les mêmes API qu’une instance de conteneur Détecteur d’anomalies s’exécutant dans un environnement de conteneur Docker standard. 

## <a name="prerequisites"></a>Prérequis

* Utilisez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.
* Installez [Azure CLI](/cli/azure/install-azure-cli).
* Un [hub IoT](../../../iot-hub/iot-hub-create-through-portal.md) et un appareil [IOT Edge](../../../iot-edge/quickstart-linux.md).

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Déployer le module Détecteur d’anomalies sur l’appareil périphérique

1. Dans le portail Azure, entrez **détecteur d’anomalies sur IoT Edge** dans la zone de recherche et ouvrez le résultat de la Place de marché Azure.
2. Vous arrivez alors à la page [Appareils cibles pour le module IoT Edge](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector) du portail Azure. Entrez les informations obligatoires suivantes.

    1. Sélectionnez votre abonnement.

    1. Sélectionnez votre hub IoT.

    1. Sélectionnez **Rechercher un appareil** et recherchez un appareil IoT Edge.

3. Cliquez sur le bouton **Créer**.

4. Sélectionnez le module **AnomalyDetectoronIoTEdge**.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Image de l’interface utilisateur des modules IoT Edge avec le lien AnomalyDetectoronIoTEdge mis en évidence dans un cadre rouge pour indiquer qu’il s’agit de l’élément à sélectionner.":::

5. Accédez à **Variables d’environnement** et indiquez les informations suivantes.

    1.  Conservez la valeur accept pour **Eula**.

    1. Pour **Billing**, entrez le point de terminaison de votre service Cognitive Services.

    1. Pour **ApiKey**, indiquez la clé API de votre service Cognitive Services.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Variables d’environnement avec des cadres rouges autour des zones qui nécessitent l’entrée de valeurs pour le point de terminaison et la clé API":::

6. Sélectionnez **Update** (Mettre à jour).

7. Sélectionnez **Suivant : Routes** pour définir votre route. Ici, vous spécifiez que tous les messages en provenance de tous les modules sont acheminés à Azure IoT Hub. Pour savoir comment déclarer un itinéraire, consultez [Établir des itinéraires dans IOT Edge](../../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true).

8. Sélectionnez **Suivant : Vérifier + créer**. Ici, vous pouvez afficher un aperçu du fichier JSON qui définit tous les modules déployés sur votre appareil IoT Edge.
    
9. Sélectionnez **Créer** pour commencer le déploiement des modules.

10. Une fois le déploiement du module terminé, vous revenez à la page IoT Edge de votre hub IoT. Sélectionnez votre appareil dans la liste des appareils IoT Edge pour voir ses informations détaillées.

11. Faites défiler vers le bas et examinez les modules listés. Vérifiez que l’état du runtime est en cours d’exécution pour votre nouveau module. 

Pour résoudre les problèmes liés à l’état du runtime de votre appareil IoT Edge, consultez le [guide de résolution des problèmes](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Tester le Détecteur d’anomalies sur un appareil IoT Edge

Vous allez effectuer un appel HTTP à l’appareil Azure IoT Edge sur lequel le conteneur Azure Cognitive Services est en cours d’exécution. Le conteneur fournit des API de point de terminaison basées sur REST. Utilisez l’hôte `http://<your-edge-device-ipaddress>:5000` pour les API de module.

Vous pouvez également [créer un client de module à l’aide de la bibliothèque de client Détecteur d’anomalies](../quickstarts/client-libraries.md?tabs=linux&pivots=programming-language-python) sur l’appareil Azure IoT Edge, puis appeler le conteneur Azure Cognitive Services en cours d’exécution en périphérie. Utilisez le point de terminaison de l’hôte `http://<your-edge-device-ipaddress>:5000` et laissez la clé d’hôte vide. 

Si votre appareil périphérique n’autorise pas encore les communications entrantes sur le port 5000, vous devez créer une **règle de port d’entrée**. 

Pour une machine virtuelle Azure, vous pouvez la définir sous **Machine virtuelle** > **Paramètres** > **Réseau** > **Règle de port d’entrée** > **Ajouter une règle de port d’entrée**.

Il existe plusieurs façons de confirmer que le module s’exécute. Recherchez l’*adresse IP externe* et le port exposé de l’appareil périphérique en question, puis ouvrez le navigateur web de votre choix. Utilisez les différentes URL de requête ci-dessous pour vérifier que le conteneur est en cours d’exécution. Les exemples d’URL de requête listés ci-dessous sont `http://<your-edge-device-ipaddress:5000`, mais votre conteneur spécifique peut varier. N’oubliez pas que vous devez utiliser l’*IP externe* de votre appareil périphérique.

| URL de la demande | Objectif |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Le conteneur fournit une page d’hébergement. |
| `http://<your-edge-device-ipaddress>:5000/status` | Cette demande, qui utilise également GET, permet de vérifier si la clé API servant à démarrer le conteneur est valide sans provoquer de requête de point de terminaison. Cette requête peut être utilisée pour les [probes liveness et readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) de Kubernetes. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Le conteneur fournit un ensemble complet de documentation pour les points de terminaison et une fonctionnalité **Essayer**. Avec cette fonctionnalité, vous pouvez entrer vos paramètres dans un formulaire HTML basé sur le web, et constituer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer les en-têtes HTTP, et le format du corps qui est nécessaire. |

![Page d’accueil du conteneur](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Installer et exécuter des conteneurs](../anomaly-detector-container-configuration.md) pour savoir comment tirer (pull) l’image conteneur et exécuter le conteneur.
* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](../anomaly-detector-container-configuration.md).
* [En savoir plus sur le service API Détecteur d’anomalies](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
