---
title: Importer les informations des appareils
description: Les capteurs Defender pour IoT surveillent et analysent le trafic en miroir. Dans ces cas, vous pouvez importer des données pour enrichir les informations sur les appareils déjà détectés.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 3446a82ec71b03c8e1647b6f95db937e7ef7f488
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293247"
---
# <a name="import-device-information-to-a-sensor"></a>Importer des informations d’appareil sur un capteur

Un capteur Microsoft Defender pour IoT surveille et analyse le trafic en miroir. Dans certains cas, en raison des stratégies de configuration réseau propres à l’organisation, certaines informations peuvent ne pas être transmises.

Dans ces cas, vous pouvez importer des données pour enrichir les informations sur les appareils déjà détectés. Deux options sont disponibles pour importer des informations dans les capteurs :

- **Importer à partir de la carte** : Mettez à jour le nom, le type ou le groupe de l’appareil ou la couche Purdue sur la carte.

- **Importer à partir des paramètres d’importation** : Importez le système d’exploitation de l’appareil, l’adresse IP, le niveau de patch ou l’état d’autorisation.

## <a name="import-from-the-map"></a>Importer à partir de la carte

Cette section décrit comment importer des noms, des types ou des groupes d’appareils ou des couches Purdue dans la carte des appareils. Vous effectuez cette opération à partir de la carte.

Voici les conditions requises pour l’importation :

- **Noms** : Peut comprendre jusqu’à 30 caractères.

- **Type** ou **Couche Purdue** : Utilisez les options qui s’affichent dans la boîte de dialogue **Propriétés de l’appareil**. (Cliquez avec le bouton droit sur l’appareil, puis sélectionnez **Afficher les propriétés**.)

- **Groupe d’appareils** : Créez un nouveau groupe et attribuez-lui un nom de 30 caractères maximum. 

> [!NOTE]
> Pour éviter les conflits, n’importez pas les données que vous avez exportées d’un capteur à un autre.

Pour importer :

1. Dans le menu latéral, sélectionnez **Appareils**.

2. Dans le coin supérieur droit de la fenêtre **Appareils**, sélectionnez :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Capture d’écran de la fenêtre de l’appareil.":::

3. Sélectionnez **Exporter des appareils**. Un large éventail d’informations s’affiche dans le fichier exporté. Ces informations incluent les protocoles utilisés par l’appareil et l’état d’autorisation de l’appareil.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informations contenues dans le fichier exporté.":::

4. Dans le fichier CSV, modifiez uniquement le nom de l’appareil, le type, le groupe et la couche Purdue. Puis enregistrez le fichier. 

   Utilisez les normes de mise en majuscules indiquées dans le fichier exporté. Par exemple, pour la couche Purdue, utilisez la mise en majuscules de la première lettre.

5. Dans le menu déroulant **Importer/exporter** de la fenêtre **Appareil**, sélectionnez **Importer des appareils**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importez des appareils par l’intermédiaire de la fenêtre de l’appareil.":::

6. Sélectionnez **Importer des appareils** et sélectionnez le fichier CSV que vous souhaitez importer. Les messages d’état de l’importation s’affichent à l’écran jusqu’à la fermeture de la boîte de dialogue **Importer des appareils**.

## <a name="import-from-import-settings"></a>Importer à partir des paramètres d’importation

Cette section décrit comment importer l’adresse IP, le système d’exploitation, le niveau de patch ou l’état d’autorisation de l’appareil dans la carte des appareils. Pour ce faire, utilisez la boîte de dialogue **Importer les paramètres**.

Pour importer l’adresse IP, le système d’exploitation et le niveau de patch :

