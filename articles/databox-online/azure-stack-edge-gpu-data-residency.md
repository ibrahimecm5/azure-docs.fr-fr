---
title: Résidence et résilience des données pour Azure Stack Edge Pro GPU/Pro R/Mini R
description: Décrit la posture de résidence des données pour Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: alkohli
ms.openlocfilehash: 01f1a0ca013dad76510df99b70a462b96bec07a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078652"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge"></a>Résidence et résilience des données pour Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article fournit des informations qui vous aideront à mieux comprendre le comportement de la résilience et de la résidence des données pour le service Azure Stack Edge. Il vous explique aussi comment activer la résidence des données dans ce service.  

## <a name="about-data-residency-for-azure-stack-edge"></a>À propos de la résidence des données pour Azure Stack Edge 

Le service Azure Stack Edge utilise des [paires régionales Azure](../best-practices-availability-paired-regions.md#azure-regional-pairs) pour le stockage et le traitement des données client dans toutes les zones géographiques où il est disponible. Pour la région Asie Sud-Est (Singapour), le service est actuellement jumelé avec Hong Kong. Avec le jumelage des régions Azure, toutes les données stockées dans la région Singapour sont répliquées dans la région Hong Kong. Singapour applique certaines lois visant à interdire la sortie des données client de son territoire. 

Pour garantir que les données client résident dans une seule et unique région, une nouvelle option est disponible dans le service Azure Stack Edge. Quand cette option est activée, le service peut stocker et traiter les données client uniquement dans la région Singapour. Les données client ne sont pas répliquées vers la région Hong Kong. Certaines métadonnées propres au service (des données non sensibles) continuent d’être répliquées vers la région jumelée.  

Quand cette option est activée, le service est résilient aux pannes à l’échelle de la zone, mais pas aux pannes à l’échelle de la région. Si les pannes à l’échelle de la région ont un impact important pour vous, vous devez sélectionner la réplication par paire régionale.

L’option de résidence des données dans une seule région est disponible uniquement pour la région Asie Sud-Est (Singapour). Pour toutes les autres régions, Azure Stack Edge stocke et traite les données client dans la zone géographique spécifiée par le client.

La posture de résidence des données pour les services d’Azure Stack Edge peut être résumée pour les aspects suivants du service :

- Service de commande et de gestion Azure Stack Edge existant.
- Nouveau service Azure Edge Hardware Center (préversion) qui sera utilisé pour les nouvelles commandes à l’avenir.
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

Le service Azure Stack Edge s’intègre aussi aux services dépendants suivants, dont les comportements sont également résumés : 

- Kubernetes avec Azure Arc
- Azure IoT Hub et Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Ressource de commande et de gestion classique Azure Stack Edge 

Si vous utilisez l’expérience classique pour passer une commande pour Azure Stack Edge, le service utilise actuellement la paire régionale Azure pour implémenter la résilience des données aux pannes à l’échelle de la région. Pour les ressources Azure Stack Edge existantes stockées dans la région Singapour, les données sont répliquées vers Hong Kong.

Si vous créez une ressource Azure Stack Edge, vous avez la possibilité d’activer la résidence des données uniquement dans la région Singapour. Lorsque cette option est activée, les données ne sont pas répliquées vers Hong Kong. En cas de panne du service à l’échelle de la région, deux options s’offrent à vous :

- Attendre que la région Singapore soit restaurée.

- Créer une ressource dans une autre région, réinitialiser l’appareil, et gérer votre appareil en utilisant la nouvelle ressource. Pour obtenir des instructions détaillées, consultez [Réinitialiser et réactiver votre appareil Azure Stack Edge](azure-stack-edge-reset-reactivate-device.md).

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Ressource de commande et de gestion d’Azure Edge Hardware Center 

Le nouveau service Azure Edge Hardware Center (préversion) est désormais disponible. Il vous permet de créer et gérer des ressources Azure Stack Edge. Lorsque vous passez une commande dans la région Asie Sud-Est, vous pouvez sélectionner l’option qui autorise la résidence de vos données uniquement dans la région Singapour, empêchant ainsi leur réplication. 

En cas de pannes à l’échelle de la région, vous ne pourrez pas accéder aux ressources de votre commande. Vous ne serez pas en mesure de retourner, d’annuler ou de supprimer les ressources. Si vous demandez une actualisation de l’état de votre commande ou si vous devez retourner un appareil de manière urgente durant l’arrêt du service, le Support Microsoft gérera ces demandes.

Pour obtenir des instructions détaillées, consultez [Créer une commande via Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Services dépendants d’Azure Stack Edge

Les services Kubernetes avec Azure Arc, Azure IoT Hub et Azure IoT Edge ainsi qu’Azure Key Vault sont des services qui s’intègrent à Azure Stack Edge.

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes avec Azure Arc 

Kubernetes avec Azure Arc est disponible sous forme d’extension pour Azure Stack Edge. Pour la région Singapour (Asie Sud-Est), les données Azure Arc résident uniquement dans Singapour et ne sont pas répliquées vers Hong Kong. <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc-enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT est disponible sous forme de module complémentaire pour Azure Stack Edge. Pour Singapour (Asie Sud-Est), Azure IoT utilise une région jumelée et réplique les données vers Hong Kong. Azure IoT est donc résilient aux pannes à l’échelle de la région. 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur les [exigences de résidence des données Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).