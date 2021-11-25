---
title: Bonnes pratiques pour la collecte de données dans Microsoft Sentinel
description: Découvrez les bonnes pratiques à suivre lors de la connexion de sources de données à Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 17561127c040fc1aedac771093e0bfa6366c4d30
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524775"
---
#  <a name="data-collection-best-practices"></a>Meilleures pratiques de collecte de données

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cette section passe en revue les bonnes pratiques pour la collecte de données avec des connecteurs de données Microsoft Sentinel. Pour plus d’informations, consultez [Connecter les sources de données](connect-data-sources.md),[ Informations de référence sur les connecteurs de données Microsoft Sentinel](data-connectors-reference.md) et le [catalogue de solutions Microsoft Sentinel](sentinel-solutions-catalog.md).

## <a name="prioritize-your-data-connectors"></a>Hiérarchiser vos connecteurs de données

Si vous n’avez pas une idée précise des connecteurs de données qui serviront le mieux votre environnement, commencez par activer tous les [connecteurs de données gratuits](azure-sentinel-billing.md#free-data-sources).

Les connecteurs de données gratuits vous permettent de tirer rapidement parti de Microsoft Sentinel tout en planifiant d’autres connecteurs de données et budgets.

Pour vos connecteurs de données de [partenaires](data-connectors-reference.md) et [personnalisés](create-custom-connector.md), commencez par configurer les connecteurs [Syslog](connect-syslog.md) et [CEF](connect-common-event-format.md), en privilégiant ceux présentant la priorité la plus élevée, ainsi que tous les appareils Linux.

Si votre ingestion de données devient trop coûteuse trop vite, arrêtez ou filtrez les journaux transférés avec l’[Agent Azure Monitor](../azure-monitor/agents/azure-monitor-agent-overview.md).

> [!TIP]
> Les connecteurs de données personnalisés vous permettent d’ingérer des données dans Microsoft Sentinel à partir de sources de données non prises en charge par les fonctionnalités intégrées, par exemple via l’agent, Logstash ou l’API. Pour plus d’informations, consultez [Ressources pour la création de connecteurs Microsoft Sentinel personnalisés](create-custom-connector.md).
>

## <a name="filter-your-logs-before-ingestion"></a>Filtrer vos journaux avant l’ingestion

Vous souhaiterez peut-être filtrer les journaux collectés, ou même le contenu des journaux, avant que les données ne soient ingérées dans Microsoft Sentinel. Par exemple, vous pouvez filtrer les journaux qui ne sont pas pertinents ou importants pour les opérations de sécurité, ou vous pouvez supprimer les détails indésirables des messages de journal. Le filtrage du contenu des messages peut également être utile pour réduire les coûts liés à l’utilisation de journaux Syslog, CEF ou Windows qui présentent de nombreux détails non pertinents.

Filtrez vos journaux en utilisant l’une des méthodes suivantes :

- **Agent Azure Monitor**. Pris en charge sur Windows et Linux pour ingérer les [événements de sécurité Windows](connect-windows-security-events.md). Filtrez les journaux collectés en configurant l’agent de façon à collecter uniquement les événements spécifiés.

- **Logstash**. Prend en charge le filtrage du contenu des messages, y compris l’apport de modifications aux messages de journal. Pour plus d’informations, consultez [Se connecter avec Logstash](create-custom-connector.md#connect-with-logstash).

> [!IMPORTANT]
> L’utilisation de Logstash pour filtrer le contenu de vos messages entraîne l’ingestion de vos journaux en tant que journaux personnalisés, ce qui amène les journaux de [niveau gratuit](azure-sentinel-billing.md#free-data-sources) à devenir des journaux de niveau payant.
>
> Les journaux personnalisés doivent également être utilisés dans les [règles d’analytique](automate-incident-handling-with-automation-rules.md), la [chasse aux menaces](hunting.md) et les [workbooks](get-visibility.md), car ils ne sont pas ajoutés automatiquement. Les journaux personnalisés ne sont pas non plus pris en charge pour les fonctionnalités de [Machine Learning](bring-your-own-ml.md).
>

## <a name="alternative-data-ingestion-requirements"></a>Autres besoins en ingestion de données

La configuration standard de la collecte de données peut ne pas fonctionner correctement pour votre organisation, en raison de différents obstacles. Les tableaux suivants décrivent les obstacles ou besoins courants ainsi que les solutions possibles et les considérations.

> [!NOTE]
> De nombreuses solutions listées ci-dessous nécessitent un connecteur de données personnalisé. Pour plus d’informations, consultez [Ressources pour la création de connecteurs Microsoft Sentinel personnalisés](create-custom-connector.md).
>

### <a name="on-premises-windows-log-collection"></a>Collecte de journaux Windows locale


|Obstacle/besoin  |Solutions possibles  |Considérations  |
|---------|---------|---------|
|**Besoin d’un filtrage de journal**     | Utiliser Logstash <br><br>Utiliser Azure Functions <br><br> Utiliser LogicApps <br><br> Utiliser du code personnalisé (.NET, Python)  |  Même si le filtrage peut entraîner une réduction des coûts et ingère uniquement les données requises, certaines fonctionnalités Microsoft Sentinel ne sont pas prises en charge, telles que l’[analytique du comportement des entités et des utilisateurs (UEBA)](identify-threats-with-entity-behavior-analytics.md), les [pages d’entité](identify-threats-with-entity-behavior-analytics.md#entity-pages), le [Machine Learning](bring-your-own-ml.md) et la [fusion](fusion.md). <br><br>Lors de la configuration du filtrage de journal, vous devez effectuer des mises à jour dans les ressources telles que les requêtes de chasse aux menaces et les règles d’analytique.     |
|**L’agent ne peut pas être installé**     |Utiliser WEF (Windows Event Forwarding), pris en charge avec l’[agent Azure Monitor](connect-windows-security-events.md#connector-options)       |   L’utilisation de WEF réduit le nombre d’événements d’équilibrage de charge par seconde du collecteur d’événements Windows de 10 000 unités à 500 à 1000.|
|**Les serveurs ne se connectent pas à Internet**     | Utiliser la [passerelle Log Analytics](../azure-monitor/agents/gateway.md)        | La configuration d’un proxy sur votre agent nécessite des règles de pare-feu supplémentaires pour permettre à la passerelle de fonctionner.        |
|**Besoin d’un étiquetage et d’un enrichissement à l’ingestion**     |Utiliser Logstash pour injecter un ID de ressource <br><br>Utiliser un modèle ARM pour injecter l’ID de ressource dans des machines locales <br><br>Ingérer l’ID de ressource dans des espaces de travail distincts        | Log Analytics ne prend pas en charge le contrôle d’accès en fonction du rôle pour les tables personnalisées. <br><br>Microsoft Sentinel ne prend pas en charge le contrôle d’accès en fonction du rôle au niveau des lignes. <br><br>**Conseil** : Vous souhaiterez peut-être adopter la conception et les fonctionnalités inter-espace de travail pour Microsoft Sentinel.        |
|**Besoin de la division des journaux d’opérations et de sécurité**     | Utiliser la fonctionnalité de multi-hébergement de l’[agent Microsoft Monitor ou l’agent Azure Monitor](connect-windows-security-events.md)        |  La fonctionnalité de multi-hébergement nécessite davantage de charge de déploiement pour l’agent.       |
|**Besoin de journaux personnalisés**     |   Collecter des fichiers à partir de chemins de dossier spécifiques <br><br>Utiliser l’ingestion d’API <br><br>Utiliser PowerShell <br><br>Utiliser Logstash     |   Vous avez peut-être des problèmes de filtrage de vos journaux. <br><br>Les méthodes personnalisées ne sont pas prises en charge. <br><br>Les connecteurs personnalisés peuvent demander des compétences en développement.       |
| | | |

### <a name="on-premises-linux-log-collection"></a>Collecte de journaux Linux locale

|Obstacle/besoin  |Solutions possibles  |Considérations  |
|---------|---------|---------|
|**Besoin d’un filtrage de journal**     | Utiliser Syslog-NG <br><br>Utiliser Rsyslog <br><br>Utiliser la configuration FluentD pour l’agent <br><br> Utiliser l’agent Azure Monitor/Microsoft Monitoring Agent <br><br> Utiliser Logstash  |  Certaines distributions Linux peuvent ne pas être prises en charge par l’agent. <br> <br>L’utilisation de Syslog ou FluentD nécessite des connaissances en développement. <br><br>Pour plus d’informations, consultez [Se connecter aux serveurs Windows pour collecter des événements de sécurité](connect-windows-security-events.md) et [Ressources pour la création de connecteurs Microsoft Sentinel personnalisés](create-custom-connector.md).      |
|**L’agent ne peut pas être installé**     |  Utiliser un redirecteur Syslog, par exemple (syslog-ng ou rsyslog)       |         |
|**Les serveurs ne se connectent pas à Internet**       | Utiliser la [passerelle Log Analytics](../azure-monitor/agents/gateway.md)        | La configuration d’un proxy sur votre agent nécessite des règles de pare-feu supplémentaires pour permettre à la passerelle de fonctionner.          |
|**Besoin d’un étiquetage et d’un enrichissement à l’ingestion**      | Utiliser Logstash pour l’enrichissement ou des méthodes personnalisées, telles que l’API ou EventHubs         | Vous avez peut-être besoin d’effectuer un effort supplémentaire pour le filtrage.    |
|**Besoin de la division des journaux d’opérations et de sécurité**     |  Utiliser l’[agent Azure Monitor](connect-windows-security-events.md) avec la configuration d’hébergement multiple       |         |
|**Besoin de journaux personnalisés**     |    Créer un collecteur personnalisé en utilisant l’agent Microsoft Monitoring (Log Analytics)      |      |
| | | |

### <a name="endpoint-solutions"></a>Solutions de point de terminaison

Si vous devez collecter des journaux à partir de solutions de point de terminaison, telles que la PEPT, d’autres événements de sécurité, Sysmon, etc., utilisez l’une des méthodes suivantes :

- **Connecteur MTP** pour collecter les journaux à partir de Microsoft 365 Defender pour point de terminaison. Cette option entraîne des coûts supplémentaires pour l’ingestion des données.
- **WEF (Windows Event Forwarding)** .

> [!NOTE]
> L’équilibrage de charge diminue le nombre d’événements par seconde qui peuvent être traités dans l’espace de travail.
>

### <a name="office-data"></a>Données Office

Si vous devez collecter des données Microsoft Office, en dehors des données du connecteur standard, utilisez l’une des solutions suivantes :

|Obstacle/besoin  |Solutions possibles  |Considérations  |
|---------|---------|---------|
|**Collecter des données brutes à partir de Teams, la trace des messages, les données de phishing, etc.**     |    Utiliser la fonctionnalité intégrée de [connecteur Office 365](./data-connectors-reference.md#microsoft-office-365), puis créer un connecteur personnalisé pour d’autres données brutes  |  Le mappage des événements avec l’ID d’enregistrement correspondant peut être difficile.  |
|**Besoin du contrôle d’accès en fonction du rôle pour diviser les pays, les services, etc.**     | Personnaliser la collecte de données en ajoutant des étiquettes aux données et en créant des espaces de travail dédiés pour chaque séparation nécessaire|   La collecte de données personnalisée présente des coûts d’ingestion supplémentaires.     |
|**Besoin de plusieurs locataires dans un seul espace de travail**     |  Personnaliser la collecte de données en utilisant Azure LightHouse et une vue centralisée des incidents|  La collecte de données personnalisée présente des coûts d’ingestion supplémentaires.  <br><br>Pour plus d’informations, consultez [Étendre Microsoft Sentinel dans les espaces de travail et les locataires](extend-sentinel-across-workspaces-tenants.md).      |
| | | |

### <a name="cloud-platform-data"></a>Données de plateforme cloud

|Obstacle/besoin  |Solutions possibles  |Considérations  |
|---------|---------|---------|
|**Filtrer les journaux à partir d’autres plateformes**     | Utiliser Logstash <br><br>Utiliser l’agent Azure Monitor/l’agent Microsoft Monitoring (Log Analytics)       |    La collecte personnalisée présente des coûts d’ingestion supplémentaires. <br><br>Vous pouvez être amené à collecter tous les événements Windows ou uniquement les événements de sécurité.     |
|**L’agent est inutilisable**     |   Utiliser WEF (Windows Event Forwarding)      | Vous devrez peut-être équilibrer la charge des efforts entre vos ressources.        |
|**Les serveurs sont sur un réseau hermétique**     | Utiliser la [passerelle Log Analytics](../azure-monitor/agents/gateway.md)        | La configuration d’un proxy sur votre agent nécessite des règles de pare-feu pour permettre à la passerelle de fonctionner.        |
|**Contrôle d’accès en fonction du rôle, étiquetage et enrichissement à l’ingestion**     |    Créer une collection personnalisée via Logstash ou l’API Log Analytics     |  Le contrôle d’accès en fonction du rôle n’est pas pris en charge pour les tables personnalisées. <br><br>Le contrôle d’accès en fonction du rôle au niveau des lignes n’est pris en charge pour aucune table.       |
|     |         |         |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Activités de prédéploiement et prérequis pour le déploiement de Microsoft Sentinel](prerequisites.md)
- [Meilleures pratiques pour Microsoft Sentinel](best-practices.md)
- [Connecter des sources de données](connect-data-sources.md)
