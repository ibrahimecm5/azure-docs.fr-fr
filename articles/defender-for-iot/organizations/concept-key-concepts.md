---
title: Principaux avantages
description: En savoir plus sur les concepts de base de Defender pour IoT.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 271af4bbfe56771576dec32799b19f2c7aebf258
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293437"
---
# <a name="basic-concepts"></a>Concepts de base

Cet article décrit les principaux avantages de Microsoft Defender pour IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Déploiement non invasif rapide et surveillance passive

Les capteurs Defender pour IoT se connectent aux ports SPAN (Miroir) des commutateurs et aux TAP du réseau et commencent immédiatement à collecter le trafic ICS via une surveillance passive (sans agent). L’inspection profonde des paquets (DPI) est utilisée pour étudier en détail le trafic à partir de l’équipement réseau de contrôle Ethernet et série. Defender pour IoT n’a aucun impact sur les réseaux OT, car il n’est pas placé dans le chemin d’accès aux données et n’analyse pas activement les appareils. 

Pour fournir des instantanés d’informations détaillées sur les appareils Windows, le capteur Defender pour IoT peut être configuré pour compléter la surveillance passive avec un composant actif facultatif. Ce composant utilise des commandes fiables, approuvées par le fournisseur, pour interroger les appareils Windows sur les détails des appareils, à la fréquence que vous avez définie.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Connaissances incorporées des protocoles, des appareils et des applications ICS

Le DPI seul ne suffit pas à identifier les anomalies de protocole et à identifier l’appareil à un niveau granulaire. Le capteur de Defender pour IoT traite certains des environnements les plus volumineux et les plus complexes. Plus de 1 300 réseaux OT ont été analysés à ce jour, dans tous les secteurs industriels.

## <a name="analytics-and-self-learning-engines"></a>Analyses et moteurs d’apprentissage automatique

Les moteurs identifient les problèmes de sécurité via la surveillance continue et cinq moteurs d’analyse qui incorporent l’apprentissage automatique pour éliminer la nécessité de mettre à jour des signatures ou de définir des règles. Les moteurs utilisent l’analyse comportementale et la science des données propres à ICS pour analyser en permanence les anomalies du trafic réseau. Les cinq moteurs sont les suivants :

- **Détection de violation de protocole** : Identifie l’utilisation des structures de paquets et des valeurs de champ qui ne respectent pas les spécifications de protocole ICS.

- **Détection de violation de stratégie** : Identifie les violations de stratégie, telles que l’utilisation non autorisée de codes de fonction, l’accès à des objets spécifiques ou la modification de la configuration de l’appareil.

- **Détection de programmes malveillants industriels** : Identifie les comportements qui indiquent la présence de programmes malveillants connus, tels que Conficker, Black Energy, Havex, WannaCry et NotPetya.

- **Détection des anomalies** : Détecte les communications et comportements machine à machine (M2M) inhabituels. En modélisant les réseaux ICS en tant que séquences déterministes d’états et de transitions, le moteur utilise une technique brevetée appelée modélisation d’état fini industriel (Industrial Finite State Modeling, IFSM). La solution nécessite une période d’apprentissage plus rapide que les approches mathématiques génériques ou les analyses, qui ont été développées à l’origine pour l’informatique et non pour les réseaux OT. Elle détecte également les anomalies plus rapidement, avec un nombre minimal de faux positifs.

- **Détection d’incidents opérationnels** : Détecte des problèmes opérationnels, tels qu’une connectivité intermittente, qui peuvent indiquer des signes précoces de défaillance de l’équipement.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analyse du trafic réseau pour l’évaluation des risques et des vulnérabilités

Unique dans le secteur, Defender pour IoT utilise des algorithmes d’analyse du trafic propriétaire (NTA) pour identifier passivement toutes les vulnérabilités du réseau et des points de terminaison, telles que :

