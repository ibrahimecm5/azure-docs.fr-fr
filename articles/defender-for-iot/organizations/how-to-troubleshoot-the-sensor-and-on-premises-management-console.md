---
title: Résoudre les problèmes du capteur et de la console de gestion locale
description: Dépannez votre capteur et la console de gestion locale pour éliminer les problèmes que vous pourriez rencontrer.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 66e4d9b221176bb8a1413e679656c6df401459dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278642"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Résoudre les problèmes du capteur et de la console de gestion locale

Cet article décrit les outils de dépannage de base pour le capteur et la console de gestion locale. Outre les éléments décrits ici, vous pouvez vérifier l’intégrité de votre système des manières suivantes :

**Alertes** : Une alerte est créée lorsque l’interface du capteur qui surveille le trafic est défaillante.

**SNMP** : L’intégrité du capteur est analysée via protocole SNMP. Microsoft Defender pour IoT répond aux requêtes SNMP envoyées à partir d’un serveur d’analyse autorisé.

**Notifications système** : Quand une console de gestion contrôle le capteur, vous pouvez transférer des alertes concernant les échecs de sauvegarde de capteur et les capteurs déconnectés.

## <a name="sensor-troubleshooting-tools"></a>Outils de dépannage des capteurs

### <a name="investigate-password-failure-at-initial-sign-in"></a>Examiner l’échec du mot de passe lors de la connexion initiale

Lorsque vous vous connectez pour la première fois à un capteur Arrow préconfiguré, vous devez récupérer le mot de passe.

**Pour récupérer votre mot de passe** :

1. Sur l’écran de connexion Defender pour IoT, sélectionnez **Récupération du mot de passe**. L’écran **Récupération du mot de passe** s’ouvre.

1. Sélectionnez **CyberX** ou **Support**, puis copiez l’identificateur unique.

1. Accédez au portail Azure et sélectionnez **Sites et capteurs**.  

1. Sélectionnez le menu déroulant **Autres actions**, puis sélectionnez **Récupérer le mot de passe de la console de gestion locale**.

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text=" Capture d’écran de l’option Récupérer le mot de passe de la console de gestion locale.":::

1. Entrez l’identificateur unique que vous avez reçu sur l’écran **Récupération du mot de passe**, puis sélectionnez **Récupérer**. Le fichier `password_recovery.zip` est téléchargé.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="Capture d’écran de la saisie de l’identificateur unique, puis sélection de l’option de récupération.":::

    > [!NOTE]
    > Ne modifiez pas le fichier de récupération du mot de passe. Il s’agit d’un fichier signé qui ne fonctionnera pas si vous le falsifiez.

1. Sur l’écran **Récupération du mot de passe**, sélectionnez **Charger**. La fenêtre **Charger le fichier de récupération du mot de passe** s’ouvre.

1. Sélectionnez **Parcourir** pour localiser votre fichier `password_recovery.zip` ou faites glisser `password_recovery.zip` sur la fenêtre.

