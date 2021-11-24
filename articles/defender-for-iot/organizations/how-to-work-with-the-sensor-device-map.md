---
title: Utiliser la carte des appareils de capteur
description: La carte des appareils fournit une représentation graphique des périphériques réseau détectés. Utilisez la carte pour analyser et gérer les informations sur les appareils et les sections réseau, et pour générer des rapports.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 559c3691ac83fa031c71b563bd43dce4fa77c31e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315441"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Examiner les détections de capteur dans la carte des appareils

La carte des appareils fournit une représentation graphique des périphériques réseau détectés. Utilisez la carte pour :

  - Récupérer, analyser et gérer les informations de l’appareil.

  - Analyser des sections de réseau, par exemple des groupes d’intérêt ou des couches Purdue spécifiques.

  - Générer des rapports, par exemple pour exporter les détails et les résumés des appareils.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Capture d’écran de la carte des appareils.":::

Pour accéder à la carte :

  - Sélectionnez **Carte des appareils** depuis l’écran principal de la console.

## <a name="map-search-and-layout-tools"></a>Outils de recherche et de disposition de cartes

Les outils suivants sont utilisés pour travailler dans la carte.

Votre rôle utilisateur détermine les outils disponibles dans la fenêtre de la carte des appareils. Pour plus d’informations sur les rôles utilisateur, consultez [Créer et gérer des utilisateurs](how-to-create-and-manage-users.md).

| Symbole | Description |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Effectuez une recherche par adresse IP ou adresse MAC pour un appareil spécifique. Entrez l’adresse IP ou l’adresse MAC dans la zone de texte. La carte affiche l’appareil que vous avez recherché avec les appareils qui y sont connectés. |
| Mettre en surbrillance et filtrer les groupes <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Capture d’écran de la mise en surbrillance et des filtres du groupe."::: | Filtrez ou mettez en surbrillance la carte en fonction de groupes d’appareils (groupes par défaut et personnalisés). |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Vue avec réduction des éléments informatiques, pour activer une vue ciblée sur les appareils OT et regrouper les appareils informatiques.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Conservez la disposition actuelle des appareils dans la carte. Par exemple, si vous faites glisser des appareils vers de nouveaux emplacements sur la carte, ceux-ci restent à ces emplacements lors de la sortie de la carte. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Ajuster à l’écran |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | - Affichez la couche Purdue identifiée pour cet appareil, y compris l’automatisation, le contrôle de processus, le superviseur et l’entreprise <br /> - Affichez les connexions entre les appareils.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Affichez ou masquez entre diffusion et multidiffusion. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtrez les appareils sur la carte en fonction de l’heure de leur dernière communication avec d’autres appareils. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="notifications" border="false"::: | Affichez les notifications relatives à un appareil. Par exemple, si une nouvelle adresse IP a été détectée pour un appareil à l’aide d’une adresse MAC existante |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Export" border="false"::: | Exportez/Importez les informations de l’appareil. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Affichez les propriétés des appareils sélectionnés. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Zoom avant" border="false"::: ou :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Zoom arrière" border="false"::: | Effectuez un zoom avant ou arrière sur les appareils de la carte. |

## <a name="view-ot-elements-only"></a>Afficher uniquement les éléments OT

Par défaut, les appareils informatiques sont automatiquement regroupés par sous-réseau, ainsi la vue cartographique est axée sur les réseaux OT et ICS. La présentation des éléments du réseau informatique est réduite à un minimum, ce qui limite le nombre total d’appareils présentés sur la carte et fournit une image claire des éléments réseau OT et ICS.

Chaque sous-réseau est présenté sous la forme d’une entité unique sur la carte des appareils, avec une fonctionnalité de réduction et d’extension interactive pour examiner les détails d’un sous-réseau informatique.

La figure ci-dessous illustre un sous-réseau informatique réduit avec 27 éléments de réseau informatique.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Sous-réseau informatique réduit avec 27 éléments de réseau informatique":::

Pour activer la fonctionnalité de réduction des réseaux informatiques :

- Dans la fenêtre **Paramètres système**, assurez-vous que la capacité Activer/désactiver le regroupement des réseaux informatiques est activée.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Fenêtre des paramètres système":::

Pour développer un sous-réseau informatique :

1. Pour faire la distinction entre les réseaux informatiques et OT, dans l’écran Paramètres système, sélectionnez **Sous-réseaux**.

   > [!NOTE]
   > Il est recommandé de nommer chaque sous-réseau avec des noms explicites au niveau de l’utilisateur, ce qui permet de différencier facilement les réseaux informatiques et les réseaux OT.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Configuration des sous-réseaux":::

2. Dans la fenêtre **Modifier la configuration des sous-réseaux**, décochez la case **Sous-réseau ICS** pour chaque sous-réseau que vous souhaitez définir en tant que sous-réseau informatique. Les sous-réseaux informatiques apparaissent réduits dans la carte des appareils avec les notifications pour les appareils ICS, comme les contrôleurs ou PLC, sur les réseaux informatiques.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Modifier la configuration des sous-réseaux":::

3. Pour développer le réseau informatique sur la carte, dans la fenêtre Appareils, cliquez avec le bouton droit et sélectionnez **Développer le réseau**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Développez la vue de votre réseau.":::