- Connexions d’accès à distance non autorisées
- Appareils non autorisés ou non référencés
- Authentifications faibles
- Appareils vulnérables (basés sur des CVE non corrigés)
- Ponts non autorisés entre les sous-réseaux
- Règles de pare-feu faibles

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Exploration de données pour les investigations, la forensique et la recherche de menaces potentielles

La plateforme fournit une interface d’exploration de données intuitive pour l’analyse granulaire du trafic historique dans toutes les dimensions pertinentes. Les exemples incluent la période, l’adresse IP, l’adresse MAC et les ports. Vous pouvez également effectuer des requêtes spécifiques au protocole en fonction des codes de fonction, des services de protocole et des modules. Les PCAP de haute fidélité sont disponibles pour une analyse approfondie.

## <a name="sensor-cloud-management-mode"></a>Mode de gestion Cloud du capteur

Le mode de gestion Cloud du capteur détermine l’emplacement d’affichage de l’appareil, de l’alerte et d’autres informations détectées par le capteur.

Pour les **capteurs connectés au Cloud**, les informations détectées s’affichent dans la console des capteurs en question. Les informations sur les alertes sont transmises par le biais d’un hub IoT et peuvent être partagées avec d’autres services Azure, comme Microsoft Sentinel.

Pour les **capteurs connectés localement**, les informations détectées s’affichent dans la console des capteurs concernés. Les informations de détection sont également partagées avec la console de gestion locale si le capteur y est connecté.

## <a name="air-gapped-networks"></a>Réseaux hermétiques

Si vous travaillez dans un environnement hermétique, la console de gestion sur site de Defender pour IoT offre une vue en temps réel des principaux indicateurs de risque et des alertes de l'IoT et de l'OT dans toutes vos installations. Étroitement intégré à vos workflows SOC et à vos runbooks, il permet de hiérarchiser facilement les activités d’atténuation et de corréler les menaces entre les différents sites.

Defender pour IoT fournit une vue détaillée de tous vos appareils. Il fournit également des informations critiques sur les appareils, telles que le type (PLC, RTU, DC, etc.), le fabricant, le modèle et la version du microprogramme, ainsi que des informations sur les alertes.

Defender pour IoT permet une gestion efficace de plusieurs déploiements et une vue unifiée complète du réseau. Defender pour IoT optimise la gestion des alertes et le contrôle de la sécurité du réseau opérationnel.

La console de gestion locale est une plateforme d’administration basée sur le Web qui vous permet de surveiller et de contrôler les activités des installations de capteurs globaux. En plus de gérer les données reçues à partir des capteurs déployés, la console de gestion locale intègre en toute transparence les données de diverses ressources de l’entreprise : CMDB, DNS, pare-feu, API Web et bien plus encore.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Affichage de la console de gestion locale.":::

Nous vous recommandons de vous familiariser avec les concepts et les fonctionnalités disponibles pour les capteurs avant d’utiliser la console de gestion locale.

## <a name="integrations"></a>Intégrations

Vous pouvez étendre les fonctionnalités de Defender pour IoT en partageant les informations des appareils et des alertes avec les systèmes partenaires. Les intégrations aident les entreprises à lier des solutions de sécurité précédemment en silo pour améliorer la visibilité des appareils et les connaissances sur les menaces. Les intégrations permettent également aux entreprises d’accélérer les réponses à l’ensemble du système et d’atténuer les risques plus rapidement. 

Les intégrations réduisent la complexité et éliminent les silos de votre installation informatique et de vos réseaux OT en les intégrant dans vos flux de travail et votre pile de sécurité SOC existants. Par exemple :

- SIEM tels qu’IBM QRadar, Splunk, ArcSight, LogRhythm et RSA NetWitness

- Systèmes d’orchestration et de gestion de tickets de sécurité tels que ServiceNow et IBM Resilient

- Solutions d’accès à distance sécurisé, telles que le gestionnaire de sessions privilégiées (PSM) CyberArk et BeyondTrust

- Systèmes de contrôle d’accès réseau (NAC) sécurisés, tels que Aruba ClearPass et Forescout CounterACT

