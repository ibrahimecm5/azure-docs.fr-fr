---
title: Intégrer Qradar à Microsoft Defender pour IoT
description: Dans ce tutoriel, découvrez comment intégrer Qradar à Microsoft Defender pour IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 3c6a1cfab17b2355f5f5501cd7fc2e944c74556f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278357"
---
# <a name="tutorial-integrate-qradar-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer Qradar à Microsoft Defender pour IoT

Ce tutoriel va vous permettre de découvrir comment intégrer QRadar à Microsoft Defender pour IoT et comment l’utiliser.

Defender pour IoT offre la seule plateforme de cybersécurité ICS et IoT, avec une analytique des menaces brevetée prenant en charge ICS et le Machine Learning.

Defender pour IoT a intégré sa plateforme de surveillance des menaces ICS continue avec IBM QRadar.

Voici quelques-uns des avantages de cette intégration :

- La possibilité de transférer Microsoft Defender pour les alertes IoT à IBM QRadar pour une informatique unifiée ainsi que la supervision de la sécurité et la gouvernance.

- La possibilité d’obtenir une vue d’ensemble des environnements informatiques et des environnements OT, ce qui vous permet de détecter les attaques multiétapes qui franchissent souvent les frontières et les limites et d’y répondre.

- Intégration aux flux de travail SOC existants.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer l’écouteur Syslog pour QRadar
> * Déployer le QID de la plateforme Defender pour IoT
> * Configurer les règles de transfert QRadar
> * Mapper les notifications à QRadar dans la console de gestion
> * Ajouter des champs personnalisés aux alertes

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Il n’existe aucun prérequis pour ce tutoriel.

## <a name="configure-syslog-listener-for-qradar"></a>Configurer l’écouteur Syslog pour QRadar

**Pour configurer l’écouteur Syslog afin qu’il fonctionne avec QRadar** :

1. Connectez-vous à QRadar.

1. Dans le volet gauche, sélectionnez **Administration** > **Sources de données**.

