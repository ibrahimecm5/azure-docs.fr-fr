---
title: Fichier include
description: Fichier include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 406893018da40f10f764f44a13d13bd420492519
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354102"
---
Pour créer un hub IoT à l’aide du portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la page d’accueil Azure, sélectionnez **+ Créer une ressource**, puis entrez *IoT Hub* dans **Rechercher dans la Place de marché**.

1. Sélectionnez **IoT Hub** dans les résultats de la recherche, puis **Créer**.

1. Dans l'onglet **De base**, renseignez les champs comme suit :

   - **Abonnement**: Sélectionnez l’abonnement à utiliser pour votre hub.

   - **Groupe de ressources** : Sélectionnez un groupe de ressources ou créez-en un. Pour créer un nouveau groupe, sélectionnez **Créer** et indiquez le nom que vous voulez utiliser. Pour utiliser un groupe de ressources existant, sélectionnez-le. Pour plus d’informations, consultez [Gérer des groupes de ressources Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Ce didacticiel utilise le nom **tutorials-iot-hub-rg**.

   - **Région** : Sélectionnez la région dans laquelle vous voulez placer votre hub. Sélectionnez l’emplacement le plus proche de vous. Certaines fonctionnalités, telles que les [flux d’appareils IoT Hub](../articles/iot-hub/iot-hub-device-streams-overview.md), sont disponibles uniquement dans certaines régions. Pour ces fonctionnalités limitées, vous devez sélectionner l’une des régions prises en charge. Ce tutoriel utilise la région **USA Ouest**.

   - **Nom du hub IoT** : Entrez un nom pour votre hub. Ce nom doit être globalement unique. Ce tutoriel utilise **tutorials-iot-hub**. Vous devez choisir votre propre nom unique quand vous créez ce hub.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Créer un hub dans le portail Azure":::

1. Sélectionnez **Suivant : Réseau** pour poursuivre la création de votre hub.

   Choisissez les points de terminaison qui peuvent se connecter à votre IoT Hub. Vous pouvez sélectionner le paramètre par défaut **Point de terminaison public (tous les réseaux)** ou choisir **Point de terminaison public (plages d’adresses IP sélectionnées)** ou **Point de terminaison privé**. Pour ce tutoriel, acceptez le paramètre par défaut.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Choisissez les points de terminaison qui peuvent se connecter.":::

1. Sélectionnez **Suivant : Gestion** pour poursuivre la création de votre hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Définissez la taille et la mise à l’échelle d’un nouveau hub avec le portail Azure.":::

   Vous pouvez accepter les paramètres par défaut ici. Si vous le souhaitez, vous pouvez modifier les champs suivants :

   - **Tarification et niveau de mise à l’échelle** : Le niveau sélectionné. Choisissez le niveau gratuit. Le niveau gratuit est destiné aux tests et à l’évaluation. Il permet la connexion de 500 appareils au hub, avec jusqu’à 8 000 messages par jour. Chaque abonnement Azure peut créer un hub IoT dans le niveau gratuit.

   - **Unités IoT Hub** : Le nombre de messages autorisés par unité par jour dépend du niveau de tarification de votre concentrateur. Par exemple, si vous souhaitez que le hub prenne en charge l’arrivée de 700 000 messages, vous choisissez deux unités de niveau S1.
   Chaque abonnement Azure peut créer un hub IoT dans le niveau gratuit. Pour plus d’informations sur les autres options de niveau, consultez [Choix du bon niveau IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender pour IoT** : Activez cette fonction pour ajouter une couche de protection supplémentaire contre les menaces à IoT et à vos appareils. Cette option n’est pas disponible pour les hubs au niveau gratuit. Pour plus d’informations sur cette fonctionnalité, consultez [Microsoft Defender pour IoT](/azure/asc-for-iot/).

   - **Paramètres avancés** > **Partitions appareil-à-cloud** : cette propriété associe les messages appareil-à-cloud au nombre de lecteurs simultanés des messages. La plupart des hubs n’ont besoin que de 4 partitions. Un hub de niveau gratuit est limité à deux partitions.

1. Sélectionnez **Suivant : Étiquettes** pour continuer à l’écran suivant.

   Les étiquettes sont des paires nom/valeur. Vous pouvez affecter la même étiquette à plusieurs ressources et groupes de ressources pour catégoriser les ressources et regrouper la facturation. Pour plus d’informations, consultez [Organisation des ressources Azure à l’aide de balises](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Affectez des étiquettes pour le hub à l’aide du portail Azure.":::

1. Sélectionnez **Suivant : Vérifier + créer** pour passer en revue vos choix. Un écran semblable au suivant s’affiche, mais avec les valeurs que vous avez sélectionnées lors de la création du hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Vérifiez les informations pour la création du hub.":::

1. Notez le nom de hub IoT choisi. Vous utiliserez cette valeur plus loin dans le didacticiel.
