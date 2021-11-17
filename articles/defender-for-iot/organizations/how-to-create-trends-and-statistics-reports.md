---
title: Générer des rapports de tendances et de statistiques
description: Familiarisez-vous avec l’activité du réseau, les statistiques et les tendances du réseau en utilisant les widgets Tendances et Statistiques Defender pour IoT.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: f84f739a9566bff2a9fe8286d301aa31d1e74199
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284034"
---
# <a name="sensor-trends-and-statistics-reports"></a>Rapports de tendances et de statistiques des capteurs

Vous pouvez créer des graphiques de widget et des graphiques en secteurs pour obtenir des informations sur les tendances et les statistiques du réseau. Les widgets peuvent être regroupés dans des tableaux de bord définis par l’utilisateur.

> [!NOTE]
> Les analystes d’administration et de sécurité peuvent créer des rapports de tendances et de statistiques.

Le tableau de bord comprend des widgets qui décrivent graphiquement les types d’informations suivants :

- Trafic par port
- Trafic principal par port
- Bande passante du canal
- Bande passante totale
- Connexion TCP active
- Bande passante supérieure par VLAN
- Appareils :
  - Nouveaux appareils
  - Appareils occupés
  - Appareils par fournisseur
  - Appareils par système d’exploitation
  - Nombre d’appareils par VLAN
  - Appareils déconnectés
- Pertes de connectivité par heure
- Alertes pour les incidents par type
- Accès à la table de base de données
- Widgets de dissection de protocole
- DELTAV
  - Distribution des opérations roc DeltaV
  - Événements roc DeltaV par nom
  - Événements DeltaV par heure
- AMS
  - Trafic AMS par port du serveur
  - Trafic AMS par commande
- Ethernet et adresse IP :
  - Trafic d’adresses Ethernet et IP par service CIP
  - Trafic d’adresses Ethernet et IP par classe CIP
  - Trafic d’adresses Ethernet et IP par commande
- OPC :
  - Principales opérations de gestion OPC
  - Principales opérations d’E/S OPC
- Siemens S7 :
  - Trafic S7 par fonction de contrôle
  - Trafic S7 par sous-fonction
- VLAN
  - Nombre d’appareils par VLAN
  - Bande passante supérieure par VLAN
- 60870-5-104
  - Trafic IEC-60870 par ASDU
- BACNET
  - Services BACnet
- DNP3
  - Trafic DNP3 par fonction
- SRTP :
  - Trafic SRTP par code de service
  - Erreurs SRTP par jour
- SuiteLink :
  - Principales balises SuiteLink interrogées
  - Comportement de la balise numérique SuiteLink
- Trafic IEC-60870 par ASDU
- Trafic DNP3 par fonction
- Trafic MMS par service
- Trafic Modbus par fonction
- Trafic OPC-UA par service

> [!NOTE]
>  L’heure dans les widgets est définie en fonction de l’heure du capteur.

## <a name="create-reports"></a>Créer des rapports

Pour afficher les tableaux de bord et les widgets :

Sélectionnez **Tendances et statistiques** dans le menu latéral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Capture d’écran d’une investigation.":::

Par défaut, les résultats sont affichés pour les détections au cours des sept derniers jours. Vous pouvez utiliser les outils de filtre pour modifier cette plage. Pat exemple, une recherche de texte libre.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

Créez un tableau de bord en sélectionnant le menu déroulant **Tableau de bord** . Vous pouvez créer et ajouter autant de widgets que vous le souhaitez à un tableau de bord.

Vous pouvez créer des tableaux de bord personnalisés à l’aide des options suivantes :

- Ajouter un widget au tableau de bord

- Supprimer un widget du tableau de bord

- Modifier le filtre d’un widget

- Redimensionner un widget

- Modifier la position d’un widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Modifier la position d’un widget.":::

Pour créer un tableau de bord personnalisé :

1. Sélectionnez **Tendances et statistiques** dans le volet gauche.

1. Sélectionnez le menu déroulant **Sélectionner un tableau de bord**, puis cliquez sur le bouton **Créer un tableau de bord**.

1. Entrez un nom explicite pour le nouveau tableau de bord, puis sélectionnez **Créer**.

