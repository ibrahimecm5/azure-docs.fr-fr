---
title: Activer et configurer votre console de gestion locale
description: L’activation de la console de gestion garantit que les capteurs sont inscrits auprès d’Azure et envoient des informations à la console de gestion locale, et que la console de gestion locale effectue des tâches de gestion sur les capteurs connectés.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 77f2a62cce7da4f9faac62820a85f38c0dd06ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306222"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Activer et configurer votre console de gestion locale 

L’activation et la configuration de la console de gestion locale garantissent que :

- Les périphériques réseau que vous surveillez par le bais de capteurs connectés sont inscrits auprès d’un compte Azure.

- Les capteurs envoient des informations à la console de gestion locale.

- La console de gestion locale effectue des tâches de gestion sur les capteurs connectés.

- Vous avez installé un certificat SSL.

## <a name="sign-in-for-the-first-time"></a>Se connecter pour la première fois

**Pour vous connecter à la console de gestion :**

1. Accédez à l’adresse IP que vous avez reçue pour la console de gestion locale lors de l’installation du système.
 
1. Entrez le nom d’utilisateur et le mot de passe que vous avez reçus pour la console de gestion locale lors de l’installation du système. 


Si vous avez oublié votre mot de passe, sélectionnez l’option **Récupérer le mot de passe**, et consultez [Récupération du mot de passe](how-to-manage-the-on-premises-management-console.md#password-recovery) pour obtenir des instructions sur la récupération de votre mot de passe.

## <a name="activate-the-on-premises-management-console"></a>Activer la console de gestion locale

Une fois que vous vous êtes connecté pour la première fois, vous devez activer la console de gestion locale en obtenant et en chargeant un fichier d’activation. 

**Pour activer la console de gestion locale :**

1. Connectez-vous à la console de gestion locale.

1. Dans la notification d’alerte en haut de l’écran, sélectionnez le lien **Entreprendre une action**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="Sélectionnez le lien Entreprendre une action dans l’alerte en haut de l’écran.":::

1. Dans l’écran contextuel Activation, sélectionnez le lien **Portail Azure**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="Sélectionnez le lien Portail Azure dans le message contextuel.":::
 
1. Sélectionnez un abonnement auquel associer la console de gestion locale, puis cliquez sur le bouton **Télécharger le fichier d’activation de la console de gestion locale**. Le fichier d’activation est téléchargé.

   La console de gestion locale peut être associée à un ou plusieurs abonnements. Le fichier d’activation sera associé à tous les abonnements sélectionnés et au nombre d’appareils validés au moment du téléchargement.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/multiple-subscriptions.png" alt-text="Vous pouvez sélectionner plusieurs abonnements auxquels intégrer votre console de gestion locale.":::

   Si vous n’avez pas encore intégré d’abonnement, [Intégrer un abonnement](how-to-manage-subscriptions.md#onboard-a-subscription).

   > [!Note]
   > Si vous supprimez un abonnement, vous devez charger un nouveau fichier d’activation vers toutes les consoles de gestion locale qui étaient affiliées à l’abonnement supprimé.

1. Revenez à l’écran contextuel **Activation** et sélectionnez **Choisir un fichier**.

1. Sélectionnez le fichier téléchargé.

Après l’activation initiale, le nombre d’appareils surveillés pourrait dépasser le nombre d’appareils validés définis lors de l’intégration. Ce problème se produit si vous connectez d’autres capteurs à la console de gestion. S’il y a un écart entre le nombre d’appareils surveillés et le nombre d’appareils validés, un avertissement s’affiche sur la console de gestion. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/device-commitment-update.png" alt-text="Si vous voyez l’avertissement relatif à la validation de l’appareil, vous devrez charger un nouveau fichier d’activation.":::

Si cet avertissement s’affiche, vous devez charger un [nouveau fichier d’activation](#activate-the-on-premises-management-console).

### <a name="activate-an-expired-license-versions-under-100"></a>Activer une licence ayant expiré (versions antérieures à 10.0)

Pour les utilisateurs dont la version est antérieure à la version 10.0, il se peut que leur licence arrive à expiration et qu’ils reçoivent une alerte du type suivant. 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="Lorsque votre licence arrivera à expiration, vous devrez mettre à jour votre licence via le fichier d’activation.":::

**Pour activer votre licence :**

1. Ouvrez un incident auprès du [support](https://portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support).

1. Indiquez au support votre numéro d’identification d’activation.

1. Le support vous fournira de nouvelles informations de licence se présentant sous la forme d’une chaîne de lettres.

1. Lisez les conditions générales, puis cochez la case pour accepter.

1. Collez la chaîne dans l’espace prévu à cet effet.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="Collez la chaîne dans le champ prévu à cet effet.":::

1. Sélectionnez **Activer**.

## <a name="set-up-a-certificate"></a>Configuration d'un certificat

Une fois la console de gestion installé, un certificat auto-signé local est généré. Ce certificat est utilisé pour accéder à la console. Quand un administrateur se connecte à la console de gestion pour la première fois, il est invité à intégrer un certificat SSL/TLS. 

Deux niveaux de sécurité sont disponibles :

- répondre aux exigences de chiffrement et de certificat spécifiques requises par votre organisation en téléchargeant le certificat signé par une autorité de certification ;

- Autorisez la validation entre la console de gestion et les capteurs connectés. La validation est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console.* Cette option est activée par défaut après l’installation.  

La console prend en charge les types de certificats suivants :

- Infrastructure à clé privée et d’entreprise (PKI privée)

- Infrastructure à clé publique (PKI publique)

- Généré localement sur l’appliance (auto-signé localement) 

  > [!IMPORTANT]
  > Nous vous recommandons de ne pas utiliser de certificat auto-signé. Le certificat n’est pas sécurisé et doit être utilisé uniquement pour les environnements de test. Le propriétaire du certificat ne peut pas être validé et la sécurité de votre système ne peut pas être assurée. N’utilisez jamais cette option pour les réseaux de production.

**Pour charger un certificat :**

1. Quand vous y êtes invité après vous être connecté, définissez un nom de certificat.

1. Chargez les fichiers de clé et CRT.

1. Entrez une phrase secrète et chargez un fichier PEM le cas échéant.

Il se peut que vous deviez actualiser votre écran après avoir chargé le certificat signé par une autorité de certification.

**Pour désactiver la validation entre la console de gestion et les capteurs connectés :**

1. Sélectionnez **Suivant**.

1. Désactivez le bouton bascule **Activer la validation à l’échelle du système**.

Pour plus d’informations sur le chargement d’un nouveau certificat, les fichiers de certificat pris en charge et les éléments connexes, consultez [Gérer la console de gestion locale](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Connecter des capteurs à la console de gestion locale

Assurez-vous que les capteurs envoient des informations à la console de gestion locale et que la console de gestion locale peut effectuer des sauvegardes, gérer des alertes et effectuer d’autres activités sur les capteurs. Pour ce faire, utilisez les procédures suivantes pour vérifier que vous établissez une connexion initiale entre les capteurs et la console de gestion locale.

Deux options sont disponibles pour connecter des capteurs Microsoft Defender pour IoT à la console de gestion locale :

- Se connecter à partir de la console du capteur

- Se connecter à l’aide du tunneling

Après vous être connecté, vous devez configurer un site avec ces capteurs.

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>Connecter des capteurs à la console de gestion locale à partir de la console du capteur

**Pour connecter des capteurs à la console de gestion locale à partir de la console du capteur :**

1. Dans la console de gestion locale, sélectionnez **Paramètres système**.

1. Sélectionnez **Copier la chaîne de connexion**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="Copiez la chaîne de connexion pour le capteur.":::

1. Sur le capteur, accédez à **Paramètres système**, puis sélectionnez **Connexion à la console de gestion** :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false":::

1. Collez la chaîne de connexion copiée à partir de la console de gestion locale dans le champ de **Chaîne de connexion**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="Collez la chaîne de connexion copiée dans le champ Chaîne de connexion.":::

1. Sélectionnez **Connecter**.

### <a name="connect-sensors-by-using-tunneling"></a>Connecter des capteurs à l’aide du tunneling

Activez une connexion sécurisée par tunneling entre les capteurs de l’organisation et la console de gestion locale. Cette configuration permet de contourner l’interaction avec le pare-feu de l’organisation et, par conséquent, de réduire la surface d’attaque.

L’utilisation du tunneling vous permet de connecter la console de gestion locale à partir de son adresse IP et d’un seul port (c’est-à-dire, 9000) à n’importe quel capteur.

**Pour configurer le tunneling à partir de la console de gestion locale :**

- Connectez-vous à la console de gestion locale et exécutez les commandes suivantes :

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

**Pour configurer le tunneling sur le capteur :**

1. Ouvrez manuellement le port TCP 9000 sur le capteur (network.properties). Si le port n’est pas ouvert, le capteur rejette la connexion depuis la console de gestion locale.

2. Connectez-vous à chaque capteur et exécutez les commandes suivantes :

   ```bash
   sudo cyberx-xsense-management-connect -ip <on-premises management console IP Address> -token < Copy the string that appears after the IP colon (:) from the Connection String field, Management Console Connection dialog box>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Configurer un site

La carte d’entreprise par défaut fournit une vue globale de vos appareils selon plusieurs niveaux d’emplacements géographiques.

La visualisation de vos appareils peut être nécessaire lorsque la structure organisationnelle et les autorisations utilisateur sont complexes. Dans ces cas, la configuration du site peut être déterminée par une structure organisationnelle globale, en plus de la structure standard du site ou de la zone.

Pour prendre en charge cet environnement, vous devez créer une topologie d’entreprise globale basée sur les unités commerciales, les régions, les sites et les zones de votre organisation. Vous devez également définir des autorisations d’accès utilisateur autour de ces entités à l’aide de groupes d’accès.

Les groupes d’accès permettent de mieux contrôler l’emplacement où les utilisateurs gèrent et analysent des appareils dans la plateforme Defender pour IoT.

### <a name="how-it-works"></a>Fonctionnement

Vous pouvez définir une unité commerciale et une région pour chaque site de votre organisation. Vous pouvez ensuite ajouter des zones, qui sont des entités logiques existant dans votre réseau. 

Attribuez au moins un capteur par zone. Le modèle à cinq niveaux offre la flexibilité et la granularité requises pour fournir un système de protection qui reflète la structure de votre organisation.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagramme montrant les capteurs et la relation régionale.":::

Dans la vue Enterprise, vous pouvez modifier vos sites directement. Lorsque vous sélectionnez un site dans la vue Entreprise, le nombre d’alertes ouvertes s’affiche en regard de chaque zone.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Capture d’écran d’une carte de la console de gestion locale avec la superposition des données de Berlin.":::

**Pour configurer un site :**

1. Ajoutez de nouvelles unités commerciales pour refléter la structure logique de votre organisation.

   1. Dans la vue Entreprise, sélectionnez **Tous les sites** > **Gérer les unités commerciales**.

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="Sélectionnez Gérer une unité commerciale dans le menu déroulant Tous les sites de l’écran Vue Entreprise.":::

   1. Entrez le nom de la nouvelle unité commerciale et sélectionnez **AJOUTER**.

1. Ajoutez de nouvelles régions pour refléter les régions de votre organisation.

   1. Dans la vue Entreprise, sélectionnez **Toutes les régions** > **Gérer les régions**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="Sélectionnez Toutes les régions, puis Gérer les régions pour gérer les régions dans votre entreprise.":::

   1. Entrez le nom de la nouvelle région et sélectionnez **AJOUTER**.

1. Ajoutez un site.

   1. Dans la vue Entreprise, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: dans la barre supérieure. Votre curseur apparaît sous la forme d’un signe plus ( **+** ).

   1. Placez le **+** à l’emplacement du nouveau site et sélectionnez-le. La boîte de dialogue **Créer un site** s’ouvre.

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Capture d’écran de la vue Créer un site.":::

   1. Définissez le nom et l’adresse physique du nouveau site, puis sélectionnez **ENREGISTRER**. Le nouveau site s’affiche sur la carte des sites.

4. [Ajoutez des zones à un site](#create-enterprise-zones).

5. [Connectez les capteurs](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console).

6. [Attribuez un capteur aux zones de site](#assign-sensors-to-zones).

### <a name="delete-a-site"></a>Suppression d’un site

Si vous n’avez plus besoin d’un site, vous pouvez le supprimer de votre console de gestion locale.

**Pour supprimer un site :**

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom du site, puis sélectionnez **Supprimer le site**. La boîte de confirmation s’affiche et vous permet de confirmer que vous souhaitez supprimer le site.

2. Dans la zone de confirmation, sélectionnez **CONFIRMER**.

## <a name="create-enterprise-zones"></a>Créer des zones d’entreprise

Les zones sont des entités logiques qui vous permettent de diviser les appareils d’un site en groupes selon différentes caractéristiques. Par exemple, vous pouvez créer des groupes pour les lignes de production, les sous-stations, les zones de site ou les types d’appareils. Vous pouvez définir des zones en fonction des caractéristiques qui conviennent à votre organisation.

Vous configurez des zones dans le cadre du processus de configuration du site.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Capture d’écran de la vue Zones dans Gestion des sites.":::

Le tableau suivant décrit les paramètres de la fenêtre **Gestion des sites**.

| Paramètre | Description |
|--|--|
| Nom | Nom du capteur. Vous ne pouvez modifier ce nom qu’à partir du capteur. Pour plus d’informations, consultez le guide de l’utilisateur Defender pour IoT. |
| IP | Adresse IP du capteur. |
| Version | Version du capteur. |
| Connectivité | État de connectivité du capteur. L’état peut être **Connecté** ou **Déconnecté**. |
| Dernière mise à niveau | Date de la dernière mise à niveau. |
| Progression de la mise à niveau | La barre de progression indique l’état du processus de mise à niveau, comme suit :<br />- Chargement du package<br />- Préparation de l’installation<br />- Arrêt des processus<br />- Sauvegarde de données<br />- Prise d’un instantané<br />- Mise à jour de la configuration<br />- Mise à jour des dépendances<br />- Mise à jour des bibliothèques<br />- Mise à jour corrective des bases de données<br />- Démarrage des processus<br />- Validation de la validité du système<br />- Validation réussie<br />- Réussite<br />- Échec<br />- Mise à niveau démarrée<br />- Démarrage de l’installationogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Pour plus d’informations sur la mise à niveau, adressez-vous à [Support Microsoft](https://support.microsoft.com/) pour obtenir de l’aide. |
| Appareils | Nombre de d’appareils OT que le capteur surveille. |
| Alertes | Nombre d’alertes sur le capteur. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Permet d’attribuer un capteur à des zones. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Permet de supprimer un capteur déconnecté du site. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Indique le nombre de capteurs actuellement connectés à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Indique le nombre de ressources OT actuellement connectées à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Indique le nombre d’alertes envoyées par les capteurs qui sont attribués à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Libère des capteurs des zones. |

**Pour ajouter une zone à un site :**

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom du site, puis sélectionnez **Ajouter une zone**. La boîte de dialogue **Créer une zone** s’affiche.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Capture d’écran de la vue Créer une zone.":::

1. Entrez le nom de la zone.

1. Entrez une description de la nouvelle zone qui indique clairement les caractéristiques que vous avez utilisées pour diviser le site en zones.

1. Sélectionnez **SAVE** (Enregistrer). La nouvelle zone s’affiche dans la fenêtre **Gestion des sites** sous le site auquel cette zone appartient.

**Pour modifier une zone :**

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom de la zone, puis sélectionnez **Modifier la zone**. La boîte de dialogue **Modifier la zone** s’affiche.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Capture d’écran qui affiche la boîte de dialogue Modifier la zone.":::

1. Modifiez les paramètres de la zone et sélectionnez **ENREGISTRER**.

**Pour supprimer une zone :**

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom de la zone, puis sélectionnez **Supprimer la zone**.

1. Dans la zone de confirmation, sélectionnez **OUI**.

**Pour filtrer selon l’état de la connectivité :**

- Dans l’angle supérieur gauche, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: à côté de **Connectivité**, puis sélectionnez l’une des options suivantes :

  - **Tout** : Présente tous les capteurs qui communiquent avec cette console de gestion locale.

  - **Connecté** : Présente uniquement les capteurs connectés.

  - **Déconnecté** : Présente uniquement les capteurs déconnectés.

**Pour filtrer selon l’état de la mise à niveau :**

- Dans l’angle supérieur gauche, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: à côté d’**État de la mise à niveau**, puis sélectionnez l’une des options suivantes :

  - **Tout** : Présente tous les capteurs qui communiquent avec cette console de gestion locale.

  - **Valide** : Présente les capteurs dont l’état de mise à niveau est valide.

  - **En cours** : Présente les capteurs en cours de mise à niveau.

  - **Échec** : Présente les capteurs dont le processus de mise à niveau a échoué.

## <a name="assign-sensors-to-zones"></a>Attribuer des capteurs à des zones

Pour chaque zone, vous devez attribuer des capteurs qui analysent le trafic local et génèrent des alertes. Vous pouvez attribuer uniquement les capteurs qui sont connectés à la console de gestion locale.

**Pour attribuer un capteur :**

1. Sélectionnez **Gestion des sites**. Les capteurs non attribués s’affichent dans l’angle supérieur gauche de la boîte de dialogue.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Capture d’écran de la vue Capteurs non attribués.":::

1. Vérifiez que l’état de **Connectivité** est Connecté. Si ce n’est pas le cas, consultez [Connecter des capteurs à la console de gestion locale](#connect-sensors-to-the-on-premises-management-console) pour plus d’informations sur la connexion. 

1. Sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: pour le capteur que vous souhaitez attribuer.

1. Dans la boîte de dialogue **Attribuer un capteur**, sélectionnez l’unité commerciale, la région, le site et la zone à attribuer.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Capture d’écran de la vue Attribuer un capteur.":::

1. Sélectionnez **ATTRIBUER**.

**Pour annuler l’attribution d’un capteur et le supprimer :**

1. Déconnectez le capteur de la console de gestion locale. Pour plus d’informations, consultez [Connecter des capteurs à la console de gestion locale](#connect-sensors-to-the-on-premises-management-console).

1. Dans la fenêtre **Gestion des sites**, sélectionnez le capteur et sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false":::. Le capteur apparaît dans la liste des capteurs non attribués après quelques instants.

1. Pour supprimer le capteur non attribué du site, sélectionnez le capteur dans la liste des capteurs non attribués, puis sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::.

## <a name="see-also"></a>Voir aussi

[Résoudre les problèmes du capteur et de la console de gestion locale](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
