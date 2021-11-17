---
title: Filtrer et gérer les alertes à partir de la page Alertes
description: Affichez les alertes selon différentes catégories et utilisez les fonctionnalités de recherche pour vous aider à trouver les alertes qui vous intéressent.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d7c8207a8dcd36fd52d9271bcc840ee6730f76da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278623"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>Filtrer et gérer les alertes à partir de la page Alertes 

Cet article explique comment afficher les alertes déclenchées par votre capteur et les gérer avec des outils d’alerte.

Vous pouvez afficher les alertes selon différentes catégories, telles que les alertes qui ont été archivées ou épinglées. Vous pouvez aussi rechercher des alertes d’intérêt, telles que les alertes basées sur une adresse IP ou MAC.  

Vous pouvez également consulter les alertes à partir du tableau de bord du capteur.

Pour examiner les alertes :

- Sélectionnez **Alertes** dans le menu latéral. La fenêtre Alertes affiche les alertes détectées par votre capteur.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="Affichage de l’écran Alertes.":::

## <a name="view-alerts-by-category"></a>Afficher les alertes par catégorie

Vous pouvez afficher les alertes selon différentes catégories à partir de la vue principale **Alertes**. Sélectionnez une alerte pour passer en revue les détails et gérer l’événement.

| Trier par type | Description |
|--|--|
| **Alertes importantes** | Alertes triées par importance. |
| **Alertes épinglées** | Alertes que l’utilisateur a épinglées pour une enquête plus approfondie. Les alertes épinglées ne sont pas archivées et sont stockées pendant 14 jours dans le dossier épinglé. |
| **Alertes récentes** | Alertes triées par date et heure. |
| **Alertes avec accusé de réception** | Alertes dont la réception a été accusée et qui n’ont pas été traitées, ou qui ont été désactivées puis réactivées. |
| **Alertes archivées** | Alertes que le système a archivées automatiquement. Par défaut, les alertes sont archivées 14 jours après le déclenchement de l’alerte. Seul l’utilisateur Administrateur peut y accéder. |

## <a name="search-for-alerts-of-interest"></a>Rechercher des alertes d’intérêt

La vue principale Alertes fournit diverses fonctionnalités de recherche pour vous aider à trouver les alertes qui vous intéressent.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Capture d’écran des alertes en mode apprentissage.":::

### <a name="text-search"></a>Recherche de texte

Utilisez l’option Recherche libre pour rechercher des alertes par texte, chiffres ou caractères.

Pour rechercher :

- Saisissez le texte requis dans le champ Recherche libre et appuyez sur la touche Entrée.

Pour effacer la recherche :

- Supprimez le texte dans le champ Recherche libre et appuyez sur la touche Entrée.

### <a name="device-group-or-device-ip-address-search"></a>Recherche un groupe d’appareils ou une adresse IP d’appareil

Recherchez des alertes qui font référence à des adresses IP ou à des groupes d’appareils spécifiques. Les groupes d’appareils sont créés dans la carte des appareils.

Pour utiliser des filtres avancés :

1. Sélectionnez **Filtres avancés** dans la vue principale **Alertes**.

2. Choisissez un groupe d’appareils ou un appareil.

3. Sélectionnez **Confirmer**.

4. Pour effacer la recherche, sélectionnez **Effacer tout**.

### <a name="security-versus-operational-alert-search"></a>Recherche des alertes opérationnelles ou des alertes de sécurité

Basculez entre l’affichage des alertes opérationnelles et celles de sécurité :

- Les alertes **de sécurité** représentent le trafic potentiel de programmes malveillants, les anomalies de réseau, les violations de stratégie et les violations de protocole.

- Les alertes **opérationnelles** représentent les anomalies de réseau, les violations de stratégie et les violations de protocole.

Si aucune des options n’est sélectionnée, toutes les alertes sont affichées.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Sécurité sur l’écran des alertes.":::

## <a name="alert-page-options"></a>Options de la page Alerte

Les messages d’alerte incluent les actions suivantes :

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: pour accuser réception d’une alerte.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: pour ne pas accuser réception d’une alerte.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: pour épingler une alerte.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: pour détacher une alerte.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: pour accuser réception de toutes les alertes.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: pour apprendre et accuser réception de toutes les alertes.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: pour exporter les informations d’alerte dans un fichier .csv. Utilisez l’option **Exportation d’étendue d’alertes** pour exporter les informations d’alerte dans des lignes distinctes pour chaque alerte qui couvre plusieurs appareils.

## <a name="alert-pop-up-window-options"></a>Options de la fenêtre contextuelle d’alerte

- Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: pour télécharger un rapport d’alerte sous forme de fichier PDF.

- Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: pour télécharger le fichier PCAP. Le fichier peut être consulté avec Wireshark, l’analyseur de protocole réseau gratuit.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: pour télécharger un fichier PCAP filtré qui contient uniquement les paquets d’alerte pertinents, réduisant ainsi la taille du fichier de sortie et permettant une analyse plus ciblée. Vous pouvez visualiser le fichier en utilisant Wireshark.

- Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: pour afficher l’alerte dans la chronologie des événements.

- Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: pour épingler l’alerte.

- Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: pour détacher l’alerte.

- Sélectionnez :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: pour approuver le trafic (analystes de sécurité et administrateurs uniquement).

- Sélectionnez un appareil pour l’afficher dans la carte des appareils.

## <a name="next-steps"></a>Étapes suivantes

[Gérer l’événement d’alerte](how-to-manage-the-alert-event.md)

[Accélérer les workflows d’alerte](how-to-accelerate-alert-incident-response.md)
