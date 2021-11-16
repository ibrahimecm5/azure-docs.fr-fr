---
title: Obtenir des informations sur les appareils découverts par un capteur spécifique
description: L’inventaire des appareils affiche un large éventail d’attributs d’appareil détectés par un capteur.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 423ddcb9b50be6aa758e28f20291f8d66e58e3f4
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028189"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Examiner les détections de capteur dans un inventaire d’appareils

L’inventaire des appareils affiche un large éventail d’attributs d’appareil détectés par un capteur. Des options sont disponibles pour :

 - Filtrer facilement les informations.

 - Exporter les informations dans un fichier CSV.

 - Importer les détails du Registre Windows.

 - Créer des groupes à afficher sur la carte des appareils.

## <a name="view-device-attributes-in-the-device-inventory"></a>Afficher les attributs de l’appareil dans l’inventaire des appareils

Les attributs suivants apparaissent dans le tableau d’inventaire des appareils.

| Paramètre | Description |
|--|--|
| Nom | Nom de l’appareil, tel qu’il a été découvert par le capteur ou entré par l’utilisateur. |
| Type | Type d’appareil déterminé par le capteur ou entré par l’utilisateur. |
| Fournisseur | Nom du fournisseur de l’appareil, tel que défini dans l’adresse MAC. |
| Système d’exploitation | Système d’exploitation de l’appareil, s’il est détecté. |
| Version du microprogramme | Microprogramme de l’appareil, s’il est détecté. |
| Adresse IP | Adresse IP de l’appareil lorsqu’elle est définie. |
| VLAN | Le VLAN de l’appareil. Pour plus d’informations sur l’instruction permettant au capteur de découvrir des VLAN, consultez [Définir des noms de VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names).(how-to-define-management-console-network-settings.md#define-vlan-names). |
| Adresse MAC | Adresse MAC de l’appareil. |
| Protocoles | Les protocoles utilisés par l’appareil. |
| Alertes sans accusé de réception | Nombre d’alertes sans accusé de réception associées à cet appareil. |
| Est autorisé | L’état d’autorisation défini par l’utilisateur :<br />- **True** : L’appareil a été autorisé.<br />- **False** : L’appareil n’a pas été autorisé. |
| Est considéré comme un scanneur | Défini comme appareil d’analyse du réseau par l’utilisateur. |
| Est un appareil de programmation | Défini comme appareil de programmation autorisé par l’utilisateur. <br />- **True** : L’appareil effectue des activités de programmation pour les PLC, RTU et contrôleurs qui sont pertinentes pour les stations d’ingénierie. <br />- **False** : L’appareil n’est pas un appareil de programmation. |
| Groupes | Groupes auxquels cet appareil participe. |
| Dernière activité | Dernière activité effectuée par l’appareil. |
| Discovered | Lorsque cet appareil a été détecté pour la première fois sur le réseau. |

## <a name="what-is-an-inventory-device"></a>Qu’est-ce qu’un appareil d’inventaire ?

L’inventaire d’appareil Defender pour IoT affiche un large éventail d’attributs de ressource qui sont détectés par les capteurs qui contrôlent les réseaux d’organisations et les points de terminaison gérés.

Defender pour IoT identifie et classe les appareils comme un appareil réseau unique dans l’inventaire pour :

1. Appareils IT, OT et IoT autonomes (avec 1 ou plusieurs cartes d’interface réseau)
1. Appareils composés de plusieurs composants de carte d’insertion (notamment tous les racks/emplacements/modules)
1. Appareils agissant comme une infrastructure réseau comme commutateur/routeur (avec plusieurs cartes d’interface réseau). 

Les adresses IP Internet publiques, les groupes de multidiffusion et les groupes de diffusion ne sont pas considérés comme des appareils d’inventaire.
Les appareils qui ont été inactifs depuis plus de 60 jours sont classés en tant qu’appareils d’inventaire inactifs.

**Pour afficher l’inventaire des appareils :**

1. Sélectionnez **Appareils** dans le volet de gauche. Le volet **Appareils** s’ouvre à droite.

2. Dans le volet **Appareils**, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false":::.

Pour masquer et afficher des colonnes, personnalisez la table d’inventaire des appareils :

1. Dans le menu supérieur droit de l’inventaire des appareils, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false":::.

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Écran des paramètres d’inventaire des appareils.":::

2. Dans la fenêtre **Paramètres d’inventaire des appareils**, sélectionnez les colonnes que vous souhaitez afficher dans le tableau d’inventaire des appareils.

3. Modifiez l’emplacement des colonnes dans la table à l’aide des flèches.

4. Sélectionnez **Enregistrer**. La fenêtre **Paramètres d’inventaire des appareils** se ferme et les nouveaux paramètres s’affichent dans la table.

### <a name="create-temporary-device-inventory-filters"></a>Créer des filtres d’inventaire des appareils temporaires

Vous pouvez définir un filtre qui définit les informations affichées dans la table. Par exemple, vous pouvez décider d’afficher uniquement les informations de l’appareil PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Apprentissage des appareils.":::

Le filtre n’est pas enregistré lorsque vous quittez l’inventaire.

### <a name="save-device-inventory-filters"></a>Enregistrer des filtres d’inventaire des appareils

Vous pouvez enregistrer un filtre ou une combinaison de filtres dont vous avez besoin, puis les réappliquer dans l’inventaire des appareils. Créez des filtres plus larges basés sur un certain type d’appareil, ou des filtres plus étroits basés sur un type spécifique et un protocole spécifique.

Les filtres que vous enregistrez sont également enregistrés sous forme de groupes de cartes d’appareils. Cette fonctionnalité offre un niveau supplémentaire de granularité lors de l’affichage des périphériques réseau sur la carte.

Pour créer des filtres :

1. Dans la colonne que vous souhaitez filtrer, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false":::.

2. Dans la boîte de dialogue **Filtre**, sélectionnez le type de filtre :

   - **Est égal à** : Valeur exacte sur laquelle vous souhaitez filtrer la colonne. Par exemple, si vous filtrez la colonne de protocole en fonction de **Est égal à** et `value=ICMP`, la colonne présentera les appareils qui utilisent uniquement le protocole ICMP.

   - **Contient** : Valeur contenue parmi les autres valeurs de la colonne. Par exemple, si vous filtrez la colonne de protocole en fonction de **Contient** et `value=ICMP`, la colonne présente les appareils qui utilisent le protocole ICMP parmi la liste des protocoles utilisés par l’appareil.

3. Pour organiser les informations de colonne par ordre alphabétique, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::. Réorganisez l’ordre en sélectionnant les flèches :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: et :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false":::.

4. Pour enregistrer un nouveau filtre, définissez le filtre et sélectionnez **Enregistrer sous**.

5. Pour modifier les définitions de filtre, modifiez les définitions et sélectionnez **Enregistrer les modifications**.

Pour afficher des filtres :

- Ouvrez le volet gauche et affichez les filtres que vous avez enregistrés :

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Affichez les filtres dans le volet gauche.":::

### <a name="view-filtered-information-as-a-map-group"></a>Afficher les informations filtrées sous forme de groupe de cartes

Lorsque vous basculez vers la vue cartographique, les appareils filtrés sont mis en surbrillance et filtrés. Le groupe de filtres que vous avez enregistré s’affiche dans le menu latéral sous le groupe **Filtres d’inventaire des appareils**.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Affichez des filtres en vue cartographique.":::

## <a name="learn-windows-registry-details"></a>Découvrir les détails du Registre Windows

Outre la découverte des appareils OT, vous pouvez découvrir des stations de travail et des serveurs Microsoft Windows. Ces appareils sont également affichés dans Inventaire des appareils. Une fois que vous avez découvert les appareils, vous pouvez enrichir Inventaire des appareils avec des informations Windows détaillées, telles que :

- Version de Windows installée

- Applications installées

- Informations au niveau du correctif

- Ouvrir les ports

- Informations plus robustes sur les versions de système d’exploitation

Deux options sont disponibles pour récupérer ces informations :

- Interrogation active à l’aide d’analyses WMI planifiées. 

- Vérification locale en distribuant et en exécutant un script sur l’appareil. L’utilisation de scripts locaux contourne les risques liés à l’exécution de l’interrogation WMI sur un point de terminaison. C’est également utile pour les réseaux réglementés avec des éléments en cascade et unidirectionnels.

Cet article explique comment examiner localement le Registre du point de terminaison Windows à l’aide d’un script. Ces informations seront utilisées pour générer des alertes, des notifications, des rapports d’exploration de données, des évaluations des risques et des rapports de vecteur d’attaque.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Capture d’écran de l’exploration de données.":::

Vous pouvez considérer les systèmes d’exploitation Windows suivants :

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Avant de commencer

Pour utiliser le script, vous devez respecter les conditions suivantes :

- Des autorisations d’administrateur sont requises pour exécuter le script sur l’appareil.

- Le capteur doit déjà avoir découvert l’appareil Windows. Cela signifie que si l’appareil existe déjà, le script récupère ses informations.

- Un capteur surveille le réseau auquel le PC Windows est connecté.

### <a name="acquire-the-script"></a>Acquérir le script

Pour recevoir le script, [contactez le support technique](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>Déployer le script

Vous pouvez déployer le script une seule fois ou planifier des requêtes en continu à l’aide des méthodes et des outils de déploiement automatisés standard.

### <a name="about-the-script"></a>À propos du script

- Le script est exécuté en tant qu’utilitaire et n’est pas un programme installé. L’exécution du script n’affecte pas le point de terminaison.

- Les fichiers générés par le script restent sur le lecteur local jusqu’à ce que vous les supprimiez.

- Les fichiers générés par le script sont situés les uns à côté des autres. Ne les séparez pas.

- Si vous réexécutez le script dans le même emplacement, ces fichiers sont remplacés.

Pour exécuter le script :  

1. Copiez le script sur un lecteur local et décompressez-le. Les fichiers suivants s’affichent :

    - start.bat

    - settings.json

    - data.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Vue des fichiers dans l’Explorateur de fichiers.":::

2. Exécutez le fichier `run.bat`.

3. Une fois le Registre interrogé, le fichier de capture instantanée CX s’affiche avec les informations du Registre.

4. Le nom de fichier indique le nom du système et la date et l’heure de l’instantané. Un exemple de nom de fichier est `CX-snaphot_SystemName_Month_Year_Time`.

### <a name="import-device-details"></a>Importer les détails de l’appareil

Les informations découvertes sur chaque point de terminaison doivent être importées dans le capteur.

Les fichiers générés à partir des requêtes peuvent être placés dans un dossier auquel vous pouvez accéder à partir de capteurs. Utilisez des outils et des méthodes standard et automatisés pour déplacer les fichiers de chaque point de terminaison Windows vers l’emplacement où vous les importerez dans le capteur.

Ne mettez pas à jour les noms de fichiers.

Pour importer :

1. Sélectionnez **Importer les paramètres** à partir de la boîte de dialogue **Importer la configuration Windows**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importez vos configurations Windows.":::

2. Sélectionnez **Ajouter**, puis sélectionnez tous les fichiers (Ctrl + A).

3. Sélectionnez **Fermer**. Les informations de Registre de l’appareil sont importées. En cas de problème lors du chargement de l’un des fichiers, vous êtes informé du fichier qui n’a pas pu être chargé.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Le chargement des fichiers ajoutés a réussi.":::

## <a name="view-and-delete-inactive-devices-from-the-inventory"></a>Afficher et supprimer les appareils inactifs d’un inventaire

Vous souhaiterez peut-être afficher les appareils de votre réseau qui ont été inactifs et les supprimer.
Les appareils peuvent devenir inactifs à cause :
- De ports SPAN mal configurés
- De modifications de la couverture réseau
- Du débranchement à partir du réseau

La suppression des appareils inactifs vous aide à :

- Defender pour IoT à créer une représentation plus précise de l’activité réseau actuelle
- Mieux évaluer les appareils validés lors de la gestion des abonnements
- Réduire l’encombrement sur votre écran

### <a name="view-inactive-devices"></a>Afficher les appareils inactifs

Vous pouvez filtrer l’inventaire pour afficher les appareils inactifs :

- pendant 7 jours ou plus
- pendant 14 jours ou plus
- 30 jours ou plus
- 90 jours ou plus

**Pour filtrer l’inventaire :**
  
1. Sélectionnez l’icône du filtre **Dernier écran** dans l’inventaire.
1. Sélectionnez une option de filtre.
1. Sélectionnez **Appliquer**.

### <a name="delete-inactive-devices"></a>Supprimer les appareils inactifs

Les appareils que vous supprimez de l’inventaire sont supprimés du mappage et ne sont pas calculés lors de la génération de rapports Defender pour IoT, par exemple l’exploration de données, l’évaluation des risques et les rapports de vecteurs d’attaque.

Vous serez invité à enregistrer la raison de la suppression des appareils. Ces informations, ainsi que la date/heure et le nombre d’appareils supprimés, s’affichent dans la chronologie des événements.

**Pour supprimer des appareils de l’inventaire :**
  
1. Sélectionnez l’icône du filtre **Dernier écran** dans l’inventaire.
1. Sélectionnez une option de filtre.
1. Sélectionnez **Appliquer**.
1. Sélectionnez **Supprimer les appareils inactifs**.
1. Dans la boîte de dialogue de confirmation qui s’ouvre, entrez la raison de la suppression, puis sélectionnez **Supprimer**. Tous les appareils détectés dans la plage du filtre seront supprimés. Si vous supprimez un grand nombre d’appareils, le processus de suppression peut prendre quelques minutes.

## <a name="export-device-inventory-information"></a>Exporter les informations de l’inventaire des appareils

Vous pouvez exporter les informations de l’inventaire des appareils vers un fichier Excel.

Pour exporter un fichier CSV :

- Dans le menu supérieur droit de l’inventaire des appareils, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false":::. Le rapport CSV est généré et téléchargé.

## <a name="see-also"></a>Voir aussi

[Examiner toutes les détections de capteur de l’entreprise d’un inventaire d’appareils](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Utiliser les vues de plan de site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
