---
title: En savoir plus sur les appareils sur des zones spécifiques
description: Utilisez la console de gestion locale pour obtenir des informations d’affichage complètes par zone spécifique
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/09/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 511c3b5f66b84490380507c5106775cd6cc6b255
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318880"
---
# <a name="view-information-per-zone"></a>Afficher les informations par zone


## <a name="view-a-device-map-for-a-zone"></a>Afficher une carte des périphériques pour une zone

Affichez une carte des périphériques pour une zone sélectionnée sur un capteur. Cette vue affiche tous les éléments réseau associés à la zone sélectionnée, y compris les capteurs, les périphériques qui y sont connectés et d’autres informations.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Capture d’écran de la carte des zones.":::


- Dans la fenêtre **Gestion des sites**, sélectionnez **Afficher la carte des zones** dans la barre contenant le nom de la zone.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Région par défaut à unité commerciale par défaut.":::

La fenêtre **Carte des appareils** s’affiche. Les outils suivants sont disponibles pour visualiser les appareils et les informations sur les appareils à partir de la carte. Pour plus d’informations sur chacune de ces fonctionnalités, consultez le *guide de l’utilisateur de la plateforme Defender pour IoT*.

- **Vues de zoom sur la carte** : Vue simplifiée, vue des connexions et vue détaillée. La vue cartographique affichée varie selon le niveau de zoom de la carte. Vous basculez entre les vues cartographiques en ajustant les niveaux de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Outils de recherche et de disposition de cartes** : Outils utilisés pour afficher des segments réseau, des appareils, des groupes d’appareils ou des couches variés.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Capture d’écran de la vue Outils de recherche et de disposition.":::

- **Étiquettes et indicateurs sur les appareils :** Par exemple, nombre d’appareils regroupés dans le sous-réseau d’un réseau informatique. Dans cet exemple, il s’agit de 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Capture d’écran des étiquettes et des indicateurs.":::

- **Afficher les propriétés de l’appareil** : Par exemple, capteur qui analyse l’appareil et ses propriétés de base. Cliquez avec le bouton droit sur l’appareil pour en afficher les propriétés.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Capture d’écran de l’affichage Propriétés de l’appareil.":::

- **Alerte associée à un appareil :** Cliquez avec le bouton droit sur l’appareil pour afficher les alertes associées.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Capture d’écran de la vue Afficher les alertes.":::

## <a name="view-alerts-associated-with-a-zone"></a>Afficher les alertes associées à une zone

Pour afficher les alertes associées à une zone spécifique :

- Sélectionnez l’icône d’alerte dans la fenêtre **Zone**. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Vue Unité commerciale par défaut et des exemples.":::

Pour plus d’informations, consultez [Vue d’ensemble : Utilisation des alertes](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Afficher l’inventaire des appareils d’une zone

Pour afficher l’inventaire des appareils associé à une zone spécifique :

- Sélectionnez **Afficher l’inventaire des appareils** dans la fenêtre **Zone**.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="L’écran Inventaire des appareils s’affiche.":::

Pour plus d’informations, consultez [Examiner toutes les détections de capteur d’entreprise dans l’inventaire des appareils](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Afficher des informations supplémentaires sur la zone

Les informations supplémentaires suivantes sur les zones sont disponibles :

- **Détails de la zone** : Visualisez le nombre d’appareils, d’alertes et de capteurs associés à la zone.

- **Détails du capteur** : Visualisez le nom, l’adresse IP et la version de chaque capteur affecté à la zone.

- **État de la connectivité** : Si un capteur est déconnecté, connectez-vous à partir du capteur. Voir [Connecter des capteurs à la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Progression de la mise à jour** : Si le capteur connecté est en cours de mise à niveau, les états de mise à niveau s’affichent. Pendant la mise à niveau, la console de gestion locale ne reçoit pas les informations d’appareil du capteur.

## <a name="next-steps"></a>Étapes suivantes

[Obtenir des informations sur les menaces locales, régionales et mondiales](how-to-gain-insight-into-global-regional-and-local-threats.md)