- Pare-feu tels que Fortinet et Check Point

## <a name="complete-protocol-support"></a>Prise en charge complète du protocole

En plus de la prise en charge des protocoles incorporés, vous pouvez sécuriser les appareils IoT et ICS exécutant des protocoles propriétaires et personnalisés, ou des protocoles qui s’écartent de n’importe quel standard. À l’aide du kit de développement logiciel (SDK) horizon Open Development Environment (ODE), les développeurs peuvent créer des plug-ins de dissecteurs qui décodent le trafic réseau en fonction des protocoles définis. Les services analysent le trafic pour fournir une surveillance, des alertes et des rapports complets. Utilisez horizon pour :

- Développez la visibilité et le contrôle sans avoir à effectuer une mise à niveau vers les nouvelles versions.

- Sécuriser les informations propriétaires en développant sur site en tant que plug-in externe.

- Localisez le texte pour les alertes, les événements et les paramètres de protocole.

En outre, vous pouvez utiliser des alertes de protocole propriétaire pour communiquer des informations :

- Sur les détections de trafic basées sur les protocoles et les protocoles sous-jacents dans un plug-in Horizon propriétaire.

- Sur une combinaison de champs de protocole à partir de toutes les couches de protocole. Par exemple, dans un environnement exécutant MODBUS, vous souhaiterez peut-être générer une alerte quand le capteur détecte une commande d’écriture dans un registre de mémoire sur une adresse IP et une destination Ethernet spécifiques. Vous pouvez également générer une alerte quand un accès est effectué sur une adresse IP spécifique.

Les alertes sont déclenchées lorsque les conditions d’alerte Horizon définies sont remplies.

En outre, l’utilisation des alertes personnalisées Horizon vous permet d’écrire vos propres titres et messages d’alerte. Les valeurs et les champs de protocole résolus peuvent être incorporés dans le texte du message d’alerte.

L’utilisation de la messagerie et du déclenchement d’alertes basés sur des conditions personnalisées permet d’identifier l’activité réseau et de mettre à jour efficacement vos équipes de sécurité, informatiques et opérationnelles.

Pour obtenir la liste complète des protocoles pris en charge, consultez [Protocoles pris en charge](concept-supported-protocols.md#supported-protocols).

## <a name="what-is-an-inventory-device"></a>Qu’est-ce qu’un appareil d’inventaire ?

L’inventaire d’appareil Defender pour IoT affiche un vaste éventail d’attributs de ressource qui sont détectés par les capteurs qui contrôlent les réseaux d’organisations et les points de terminaison gérés.

Defender pour IoT identifie et classe les appareils comme un appareil réseau unique dans l’inventaire pour :

1. Appareils IT, OT et IoT autonomes (avec 1 ou plusieurs cartes d’interface réseau)
1. Appareils composés de plusieurs composants de carte d’insertion (notamment tous les racks/emplacements/modules)
1. Appareils agissant comme une infrastructure réseau comme commutateur/routeur (avec plusieurs cartes d’interface réseau). 

Les adresses IP Internet publiques, les groupes de multidiffusion et les groupes de diffusion ne sont pas considérés comme des appareils d’inventaire.
Les appareils qui ont été inactifs depuis plus de 60 jours sont classés en tant qu’appareils d’inventaire inactifs.


## <a name="high-availability"></a>Haute disponibilité

Augmentez la résilience de votre déploiement de Defender pour IoT en installant une appliance à haute disponibilité dans la console de gestion locale. Les déploiements à haute disponibilité garantissent que vos capteurs gérés envoient des rapports constants à la console de gestion locale active.

Ce déploiement est implémenté avec une paire de la console de gestion locale qui comprend une appliance principale et secondaire.

## <a name="localization"></a>Localisation

De nombreuses fonctionnalités de la console prennent en charge un large éventail de langues.

## <a name="next-step"></a>Étape suivante

[Bien démarrer avec Defender pour IoT](getting-started.md)
