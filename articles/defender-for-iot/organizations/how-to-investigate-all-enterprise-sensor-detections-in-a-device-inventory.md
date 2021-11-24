---
title: En savoir plus sur les appareils découverts par tous les capteurs d’entreprise
description: Utilisez l’inventaire des appareils dans la console de gestion locale pour obtenir une vue complète des informations d’appareil obtenues à partir des capteurs connectés. Utilisez les outils d’importation, d’exportation et de filtrage pour gérer ces informations.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 0025016dd449bf4083c707d4a6db5cd25bf66cfd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343664"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Examiner toutes les détections de capteur d’entreprise dans l’inventaire des appareils

Vous pouvez afficher les informations d’appareil à partir de capteurs connectés à l’aide de l’*inventaire des appareils* dans la console de gestion locale. Cette fonctionnalité vous offre une vue complète de toutes les informations réseau. Utilisez les outils d’importation, d’exportation et de filtrage pour gérer ces informations. Les informations d’état relatives aux versions des capteurs connectés s’affichent également.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Capture d’écran de la table de données de l’inventaire des appareils.":::

Le tableau suivant décrit les colonnes de table dans l’inventaire des appareils.

| Paramètre | Description |
|--|--|
| **Alertes sans accusé de réception** | Nombre d’alertes non gérées associées à cet appareil. |
| **Unité commerciale** | Unité commerciale qui contient cet appareil. |
| **Région** | Région qui contient cet appareil. |
| **Site** | Site qui contient cet appareil. |
| **Zone** | Zone qui contient cet appareil. |
| **Appliance** | Le capteur Microsoft Defender pour IoT qui protège cet appareil. |
| **Nom** | Nom de cet appareil tel que Defender pour IoT l’a découvert. |
| **Type** | Type d’appareil, tel que PLC ou IHM. |
| **Fournisseur** | Nom du fournisseur de l’appareil, tel que défini dans l’adresse MAC. |
| **Système d’exploitation** | Système d’exploitation de l’appareil. |
| **Microprogramme** | Microprogramme de l’appareil. |
| **Adresse IP** | Adresse IP de l’appareil. |
| **VLAN** | VLAN de l’appareil. |
| **Adresse MAC** | Adresse MAC de l’appareil. |
| **Protocoles** | Protocoles utilisés par l’appareil. |
| **Alertes sans accusé de réception** | Nombre d’alertes non gérées associées à cet appareil. |
| **Est autorisé** | État d’autorisation de l’appareil :<br />- **True** : L’appareil a été autorisé.<br />- **False** : L’appareil n’a pas été autorisé. |
| **Est considéré comme un scanneur** | Indique si cet appareil effectue des activités de type analyse dans le réseau. |
| **Est un appareil de programmation** | S’il s’agit d’un appareil de programmation :<br />- **True** : L’appareil effectue des activités de programmation pour les PLC, RTU et contrôleurs qui sont pertinentes pour les stations d’ingénierie.<br />- **False** : L’appareil n’est pas un appareil de programmation. |
| **Groupes** | Groupes auxquels cet appareil participe. |
| **Dernière activité** | Dernière activité effectuée par l’appareil. |
| **Discovered** | Lorsque cet appareil a été détecté pour la première fois sur le réseau. |
| **Mode PLC (préversion)** | Le mode de fonctionnement PLC comprend l’état Clé (physique) et l’état Exécution (logique). Les états **Clés** possibles incluent Exécuter, Programme, Distant, Arrêter, Non valide, Programmation, Désactivé. Les états **Exécution** possibles sont Exécuter, Programmer, Arrêter, Suspendu, Exception, Arrêté, Intercepté, Inactif, Hors connexion. Si les deux états sont identiques, seul l’état OE est présenté. |

## <a name="what-is-an-inventory-device"></a>Qu’est-ce qu’un appareil d’inventaire ?

L’inventaire d’appareil Defender pour IoT affiche un large éventail d’attributs d’appareils qui sont détectés par les capteurs qui contrôlent les réseaux organisationnels et les points de terminaison gérés. Defender pour IoT identifie et classe les appareils comme un seul appareil réseau unique dans l’inventaire pour :

1. Appareils IT, OT et IoT autonomes (avec 1 ou plusieurs cartes d’interface réseau)
1. Appareils composés de plusieurs composants de carte d’insertion (notamment tous les racks/emplacements/modules)
1. Appareils agissant comme une infrastructure réseau comme commutateur/routeur (avec plusieurs cartes d’interface réseau). 

Les adresses IP Internet publiques, les groupes de multidiffusion et les groupes de diffusion ne sont pas considérés comme des appareils d’inventaire.
Les appareils qui ont été inactifs depuis plus de 60 jours sont classés en tant qu’appareils d’inventaire inactifs.

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Intégrer des données dans l’inventaire des appareils de l’entreprise

Les capacités d’intégration de données vous permettent d’améliorer les données de l’inventaire des appareils avec des informations provenant d’autres ressources de l’entreprise. Ces sources incluent les CMDB, le DNS, des pare-feu et des API web.

Vous pouvez utiliser ces informations pour en savoir plus. Par exemple :

- Dates d’achat des appareils et dates de fin de garantie

- Utilisateurs responsables de chaque appareil

- Tickets ouverts pour les appareils

- Date de la dernière mise à niveau du microprogramme

- Appareils autorisés à accéder à Internet

- Appareils exécutant des applications antivirus actives

