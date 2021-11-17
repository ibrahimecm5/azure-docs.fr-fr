---
title: Intégrer la Gestion des API Azure avec Azure Application Insights
titleSuffix: Azure API Management
description: Découvrez comment consigner et afficher des événements de la Gestion des API Azure dans Azure Application Insights.
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/27/2021
ms.author: danlep
ms.openlocfilehash: eaeacb016098df74d4a8ff7f20ebf677e4322479
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447452"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Guide pratique pour intégrer la Gestion des API Azure avec Azure Application Insights

Vous pouvez facilement intégrer Azure Application Insights avec Gestion des API Azure. Azure Application Insights est un service extensible permettant aux développeurs web de créer et de gérer des applications sur de nombreuses plateformes. Dans ce guide, vous allez :
* Parcourir toutes les étapes de l’intégration d’Application Insights dans Gestion des API.
* Découvrir les stratégies permettant de réduire l’impact sur les performances de votre instance de service Gestion des API.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une instance du service Gestion des API Azure. Commencez par en [créer une](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Créer une instance Application Insights

Pour utiliser Application Insights, [créez une instance du service Application Insights](../azure-monitor/app/create-new-resource.md). Pour créer une instance à l’aide du portail Azure, consultez [Ressources Application Insights basées sur l’espace de travail](../azure-monitor/app/create-workspace-resource.md).

## <a name="create-a-connection-between-application-insights-and-api-management"></a>Créer une connexion entre Application Insights et API Management

1. Accédez à votre **instance de service Gestion des API Azure** sur le **Portail Azure**.
1. Sélectionnez **Application Insights** dans le menu de gauche.
1. Sélectionnez **Ajouter**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Capture d’écran montrant où ajouter une nouvelle connexion":::
1. Sélectionnez l’instance **Application Insights** que vous avez créée précédemment, et entrez une brève description.
1. Pour activer la [supervision de la disponibilité](../azure-monitor/app/monitor-web-app-availability.md) de votre instance de gestion des API dans Application Insights, cochez la case **Ajouter un moniteur de disponibilité**.
    * Ce paramètre valide régulièrement si le point de terminaison du service de gestion des API répond. 
    * Les résultats s’affichent dans le volet **Disponibilité** de l’instance Application Insights.
1. Sélectionnez **Create** (Créer).
1. Vérifiez que le nouvel enregistreur d’événements d’Application Insights avec une clé d’instrumentation figure désormais dans la liste.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Capture d’écran montrant où afficher l’enregistreur d’événements Application Insights nouvellement créé avec la clé d’instrumentation":::

> [!NOTE]
> En arrière-plan, une entité [Enregistreur d’événements](/rest/api/apimanagement/2020-12-01/logger/create-or-update) est créée dans votre instance de gestion des API, qui contient la clé d’instrumentation de l’instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Activer la journalisation Application Insights pour l’API

1. Accédez à votre **instance de service Gestion des API Azure** sur le **Portail Azure**.
1. Sélectionnez **API** dans le menu de gauche.
1. Cliquez sur votre API, dans ce cas **Demo Conference API**. Si elle est configurée, sélectionnez une version.
1. Accédez à l’onglet **Paramètres** de la barre supérieure.
1. Descendez jusqu’à la section **Journaux de diagnostic**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Enregistreur d'événements App Insights":::
1. Cochez la case **Activer**.
1. Sélectionnez l’enregistreur d'événements joint dans la liste déroulante **Destination**.
1. Entrez **100** pour **Échantillonnage (%)** et sélectionnez la case **Toujours consigner les erreurs**.
1. Laissez le reste des paramètres tel quel.

    > [!WARNING]
    > Le remplacement de la valeur **Nombre d’octets de charge utile à journaliser** par défaut, **0**, peut réduire considérablement les performances de vos API.

1. Sélectionnez **Enregistrer**.
1. En coulissez, une entité [Diagnostic](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update) nommée `applicationinsights`’ est créée au niveau de l’API.

> [!NOTE]
> Les demandes aboutissent une fois que Gestion des API a envoyé l’intégralité de la réponse au client.


| Nom du paramètre                        | Type de valeur                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Activer                              | boolean                           | Indique si la journalisation de cette API est activée.                                                                                                                                                                                                                                                                                                |
| Destination                         | Enregistreur d'événements Azure Application Insights | Spécifie l’enregistreur d’événements Azure Application Insights utilisé.                                                                                                                                                                                                                                                                                           |
| Échantillonnage (%)                        | Décimal                           | Valeurs comprises entre 0 et 100 (%). <br/> Spécifie le pourcentage de requêtes qui seront consignées dans Application Insights. Un échantillonnage à 0 % signifie qu’aucune demande ne sera consignée, tandis qu’un échantillonnage à 100 % indique qu’elles le seront toutes. <br/> Utilisez ce paramètre pour réduire l’effet sur les performances lors de la journalisation des demandes sur Application Insights. Consultez [Conséquences sur les performances et échantillonnage des journaux](#performance-implications-and-log-sampling). |
| Toujours consigner les erreurs                   | boolean                           | Si ce paramètre est sélectionné, toutes les défaillances seront consignées dans Application Insights, quel que soit le paramètre **Échantillonnage**.   
| Adresse IP du client du journal | |  Si ce paramètre est sélectionné, l’adresse IP du client pour les requêtes d’API sera enregistrée dans Application Insights.                                         |
| Commentaires         |                                   | Spécifie le niveau de verbosité. Seules les traces personnalisées avec un niveau de gravité supérieur sont journalisées. Valeur par défaut : Information.      | 
| Protocole de corrélation |  |  Sélectionnez le protocole utilisé pour mettre en corrélation les données de télémétrie envoyées par plusieurs composants. Valeur par défaut : hérité <br/>Pour plus d’informations, consultez l’article [Corrélation de télémétrie dans Application Insights](../azure-monitor/app/correlation.md).  |
| Options de base : En-têtes à consigner              | list                              | Précise quels en-têtes seront consignés dans Application Insights pour les demandes et les réponses.  Par défaut, aucun en-tête n’est consigné.                                                                                                                                                                                                             |
| Options de base : nombre d’octets de charge utile à enregistrer dans le journal | entier                           | Indique combien de premiers octets du corps seront consignés dans Application Insights pour les demandes et les réponses.  Valeur par défaut : 0.                                                                                                                                                                                                    |
| Options avancées : Demande du serveur frontal  |                                   | Précise si des *demandes frontales* seront consignées dans Application Insights et, si oui, comment. Une *demande frontale* est une demande entrante dans le service Gestion des API Azure.                                                                                                                                                                        |
| Options avancées : Réponse du serveur frontal |                                   | Précise si des *réponses frontales* seront consignées dans Application Insights et, si oui, comment. Une *réponse frontale* est une réponse sortante du service Gestion des API Azure.                                                                                                                                                                   |
| Options avancées : Demande principale   |                                   | Précise si des *demandes principales* seront consignées dans Application Insights et, si oui, comment. Une *demande principale* est une demande sortante du service Gestion des API Azure.                                                                                                                                                                        |
| Options avancées : Réponse principale  |                                   | Précise si des *réponses principales* seront consignées dans Application Insights et, si oui, comment. Une *réponse principale* est une réponse entrante dans le service Gestion des API Azure.                                                                                                                                                                       |

> [!NOTE]
> Vous pouvez spécifier des enregistreurs d’événements à des différents niveaux : 
> + Enregistreur d’événements d’API unique.
> + Enregistreur d’événements pour toutes les API.
>  
> Spécification *des deux* :
> + S’ils sont différents, les deux seront utilisés (journaux d’activité de multiplexage).
> + S’ils sont identiques avec des paramètres différents, l’enregistreur d’événements d’API unique (niveau de détail supérieur) aura la priorité sur celui de toutes les API.

## <a name="what-data-is-added-to-application-insights"></a>Données ajoutées à Application Insights

Application Insights reçoit :

| Élément de télémétrie | Description |
| -------------- | ----------- |
| *Requête* | Pour chaque demande entrante : <ul><li>*Demande du serveur frontal*</li><li>*Réponse du serveur frontal*</li></ul> |
| *Dépendance* | Pour chaque demande transférée à un service principal : <ul><li>*Demande principale*</li><li>*Réponse principale*</li></ul> |
| *Exception* | Pour chaque demande ayant échoué : <ul><li>A échoué en raison d’une interruption de la connexion cliente.</li><li>A déclenché une section *on-error* des stratégies d’API.</li><li>A un code d’état HTTP de réponse de type 4xx ou 5xx.</li></ul> |
| *Trace* | Si vous configurez une stratégie de [trace](api-management-advanced-policies.md#Trace) . <br /> Le paramètre `severity` de la stratégie `trace` doit être supérieur ou égal au paramètre `verbosity` de journalisation d’Application Insights. |

### <a name="emit-custom-metrics"></a>Émettre des métriques personnalisées
Vous pouvez émettre des métriques personnalisées en configurant la stratégie [`emit-metric`](api-management-advanced-policies.md#emit-metrics). 

Pour rendre les métriques pré-agrégées d’Application Insights disponibles dans Gestion des API, vous devez activer manuellement les métriques personnalisées dans le service.
1. Utilisez la stratégie [`emit-metric`](api-management-advanced-policies.md#emit-metrics) avec l’[API de création ou de mise à jour](https://docs.microsoft.com/rest/api/apimanagement/2021-04-01-preview/api-diagnostic/create-or-update).
1. Ajoutez `"metrics":true` à la charge utile, ainsi que toutes les autres propriétés.

> [!NOTE]
> Consultez [Limites d’Application Insights](../azure-monitor/service-limits.md#application-insights) pour plus d’informations sur la taille maximale et le nombre d’événements et de métriques par instance d’Application Insights.

## <a name="performance-implications-and-log-sampling"></a>Conséquences sur les performances et échantillonnage des journaux

> [!WARNING]
> La journalisation de l’ensemble des événements peut avoir de grosses répercussions sur les performances en fonction du rythme des demandes entrantes.

Lors de tests de charge internes, cette fonctionnalité de journalisation a provoqué une baisse du débit de 40 %-50 % lorsque la fréquence des demandes a dépassé 1 000 par seconde. La fonctionnalité Application Insights est conçue pour évaluer les performances des applications à l’aide d’une analyse statistique. Elle n’est pas :
* conçue pour être un système d’audit.
* adaptée à la journalisation de chaque demande d’API de volume important.

Vous pouvez manipuler le nombre de demandes journalisées en [réglant le paramètre **Échantillonnage**](#enable-application-insights-logging-for-your-api). Une valeur de 100 % signifie que toutes les demandes sont consignées, tandis que le 0 % indique une absence de journalisation. 

L’**échantillonnage** permet de réduire le volume de télémétrie, ce qui permet de prévenir efficacement une dégradation significative des performances tout en conservant les avantages de la journalisation.

Pour améliorer les problèmes de performances, ignorez :
* les en-têtes de demande et les réponses ;
* la journalisation du corps de demande.

## <a name="video"></a>Vidéo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Étapes suivantes

+ Découvrez plus en détail [Azure Application Insights](/azure/application-insights/).
+ Étudiez la possibilité de [journalisation avec Azure Event Hubs](api-management-howto-log-event-hubs.md).
