---
title: Installation de Defender pour IoT
description: Découvrez comment installer un capteur et la console de gestion locale pour Microsoft Defender pour IoT.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 23776a9abf35cc8cdba512517163ab2f723b458d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343778"
---
# <a name="defender-for-iot-installation"></a>Installation de Defender pour IoT

Cet article explique comment installer les composants suivants de Microsoft Defender pour IoT :

- **Capteur** : Les capteurs Defender pour IoT collectent le trafic de partage de connexion Internet (ICS) à l’aide de la surveillance passive (sans agent). Passifs et non intrusifs, les capteurs n’ont aucun impact sur les réseaux et appareils OT et IoT. Le capteur se connecte à un port SPAN ou à un TAP réseau et commence immédiatement à surveiller votre réseau. Les détections s’affichent dans la console du capteur. Dans cette console, vous pouvez les visualiser, les examiner et les analyser dans une carte du réseau, un inventaire des appareils et un large éventail de rapports. Les exemples incluent des rapports d’évaluation des risques, des requêtes d’exploration de données et des vecteurs d’attaque.

- **Console de gestion locale** : La console de gestion locale vous permet de gérer les périphériques, les risques et les vulnérabilités. Vous pouvez également l’utiliser pour surveiller les menaces et répondre aux incidents dans l’ensemble de votre entreprise. Elle offre une vue unifiée de tous les périphériques réseau, des principaux indicateurs de risque IoT et OT et des alertes détectés dans les installations où les capteurs sont déployés. Utilisez la console de gestion locale pour visualiser et gérer les capteurs dans les réseaux hermétiques.

Cet article aborde les informations d’installation suivantes :

- **Matériel :** Détails des appliances physiques Dell et HPE.

- **Logiciels :** Installation du logiciel de la console de gestion locale et du capteur.

- **Appliances virtuelles :** Détails des machines virtuelles et installation du logiciel.

Après l’installation, connectez votre capteur à votre réseau.

## <a name="about-defender-for-iot-appliances"></a>À propos des appliances Defender pour IoT

Les sections suivantes fournissent des informations sur les appliances de détection Defender pour IoT et l’appliance pour la console de gestion locale de Defender pour IoT.

### <a name="physical-appliances"></a>Appliances physiques

Le capteur de l’appliance Defender pour IoT se connecte à un port SPAN ou à un TAP réseau et commence immédiatement à collecter le trafic ICS à l’aide de la surveillance passive (sans agent). Ce processus n’a aucun impact sur les réseaux et appareils OT, car il n’est pas placé sous le chemin d’accès aux données et n’analyse pas activement les appareils OT.

Les appliances de montage en rack suivantes sont disponibles :

| **Type de déploiement** | **Entreprise** | **Entreprise** | **SMB** |**SMB renforcé** |
|--|--|--|--|--|
| **Modèle** | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 | HPE EL300 |
| **Ports de surveillance** | jusqu’à 15 RJ45 ou 8 OPT | jusqu’à 8 RJ45 ou 6 OPT | jusqu’à 4 RJ45 | jusqu’à 5 RJ45 |
| **Bande passante maximale\*** | 3 Go/s | 1 Go/s | 200 Mo/s | 100 Mo/s |
| **Nombre maximal d’appareils protégés** | 10 000 | 10 000 | 1 000 | 800 |

*La capacité maximale de la bande passante peut varier en fonction de la distribution du protocole.

### <a name="virtual-appliances"></a>Appliances virtuelles

Les appliances virtuelles suivantes sont disponibles :

| **Type de déploiement** | **Entreprise** | **Entreprise** | **SMB** |
|--|--|--|--|
| **Description** | Appliance virtuelle pour les déploiements institutionnels | Appliance virtuelle pour les déploiements d’entreprise | Appliance virtuelle pour les déploiements SMB |
| **Bande passante maximale\*** | 2,5 Go/s | 800 Mo/s | 160 Mo/s |
| **Nombre maximal d’appareils protégés** | 10 000 | 10 000 | 800 |
| **Type de déploiement** | Entreprise | Enterprise | SMB |

*La capacité maximale de la bande passante peut varier en fonction de la distribution du protocole.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Spécifications matérielles de la console de gestion locale

 | Élément | Description |
 |----|--|
 **Description** | Dans une architecture multiniveau, la console de gestion locale offre une visibilité et un contrôle sur des sites géographiquement dispersés. Elle s’intègre aux piles de sécurité du centre des opérations de sécurité (SOC), notamment les SIEM, aux systèmes de tickets, aux pare-feu de nouvelle génération, aux plateformes sécurisées d’accès à distance et au bac à sable de programmes malveillants ICS Defender pour IoT. |
 **Type de déploiement** | Enterprise |
 **Type d’appliance**  | Machine virtuelle Dell R340 |
 **Nombre de capteurs gérés** | Illimité |

## <a name="prepare-for-the-installation"></a>Préparation de l’installation

### <a name="access-the-iso-installation-image"></a>Accéder à l’image d’installation ISO

L’image d’installation est accessible à partir du portail Defender pour IoT dans le portail Azure.

Pour accéder au fichier :

1. Connectez-vous à votre compte Defender pour IoT.

1. Accédez à la page **Capteur réseau** ou **Console de gestion locale** et sélectionnez une version à télécharger.

### <a name="install-from-dvd"></a>Installer à partir d’un DVD

Avant l’installation, vérifiez que vous disposez des éléments suivants :

- Un lecteur de DVD portable avec connecteur USB.

- Une image du programme d’installation ISO.

Pour effectuer l’installation :

1. Gravez l’image sur un DVD ou préparez un disque sur une clé. Connectez un lecteur de DVD portable à votre ordinateur, cliquez avec le bouton droit sur l’image ISO, puis sélectionnez **Graver sur le disque**.

1. Connectez le DVD ou le disque sur clé et configurez l’appliance pour qu’elle démarre à partir du DVD ou du disque sur clé.

### <a name="install-from-disk-on-a-key"></a>Installer à partir d’un disque sur clé

Avant l’installation, vérifiez que vous disposez des éléments suivants :

- Rufus installé.
  
- Un disque sur clé avec USB 3.0 et versions ultérieures. La taille minimale est de 4 Go.

- Une fichier image du programme d’installation ISO.

Le disque sur clé sera effacé au cours de ce processus.

Pour préparer un disque sur une clé :

1. Exécutez Rufus et sélectionnez **SENSOR ISO**.

1. Connectez le disque sur clé au panneau avant.

1. Définissez le BIOS du serveur de sorte qu’il démarre à partir de l’USB.

## <a name="dell-poweredger340xl-installation"></a>Installation de Dell PowerEdgeR340XL

Avant d’installer le logiciel sur l’appliance Dell, vous devez ajuster la configuration du BIOS de l’appliance :

