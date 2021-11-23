---
title: Obtenir des informations sur les menaces locales, régionales et mondiales
description: Obtenez une vue d’ensemble sur les menaces locales, régionales et globales à l’aide du plan de site dans la console de gestion locale.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 6cef79e708f5aff5eff2c02f2b0fe612aa11a224
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293228"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Obtenir des informations sur les menaces locales, régionales et mondiales

Le plan de site de la console de gestion locale vous permet d’obtenir une couverture complète de la sécurité en divisant votre réseau en segments géographiques et logiques correspondant à la topologie de votre entreprise :

- **Niveau géographique** : Un site représente un certain nombre d’appareils regroupés en fonction d’un emplacement géographique présenté sur la carte. Par défaut, Microsoft Defender pour IoT vous fournit une carte du monde. Vous mettez à jour cette carte en fonction de la structure organisationnelle ou commerciale de votre organisation. Par exemple, utilisez une carte qui présente les sites par pays, par ville ou un par site industriel. Lorsque la couleur du site change sur la carte, il fournit à l’équipe SOC une indication de l’état du système critique de cette installation.

  Cette carte est interactive et permet d’ouvrir chaque site et de plonger dans les informations de ce site.

- **Couche logique globale** : la division est un bon moyen pour diviser votre entreprise en segments logiques selon différents secteurs. Dans ce cas, la topologie de votre entreprise est présentée sur la carte.

  Par exemple, une multinationale qui possède des usines de production de verre et de plastique et de construction automobile peut être gérée comme trois entités différentes. Un site physique à Toronto comprend trois lignes de production de verre, une ligne de production de plastique et une ligne de production de moteurs de camions. Ce site possède donc des représentants des trois entités.

- **Niveau régional** : Créez des régions pour diviser une entreprise globale en régions géographiques. Par exemple, l’entreprise que nous avons décrite peut utiliser les régions Amérique du Nord, Europe Ouest et Europe Est. Amérique du Nord comporte des usines des trois entités. Europe Ouest comporte des usines d’automobile et de production de verre et Europe Est n’a que des usines produisant du plastique.

- **Niveau de segment logique local** : Une zone est un segment logique au sein d’un site et qui définit, par exemple, une zone fonctionnelle ou une ligne de production. L’utilisation de zones permet d’appliquer des stratégies de sécurité pertinentes en fonction de la définition de la zone. Par exemple, un site qui contient cinq lignes de production peut être segmenté en cinq zones.

- **Niveau local** : La vue locale d’une installation à capteur unique fournit des informations sur l’état de fonctionnement et de sécurité des appareils connectés.

## <a name="work-with-site-map-views"></a>Utiliser les vues de plan de site

La console de gestion locale fournit une vue d’ensemble de votre réseau industriel dans une carte contextuelle. La vue générale présente la carte globale de votre organisation avec l’emplacement géographique de chaque site.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Capture d’écran de la vue de carte d’entreprise.":::

### <a name="color-coded-map-views"></a>Affichages de cartes en couleur

**Vert** : Le nombre d’événements de sécurité est inférieur au seuil défini par Defender pour IoT pour votre système. Aucune action n'est nécessaire.

**Jaune** : Le nombre d’événements de sécurité est égal au seuil défini par Defender pour IoT pour votre système. Vous devrez peut-être examiner ces événements.  

**Rouge** : Le nombre d’événements de sécurité dépasse le seuil défini par Defender pour IoT pour votre système. Vous devez agir immédiatement.

### <a name="risk-level-map-views"></a>Affichage par niveau de risque

**Évaluation des risques** : l’affichage évaluation des risques affiche des informations sur les risques liés au site. Les informations sur les risques vous aident à hiérarchiser les priorités et à créer une feuille de route pour planifier les améliorations à apporter à la sécurité.

**Réponse aux incidents** : bénéficiez d’une vue centralisée de toutes les alertes non vues sur chaque site de l’entreprise. Vous pouvez analyser et gérer les alertes détectées dans chaque site.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Capture d’écran de la vue de carte d’entreprise avec réponse aux incidents.":::

**Activités malveillantes** : si un programme malveillant a été détecté, le site s’affiche en rouge. Cela indique que vous devez immédiatement agir.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Capture d’écran de la vue de carte d’entreprise avec les activités malveillantes.":::

**Alertes opérationnelles** : Cette vue cartographique pour les systèmes OT permet de mieux comprendre quel système OT peut faire face à des incidents, par exemple un arrêts des applications, le chargement du microprogramme et le téléchargement de programmes.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Capture d’écran de la vue de carte d’entreprise avec les alertes opérationnelles.":::

Pour choisir un style de carte :

1. Sélectionnez **Vue par défaut** dans la carte.
2. Sélectionnez un affichage.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Capture d’écran de l’affichage par défaut du plan du site.":::

## <a name="update-the-site-map-image"></a>Mettre à jour l’image du plan de site

Defender pour IoT fournit par défaut une carte du monde. Vous pouvez la changer pour qu’elle corresponde mieux à votre organisation : une carte du pays ou un plan de la ville, par exemple. 

Pour remplacer la carte :

1. Dans le volet gauche, sélectionnez **Paramètres du système**.

2. Sélectionnez **Modifier le plan de site** et chargez le fichier graphique qui doit remplacer la carte par défaut.

## <a name="next-step"></a>Étape suivante

[Afficher les alertes](how-to-view-alerts.md)
