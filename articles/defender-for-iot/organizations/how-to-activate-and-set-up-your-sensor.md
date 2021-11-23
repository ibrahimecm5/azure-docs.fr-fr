---
title: Activer et configurer votre capteur
description: Cet article explique comment activer une console de capteur et s’y connecter.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 86c5215e91a32fd67a29e157df2c3ab8606f15b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278965"
---
# <a name="activate-and-set-up-your-sensor"></a>Activer et configurer votre capteur

Cet article explique comment activer un capteur et effectuer la configuration initiale.

Les utilisateurs Administrateurs procèdent à l’activation lors de la première connexion et lorsque la gestion de l’activation est nécessaire. La configuration permet de s’assurer que le capteur est configuré pour détecter et alerter de manière optimale.

Les analystes de sécurité et les utilisateurs en lecture seule ne peuvent pas activer un capteur ni générer un nouveau mot de passe.

## <a name="sign-in-and-activation-for-administrator-users"></a>Connexion et activation pour les utilisateurs Administrateurs

Les administrateurs qui se connectent pour la première fois doivent vérifier qu’ils ont accès aux fichiers d’activation et de récupération de mot de passe qui ont été téléchargés lors de l’intégration du capteur. Si ce n’est pas le cas, ils ont besoin des autorisations des rôles Azure Administrateur de la sécurité, Contributeur d’abonnement ou Propriétaire d’abonnement pour générer ces fichiers sur le portail Microsoft Defender pour IoT.

### <a name="first-time-sign-in-and-activation-checklist"></a>Liste de contrôle pour la première connexion et l’activation

Avant de se connecter à la console du capteur, les utilisateurs Administrateurs doivent avoir accès aux éléments suivants :

- L’adresse IP du capteur qui a été définie lors de l’installation.

- Les informations d’identification de connexion de l’utilisateur pour le capteur. Si vous avez téléchargé une image ISO pour le capteur, utilisez les informations d’identification par défaut que vous avez reçues lors de l’installation. Nous vous recommandons de créer un nouvel utilisateur *Administrateur* après l’activation.

- Un mot de passe initial. Si vous avez acheté un capteur préconfiguré à partir d’Arrow, vous devez générer un mot de passe lorsque vous vous connectez pour la première fois.

- Le fichier d’activation associé à ce capteur. Le fichier a été généré et téléchargé lors de l’intégration du capteur sur Defender pour IoT.

- Un certificat SSL/TLS signé par une autorité de certification dont votre entreprise a besoin.

### <a name="about-activation-files"></a>À propos des fichiers d’activation

Votre capteur a été intégré à Microsoft Defender pour IoT dans un mode d’administration spécifique :

| Type de mode | Description |
|--|--|
| **Mode connecté au cloud** | Les informations détectées par le capteur s’affichent dans la console du capteur. Les informations sur les alertes sont également transmises par le biais du hub IoT et peuvent être partagées avec d’autres services Azure, comme Microsoft Sentinel. Vous pouvez aussi activer les mises à jour automatiques du renseignement sur les menaces. |
| **Mode connecté localement** | Les informations détectées par le capteur s’affichent dans la console du capteur. Les informations de détection sont également partagées avec la console de gestion locale, si le capteur y est connecté. |

Un fichier d’activation du mode connecté localement ou connecté au cloud a été généré et téléchargé pour ce capteur pendant l’intégration. Le fichier d’activation contient des instructions pour le mode d’administration du capteur. *Un fichier d’activation unique doit être chargé sur chaque capteur que vous déployez.*  La première fois que vous vous connectez, vous devez télécharger le fichier d’activation approprié pour ce capteur.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Defender pour IoT dans le portail Azure, capteur intégré.":::

### <a name="about-certificates"></a>À propos des certificats

Après l’installation du capteur, un certificat auto-signé local est généré et utilisé pour accéder à la console du capteur. Quand un administrateur se connecte à la console pour la première fois, il est invité à intégrer un certificat SSL/TLS.

Deux niveaux de sécurité sont disponibles :

- répondre aux exigences de chiffrement et de certificat spécifiques requises par votre organisation, en téléchargeant le certificat signé par une autorité de certification ;
- Autorisez la validation entre la console de gestion et les capteurs connectés. La validation est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console.* Cette option est activée par défaut après l’installation.  

La console prend en charge les types de certificats suivants :

- Infrastructure à clé privée et d’entreprise (PKI privée)

- Infrastructure à clé publique (PKI publique)

- Généré localement sur l’appliance (auto-signé localement) 

  > [!IMPORTANT]
  > Nous vous recommandons de ne pas utiliser de certificat auto-signé par défaut. Le certificat n’est pas sécurisé et doit être utilisé uniquement pour les environnements de test. Le propriétaire du certificat ne peut pas être validé et la sécurité de votre système ne peut pas être assurée. N’utilisez jamais cette option pour les réseaux de production.

