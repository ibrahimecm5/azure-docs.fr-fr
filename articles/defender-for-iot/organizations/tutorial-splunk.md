---
title: Intégrer Splunk à Microsoft Defender pour IoT
description: Dans ce tutoriel, découvrez comment intégrer Splunk à Microsoft Defender pour IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: e4b0f321741d6f07f2ac636d57de31bf02503877
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283578"
---
# <a name="tutorial-integrate-splunk-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer Splunk à Microsoft Defender pour IoT

Ce tutoriel va vous permettre de découvrir comment intégrer Splunk à Microsoft Defender pour IoT et comment l’utiliser.

Defender pour IoT atténue le risque IIoT, ICS et SCADA avec des moteurs d’apprentissage automatique brevetés et prenant en charge ICS, qui fournissent des insights immédiats sur les appareils, les vulnérabilités et les menaces ICS en moins d’une heure et sans s’appuyer sur des agents, des règles ou des signatures, des compétences spécialisées ou une connaissance préalable de l’environnement.

Pour répondre à un manque de visibilité sur la sécurité et la résilience des réseaux OT, Defender pour IoT a développé Defender pour IoT, IIoT, et l’application de surveillance des menaces ICS pour Splunk, une intégration native entre Defender pour IoT et Splunk qui permet une approche unifiée de sécurité informatique et OT.

L’application fournit aux analystes SOC une visibilité multidimensionnelle sur les protocoles OT et les appareils IoT spécialisés déployés dans des environnements industriels, ainsi qu’une analytique des comportements prenant en charge ICS pour détecter rapidement les comportements suspects ou anormaux. L’application active également la réponse aux incidents IT et OT au sein d’un service SOC d’entreprise. Il s’agit d’une évolution importante en raison de la convergence continue des secteurs IT et OT à prendre en charge de nouvelles initiatives IIoT, telles que les machines intelligentes et l’intelligence en temps réel.

L’application Splunk peut être installée localement ou exécutée sur un cloud. L’intégration à Splunk avec celle à Defender pour IoT prend en charge les deux déploiements.

> [!Note]
> Les références à CyberX concernent Microsoft Defender pour IoT.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Télécharger l’application Defender pour IoT dans Splunk
> * Envoyer les alertes Defender pour IoT à Splunk

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

### <a name="version-requirements"></a>Configuration requise pour la version

Les versions suivantes sont requises pour que l’application s’exécute.

- Defender pour IoT versions 2.4 et ultérieures.

- Splunkbase versions 11 et ultérieures.

- Splunk Enterprise versions 7.2 et ultérieures.

### <a name="splunk-permission-requirements"></a>Spécifications relatives aux autorisations Splunk

L’autorisation Splunk suivante est requise :

- Tout utilisateur avec un rôle utilisateur au niveau *Administrateur*.

## <a name="download-the-defender-for-iot-application-in-splunk"></a>Télécharger l’application Defender pour IoT dans Splunk

Pour accéder à l’application Defender for IoT dans Splunk, vous devez télécharge l’application auprès du magasin d’applications Splunkbase.

**Pour accéder à l’application Defender pour IoT dans Splunk** :

1. Accédez au Magasin d’applications [Splunkbase](https://splunkbase.splunk.com/).

1. Recherchez `CyberX ICS Threat Monitoring for Splunk`.

1. Sélectionnez l’application CyberX ICS Threat Monitoring pour Splunk.

1. Sélectionnez le **BOUTON CONNEXION POUR TÉLÉCHARGEMENT**.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Envoyer les alertes Defender pour IoT à Splunk

Les alertes Defender pour IoT fournissent des informations sur un large éventail d’événements de sécurité. Ces événements sont les suivants :

- Écarts par rapport à l’activité réseau de ligne de base acquise.

- Détections de programmes malveillants.

- Détections basées sur des changements opérationnels suspects.

- Anomalies réseau.

- Écarts de protocole par rapport aux spécifications de protocole.

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="L’écran des détections.":::

Vous pouvez également configurer Defender pour IoT pour envoyer des alertes au serveur Splunk, où les informations relatives aux alertes sont affichées sur le tableau de bord Splunk Enterprise.

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="Affichez toutes les alertes et leurs détails." lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

Pour envoyer des informations d’alerte aux serveurs Splunk à partir de Defender pour IoT, vous devez créer une règle de transfert.

**Pour créer une règle de transfert** :

1. Connectez-vous au capteur et sélectionnez **Transfert** à partir du volet de gauche.

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="Sélectionnez le bouton bleu Create Forwarding Alert"::: (Créer une alerte de transfert).

1. Sélectionnez **Create Forwarding Rules** (Créer des règles de transfert).

1. Dans la fenêtre **Create Forwarding Rule** (Créer une règle de transfert), définissez les paramètres de la règle.

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="Créez les règles pour votre règle de transfert." lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | Paramètre | Description |
    |--|--|
    | **Nom** | Nom de la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Incident de niveau de sécurité minimal à transférer. Par exemple, si Minor (Mineur) est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Par défaut, tous les protocoles sont sélectionnés. Pour sélectionner un protocole spécifique, sélectionnez **Specific** (Spécifique), puis sélectionnez le protocole pour lequel cette règle est appliquée. |
    | **Engines** (Moteurs) | Par défaut, tous les moteurs de sécurité sont impliqués. Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Specific** (Spécifique), puis sélectionnez le moteur. |
    | **System Notifications** (Notifications système) | État en ligne/hors connexion du capteur de transfert. Cette option est disponible uniquement si vous êtes connecté à Central Manager. |

1. Sélectionnez **Action**, puis **Envoyer au serveur Splunk**.

1. Entrez les paramètres Splunk suivants.

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="Les paramètres Splunk que vous devez entrer sur cet écran." lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | Paramètre | Description |
    |--|--|
    | **Hôte** | Adresse du serveur Splunk |
    | **Port** | 8089 |
    | **Nom d’utilisateur** | Nom d’utilisateur du serveur Splunk |
    | **Mot de passe** | Mot de passe du serveur Splunk |

1. Sélectionnez **Envoyer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration Splunk. Découvrez à présent comment [Intégrer ServiceNow à Microsoft Defender pour IoT](tutorial-servicenow.md).

> [!div class="nextstepaction"]
> [Intégrer ServiceNow à Microsoft Defender pour IoT](tutorial-servicenow.md)