- Les sections [Panneau avant de Dell PowerEdge R340](#dell-poweredge-r340-front-panel) et [Panneau arrière de Dell PowerEdge R340](#dell-poweredge-r340-back-panel) contiennent la description des panneaux avant et arrière, ainsi que les informations requises pour l’installation, telles que les pilotes et les ports.

- [Configuration du BIOS Dell](#dell-bios-configuration) fournit des informations sur la façon de se connecter à l’interface de gestion de l’appliance Dell et de configurer le BIOS.

- [Installation du logiciel (Dell R340)](#software-installation-dell-r340) décrit la procédure requise pour installer le logiciel du capteur Defender pour IoT.

### <a name="dell-poweredge-r340xl-requirements"></a>Configuration requise pour Dell PowerEdge R340XL

Pour installer l’appliance Dell PowerEdge R340XL, vous avez besoin des éléments suivants :

- Licence d’entreprise pour le contrôleur d’accès à distance Dell (iDrac)

- Fichier XML de configuration du BIOS

- Versions du microprogramme du serveur :

  - BIOS version 2.1.6

  - iDrac version 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Panneau avant de Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Panneau avant de Dell PowerEdge R340.":::

 1. Panneau de configuration gauche
 1. Lecteur optique (en option)
 1. Panneau de commande de droite
 1. Plaquette d’information
 1. Lecteurs  

### <a name="dell-poweredge-r340-back-panel"></a>Panneau arrière de Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Panneau arrière de Dell PowerEdge R340.":::

1. Port série
1. Port de carte NIC (Gb 1)
1. Port de carte NIC (Gb 1)
1. Logement de carte d’extension PCIe mi-hauteur
1. Logement de carte d’extension PCIe pleine hauteur
1. Bloc d’alimentation 1
1. Bloc d’alimentation 2
1. Bouton d’identification du système
1. Port du câble du voyant d’état du système (CMA)
1. Ports USB 3.0 (2)
1. Port réseau dédié iDRAC9
1. Port VGA

### <a name="dell-bios-configuration"></a>Configuration du BIOS Dell

La configuration du BIOS Dell est requise pour adapter l’appliance Dell à l’utilisation du logiciel.

L’appliance Dell est gérée par un iDRAC intégré avec contrôleur de cycle de vie. Le contrôleur de cycle de vie est incorporé à chaque serveur Dell PowerEdge et fournit des fonctionnalités qui vous permettent de déployer, mettre à jour, surveiller et gérer vos appliances Dell PowerEdge.

Pour établir la communication entre l’appliance Dell et l’ordinateur de gestion, vous devez définir l’adresse IP de l’iDRAC et l’adresse IP de l’ordinateur de gestion sur le même sous-réseau.

Lorsque la connexion est établie, le BIOS est configurable.

**Pour configurer le BIOS Dell** :

1. [Configurez l’adresse IP de l’iDRAC](#configure-idrac-ip-address).

1. [Configuration du BIOS](#configuring-the-bios)

#### <a name="configure-idrac-ip-address"></a>Configurer l’adresse IP de l’iDRAC

1. Mettez le capteur sous tension.

1. Si le système d’exploitation est déjà installé, sélectionnez la touche F2 pour entrer dans la configuration du BIOS.

1. Sélectionnez **Paramètres de l’iDRAC**.

1. Sélectionnez **Réseau**.

   > [!NOTE]
   > Pendant l’installation, vous devez configurer l’adresse IP et le mot de passe par défaut de l’iDRAC mentionnés dans les étapes suivantes. Après l’installation, vous modifierez ces définitions.

1. Remplacez l’adresse IPv4 statique par **10.100.100.250**.

1. Remplacez le masque de sous-réseau statique par **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Capture d’écran montrant le masque de sous-réseau statique.":::

1. Sélectionnez **Retour** > **Terminer**.

#### <a name="configuring-the-bios"></a>Configuration du BIOS

Vous devez configurer le BIOS de l’appliance si :

- Vous n’avez pas acheté votre appliance auprès d’Arrow.

- Vous disposez d’une appliance, mais vous n’avez pas accès au fichier config XML.

Une fois que vous avez accès au BIOS, accédez à **Paramètres de l’appareil**.

**Pour configurer le BIOS** :

1. Accédez au BIOS de l’appliance directement en utilisant un clavier et un écran, ou utilisez iDRAC.

   - Si l’appareil n’est pas une appliance Defender pour IoT, ouvrez un navigateur et accédez à l’adresse IP qui a été configurée précédemment. Connectez-vous avec les privilèges d’administrateur par défaut de Dell. Utilisez **root** comme nom d’utilisateur et **calvin** comme mot de passe.

   - Si l’appareil est une appliance Defender pour IoT, connectez-vous en utilisant **XXX** comme nom d’utilisateur et **XXX** comme mot de passe.

1. Une fois que vous avez accès au BIOS, accédez à **Paramètres de l’appareil**.

1. Choisissez la configuration contrôlée par RAID en sélectionnant **Contrôleur RAID intégré 1 : Utilitaire de configuration de Dell PERC\<PERC H330 Adapter\>** .

1. Sélectionnez **Gestion de la configuration**.

1. Sélectionnez **Créer un disque virtuel**.

1. Dans le champ **Sélectionner le niveau RAID**, sélectionnez **RAID5**. Dans le champ **Nom du disque virtuel**, entrez **ROOT** et sélectionnez **Disques physiques**.

1. Sélectionnez **Cocher tout** puis sélectionnez **Appliquer les modifications**.

1. Sélectionnez **OK**.

1. Faites défiler la page et sélectionnez **Créer un disque virtuel**.

1. Cochez la case **Confirmer** et sélectionnez **Oui**.

1. Sélectionnez **OK**.

1. Revenez à l’écran principal et sélectionnez **BIOS du système**.

1. Sélectionnez **Paramètres de démarrage**.

1. Pour l’option **Mode de démarrage**, sélectionnez **BIOS**.

1. Sélectionnez **Retour**, puis sélectionnez **Terminer** pour quitter les paramètres du BIOS.

### <a name="software-installation-dell-r340"></a>Installation du logiciel (Dell R340)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour effectuer l’installation :

1. Vérifiez que le support de la version est monté sur l’appliance de l’une des manières suivantes :

   - Connectez le CD externe ou le disque sur clé sur lequel se trouve la version.

   - Montez l’image ISO à l’aide de l’iDRAC. Après vous être connecté à l’iDRAC, sélectionnez la console virtuelle, puis sélectionnez **Support virtuel**.

1. Dans la section **Mapper le CD/DVD**, sélectionnez **Choisir un fichier**.

1. Choisissez le fichier image ISO de la version appropriée dans la boîte de dialogue qui s’ouvre.

1. Sélectionnez le bouton **Mapper l’appareil**.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Capture d’écran montrant un appareil mappé.":::

1. Le support est monté. Sélectionnez **Fermer**.

1. Démarrez l’appliance. Lorsque vous utilisez l’iDRAC, vous pouvez redémarrer les serveurs en sélectionnant le bouton **Contrôle Consul**. Ensuite, dans les **macros du clavier**, sélectionnez le bouton **Appliquer**, qui démarre la séquence Ctrl+Alt+Suppr.

1. Sélectionnez **Français**.

1. Sélectionnez **SENSOR-RELEASE-\<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sélectionnez la version de votre capteur et le type d’entreprise.":::

1. Définissez le profil de l’appliance et les propriétés du réseau :

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Capture d’écran montrant le profil de l’appliance et les propriétés du réseau.":::

   | Paramètre | Configuration |
   |--|--|
   | **Profil matériel** | **enterprise** |
   | **Interface de gestion** | **eno1** |
   | **Paramètres réseau (fournis par le client)** | - |
   |**Adresse IP du réseau de gestion :** | - |
   | **Masque de sous-réseau :** | - |
   | **Nom d’hôte de l’appliance :** | - |
   | **DNS :** | - |
   | **Adresse IP de la passerelle par défaut :** | - |
   | **Interfaces d’entrée :** |  Le système génère la liste des interfaces d’entrée pour vous. Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule. Vous n’avez pas besoin de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. |

1. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.  

1. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

1. Sélectionnez **Entrée** pour continuer.

## <a name="hpe-proliant-dl20-installation"></a>Installation de HPE ProLiant DL20

Cette section décrit le processus d’installation de HPE ProLiant DL20, qui comprend les étapes suivantes :

- Activer l’accès à distance et mettre à jour le mot de passe administrateur par défaut.
- Configurer les paramètres BIOS et RAID.
- Installer le logiciel.

### <a name="about-the-installation"></a>À propos de l’installation

- Les appliances Enterprise et SMB peuvent être installées. Le processus d’installation est identique pour les deux types d’appliances, à l’exception de la configuration des groupes.
- Un utilisateur administratif par défaut est fourni. Nous vous recommandons de modifier le mot de passe lors du processus de configuration du réseau.
- Pendant le processus de configuration du réseau, vous configurerez le port iLO sur le port réseau 1.
- Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

### <a name="hpe-proliant-dl20-front-panel"></a>Panneau avant de HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Panneau avant de HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Panneau arrière de HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Panneau arrière de HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Activer l’accès à distance et mettre à jour le mot de passe

Utilisez la procédure suivante pour configurer les options réseau et mettre à jour le mot de passe par défaut.

Pour activer et mettre à jour le mot de passe :

1. Connectez un écran et un clavier à l’appliance HP, allumez l’appliance, puis appuyez sur **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Capture d’écran montrant la fenêtre HPE ProLiant.":::

1. Accédez à **Utilitaires système** > **Configuration du système** > **Utilitaires de configuration iLO 5** > **Options réseau**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Capture d’écran montrant la fenêtre Configuration du système.":::

    1. Sélectionnez **Port réseau partagé : LOM** dans le champ **Carte réseau**.

    1. Désactivez DHCP.

    1. Entrez l’adresse IP, le masque de sous-réseau et l’adresse IP de la passerelle.

1. Sélectionnez **F10 : Enregistrer**.

1. Sélectionnez **Échap** pour revenir à **Utilitaires de configuration iLO 5**, puis sélectionnez **Gestion des utilisateurs**.

1. Sélectionnez **Modifier/supprimer un utilisateur**. L’administrateur est le seul utilisateur par défaut défini.

1. Modifiez le mot de passe par défaut et sélectionnez **F10 : Enregistrer**.

### <a name="configure-the-hpe-bios"></a>Configurer le BIOS de HPE

La procédure suivante décrit comment configurer le BIOS de HPE pour les appliances Enterprise et SMB.

**Pour configurer le BIOS du HPE** :

1. Sélectionnez **Utilitaires système** > **Configuration du système** > **Configuration du BIOS/de la plateforme (RBSU)** .

1. Dans le formulaire **Configuration du BIOS/de la plateforme (RBSU)** , sélectionnez **Options de démarrage**.

1. Modifiez **Mode de démarrage** en **Mode BIOS hérité**, puis sélectionnez **F10 : Enregistrer**.

1. Sélectionnez **Échap** deux fois pour fermer le formulaire **Configuration du système**.

#### <a name="for-the-enterprise-appliance"></a>Pour l’appliance d’entreprise

1. Sélectionnez **RAID 1 incorporé : HPE Smart Array P408i-a SR de 10e génération** > **Configuration des groupes** > **Créer un groupe**.

1. Dans le formulaire **Créer un groupe**, sélectionnez toutes les options. Trois options sont disponibles pour l’appliance **Enterprise**.

#### <a name="for-the-smb-appliance"></a>Pour l’appliance SMB

1. Sélectionnez **RAID 1 incorporé : HPE Smart Array P208i-a SR de 10e génération** > **Configuration des groupes** > **Créer un groupe**.

1. Sélectionnez **Passer au formulaire suivant**.

1. Dans le formulaire **Définir le niveau RAID**, définissez le niveau sur **RAID 5** pour les déploiements d’entreprise et **RAID 1** pour les déploiements SMB.

1. Sélectionnez **Passer au formulaire suivant**.

1. Dans le formulaire **Étiquette de lecteur logique**, entrez **Lecteur logique 1**.

1. Sélectionnez **Soumettre les modifications**.

1. Dans le formulaire **Soumettre**, sélectionnez **Retour au menu principal**.

1. Sélectionnez **F10 : Enregistrer** puis appuyez deux fois sur **Échap**.

1. Dans la fenêtre **Utilitaires système**, sélectionnez **Menu de démarrage unique**.

1. Dans le formulaire **Menu de démarrage unique**, sélectionnez **Menu de démarrage unique BIOS hérité**.

1. Les fenêtres **Démarrage hérité** et **Substitution de démarrage** s’affichent. Choisissez une option de substitution de démarrage ; par exemple, sur un CD-ROM, un lecteur USB, un HDD ou un interpréteur de commandes UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Capture d’écran montrant la première fenêtre Substitution de démarrage.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Capture d’écran montrant la deuxième fenêtre Substitution de démarrage.":::

### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Installation du logiciel (Appliance HPE ProLiant DL20)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour installer le logiciel :

1. Connectez l’écran et le clavier à l’appliance, puis connectez-vous à l’interface de ligne de commande.

1. Connectez un CD externe ou un disque sur clé sur lequel se trouve l’image ISO que vous avez téléchargée à partir de la page **Mises à jour** de Defender pour IoT dans le portail Azure.

1. Démarrez l’appliance.

1. Sélectionnez **Français**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Sélection du français dans la fenêtre de l’interface de ligne de commande.":::

1. Sélectionnez **SENSOR-RELEASE-\<version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Capture d’écran de la sélection d’une version.":::

1. Dans l’Assistant Installation, définissez le profil matériel et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Capture d’écran montrant l’Assistant Installation.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Enterprise** ou **Office** pour les déploiements SMB. |
    | **Interface de gestion** | **eno2** |
    | **Paramètres réseau par défaut (en général, les paramètres sont fournis par le client)** | **Adresse IP du réseau de gestion :** <br/> <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Adresse IP de la passerelle par défaut :**|
    | **Interfaces d’entrée :** | Le système génère la liste des interfaces d’entrée pour vous.<br/><br/>Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule : **eno5, eno3, eno1, eno6, eno4**.<br/><br/>**Pour HPE DL20 : Ne pas répertorier eno1, enp1s0f4u4 (interfaces iLo)** .<br/><br/>**PONT** : Il n’est pas nécessaire de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. Appuyez sur **Entrée** pour continuer. |

1. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.

1. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin des informations d’identification pour accéder à la plateforme pour la première fois.

1. Sélectionnez **Entrée** pour continuer.

## <a name="hpe-proliant-dl360-installation"></a>Installation de HPE ProLiant DL360

- Un utilisateur administratif par défaut est fourni. Nous vous recommandons de modifier le mot de passe lors de la configuration du réseau.

- Au cours de la configuration du réseau, vous allez configurer le port iLO.

- Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

### <a name="hpe-proliant-dl360-front-panel"></a>Panneau avant de HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Panneau avant de HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Panneau arrière de HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Panneau arrière de HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Activer l’accès à distance et mettre à jour le mot de passe

Reportez-vous aux sections précédentes pour l’installation de HPE ProLiant DL20 :

- « Activer l’accès à distance et mettre à jour le mot de passe »

- « Configurer le BIOS de HPE »

La configuration d’entreprise est identique.

> [!Note]
> Dans le formulaire du groupe, assurez-vous de sélectionner toutes les options.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Installation à distance d’iLO (à partir d’un lecteur virtuel)

Cette procédure décrit l’installation d’iLO à partir d’un lecteur virtuel.

Pour effectuer l’installation :

1. Connectez-vous à la console iLO, puis cliquez avec le bouton droit sur l’écran des serveurs.

1. Sélectionnez **Console HTML5**.

1. Dans la console, sélectionnez l’icône de CD, puis choisissez l’option CD/DVD.

1. Sélectionnez **Fichier ISO local**.

1. Dans la boîte de dialogue, choisissez le fichier ISO approprié.

1. Accédez à l’icône de gauche, sélectionnez **Alimentation** puis **Réinitialiser**.

1. L’appliance redémarre et exécute le processus d’installation du capteur.

### <a name="software-installation-hpe-dl360"></a>Installation du logiciel (HPE DL360)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour effectuer l’installation :

1. Connectez l’écran et le clavier à l’appliance, puis connectez-vous à l’interface de ligne de commande.

1. Connectez un CD externe ou un disque sur une clé avec l’image ISO que vous avez téléchargée à partir de la page **Mises à jour** de Defender pour IoT dans le portail Azure.

1. Démarrez l’appliance.

1. Sélectionnez **Français**.

1. Sélectionnez **SENSOR-RELEASE-\<version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Capture d’écran qui montre la sélection de la version.":::

1. Dans l’Assistant Installation, définissez le profil de l’appliance et les propriétés du réseau.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Capture d’écran montrant l’Assistant Installation.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Société**. |
    | **Interface de gestion** | **eno2** |
    | **Paramètres réseau par défaut (fournis par le client)** | **Adresse IP du réseau de gestion :** <br> **Masque de sous-réseau :** <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Adresse IP de la passerelle par défaut :**|
    | **Interfaces d’entrée :**  | Le système génère une liste d’interfaces d’entrée pour vous.<br/><br/>Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule.<br/><br/> Vous n’avez pas besoin de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. |

1. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.

1. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme pour la première fois.

1. Sélectionnez **Entrée** pour continuer.

## <a name="hp-edgeline-300-installation"></a>Installation de HP EdgeLine 300

- Un utilisateur administratif par défaut est fourni. Nous vous recommandons de modifier le mot de passe lors de la configuration du réseau.

- Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

### <a name="hp-edgeline-300-back-panel"></a>Panneau arrière de HP EdgeLine 300

:::image type="content" source="media/tutorial-install-components/edgeline-el300-panel.png" alt-text="Vue du panneau arrière de l’EL300":::

### <a name="enable-remote-access"></a>Activer l'accès à distance

1. Entrez l’adresse IP iSM dans votre navigateur web.

1. Connectez-vous à l’aide du nom d’utilisateur et du mot de passe par défaut trouvés sur votre appliance.

1. Accédez à **Wired and Wireless Network (Réseau câblé et sans fil)**  > **IPv4**.

    :::image type="content" source="media/tutorial-install-components/wired-and-wireless.png" alt-text="Accédez aux sections mis en évidence.":::

1. Désactivez le **bouton bascule DHCP**.

1. Configurez les adresses IPv4 comme suit :
    - **Adresse IPV4** : `192.168.1.125`
    - **Masque de sous-réseau IPv4** : `255.255.255.0`
    - **Passerelle IPv4** : `192.168.1.1`

1. Sélectionnez **Appliquer**.

1. Déconnectez-vous et redémarrez l’appliance.

### <a name="configure-the-bios"></a>Configurer le BIOS

La procédure suivante décrit comment configurer le BIOS pour l’appliance HP EL300.

**Pour configurer le BIOS** :

1. Activez l’appliance et appuyez sur la touche **F9** pour entrer dans le BIOS.

1. Sélectionnez **Advanced** (Avancé), puis faites défiler jusqu’à **CSM Support** (Prise en charge de CSM).

    :::image type="content" source="media/tutorial-install-components/csm-support.png" alt-text="Activez la prise en charge de CSM pour ouvrir le menu supplémentaire.":::

1. Appuyez sur **Entrée** pour activer la prise en charge de CSM.

1. Accédez à **Storage** (Stockage) et appuyez sur **+/-** pour le définir sur Legacy (Hérité).

1. Accédez à **Video** (Vidéo) et appuyez sur **+/-** pour le définir sur Legacy (Hérité).

    :::image type="content" source="media/tutorial-install-components/storage-and-video.png" alt-text="Accédez à Storage (Stockage) et Video (Vidéo) et remplacez la valeur par Legacy (Hérité).":::

1. Accédez à **Boot** (Démarrage)  > **Boot mode select** (Sélection du mode de démarrage).

1. Appuyez sur **+/-** pour le définir sur Legacy (Hérité).

    :::image type="content" source="media/tutorial-install-components/boot-mode.png" alt-text="Modifiez la sélection du mode de démarrage sur Legacy (Hérité).":::

1. Accédez à **Save & Exit** (Enregistrer et quitter).

1. Sélectionnez **Save Changes and Exit** (Enregistrer les modifications et quitter).

    :::image type="content" source="media/tutorial-install-components/save-and-exit.png" alt-text="Enregistrez vos modifications et quittez le système.":::

1. Sélectionnez **Yes** (Oui), et l’appliance redémarre.

1. Appuyez sur la touche **F11** pour accéder au **menu de démarrage**.

1. Sélectionnez le périphérique avec l’image du capteur. Soit **DVD**, soit **USB**.

1. Sélectionnez votre langue.

1. Sélectionnez **sensor-10.0.3.12-62a2a3f724 Office : 4 UC, RAM 8 GO, STOCKAGE 100 GO**.

    :::image type="content" source="media/tutorial-install-components/sensor-select-screen.png" alt-text="Sélectionnez la version du capteur comme indiqué.":::

1. Dans l’Assistant Installation, définissez le profil de l’appliance et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/appliance-parameters.png" alt-text="Définissez le profil de l’appliance et les configurations réseau avec les paramètres suivants.":::

    | Paramètre | Configuration |
    |--|--|
    | **configurer le profil matériel** | **office** |
    | **configurer l’interface réseau de gestion** | **enp3s0** <br /> ou <br />**valeur possible** |
    | **configurer l’adresse IP du réseau de gestion** | **adresse IP fournie par le client** |
    | **configurer le masque de sous-réseau** | **adresse IP fournie par le client** |
    | **configurer DNS** | **adresse IP fournie par le client** |
    | **configurer l’adresse IP par défaut de la passerelle** | **adresse IP fournie par le client** |
    | **configurer les interfaces d’entrée** | **enp4s0** <br /> ou <br />**valeur possible** |
    | **configurer les interfaces de pont** | N/A |

1. Acceptez les paramètres et continuez en entrant `Y`.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Installation du capteur pour l’appliance virtuelle

Vous pouvez déployer la machine virtuelle pour le capteur Defender pour IoT dans les architectures suivantes :

| Architecture | Spécifications | Utilisation | Commentaires |
|---|---|---|---|
| **Entreprise** | Processeur : 8<br/>Mémoire : RAM 32 Go<br/>HDD : 1800 Go | Environnement de production | Architecture par défaut et la plus courante |
| **Small Business** | Processeur : 4 <br/>Mémoire : RAM 8 Go<br/>HDD : 500 Go | Environnements de test ou petits environnements de production | -  |
| **Office** | Processeur : 4<br/>Mémoire : RAM 8 Go<br/>HDD : 100 Go | Petits environnements de test | -  |

### <a name="prerequisites"></a>Prérequis

La console de gestion locale prend en charge les options de déploiement de VMware et Hyper-V. Avant de commencer l’installation, assurez-vous de disposer des éléments suivants :

- Hyperviseur VMware (ESXi 5.5 ou version ultérieure) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel

- Ressources matérielles disponibles pour la machine virtuelle

- Fichier d’installation ISO pour le capteur Microsoft Defender pour IoT

Assurez-vous que l’hyperviseur fonctionne.

### <a name="create-the-virtual-machine-esxi"></a>Créer la machine virtuelle (ESXi)

1. Connectez-vous à ESXi, choisissez le **magasin de données** approprié et sélectionnez **Navigateur de magasin de données**.

1. **Chargez** l’image, puis sélectionnez **Fermer**.

1. Accédez à **Machines virtuelles**, puis sélectionnez **Créer/inscrire une machine virtuelle**.

1. Sélectionnez **Créer une nouvelle machine virtuelle**, puis sélectionnez **Suivant**.

1. Ajoutez un nom de capteur et choisissez :

   - Compatibilité : **&lt;version ESXi la plus récente&gt;**

   - Famille de système d’exploitation invité : **Linux**

   - Version du système d’exploitation invité : **Ubuntu Linux (64 bits)**

1. Sélectionnez **Suivant**.

1. Choisissez le magasin de données approprié et sélectionnez **Suivant**.

1. Modifiez les paramètres du matériel virtuel en fonction de l’architecture requise.

1. Pour **Lecteur CD/DVD 1**, sélectionnez **Fichier ISO de magasin de données** et choisissez le fichier ISO que vous avez chargé précédemment.

1. Sélectionnez **Suivant** > **Terminer**.

### <a name="create-the-virtual-machine-hyper-v"></a>Créer la machine virtuelle (Hyper-V)

Cette procédure décrit comment créer une machine virtuelle à l’aide d’Hyper-V.

Pour créer une machine virtuelle :

1. Créez un disque virtuel dans Hyper-V Manager.

1. Sélectionnez **format = VHDX**.

1. Sélectionnez **type = Développement dynamique**.

1. Entrez le nom et l’emplacement du disque dur virtuel.

1. Entrez la taille requise (en fonction de l’architecture).

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Dans le menu **Actions**, créez une machine virtuelle.

1. Entrez un nom pour la machine virtuelle.

1. Sélectionnez **Spécifier la génération** > **Génération 1**.

1. Spécifiez l’allocation de mémoire (en fonction de l’architecture) et cochez la case de la mémoire dynamique.

1. Configurez la carte réseau en fonction de la topologie de réseau de votre serveur.

1. Connectez le VHDX créé précédemment à la machine virtuelle.

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Cliquez avec le bouton droit sur la nouvelle machine virtuelle, puis sélectionnez **Paramètres**.

1. Sélectionnez **Ajouter du matériel** et ajouter une nouvelle carte réseau.

1. Sélectionnez le commutateur virtuel qui sera connecté au réseau de gestion du capteur.

1. Allouez des ressources d’UC (selon l’architecture).

1. Connectez l’image ISO de la console de gestion à un lecteur de DVD virtuel.

1. Démarrez la machine virtuelle.

1. Dans le menu **Actions**, sélectionnez **Connexion** pour poursuivre l’installation du logiciel.

### <a name="software-installation-esxi-and-hyper-v"></a>Installation des logiciels (ESXi et Hyper-V)

Cette section décrit l’installation des logiciels ESXi et Hyper-V.

Pour effectuer l’installation :

1. Ouvrez la console de la machine virtuelle.

1. La machine virtuelle démarre à partir de l’image ISO et l’écran de sélection de la langue s’affiche. Sélectionnez **Français**.

1. Sélectionnez l’architecture requise.

1. Définissez le profil de l’appareil et les propriétés du réseau :

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | &lt;architecture requise&gt; |
    | **Interface de gestion** | **ens192** |
    | **Paramètres réseau (fournis par le client)** | **Adresse IP du réseau de gestion :** <br/>**Masque de sous-réseau :** <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Passerelle par défaut :** <br/>**Interfaces d’entrée :**|
    | **Interfaces de pont :** | Il n’est pas nécessaire de configurer l’interface de pont. Cette option est réservée à des cas d’usage particuliers. |

1. Entrez **O** pour accepter les paramètres.

1. Les informations d’identification de connexion sont automatiquement générées et présentées. Copiez le nom d’utilisateur et le mot de passe dans un endroit sûr, car ils sont requis à des fins de connexion et d’administration.

      - **Support** : Utilisateur administratif pour la gestion des utilisateurs.

      - **CyberX** : Équivalent de la racine pour accéder à l’appliance.

1. L’appliance redémarre.

1. Accédez à la console de gestion à l’aide de l’adresse IP précédemment configurée : `https://ip_address`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Capture d’écran qui montre l’accès à la console de gestion.":::

## <a name="on-premises-management-console-installation"></a>installation de la console de gestion locale

Avant d’installer le logiciel sur l’appliance, vous devez ajuster la configuration du BIOS de l’appliance :

### <a name="bios-configuration"></a>Configuration du BIOS

**Pour configurer le BIOS de votre appliance** :

1. [Activez l’accès à distance et mettez à jour le mot de passe](#enable-remote-access-and-update-the-password).

1. [Configurez le BIOS](#configure-the-hpe-bios).

### <a name="software-installation"></a>Installation de logiciels

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pendant le processus d’installation, vous pouvez ajouter une carte réseau secondaire. Si vous choisissez de ne pas installer la carte réseau secondaire lors de l’installation, vous pouvez [ajouter une carte réseau secondaire](#add-a-secondary-nic) ultérieurement.

Pour installer le logiciel :

1. Sélectionnez la langue de votre choix pour le processus d’installation.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Sélectionnez la langue de votre choix pour le processus d’installation.":::

1. Sélectionnez **MANAGEMENT-RELEASE-\<version\>\<deployment type\>** .

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Sélectionnez votre version.":::

1. Dans l’Assistant Installation, définissez les propriétés du réseau :

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Capture d’écran montrant le profil de l’appliance.":::

   | Paramètre | Configuration |
   |--|--|
   | **configurer l’interface réseau de gestion** | Pour Dell : **eth0, eth1** <br /> Pour HP : **enu1, enu2** <br>  ou <br />**valeur possible** |
   | **configurer l’adresse IP du réseau de gestion** | **adresse IP fournie par le client** |
   | **configurer le masque de sous-réseau** | **adresse IP fournie par le client** |
   | **configurer DNS** | **adresse IP fournie par le client** |
   | **configurer l’adresse IP par défaut de la passerelle** | **adresse IP fournie par le client** |

1. **(Facultatif)** Si vous souhaitez installer une carte réseau secondaire, définissez le profil d’appliance suivant et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Capture d’écran montrant les questions relatives à l’installation de la carte réseau secondaire.":::

   | Paramètre | Configuration |
   |--|--|
   | **configurer l’interface de surveillance du capteur (Facultatif)** | **eth1** ou **valeur possible** |
   | **configurer une adresse IP pour l’interface de surveillance du capteur** | **adresse IP fournie par le client** |
   | **configurer un masque de sous-réseau pour l’interface de surveillance du capteur** | **adresse IP fournie par le client** |

1. Acceptez les paramètres et continuez en saisissant `Y`.

1. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Copiez ces informations d’identification, car elles ne seront pas présentées de nouveau.":::  

   Enregistrez les noms d’utilisateur et les mots de passe. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

1. Sélectionnez **Entrée** pour continuer.

Pour plus d’informations sur la façon de trouver le port physique sur votre appliance, consultez [Rechercher votre port](#find-your-port).

### <a name="add-a-secondary-nic"></a>Ajouter une carte réseau secondaire

Vous pouvez renforcer la sécurité de votre console de gestion locale en ajoutant une carte réseau secondaire. En ajoutant une carte réseau secondaire, vous en aurez une dédiée à vos utilisateurs, et l’autre prendra en charge la configuration d’une passerelle pour les réseaux routés. La deuxième carte réseau est dédiée à tous les capteurs connectés dans une plage d’adresses IP.

:::image type="content" source="media/tutorial-install-components/secondary-nic.png" alt-text="Architecture globale de la carte réseau secondaire.":::

Les deux cartes réseau prendront en charge l’interface utilisateur (IU). Si vous choisissez de ne pas déployer de carte réseau secondaire, toutes les fonctionnalités seront disponibles via la carte réseau principale.

Si vous avez déjà configuré votre console de gestion locale et que vous souhaitez y ajouter une carte réseau secondaire, procédez comme suit :

1. Utilisez la commande de reconfiguration du réseau :

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Entrez les réponses suivantes aux questions ci-dessous :

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Entrez les réponses suivantes pour configurer votre appliance.":::

    | Paramètres | Réponse à entrer |
    |--|--|
    | **Adresse IP du réseau de gestion** | `N` |
    | **Masque de sous-réseau** | `N` |
    | **DNS** | `N` |
    | **Adresse IP par défaut de la passerelle** | `N` |
    | **Interface de surveillance du capteur (Facultatif. S’applique lorsque les capteurs se trouvent sur un segment de réseau différent. Pour plus d’informations, consultez les instructions d’installation.)**| `Y`, **sélectionnez une valeur possible** |
    | **Adresse IP pour l’interface de surveillance du capteur (accessible par les capteurs)** | `Y`, **adresse IP fournie par le client**|
    | **Masque de sous-réseau pour l’interface de surveillance du capteur (accessible par les capteurs)** | `Y`, **adresse IP fournie par le client** |
    | **Nom d’hôte** | **fourni par le client** |

1. Vérifiez tous les choix, puis entrez `Y` pour accepter les modifications. Le système redémarre.

### <a name="find-your-port"></a>Rechercher votre port

Si vous avez des difficultés à localiser le port physique sur votre appareil, vous pouvez utiliser la commande suivante :

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Cette commande fait clignoter le voyant du port pendant la durée spécifiée. Par exemple, si vous entrez `sudo ethtool -p eno1 120`, le port eno1 clignotera pendant deux minutes, ce qui vous permet de trouver le port à l’arrière de votre appliance.

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Appliance virtuelle : installation de la console de gestion locale

La machine virtuelle de la console de gestion locale prend en charge les architectures suivantes :

| Architecture | Spécifications | Utilisation |
|--|--|--|
| Enterprise <br/>(Par défaut et la plus courante) | Processeur : 8 <br/>Mémoire : RAM 32 Go<br/> HDD : 1,8 To | Environnements de production volumineux |
| Petite | Processeur : 4 <br/> Mémoire : RAM 8 Go<br/> HDD : 500 Go | Environnements de production volumineux |
| Office | Processeur : 4 <br/>Mémoire : RAM 8 Go <br/> HDD : 100 Go | Petits environnements de test |

### <a name="prerequisites"></a>Prérequis

La console de gestion locale prend en charge les options de déploiement de VMware et Hyper-V. Avant de commencer l’installation, vérifiez que les conditions suivantes sont remplies :

- L’hyperviseur VMware (ESXi 5.5 ou version ultérieure) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel.

- Les ressources matérielles sont disponibles pour la machine virtuelle.

- Vous disposez du fichier d’installation ISO pour la console de gestion locale.

- L’hyperviseur fonctionne.

### <a name="create-the-virtual-machine-esxi"></a>Créer la machine virtuelle (ESXi)

Pour créer une machine virtuelle (ESXi) :

1. Connectez-vous à ESXi, choisissez le **magasin de données** approprié et sélectionnez **Navigateur de magasin de données**.

1. Chargez l’image, puis sélectionnez **Fermer**.

1. Accédez à **Machines virtuelles**.

1. Sélectionnez **Créer/inscrire une machine virtuelle**.

1. Sélectionnez **Créer une nouvelle machine virtuelle**, puis sélectionnez **Suivant**.

1. Ajoutez un nom de capteur et choisissez :

   - Compatibilité : \<latest ESXi version>

   - Famille de système d’exploitation invité : Linux

   - Version du système d’exploitation invité : Ubuntu Linux (64 bits)

1. Sélectionnez **Suivant**.

1. Choisissez le magasin de données approprié et sélectionnez **Suivant**.

1. Modifiez les paramètres du matériel virtuel en fonction de l’architecture requise.

1. Pour **Lecteur CD/DVD 1**, sélectionnez **Fichier ISO de magasin de données** et choisissez le fichier ISO que vous avez chargé précédemment.

1. Sélectionnez **Suivant** > **Terminer**.

### <a name="create-the-virtual-machine-hyper-v"></a>Créer la machine virtuelle (Hyper-V)

Pour créer une machine virtuelle à l’aide d’Hyper-V :

1. Créez un disque virtuel dans Hyper-V Manager.

1. Sélectionnez le format **VHDX**.

1. Sélectionnez **Suivant**.

1. Sélectionnez le type **Développement dynamique**.

1. Sélectionnez **Suivant**.

1. Entrez le nom et l’emplacement du disque dur virtuel.

1. Sélectionnez **Suivant**.

1. Entrez la taille requise (en fonction de l’architecture).

1. Sélectionnez **Suivant**.

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Dans le menu **Actions**, créez une machine virtuelle.

1. Sélectionnez **Suivant**.

1. Entrez un nom pour la machine virtuelle.

1. Sélectionnez **Suivant**.

1. Sélectionnez **Génération** et définissez-la sur **Génération 1**.

1. Sélectionnez **Suivant**.

1. Spécifiez l’allocation de mémoire (en fonction de l’architecture) et cochez la case de la mémoire dynamique.

1. Sélectionnez **Suivant**.

1. Configurez la carte réseau en fonction de la topologie de réseau de votre serveur.

1. Sélectionnez **Suivant**.

1. Connectez le VHDX créé précédemment à la machine virtuelle.

1. Sélectionnez **Suivant**.

1. Vérifiez le résumé et sélectionnez **Terminer**.

1. Cliquez avec le bouton droit sur la nouvelle machine virtuelle, puis sélectionnez **Paramètres**.

1. Sélectionnez **Ajouter du matériel** et ajouter un nouvel adaptateur pour **Carte réseau**.

1. Pour **Commutateur virtuel**, sélectionnez le commutateur qui sera connecté au réseau de gestion du capteur.

1. Allouez des ressources d’UC (selon l’architecture).

1. Connectez l’image ISO de la console de gestion à un lecteur de DVD virtuel.

1. Démarrez la machine virtuelle.

1. Dans le menu **Actions**, sélectionnez **Connexion** pour poursuivre l’installation du logiciel.

### <a name="software-installation-esxi-and-hyper-v"></a>Installation des logiciels (ESXi et Hyper-V)

Le démarrage de la machine virtuelle démarre le processus d’installation à partir de l’image ISO.

Pour installer le logiciel :

1. Sélectionnez **Français**.

1. Sélectionnez l’architecture requise pour votre déploiement.

1. Définissez l’interface réseau pour le réseau de gestion du capteur : interface, IP, sous-réseau, serveur DNS et passerelle par défaut.

1. Les informations d’identification de connexion sont automatiquement générées. Enregistrez le nom d’utilisateur et le mot de passe. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

   L’appliance redémarre.

1. Accédez à la console de gestion à l’aide de l’adresse IP précédemment configurée : `<https://ip_address>`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Capture d’écran montrant l’écran de connexion de la console de gestion.":::

## <a name="legacy-appliances"></a>Appliances héritées

Cette section décrit les procédures d’installation pour les appliances *héritées* uniquement. Consultez [Identifier les appliances requises](how-to-identify-required-appliances.md#identify-required-appliances), si vous achetez une nouvelle appliance.

### <a name="nuvo-5006lp-installation"></a>Installation du Nuvo 5006LP

Cette section décrit la procédure d'installation du Nuvo 5006LP. Avant d'installer le logiciel sur le Nuvo 5006LP, vous devez ajuster la configuration du BIOS de l'appliance :

#### <a name="nuvo-5006lp-front-panel"></a>Panneau avant du Nuvo 5006LP

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_frontpanel.png" alt-text="Vue du panneau avant de l'appareil Nuvo 5006LP.":::

1. Bouton d'alimentation, voyant d'alimentation
1. Connecteurs vidéo DVI
1. Connecteurs vidéo HDMI
1. Connecteurs vidéo VGA
1. Commande marche/arrêt à distance et sortie LED d'état
1. Bouton de réinitialisation
1. Carte réseau de gestion
1. Ports permettant de recevoir les données mises en miroir

#### <a name="nuvo-back-panel"></a>Panneau arrière du Nuvo

:::image type="content" source="media/tutorial-install-components/nuvo5006lp_backpanel.png" alt-text="Vue du panneau arrière du Nuvo 5006lp.":::

1. Emplacement carte SIM
1. Micro et haut-parleurs
1. Ports COM
1. Connecteurs USB
1. Port d'alimentation DC (DC IN)

#### <a name="configure-the-nuvo-5006lp-bios"></a>Configurer le BIOS du Nuvo 5006LP

La procédure suivante explique comment configurer le BIOS du Nuvo 5006LP. Assurez-vous que le système d'exploitation a déjà été installé sur l'appliance.

**Pour configurer le BIOS** :

1. Mettez l'appliance sous tension.

1. Appuyez sur **F2** pour accéder à la configuration du BIOS.

1. Accédez à **Power** et définissez « Power On after Power Failure » sur « S0-Power On ».

    :::image type="content" source="media/tutorial-install-components/nuvo-power-on.png" alt-text="Faites en sorte que votre Nuvo 5006 s'allume après une panne de courant.":::

1. Accédez à **Boot** et vérifiez que l'option **PXE Boot to LAN** est définie sur **Disabled**.

1. Appuyez sur **F10** pour enregistrer, puis sélectionnez **Exit**.

#### <a name="software-installation-nuvo-5006lp"></a>Installation du logiciel (Nuvo 5006LP)

Le processus d'installation prend environ 20 minutes. Après l'installation, le système redémarre plusieurs fois.

**Pour installer le logiciel** :

1. Connectez le CD externe ou le disque sur clé avec l'image ISO.

1. Démarrez l'appliance.

1. Sélectionnez **Français**.

1. Sélectionnez **XSENSE-RELEASE-\<version> Office...** .

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sélectionnez la version du capteur à installer.":::

1. Définissez l'architecture de l'appliance et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Définissez l'architecture du Nuvo et les propriétés du réseau.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Office**. |
    | **Interface de gestion** | **eth0** |
    | **Adresse IP du réseau de gestion** | **adresse IP fournie par le client** |
    | **Masque du sous-réseau de gestion** | **adresse IP fournie par le client** |
    | **DNS** | **adresse IP fournie par le client** |
    | **Adresse IP par défaut de la passerelle** | **0.0.0.0** |
    | **Interface d'entrée** | La liste des interfaces d'entrée est automatiquement générée par le système. <br />Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule. |
    | **Interface de pont** | - |

1. Acceptez les paramètres et continuez en entrant `Y`.

Les informations d'identification sont automatiquement générées au bout d'une dizaine de minutes. Enregistrez le nom d’utilisateur et le mot de passe. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

### <a name="fitlet2-mini-sensor-installation"></a>Installation du mini-capteur Fitlet2

Cette section décrit la procédure d'installation du Fitlet2. Avant d'installer le logiciel sur le Fitlet2, vous devez ajuster la configuration du BIOS de l'appliance :

#### <a name="fitlet2-front-panel"></a>Panneau avant du Fitlet2

:::image type="content" source="media/tutorial-install-components/fitlet-front-panel.png" alt-text="Vue du panneau avant du Fitlet 2.":::

#### <a name="fitlet2-back-panel"></a>Panneau arrière du Fitlet2

:::image type="content" source="media/tutorial-install-components/fitlet2-back-panel.png" alt-text="Vue du panneau arrière du Fitlet2.":::

#### <a name="configure-the-fitlet2-bios"></a>Configurez le BIOS du Fitlet2.

**Pour configurer le BIOS du Fitlet2** :

1. Mettez l'appliance sous tension.

1. Accédez à **Main** > **OS Selection**.

1. Appuyez sur **+/-** pour sélectionner **Linux**.

    :::image type="content" source="media/tutorial-install-components/fitlet-linux.png" alt-text="Définissez le système d'exploitation sur Linux sur votre Fitlet2.":::

1. Vérifiez que la date et l'heure du système sont mises à jour avec la date et l'heure d'installation.

1. Accédez à **Advanced** et sélectionnez **ACPI Settings**.

1. Sélectionnez **Enable Hibernation** et appuyez sur **+/-** pour sélectionner **Disabled**.

    :::image type="content" source="media/tutorial-install-components/disable-hibernation.png" alt-text="Désactiver le mode de mise en veille prolongée sur votre Fitlet2.":::

1. Appuyez sur **Échap**.

1. Accédez à **Advanced** > **TPM Configuration**.

1. Sélectionnez **fTPM** et appuyez sur **+/-** pour sélectionner **Disabled**.

1. Appuyez sur **Échap**.

1. Accédez à **CPU Configuration** > **VT-d**.

1. Appuyez sur **+/-** pour sélectionner **Enabled**.

1. Accédez à **CSM Configuration** > **CSM Support**.

1. Appuyez sur **+/-** pour sélectionner **Enabled**.

1. Accédez à **Advanced** > **Boot option filter [Legacy only]** et définissez la valeur des champs suivants sur **Legacy** :

    - Réseau
    - Stockage
    - Vidéo
    - Autre PCI

    :::image type="content" source="media/tutorial-install-components/legacy-only.png" alt-text="Définissez tous les champs sur Legacy.":::

1. Appuyez sur **Échap**.

1. Accédez à **Security** > **Secure Boot Customization**.

1. Appuyez sur **+/-** pour sélectionner **Disabled**.

1. Appuyez sur **Échap**.

1. Accédez à **Boot** > **Boot mode** et sélectionnez **Legacy**.

1. Sélectionnez **Boot Option #1 – [USB CD/DVD]** .

1. Sélectionnez **Save & Exit**.

#### <a name="software-installation-fitlet2"></a>Installation du logiciel (Fitlet2)

Le processus d'installation prend environ 20 minutes. Après l'installation, le système redémarre plusieurs fois.

1. Connectez le CD externe ou le disque sur clé avec l'image ISO.

1. Démarrez l'appliance.

1. Sélectionnez **Français**.

1. Sélectionnez **XSENSE-RELEASE-\<version> Office...** .

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Sélectionnez la version du capteur à installer.":::

    > [!Note]
    > Ne sélectionnez pas Ruggedized.

1. Définissez l'architecture de l'appliance et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/nuvo-profile-appliance.png" alt-text="Définissez l'architecture du Nuvo et les propriétés du réseau.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Office**. |
    | **Interface de gestion** | **em1** |
    | **Adresse IP du réseau de gestion** | **adresse IP fournie par le client** |
    | **Masque du sous-réseau de gestion** | **adresse IP fournie par le client** |
    | **DNS** | **adresse IP fournie par le client** |
    | **Adresse IP par défaut de la passerelle** | **0.0.0.0** |
    | **Interface d'entrée** | La liste des interfaces d'entrée est automatiquement générée par le système. <br />Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule. |
    | **Interface de pont** | - |

1. Acceptez les paramètres et continuez en entrant `Y`.

Les informations d'identification sont automatiquement générées au bout d'une dizaine de minutes. Enregistrez le nom d’utilisateur et le mot de passe. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

## <a name="post-installation-validation"></a>Validation après installation

Pour valider l’installation d’une appliance physique, vous devez effectuer de nombreux tests. Le même processus de validation s’applique à tous les types d’appliances.

Effectuez la validation à l’aide de l’interface graphique utilisateur ou de l’interface de ligne de commande. La validation est disponible pour l’utilisateur **Support** et l’utilisateur **CyberX**.

La validation après installation doit inclure les tests suivants :

- **Test de validité** : Vérifiez que le système fonctionne.

- **Version** : Vérifiez que la version est correcte.

- **ifconfig** : Vérifiez que toutes les interfaces d’entrée configurées pendant le processus d’installation fonctionnent.

### <a name="check-system-health-by-using-the-gui"></a>Vérifier l’intégrité du système à l’aide de l’interface graphique utilisateur

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Capture d’écran montrant la vérification de l’intégrité du système.":::

#### <a name="sanity"></a>Validité

- **Appliance** : Exécute la vérification de l’intégrité de l’appliance. Vous pouvez effectuer la même vérification à l’aide de la commande CLI `system-sanity`.

- **Version** : Affiche la version de l’appliance.

- **Propriétés réseau** : Affiche les paramètres réseau du capteur.

#### <a name="redis"></a>Redis

- **Mémoire** : Fournit une vue d’ensemble de l’utilisation de la mémoire, par exemple la quantité de mémoire utilisée et la quantité restante.

- **Clé la plus longue** : Affiche les clés les plus longues qui peuvent entraîner une utilisation intensive de la mémoire.

#### <a name="system"></a>Système

- **Journal principal** : Fournit les 500 dernières lignes du journal principal, ce qui vous permet de visualiser les lignes de journal récentes sans exporter l’intégralité du journal système.

- **Gestionnaire des tâches** : Traduit les tâches qui s’affichent dans le tableau des processus en couches comme suit :
  
  - Couche persistante (Redis)

  - Couche de cache (SQL)

- **Statistiques relatives au réseau** : Affiche les statistiques de votre réseau.

- **TOP** : Affiche la table des processus. Il s’agit d’une commande Linux qui fournit une vue dynamique en temps réel du système en cours d’exécution.

- **Vérification de la mémoire de sauvegarde** : Indique l’état de la mémoire de sauvegarde, en vérifiant les éléments suivants :

  - Emplacement du dossier de sauvegarde

  - Taille du dossier de sauvegarde

  - Limitations du dossier de sauvegarde

  - Date et heure de la dernière sauvegarde

  - Espace disponible pour les fichiers de sauvegarde supplémentaires

- **ifconfig** : Affiche les paramètres des interfaces physiques de l’appliance.

- **CyberX nload** : Affiche le trafic et la bande passante en utilisant les tests de six secondes.

- **Erreurs de Core, log** : Affiche les erreurs du fichier journal principal.

**Pour accéder à l’outil** :

1. Connectez-vous au capteur en utilisant les informations d’identification de l’utilisateur **Support**.

1. Sélectionnez **Statistiques du système** dans la fenêtre **Paramètres du système**.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="check-system-health-by-using-the-cli"></a>Vérifier l’intégrité du système à l’aide de l’interface de ligne de commande

Vérifiez que le système est opérationnel et qu’il est en cours d’exécution avant de tester l’intégrité du système.

**Pour tester l’intégrité du système** :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

1. Entrez `system sanity`.

1. Vérifiez que tous les services sont en vert (en cours d’exécution).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Capture d’écran montrant les services en cours d’exécution.":::

1. Vérifiez que **System is UP! (prod)** [Le système est opérationnel ! (prod)] s’affiche en bas.

Vérifiez que la version correcte est utilisée :

**Pour vérifier la version du système** :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

1. Entrez `system version`.

1. Vérifiez que la version correcte s’affiche.

Vérifiez que toutes les interfaces d’entrée configurées pendant le processus d’installation fonctionnent :

**Pour valider l’état du réseau du système** :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

1. Entrez `network list` (l’équivalent de la commande Linux `ifconfig`).

1. Confirmez que les interfaces d’entrée requises s’affichent. Par exemple, si deux cartes réseau Cuivre 4 ports sont installées, la liste doit compter 10 interfaces.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Capture d’écran montrant la liste des interfaces.":::

Vérifiez que vous pouvez accéder à la GUI web de la console :

**Pour vérifier que la gestion a accès à l’interface utilisateur** :

1. Connectez un ordinateur portable au port de gestion (**Gb1**) à l’aide d’un câble Ethernet.

1. Définissez l’adresse de la carte réseau de l’ordinateur portable pour qu’elle soit dans la même plage que celle de l’appliance.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Capture d’écran montrant l’accès de gestion à l’interface utilisateur.":::

1. Effectuez un test ping sur l’adresse IP de l’appliance à partir de l’ordinateur portable pour vérifier la connectivité (par défaut : 10.100.10.1).

1. Ouvrez le navigateur Chrome sur l’ordinateur portable et entrez l’adresse IP de l’appliance.

1. Dans la fenêtre **Votre connexion n’est pas privée**, sélectionnez **Avancé** et continuez.

1. Le test est réussi lorsque l’écran de connexion Defender pour IoT s’affiche.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Capture d’écran qui montre l’accès à la console de gestion.":::

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="you-cant-connect-by-using-a-web-interface"></a>Vous ne pouvez pas vous connecter à l’aide d’une interface web

1. Vérifiez que l’ordinateur que vous essayez de connecter se trouve sur le même réseau que l’appliance.

1. Vérifiez que le réseau de l’interface graphique utilisateur est connecté au port de gestion.

1. Effectuez un test ping sur l’adresse IP de l’appliance. Si vous n’avez pas l’utilitaire Ping :

   1. Connectez un moniteur et un clavier à l’appliance.

   1. Utilisez l’utilisateur **Support** et son mot de passe pour vous connecter.

   1. Utilisez la commande `network list` pour afficher l’adresse IP actuelle.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Capture d’écran montrant la liste des réseaux.":::

1. Si les paramètres du réseau sont mal configurés, utilisez la procédure suivante pour les modifier :

   1. Utilisez la commande `network edit-settings`.

   1. Pour modifier l’adresse IP du réseau de gestion, sélectionnez **O**.

   1. Pour modifier le masque de sous-réseau, sélectionnez **O**.

   1. Pour modifier le DNS, sélectionnez **O**.

   1. Pour modifier l’adresse IP de la passerelle par défaut, sélectionnez **O**.

   1. Pour la modification de l’interface d’entrée (capteur uniquement), sélectionnez **N**.

   1. Pour appliquer les paramètres, sélectionnez **O**.

1. Après le redémarrage, connectez-vous avec les informations d’identification de l’utilisateur Support et utilisez la commande `network list` pour vérifier que les paramètres ont été modifiés.

1. Essayez d’effectuer un test ping et de vous reconnecter à partir de l’interface graphique utilisateur.

### <a name="the-appliance-isnt-responding"></a>L’appliance ne répond pas

1. Connectez un moniteur et un clavier à l’appliance, ou utilisez PuTTY pour vous connecter à distance à l’interface de ligne de commande.

1. Utilisez les informations d’identification de l’utilisateur **Support** pour vous connecter.

1. Utilisez la commande `system sanity` et vérifiez que tous les processus sont en cours d’exécution.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Capture d’écran montrant la commande system sanity.":::

Pour tout autre problème, contactez [Support Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="configure-a-span-port"></a>Configurer un port SPAN

Un commutateur virtuel n’a pas de fonctionnalités de mise en miroir. Toutefois, vous pouvez utiliser le mode de proximité dans un environnement de commutateur virtuel. Le mode de proximité est un mode de fonctionnement, ainsi qu’une technique de sécurité, de supervision et d’administration, défini au niveau du commutateur virtuel, ou des ports. Par défaut, le mode de proximité est désactivé. Lorsque le mode de proximité est activé, les interfaces réseau de la machine virtuelle qui se trouvent dans le même groupe de ports utilisent le mode de proximité pour afficher tout le trafic réseau qui traverse ce commutateur virtuel. Vous pouvez implémenter une solution de contournement avec ESXi ou Hyper-V.

:::image type="content" source="media/tutorial-install-components/purdue-model.png" alt-text="Capture d’écran de l’emplacement du capteur dans votre architecture.":::

### <a name="configure-a-span-port-with-esxi"></a>Configurer un port SPAN avec ESXi

**Pour configurer un port SPAN avec ESXi** :

1. Ouvrez les propriétés du commutateur virtuel.

1. Sélectionnez **Ajouter**.

1. Sélectionnez **Machine virtuelle** > **Suivant**.

1. Insérez une étiquette réseau **Réseau SPAN**, sélectionnez **ID VLAN** > **Tout**, puis sélectionnez **Suivant**.

1. Sélectionnez **Terminer**.

1. Sélectionnez **Réseau SPAN** > **Modifier*.

1. Sélectionnez **Sécurité** et vérifiez que la stratégie **Mode de proximité** est définie sur **Accepter le mode**.

1. Sélectionnez **OK**, puis sélectionnez **Fermer** pour fermer les propriétés du commutateur virtuel.

1. Ouvrez les propriétés de la **machine virtuelle XSense**.

1. Pour **Carte réseau 2**, sélectionnez le réseau **SPAN**.

1. Sélectionnez **OK**.

1. Connectez-vous au capteur et vérifiez que la mise en miroir fonctionne.

### <a name="configure-a-span-port-with-hyper-v"></a>Configurer un port SPAN avec Hyper-V

Avant de commencer, vous devez :

- Vérifier qu’aucune instance d’appliance virtuelle n’est en cours d’exécution.

- Activer SPAN sur le port de données, et non sur le port de gestion.

- Vérifier que la configuration SPAN du port de données n’est pas définie avec une adresse IP.

**Pour configurer un port SPAN avec Hyper-V** :

1. Ouvrez le gestionnaire de commutateur virtuel.

1. Dans la liste des commutateurs virtuels, sélectionnez **Nouveau commutateur réseau virtuel** > **Externe** comme type de carte réseau avec SPAN dédiée.

    :::image type="content" source="media/tutorial-install-components/new-virtual-network.png" alt-text="Capture d’écran de la sélection de Nouveau réseau virtuel et de l’option Externe avant la création du commutateur virtuel.":::

1. Sélectionnez **Créer un commutateur virtuel**.

1. Sous Type de connexion, sélectionnez **Réseau externe**.

1. Vérifiez que la case **Autoriser le système d’exploitation de gestion à partager cette carte réseau** est cochée.

   :::image type="content" source="media/tutorial-install-components/external-network.png" alt-text="Sélectionnez Réseau externe, puis Autoriser le système d’exploitation de gestion à partager cette carte réseau.":::

1. Sélectionnez **OK**.

#### <a name="attach-a-span-virtual-interface-to-the-virtual-switch"></a>Attacher une interface virtuelle SPAN au commutateur virtuel

Vous pouvez attacher une interface virtuelle SPAN au commutateur virtuel via Windows PowerShell ou via le gestionnaire Hyper-V.

**Pour attacher une interface virtuelle SPAN au commutateur virtuel avec PowerShell** :

1. Sélectionnez le commutateur virtuel SPAN qui vient d’être ajouté, puis ajoutez une nouvelle carte réseau avec la commande suivante :

    ```bash
    ADD-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 -Name Monitor -SwitchName vSwitch_Span
    ```

1. Activez la mise en miroir des ports pour l’interface sélectionnée comme destination SPAN avec la commande suivante :

    ```bash
    Get-VMNetworkAdapter -VMName VK-C1000V-LongRunning-650 | ? Name -eq Monitor | Set-VMNetworkAdapter -PortMirroring Destination
    ```

    | Paramètre | Description |
    |--|--|
    | VK-C1000V-LongRunning-650 | Nom VA CPPM |
    |vSwitch_Span |Nom du commutateur virtuel SPAN récemment ajouté |
    |Superviser |Nom de l’adaptateur récemment ajouté |

1. Sélectionnez **OK**.

Ces commandes définissent l’adaptateur qui vient d’être ajouté avec le nom `Monitor`. Si vous utilisez le gestionnaire Hyper-V, le matériel de l’adaptateur récemment ajouté est défini avec le nom `Network Adapter`.

**Pour attacher une interface virtuelle SPAN au commutateur virtuel avec le gestionnaire Hyper-V** :

1. Sous la liste du matériel, sélectionnez **Carte réseau**.

1. Dans le champ Commutateur virtuel, sélectionnez **vSwitch_Span**.

    :::image type="content" source="media/tutorial-install-components/vswitch-span.png" alt-text="Capture d’écran montrant la sélection des options suivantes sur l’écran du commutateur virtuel.":::

1. Dans la liste du matériel, sous la liste déroulante des cartes réseau, sélectionnez **Fonctionnalités avancées**.

1. Dans la section Mise en miroir des ports, sélectionnez **Destination** comme mode de mise en miroir pour la nouvelle interface virtuelle.

    :::image type="content" source="media/tutorial-install-components/destination.png" alt-text="Capture d’écran montrant les sélections nécessaires pour configurer le mode de mise en miroir.":::

1. Sélectionnez **OK**.

#### <a name="enable-microsoft-ndis-capture-extensions-for-the-virtual-switch"></a>Activer les extensions Microsoft NDIS Capture pour le commutateur virtuel

Les extensions Microsoft NDIS Capture doivent être activées pour le nouveau commutateur virtuel.

**Pour activer les extensions Microsoft NDIS Capture pour le commutateur virtuel nouvellement ajouté** :

1. Ouvrez le gestionnaire de commutateur virtuel sur l’hôte Hyper-V.

1. Dans la liste des commutateurs virtuels, développez le nom du commutateur virtuel `vSwitch_Span` et sélectionnez **Extensions**.

1. Dans le champ Extensions du commutateur, sélectionnez **Microsoft NDIS Capture**.

    :::image type="content" source="media/tutorial-install-components/microsoft-ndis.png" alt-text="Capture d’écran montrant la sélection du menu Extensions et l’activation de Microsoft NDIS Capture.":::

1. Sélectionnez **OK**.

#### <a name="set-the-mirroring-mode-on-the-external-port"></a>Définir le mode de mise en miroir sur le port externe

Le mode de mise en miroir doit être défini sur le port externe du nouveau commutateur virtuel à utiliser comme source.

Vous devez configurer le commutateur virtuel Hyper-V (vSwitch_Span) pour transférer tout le trafic qui arrive au port source externe vers la carte réseau virtuelle que vous avez configurée comme destination.

Utilisez les commandes PowerShell suivantes pour définir le port de commutateur virtuel externe en mode miroir source :

```bash
$ExtPortFeature=Get-VMSystemSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings"
$ExtPortFeature.SettingData.MonitorMode=2
Add-VMSwitchExtensionPortFeature -ExternalPort -SwitchName vSwitch_Span -VMSwitchExtensionFeature $ExtPortFeature
```

| Paramètre | Description |
|--|--|
| vSwitch_Span | Nom du commutateur virtuel SPAN récemment ajouté. |
| MonitorMode=2 | Source |
| MonitorMode=1 | Destination |
| MonitorMode=0 | Aucun |

Utilisez la commande PowerShell suivante pour vérifier l’état du mode de supervision :

```bash
Get-VMSwitchExtensionPortFeature -FeatureName "Ethernet Switch Port Security Settings" -SwitchName vSwitch_Span -ExternalPort | select -ExpandProperty SettingData
```

| Paramètre | Description |
|--|--|
| vSwitch_Span | Nom du commutateur virtuel SPAN récemment ajouté |

## <a name="access-sensors-from-the-on-premises-management-console"></a>Accéder aux capteurs à partir de la console de gestion locale

Vous pouvez renforcer la sécurité du système en empêchant l’accès direct des utilisateurs au capteur. Au lieu de cela, utilisez le tunneling de proxy pour permettre aux utilisateurs d’accéder au capteur à partir de la console de gestion locale avec une seule règle de pare-feu. Cette technique réduit le risque d’accès non autorisé à l’environnement réseau au-delà du capteur. L’expérience de l’utilisateur lorsqu’il se connecte au capteur reste la même.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Capture d’écran montrant l’accès au capteur.":::

**Pour activer le tunneling** :

1. Connectez-vous à l'interface de ligne de commande de la console de gestion locale en utilisant les informations d'identification de l'utilisateur **CyberX** ou **Support**.

1. Entrez `sudo cyberx-management-tunnel-enable`.

1. Sélectionnez **Enter** (Entrer).

1. Entrez `--port 10000`.

### <a name="next-steps"></a>Étapes suivantes

[Configurer votre réseau](how-to-set-up-your-network.md)