4. Une fenêtre de confirmation s’affiche, vous informant que la modification de la disposition ne peut pas être rétablie.

5. Sélectionnez **OK**. Les éléments du sous-réseau informatique apparaissent sur la carte.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Pour réduire un sous-réseau informatique :

1.  Dans le volet gauche, sélectionnez **Runbooks**.

2. Dans la fenêtre Appareils, sélectionnez l’icône Réduire. Le nombre en rouge indique le nombre de sous-réseaux informatiques développés actuellement affichés sur la carte.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Fenêtre Périphérique":::

3. Sélectionnez le ou les sous-réseaux que vous souhaitez réduire, ou sélectionnez **Réduire tout**. Le sous-réseau sélectionné apparaît réduit sur la carte.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Réduire tout":::

L’icône de réduction est mise à jour avec le nombre de sous-réseaux informatiques développés mis à jour.

## <a name="view-or-highlight-device-groups"></a>Afficher ou mettre en surbrillance des groupes d’appareils

Vous pouvez personnaliser l’affichage de la carte en fonction des groupes d’appareils. Par exemple, des groupes d’appareils associés à un protocole OT, à un VLAN ou à sous-réseau spécifique. Des groupes prédéfinis sont disponibles et des groupes personnalisés peuvent être créés.

Affichez les groupes par :

  - **Mise en surbrillance :** Mettez en surbrillance les appareils qui appartiennent à un groupe spécifique en bleu.

  - **Filtrage :** Affiche uniquement les appareils qui appartiennent à un groupe spécifique.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Vue standard de votre port":::

Les groupes prédéfinis suivants sont disponibles :

| Nom du groupe | Description |
|--|--|
| **Applications connues** | Appareils qui utilisent des ports réservés, tels que TCP.  |
| **Ports non standard (par défaut)** | Appareils qui utilisent des ports non standard ou des ports auxquels aucun alias n’a été attribué. |
| **Protocoles OT (par défaut)** | Appareils qui gèrent le trafic OT connu. |
| **Autorisation (par défaut)** | Appareils qui ont été découverts sur le réseau pendant le processus d’apprentissage ou qui ont été officiellement autorisés sur le réseau. |
| **Filtres d’inventaire des appareils** | Appareils regroupés en fonction des filtres enregistrés dans le tableau d’inventaire des appareils. |
| **Fréquences d’interrogation** | Appareils regroupés par intervalles d’interrogation. Les intervalles d’interrogation sont générés automatiquement en fonction de canaux cycliques ou de périodes. Par exemple, 15,0 secondes, 3,0 secondes, 1,5 seconde ou n’importe quel intervalle. L’examen de ces informations vous aide à déterminer si les systèmes sont interrogés trop rapidement ou lentement. |
| **Programmation** | Stations d’ingénierie et machines de programmation. |
| **Sous-réseaux** | Appareils appartenant à un sous-réseau spécifique. |
| **VLAN** | Appareils associés à un ID de VLAN spécifique. |
| **Connexions entre sous-réseaux** | Appareils qui communiquent d’un sous-réseau à un autre sous-réseau. |
| **Alertes épinglées** | Appareils pour lesquels l’utilisateur a épinglé une alerte. |
| **Simulations de vecteurs d’attaque** | Appareils vulnérables détectés dans les rapports de vecteur d’attaque. Pour afficher ces appareils sur la carte, activez la case **Afficher sur la carte des appareils** lors de la génération du vecteur d’attaque. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Ajouter des simulations de vecteur d’attaque":::. |
| **Dernière consultation** | Appareils regroupés selon la période de temps pendant laquelle ils ont été vus pour la dernière fois, par exemple : Une heure, six heures, un jour, sept jours. |
| **Non présent dans Active Directory** | Tous les appareils non PLC qui ne communiquent pas avec Active Directory. |

Pour mettre en surbrillance ou filtrer des appareils :

1. Sélectionnez **Carte des appareils** dans le menu latéral.

2. Sélectionnez l’icône de filtre. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. Dans le volet Groupes, sélectionnez le groupe que vous souhaitez mettre en surbrillance ou pour lequel vous souhaitez filtrer les appareils.

4. Sélectionnez **Mettre en surbrillance** ou **Filtrer**. Activez ou désactivez la même sélection pour supprimer la mise en surbrillance ou le filtre.

## <a name="define-custom-groups"></a>Définir des groupes personnalisés

Outre l’affichage des groupes prédéfinis, vous pouvez définir des groupes personnalisés. Les groupes s’affichent dans la carte des appareils, l’inventaire des appareils et les rapports d’exploration de données.

> [!NOTE]
> Vous pouvez également créer des groupes à partir de l’inventaire des appareils.

Pour créer un groupe :

1. Sélectionnez **Appareils** dans le menu latéral. La carte des appareils s’affiche.

1. Sélectionnez :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Paramètres de groupe"::: pour afficher les paramètres du groupe.

1. Sélectionnez :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="groupes"::: pour créer un groupe personnalisé.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Écran Créer un groupe personnalisé":::

