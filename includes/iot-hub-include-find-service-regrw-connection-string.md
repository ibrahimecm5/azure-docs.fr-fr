---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9d5710e78b323af65a12b1f92095cdfa1f385ecb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288056"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Pour créer une stratégie d’accès partagé qui accorde des autorisations de **connexion de service** et d’**écriture du registre**, et obtenir une chaîne de connexion pour cette stratégie, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Groupes de ressources**. Sélectionnez le groupe de ressources dans lequel se trouve votre hub, puis sélectionnez votre hub dans la liste des ressources.

1. Dans le volet de gauche de votre hub, sélectionnez **Stratégies d’accès partagé**.

1. Dans le menu au-dessus de la liste des stratégies, sélectionnez **Ajouter une stratégie d'accès partagé**.

1. Sous **Ajouter une stratégie d’accès partagé**, entrez un nom descriptif pour votre stratégie, par exemple *serviceAndRegistryReadWrite*. Sous **Autorisations**, sélectionnez **Écriture du registre** et **Connexion du service**, puis sélectionnez **Ajouter**. (l’autorisation **Lecture du registre** est incluse automatiquement lorsque vous sélectionnez **Écriture du Registre**).

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png" alt-text="Capture d’écran qui montre comment ajouter une nouvelle stratégie d’accès partagé" border="true":::

1. Sélectionnez votre nouvelle stratégie dans la liste des stratégies.

1. Sous **Clés d’accès partagé**, sélectionnez l’icône de copie pour la **Chaîne de connexion principale** et enregistrez la valeur.

    :::image type="content" source="./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png" alt-text="Capture d’écran qui montre comment récupérer la chaîne de connexion" border="true":::

Pour plus d’informations sur les autorisations et les stratégies d’accès partagé IoT Hub, consultez [Contrôle d’accès et autorisations](../articles/iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
