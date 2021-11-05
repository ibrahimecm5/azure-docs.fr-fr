---
title: Configuration et réponse aux alertes de Microsoft Defender pour les bases de données relationnelles open source
description: Découvrez comment configurer Microsoft Defender pour les bases de données relationnelles open source afin de détecter les activités anormales de base de données indiquant des menaces de sécurité potentielles pour la base de données.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7d34663e9c83e74ea25ce501a89a5a34b38677e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055999"
---
# <a name="enable-microsoft-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>Activer Microsoft Defender pour les bases de données relationnelles open source et répondre aux alertes

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender détecte les activités anormales indiquant des tentatives inhabituelles et potentiellement dangereuses pour accéder à des bases de données ou les exploiter pour les services suivants :

- [Base de données Azure pour PostgreSQL](../postgresql/index.yml)
- [Azure Database pour MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Pour recevoir des alertes du plan Microsoft Defender, vous devez d’abord l’activer comme [illustré ci-dessous](#enable-enhanced-security).

Pour en savoir plus sur ce plan Microsoft Defender, consultez [Présentation de Microsoft Defender pour les bases de données relationnelles open source](defender-for-databases-introduction.md).

## <a name="enable-enhanced-security"></a>Activer la sécurité renforcée

1. Dans [le portail Azure](https://portal.azure.com), ouvrez la page de configuration du serveur de base de données que vous souhaitez protéger.

1. Dans le menu Sécurité sur la gauche, sélectionnez **Microsoft Defender pour le cloud**.

1. Si la sécurité renforcée n’est pas activée, un bouton s’affiche comme indiqué dans la capture d’écran suivante. Sélectionnez **Activer Microsoft Defender pour [Type de base de données]** (par exemple, « Microsoft Defender pour MySQL ») et sélectionnez **Enregistrer**.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Activer Microsoft Defender pour MySQL." lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > Cette page dans le portail sera la même quel que soit le type de base de données (PostgreSQL, MySQL ou MariaDB).

## <a name="respond-to-security-alerts"></a>Répondre à des alertes de sécurité

Quand Microsoft Defender est activé sur votre base de données, il détecte les activités anormales et génère des alertes. Ces alertes sont disponibles à partir de plusieurs emplacements, notamment :

- Dans le portail Azure :
    - **Page des alertes de sécurité de Microsoft Defender** - Affiche les alertes pour toutes les ressources protégées par Defender pour le cloud dans les abonnements que vous avez le droit de visualiser.
    - La page **Microsoft Defender pour le cloud** de la ressource - Affiche les alertes et les recommandations pour une ressource spécifique, comme indiqué ci-dessus dans [Activer le sécurité renforcée](#enable-enhanced-security).
- Dans la boîte de réception de la personne de votre organisation qui a été [ désignée pour recevoir des alertes par e-mail](configure-email-notifications.md).  

> [!TIP]
> Une vignette dynamique sur le [tableau de bord de Microsoft Defender pour le cloud](overview-page.md) permet de suivre l’état des menaces actives pour toutes vos ressources, y compris les bases de données. Sélectionnez la vignette pour lancer la page des alertes Defender pour le cloud et afficher une vue d’ensemble des menaces actives détectées sur vos bases de données.
>
> Pour connaître les étapes détaillées et la méthode recommandée pour répondre aux alertes de sécurité, consultez [Répondre à une alerte de sécurité](tutorial-security-incident.md#respond-to-a-security-alert).

### <a name="respond-to-email-notifications-of-security-alerts"></a>Répondre aux notifications par e-mail des alertes de sécurité

Defender pour le cloud envoie des notifications par e-mail lorsqu’il détecte des activités de base de données anormales. L’e-mail contient des détails sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur, le nom de l’application et l’heure de l’événement. L’e-mail fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur la base de données.

1. Dans l’e-mail, sélectionnez le lien **Afficher l’alerte complète** pour ouvrir le portail Azure et accéder à la page d’alertes, qui fournit une vue d’ensemble des menaces actives détectées sur la base de données.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="Notification par e-mail de Defender pour le cloud concernant une attaque par force brute potentielle.":::

    Affichez les menaces actives au niveau de l’abonnement dans les pages du portail Defender pour le cloud :

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="Les menaces actives sur un ou plusieurs abonnements sont affichées dans Microsoft Defender pour le cloud." lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. Pour obtenir des détails supplémentaires et des actions recommandées pour examiner la menace actuelle et corriger les menaces futures, sélectionnez une alerte spécifique.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="Détails d’une alerte spécifique." lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> Pour un tutoriel détaillé sur la façon de gérer vos alertes, consultez [Tutoriel: Trier les alertes de sécurité, les examiner et y répondre](tutorial-security-incident.md).


## <a name="next-steps"></a>Étapes suivantes

- [Automatiser les réponses aux déclencheurs Defender pour le cloud.](workflow-automation.md)
- [Diffuser des alertes sur une solution SIEM, SOAR ou ITSM](export-to-siem.md)
- [Supprimer les alertes de Defender pour le cloud](alerts-suppression-rules.md)
