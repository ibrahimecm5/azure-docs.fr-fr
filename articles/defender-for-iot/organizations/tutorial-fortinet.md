---
title: Intégrer Fortinet à Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez découvrir comment intégrer Microsoft Defender pour IoT à Fortinet.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0556cab649a87b7c0309671acc60d9756a3d03c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283654"
---
# <a name="tutorial-integrate-fortinet-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer Fortinet à Microsoft Defender pour IoT

Ce tutoriel va vous aider à découvrir comment intégrer Fortinet à Microsoft Defender pour IoT et comment l’utiliser.

Microsoft Defender pour IoT atténue le risque IIoT, ICS et SCADA avec des moteurs d’auto-apprentissage prenant en charge ICS, qui fournissent des insights immédiats sur les appareils, les vulnérabilités et les menaces ICS.  Defender pour IoT réalise cela sans s’appuyer sur des agents, des règles, des signatures, des compétences spécialisées ou une connaissance préalable de l’environnement.

Defender pour IoT et Fortinet ont établi un partenariat technologique qui détecte et arrête les attaques sur les réseaux IoT et ICS.

Fortinet et Microsoft Defender pour IoT empêchent les éléments suivants :

- Changements non autorisés apportés aux contrôleurs logiques programmables (PLC).

- Programmes malveillants qui manipulent les appareils ICS et IoT par le biais de leurs protocoles natifs.

- Outils de reconnaissance issus de la collecte de données.

- Violations de protocole provoquées par des configurations incorrectes ou des attaquants malveillants.

Defender pour IoT détecte un comportement anormal dans les réseaux IoT et ICS, et fournit ces informations à FortiGate et FortiSIEM, comme suit :

- **Visibilité :** Les informations fournies par Defender pour IoT permettent aux administrateurs FortiSIEM d’avoir une visibilité sur les réseaux IoT et ICS auparavant invisibles.

- **Blocage des attaques malveillantes :** Les administrateurs FortiGate peuvent utiliser les informations découvertes par Defender pour IoT afin de créer des règles destinées à faire cesser un comportement anormal, qu’il soit causé par des acteurs chaotiques ou des appareils mal configurés, avant qu’il entraîne des dommages pour la production, les bénéfices ou les personnes.

FortiSIEM, et la solution multifournisseur SIEM (Security Information and Event Management, gestion des informations de sécurité et des événements) de Fortinet, regroupent la visibilité, la corrélation, la réponse automatisée et la correction dans une solution unique et scalable.

À l’aide d’une vue Services aux entreprises, la complexité de la gestion des opérations réseau et de sécurité est réduite, ce qui permet de libérer des ressources et d’améliorer la détection des violations. FortiSIEM fournit une corrélation croisée tout en appliquant le machine learning et UEBA pour améliorer la réponse, afin d’arrêter les violations avant qu’elles ne se produisent.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> - Créer une clé API dans Fortinet
> - Définir une règle de transfert pour bloquer les alertes liées aux programmes malveillants
> - Bloquer la source des alertes suspectes
> - Envoyer les alertes Defender pour IoT à FortiSIEM
> - Bloquer une source malveillante avec le pare-feu FortiGate

Si vous n’avez pas encore de compte Azure, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

Il n’existe aucun prérequis pour ce tutoriel.

## <a name="create-an-api-key-in-fortinet"></a>Créer une clé API dans Fortinet

Une clé d’interface de programmation d’applications (API) est un code généré de manière unique qui permet à une API d’identifier l’application ou l’utilisateur qui demande l’accès à celle-ci. Une clé API est nécessaire pour que Microsoft Defender pour IoT et Fortinet communiquent correctement.

**Pour créer une clé API dans Fortinet** :

1. Dans FortiGate, accédez à **System** > **Admin Profiles** (Profils administrateur).

1. Créez un profil avec les autorisations suivantes :

    | Paramètre | Sélection |
    |--|--|
    | **Security Fabric** (Infrastructure de sécurité) | None |
    | **Fortiview** | None |
    | **User & Device** (Utilisateur et appareil) | None |
    | **Pare-feu** | Custom |
    | **Stratégie** | Lecture/écriture |
    | **Adresse** | Lecture/écriture  |
    | **Service** | None |
    | **Planification** | None |
    | **Logs & Report** (Journaux et rapport) | None |
    | **Réseau** | None |
    | **Système** | None |
    | **Security Profile** (Profil de sécurité) | None |
    | **VPN** | None |
    | **WAN Opt & Cache** (Option et cache WAN) | None |
    | **WiFi & Switch** (WiFi et commutateur) | None |

1. Accédez à **System** > **Administrators** (Administrateurs), puis créez un administrateur d’API REST (**REST API Admin**) avec les champs suivants :

    | Paramètre | Description |
    | --------- | ----------- |
    | **Nom d’utilisateur** | Entrez le nom de la règle de transfert. |
    | **Commentaires** | Entrez l’incident de niveau de sécurité minimal à transférer. Par exemple, si **Minor** est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Administrator Profile** (Profil administrateur) | Dans la liste déroulante, sélectionnez le nom de profil que vous avez défini à l’étape précédente. |
    | **PKI Group** (Groupe PKI) | Basculez le commutateur sur **Disable** (Désactiver). |
    | **CORS Allow Origin** (Autoriser l’origine CORS) | Basculez le commutateur sur **Enable** (Activer). |
    | **Restrict login to trusted hosts** (Limiter la connexion aux hôtes approuvés) | Ajoutez les adresses IP des capteurs et des consoles de gestion qui se connecteront à FortiGate. |