1. Ajoutez le nom du groupe, en utilisant jusqu’à 30 caractères.

1. Sélectionnez les appareils pertinents, comme suit :

   - Ajoutez les appareils à partir de ce menu en les sélectionnant dans la liste (sélectionnez le bouton fléché),<br /> Ou, <br /> 
   - Ajoutez les appareils à partir de ce menu en les copiant à partir d’un groupe sélectionné (sélectionnez le bouton fléché)

1. Sélectionnez **Ajouter un groupe** pour ajouter des groupes existants à des groupes personnalisés.

### <a name="add-devices-to-a-custom-group"></a>Ajouter des appareils à un groupe personnalisé

Vous pouvez ajouter des appareils à un groupe personnalisé ou créer un groupe personnalisé avec des appareils.

1. Cliquez avec le bouton droit sur un ou plusieurs appareils sur la carte.

1. Sélectionnez **Ajouter au groupe**.

1. Entrez un nom de groupe dans le champ de groupe et sélectionnez +. Le nouveau groupe s’affiche. Si le groupe existe déjà, l’appareil est ajouté au groupe personnalisé existant.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nom du groupe":::

1. Ajoutez des appareils à un groupe en répétant les étapes 1-3.

## <a name="map-zoom-views"></a>Vues de zoom sur la carte

L’utilisation des vues cartographiques permet d’accélérer les vérifications lors de l’analyse de grands réseaux.

