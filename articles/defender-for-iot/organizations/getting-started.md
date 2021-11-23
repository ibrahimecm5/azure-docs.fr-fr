---
title: 'Démarrage rapide : Bien démarrer'
description: Suivez ce guide de démarrage rapide pour vous familiariser avec le workflow de base du déploiement de Defender pour IoT.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 85bd254236a6d08d8f232e14e5a41dab99f0af04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283863"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Démarrage rapide : Bien démarrer avec Defender pour IoT

Cet article fournit une vue d’ensemble des étapes à suivre pour configurer Microsoft Defender pour IoT. En voici les prérequis :

- Inscrivez votre abonnement et les capteurs sur Defender pour IoT dans le portail Azure.
- Installez le capteur et le logiciel de la console de gestion locale.
- Effectuez l’activation initiale du capteur et de la console de gestion.

## <a name="permission-requirements"></a>Spécifications relatives aux autorisations

### <a name="for-sensors-and-on-premises-management-consoles"></a>Pour les capteurs et consoles de gestion locale

Certaines des étapes de configuration exigent des autorisations d’utilisateur spécifiques.

Des autorisations d’utilisateur administratif sont requises pour activer le capteur et la console de gestion, charger des certificats SSL/TLS et générer de nouveaux mots de passe.

### <a name="for-defender-for-iot-in-the-azure-portal"></a>Pour Defender pour IoT dans le portail Azure

Le tableau suivant décrit les autorisations d’accès utilisateur aux outils du portail Azure :

| Autorisation | Lecteur de sécurité | Administrateur de sécurité | Collaborateur de l’abonnement | Propriétaire de l’abonnement |
|--|--|--|--|--|
| Afficher les détails et accéder aux logiciels, aux fichiers d’activation et aux packages Threat Intelligence  | ✓ | ✓ | ✓ | ✓ |
| Intégrer des capteurs  |  |  ✓ | ✓ | ✓ |
| Intégrer des abonnements et mettre à jour des appareils validés  |  |  | ✓ | ✓ |
| Récupérer les mots de passe  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identification de l’infrastructure de la solution

**Clarification des besoins de configuration réseau**

Recherchez les éléments suivants :

- Architecture réseau
- Bande passante supervisée
- Conditions requises pour la création de certificats
- Autres informations sur le réseau

Pour plus d’informations, consultez [À propos de la configuration réseau de Microsoft Defender pour IoT](how-to-set-up-your-network.md).

**Clarification des capteurs et des appliances de la console de gestion nécessaires pour gérer la charge réseau**

Microsoft Defender pour IoT prend en charge les déploiements physiques et virtuels. Dans le cas des déploiements physiques, vous pouvez acheter plusieurs appliances certifiées. Pour plus d’informations, consultez [Identification des appliances requises](how-to-identify-required-appliances.md).

Nous vous recommandons de calculer le nombre approximatif d’appareils qui seront analysés. Plus tard, lorsque vous enregistrerez votre abonnement Azure sur le portail, il vous sera demandé d’entrer ce nombre. Les nombres peuvent être ajoutés par intervalles de 1 000, par exemple 1 000, 2 000, 3 000. Le nombre d’appareils analysés est appelé *appareils validés*.

## <a name="register-with-microsoft-defender-for-iot"></a>S’inscrire auprès de Microsoft Defender pour IoT

L’inscription comprend plusieurs opérations :

- Intégration des abonnements Azure à Defender pour IoT
- Définition des appareils validés
- Téléchargement d’un fichier d’activation pour la console de gestion locale

