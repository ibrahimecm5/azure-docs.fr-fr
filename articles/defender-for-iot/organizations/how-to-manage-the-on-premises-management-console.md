---
title: Gérer la console de gestion locale
description: Découvrez les options de la console de gestion locale, telles que la sauvegarde et la restauration, la définition du nom d’hôte et la configuration d’un proxy pour les capteurs.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 3604b4674a86d719f2fecf841347c0a55f5b9757
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339703"
---
# <a name="manage-the-on-premises-management-console"></a>Gérer la console de gestion locale

Cet article traite des options de la console de gestion locale, telles que la sauvegarde et la restauration, le téléchargement du fichier d’activation des appareils validés, la mise à jour des certificats et la configuration d’un proxy pour les capteurs.

Vous intégrez la console de gestion locale à partir du portail Azure.

## <a name="upload-an-activation-file"></a>Charger un fichier d’activation

Quand vous vous connectez pour la première fois, un fichier d’activation pour la console de gestion locale est téléchargé. Ce fichier contient l’ensemble des appareils validés qui sont définis pendant le processus d’intégration. La liste comprend les capteurs associés à plusieurs abonnements.

Après l’activation initiale, le nombre d’appareils analysés peut dépasser le nombre d’appareils validés définis lors de l’intégration. Cet événement peut se produire, par exemple, si vous connectez d’autres capteurs à la console de gestion. S’il y a une différence entre le nombre d’appareils analysés et le nombre d’appareils validés, un avertissement s’affiche dans la console de gestion. Si cet événement se produit, vous devez charger un nouveau fichier d’activation.

**Pour charger un fichier d’activation :**

1. Accédez à la page **Tarification** de Microsoft Defender pour IoT.
1. Sélectionnez l’onglet **Télécharger le fichier d’activation de la console de gestion**. Le fichier d’activation est téléchargé.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Télécharger le fichier d’activation.":::

1. Sélectionnez **Paramètres système** à partir de la console de gestion.
1. Sélectionnez **Activation**.
1. Sélectionnez **Choisir un fichier** et sélectionnez le fichier que vous avez enregistré.

## <a name="manage-certificates"></a>Gérer des certificats

Après l’installation de la console de gestion locale, un certificat auto-signé local est généré et utilisé pour accéder à l’application web. Lorsqu’ils se connectent à la console de gestion locale pour la première fois, les utilisateurs administrateurs sont invités à fournir un certificat SSL/TLS. 

Les administrateurs peuvent être amenés à mettre à jour les certificats qui ont été téléchargés après la connexion initiale. Cela peut se produire par exemple si un certificat a expiré.

**Pour mettre à jour un certificat :**

1. Sélectionnez **Paramètres système**.

1. Sélectionnez **Certificats SSL/TLS**.

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="Téléchargement d'un certificat":::

1. Dans la boîte de dialogue Certificats SSL/TLS, supprimez le certificat existant et ajoutez-en un nouveau.

    - Ajoutez un nom de certificat.
    - Téléchargez un fichier CRT et un fichier de clé.
    - Chargez un fichier PEM si nécessaire.

Si le téléchargement échoue, contactez votre administrateur informatique ou de sécurité, ou passez en revue les informations figurant dans la section [À propos des certificats](how-to-deploy-certificates.md).

**Pour modifier le paramètre de validation du certificat :**

