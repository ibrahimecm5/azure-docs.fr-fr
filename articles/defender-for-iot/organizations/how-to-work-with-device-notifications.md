---
title: Utiliser des notifications d’appareils
description: Les notifications fournissent des informations sur l’activité réseau qui peut nécessiter votre attention, ainsi que des suggestions pour la gestion de cette activité.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 27f42f27920d41643f571efed1d5c033ebb47f40
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315385"
---
# <a name="work-with-device-notifications"></a>Utiliser des notifications d’appareils

Les notifications fournissent des informations sur l’activité réseau qui peut nécessiter votre attention, ainsi que des suggestions pour la gestion de cette activité. Par exemple, vous pouvez recevoir une notification concernant :

- un appareil inactif. Si l’appareil ne fait plus partie de votre réseau, vous pouvez le supprimer. Si l’appareil est inactif, par exemple parce qu’il est déconnecté par erreur du réseau, reconnectez l’appareil et ignorez la notification.

- Une adresse IP a été détectée sur un appareil actuellement identifié par une adresse MAC uniquement. Répondez en autorisant l’adresse IP de l’appareil.

La réponse aux notifications améliore les informations fournies dans le mappage des appareils, l’inventaire des appareils, ainsi que les rapports et les requêtes d’exploration de données. Il fournit également des insights sur les modifications réseau légitimes et les problèmes de configuration de réseau potentiels.

**Notifications contre alertes**

En plus de recevoir des notifications sur l’activité réseau, vous pouvez recevoir des *alertes*. Les notifications fournissent des informations sur les modifications du réseau ou les propriétés des appareils non résolues qui ne présentent pas de menace. Les alertes fournissent des informations sur les écarts réseau et les modifications susceptibles de représenter une menace pour le réseau.

Pour afficher les notifications :

1. Sélectionnez **Carte des appareils** depuis le volet menu de la console.

2. Sélectionnez l’icone **Notifications**. Le nombre rouge au-dessus de l’icône indique le nombre de notifications.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Icône de notification.":::

   La fenêtre **Notifications** affiche toutes les notifications détectées par le capteur.

   Des :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="notifications.":::

## <a name="filter-the-notifications-window"></a>Filtrer la fenêtre Notifications

Utilisez des filtres de recherche pour afficher les notifications qui vous intéressent.

| Filtrer par | Description |
|--|--|
| Filtrer par type | Affichez les notifications qui couvrent un domaine d’intérêt spécifique. Par exemple, affichez uniquement les notifications pour les appareils inactifs. |
| Filtrer par plage de dates | Affichez les notifications qui couvrent un intervalle de temps spécifique. Par exemple, afficher les notifications envoyées au cours de la semaine dernière uniquement. |
| Rechercher des informations spécifiques | Recherchez des notifications spécifiques. |

## <a name="notification-events-and-responses"></a>Événements et réponses aux notifications

Le tableau suivant décrit les types d’événements de notification que vous pouvez recevoir, ainsi que les options permettant de les gérer. Vous pouvez mettre à jour les informations de l’appareil avec une valeur recommandée ou ignorer la notification. Lorsque vous ignorez une notification, les informations de l’appareil ne sont pas mises à jour avec les informations recommandées. Si le trafic est de nouveau détecté, la notification sera renvoyée.