Vous pouvez également utiliser un abonnement d’évaluation pour superviser 1000 appareils gratuitement pendant 30 jours. Pour plus d’informations, consultez [Intégrer un abonnement d’évaluation](how-to-manage-subscriptions.md#onboard-a-trial-subscription).

**Pour vous inscrire** :

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.

1. Sélectionnez **Intégrer un abonnement**.

1. Sur la page **Tarifs**, sélectionnez un abonnement ou créez-en un, puis ajoutez le nombre d’appareils validés.

1. Sélectionnez l’onglet **Téléchargez la console de gestion locale** et enregistrez le fichier d’activation téléchargé. Ce fichier contient les appareils validés agrégés que vous avez définis. Il sera chargé dans la console de gestion après la connexion initiale.

Pour plus d’informations sur la manière de désactiver un abonnement, consultez [Désactiver un abonnement](how-to-manage-subscriptions.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Installation et configuration de la console de gestion locale

Après avoir fait l’acquisition de votre appliance de console de gestion locale, procédez comme suit :

- Téléchargez le package ISO à partir du portail Azure.
- Installez le logiciel.
- Activez la console de gestion et effectuez la configuration initiale.

**Pour installer configurer** :

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.

1. Sélectionnez l’onglet **Console de gestion locale**.

1. Choisissez une version et sélectionnez **Télécharger**.

1. Installez le logiciel de la console de gestion locale. Pour plus d’informations, consultez [Installation de Defender pour IoT](how-to-install-software.md).

1. Activez et configurez la console de gestion. Pour plus d’informations, consultez [Activation et configuration de la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Intégrer un capteur ##

Intégrez un capteur en l’inscrivant auprès de Microsoft Defender pour IoT et en téléchargeant un fichier d’activation de capteur :

1. Définissez un nom de capteur et associez-le à un abonnement.

1. Choisissez un mode de connexion du capteur :

   - **Capteurs connectés au cloud** : les informations détectées par les capteurs s’affichent dans la console de ces derniers. Par ailleurs, les données des alertes, transmises par le biais d’un hub IoT, sont partageables avec d’autres services Azure, comme Microsoft Sentinel. Vous pouvez également choisir d’envoyer automatiquement des packages Threat Intelligence à partir de Defender pour IoT vers vos capteurs. Pour plus d’informations, consultez [Recherche et packages Threat Intelligence](how-to-work-with-threat-intelligence-packages.md).

   - **Capteurs gérés localement** : Les informations détectées par les capteurs s’affichent dans leur console. Si vous travaillez sur un réseau en air gap et que vous souhaitez une vue unifiée de toutes les informations détectées par plusieurs capteurs gérés localement, utilisez la console de gestion locale.

1. Sélectionnez le site auquel vous souhaitez associer votre capteur dans un hub IoT. Le hub IoT servira de passerelle entre ce capteur et Microsoft Defender pour IoT. Définissez le nom complet et la zone. Vous pouvez également ajouter des étiquettes descriptives. Le nom complet, la zone et les étiquettes constituent des entrées descriptives de la page [Sites et capteurs](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors).

1. Sélectionnez **Inscription**.

1. Sélectionnez **Télécharger le fichier d’activation**.

Pour plus d’informations sur l’intégration, consultez [Intégrer et gérer des capteurs avec Defender pour IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Installation et configuration du capteur

Téléchargez le package ISO sur le portail Azure, installez le logiciel et configurez le capteur.

1. Accédez à [Defender pour IoT : Prise en main](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) dans le portail Azure.

1. Sélectionnez **Configurer le capteur**.

1. Choisissez une version et sélectionnez **Télécharger**.

1. Installez le logiciel du capteur. Pour plus d’informations, consultez [Installation de Defender pour IoT](how-to-install-software.md).

1. Activez et configurez votre capteur. Pour plus d’informations, consultez [Connexion à un capteur et activation](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Connexion des capteurs à une console de gestion locale

Connectez les capteurs à la console de gestion. L’objectif est double :

- Les capteurs envoient les informations d’inventaire d’alerte et d’inventaire des appareils à la console de gestion locale.

- La console de gestion locale peut effectuer des sauvegardes de capteurs, gérer des alertes détectées par ces derniers, examiner leurs déconnexions et effectuer d’autres activités sur les capteurs connectés.

Nous vous recommandons de regrouper dans une même zone les capteurs qui surveillent les mêmes réseaux. Les informations collectées par ces capteurs seront ainsi fusionnées.

Pour plus d’informations, consultez [Connexion des capteurs à la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-microsoft-sentinel-with-alert-information-optional"></a>Remplir les informations d’alerte dans Microsoft Sentinel (facultatif)

Envoyez des informations d’alerte à Microsoft Sentinel en configurant ce dernier. Consultez [Connexion des données de Defender pour IoT à Microsoft Sentinel](how-to-configure-with-sentinel.md).  

## <a name="next-steps"></a>Étapes suivantes ##

[Bienvenue dans Microsoft Defender pour IoT](overview.md)

[Architecture de Microsoft Defender pour IoT](architecture.md)
