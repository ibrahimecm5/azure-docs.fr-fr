---
title: Scalabilité et haute disponibilité d’Azure IoT Central | Microsoft Docs
description: Cet article décrit le processus par lequel IoT Central se met automatiquement à l’échelle pour gérer davantage d’appareils et sa haute disponibilité.
author: dominicbetts
ms.author: dobett
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bea4e5d7dcb4349f17a4a967d8e0ef15e6f737b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097681"
---
# <a name="scalability-and-high-availability"></a>Scalabilité et haute disponibilité

Les applications IoT Central utilisent en interne plusieurs services Azure tels qu’IoT Hub et Service IoT Hub Device Provisioning (DPS). Nombre de ces services sous-jacents sont multilocataires. Toutefois, pour garantir l’isolation complète des données des clients, IoT Central utilise des hubs IoT monolocataires. IoT Central gère automatiquement plusieurs instances de ses services sous-jacents pour mettre à l’échelle vos applications IoT Central et les rendre hautement disponibles.

IoT Central met automatiquement à l’échelle ses hubs IoT en fonction des profils de charge de votre application. IoT Central peut effectuer un scale-up des hubs IoT individuels et un scale-out du nombre de hubs IoT. Pour une connectivité d’appareil hautement disponible, chaque instance IoT Central a toujours au moins deux hubs IoT. Bien qu’IoT Central gère ses hubs IoT pour vous, le fait de disposer de plusieurs hubs IoT a un impact sur l’implémentation du microprogramme de votre appareil.

Les hubs IoT d’une application IoT Central se trouvent tous dans la même région Azure. C’est la raison pour laquelle l’architecture à plusieurs hubs IoT assure une connectivité d’appareil hautement disponible en cas de panne isolée. Si une région Azure entière devient indisponible, les procédures de récupération d’urgence basculent toutes les applications IoT Central vers une autre région.

## <a name="device-provisioning"></a>Approvisionnement des appareils

Avant qu’un appareil se connecte à IoT Central, il doit être inscrit et approvisionné dans les services sous-jacents. Lorsque vous ajoutez un appareil à une application IoT Central, IoT Central ajoute une entrée à un groupe d’inscription DPS. Les informations du groupe d’inscription, telles que l’étendue de l’ID, l’identité d’appareil et les clés, apparaissent dans l’interface utilisateur d’IoT Central.

Quand un appareil se connecte pour la première fois à votre application IoT Central, DPS approvisionne l’appareil dans l’un des hubs IoT liés du groupe d’inscriptions. DPS utilise une stratégie d’allocation pour équilibrer la charge de l’approvisionnement entre les hubs IoT de l’application. Ce processus garantit que chaque hub IoT possède un nombre similaire d’appareils approvisionnés.

Pour en savoir plus sur l’inscription et l’approvisionnement dans IoT Central, consultez [Se connecter à Azure IoT Central](concepts-get-connected.md).

## <a name="device-connections"></a>Connexions d’appareils

Une fois que DPS a approvisionné un appareil sur un hub IoT, l’appareil tente toujours de se connecter à ce hub. Si un appareil ne peut pas atteindre le hub IoT sur lequel il est approvisionné, il ne peut pas se connecter à votre application IoT Central. Pour gérer ce scénario, le microprogramme de votre appareil doit inclure une stratégie de nouvelle tentative.

Pour en savoir plus sur la façon dont le microprogramme de l’appareil doit gérer les erreurs de connexion et se connecter à un autre hub, consultez [Meilleures pratiques relatives au développement d’appareils](concepts-best-practices.md).

Pour en savoir plus sur la façon de vérifier que le microprogramme de votre appareil peut gérer les échecs de connexion, consultez [Tester les capacités de basculement](concepts-best-practices.md#test-failover-capabilities).

## <a name="data-export"></a>Exportation de données

Les applications IoT Central utilisent souvent d’autres services configurés par l’utilisateur. Par exemple, vous pouvez configurer votre application IoT Central pour exporter en continu des données vers des services tels qu’Azure Event Hubs et Stockage Blob Azure.

Si une exportation de données configurée ne peut pas écrire dans sa destination, IoT Central tente de retransmettre les données pendant 15 minutes maximum, après quoi IoT Central marque la destination comme étant en échec. Les destinations en échec sont régulièrement vérifiées pour savoir si elles sont accessibles en écriture.

Vous pouvez forcer IoT Central à redémarrer les exportations ayant échoué en désactivant et en réactivant l’exportation des données.

Consultez les meilleures pratiques en matière de haute disponibilité et de scalabilité pour le service de destination des exportations de données que vous utilisez :

- Stockage Blob Azure : [Redondance de Stockage Azure](../../storage/common/storage-redundancy.md) et [Liste de contrôle du niveau de performance et de la scalabilité de Stockage Blob](../../storage/blobs/storage-performance-checklist.md)
- Azure Event Hubs : [Disponibilité et cohérence dans Event Hubs](../../event-hubs/event-hubs-availability-and-consistency.md) et [Mise à l’échelle avec Event Hubs](../../event-hubs/event-hubs-scalability.md)
- Azure Service Bus : [Meilleures pratiques pour protéger les applications contre les pannes de Service Bus et les sinistres](../../service-bus-messaging/service-bus-outages-disasters.md) et [Mettre à jour automatiquement les unités de messagerie d’un espace de noms Azure Service Bus](../../service-bus-messaging/automate-update-messaging-units.md)

## <a name="limitations"></a>Limitations

Actuellement, il existe quelques applications IoT Central héritées créées avant avril 2021 qui n’ont pas encore été migrées vers l’architecture à plusieurs hubs IoT. Utilisez la commande `az iot central device manual-failover` pour vérifier si votre application utilise toujours un seul hub IoT.

Actuellement, les appareils IoT Edge ne peuvent pas passer d’un hub IoT à l’autre.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert la scalabilité et la haute disponibilité d’Azure IoT Central, la prochaine étape consiste à découvrir la [connectivité des appareils](concepts-get-connected.md) dans Azure IoT Central.