Trois vues détaillées des appareils peuvent être affichées :

  - [Vue aérienne](#birds-eye-view)

  - [Vue par type d’appareil et connexion](#device-type-and-connection-view)

  - [Vue détaillée](#detailed-view)

### <a name="birds-eye-view"></a>Vue aérienne

Cette vue fournit une vue globale des appareils représentés comme suit :

  - Les points rouges indiquent les appareils avec une ou plusieurs alertes

  - Les points en étoile indiquent les appareils marqués comme importants

  - Les points noirs indiquent les appareils sans alertes

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Vue aérienne":::

### <a name="device-type-and-connection-view"></a>Vue par type d’appareil et connexion 

Cette vue présente les appareils représentés sous forme d’icônes sur la carte afin de mettre en évidence les appareils avec des alertes, les types d’appareils et les appareils connectés.

  - Les appareils avec des alertes s’affichent avec un anneau rouge

  - Les appareils sans alertes s’affichent avec un anneau gris

  - Les appareils affichés sous la forme d’une étoile ont été marqués comme importants

L’icône de type d’appareil s’affiche avec les appareils connectés.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="Vue de connexion":::

### <a name="detailed-view"></a>Vue détaillée 

La vue détaillée présente les appareils et les étiquettes et indicateurs d’appareil avec les informations suivantes :

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Vue détaillée":::

### <a name="control-the-zoom-view"></a>Contrôler la vue avec zoom

La vue cartographique affichée dépend du niveau de zoom de la carte. Le basculement entre les vues cartographiques s’effectue en modifiant les niveaux de zoom.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Contrôler la vue avec zoom":::

### <a name="enable-simplified-zoom-views"></a>Activer les vues avec zoom simplifiées

Les administrateurs qui souhaitent que les analystes de la sécurité et les utilisateurs en lecture seule puissent accéder aux vues aérienne, par appareil et par type de connexion doivent activer l’option de vue simplifiée.

Pour activer les vues cartographiques simplifiées :

  - Sélectionnez **Paramètres système**, puis basculez l’option **Vue cartographique simplifiée**.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Vue cartographique simplifiée":::

## <a name="learn-more-about-devices"></a>En savoir plus sur les appareils

Un large éventail d’outils sont disponibles pour en savoir plus sur les appareils à partir de la carte des appareils :

- [Étiquettes et indicateurs d’appareil](#device-labels-and-indicators)

- [Vues rapides d’appareil](#device-quick-views)

- [Afficher et gérer les propriétés de l’appareil](#view-and-manage-device-properties)

- [Afficher les types d’appareils](#view-device-types)

- [Fond de panier](#backplane-properties)

- [Afficher une chronologie des événements pour l’appareil](#view-a-timeline-of-events-for-the-device)

- [Analyser les détails et modifications de programmation](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Étiquettes et indicateurs d’appareil

Les étiquettes et indicateurs suivants peuvent apparaître sur les appareils sur la carte :

| Étiquette de l’appareil | Description |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nom d’hôte IP"::: | Adresse IP et nom d’hôte de l’adresse IP, ou adresses de sous-réseau |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Nombre d’alertes"::: | Nombre d’alertes associées à l’appareil |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Icône de type d’appareil, par exemple stockage, PLC ou historien. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="Appareils regroupés"::: | Nombre d’appareils regroupés dans un sous-réseau d’un réseau informatique. Dans cet exemple, 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="Période d’apprentissage des appareils"::: | Un appareil détecté après la période d’apprentissage et non autorisé en tant que périphérique réseau. |
| Trait plein | Connexion logique entre les appareils |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nouvel appareil"::: | Nouvel appareil découvert une fois l’apprentissage terminé. |

### <a name="device-quick-views"></a>Vues rapides d’appareil

Accédez aux propriétés et connexions de l’appareil à partir de la carte.

Pour ouvrir le menu Propriétés rapides :

  - Sélectionnez le menu Propriétés rapides :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="menu Propriétés rapides":::.

#### <a name="quick-device-properties"></a>Propriétés rapides de l’appareil

Sélectionnez un ou plusieurs appareils lorsque l’écran Propriétés rapides est ouvert pour voir les points importants de ces appareils :

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Propriétés rapides de l’appareil":::

#### <a name="quick-connection-properties"></a>Propriétés rapides de connexion

Sélectionnez une connexion lorsque l’écran Propriétés rapides est ouvert pour afficher les protocoles utilisés dans cette connexion et le moment où ils ont été utilisés pour la dernière fois :

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Propriétés rapides de connexion":::

## <a name="view-and-manage-device-properties"></a>Afficher et gérer les propriétés de l’appareil

Vous pouvez afficher les propriétés d’appareil pour chaque appareil affiché sur la carte. Par exemple, le nom, le type ou le système d’exploitation de l’appareil, ou le microprogramme ou le fournisseur.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Afficher et gérer les propriétés de l’appareil":::

Les informations suivantes peuvent être mises à jour manuellement. Les informations entrées manuellement remplacent les informations découvertes par Defender pour IoT.

  - Nom

  - Type

  - Système d''exploitation

  - Couche Purdue

  - Description

| Élément | Description |
|--|--|
| Informations de base | Les informations de base nécessaires. |
| Nom | Le nom de l'appareil. <br /> Par défaut, le capteur découvre le nom de l’appareil tel qu’il est défini dans le réseau. Par exemple, un nom défini dans le serveur DNS. <br /> Si aucun nom de ce type n’a été défini, l’adresse IP de l’appareil apparaît dans ce champ. <br /> Vous pouvez modifier un nom de périphérique manuellement. Donnez à vos appareils des noms significatifs qui reflètent leurs fonctionnalités. |
| Type | Type d’appareil détecté par le capteur. <br /> Pour plus d’informations, consultez [Afficher les types d’appareils](#view-device-types). |
| Fournisseur | Le fournisseur de l’appareil. Il est déterminé par les caractères au début de l’adresse MAC de l’appareil. Ce champ est en lecture seule. |
| Système d’exploitation | Le système d’exploitation de l’appareil détecté par le capteur. |
| Couche Purdue | La couche Purdue identifiée par le capteur pour cet appareil, y compris : <br /> - Automatique <br /> - Contrôle de processus <br /> - Superviseur <br /> - Entreprise |
| Description | Un champ de texte libre. <br /> Ajoutez plus d’informations sur l’appareil. |
| Attributs | Des informations supplémentaires ont été découvertes sur l’appareil. Par exemple, affichez l’état Clé et Exécution du PLC, l’état sécurisé du PLC ou les informations sur le moment où l’état a changé.  <br /> Les informations sont en lecture seule et ne peuvent pas être mises à jour à partir de la section Attributs. |
| Paramètres | Vous pouvez modifier manuellement les paramètres de l’appareil pour éviter les faux positifs : <br /> - **Appareil autorisé** : Pendant la période d’apprentissage, tous les appareils découverts sur le réseau sont identifiés comme des appareils autorisés. Quand un appareil est découvert après la période d’apprentissage, il apparaît comme un appareil non autorisé par défaut. Vous pouvez modifier cette définition manuellement. <br /> - **Est considéré comme un scanneur** : Activez cette option si vous savez que cet appareil est connu comme étant un scanneur et qu’il est inutile de vous en informer. <br /> - **Appareil de programmation** : Activez cette option si vous savez que cet appareil est connu comme étant un appareil de programmation et qu’il est utilisé pour apporter des modifications de programmation. Son identification en tant qu’appareil de programmation bloquera les alertes relatives aux modifications de programmation provenant de cette ressource. |
| Groupes personnalisés | Groupes personnalisés dans la table des appareils auxquels cet appareil participe. |
| État | La sécurité et l’état d’autorisation de l’appareil : <br /> - L’État est `Secured` s’il n’y a aucune alerte <br /> - Lorsqu’il existe des alertes relatives à l’appareil, le nombre d’alertes s’affiche <br /> -L’état `Unauthorized` s’affiche pour les appareils qui ont été ajoutés au réseau après la période d’apprentissage. Vous pouvez définir manuellement l’appareil comme étant un `Authorized Device` dans les paramètres <br /> - Si l’adresse de cet appareil est définie en tant qu’adresse dynamique, `DHCP` est ajouté à l’état. |


| Réseau | Description |
|--|--|
| Interfaces | Les interfaces d’appareil. Champ en lecture seule. |
| Protocoles | Protocoles utilisés par l’appareil. Champ en lecture seule. |
| Microprogramme | Si les informations de fond de panier sont disponibles, les informations du microprogramme ne seront pas affichées. |
| Adresse | L’adresse IP de l’appareil. |
| Série | Le numéro de série de l’appareil. |
| Adresse du module | Le modèle d’appareil et le numéro ou l’ID de l’emplacement. |
| Modèle | Numéro de modèle de l’appareil. |
| Version du microprogramme | Le numéro de version du microprogramme. |

Pour afficher les informations sur l’appareil :

1. Sélectionnez **Appareils** dans le menu latéral.

2. Cliquez avec le bouton droit sur un appareil, puis sélectionnez **Afficher les propriétés**. La fenêtre Propriétés de l’appareil est affichée.

3. Sélectionnez l’alerte requise en bas de cette fenêtre pour afficher des informations détaillées sur les alertes de cet appareil.

### <a name="view-device-types"></a>Afficher les types d’appareils

Le type d’appareil est automatiquement identifié par le capteur pendant le processus de découverte d’appareil. Vous pouvez modifier le type manuellement.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Afficher les types d’appareils":::

Le tableau suivant présente tous les types dans le système :

| Category | Type d’appareil |
|--|--|
| ICS | Station d’ingénierie <br /> PLC <br />Historien <br />HMI <br />IED <br />Contrôleur DCS <br />RTU <br />Système d’empaquetage industriel <br />Échelle industrielle <br />Robot industriel <br />Emplacement <br />Compteur <br />Lecteur de fréquence variable  <br />Contrôleur de robot <br />Servomoteur <br />Appareil pneumatique <br />Chapiteau |
| IT | Contrôleur de domaine <br />Serveur de base de données <br />Station de travail <br />Serveur <br />Station terminale <br />Stockage <br />Smart Phone <br />Tablette <br />Serveur de sauvegarde |
| IoT | Caméra IP <br />Imprimante  <br />Pointeuse <br />ATM <br />Télévision connectée <br />Console de jeu <br />Enregistreur numérique (DVR) <br />Panneau de configuration de porte <br />Climatisation <br />Thermostat <br />Alarme incendie <br />Éclairage intelligent <br />Commutateur intelligent <br />Détecteur d’incendie <br />Telephone IP <br />Système d’alarme <br />Sirène d’alarme <br />Motion Detector <br />Elevator (Élévateur) <br />Capteur d’humidité <br />Scanneur de codes-barres <br />Alimentation de secours <br />Système de compteur de personnes <br />Intercom <br />Tourniquet |
| Réseau | Point d’accès sans fil <br />Routeur <br />Commutateur <br />Pare-feu <br />Passerelle VPN <br />Serveur NTP <br />Wifi Pineapple <br />Emplacement physique <br />Adaptateur d’E/S <br /> Convertisseur de protocole |

Pour afficher les informations sur l’appareil :

1.  Sélectionnez **Appareils** dans le menu latéral.

2. Cliquez avec le bouton droit sur un appareil, puis sélectionnez **Afficher les propriétés**. La fenêtre Propriétés de l’appareil est affichée.

3. Sélectionnez l’alerte requise pour afficher des informations détaillées sur les alertes de cet appareil.

### <a name="backplane-properties"></a>Propriétés du fond de panier

Si un PLC contient plusieurs modules séparés en racks et emplacements, les caractéristiques peuvent varier entre les cartes de module. Par exemple, si l’adresse IP et l’adresse MAC sont les mêmes, le microprogramme peut être différent.

Vous pouvez utiliser l’option de fond de panier pour passer en revue plusieurs contrôleurs/cartes et leurs appareils imbriqués sous la forme d’une entité avec diverses définitions. Chaque emplacement de la vue du fond de panier représente les appareils sous-jacents, c’est-à-dire ceux qui ont été détectés derrière lui.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Propriétés du fond de panier":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Propriétés de l’appareil de fond de panier":::

Un fond de panier peut contenir jusqu’à 30 cartes de contrôleur et jusqu’à 30 unités en rack. Le nombre total d’appareils inclus dans les différents niveaux peut atteindre jusqu’à 200 appareils.

Le volet Fond de panier s’affiche dans la fenêtre Propriétés de l’appareil lorsque des détails de fond de panier sont détectés.

Chaque emplacement apparaît avec le nombre d’appareils sous-jacents et l’icône qui indique le type de module.

| Icône | Type de module |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Alimentation"::: | Alimentation |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="E/S analogiques"::: | E/S analogiques |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adaptateur de communication"::: | Adaptateur de communication |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="E/S numériques"::: | E/S numériques |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="UC"::: | UC |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Générique"::: | Générique |

Lorsque vous sélectionnez un emplacement, les détails de l’emplacement s’affichent :

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Sélectionner un emplacement":::

Pour afficher les appareils sous-jacents situés derrière l’emplacement, sélectionnez **Afficher sur la carte**. L’emplacement est présenté dans la carte des appareils avec tous les modules et périphériques sous-jacents qui y sont connectés.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="AFFICHER SUR LA CARTE":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Afficher une chronologie des événements pour l’appareil

Affichez une chronologie des événements associés à un appareil.

Pour afficher la chronologie :

1. Cliquez avec le bouton droit sur un appareil de la carte.

2. Sélectionnez **Afficher les événements**. La fenêtre Chronologie des événements s’ouvre et affiche des informations sur les événements détectés pour l’appareil sélectionné.

Pour plus d’informations, consultez [Chronologie des événements](#event-timeline).

## <a name="analyze-programming-details-and-changes"></a>Analyser les détails et modifications de programmation

Améliorez les investigations en affichant les événements de programmation effectués sur vos appareils réseau et en analysant les modifications de code. Ces informations vous aident à découvrir les activités de programmation suspectes, par exemple :

  - Erreur humaine : Un ingénieur programme le mauvais appareil.

  - Automatisation de la programmation endommagée : La programmation est exécutée par erreur en raison d’un échec d’automatisation.

  - Systèmes piratés : Utilisateurs non autorisés connectés à un appareil de programmation.

Vous pouvez afficher un appareil programmé et faire défiler les différentes modifications de programmation effectuées par d’autres appareils.

Affichez le code qui a été ajouté, modifié, supprimé ou rechargé par l’appareil de programmation. Recherchez des modifications de programmation en fonction des types de fichiers, des dates ou des heures qui vous intéressent.

### <a name="when-to-review-programming-activity"></a>Quand passer en revue l’activité de programmation 

Vous pourriez avoir à passer en revue l’activité de programmation :

  - Après l’affichage d’une alerte concernant une programmation non autorisée

  - Après une mise à jour planifiée des contrôleurs

  - Quand un processus ou un ordinateur ne fonctionne pas correctement (pour voir qui a effectué la dernière mise à jour et à quel moment)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Journal des modifications de programmation":::

Autres options disponibles :

  - Marquer les événements d’intérêt avec une étoile.

  - Télécharger un fichier *.txt avec le code actuel.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>À propos des événements de programmation autorisés et non autorisés 

Les événements de programmation non autorisée sont effectués par des appareils qui n’ont pas été découverts ou définis manuellement comme appareils de programmation. Les événements de programmation autorisés sont effectués par des appareils résolus ou définis manuellement comme appareils de programmation.

La fenêtre Analyse de programmation affiche les événements de programmation autorisés et non autorisés.

### <a name="accessing-programming-details-and-changes"></a>Accès aux détails et aux modifications de programmation

Accédez à la fenêtre Analyse de programmation à partir des emplacements suivants :

- [Chronologie des événements](#event-timeline)

- [Alertes de programmation non autorisée](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Chronologie des événements

Utilisez la chronologie des événements pour afficher une chronologie des événements dans lesquels des modifications de programmation ont été détectées.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Une vue de la chronologie des événements.":::

### <a name="unauthorized-programming-alerts"></a>Alertes de programmation non autorisée

Des alertes sont déclenchées lorsque des appareils de programmation non autorisés effectuent des activités de programmation.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Alertes de programmation non autorisée":::

> [!NOTE]
> Vous pouvez également afficher des informations de programmation de base dans la fenêtre Propriétés de l’appareil et dans l’inventaire des appareils.

### <a name="working-in-the-programming-timeline-window"></a>Utilisation de la fenêtre de chronologie de programmation

Cette section décrit comment afficher des fichiers de programmation et comparer des versions. Recherchez des fichiers spécifiques envoyés à un appareil programmé. Recherchez des fichiers en fonction des propriétés suivantes :

  - Date

  - Type de fichier

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="Fenêtre de chronologie de programmation":::

|Type de chronologie de programmation | Description |
|--|--|
| Appareil programmé | Fournit des détails sur l’appareil qui a été programmé, y compris le nom d’hôte et le fichier. |
| Événements récents | Affiche les 50 événements les plus récents détectés par le capteur. <br />Pour mettre en surbrillance un événement, pointez dessus et cliquez sur l’étoile. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Les 50 derniers événements peuvent être affichés. |
| Files | Affiche les fichiers détectés pour la date choisie et la taille du fichier sur l’appareil programmé. <br /> Par défaut, le nombre maximal de fichiers disponibles pour l’affichage par appareil est de 300. <br /> Par défaut, la taille de fichier maximale pour chaque fichier est de 15 Mo. |
| État du fichier :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Les étiquettes de fichier indiquent l’état du fichier sur l’appareil, notamment : <br /> **Ajouté** : le fichier a été ajouté au point de terminaison à la date ou l’heure sélectionnée. <br /> **Mis à jour** : Le fichier a été mis à jour à la date ou à l’heure sélectionnée. <br /> **Supprimé** : Ce fichier a été supprimé. <br /> **Sans étiquette** : Ce fichier n’a pas été modifié.   |
| Appareil de programmation | L’appareil qui a effectué la modification de programmation. Plusieurs appareils peuvent avoir effectué des modifications de programmation sur un appareil programmé. Le nom d’hôte, la date ou l’heure de modification et l’utilisateur connecté sont affichés. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Affiche le fichier en cours d’installation sur l’appareil programmé. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Téléchargez un fichier texte du code affiché. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Comparez le fichier actuel au fichier détecté à la date sélectionnée. |

### <a name="choose-a-file-to-review"></a>Choisir un fichier à examiner

Cette section décrit comment choisir un fichier à examiner.

Pour choisir un fichier à examiner :

1. Sélectionnez un événement dans le volet **Événements récents**

2. Sélectionnez un fichier dans le volet Fichier. Le fichier s’affiche dans le volet actif.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Sélectionnez le fichier à utiliser.":::

### <a name="compare-files"></a>Comparer des fichiers

Cette section décrit comment comparer des fichiers de programmation.

Pour comparer :

1. Sélectionnez un événement dans le volet Événements récents.

2. Sélectionnez un fichier dans le volet Fichier. Le fichier s’affiche dans le volet actif. Vous pouvez comparer ce fichier à d’autres fichiers.

3. Sélectionnez l’indicateur de comparaison.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Indicateur de comparaison":::

   La fenêtre affiche toutes les dates auxquelles le fichier sélectionné a été détecté sur l’appareil programmé. Le fichier peut avoir été mis à jour sur l’appareil programmé par plusieurs appareils de programmation.

   Le nombre de différences détectées apparaît dans le coin supérieur droit de la fenêtre. Vous devrez peut-être faire défiler l’écran pour afficher les différences.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Faites défiler jusqu’à votre sélection":::

   Le nombre est calculé sur la base des lignes adjacentes de texte modifié. Par exemple, si huit lignes consécutives de code ont été modifiées (supprimées, mises à jour ou ajoutées), elles sont calculées comme une seule différence.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Votre vue de chronologie de programmation.":::

4. Sélectionnez une date. Le fichier détecté à la date sélectionnée s’affiche dans la fenêtre.

5. Le fichier sélectionné dans le volet Événements/Fichiers récents s’affiche toujours à droite.

### <a name="device-programming-information-other-locations"></a>Informations de programmation d’appareil : Autres emplacements

En plus de passer en revue les détails de la chronologie de programmation, vous pouvez accéder aux informations de programmation dans la fenêtre Propriétés d’appareil et dans l’inventaire des appareils.

| Type d’appareil | Description |
|--|--|
| Propriétés d’appareil | La fenêtre Propriétés de l’appareil fournit des informations sur le dernier événement de programmation détecté sur l’appareil. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Propriétés de votre appareil"::: |
| L’inventaire des appareils | L’inventaire des appareils indique si l’appareil est un appareil de programmation. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Inventaire des appareils"::: |

## <a name="manage-device-information-from-the-map"></a>Gérer les informations de l’appareil à partir de la carte

Le capteur ne met pas à jour et n’affecte pas les appareils directement sur le réseau. Les modifications apportées ici affectent uniquement la manière dont il analyse l’appareil.

### <a name="delete-devices"></a>Supprimer des appareils

Vous souhaiterez peut-être supprimer un appareil si les informations apprises ne sont pas pertinentes. Par exemple,

  - Un entrepreneur partenaire sur une station de travail d’ingénierie se connecte temporairement pour effectuer des mises à jour de configuration. Une fois la tâche terminée, l’appareil est supprimé.

  - En raison des modifications apportées au réseau, certains appareils ne sont plus connectés.

Si vous ne supprimez pas l’appareil, le capteur continue de l’analyser. Après 60 jours, une notification s’affiche et vous conseille de le supprimer.

Vous pouvez recevoir une alerte indiquant que l’appareil ne répond pas si un autre appareil tente d’y accéder. Dans ce cas, votre réseau est peut-être mal configuré.

L’appareil sera supprimé de la carte des appareils, de l’inventaire des appareils et des rapports d’exploration de données. Les autres informations, comme celles stockées dans les widgets, sont conservées.

L’appareil doit être inactif pendant au moins 10 minutes avant de pouvoir le supprimer.

Pour supprimer un appareil de la carte des appareils :

1. Sélectionnez **Appareils** dans le menu latéral.

2. Cliquez avec le bouton droit sur un appareil, puis sélectionnez **Supprimer**.

### <a name="merge-devices"></a>Fusionner des appareils

Dans certains cas, vous devrez peut-être fusionner des appareils. Cela peut être nécessaire si le capteur a découvert des entités réseau distinctes qui sont associées à un appareil unique. Par exemple,

  - Un PLC avec quatre cartes réseau.

  - Un ordinateur portable avec une carte Wi-Fi et une carte physique.
  
  - Une station de travail avec deux cartes réseau ou plus.

Lors de la fusion, vous indiquez au capteur de combiner les propriétés de deux appareils en un seul. Lorsque vous procédez ainsi, la fenêtre Propriétés de l’appareil et les rapports de capteur sont mis à jour avec les nouveaux détails de propriété de l’appareil.

Par exemple, si vous fusionnez deux appareils, chacun avec une adresse IP, les deux adresses IP s’affichent en tant qu’interfaces distinctes dans la fenêtre Propriétés de l’appareil. Vous ne pouvez fusionner que des appareils autorisés.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Fenêtre Propriétés de l’appareil":::

La chronologie des événements présente l’événement de fusion.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Chronologie des événements avec événements fusionnés.":::

Vous ne pouvez pas annuler une fusion d’appareil. Si vous avez fusionné deux appareils par erreur, supprimez l’appareil et attendez que le capteur redécouvre les deux.

Pour fusionner des appareils :

1. Sélectionnez deux appareils (Maj + clic), puis cliquez avec le bouton droit sur l’un d’eux.

2. Sélectionnez **Fusionner** pour fusionner les appareils. La fusion peut prendre jusqu’à 2 minutes.

3. Dans la boîte de dialogue Définir les attributs de fusion des appareils, choisissez un nom d’appareil.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="Boîte de dialogue Attributs":::

4. Sélectionnez **Enregistrer**.

### <a name="authorize-and-unauthorize-devices"></a>Autoriser et annuler l’autorisation d’appareils

Pendant la période d’apprentissage, tous les appareils découverts sur le réseau sont identifiés comme des appareils autorisés. L’étiquette **Autorisé** n’apparaît pas sur ces appareils dans la carte des appareils.

Quand un appareil est découvert après la période d’apprentissage, il apparaît comme un appareil non autorisé. En plus de voir les appareils non autorisés sur la carte, vous pouvez également les voir dans l’inventaire des appareils.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventaire des appareils":::

**Appareil nouveau ou non autorisé**

Les nouveaux appareils détectés après la période d’apprentissage s’affichent avec les étiquettes `New` et `Unauthorized`.

Si vous déplacez un appareil sur la carte ou modifiez manuellement les propriétés de l’appareil, l’étiquette `New` est supprimée de l’icône de l’appareil.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Appareils non autorisés : vecteurs d’attaque et rapports d’évaluation des risques

Les appareils non autorisés sont inclus dans les rapports d’évaluation des risques et de vecteurs d’attaque.

- **Rapports de vecteur d’attaque :** Les appareils marqués comme non autorisés sont résolus dans le vecteur d’attaque en tant qu’appareils non autorisés soupçonnés d’être malveillants qui peuvent constituer une menace pour le réseau.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Afficher vos rapports de vecteur d’attaque":::

- **Rapports d’évaluation des risques :** Les appareils marqués comme non autorisés sont :

  - Identifiés dans les rapports d’évaluation des risques

Pour autoriser ou annuler l’autorisation des appareils manuellement :

1. Cliquez avec le bouton droit sur l’appareil sur la carte et sélectionnez **Non autorisé**

### <a name="mark-devices-as-important"></a>Marquer les appareils comme importants

Vous pouvez marquer les périphériques réseau importants comme tels, par exemple les serveurs essentiels pour l’entreprise. Ces appareils sont marqués d’une étoile sur la carte. L’étoile varie en fonction du niveau de zoom sur la carte.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Appareils importants : vecteurs d’attaque et rapports d’évaluation des risques

Les appareils importants sont calculés lors de la génération de rapports d’évaluation des risques et de rapports de vecteur d’attaque.

  - Rapports de vecteur d’attaque : les appareils signalés comme importants sont résolus dans le vecteur d’attaque en tant que cibles d’attaque. 

  - Rapports d’évaluation des risques : Les appareils marqués comme importants sont calculés lors de la fourniture du score de sécurité dans le rapport d’évaluation des risques.

## <a name="generate-activity-reports-from-the-map"></a>Générer des rapports d’activité à partir de la carte

Générez un rapport d’activité pour un appareil sélectionné sur une période de 1, 6, 12 ou 24 heures. Les informations suivantes sont disponibles :

  - Catégorie : Informations de détection de base basées sur les scénarios de trafic.

  - Appareils source et de destination

  - Données : Informations supplémentaires désinscrites.

  - Heure et date de la dernière consultation.

Vous pouvez enregistrer le rapport sous la forme d’un fichier Microsoft Excel ou Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Activités récentes":::

Pour générer un rapport d’activité pour un appareil :

1. Cliquez avec le bouton droit sur un appareil de la carte.

2. Sélectionnez un rapport d’activité.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Affichez un rapport de votre activité.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Générer des rapports de vecteur d’attaque à partir de la carte

Simulez un rapport de vecteur d’attaque pour savoir si un appareil sur la carte que vous sélectionnez est une cible d’attaque vulnérable.

Les rapports de vecteur d’attaque fournissent une représentation graphique d’une chaîne de vulnérabilité d’appareils exploitables. Ces vulnérabilités peuvent permettre à une personne malveillante d’accéder à des périphériques réseau clés. Le simulateur de vecteur d’attaque calcule les vecteurs d’attaque en temps réel et analyse tous les vecteurs d’attaque par cible spécifique.

Pour afficher un appareil dans un rapport de vecteur d’attaque :

1. Cliquez avec le bouton droit sur un appareil de la carte.

2. Sélectionnez **Simuler les vecteurs d’attaque**. La boîte de dialogue Vecteur d’attaque s’ouvre avec l’appareil que vous sélectionnez comme cible d’attaque.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Ajouter une simulation de vecteur d’attaque":::

3. Ajoutez les paramètres restants à la boîte de dialogue et sélectionnez **Ajouter une simulation**.

## <a name="export-device-information-from-the-map"></a>Exporter les informations de l’appareil à partir de la carte

Exportez les informations d’appareil suivantes à partir de la carte.

  - Détails de l’appareil (Microsoft Excel)

  - Un résumé de l’appareil (Microsoft Excel)

  - Un fichier Word avec des groupes (Microsoft Word)

Pour exporter :

1. Sélectionnez l’icône Exporter dans la carte.

1. Sélectionnez une option d’exportation.

## <a name="see-also"></a>Voir aussi

[Examiner les détections de capteur dans un inventaire des appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)
