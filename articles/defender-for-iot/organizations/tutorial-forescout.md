---
title: Intégrer Forescout à Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez découvrir comment intégrer Microsoft Defender pour IoT à Forescout.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: c395c799b8f6dca602b20cb330f89630f9fb591e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325233"
---
# <a name="tutorial-integrate-forescout-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer Forescout à Microsoft Defender pour IoT

> [!Note]
> Les références à CyberX concernent Microsoft Defender pour IoT.

Ce tutoriel va vous aider à découvrir comment intégrer Forescout à Microsoft Defender pour IoT.

Microsoft Defender pour IoT offre une plateforme de cybersécurité pour le partage de connexion Internet (ICS) et l’Internet des objets (IoT). Defender pour IoT est la seule plateforme offrant une analytique des menaces prenant en charge ICS et le Machine Learning. Defender pour IoT offre les fonctionnalités suivantes :

- Insights immédiats sur ICS dans le paysage de l’appareil avec un large éventail de détails sur les attributs.

- Connaissance intégrée prenant en charge ICS sur les protocoles, les appareils, les applications OT et leur comportement.

- Insights immédiats sur les vulnérabilités et menaces zero-day connues.

- Technologie de modélisation des menaces ICS automatisée pour prédire les chemins les plus probables des attaques ICS ciblées via une analytique propriétaire.

L’intégration de Forescout vous aide à réduire le temps nécessaire aux organisations dotées d’une infrastructure industrielle et critique pour détecter, examiner et traiter les cybermenaces.

- Utilisez le renseignement sur les appareils OT de Microsoft Defender pour IoT afin de clore le cycle de sécurité en déclenchant des actions de stratégie Forescout. Par exemple, vous pouvez envoyer automatiquement un e-mail d’alerte aux administrateurs SOC lorsque des protocoles spécifiques sont détectés ou lorsque des détails du microprogramme changent.

- Mettez en corrélation les informations relatives à Defender pour IoT avec d’autres modules *Forescout eyeExtend* qui supervisent la surveillance, la gestion des incidents et le contrôle des appareils.

L’intégration de Defender pour IoT à la plateforme Forescout offre une visibilité, une supervision et un contrôle centralisés pour le paysage IoT et OT. Ces plateformes reliées par un pont activent la visibilité et la gestion automatisées des appareils pour les appareils ICS et les workflows en silo. L’intégration offre aux analystes SOC une visibilité à plusieurs niveaux des protocoles OT déployés dans des environnements industriels. Des informations sont ainsi disponibles, telles que les microprogrammes, les types d’appareils, les systèmes d’exploitation et les scores d’analyse des risques basés sur des technologies Microsoft Defender pour IoT propriétaires.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> - Générer un jeton d’accès
> - Configurer la plateforme Forescout
> - Vérifier la communication
> - Afficher les attributs d’appareil dans Forescout
> - Créer des stratégies Microsoft Defender pour IoT dans Forescout

Si vous n’avez pas encore de compte Azure, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Microsoft Defender pour IoT versions 2.4 ou ultérieures

- Forescout versions 8.0 ou ultérieures

- Une licence pour le module Forescout eyeExtend pour la plateforme Microsoft Defender pour IoT.

## <a name="generate-an-access-token"></a>Générer un jeton d’accès

Les jetons d’accès permettent à des systèmes externes d’accéder aux données découvertes par Defender pour IoT. Les jetons d’accès permettent d’utiliser ces données pour les API REST externes et via des connexions SSL. Vous pouvez générer des jetons d’accès afin d’accéder à l’API REST Microsoft Defender pour IoT.

Pour garantir la communication entre Defender pour IoT et Forescout, vous devez générer un jeton d’accès dans Defender pour IoT.

**Pour générer un jeton d’accès** :

1. Connectez-vous au capteur Defender pour IoT que Forescout interrogera.

1. Dans la section **Général**, sélectionnez **Paramètres système** > **Jetons d’accès**.

1. Sélectionnez **Générer un nouveau jeton**.

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="Capture d’écran de l’écran de génération d’un jeton d’accès.":::

1. Dans la boîte de dialogue **Nouveau jeton d’accès**, entrez une description pour le jeton.

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="Nouveau jeton d’accès":::

1. Sélectionnez **Suivant**. Le jeton s’affiche alors dans la boîte de dialogue.

   > [!NOTE]
   > Enregistrez le jeton en lieu sûr. Vous en aurez besoin lors de la configuration de la plateforme Forescout.

1. Sélectionnez **Terminer**.

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Achevez d’ajouter le jeton":::

## <a name="configure-the-forescout-platform"></a>Configurer la plateforme Forescout

Vous pouvez à présent configurer la plateforme Forescout pour qu’elle communique avec un capteur Defender pour IoT.

**Pour configurer la plateforme Forescout** :

1. Sur la plateforme Forescout, recherchez et installez **le module Forescout eyeExtend pour CyberX**.

1. Connectez-vous à la console CounterACT.

1. Dans le menu Outils , sélectionnez **Options**.

1. Accédez à **Modules** > **CyberX Platform** (Plateforme CyberX).

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Paramètres du module Microsoft Defender pour IoT":::

1. Dans le champ Server Address (Adresse du serveur), entrez l’adresse IP du capteur Defender pour IoT que l’appliance Forescout interrogera.

