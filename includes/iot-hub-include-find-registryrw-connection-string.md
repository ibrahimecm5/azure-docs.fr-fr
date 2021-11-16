---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: ce13f1cdd3effdd08bfa76a996587edc213c5701
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288443"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Pour obtenir la chaîne de connexion IoT Hub pour la stratégie **registryReadWrite**, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources**. Sélectionnez le groupe de ressources dans lequel se trouve votre hub, puis sélectionnez votre hub dans la liste des ressources.

2. Dans le volet de gauche de votre hub, sélectionnez **Stratégies d’accès partagé**.

3. Dans la liste des stratégies, sélectionnez la stratégie **registryReadWrite**.

4. Sous **Clés d’accès partagé**, sélectionnez l’icône de copie pour la **Chaîne de connexion principale** et enregistrez la valeur.

    :::image type="content" source="./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png" alt-text="Capture d’écran qui montre comment récupérer la chaîne de connexion" border="true":::

Pour plus d’informations sur les autorisations et les stratégies d’accès partagé IoT Hub, consultez [Contrôle d’accès et autorisations](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
