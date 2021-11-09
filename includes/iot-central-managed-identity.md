---
title: Fichier include
description: Fichier Include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0a2e035cdc5118cc7d952e4046e9093f95094585
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070390"
---
Pour configurer l’identité managée qui permet à votre application IoT Central d’exporter des données en toute sécurité vers votre Event Hub :

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre application IoT Central.

    > [!TIP]
    > Par défaut, les applications IoT Central sont créées dans le groupe de ressources **IOTC** de votre abonnement.

1. Sélectionnez **Identité**. Ensuite, sur la page **Affectée par le système**, définissez l’état sur **Activé**, puis sélectionnez **Enregistrer**.

1. Après quelques secondes, l’identité managée affectée par le système pour votre application IoT Central est activée et vous pouvez sélectionner **Attributions de rôle Azure** :

    :::image type="content" source="media/iot-central-managed-identity/azure-role-assignments.png" alt-text="Capture d’écran de la page d’identité de l’application IoT Central dans le portail Azure.":::

1. Sur la page **Attributions de rôle Azure**, sélectionnez **+ Ajouter une attribution de rôle**.
