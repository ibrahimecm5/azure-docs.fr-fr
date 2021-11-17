---
title: Suivre l’activité d’un capteur
description: La chronologie des événements présente une chronologie des activités détectées sur votre réseau, notamment les alertes et les actions de gestion des alertes, les événements réseau et les opérations des utilisateurs, telles que la connexion et la suppression d’un utilisateur.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: b70675ec16483b50a7207662b85fc6c65a2cf401
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278604"
---
# <a name="track-sensor-activity"></a>Suivre l’activité d’un capteur

## <a name="event-timeline"></a>Chronologie des événements

La chronologie des événements présente une chronologie des activités détectées par votre capteur. Par exemple :

  - Alertes et actions de gestion des alertes

  - Événements réseau

  - Opérations des utilisateurs, telles que la connexion et la suppression d’un utilisateur

La chronologie des événements fournit une vue chronologique des événements qui se sont produits sur le réseau. La chronologie des événements permet de comprendre et d’analyser la chaîne des événements qui ont précédé et suivi une attaque ou un incident, ce qui facilite les enquêtes et la forensique.

> [!NOTE]
> Les *administrateurs* et les *analystes de la sécurité* peuvent effectuer les procédures décrites dans cette section.

Pour consulter les journaux des événements :

- Dans le menu latéral, sélectionnez **Chronologie des événements**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Visualisez vos événements dans la chronologie des événements.":::

Outre l’affichage des événements détectés par le capteur, vous pouvez ajouter manuellement des événements à la chronologie. Ce processus est utile si l’événement s’est produit dans un système externe, mais qu’il a un impact sur votre réseau, et qu’il est important d’enregistrer l’événement et de le présenter comme faisant partie de la chronologie.

Pour ajouter manuellement des événements :

- Sélectionnez **Créer un événement**.

Pour exporter les informations du journal des événements dans un fichier CSV :

- Sélectionnez **Exporter**.

## <a name="filter-the-event-timeline"></a>Filtrer la chronologie des événements

Filtrez la chronologie pour afficher les appareils et les événements qui vous intéressent.

Pour filtrer la chronologie :

1. Sélectionnez **Filtres avancés**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Utilisez la fenêtre Filtres avancés des événements pour filtrer vos événements.":::

2. Définissez les filtres d’événements comme suit :

   - **Inclure l’adresse** : Affiche les événements pour des appareils spécifiques.

   - **Exclure l’adresse** : Masque les événements pour des appareils spécifiques.

   - **Inclure les types d’événements** : Affiche les types d’événements spécifiques.

   - **Exclure les types d’événements** : Masque les types d’événements spécifiques.

   - **Groupe d’appareils** : Sélectionnez un groupe d’appareils, tel qu’il a été défini dans la carte des appareils. Seuls les événements de ce groupe sont présentés.

3. Sélectionnez **Effacer tout** pour effacer tous les filtres sélectionnés.

4. Recherchez par **Alertes uniquement**, **Alertes et notifications** ou **Tous les événements**.

5. Sélectionnez **Sélectionner la date** pour choisir une date spécifique. Choisissez un jour, une heure et une minute. Les événements du laps de temps sélectionné s’affichent.

6.  Sélectionnez **Opérations utilisateur** pour inclure ou exclure des événements d’opération utilisateur.

7.  Sélectionnez la flèche (**V**) pour afficher plus d’informations sur l’événement :

    - Sélectionnez les alertes associées (le cas échéant) pour afficher une description détaillée de l’alerte.

    - Sélectionnez l’appareil pour afficher l’appareil sur la carte.

    - Sélectionnez **Filtrer les événements par appareils associés** si vous souhaitez filtrer par appareils associés.

    - Sélectionnez **Fichier PCAP** pour télécharger le fichier PCAP (s’il existe) qui contient une capture de paquets de l’ensemble du réseau à un moment précis. 
    
      Le fichier PCAP contient des informations techniques qui peuvent aider les ingénieurs réseau à déterminer les paramètres exacts de l’événement. Vous pouvez analyser le fichier PCAP avec un analyseur de protocole réseau tel que Wireshark, une application open source.

## <a name="see-also"></a>Voir aussi

[Afficher les alertes](how-to-view-alerts.md)