Pour plus d’informations sur l’utilisation de certificats, consultez [Gérer des certificats](how-to-manage-individual-sensors.md#manage-certificates).

### <a name="sign-in-and-activate-the-sensor"></a>Se connecter et activer le capteur

**Pour vous connecter et activer :**

1. Accédez à la console du capteur à partir de votre navigateur à l’aide de l’adresse IP définie lors de l’installation. La boîte de dialogue de connexion s’ouvre.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Capteur Microsoft Defender pour IoT.":::

1. Entrez les informations d’identification définies lors de l’installation du capteur ou sélectionnez l’option **Récupération du mot de passe**. Si vous avez acheté un capteur préconfiguré auprès d’Arrow, vous devez d’abord générer un mot de passe. Pour plus d’informations sur la récupération du mot de passe, consultez [Examiner l’échec du mot de passe lors de la connexion initiale](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Une fois que vous êtes connecté, la boîte de dialogue **Activation** s’ouvre. Sélectionnez **Charger** et accédez au fichier d’activation que vous avez téléchargé lors de l’intégration du capteur.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Sélectionnez Charger et accédez au fichier d’activation.":::

1. Sélectionnez le lien **Configuration réseau du capteur** si vous souhaitez modifier la configuration du réseau du capteur avant l’activation. Voir [Mettre à jour la configuration réseau du capteur avant l’activation](#update-sensor-network-configuration-before-activation).

1. Acceptez les conditions générales.

1. Sélectionnez **Activer**. La boîte de dialogue Certificat SSL/TLS s’ouvre.

1. Définissez un nom de certificat.
1. Chargez les fichiers de clé et CRT.
1. Entrez une phrase secrète et chargez un fichier PEM le cas échéant.
1. Sélectionnez **Suivant**. L’écran de validation s’ouvre. Par défaut, la validation entre la console de gestion et les capteurs connectés est activée.
1. Désactivez le bouton bascule **Activer la validation à l’échelle du système** pour désactiver la validation. Nous vous recommandons d’activer la validation.
1. Sélectionnez **Enregistrer**.  

Vous devrez peut-être actualiser votre écran après avoir chargé le certificat signé par une autorité de certification.

Pour plus d’informations sur le chargement d’un nouveau certificat, sur les paramètres de certificat pris en charge et sur l’utilisation des commandes de certificat CLI, consultez [Gérer des capteurs individuels](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Mettre à jour la configuration réseau du capteur avant l’activation  

Les paramètres de configuration du réseau du capteur ont été définis pendant l’installation du logiciel ou quand vous avez acheté le capteur préconfiguré. Les paramètres suivants ont été définis :

- Adresse IP
- DNS
- Passerelle par défaut
- Masque de sous-réseau
- Nom de l’hôte

Vous souhaiterez peut-être mettre à jour ces informations avant d’activer le capteur. Par exemple, vous devrez peut-être modifier les paramètres préconfigurés définis par Arrow. Vous pouvez également définir des paramètres de proxy avant d’activer votre capteur.

**Pour mettre à jour les paramètres de configuration du réseau du capteur :**

1. Sélectionnez le lien **Configuration réseau du capteur** dans la boîte de dialogue **Activation**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Configuration réseau du capteur.":::

2. Les paramètres définis lors de l’installation s’affichent. L’option permettant de définir le proxy est également disponible. Mettez à jour les paramètres si nécessaire, puis sélectionnez **Enregistrer**.

### <a name="activate-an-expired-license-versions-under-100"></a>Activer une licence ayant expiré (versions inférieures à 10.0)

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

### <a name="subsequent-sign-ins"></a>Connexions suivantes

Après la première activation, la console du capteur Microsoft Defender pour IoT s’ouvre après la connexion sans nécessiter de fichier d’activation. Vous n’avez besoin que de vos informations d’identification de connexion.

Après votre connexion, la console Microsoft Defender pour IoT s’ouvre.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Console Microsoft Defender pour IoT.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Configuration initiale et apprentissage (pour les administrateurs)

Après votre première connexion, le capteur Microsoft Defender pour IoT commence à surveiller automatiquement votre réseau. Les appareils réseau apparaîtront dans les sections de carte et d’inventaire des appareils. Microsoft Defender pour IoT commencera à détecter tous les incidents de sécurité et d’exploitation qui se produisent dans votre réseau et à vous en avertir. Vous pouvez ensuite créer des rapports et des requêtes basés sur les informations détectées.

Initialement, cette activité est effectuée en mode d’apprentissage, qui demande à votre capteur d’apprendre l’activité habituelle de votre réseau. Par exemple, le capteur apprend les appareils découverts dans votre réseau, les protocoles détectés dans le réseau et les transferts de fichiers qui se produisent entre des appareils spécifiques. Cette activité devient l’activité de ligne de base de votre réseau.

### <a name="review-and-update-basic-system-settings"></a>Examiner et mettre à jour les paramètres de base du système

Passez en revue les paramètres système du capteur pour vous assurer que le capteur est configuré de manière à détecter et à alerter de manière optimale.

Définissez les paramètres système du capteur. Par exemple :

- Définissez l’ICS (ou IoT) et les sous-réseaux séparés.

- Définissez les alias de port pour les protocoles spécifiques au site.

- Définissez les VLAN et les noms qui sont utilisés.

- Si le protocole DHCP est utilisé, définissez des plages DHCP légitimes.

- Définissez l’intégration à Active Directory et à des serveurs de messagerie, le cas échéant.

### <a name="disable-learning-mode"></a>Désactiver le mode d’apprentissage

Après avoir ajusté les paramètres système, vous pouvez laisser le capteur Microsoft Defender pour IoT s’exécuter en mode d’apprentissage jusqu’à ce que vous pensiez que les détections du système reflètent avec précision votre activité sur le réseau.

Le mode d’apprentissage devrait s’exécuter pendant environ deux à six semaines, selon la taille et la complexité de votre réseau. Une fois le mode d’apprentissage désactivé, toute activité qui diffère de votre activité de ligne de base déclenche une alerte.

**Pour désactiver le mode d’apprentissage :**

- Sélectionnez **Paramètres système** et désactivez l’option **Apprentissage**.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Première connexion pour les analystes de sécurité et les utilisateurs en lecture seule

Avant de vous connecter, vérifiez que vous disposez des éléments suivants :

- Adresse IP du capteur.
- Informations d’identification de connexion fournies par votre administrateur.

## <a name="console-tools-overview"></a>Outils de la console : Vue d’ensemble

Vous accédez aux outils de la console à partir du menu latéral.

**Navigation** 

| Fenêtre | Icône | Description |
| -----------|--|--|
| tableau de bord | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Visualiser un instantané intuitif de l’état de la sécurité du réseau. |
| Carte des appareils | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Visualiser les périphériques réseau et les connexions et propriétés des appareils dans une carte. Plusieurs options de zoom, de mise en surbrillance et de filtre sont disponibles pour afficher votre réseau. |
| Gestion des appareils | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | L’inventaire des appareils affiche la liste des attributs d’appareil détectés par ce capteur. Des options sont disponibles pour : <br /> – Trier ou filtrer les informations en fonction des champs de la table et voir les informations filtrées affichées. <br /> – Exporter les informations dans un fichier CSV. <br /> – Importer les détails du Registre Windows.|
| Alertes | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Afficher des alertes lorsque des violations de stratégie se produisent, que des écarts par rapport au comportement de la ligne de base se produisent ou que tout type d’activité suspecte dans le réseau est détecté. |
| Rapports | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Afficher des rapports basés sur des requêtes d’exploration de données. |

**Analyse**

| Fenêtre| Icône | Description |
|---|---|---|
| Chronologie des événements | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Visualiser une chronologie avec des informations sur les alertes, les événements réseau (informationnels) et les opérations des utilisateurs, telles que les ouvertures de session et les suppressions d’utilisateurs.|

**Navigation**

| Fenêtre | Icône | Description |
|---|---|---|
| Exploration de données | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Générer des informations complètes et granulaires sur les appareils de votre réseau dans différentes couches. |
| Investigation | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Afficher les tendances et les statistiques dans une large gamme de widgets. |
| Évaluation des risques | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Afficher la fenêtre **Vulnérabilités**. |

**Administrateur**

| Fenêtre | Icône | Description |
|---|---|---|
| Utilisateurs | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Définir des utilisateurs et des rôles avec différents niveaux d’accès. |
| Transfert | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Transmettre des informations d’alerte aux partenaires, ainsi que les sources internes (par exemple, Microsoft Sentinel), qui s’intègrent à Defender pour IoT, aux adresses e-mail, aux serveurs webhook, et bien plus encore. <br /> Voir [Transférer les informations d’alerte](how-to-forward-alert-information-to-partners.md) pour plus d’informations. |
| Paramètres système | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Configurer les paramètres système. Par exemple, définir les paramètres DHCP, fournir les détails du serveur de messagerie ou créer des alias de port. |
| Paramètres d’importation | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Afficher la fenêtre **Importer les paramètres**. Vous pouvez apporter des modifications manuelles dans les informations d’un appareil.<br /> Voir [Importer les informations des appareils](how-to-import-device-information.md) pour plus d’informations. |

**Support**

| Fenêtre| Icône | Description |
|----|---|---|
| Support | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Contactez [Support Microsoft](https://support.microsoft.com/) pour obtenir de l’aide. |

## <a name="see-also"></a>Voir aussi

[Recherche et packages de renseignement sur les menaces #](how-to-work-with-threat-intelligence-packages.md)

[Intégrer un capteur](getting-started.md#onboard-a-sensor)

[Gérer les fichiers d’activation de capteur](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Contrôler quel trafic est surveillé](how-to-control-what-traffic-is-monitored.md)
