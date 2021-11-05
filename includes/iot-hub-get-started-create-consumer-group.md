---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: 090a7ae8e053b82e157d1e46e740d9b3911fef99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077582"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub

Les [Groupes de consommateurs](../articles/event-hubs/event-hubs-features.md#event-consumers) fournissent des vues indépendantes dans le flux d’événements qui permettent aux applications et services Azure de consommer indépendamment des données à partir du même point de terminaison Event Hub. Dans cette section, vous ajoutez un groupe de consommateurs à votre point de terminaison intégré utilisé ultérieurement dans ce didacticiel pour extraire les données du point de terminaison.

Pour ajouter un groupe de consommateurs à votre instance IoT Hub, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez votre instance IoT Hub.

1. Dans le volet gauche, sélectionnez **Points de terminaison intégrés**. Entrez un nom pour votre nouveau groupe de consommateurs dans la zone de texte sous **Groupes de consommateurs**.

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="Capture d’écran montrant comment créer un groupe de consommateurs dans votre hub IoT." border="true":::

1. Cliquez n’importe où en dehors de la zone de texte pour enregistrer le groupe de consommateurs.