1. Activez ou désactivez le bouton bascule **Activer la validation du certificat**. Si cette option est activée et que la validation échoue, la communication entre les composants concernés est interrompue et une erreur de validation apparaît sur la console. Si elle est désactivée, la validation du certificat n’est pas effectuée. Pour plus d’informations, consultez [À propos de la validation des certificats](how-to-deploy-certificates.md#about-certificate-validation).

1. Sélectionnez **Enregistrer**.

Pour plus d’informations sur le chargement des certificats pour la première fois, consultez [Première connexion et liste de contrôle d’activation](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)

## <a name="define-backup-and-restore-settings"></a>Définir les paramètres de sauvegarde et de restauration

La sauvegarde du système de la console de gestion locale est effectuée automatiquement et quotidiennement. Les données sont enregistrées sur un autre disque. L’emplacement par défaut est `/var/cyberx/backups`. 

Vous pouvez transférer automatiquement ce fichier vers le réseau interne. 

> [!NOTE]
> Vous pouvez effectuer la procédure de sauvegarde et de restauration uniquement sur la même version. 

Pour sauvegarder l’ordinateur de la console de gestion locale : 

- Connectez-vous à un compte d’administrateur et entrez `sudo cyberx-management-backup -full`.

Pour restaurer le dernier fichier de sauvegarde :

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-management-system-restore`.

Pour enregistrer la sauvegarde sur un serveur SMB externe :

1. Créez un dossier partagé sur le serveur SMB externe.  

   Obtenez le chemin d’accès au dossier, le nom d’utilisateur et le mot de passe requis pour accéder au serveur SMB. 

2. Dans Defender pour IoT, créez un répertoire pour les sauvegardes :

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Modifiez fstab :  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Modifiez ou créez les informations d’identification pour le serveur SMB à partager : 

   - `sudo nano /etc/samba/user` 

5. Ajoutez : 

   - `username=<user name>`

   - `password=<password>` 

6. Montez le répertoire : 

   - `sudo mount -a` 

7. Configurez un répertoire de sauvegarde dans le dossier partagé de la console de gestion locale Defender pour IoT :  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Modifier le nom d’hôte

Pour modifier le nom d’hôte de la console de gestion configuré dans le serveur DNS de l’organisation :

1. Dans le volet gauche de la console de gestion, sélectionnez **Paramètres système**.  

2. Dans la section de mise en réseau de la console, sélectionnez **Réseau**. 

3. Entrez le nom d’hôte configuré dans le serveur DNS de l’organisation. 

4. Sélectionnez **Enregistrer**.

## <a name="define-vlan-names"></a>Définir des noms de VLAN

Les noms de VLAN ne sont pas synchronisés entre le capteur et la console de gestion. Définissez des noms identiques sur les composants.

Dans la zone mise en réseau, sélectionnez **VLAN** et ajoutez des noms aux ID de VLAN détectés. Ensuite, sélectionnez **Enregistrer**.

## <a name="define-a-proxy-to-sensors"></a>Définir un proxy pour les capteurs

Renforcez la sécurité du système en empêchant les utilisateurs de se connecter directement au capteur. Au lieu de cela, utilisez le tunneling de proxy pour permettre aux utilisateurs d’accéder au capteur à partir de la console de gestion locale avec une seule règle de pare-feu. Cette amélioration réduit le risque d’accès non autorisé à l’environnement réseau au-delà du capteur.

Utilisez un proxy dans les environnements où il n’existe aucune connectivité directe aux capteurs.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Utilisateur.":::

La procédure suivante connecte un capteur à la console de gestion locale et active le tunneling sur celle-ci :

1. Connectez-vous à l’interface de commande de l’appliance de console de gestion locale avec les informations d’identification d’administration.

2. Entrez `sudo cyberx-management-tunnel-enable`, puis sélectionnez **Entrée**.

4. Entrez `--port 10000`, puis sélectionnez **Entrée**.

## <a name="adjust-system-properties"></a>Ajuster les propriétés du système

Les propriétés système contrôlent plusieurs opérations et paramètres dans la console de gestion. Leur modification peut mettre à mal le fonctionnement de la console de gestion. Contactez [Support Microsoft](https://support.microsoft.com) avant de modifier vos paramètres.

Pour accéder aux propriétés système : 

1. Connectez-vous à la console de gestion locale ou au capteur.

2. Sélectionnez **Paramètres système**.

3. Sélectionnez **Propriétés système** dans la section **Général**.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Modifier le nom de la console de gestion locale

Vous pouvez modifier le nom de la console de gestion locale. Le nouveau nom apparaît dans le navigateur web de la console, dans les différentes fenêtres de la console et dans les journaux de dépannage. Le nom par défaut est **management console**.

Pour modifier le nom :

1. En bas du volet gauche, sélectionnez le nom actuel.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Capture d’écran de la version de la console de gestion locale.":::

2. Dans la boîte de dialogue **Modifier la configuration de la console de gestion**, entrez le nouveau nom. Le nom ne peut pas dépasser 25 caractères.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Capture d’écran de la modification de la configuration de la plateforme Defender pour IoT.":::

3. Sélectionnez **Enregistrer**. Le nouveau nom est appliqué.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Capture d’écran montrant le nom modifié de la console.":::

## <a name="password-recovery"></a>Récupération de mot de passe

La récupération du mot de passe pour votre console de gestion locale est liée à l’abonnement auquel l’appareil est rattaché. Vous ne pouvez pas récupérer un mot de passe si vous ne savez pas à quel abonnement un appareil est rattaché.

Pour réinitialiser votre mot de passe :

1. Accédez à la page de connexion de la console de gestion locale.
1. Sélectionnez **Récupération du mot de passe**.
1. Copiez l’identificateur unique.
1. Accédez à la page **Sites et capteurs** de Defender pour IoT et sélectionnez l’onglet **Récupérer mon mot de passe**.
1. Entrez l’identificateur unique et sélectionnez **Récupérer**. Le fichier d’activation est téléchargé.
1. Accédez à la page **Récupération du mot de passe** et chargez le fichier d’activation.
1. Sélectionnez **Suivant**.
 
   Vous recevez maintenant votre nom d’utilisateur et un nouveau mot de passe généré par le système.

> [!NOTE]
> Le capteur est lié à l’abonnement auquel il était connecté à l’origine. Vous pouvez récupérer le mot de passe uniquement en utilisant le même abonnement que celui auquel il est rattaché.

## <a name="update-the-software-version"></a>Mettre à jour la version du logiciel

La procédure suivante décrit la mise à jour de la version du logiciel de la console de gestion locale. Le processus de mise à jour prend environ 30 minutes.

Si vous utilisez une console de gestion locale et des capteurs managés, **commencez par mettre à jour la console de gestion**.

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Accédez à Defender pour IoT.

1. Accédez à la page **Mises à jour**.

1. Sélectionnez une version dans la section relative à la console de gestion locale.

1. Sélectionnez **Télécharger** et enregistrez le fichier.

1. Connectez-vous à la console de gestion locale et sélectionnez **Paramètres système** dans le menu latéral.

1. Dans le volet **Mise à jour de la version**, sélectionnez **Mettre à jour**.

1. Sélectionnez le fichier que vous avez téléchargé à partir de la page **Mises à jour** de Defender pour IoT.

## <a name="mail-server-settings"></a>Paramètres du serveur de messagerie

Définissez les paramètres du serveur de messagerie SMTP pour la console de gestion locale.

Pour les définir :

1. Connectez-vous à l’interface CLI destinée à la gestion locale avec les informations d’identification d’administration.
1. Tapez ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Sélectionnez Enter (Entrer). Les invites suivantes s’affichent.
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. Entrez le nom du serveur SMTP et l’expéditeur, puis sélectionnez Entrée.

## <a name="see-also"></a>Voir aussi

[Gérer les capteurs depuis la console de gestion](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Gérer des capteurs individuels](how-to-manage-individual-sensors.md)