1. Sélectionnez **Ajouter un widget** en haut à gauche de la page.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Sélectionnez le widget dans le magasin de widgets.":::

1. Les widgets **Sécurité** et **En fonctionnement** sont disponibles en haut à droite de la fenêtre. Choisissez parmi différentes catégories et protocoles. Une brève description avec un graphique miniature apparaît pour chaque widget. Utilisez la barre de défilement pour afficher tous les widgets disponibles.

1. Sélectionnez un widget à l’aide du bouton **Cliquer pour ajouter** . Le widget apparaît immédiatement sur le tableau de bord.

Pour supprimer un tableau de bord :

1. Sélectionnez le nom du tableau de bord dans le menu déroulant.

1. Sélectionnez l’icône **Supprimer**, puis cliquez sur **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Sélectionnez l’icône Supprimer pour supprimer le tableau de bord.":::

Pour renommer un tableau de bord :

1. Sélectionnez le nom du tableau de bord dans le menu déroulant.

1. Sélectionnez l’icône **Éditer**.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Sélectionnez l’icône Éditer pour renommer votre tableau de bord.":::

1. Saisissez le nouveau nom du tableau de bord, puis sélectionnez **Enregistrer**.
 
Pour définir le tableau de bord par défaut :

1. Sélectionnez le nom du tableau de bord dans le menu déroulant.

1. Sélectionnez l’icône en forme d’**étoile** pour choisir le tableau de bord à définir comme tableau de bord par défaut.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Sélectionnez l’icône en forme d’étoile pour choisir votre tableau de bord par défaut."::: 

Pour modifier les données de filtrage d’un widget :

1. Sélectionnez l’icône **Filtrer**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Sélectionnez l’icône Filtrer pour définir les paramètres de votre widget.":::

1. Éditez les paramètres requis.

1. Sélectionnez **OK**.

Pour supprimer un widget :

- Sélectionnez l’icône :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false":::.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Sélectionnez X pour supprimer le widget.":::

## <a name="creating-widgets"></a>Créer des widgets 

Le magasin de widgets vous permet de sélectionner des widgets par catégorie et protocole. Vous pouvez afficher les widgets **Sécurité** et **En fonctionnement** disponibles en les sélectionnant.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Sélectionnez votre widget dans le magasin de widgets.":::

Chaque widget contient des données spécifiques sur le trafic système, les statistiques réseau, les statistiques de protocole, l’appareil et les informations d’alerte. Un message s’affiche lorsqu’il n’y a pas de données pour un widget.

Vous pouvez supprimer une section du graphique à secteurs, dans un graphique à secteurs, pour afficher plus clairement la signification relative des autres secteurs. Sélectionnez le nom du secteur dans la légende en bas de l’écran pour effectuer cette opération.

Les sections suivantes présentent des exemples de cas d’usage pour certains des widgets.

### <a name="busy-devices-widget"></a>Widget Appareils occupés

Ce widget répertorie les cinq principaux appareils les plus actifs. En mode **Édition**, vous pouvez filtrer par protocoles connus.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Vue du widget Appareils occupés.":::

### <a name="total-bandwidth-widget"></a>Widget Bande passante totale

Ce widget mesure la bande passante en Mbit/s (mégabits par seconde). La bande passante est indiquée sur l’axe Y, la date apparaissant sur l’axe X. Le mode **Édition** vous permet de filtrer les résultats en fonction du client, du serveur, du port du serveur, ou du sous-réseau. Une info-bulle s’affiche lorsque vous placez le curseur sur le graphe.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Vue du widget Bande passante totale.":::

### <a name="channels-bandwidth-widget"></a>Widget Canaux de bande passante

Ce widget affiche les cinq principaux canaux de trafic. Vous pouvez filtrer par adresse et définir le nombre de résultats présentés. Sélectionnez la flèche vers le bas pour afficher plus de canaux.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Vue du widget Canaux de bande passante.":::

### <a name="traffic-by-port-widget"></a>Widget Trafic par port

Ce widget affiche le trafic par port, indiqué par un graphique à secteurs, avec chaque port désigné par une couleur différente. Le volume de trafic dans chaque port est proportionnel à la taille de sa portion du graphique.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Vue du widget Trafic par port.":::