| Type d'événement de notification | Description | Réponses |
|--|--|--|
| Nouvelle adresse IP détectée | Une nouvelle adresse IP est associée à l’appareil. Cinq scénarios peuvent être détectés : <br /><br /> Une adresse IP supplémentaire a été associée à un appareil. Cet appareil est également associé à une adresse MAC existante.<br /><br /> Une nouvelle adresse IP a été détectée pour un appareil qui utilise une adresse MAC existante. Actuellement, l’appareil ne communique pas à l’aide d’une adresse IP.<br /> <br /> Une nouvelle adresse IP a été détectée pour un appareil qui utilise un nom NetBIOS. <br /><br /> Une adresse IP a été détectée en tant qu’interface de gestion pour un appareil associé à une adresse MAC. <br /><br /> Une nouvelle adresse IP a été détectée pour un appareil qui utilise une adresse IP virtuelle. | **Définir une adresse IP supplémentaire sur l’appareil** (fusionner des appareils) <br /> <br />**Remplacer l’adresse IP existante** <br /> <br /> **Abandonner**<br /> Supprimez la notification. |
| Appareils inactifs | Le trafic n’a pas été détecté sur un appareil depuis plus de 60 jours. | **Supprimer** <br /> Si cet appareil ne fait pas partie de votre réseau, supprimez-le. <br /><br />**Abandonner** <br /> Supprimez la notification si l’appareil fait partie de votre réseau. Si l’appareil est inactif, (par exemple parce qu’il est déconnecté par erreur du réseau), ignorez la notification et reconnectez l’appareil. |
| Nouveaux appareils OT | Un sous-réseau comprend un appareil OT qui n’est pas défini dans un sous-réseau ICS. <br /><br /> Chaque sous-réseau qui contient au moins un appareil OT peut être défini en tant que sous-réseau ICS. Cela permet de faire la différence entre les appareils OT et informatiques sur la carte. | **Définir comme sous-réseau ICS** <br /> <br /> **Abandonner** <br />Supprimez la notification si l’appareil ne fait pas partie du sous-réseau. |
| Aucun sous-réseau configuré | Aucun sous-réseau n’est actuellement configuré dans votre réseau. <br /><br /> Configurez des sous-réseaux pour une meilleure représentation dans le mappage et la possibilité de faire la distinction entre les appareils OT et les appareils informatiques. | **Ouvrez Configuration des sous-réseaux** et configurez les sous-réseaux. <br /><br />**Abandonner** <br /> Supprimez la notification. |
| Changements liés au système d’exploitation | Un ou plusieurs nouveaux systèmes d’exploitation ont été associés à l’appareil. | Sélectionnez le nom du nouveau système d’exploitation que vous souhaitez associer à l’appareil.<br /><br /> **Abandonner** <br /> Supprimez la notification. |
| Nouveaux sous-réseaux | De nouveaux sous-réseaux ont été découverts. | **Learn**<br />Ajoutez automatiquement le sous-réseau.<br />**Ouvrir la configuration des sous-réseaux**<br />Ajoutez toutes les informations de sous-réseau manquantes.<br />**Abandonner**<br />Supprimez la notification. |
| Modifications du type d’appareil | Un nouveau type d’appareil a été associé à l’appareil. | **Défini en tant que {...}**<br />Associez le nouveau type à l’appareil.<br />**Abandonner**<br />Supprimez la notification. |

## <a name="respond-to-many-notifications-simultaneously"></a>Répondre simultanément à de nombreuses notifications

Vous devrez peut-être gérer plusieurs notifications simultanément. Par exemple :

- Si le service informatique a effectué une mise à niveau du système d’exploitation vers un grand ensemble de serveurs réseau, vous pouvez demander au capteur de découvrir tous les serveurs mis à jour des nouvelles versions du serveur. 

- Si un groupe d’appareils d’une certaine ligne a été supprimé et n’est plus actif, vous pouvez demander au capteur de supprimer ces appareils de la console.

Vous pouvez demander au capteur d’appliquer des informations récemment détectées à plusieurs appareils ou de les ignorer.   

Pour afficher les notifications et gérer les notifications :

1. Utilisez l’option **filtrer par type, plage de dates** ou l’option **Sélectionner tout**. Désélectionnez les notifications si nécessaire.

2. Demander au capteur d’appliquer des informations récemment détectées à des appareils sélectionnés en sélectionnant **APPRENDRE**. Ou, demandez au capteur d’ignorer les informations récemment détectées en sélectionnant **IGNORER**. Le nombre de notifications que vous pouvez apprendre et ignorer simultanément, ainsi que le nombre de notifications que vous devez gérer individuellement, s’affichent.

Les événements configurés **Nouvelles adresses IP** et **Aucun sous-réseau** ne peuvent pas être gérés simultanément. Ils nécessitent une confirmation manuelle.

## <a name="see-also"></a>Voir aussi

[Afficher les alertes](how-to-view-alerts.md)