1. Dans le champ Access Token (Jeton d’accès), entrez le jeton d’accès qui a été généré.

1. Sélectionnez **Appliquer**.

### <a name="change-sensors-in-forescout"></a>Changer de capteur dans Forescout

Pour que la plateforme Forescout communique avec un autre capteur, la configuration dans Forescout doit être changée.

**Pour changer de capteur dans Forescout** :

1. Créez un jeton d’accès dans le capteur Defender pour IoT approprié.

1. Accédez à **Forescout Modules** > **CyberX Platform** (Modules Forescout > Plateforme CyberX).

1. Supprimez les informations affichées dans les deux champs.

1. Connectez-vous au nouveau capteur Défendeur pour IoT et [générez un nouveau jeton d’accès](#generate-an-access-token).

1. Dans le champ Server Address (Adresse du serveur), entrez la nouvelle adresse IP du capteur Defender pour IoT que l’appliance Forescout interrogera.

1. Dans le champ Access Token (Jeton d’accès), entrez le nouveau jeton d’accès.

1. Sélectionnez **Appliquer**.

## <a name="verify-communication"></a>Vérifier la communication

Une fois la connexion configurée, vous devez vérifier que les deux plateformes communiquent.

**Pour vérifier que les deux plateformes communiquent** :

1. Connectez-vous au capteur Defender pour IoT.

1. Accédez à **Paramètres système** > **Jetons d’accès**.

Le champ Utilisé vous avertit si la connexion entre le capteur et l’appliance Forescout ne fonctionne pas. Si **N/A** s’affiche, la connexion ne fonctionne pas. Si **Utilisé** s’affiche, l’heure à laquelle le dernier appel externe avec ce jeton a été reçu est indiquée.

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Vérifie que le jeton a été reçu":::

## <a name="view-device-attributes-in-forescout"></a>Afficher les attributs d’appareil dans Forescout

En intégrant Defender pour IoT à Forescout, vous êtes en mesure d’afficher les différents attributs d’appareil détectés par Defender pour IoT, dans l’application Forescout.

Le tableau suivant liste tous les attributs qui sont visibles par le biais de l’application Forescout :

| Élément | Description |
|--|--|
| **Autorisé par Microsoft Defender pour IoT** | Appareil que Defender pour IoT a détecté sur votre réseau pendant la période d’apprentissage du réseau. |
| **Microprogramme** | Détails du microprogramme de l’appareil. Par exemple, détails du modèle et de la version. |
| **Nom** | Nom de l’appareil |
| **Système d’exploitation** | Système d’exploitation de l’appareil. |
| **Type** | Type d’appareil. Par exemple, PLC, Historien ou Station d’ingénierie. |
| **Fournisseur** | Fournisseur de l’appareil. Par exemple, Rockwell Automation. |
| **Niveau de risque** | Niveau de risque calculé par Defender pour IoT. |
| **Protocoles** | Les protocoles détectés dans le trafic généré par l’appareil. |

**Pour afficher les attributs d’un appareil** :

1. Connectez-vous à la plateforme Forescout, puis accédez à l’**inventaire des ressources**.

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="Affichez les attributs du microprogramme.":::

1. Sélectionnez la **plateforme CyberX**.

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="Affichez les attributs des fournisseurs.":::

### <a name="view-more-details"></a>Afficher plus de détails

Après avoir consulté les attributs d’un appareil, vous pouvez voir plus de détails pour chaque appareil, tels que les informations de conformité et de stratégie Forescout.

**Pour voir d’autres détails** :

1. Connectez-vous à la plateforme Forescout, puis accédez à l’**inventaire des ressources**.

1. Sélectionnez la **plateforme CyberX**.

1. Dans la section Device Inventory Hosts (Hôtes d’inventaire des appareils), cliquez avec le bouton droit sur un appareil. La boîte de dialogue Détails de l’hôte s’ouvre, affichant des informations supplémentaires.

## <a name="create-microsoft-defender-for-iot-policies-in-forescout"></a>Créer des stratégies Microsoft Defender pour IoT dans Forescout

Vous pouvez utiliser des stratégies Forescout pour automatiser le contrôle et la gestion des appareils détectés par Defender pour IoT. Par exemple,

- Envoyez automatiquement un e-mail aux administrateurs SOC quand des versions de microprogramme spécifiques sont détectées.

- Ajoutez des appareils Defender pour IoT spécifiques détectés à un groupe Forescout pour une gestion plus poussée des flux de travail d’incident et de sécurité, par exemple, avec d’autres intégrations SIEM.

Vous pouvez créer des stratégies personnalisées dans Forescout avec les propriétés conditionnelles de Defender pour IoT.

**Pour accéder aux propriétés de Defender pour IoT** :

1. Accédez à **Policy Conditions** > **Properties Tree** (Conditions de stratégie > Arborescence des propriétés).

1. Dans l’arborescence des propriétés, développez le dossier CyberX Platform (Plateforme CyberX). Les propriétés suivantes de Defender pour IoT sont disponibles.

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="Propriétés":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de Forescout. Poursuivez pour en savoir plus sur notre [intégration de Palo Alto](./tutorial-palo-alto.md).

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](./tutorial-palo-alto.md)