1. Sélectionnez **Suivant**, et votre utilisateur et le mot de passe généré par le système pour votre console de gestion s’affichent alors.

    > [!NOTE]
    > Lorsque vous vous connectez pour la première fois à un capteur ou à une console de gestion locale, il sera lié à l’abonnement avec lequel vous vous êtes connecté. Si vous devez réinitialiser le mot de passe pour l’utilisateur CyberX ou Support, vous devez sélectionner cet abonnement. Pour plus d’informations sur la récupération d’un mot de passe d’utilisateur CyberX ou Support, consultez [Récupérer le mot de passe pour la console de gestion locale ou le capteur](how-to-create-and-manage-users.md#recover-the-password-for-the-on-premises-management-console-or-the-sensor).

### <a name="investigate-a-lack-of-traffic"></a>Examiner un manque de trafic

Un indicateur apparaît en haut de la console lorsque le capteur détecte qu’il n’y a aucun trafic sur l’un des ports configurés. Cet indicateur est visible pour tous les utilisateurs.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Capture d’écran de l’alerte signalant qu’aucun trafic n’a été détecté.":::

Lorsque ce message s’affiche, vous pouvez rechercher l’emplacement où il n’y a aucun trafic. Assurez-vous que le câble SPAN est connecté et que l’architecture SPAN n’a pas été modifiée.  

Pour obtenir des informations de support et de dépannage, contactez [Support Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Vérifier les performances du système.

Quand un nouveau capteur est déployé ou, par exemple, si le capteur fonctionne lentement ou n’affiche aucune alerte, vous pouvez vérifier les performances du système.

**Pour vérifier les performances du système** :

1. Dans le tableau de bord, assurez-vous que `PPS > 0`.

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Capture d’écran d’un exemple de tableau de bord.":::

1. Dans le menu latéral, sélectionnez **Appareils**.

1. Dans la fenêtre **Appareils**, vérifiez que les appareils sont bien détectés.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Capture d’écran des appareils détectés.":::

1. Dans le menu latéral, sélectionnez **Exploration de données**.

1. Dans la fenêtre **Exploration de données**, sélectionnez **TOUT** et générez un rapport.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Capture d’écran de l’écran de génération d’un nouveau rapport à l’aide de l’exploration de données.":::

1. Assurez-vous que le rapport contient des données.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Capture d’écran de l’écran vérifier que le rapport contient des données.":::

1. Dans le menu latéral, sélectionnez **Tendances et statistiques**.

1. Dans la fenêtre **Tendances et statistiques**, sélectionnez **Ajouter un widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Capture d’écran de l’ajout d’un widget en le sélectionnant.":::

1. Ajoutez un widget et assurez-vous qu’il affiche des données.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Capture d’écran du widget affichant les données.":::

1. Dans le menu latéral, sélectionnez **Alertes**. La fenêtre **Alertes** apparaît.

1. Vérifiez que les alertes ont été créées.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Capture d’écran des alertes créées.":::

### <a name="investigate-a-lack-of-expected-alerts-on-the-sensor"></a>Examiner un manque d’alertes attendues sur le capteur

Si la fenêtre **Alertes** n’affiche pas une alerte que vous attendiez, vérifiez les points suivants :

- Vérifiez si la même alerte apparaît déjà dans la fenêtre **Alertes** en réaction à une instance de sécurité différente. Si c’est le cas, et si cette alerte n’a pas encore été traitée, la console du capteur n’affiche pas de nouvelle alerte.

- Vérifiez que vous n’avez pas exclu cette alerte en utilisant les règles **Exclusion d’alerte** dans la console de gestion.

### <a name="investigate-widgets-that-show-no-data"></a>Examiner des widgets qui n’affichent aucune donnée

Lorsque les widgets de la fenêtre **Tendances et statistiques** n’affichent aucune donnée, procédez comme suit :

- [Vérifiez les performances du système](#check-system-performance).

- Assurez-vous que les paramètres de l’heure et de la région sont correctement configurés et qu’ils ne sont pas définis sur une heure ultérieure.

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Examiner une carte des appareils qui affiche uniquement les appareils de diffusion

Lorsque les appareils indiqués sur la carte semblent ne pas être connectés entre eux, il est possible qu’il y ait un problème dans la configuration du port SPAN. Autrement dit, vous ne voyez peut-être que des appareils de diffusion et aucun trafic de monodiffusion.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Capture d’écran des appareils de diffusion.":::

Dans ce cas, vérifiez que vous ne voyez que le trafic de diffusion, puis demandez à l’ingénieur réseau de corriger la configuration du port SPAN pour que vous puissiez également voir le trafic de monodiffusion.

Pour confirmer que vous ne voyez que le trafic de diffusion :

- Dans l’écran **Exploration de données**, créez un rapport à l’aide de l’option **Tout**. Vérifiez ensuite si le rapport ne contient que le trafic de diffusion et de multidiffusion (et non le trafic de monodiffusion).

Ou :

- Enregistrez un PCAP directement à partir du commutateur ou connectez un ordinateur portable en utilisant Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Connecter le capteur à NTP

Vous pouvez configurer un capteur autonome et une console de gestion, avec les capteurs qui y sont associés, pour vous connecter à NTP.

Pour connecter un capteur autonome à NTP :

- [Contactez l’équipe du support technique pour obtenir de l’aide](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Pour connecter un capteur contrôlé par la console de gestion à NTP :

- La connexion à NTP est configurée sur la console de gestion. Tous les capteurs contrôlés par la console de gestion obtiennent automatiquement la connexion à NTP.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Déterminer quand des appareils ne sont pas affichés sur la carte ou que vous avez plusieurs alertes relatives à Internet

Parfois, les périphériques ICS sont configurés avec des adresses IP externes. Ces périphériques ICS ne sont pas affichés sur la carte. Au lieu des périphériques, un cloud Internet apparaît sur la carte. Les adresses IP de ces périphériques sont incluses dans l’image du cloud.

Une autre indication du même problème est lorsque plusieurs alertes liées à Internet s’affichent.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Capture d’écran des alertes liées à Internet.":::

**Pour corriger la configuration** :

1. Cliquez avec le bouton droit sur l’icône du cloud sur la carte des appareils, puis sélectionnez **Exporter des adresses IP**. Copiez les plages publiques qui sont privées et ajoutez-les à la liste des sous-réseaux. Pour plus d’informations, consultez [Configurer des sous-réseaux](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Générez un nouveau rapport d’exploration de données pour les connexions Internet.

1. Dans le rapport d’exploration de données, sélectionnez :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: pour passer en mode administrateur et supprimer les adresses IP de vos périphériques ICS.

### <a name="tweak-the-sensors-quality-of-service-qos"></a>Ajuster la qualité de service du capteur

Pour économiser vos ressources réseau, vous pouvez limiter la bande passante de l’interface que le capteur utilise pour les procédures quotidiennes.

Pour limiter la bande passante de l’interface, utilisez l’outil CLI `cyberx-xsense-limit-interface` qui doit être exécuté avec les autorisations sudo. L’outil obtient les arguments suivants :

- `* -i` : interfaces (exemple : eth0).

- `* -l` : limite (exemple : 30 kbit/1 mbit). Vous pouvez utiliser les unités de bande passante suivantes : kbps, Mbits/s, kbit, mbit ou bps.

- `* -c` : effacer (pour effacer la limitation de la bande passante de l’interface).

**Pour ajuster la qualité de service (QoS)**  :

1. Connectez-vous à l’interface de ligne de commande du capteur en tant qu’utilisateur Defender pour IoT, puis entrez `sudo cyberx-xsense-limit-interface-I eth0 -l value`.

   Par exemple : `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Pour une appliance physique, utilisez l’interface em1.

1. Pour désactiver la limitation de l’interface, entrez `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c`.

## <a name="on-premises-management-console-troubleshooting-tools"></a>Outils de dépannage de la console de gestion locale

### <a name="investigate-a-lack-of-expected-alerts-on-the-management-console"></a>Examiner un manque d’alertes attendues sur la console de gestion

Si une alerte attendue n’est pas affichée dans la fenêtre **Alertes**, vérifiez les éléments suivants :

- Vérifiez si la même alerte apparaît déjà dans la fenêtre **Alertes** en réaction à une instance de sécurité différente. Si c’est le cas, et si cette alerte n’a pas encore été traitée, une nouvelle alerte n’est pas affichée.

- Vérifiez que vous n’avez pas exclu cette alerte en utilisant les règles **Exclusion d’alerte** dans la console de gestion locale.  

### <a name="tweak-the-quality-of-service-qos"></a>Ajuster la qualité de service

Pour économiser vos ressources réseau, vous pouvez limiter le nombre d’alertes envoyées à des systèmes externes (tels que les e-mails ou les SIEM) en une seule opération de synchronisation entre une appliance et la console de gestion locale.

La valeur par défaut est de 50. Cela signifie que dans une session de communication entre une appliance et la console de gestion locale, il n’y aura pas plus de 50 alertes envoyées à des systèmes externes. 

Pour limiter le nombre d’alertes, utilisez la propriété `notifications.max_number_to_report` disponible dans `/var/cyberx/properties/management.properties`. Aucun redémarrage n’est nécessaire après la modification de cette propriété.

**Pour ajuster la qualité de service (QoS)**  :

1. Connectez-vous en tant qu’utilisateur Defender pour IoT.

1. Vérifiez les valeurs par défaut :

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Les valeurs par défaut suivantes s’affichent :

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Modifiez les paramètres par défaut :

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Modifiez les paramètres des lignes suivantes :

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Enregistrez les modifications. Aucun redémarrage n'est requis.

## <a name="export-information-from-the-sensor-for-troubleshooting"></a>Exporter les informations du capteur pour la résolution des problèmes

En plus des outils de surveillance et d’analyse de votre réseau, vous pouvez envoyer des informations à l’équipe du support technique pour approfondir votre investigation. Lorsque vous exportez des journaux, le capteur génère automatiquement un mot de passe à usage unique pour les journaux exportés, dans un fichier texte séparé.

**Pour exporter des journaux** :

1. Dans le volet gauche, sélectionnez **Paramètres du système**.

1. Sélectionnez **Exporter les journaux d’activité**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/sensor-export-log.png" alt-text="Capture d’écran de l’écran d’exportation d’un journal sur le support système.":::

1. Dans le champ **Nom de fichier**, entrez le nom du fichier que vous souhaitez utiliser pour l’exportation des journaux. La valeur par défaut est la date actuelle.

1. Pour définir les données que vous souhaitez exporter, sélectionnez les catégories de données :  

    | Catégorie d’exportation | Description |
    |--|--|
    | **Journaux du système d’exploitation** | Sélectionnez cette option pour obtenir des informations sur l’état du système d’exploitation. |
    | **Journaux d’installation/de mise à niveau** | Sélectionnez cette option pour examiner les paramètres de configuration de l’installation et de la mise à niveau. |
    | **Résultat de la validité du système** | Sélectionnez cette option pour vérifier les performances du système. |
    | **Journaux de dissection** | Sélectionnez cette option pour permettre une inspection avancée de la dissection du protocole. |
    | **Images mémoire du noyau du système d’exploitation** | Sélectionnez cette option pour exporter votre image mémoire du noyau. Une image mémoire du noyau contient toute la mémoire utilisée par le noyau au moment où le problème s’est produit dans celui-ci. La taille du fichier de vidage sur incident est inférieure à l’image mémoire complète. En règle générale, le fichier de vidage sur incident correspond environ à un tiers de la taille de la mémoire physique du système. |
    | **Journaux de transfert** | Sélectionnez cette option pour l’investigation des règles de transfert. |
    | **Journaux SNMP** | Sélectionnez cette option pour recevoir les informations de vérification de l’intégrité SNMP. |
    | **Journaux des applications de base** | Sélectionnez cette option pour exporter des données sur la configuration et l’opération des applications principales. |
    | **Journaux de communication avec CM** | Sélectionnez cette option en cas de problèmes permanents ou d’interruptions de la connexion avec la console de gestion. |
    | **Journaux des applications web** | Sélectionnez cette option pour obtenir des informations sur toutes les demandes envoyées à partir de l’interface web de l’application. |
    | **Sauvegarde du système** | Sélectionnez cette option pour exporter une sauvegarde de toutes les données système afin d’examiner l’état exact du système. |
    | **Statistiques relatives à la dissection** | Sélectionnez cette option pour permettre une inspection avancée des statistiques du protocole. |
    | **Journaux de base de données** | Sélectionnez cette option pour exporter les journaux de la base de données système. L’examen des journaux système facilite l’identification des problèmes sur le système. |
    | **Configuration** | Sélectionnez cette option pour exporter des informations sur tous les paramètres configurables afin de vérifier que tout a été correctement configuré. |

1. Pour sélectionner toutes les options, sélectionnez **Sélectionner tout** à côté de **Choisir des catégories**.

1. Sélectionnez **Exporter les journaux d’activité**.

Les journaux exportés sont ajoutés à la liste **Journaux archivés**. Envoyez le mot de passe à usage unique à l’équipe du support technique dans un message et sur un support distincts des journaux exportés. L’équipe du support technique ne pourra extraire les journaux exportés qu’en utilisant le mot de passe unique utilisé pour chiffrer les journaux.

La liste des journaux archivés peut contenir jusqu’à cinq éléments. Si le nombre d’éléments de la liste dépasse ce nombre, l’élément le plus ancien est supprimé.

## <a name="export-audit-log-from-the-management-console"></a>Exporter le journal d’audit à partir de la console de gestion

Les journaux d’audit enregistrent les informations clés au moment de l’occurrence. Les journaux d’audit sont utiles lorsque vous essayez de déterminer quelles modifications ont été apportées, et par qui. Les journaux d’audit peuvent être exportés dans la console de gestion et contenir les informations suivantes :

| Action | Informations enregistrées |
|--|--|
| **Apprentissage et correction des alertes** | ID de l’alerte |
| **Modifications de mot de passe** | Utilisateur, ID utilisateur |
| **Connexion** | Utilisateur |
| **Création de l’utilisateur** | Utilisateur, rôle d’utilisateur |
| **Réinitialisation du mot de passe** | Nom d’utilisateur |
| **Règle d’exclusion** : </br></br>- Création </br></br>- Modification </br></br>- Suppression | </br></br>Résumé de la règle </br></br>ID de règle, Résumé de la règle </br></br>Identificateur de la règle |
| **Mise à niveau de la console de gestion** | Fichier de mise à niveau utilisé |
| **Nouvelle tentative de mise à niveau du capteur** | ID de capteur |
| **Package TI chargé** | Aucune information supplémentaire enregistrée. |

**Pour exporter le journal d’audit** :

1. Dans le volet gauche de la console de gestion, sélectionnez **Paramètres système**.

1. Sélectionnez **Exporter**.

1. Dans la zone Nom de fichier, entrez le nom du fichier que vous souhaitez utiliser pour le journal exporté. Si aucun nom n’est entré, le nom de fichier par défaut est la date actuelle.

1. Sélectionnez **Journaux d’audit**.

1. Sélectionnez **Exporter**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/audit-logs-export.png" alt-text="Capture d’écran des journaux d’audit sélectionnés, puis sélectionnez Exporter pour créer votre écran de fichiers.":::

Le journal exporté est ajouté à la liste **Journaux archivés**. Sélectionnez le bouton :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/eye-icon.png" border="false"::: pour afficher le mot de passe à usage unique. Envoyez la chaîne du mot de passe à usage unique à l’équipe du support technique dans un message distinct des journaux exportés. L’équipe du support technique ne pourra extraire les journaux exportés qu’en utilisant le mot de passe unique utilisé pour chiffrer les journaux.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/archived-files.png" alt-text="Capture d’écran du fichier que vous avez créé dans la section des fichiers archivés de la fenêtre Informations de résolution des problèmes d’exportation.":::

## <a name="next-steps"></a>Étapes suivantes

- [Afficher les alertes](how-to-view-alerts.md)

- [Configurer la supervision de SNMP MIB](how-to-set-up-snmp-mib-monitoring.md)

- [Comprendre les événements de déconnexion des capteurs](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
