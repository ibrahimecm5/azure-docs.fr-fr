---
title: Gérer les capteurs depuis la console de gestion locale
description: Apprenez à gérer des capteurs à partir de la console de gestion, notamment la mise à jour des versions de capteur, l’envoi (push) des paramètres système aux capteurs et l’activation et la désactivation de moteurs sur les capteurs.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 1015810f8999671665cf48d74058e9ad3fc47558
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278832"
---
# <a name="manage-sensors-from-the-management-console"></a>Gérer les capteurs depuis la console de gestion

Cet article explique comment gérer les capteurs à partir de la console de gestion, notamment :

- Envoyer (push) des paramètres système aux capteurs

- Activer et désactiver des moteurs sur les capteurs

- Mettre à jour les versions de capteur

## <a name="push-configurations"></a>Envoyer (push) des configurations

Vous pouvez définir différents paramètres système et les appliquer automatiquement aux capteurs qui sont connectés à la console de gestion. Cela vous permet de gagner du temps et de rationaliser les paramètres des capteurs de votre entreprise.

Vous pouvez définir les paramètres système suivants pour les capteurs à partir de la console de gestion :

- Serveur de messagerie

- Analyse SNMP MIB

- Active Directory

- Paramètres DNS

- Sous-réseaux

- Alias de port

**Pour appliquer les paramètres système** :

1. Dans le volet gauche de la console, sélectionnez **Paramètres système**.

1. Dans le volet **Configurer les capteurs**, sélectionnez l’une des options.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Options de paramètre système pour un capteur.":::

   L’exemple suivant décrit la façon de définir des paramètres de serveur de messagerie pour vos capteurs d’entreprise.

1. Sélectionnez **Serveur de messagerie**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Sélectionnez votre serveur de messagerie dans l’écran Paramètres système.":::

1. Sélectionnez un capteur sur la gauche.

1. Définissez les paramètres du serveur de messagerie et sélectionnez **Dupliquer**. Chaque élément de l’arborescence du capteur apparaît avec une case à cocher à côté.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Vérifiez que les cases sont cochées pour vos capteurs.":::

1. Dans l’arborescence du capteur, sélectionnez les éléments auxquels vous souhaitez appliquer la configuration.

1. Sélectionnez **Enregistrer**.

## <a name="update-sensor-versions"></a>Mettre à jour les versions de capteur

Vous pouvez mettre à jour plusieurs capteurs simultanément à partir de la console de gestion locale.

### <a name="update-sequence"></a>Ordre de mise à jour

Quand vous effectuez la mise à niveau d’une console de gestion locale et de capteurs managés, mettez à jour la console de gestion avant de mettre à jour les capteurs. Le processus de mise à jour des capteurs échoue si vous ne respectez pas cet ordre de mise à jour.

**Pour mettre à jour plusieurs capteurs** :

