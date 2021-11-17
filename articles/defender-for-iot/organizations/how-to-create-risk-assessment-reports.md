---
title: Créer des rapports d’évaluation des risques
description: Obtenez des informations sur les risques réseau détectés par des capteurs individuels ou un affichage agrégé des risques détectés par tous les capteurs.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: fbd7dc1883c52f9a5b949ac8088a998a23519d14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278756"
---
# <a name="risk-assessment-reporting"></a>Rapport d’évaluation des risques

## <a name="about-risk-assessment-reports"></a>À propos des rapports d’évaluation des risques

Les rapports d’évaluation des risques fournissent les éléments suivants :

- Score de sécurité global pour les appareils détectés par les capteurs organisationnels.

- Score de sécurité pour chaque périphérique réseau détecté par un capteur individuel.

- Une répartition du nombre d’appareils vulnérables, des appareils nécessitant une amélioration et des appareils sécurisés.

-  Compréhension des problèmes de sécurité et opérationnels :

    - Problèmes de configuration

    - Vulnérabilité des appareils hiérarchisée par niveau de sécurité

    - Problèmes de sécurité réseau

    - Problèmes opérationnels du réseau

    - Connexions aux réseaux ICS

    - connexions Internet

    - Indicateurs de programme malveillant industriel

    - Problèmes de protocole

    - Vecteurs d’attaque

### <a name="risk-mitigation"></a>Atténuation des risques

Les rapports fournissent des recommandations pour vous aider à améliorer votre score de sécurité. Par exemple :
- installer les dernières mises à jour de sécurité.
- Mettez à niveau le microprogramme vers la version la plus récente.
- Examinez les PLC dans des états non sécurisés.

## <a name="about-security-scores"></a>À propos des scores de sécurité

Le score global de sécurité réseau est généré dans chaque rapport. Le score représente le pourcentage de sécurité (sur une échelle de 100 %). Par exemple, un score de 30 % indique que votre réseau est sécurisé à 30 %.

Les scores Évaluation des risques sont basés sur les informations apprises de l’inspection des paquets, des moteurs de modélisation comportementale et de la conception d’une machine à états spécifique au système SCADA.

Les **appareils sécurisés** sont des appareils dont le score de sécurité est supérieur à 90 %.

**Appareils nécessitant une amélioration** : Appareils dont le score de sécurité est compris entre 70 % et 89 %.

Les **appareils vulnérables** sont des appareils dont le score de sécurité est inférieur à 70 %.

### <a name="about-backup-and-anti-virus-servers"></a>À propos des serveurs de sauvegarde et de protection antivirus

Si vous ne définissez pas d’adresses de serveur de sauvegarde et de protection antivirus dans votre capteur, cela risque d’impacter négativement le score d’évaluation des risques. À l’inverse, si vous ajoutez ces adresses, votre score s’améliorera. Par défaut, ces adresses ne sont pas définies.
La page de garde du rapport d’évaluation des risques indique si les serveurs de sauvegarde et de protection antivirus ne sont pas définis.

**Pour ajouter des serveurs :**

1. Sélectionnez **Paramètres système**, puis **Propriétés système**.
1. Sélectionnez **Évaluation des vulnérabilités** et ajoutez les adresses aux champs **backup_servers** et **AV_addresses**. Utilisez des virgules pour séparer plusieurs adresses.  séparées par des virgules.  
1. Sélectionnez **Enregistrer**.
## <a name="create-risk-assessment-reports"></a>Créer des rapports d’évaluation des risques

Créer un rapport d’évaluation des risques au format PDF Le rapport est généré automatiquement sous le nom risk-assessment-report-1.pdf Le numéro est mis à jour pour chaque nouveau rapport que vous créez.  L’heure et le jour de création sont affichés.

### <a name="create-a-sensor-risk-assessment-report"></a>Créer un rapport d’évaluation des risques de capteur

Créez un rapport d’évaluation des risques basé sur les détections effectuées par le capteur auquel vous êtes connecté.

Pour créer un rapport :

1. Connectez-vous à la console du capteur.
1. Sélectionnez **Évaluation des risques** dans le menu latéral.
1. Sélectionnez **Générer un rapport**. Le rapport s’affiche dans la section Rapports archivés.
1. Sélectionnez le rapport dans la section Rapports archivés pour le télécharger.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Vue de l’évaluation des risques.":::

Pour importer un logo de société :

- Sélectionnez **Importer un logo**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Créer un rapport d’évaluation des risques de la console de gestion locale

Créez un rapport d’évaluation des risques basé sur les détections effectuées par l’un des capteurs gérés par votre console de gestion locale. 

Pour créer un rapport :

1. Sélectionnez **Évaluation des risques** dans le menu latéral.

2. Sélectionnez un capteur dans la liste déroulante **Sélectionner le capteur**.

3. Sélectionnez **Générer un rapport**.

4. Sélectionnez **Télécharger** à partir de la section **Rapports archivés**.

Pour importer un logo de société :

- Sélectionnez **Importer un logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importez votre logo par le biais de l’affichage de l’évaluation des risques.":::

## <a name="see-also"></a>Voir aussi

[Création de rapports de vecteurs d’attaque](how-to-create-attack-vector-reports.md)