Une fois la clé API générée, enregistrez-la, car elle ne sera pas refournie.

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="Capture d’écran de la description de la génération automatique d’une nouvelle clé API":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>Définir une règle de transfert pour bloquer les alertes liées aux programmes malveillants

Le pare-feu FortiGate peut être utilisé pour bloquer le trafic suspect.

**Pour définir une règle de transfert afin de bloquer les alertes liées aux programmes malveillants** :

1. Connectez-vous à la console de gestion Microsoft Defender pour IoT.

1. Dans le volet de gauche, sélectionnez **Forwarding** (Transfert).

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Capture d’écran de l’option de la fenêtre Transfert dans un capteur.":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. Sélectionnez **Créer une règle de transfert**, puis définissez les paramètres de règle suivants.

    | Paramètre | Description |
    | --------- | ----------- |
    | **Nom** | Entrez un nom explicite pour la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Dans le menu déroulant, sélectionnez l’incident de niveau de sécurité minimal à transférer. Par exemple, si **Minor** est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Pour sélectionner un protocole spécifique, sélectionnez **Spécifique**, puis sélectionnez le protocole pour lequel cette règle est appliquée. Par défaut, tous les protocoles sont sélectionnés. |
    | **Engines** (Moteurs) | Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Spécifique**, puis sélectionnez le moteur. Par défaut, tous les moteurs de sécurité sont impliqués. |
    | **System Notifications** (Notifications système) | Transférez l’état *en ligne* et *hors connexion* du capteur. Cette option est disponible uniquement si vous êtes connecté à la console de gestion locale. |

1. Dans la section Actions, sélectionnez **Ajouter**, puis **Envoyer à FortiGate** dans le menu déroulant.

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="Capture d’écran de la section d’ajout d’une action de la fenêtre de création d’une règle de transfert.":::

1. Pour configurer la règle de transfert FortiGate, définissez les paramètres suivants :

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="Capture d’écran de la fenêtre permettant de configurer la règle de transfert en cours de création.":::

    | Paramètre | Description |
    |--|--|
    | **Hôte** | Entrez l’adresse IP du serveur FortiGate. |
    | **Clé API** | Entrez la [clé API](#create-an-api-key-in-fortinet) que vous avez créée dans FortiGate. |
    | **Incoming Interface (Interface entrante)** | Entrez le port de l’interface de pare-feu entrante. |
    | **Outgoing Interface (Interface sortante)** | Entrez le port de l’interface de pare-feu sortante. |
    | **Configurer**| Assurez-vous qu’un **√** est affiché dans les options suivantes pour activer le blocage des sources suspectes via le pare-feu FortiGate : <br> - **Bloquer les codes de fonction non conformes** : Violations de protocole - Valeur de champ non conforme qui ne respecte pas la spécification de protocole ICS (attaque potentielle) <br /> - **Bloquer les mises à jour non autorisées des microprogrammes/programmes PLC** : Changements de contrôleur PLC non autorisés <br /> - **Bloquer l’arrêt de PLC non autorisé** : Arrêt du PLC (temps d’arrêt) <br> - **Bloquer les alertes liées aux programmes malveillants** : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). <br> -  **(Facultatif)** Vous pouvez sélectionner l’option **Blocage automatique**. Si l’option Blocage automatique est sélectionnée, le blocage est exécuté automatiquement et immédiatement. <br /> - **Bloquer l’analyse non autorisée** : Analyse non autorisée (reconnaissance potentielle) |

1. Sélectionnez **Envoyer**.

## <a name="block-the-source-of-suspicious-alerts"></a>Bloquer la source des alertes suspectes

La source des alertes suspectes peut être bloquée afin d’éviter d’autres occurrences.

**Pour bloquer la source des alertes suspectes** :

1. Connectez-vous à la console de gestion et sélectionnez **Alertes** dans le menu de gauche.

1. Sélectionnez l’alerte liée à l’intégration de Fortinet.

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Block Source** (Bloquer la source).

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="Capture d’écran de la fenêtre Alert.":::

1. Dans la boîte de dialogue Please Confirm, sélectionnez **OK**.

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>Envoyer les alertes Defender pour IoT à FortiSIEM

Les alertes Defender pour IoT fournissent des informations sur un large éventail d’événements de sécurité, notamment :

- Écarts par rapport à l’activité réseau de ligne de base acquise

- Détections de programmes malveillants

- Détections basées sur des changements opérationnels suspects

- Anomalies réseau

- Écarts de protocole par rapport aux spécifications de protocole

Vous pouvez configurer Defender pour IoT afin d’envoyer des alertes au serveur FortiSIEM, où les informations concernant les alertes s’affichent dans la fenêtre Analytics (Analytique) :

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="Capture d’écran de la fenêtre Analytics.":::