1. Assurez-vous d’avoir déjà mis à jour la console de gestion locale vers la version avec laquelle vous voulez mettre à jour les capteurs. Pour plus d’informations sur la mise à jour de la console de gestion locale, consultez [Mettre à jour la version du logiciel](how-to-manage-the-on-premises-management-console.md#update-the-software-version).

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Accédez à Microsoft Defender pour IoT.

1. Accédez à la page **Mises à jour**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Capture d’écran de la vue du tableau de bord Mises à jour.":::

1. Sélectionnez **Télécharger** dans la section **Capteurs** et enregistrez le fichier.

1. Connectez-vous à la console de gestion et sélectionnez **Paramètres système**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Capture d’écran du menu Administration pour sélectionner Paramètres système.":::

1. Sélectionnez les capteurs à mettre à jour dans la section **Configuration des moteurs des capteurs**, puis sélectionnez **Mises à jour automatiques**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Deux capteurs indiquant le mode d’apprentissage et les mises à jour automatiques.":::

1. Sélectionnez **Enregistrer les modifications**.

1. Dans la console de gestion, sélectionnez **Paramètres système**.
1. Sous la section Mise à jour de la version des capteurs, sélectionnez le bouton :::image type="icon" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/add-icon.png" border="false":::.

    :::image type="content" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/sendor-version-update-window.png" alt-text="Dans la fenêtre Mise à jour de la version des capteurs, sélectionnez l’icône + pour mettre à jour tous les capteurs qui sont connectés à la console de gestion.":::

9. Un boîte de dialogue **Télécharger un fichier** s’ouvre. Chargez le fichier que vous avez téléchargé à partir de la page **Mises à jour**.

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Sélectionnez le bouton Parcourir pour charger votre fichier.":::

Vous pouvez surveiller l'état de mise à jour de chaque capteur dans la fenêtre **Gestion des sites**.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observez la progression de votre mise à jour.":::

### <a name="update-sensors-from-the-on-premises-management-console"></a>Mettre à jour les capteurs depuis la console de gestion locale

Vous pouvez consulter l'état de mise à jour de vos capteurs à partir de la console de gestion. Si la mise à jour a échoué, vous pouvez effectuer une nouvelle tentative à partir de la console de gestion locale (versions 2.3.5 et suivantes).

**Pour mettre à jour le capteur à partir de la console de gestion locale :**

1. Connectez-vous à la console de gestion locale et accédez à la page **Gestion des sites**.

1. Repérez les capteurs qui ont **échoué** dans la colonne Progression de la mise à jour et sélectionnez le bouton de téléchargement. 

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/download-update-button.png" alt-text="Sélectionnez l'icône de téléchargement pour essayer de télécharger et d'installer la mise à jour de votre capteur.":::

Vous pouvez surveiller l'état de mise à jour de chaque capteur dans la fenêtre **Gestion des sites**.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observez la progression de votre mise à jour.":::

Si vous ne parvenez pas à mettre à jour le capteur, contactez le support technique pour obtenir de l'aide.

## <a name="update-threat-intelligence-packages"></a>Mettre à jour des packages de renseignement sur les menaces 

Le paquet de données pour le renseignement sur les menaces est fourni avec chaque nouvelle version de Defender pour IoT, ou entre deux versions si nécessaire. Le package contient des signatures (y compris des signatures de programmes malveillants), des CVE et d’autres contenus de sécurité. 

Vous pouvez charger manuellement ce fichier dans le portail Azure et le mettre à jour automatiquement sur des capteurs. 

**Pour mettre à jour les données de renseignement sur les menaces :**

1. Accédez à la page **Mises à jour** de Defender pour IoT. 

1. Téléchargez et enregistrez le fichier.

1. Connectez-vous à la console de gestion. 

1. Dans le menu latéral, sélectionnez **Paramètres système**. 

1. Sélectionnez les capteurs qui doivent recevoir la mise à jour dans la section **Configuration des moteurs des capteurs**.  

1. Dans la section **Sélectionner les données de renseignement sur les menaces**, sélectionnez le signe plus ( **+** ). 

1. Chargez le package que vous avez téléchargé à partir de la page **Mises à jour** de Defender pour IoT.

## <a name="understand-sensor-disconnection-events"></a>Comprendre les événements de déconnexion des capteurs

La fenêtre **Site Manager** affiche des informations de déconnexion si les capteurs se déconnectent de la console de gestion locale qui leur est attribuée. Les informations suivantes concernant la déconnexion des capteurs sont disponibles :

- « La console de gestion locale ne peut pas traiter les données reçues du capteur. »

- « Dérive temporelle détectée. La console de gestion locale a été déconnectée du capteur. »

- « Le capteur ne communique pas avec la console de gestion locale. Vérifiez la connectivité réseau ou la validation du certificat. »

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Capture d’écran de la vue Zone 1.":::

Vous pouvez envoyer des alertes à des tiers avec des informations sur les capteurs déconnectés. Pour plus d’informations, consultez [Transférer les alertes d’échec des capteurs](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Activer ou désactiver des capteurs

Les capteurs sont protégés par cinq moteurs Defender pour IoT. Vous pouvez activer ou désactiver les moteurs pour les capteurs connectés.

| Moteur | Description | Exemple de scénario |
|--|--|--|
| Moteur de violation de protocole | Une violation de protocole se produit lorsque la structure de paquets ou les valeurs de champ ne sont pas conformes à la spécification de protocole. | Alerte « Opération MODBUS illégale (code de fonction zéro) ». Cette alerte indique qu’un appareil principal a envoyé une requête avec le code de fonction 0 à un appareil secondaire. Cela n’est pas autorisé conformément aux spécifications du protocole, et l’appareil secondaire peut ne pas gérer correctement l’entrée. |
| Moteur de violation de stratégie | Une violation de stratégie se produit lorsqu’il y a un écart par rapport au comportement de la ligne de base défini dans la stratégie apprise ou configurée. | Alerte « Agent utilisateur HTTP non autorisé ». Cette alerte indique qu’une application qui n’a pas été apprise ou approuvée par la stratégie est utilisée comme client HTTP sur un appareil. Il peut s’agir d’un nouveau navigateur web ou d’une nouvelle application sur cet appareil. |
| Moteur de programme malveillant | Le moteur de programme malveillant détecte l’activité de réseau malveillante. | Alerte « Suspicion d’activité malveillante (Stuxnet) ». Cette alerte indique que le capteur a détecté une activité réseau suspecte connue pour être liée au programme malveillant Stuxnet, qui est une menace persistante avancée visant les réseaux de contrôle industriel et SCADA. |
| Moteur d’anomalies | Le moteur de programme malveillant détecte une anomalie dans le comportement du réseau. | Alerte « Comportement périodique dans le canal de communication ». Il s’agit d’un composant qui inspecte les connexions réseau et recherche le comportement périodique ou cyclique de la transmission des données, ce qui est courant dans les réseaux industriels. |
| Moteur opérationnel | Ce moteur détecte les incidents opérationnels ou les entités défectueuses. | Alerte `Device is Suspected to be Disconnected (Unresponsive)`. Cette alerte est déclenchée lorsqu’un appareil ne répond à aucune requête pendant une période prédéfinie. Cela peut indiquer un arrêt, une déconnexion ou un dysfonctionnement de l’appareil.
|

**Pour activer ou désactiver des moteurs pour les capteurs connectés :**

1. Dans le volet gauche de la console, sélectionnez **Paramètres système**.

1. Dans la section **Configuration des moteurs des capteurs**, sélectionnez **Activer** ou **Désactiver** pour les moteurs.
         
1. Sélectionnez **ENREGISTRER LES MODIFICATIONS**.

   Un point d’exclamation rouge s’affiche si les moteurs activés ne correspondent pas sur l’un des capteurs de votre entreprise. Le moteur a peut-être été désactivé directement à partir du capteur.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Non correspondance des moteurs activés."::: 

## <a name="define-sensor-backup-schedules"></a>Définir la planification des sauvegardes des capteurs

Vous pouvez planifier des sauvegardes de capteurs et effectuer des sauvegardes de capteurs à la demande à partir de la console de gestion locale. Cela permet de se protéger contre les défaillances de disque dur et la perte de données.

- Éléments sauvegardés : Configurations et données.

- Éléments non sauvegardés : Fichiers PCAP et journaux. Vous pouvez sauvegarder et restaurer manuellement les fichiers PCAP et les journaux.

Par défaut, les capteurs sont automatiquement sauvegardés tous les jours à 3 h. La fonctionnalité de planification des sauvegardes des capteurs vous permet de collecter ces sauvegardes et de les stocker sur la console de gestion locale ou sur un serveur de sauvegarde externe. La copie des fichiers des capteurs vers la console de gestion locale s’effectue sur un canal chiffré.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Vue de l’écran de sauvegarde des capteurs.":::

Lorsque l’emplacement par défaut de la sauvegarde des capteurs est modifié, la console de gestion locale récupère automatiquement les fichiers du nouvel emplacement sur le capteur ou d’un emplacement externe, à condition que la console ait l’autorisation d’accéder à l’emplacement. 

Lorsque les capteurs ne sont pas inscrits auprès la console de gestion locale, la boîte de dialogue **Planification des sauvegardes des capteurs** indique qu’aucun capteur n’est géré.  

Le processus de restauration est le même, quel que soit l’emplacement de stockage des fichiers.

### <a name="backup-storage-for-sensors"></a>Stockage de sauvegarde pour les capteurs

Vous pouvez utiliser la console de gestion locale pour gérer jusqu’à neuf sauvegardes pour chaque capteur géré, à condition que les fichiers sauvegardés ne dépassent pas l’espace de sauvegarde maximal alloué. 

L’espace disponible est calculé en fonction du modèle de console de gestion que vous utilisez : 

- **Modèle de production** : Le stockage par défaut est de 40 Go ; la limite est de 100 Go. 

- **Modèle moyen** : Le stockage par défaut est de 20 Go ; la limite est de 50 Go. 

- **Modèle d’ordinateur portable** : Le stockage par défaut est de 10 Go ; la limite est de 25 Go. 

- **Modèle fin** : Le stockage par défaut est de 2 Go ; la limite est de 4 Go. 

- **Modèle robuste** : Le stockage par défaut est de 10 Go ; la limite est de 25 Go. 

L’allocation par défaut est affichée dans la boîte de dialogue **Planification des sauvegardes des capteurs**. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Écran Modifier la configuration du serveur de messagerie.":::

Il n’existe aucune limite de stockage lorsque vous sauvegardez sur un serveur externe. Toutefois, vous devez définir une limite d’allocation supérieure dans le champ **Planification des sauvegardes des capteurs** > **Chemin personnalisé**. Les nombres et caractères suivants sont pris en charge : `/, a-z, A-Z, 0-9, and _`. 

Voici des informations sur le dépassement des limites du stockage alloué :

- Si vous dépassez l’espace de stockage alloué, le capteur n’est pas sauvegardé. 

- Si vous sauvegardez plusieurs capteurs, la console de gestion tente de récupérer les fichiers des capteurs gérés.  

- Si la récupération d’un capteur dépasse la limite, la console de gestion tente de récupérer les informations de sauvegarde du capteur suivant. 

Lorsque vous dépassez le nombre de sauvegardes défini, le fichier de sauvegarde le plus ancien est supprimé pour prendre accueillir le nouveau.

Les fichiers de sauvegarde des capteurs sont automatiquement nommés au format suivant : `<sensor name>-backup-version-<version>-<date>.tar`. Par exemple : `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

**Pour sauvegarder des capteurs :**

1. Sélectionnez **Planifier la sauvegarde des capteurs** dans la fenêtre **Paramètres système**. Les capteurs gérés par votre console de gestion locale s’affichent dans la boîte de dialogue **Planification des sauvegardes des capteurs**.  

1. Activez le bouton bascule **Collecter les sauvegardes**.  

1. Sélectionnez un intervalle de calendrier, une date et un fuseau horaire. Le format de l’heure est basé sur une horloge numérique de 24 heures. Par exemple, entrez 18 h sous la forme **18:00**. 

1. Dans le champ **Allocation du stockage de sauvegarde**, entrez le stockage que vous souhaitez allouer à vos sauvegardes. Vous êtes averti si vous dépassez l’espace maximal.

1. Dans le champ **Conserver le dernier**, indiquez le nombre de sauvegardes par capteur que vous souhaitez conserver. Lorsque la limite est dépassée, la sauvegarde la plus ancienne est supprimée.  

1. Choisissez un emplacement de sauvegarde :  

   - Pour effectuer une sauvegarde sur la console de gestion locale, désactivez le bouton bascule **Chemin personnalisé**. L’emplacement par défaut est `/var/cyberx/sensor-backups`.  

   - Pour effectuer une sauvegarde sur un serveur externe, activez le bouton bascule **Chemin personnalisé** et entrez un emplacement. Les nombres et caractères suivants sont pris en charge : `/, a-z, A-Z, 0-9, and, _`. 

1. Sélectionnez **Enregistrer**. 

**Pour sauvegarder immédiatement :**

- Sélectionnez **Sauvegarder maintenant**. La console de gestion locale crée et collecte des fichiers de sauvegarde de capteur. 

### <a name="receiving-backup-notifications-for-sensors"></a>Réception de notifications de sauvegarde pour les capteurs 

La boîte de dialogue **Planification des sauvegardes des capteurs** et le journal de sauvegarde répertorient automatiquement les informations relatives aux réussites et aux échecs de sauvegarde.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Visualisez vos capteurs et leur emplacement, ainsi que toutes les informations pertinentes.":::

Des échecs peuvent se produire pour les raisons suivantes :    

- Aucun fichier de sauvegarde n’a été trouvé. 

- Un fichier a été trouvé, mais il ne peut pas être récupéré.  

- Une défaillance de la connexion réseau s’est produite. 

- Il n’y a pas assez d’espace alloué à la console de gestion locale pour terminer la sauvegarde.  

Vous pouvez envoyer une notification par e-mail, des mises à jour Syslog et des notifications système lorsqu’une défaillance se produit. Pour ce faire, créez une règle de transfert dans **Notifications système**. 

### <a name="restoring-sensors"></a>Restauration des capteurs 

Vous pouvez restaurer des sauvegardes à partir de la console de gestion locale et à l’aide de l’interface CLI.  

**Pour effectuer une restauration à partir de la console :**

- Sélectionnez **Restaurer une image** dans la fenêtre **Paramètres système** du capteur.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Restaurez une sauvegarde de votre image.":::

  La console affiche alors les échecs de restauration.  

Pour effectuer une restauration à l’aide de l’interface CLI : 

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-xsense-system-restore`. 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Enregistrer une sauvegarde de capteur sur un serveur SMB externe

**Pour configurer un serveur SMB afin de pouvoir enregistrer une sauvegarde de capteur sur un lecteur externe :**

1. Créez un dossier partagé sur le serveur SMB externe. 

1. Obtenez le chemin d’accès au dossier, le nom d’utilisateur et le mot de passe requis pour accéder au serveur SMB. 

1. Dans Defender pour IoT, créez un répertoire pour les sauvegardes : 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

1. Modifiez fstab :  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

1. Modifiez ou créez des informations d’identification à partager. Il s’agit des informations d’identification du serveur SMB : 

   `sudo nano /etc/samba/user` 

1. Ajoutez :  

   `username=<user name>` 

   `password=<password>` 

1. Montez le répertoire : 

   `sudo mount -a` 

1. Configurez un répertoire de sauvegarde dans le dossier partagé sur le capteur Defender pour IoT :  

   `sudo nano /var/cyberx/properties/backup.properties` 

1. Définissez `Backup.shared_location` sur `<backup_folder_name_on_cyberx_server>`.

## <a name="see-also"></a>Voir aussi

[Gérer des capteurs individuels](how-to-manage-individual-sensors.md)