- Utilisateurs connectés aux appareils

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Table de données sur l’écran d’inventaire des appareils.":::

Vous pouvez intégrer les données de l’une des deux façons suivantes :

- Les ajouter manuellement

- Exécuter des scripts personnalisés fournis par Defender pour IoT

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagramme de l’intégrateur de données d’entreprise.":::

Vous pouvez travailler avec l’équipe du support technique de Defender pour IoT pour configurer votre système afin qu’il reçoive des requêtes d’API web.

Pour ajouter des données manuellement :

1. Dans le menu latéral, sélectionnez **Inventaire des appareils** puis sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false":::.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Modifiez les paramètres d’inventaire de votre appareil.":::

2. Dans la boîte de dialogue **Paramètres d’inventaire des appareils**, sélectionnez **AJOUTER UNE COLONNE PERSONNALISÉE**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Ajouter une colonne personnalisée à votre inventaire.":::

3. Dans la boîte de dialogue **Ajouter une colonne personnalisée**, ajoutez le nom de la nouvelle colonne (jusqu’à 250 caractères UTF), sélectionnez **Manuel**, puis sélectionnez **Enregistrer**. Le nouvel élément apparaît dans la boîte de dialogue **Paramètres d’inventaire des appareils**.

4. Dans le coin supérieur droit de la fenêtre **Inventaire des appareils**, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, puis **Exporter tout l’inventaire des appareils**. Le fichier CSV est généré.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Fichier CSV exporté.":::

5. Ajoutez manuellement les informations à la nouvelle colonne et enregistrez le fichier.

6. Dans le coin supérieur droit de la fenêtre **Inventaire des appareils**, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, sélectionnez **Importer les colonnes d’entrée manuelles**, puis accédez au fichier CSV. Les nouvelles données s’affichent dans le tableau **Inventaire des appareils**.

Pour intégrer des données provenant d’autres entités de l’entreprise :

1. Dans le coin supérieur droit de la fenêtre **Inventaire des appareils**, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, puis **Exporter tout l’inventaire des appareils**.

2. Dans la boîte de dialogue **Paramètres d’inventaire des appareils**, sélectionnez **AJOUTER UNE COLONNE PERSONNALISÉE**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Ajouter une colonne personnalisée à votre inventaire.":::

3. Dans la boîte de dialogue **Ajouter une colonne personnalisée**, ajoutez le nom de la nouvelle colonne (jusqu’à 250 caractères UTF), puis sélectionnez **Automatique**. Les options **CHARGER UN SCRIPT** et **TESTER UN SCRIPT** s’affichent.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Ajoutez automatiquement des colonnes personnalisées.":::

4. Chargez et testez le script que vous avez reçu de [Support Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Récupérer des informations de l’inventaire des appareils

Vous pouvez récupérer un large éventail d’informations d’appareil détectées par des capteurs gérés et intégrer ces informations à des systèmes partenaires. Par exemple, vous pouvez récupérer les informations sur le capteur, la zone, l’ID de site, l’adresse IP, l’adresse MAC, le microprogramme, le protocole et le fournisseur. Filtrez les informations que vous récupérez en fonction des éléments suivants :

- Appareils autorisés et non autorisés.

- Appareils associés à des sites spécifiques.

- Appareils associés à des zones spécifiques.

- Appareils associés à des capteurs spécifiques.

Utilisez les commandes de l’API Defender pour IoT pour récupérer et intégrer ces informations. Pour plus d’informations, consultez [Capteur d’API Defender pour IoT et API de la console de gestion](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrer l’inventaire des appareils

Vous pouvez filtrer l’inventaire des appareils pour afficher les colonnes qui vous intéressent. Par exemple, vous pouvez afficher les informations sur les appareils PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Capture d’écran de l’inventaire des appareils.":::

Le filtre est effacé lorsque vous quittez la fenêtre.

Pour utiliser le même filtre plusieurs fois, vous pouvez enregistrer un filtre ou une combinaison de filtres dont vous avez besoin. Vous pouvez ouvrir un volet gauche et visualiser les filtres que vous avez enregistrés :

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Écran de l’inventaire des appareils.":::

Pour filtrer l’inventaire des appareils :

1. Dans la colonne que vous souhaitez filtrer, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false":::.

2. Dans la boîte de dialogue **Filtre**, sélectionnez le type de filtre :

   - **Est égal à** : Valeur exacte sur laquelle vous souhaitez filtrer la colonne. Par exemple, si vous filtrez la colonne de protocole en fonction de **Est égal à** et `value=ICMP`, la colonne présentera les appareils qui utilisent uniquement le protocole ICMP.

   - **Contient** : Valeur contenue parmi les autres valeurs de la colonne. Par exemple, si vous filtrez la colonne de protocole en fonction de **Contient** et `value=ICMP`, la colonne présente les appareils qui utilisent le protocole ICMP parmi la liste des protocoles utilisés par l’appareil.

3. Pour organiser les informations de colonne par ordre alphabétique, sélectionnez :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::. Réorganisez l’ordre en sélectionnant les flèches :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: et :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false":::.

4. Pour enregistrer un nouveau filtre, définissez le filtre et sélectionnez **Enregistrer sous**.

5. Pour modifier les définitions de filtre, modifiez les définitions et sélectionnez **Enregistrer les modifications**.

## <a name="next-steps"></a>Étapes suivantes

[Examiner toutes les détections de capteur d’un inventaire d’appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)
