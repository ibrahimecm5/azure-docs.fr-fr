---
title: Comprendre les alertes de capteur
description: Utilisez des alertes pour vous aider à améliorer la sécurité et l’opération de votre réseau.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 7f97ba63303c1dec4f8559a896748e9860e25060
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318861"
---
# <a name="about-sensor-alerts"></a>À propos des alertes de capteur

Les alertes vous aident à améliorer la sécurité et le fonctionnement de votre réseau. Les alertes vous fournissent des informations sur les éléments suivants :

- écarts par rapport à l’activité réseau autorisée

- protocole et anomalies opérationnelles

- trafic des programmes malveillants suspectés

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Détecter une analyse d’adresse.":::

Les options de gestion des alertes permettent aux utilisateurs :

- de demander aux capteurs d’apprendre l’activité détectée comme du trafic autorisé

- de reconnaitre la révision de l’alerte

- de demander aux capteurs de désactiver le micro des événements détectés avec des appareils identiques et un trafic comparable.

Des outils supplémentaires sont disponibles pour vous aider à améliorer et à accélérer l’investigation des alertes. Par exemple :

  - Ajoutez des commentaires pédagogiques pour les réviseurs d’alertes.

  - Créez des groupes d’alertes à afficher dans les solutions SOC. 

  - Recherchez des alertes spécifiques ; évaluez les fichiers PCAP associés ; affichez les périphériques détectés et les autres appareils connectés dans la carte de l’appareil ou envoyez les détails de l’alerte aux systèmes partenaires.

  - Transférer des alertes aux fournisseurs de partenaires : systèmes SIEM, systèmes MSSP et bien plus encore.

## <a name="alerts-and-engines"></a>Alertes et moteurs

Les alertes sont déclenchées lorsque les moteurs de capteurs détectent des modifications du trafic réseau et du comportement qui nécessitent votre attention. Cet article décrit le type d’alertes déclenchées par chaque moteur.

| Type d’alerte | Description |
|-|-|
| Alertes de violation de stratégie | Déclenchées lorsque le moteur de violation de stratégie détecte un écart par rapport au trafic précédemment appris. Par exemple : <br /> - un nouvel appareil est détecté  <br /> - une nouvelle configuration est détectée sur un appareil <br /> - un appareil non défini en tant que périphérique de programmation effectue une modification de programmation <br /> - une version de microprogramme est modifiée. |
| Alertes de violation de protocole | Déclenchées lorsque le moteur de violation du protocole détecte des structures de paquets ou des valeurs de champ qui ne sont pas conformes avec la spécification du protocole. | 
| Alertes opérationnelles | Déclenchées lorsque le moteur opérationnel détecte des incidents opérationnels réseau ou un appareil défectueux. Par exemple, un périphérique réseau a été arrêté à l’aide d’une commande Stop PLC ou une interface sur un capteur a cessé d’analyser le trafic. |
| Alertes de programmes malveillants | Déclenchées lorsque le moteur de programmes malveillants détecte une activité réseau malveillante. Par exemple, le moteur détecte une attaque connue telle que Conficker. |
| Alertes concernant des anomalies | Déclenchées lorsque le moteur Anomalie détecte un écart. Par exemple, un appareil effectue des analyses réseau, mais n’est pas défini en tant qu’appareil d’analyse. |

Des outils sont disponibles pour activer et désactiver les moteurs de capteur. Les alertes ne sont pas déclenchées à partir des moteurs qui sont désactivés. Consultez [Contrôler quel trafic est analysé](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Alertes et rapports de capteur

L’activité reflétée dans les alertes est calculée lorsque vous générez des rapports d’exploration de données, d’évaluation des risques et de vecteur d’attaque. Lorsque vous gérez ces événements, le capteur met à jour les rapports en conséquence.

Par exemple :

  - La connectivité non autorisée entre un appareil dans un sous-réseau défini et des appareils situés en dehors du sous-réseau (public) apparaît dans le rapport d’exploration de données *Activité Internet* et dans la section d’évaluation des risques *Connexions Internet*. Une fois ces appareils autorisés (appris), ils sont calculés lors de la génération de ces rapports.

  - Les événements de programmes malveillants détectés sur les périphériques réseau sont signalés dans les rapports d’évaluation des risques. Quand les alertes relatives aux événements de programmes malveillants ont le *micro désactivé*, les appareils affectés ne sont pas calculés dans le rapport d’évaluation des risques.

## <a name="next-steps"></a>Étapes suivantes

[Apprentissage et modes d’apprentissage informatique intelligent](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
[Afficher les informations fournies dans les alertes](how-to-view-information-provided-in-alerts.md)
[Gérer l’événement d’alerte](how-to-manage-the-alert-event.md)
[Accélérer les workflows d’alerte](how-to-accelerate-alert-incident-response.md)
[Types d’alertes et descriptions](alert-engine-messages.md)