1. Téléchargez le fichier [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) à partir du [centre d’aide](https://cyberx-labs.zendesk.com/hc/en-us) et entrez les informations comme suit :

   - **Adresse IP** : Entrez l’adresse IP de l’appareil.

   - **Système d’exploitation**: Sélectionnez dans la liste déroulante.

   - **Dernière mise à jour** : Utilisez le format AAAA-MM-JJ.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="L’écran Options.":::

2. Dans le menu latéral, sélectionnez **Importer les paramètres**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importez vos paramètres.":::

3. Pour charger la configuration requise, dans la section **Informations sur l’appareil**, sélectionnez **Ajouter** et chargez le fichier CSV que vous avez préparé.

Pour importer l’état de l’autorisation :

1. Téléchargez et enregistrez le fichier [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) à partir du centre d’aide Defender pour IoT. Vérifiez que vous avez enregistré le fichier au format CSV.

2. Entrez les informations suivantes :

   - **Adresse IP** : L’adresse IP de l’appareil.

   - **Nom** : Le nom de l’appareil autorisé. Assurez-vous que les noms sont exacts. Les noms donnés aux appareils dans la liste importée remplacent les noms affichés dans la carte des appareils.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Fichiers Excel avec la liste des appareils importés.":::

3. Dans le menu latéral, sélectionnez **Importer les paramètres**.

4. Dans la section **Appareils autorisés**, sélectionnez **Ajouter** et chargez le fichier CSV que vous avez enregistré.

Lorsque les informations sont importées, vous recevez des alertes d’appareils non autorisés pour tous les appareils qui ne figurent pas dans cette liste.

## <a name="import-device-information-to-the-sensor"></a>Importer des informations d’appareil sur le capteur

Un capteur surveille et analyse le trafic en miroir. Dans certains cas, en raison des stratégies de configuration réseau propres à l’organisation, certaines informations peuvent ne pas être transmises.

Dans ces cas, vous pouvez importer des données pour enrichir les informations d’appareil sur les appareils déjà détectés. Deux options sont disponibles pour importer des informations dans les capteurs :

- **Importer à partir de la carte** : Mettez à jour le nom, le type ou le groupe de l’appareil ou la couche Purdue sur la carte.

- **Importer à partir des paramètres d’importation** : Importez le système d’exploitation de l’appareil, l’adresse IP, le niveau de patch ou l’état d’autorisation.

### <a name="import-from-the-map"></a>Importer à partir de la carte

Cette section décrit comment importer des noms, des types ou des groupes d’appareils ou des couches Purdue dans la carte des appareils. Vous effectuez cette opération à partir de la carte.

Voici les conditions requises pour l’importation :

- **Noms** : Peut comprendre jusqu’à 30 caractères.

- **Type** ou **Couche Purdue** : Utilisez les options qui s’affichent dans la boîte de dialogue **Propriétés de l’appareil**. (Cliquez avec le bouton droit sur l’appareil, puis sélectionnez **Afficher les propriétés**.)

- **Groupe d’appareils** : Créez un nouveau groupe et attribuez-lui un nom de 30 caractères maximum. 

> [!NOTE]
> Pour éviter les conflits, n’importez pas les données que vous avez exportées d’un capteur à un autre.

Pour importer :

1. Dans le menu latéral, sélectionnez **Appareils**.

2. Dans le coin supérieur droit de la fenêtre **Appareils**, sélectionnez :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Fenêtre à partir de laquelle choisir votre appareil.":::

3. Sélectionnez **Exporter des appareils**. Un large éventail d’informations s’affiche dans le fichier exporté. Les exemples incluent les protocoles utilisés par l’appareil et l’état d’autorisation de l’appareil.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Informations contenues dans le fichier exporté.":::

4. Dans le fichier CSV, modifiez uniquement le nom de l’appareil, le type, le groupe et la couche Purdue. Puis enregistrez le fichier. 

   Utilisez les normes de mise en majuscules indiquées dans le fichier exporté. Par exemple, pour la couche Purdue, utilisez la mise en majuscules de la première lettre.

5. Dans le menu déroulant **Importer/exporter** de la fenêtre **Appareil**, sélectionnez **Importer des appareils**.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importez vos appareils.":::

6. Sélectionnez **Importer des appareils** et sélectionnez le fichier CSV que vous souhaitez importer. Les messages d’état de l’importation s’affichent à l’écran jusqu’à la fermeture de la boîte de dialogue **Importer des appareils**.

### <a name="import-from-import-settings"></a>Importer à partir des paramètres d’importation 

Cette section décrit comment importer l’adresse IP, le système d’exploitation, le niveau de patch ou l’état d’autorisation de l’appareil dans la carte des appareils. Pour ce faire, utilisez la boîte de dialogue **Importer les paramètres**.

Pour importer l’adresse IP, le système d’exploitation et le niveau de patch :

1. Téléchargez le fichier [devices_info_2.2.8 and up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) à partir du [centre d’aide](https://cyberx-labs.zendesk.com/hc/en-us) et entrez les informations comme suit :

   - **Adresse IP** : L’adresse IP de l’appareil.

   - **Système d’exploitation**: Sélectionnez dans la liste déroulante.

   - **Date de la dernière mise à jour** : Utilisez le format AAAA-MM-JJ.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="Contenu à l’écran.":::

2. Dans le menu latéral, sélectionnez **Importer les paramètres**.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Écran Renseigner les paramètres d’importation.":::

3. Pour charger la configuration requise, dans la section **Informations sur l’appareil**, sélectionnez **Ajouter** et chargez le fichier CSV que vous avez préparé.

Pour importer l’état de l’autorisation :

1. Téléchargez et enregistrez le fichier [authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) à partir du centre d’aide Defender pour IoT. Vérifiez que vous avez enregistré le fichier au format CSV.

2. Entrez les informations suivantes :

   - **Adresse IP** : L’adresse IP de l’appareil.

   - **Nom** : Le nom de l’appareil autorisé. Vérifiez que les noms sont exacts. Les noms donnés aux appareils dans la liste importée remplacent les noms affichés dans la carte des appareils.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="La liste d’importation dans la carte des appareils.":::

3. Dans le menu latéral, sélectionnez **Importer les paramètres**.

4. Dans la section **Appareils autorisés**, sélectionnez **Ajouter** et chargez le fichier CSV que vous avez enregistré.

Lorsque les informations sont importées, vous recevez des alertes d’appareils non autorisés pour tous les appareils qui ne figurent pas dans cette liste.

## <a name="see-also"></a>Voir aussi

[Contrôler quel trafic est surveillé](how-to-control-what-traffic-is-monitored.md)

[Examiner toutes les détections de capteur d’un inventaire d’appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)
