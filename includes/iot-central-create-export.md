---
title: Fichier Include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a686ab26c93130d7371bcf2435dccbd7aaa1c985
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070391"
---
Maintenant que vous disposez d'une destination vers laquelle exporter vos données, configurez la fonctionnalité d'exportation de données dans votre application IoT Central :

1. Connectez-vous à votre application IoT Central.

1. Dans le volet gauche, sélectionnez **Exportation de données**.

    > [!Tip]
    > Si vous ne voyez pas **Exportation de données** dans le volet gauche, cela signifie que vous n’avez pas les autorisations nécessaires pour configurer l’exportation de données dans votre application. Contactez un administrateur pour configurer l’exportation de données.

1. Sélectionnez **+ Nouvelle exportation**.

1. Entrez un nom d'affichage pour votre nouvelle exportation et assurez-vous que l'exportation de données est **activée**.

1. Choisissez le type de données à exporter. Le tableau suivant répertorie les types d'exportation de données pris en charge :

    | Type de données | Description | Format de données |
    | :------------- | :---------- | :----------- |
    |  Télémétrie | Exportez des messages de télémétrie à partir d’appareils en quasi-temps réel. Chaque message exporté contient le contenu complet du message d'origine de l'appareil, normalisé.   |  [Format du message de télémétrie](#telemetry-format)   |
    | Modifications de la propriété | Exportez les modifications apportées aux propriétés de l’appareil et du cloud en quasi-temps réel. Pour les propriétés en lecture seule de l'appareil, les modifications apportées aux valeurs signalées sont exportées. Pour les propriétés en lecture-écriture, les valeurs signalées et souhaitées sont exportées. | [Format du message de modification de la propriété](#property-changes-format) |
    | Connectivité des appareils | Exportez les événements connectés et déconnectés des appareils. | [Format de message de connectivité des appareils](#device-connectivity-changes-format) |
    | Cycle de vie de l’appareil | Exportez les événements enregistrés, supprimés, approvisionnés, activés, désactivés, displayNameChanged et deviceTemplateChanged de l’appareil. | [Format du message relatif aux changements de cycle de vie d’appareil](#device-lifecycle-changes-format) |
    | Cycle de vie de modèle d’appareil | Exportez les changements de modèle d’appareil publiés, dont les créations, les mises à jour et les suppressions. | [Format de message relatif aux changements de cycle de vie d’appareil](#device-template-lifecycle-changes-format) |

1. Vous pouvez également ajouter des filtres pour réduire la quantité de données exportées. Différents types de filtres sont disponibles pour chaque type d’exportation de données : <a name="DataExportFilters"></a>

    | Type de données | Filtres disponibles|
    |--------------|------------------|
    |Télémétrie|<ul><li>Filtrer par nom d’appareil, identité d'appareil et modèle d’appareil et indiquez si l’appareil est simulé</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie correspondant aux conditions de filtre</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie des appareils dont les propriétés correspondent aux conditions de filtre</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement la télémétrie avec des *propriétés de message* correspondant à la condition de filtre. Les *propriétés de message* (également appelées *propriétés d’application*) sont envoyées dans un ensemble de paires clé-valeur sur chaque message de télémétrie éventuellement envoyé par des appareils qui utilisent les kits de développement logiciel (SDK) d’appareil. Pour créer un filtre de propriété de message, entrez la clé de la propriété de message que vous recherchez, et spécifiez une condition. Seuls les messages de télémétrie dont les propriétés correspondent à la condition de filtre spécifiée sont exportés. [En savoir plus sur les propriétés d’application dans des documents IoT Hub](../articles/iot-hub/iot-hub-devguide-messages-construct.md) </li></ul>|
    |Modifications de la propriété|<ul><li>Filtrer par nom d’appareil, identité d'appareil et modèle d’appareil et indiquez si l’appareil est simulé</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement les changements de propriété correspondant aux conditions de filtre</li></ul>|
    |Connectivité des appareils|<ul><li>Filtrer par nom d’appareil, ID d’appareil, modèle d’appareil, organisations et indiquer si l’appareil est simulé</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement les changements des appareils dont les propriétés correspondent aux conditions de filtre</li></ul>|
    |Cycle de vie de l’appareil|<ul><li>Filtrer par nom d’appareil, identité d’appareil, modèle d’appareil et indiquez si l’appareil est approvisionné, activé ou simulé</li><li>Filtrer le flux de façon à ce qu’il contienne uniquement les changements des appareils dont les propriétés correspondent aux conditions de filtre</li></ul>|
    |Cycle de vie de modèle d’appareil|<ul><li>Filtrer par modèle d’appareil</li></ul>|

1. Si vous le souhaitez, vous pouvez également enrichir les messages exportés avec des métadonnées supplémentaires dans les paires clé-valeur. Les enrichissements suivants sont disponibles pour les types d’exportation de données de télémétrie, de modification des propriétés, de connectivité des appareils et de cycle de vie des appareils : <a name="DataExportEnrichmnents"></a>
    - **Chaîne personnalisée** : Ajoute une chaîne statique personnalisée à chaque message. Entrez n’importe quelle clé, puis entrez une valeur de chaîne.
    - **Propriété**, qui ajoute à chaque message ce qui suit :
       - Métadonnées d’appareil, par exemple nom de l’appareil, nom du modèle d’appareil, informations relatives à l’activation, aux organisations, au provisionnement et à la simulation.
       - Valeur actuelle de la propriété rapportée d’appareil ou de la propriété de cloud à chaque message. Si le message exporté provient d'un appareil qui ne dispose pas de la propriété spécifiée, l'enrichissement ne s'applique pas à ce message.