Chaque alerte Defender pour IoT est ensuite analysée sans aucune autre configuration côté FortiSIEM et elle est présentée dans FortiSIEM sous la forme d’un événement de sécurité. Les détails d’événement suivants s’affichent par défaut :

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="Capture d’écran de la vue des détails de vos événements dans la fenêtre Event Details (Détails de l’événement).":::

Vous pouvez ensuite utiliser la fonctionnalité Règles de transfert de Defender pour IoT pour envoyer des informations d’alerte à FortiSIEM.

**Pour utiliser la fonctionnalité Règles de transfert de Defender pour IoT pour envoyer des informations d’alerte à FortiSIEM** :

1. À partir du capteur ou du volet de gauche de la console de gestion, sélectionnez **Transfert**.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Capture d’écran de la vue de vos règles de transfert dans la fenêtre Transfert.":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. Sélectionnez **Créer une règle de transfert**, puis définissez les paramètres de la règle.

    | Paramètre | Description |
    |--|--|
    | **Nom** | Entrez un nom explicite pour la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Sélectionnez l’incident de niveau de sécurité minimal à transférer. Par exemple, si **Minor** est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Pour sélectionner un protocole spécifique, sélectionnez **Spécifique**, puis sélectionnez le protocole pour lequel cette règle est appliquée. Par défaut, tous les protocoles sont sélectionnés. |
    | **Engines** (Moteurs) | Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Specific** (Spécifique), puis sélectionnez le moteur. Par défaut, tous les moteurs de sécurité sont impliqués. |
    | **System Notifications** (Notifications système) | Transférez l’état *en ligne* ou *hors connexion* d’un capteur. Cette option est disponible uniquement si vous êtes connecté à la console de gestion locale. |

3. Dans la section Actions, sélectionnez **Envoyer à FortiSIEM**.

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="Capture d’écran de la création d’une règle de transfert et de la sélection de l’option Envoyer à FortiSIEM.":::

4. Entrez les détails du serveur FortiSIEM.

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="Capture d’écran de l’ajout des informations concernant FortiSIEM à la règle de transfert.":::

    | Paramètre | Description |
    | --------- | ----------- |
    | **Hôte** | Entrez l’adresse IP du serveur FortiSIEM. |
    | **Port** | Entrez le port du serveur FortiSIEM. |
    | **Fuseau horaire** | Horodatage de la détection d’alerte. |

5. Sélectionnez **Envoyer**.

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>Bloquer une source malveillante avec le pare-feu FortiGate

Vous pouvez définir des stratégies pour bloquer automatiquement les sources malveillantes dans le pare-feu FortiGate avec des alertes dans Defender pour IoT.

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="Capture d’écran de la fenêtre du pare-feu FortiGate.":::

Par exemple, l’alerte suivante peut bloquer la source malveillante :

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="Capture d’écran de la fenêtre de suspicion de programme malveillant NotPetya.":::

**Pour définir une règle de pare-feu FortiGate qui bloque une source malveillante** :

1. Dans FortiGate, [créez une clé API](#create-an-api-key-in-fortinet).

1. Connectez-vous au capteur Defender pour IoT ou à la console de gestion, puis sélectionnez **Transfert** et [définissez une règle de transfert qui bloque les alertes liées aux logiciels malveillants](#set-a-forwarding-rule-to-block-malware-related-alerts).

1. Dans le capteur Defender pour IoT ou la console de gestion, sélectionnez **Alertes** et [bloquez une source malveillante](#block-a-malicious-source-using-the-fortigate-firewall).

1. Accédez à la fenêtre **Administrator** de FortiGage, puis localisez l’adresse de la source malveillante que vous avez bloquée.

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="Capture d’écran de la vue de la fenêtre Administrator de FortiGate.":::

   La stratégie de blocage est automatiquement créée et s’affiche dans la fenêtre IPv4 Policy (Stratégie IPv4) de FortiGate.

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="Capture d’écran de la vue de la fenêtre IPv4 Policy (Stratégie IPv4) de FortiGate.":::

1. Sélectionnez la stratégie et assurez-vous que l’option Enable this policy (Activer cette stratégie) est activée.

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="Capture d’écran de la vue de la fenêtre permettant de modifier une stratégie IPv4 de FortiGate.":::

    | Paramètre | Description|
    |--|--|
    | **Nom** | Nom de la stratégie. |
    | **Incoming Interface (Interface entrante)** | Interface de pare-feu entrante pour le trafic. |
    | **Outgoing Interface (Interface sortante)** | Interface de pare-feu sortante pour le trafic. |
    | **Source** | Adresse(s) source(s) du trafic. |
    | **Destination** | Adresse(s) de destination pour le trafic. |
    | **Planification** | Occurrence de la règle nouvellement définie. Par exemple : `always`. |
    | **Service** | Protocole, ou ports spécifiques pour le trafic. |
    | **Action** | Action que le pare-feu doit effectuer. |

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de Fortinet. Poursuivez pour en savoir plus sur notre intégration de Palo Alto.

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](./tutorial-palo-alto.md)