1. Dans la fenêtre Sources de données, sélectionnez **Sources de journaux**.

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="Capture d’écran de la sélection de Sources de journaux parmi les options disponibles.":::](media/tutorial-qradar/log.png#lightbox)

1. Dans la fenêtre **Modal**, sélectionnez **Ajouter**.

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="Capture d’écran de l’ouverture de la fenêtre modale après la sélection de Syslog.":::](media/tutorial-qradar/modal.png#lightbox)

1. Dans la boîte de dialogue **Ajouter une source de journal**, définissez les paramètres suivants :

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="Capture d’écran de l’ajout d’une source de journal en renseignant les champs appropriés.":::

   - **Nom de la source de journal** : `<Sensor name>`

   - **Description de la source de journal** : `<Sensor name>`

   - **Type de source de journal** : `Universal LEEF`

   - **Configuration du protocole** : `Syslog`

   - **Identificateur de la source de journal** : `<Sensor name>`

   > [!NOTE]
   > Le nom de l’identificateur de la source de journal ne doit pas contenir d’espace blanc. Il est recommandé de remplacer chaque caractère d’espace blanc par un trait de soulignement.

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Déployer les modifications**.

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="Capture d’écran de l’affichage Déployer les modifications":::

## <a name="deploy-defender-for-iot-platform-qid"></a>Déployer le QID de la plateforme Defender pour IoT

QID est un identificateur d’événement dans QRadar. Tous les rapports de la plateforme Defender pour IoT sont marqués sous le même événement (alerte de capteur).

**Pour déployer le QID de la plateforme Defender pour IoT** :

1. Connectez-vous à la console QRadar.

1. Créez un fichier appelé `xsense_qids`.

1. Dans le fichier, utilisez la commande suivante : `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Execute: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Le message indiquant que le QID a été déployé avec succès s’affiche.

## <a name="setup-qradar-forwarding-rules"></a>Configurer les règles de transfert QRadar

Pour que l’intégration fonctionne, vous devez configurer une règle de transfert Qradar dans l’appliance Defender pour IoT.

**Pour définir des notifications QRadar dans l’appliance Defender for IoT** :

1. Sélectionnez **Transfert** dans le menu latéral.

1. Sélectionnez **Créer une règle de transfert**.

1. Définissez l’Action sur **QRadar**.

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="Capture d’écran de la fenêtre de création d’une règle de transfert":::

1. Configurez l’adresse IP QRadar et le fuseau horaire.

1. Sélectionnez **Envoyer**.

## <a name="map-notifications-to-qradar"></a>Mapper les notifications à QRadar

La règle doit ensuite être mappée sur la console de gestion locale.

**Pour mapper les notifications à QRadar** :

1. Connectez-vous à la console de gestion.

1. Dans le volet de gauche, sélectionnez **Transfert**.

1. Dans l’interface graphique utilisateur de Qradar, sous QRadar, sélectionnez **Activité de journal**.

1. Sélectionnez **Ajouter un filtre** et définissez les paramètres suivants :
   - Paramètre : `Log Sources [Indexed]`
   - Opérateur : `Equals`
   - Groupe de sources du journal : `Other`
   - Source du journal : `<Xsense Name>`

1. Double-cliquez sur un rapport inconnu à partir du capteur.

1. Sélectionnez **Événement de mappage**.

1. Dans la page événement de source de journal modal, effectuez les sélections suivantes :
   - Catégorie de niveau élevé - Activité suspecte + Catégorie de bas niveau - Événement suspect inconnu + Journal
   - Type de source - quelconque

1. Sélectionnez **Recherche**.

1. Dans les résultats, choisissez la ligne dans laquelle le nom XSense s’affiche, puis sélectionnez **OK**.

Tous les rapports de capteur sont désormais marqués en tant qu’alertes de capteur.

## <a name="add-custom-fields-to-the-alerts"></a>Ajouter des champs personnalisés aux alertes

**Pour ajouter des champs personnalisés aux alertes** :

1. Sélectionnez **Extraire la propriété**.

1. Sélectionnez **Basé sur Regex**.

1. Configurez les champs suivants :
   - Nouvelle propriété : _faites un choix dans liste ci-dessous_
      - Description de l’alerte de capteur
      - ID d’alerte de capteur
      - Score de l’alerte de capteur
      - Titre de l’alerte de capteur
      - Nom de la destination du capteur
      - Redirection directe du capteur
      - Adresse IP de l’expéditeur du capteur
      - Nom de l’expéditeur du capteur
      - Moteur d’alerte de capteur
      - Nom de l’appareil source de capteur
   - Cochez **Optimiser l’analyse**
   - Type de champ : `AlphaNumeric`
   - Cochez **Activé**
   - Type de source de journal : `Universal LEAF`
   - Source du journal : `<Sensor Name>`
   - Nom de l’événement (doit être déjà défini sur Alerte de capteur)
   - Groupe de capture : 1
   - Regex :
      - RegEx de la description de l’alerte de capteur : `msg=(.*)(?=\t)`
      - RegEx de l’ID de l’alerte de capteur : `alertId=(.*)(?=\t)`
      - RegEx du score de l’alerte de capteur : `Detected score=(.*)(?=\t)`
      - RegEx du titre de l’alerte de capteur : `title=(.*)(?=\t)`
      - RegEx du nom de la destination du capteur : `dstName=(.*)(?=\t)`
      - RegEx de la redirection directe du capteur : `rta=(.*)(?=\t)`
      - RegEx de l’adresse IP de l’expéditeur du capteur : `reporter=(.*)(?=\t)`
      - RegEx du nom de l’expéditeur du capteur : `senderName=(.*)(?=\t)`
      - RegEx du moteur d’alerte du capteur : `engine =(.*)(?=\t)`
      - RegEx du nom de l’appareil source du capteur : `src`

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de QRadar. Découvrez à présent comment [Intégrer ServiceNow à Microsoft Defender pour IoT](tutorial-servicenow.md).

> [!div class="nextstepaction"]
> [Intégrer ServiceNow à Microsoft Defender pour IoT](tutorial-servicenow.md)