### <a name="new-devices-widget"></a>Widget Nouveaux appareils

Ce widget affiche le graphique à barres des nouveaux appareils, indiquant le nombre de nouveaux appareils découverts à une date donnée.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Vue du widget Nouveaux appareils.":::

### <a name="protocol-dissection-widgets"></a>Widgets de dissection de protocole

Ce widget affiche un graphique à secteurs qui vous donne un aperçu du trafic par protocole, par codes de fonction, et par services. La taille de chaque secteur du graphique est proportionnelle à la quantité de trafic des autres secteurs.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Vue du widget Dissection de protocole.":::

### <a name="active-tcp-connections-widget"></a>Widget Connexions TCP actives

Ce widget affiche un graphique qui indique le nombre de connexions TCP actives dans le système.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Vue du widget Connexions TCP actives.":::

### <a name="incident-by-type-widget"></a>Widget Incident par type

Ce widget affiche un graphique à secteurs qui indique le nombre d’incidents par type. Il s’agit du nombre d’alertes générées par chaque moteur sur une période prédéfinie.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Vue du widget Incident par type.":::

## <a name="devices-by-vendor-widget"></a>Widget Appareils par fournisseur

Ce widget affiche un graphique à secteurs qui indique le nombre d’appareils par fournisseur. Le nombre d’appareils pour un fournisseur spécifique est proportionnel à la taille de la partie du fournisseur de cet appareil sur le disque par rapport aux autres fournisseurs d’appareils.

## <a name="number-of-devices-per-vlan-widget"></a>Widget Nombre d’appareils par VLAN

Ce widget affiche un graphique à secteurs qui indique le nombre d’appareils détectés par réseau local virtuel (VLAN). La taille de chaque secteur du graphique est proportionnelle au nombre d’appareils détectés par rapport aux autres secteurs.

Chaque réseau local virtuel apparaît avec la balise VLAN affectée par le capteur ou le nom que vous avez ajouté manuellement.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Vue du widget Nombre d’appareils.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Widget Bande passante supérieure par VLAN

Ce widget affiche la consommation de bande passante par VLAN. Par défaut, le widget affiche cinq VLAN avec l’utilisation de bande passante la plus élevée.

Vous pouvez filtrer les données par la période présentée dans le widget. Sélectionnez la flèche vers le bas pour afficher plus de résultats.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Vue du widget Bande passante supérieure par VLAN.":::

## <a name="system-report"></a>Rapport système

Pour télécharger le rapport système : 

1. Sélectionnez **Tendances et statistiques** dans le menu latéral.

1. Sélectionnez **Rapport système** dans l’angle supérieur droit. Le rapport est téléchargé automatiquement.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Sélectionnez le bouton du rapport système pour télécharger une copie du rapport système.":::

Le rapport système est un fichier PDF contenant toutes les données du système :

  - Appareils

  - Alertes

  - Informations sur la stratégie réseau

## <a name="devices-in-a-system-report"></a>Appareils dans un rapport système 

Le rapport système affiche une liste de tous les appareils et leurs informations. Par exemple, le type, le nom et les protocoles utilisés. Le rapport système affiche également une liste d’appareils par fournisseur.

## <a name="alerts-in-system-report"></a>Alertes dans un rapport système 

Le rapport système affiche une liste de toutes les alertes avec leurs informations telles que la date et la gravité.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Vue des alertes dans les rapports système.":::

## <a name="network-information-in-system-report"></a>Informations réseau dans un rapport système 

Le rapport système affiche en détail la ligne de base de votre réseau. Par exemple, le code de fonction DNP3 et les ports ouverts par connexion.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Vue de la fonction DNP3 dans le rapport système.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Vue du rapport sur le port ouvert par connexion.":::

## <a name="see-also"></a>Voir aussi

[Rapports d’évaluation des risques](how-to-create-risk-assessment-reports.md)
[Requêtes d’exploration de données des capteurs](how-to-create-data-mining-queries.md)
[Création de rapports de vecteur d'attaque](how-to-create-attack-vector-reports.md)
