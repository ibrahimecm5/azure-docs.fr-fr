---
title: Rapport d’informations sur les menaces de Microsoft Defender pour le cloud | Microsoft Docs
description: Cette page explique comment utiliser les rapports d’informations sur les menaces de Microsoft Defender pour le cloud pendant une investigation afin de trouver plus d’informations sur les alertes de sécurité
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2021
ms.author: memildin
ms.openlocfilehash: 190cc14274b8e96434ab82fe7ff1df486d1b608b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472083"
---
# <a name="microsoft-defender-for-cloud-threat-intelligence-report"></a>Rapport d’informations sur les menaces de Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette page explique comment les rapports d’informations sur les menaces de Microsoft Defender pour le cloud peuvent vous aider à en savoir plus sur une menace ayant déclenché une alerte de sécurité.

## <a name="what-is-a-threat-intelligence-report"></a>Qu’est-ce qu’un rapport d’informations sur les menaces ?

La protection contre les menaces de Defender pour le cloud fonctionne en surveillant les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces. Pour plus d’informations, consultez [Comment Microsoft Defender pour le cloud détecte aux menaces et y répond](alerts-overview.md#detect-threats).

Lorsque Defender pour le cloud identifie une menace, il déclenche une [alerte de sécurité](managing-and-responding-alerts.md), qui contient des informations détaillées sur l’événement, notamment des suggestions de correction. Pour aider les équipes de réponse aux incidents à examiner et à corriger les menaces, Defender pour le cloud fournit un rapport contenant des informations sur les menaces détectées. Le rapport inclut des informations, telles que :

* Identité ou associations des attaquants (si cette information est disponible)
* Objectifs des attaquants
* Campagnes d’attaque actuelles et historiques (si cette information est disponible)
* Tactiques, outils et procédures des attaquants
* Indicateurs de compromission (IoC) associés, tels que les URL et les hachages de fichier
* Victimologie, c’est-à-dire la prévalence industrielle et géographique, afin de vous aider à déterminer si vos ressources Azure sont exposées au risque
* Informations d’atténuation et de correction

> [!NOTE]
> La quantité d’informations dans un rapport donné peut varier. Le niveau de détail est basé sur l’activité et la prévalence du programme malveillant.

Defender pour le cloud propose trois types de rapports sur les menaces, qui peuvent varier en fonction de l’attaque. Les rapports disponibles sont les suivants :

* **Rapport sur le groupe d’activités** : fournit des informations détaillées sur les attaquants, leurs objectifs et leurs tactiques.
* **Rapport sur la campagne** : se concentre sur les détails des campagnes d’attaque spécifiques.
* **Rapport de synthèse sur la menace** : couvre tous les éléments des deux rapports précédents.

Ce type d'informations est utile pendant le processus de réponse aux incidents, au cours duquel des examens sont effectués en continu afin d'identifier la source de l'attaque, les motivations de l'attaquant et les mesures à prendre pour éviter que le problème se reproduise.

## <a name="how-to-access-the-threat-intelligence-report"></a>Comment accéder au rapport d’informations sur les menaces ?

1. À partir du menu de Defender pour le cloud, ouvrez la page **Alertes de sécurité**.
1. Sélectionnez une alerte. 

    La page des détails des alertes s’ouvre et affiche des détails supplémentaires sur l’alerte. Vous trouverez ci-dessous la page des détails de l’alerte **Des indicateurs de ransomware ont été détectés**.

    [![Page des détails de l’alerte Des indicateurs de ransomware ont été détectés.](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Sélectionnez le lien du rapport et un fichier PDF s’ouvre dans votre navigateur par défaut.

    [![Page des détails de l’alerte Action potentiellement non sécurisée.](media/threat-intelligence-reports/threat-intelligence-report.png)](media/threat-intelligence-reports/threat-intelligence-report.png#lightbox)

    Vous pouvez éventuellement télécharger le rapport au format PDF. 

    >[!TIP]
    > La quantité d’informations disponibles pour chaque alerte de sécurité varie selon le type d’alerte.

## <a name="next-steps"></a>Étapes suivantes

Cette page explique comment ouvrir des rapports de renseignements sur les menaces lors de l’examen des alertes de sécurité. Pour accéder à des informations connexes, consultez les rubriques suivantes :

* [Gérer les alertes de sécurité et y répondre dans Microsoft Defender pour le cloud](managing-and-responding-alerts.md). découvrez comment gérer et résoudre les alertes de sécurité.
* [Gérer les incidents de sécurité dans Microsoft Defender pour le cloud](incidents.md)
