---
title: Connecter des sources de données à Microsoft Sentinel via Logstash | Microsoft Docs
description: Apprenez à utiliser Logstash pour transférer des journaux provenant de sources de données externes vers Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 1bb2d8c60fd8962363277a7f530db7d84394498e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521811"
---
# <a name="use-logstash-to-connect-data-sources-to-microsoft-sentinel"></a>Utiliser Logstash pour connecter des sources de données à Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> La fonctionnalité d'ingestion de données à l'aide du plug-in de sortie Logstash est actuellement en préversion publique. Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le plug-in de sortie Microsoft Sentinel dédié au **moteur de collecte de données Logstash** permet d’utiliser Logstash pour envoyer tout type de journaux à votre espace de travail Log Analytics dans Microsoft Sentinel. Vos journaux seront envoyés à une table personnalisée que vous définirez à l'aide du plug-in de sortie.

Pour en savoir plus sur l'utilisation du moteur de collecte de données Logstash, consultez [Prise en main de Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Vue d’ensemble

### <a name="architecture-and-background"></a>Architecture et contexte

![Diagramme de l’architecture Logstash.](./media/connect-logstash/logstash-architecture.png)

Le moteur Logstash est constitué de trois composants :

- Plug-ins d’entrée : collection personnalisée de données provenant de sources diverses.
- Plug-ins de filtrage : manipulation et normalisation des données en fonction de critères spécifiés.
- Plug-ins de sortie : envoi personnalisé des données collectées et traitées vers diverses destinations.

> [!NOTE]
> - Microsoft prend en charge uniquement le plug-in de sortie Logstash fourni par Microsoft Sentinel abordé ici. La version actuelle de ce plug-in est v1.0.0, publiée le 25/08/2020. Vous pouvez [ouvrir un ticket de support](https://ms.portal.azure.com/#create/Microsoft.Support) pour tous les problèmes concernant le plug-in de sortie.
>
> - Microsoft ne prend pas en charge les plug-ins de sortie Logstash tiers pour Microsoft Sentinel, ni aucun autre plug-in Logstash de quelque type que ce soit.
>
> - Le plug-in de sortie Logstash de Microsoft Sentinel prend en charge uniquement les **versions 7.0 à 7.15 de Logstash**.

Le plug-in de sortie Microsoft Sentinel pour Logstash envoie des données au format JSON à votre espace de travail Log Analytics, en utilisant l’API REST du collecteur de données HTTP de Log Analytics. Les données sont ingérées dans des journaux personnalisés.

- Apprenez-en davantage sur l’[API REST Log Analytics](/rest/api/loganalytics/create-request).
- Apprenez-en davantage sur les [journaux personnalisés](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="deploy-the-microsoft-sentinel-output-plugin-in-logstash"></a>Déployer le plug-in de sortie Microsoft Sentinel dans Logstash

### <a name="step-1-installation"></a>Étape 1 : Installation

Le plug-in de sortie Microsoft Sentinel est disponible dans la collection Logstash.

- Suivez les instructions du document Logstash [Utilisation des plug-ins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) pour installer le plug-in ***[microsoft-logstash-output-azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)***.
   
- Si votre système Logstash n'a pas accès à Internet, suivez les instructions du document Logstash [Gestion des plug-ins hors connexion](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) pour préparer et utiliser un pack de plug-ins hors connexion. (Il vous faudra pour cela créer un autre système Logstash avec accès à Internet).

### <a name="step-2-configuration"></a>Étape 2 : Configuration

Utilisez les informations du document [Structure d’un fichier de configuration](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) de Logstash et ajoutez le plug-in de sortie Microsoft Sentinel à la configuration avec les clés et valeurs suivantes. (La syntaxe appropriée du fichier de configuration est indiquée après le tableau.)

| Nom du champ | Type de données | Description |
|----------------|---------------|-----------------|
| `workspace_id` | string | Entrez le GUID de l’ID de votre espace de travail (voir les conseils). |
| `workspace_key` | string | Entrez le GUID de la clé primaire de votre espace de travail (voir les conseils). |
| `custom_log_table_name` | string | Définissez le nom de la table dans laquelle les journaux seront ingérés. Vous ne pouvez configurer qu'un seul nom de table par plug-in de sortie. La table des journaux apparaîtra dans Microsoft Sentinel sous **Journaux**, dans **Tables**, catégorie **Journaux personnalisés**, avec le suffixe `_CL`. |
| `endpoint` | string | Champ facultatif. Par défaut, il s'agit du point de terminaison Log Analytics. Utilisez ce champ pour définir un autre point de terminaison. |
| `time_generated_field` | string | Champ facultatif. Cette propriété remplace le champ par défaut **TimeGenerated** dans Log Analytics. Entrez le nom du champ d'horodatage dans la source de données. Les données du champ doivent être conformes au format ISO 8601 (`YYYY-MM-DDThh:mm:ssZ`) |
| `key_names` | tableau | Entrez une liste de champs de schéma de sortie Log Analytics. Chaque élément de la liste doit être placé entre guillemets simples et les éléments séparés par des virgules, et toute la liste doit être placée entre crochets. Voir l'exemple ci-dessous. |
| `plugin_flush_interval` | nombre | Champ facultatif. Permet de définir l'intervalle maximum (en secondes) entre les transmissions de messages à Log Analytics. La valeur par défaut est 5. |
| `amount_resizing` | boolean | True ou false. Activez ou désactivez le mécanisme de mise à l'échelle automatique, qui ajuste la taille de la mémoire tampon des messages en fonction du volume de données de journal reçues. |
| `max_items` | nombre | Champ facultatif. S'applique uniquement si `amount_resizing` est défini sur « false ». Permet de fixer un plafond à la taille de la mémoire tampon des messages (dans les enregistrements). La valeur par défaut est 2000.  |
| `azure_resource_id` | string | Champ facultatif. Définit l’ID de la ressource Azure où résident les données. <br>La valeur de l’ID de ressource est particulièrement utile si vous utilisez un [RBAC dans le contexte de la ressource](resource-context-rbac.md) pour donne accès uniquement à des données spécifiques. |
| | | |

> [!TIP]
> -  Vous pouvez trouver l’ID et la clé primaire de l’espace de travail dans la ressource d’espace de travail, sous **Gestion des agents**.
> - **Toutefois**, étant donné que les informations d’identification et autres informations sensibles stockées en texte clair dans les fichiers de configuration ne sont pas conformes aux meilleures pratiques de sécurité, il est vivement recommandé d’utiliser le **magasin de clés Logstash** pour inclure en toute sécurité l'**ID de l’espace de travail** et la **clé primaire de l’espace de travail** dans la configuration. Pour obtenir des instructions, consultez la [documentation d’Elastic](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

#### <a name="sample-configurations"></a>Exemples de configurations

Voici quelques exemples de configurations qui utilisent des options différentes.

- Configuration de base qui utilise un canal d’entrée filebeat :

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Configuration de base qui utilise un canal d’entrée tcp :

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Configuration avancée :

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Visitez le [dépôt GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) du plug-in de sortie pour en savoir plus sur son fonctionnement interne, sa configuration et ses paramètres de performances.

### <a name="step-3-restart-logstash"></a>Étape 3 : Redémarrer Logstash

### <a name="step-4-view-incoming-logs-in-microsoft-sentinel"></a>Étape 4 : Afficher les journaux entrants dans Microsoft Sentinel

1. Vérifiez que les messages sont envoyés au plug-in de sortie.

1. Dans le menu de navigation de Microsoft Sentinel, cliquez sur **Journaux**. Sous l'en-tête **Tables**, développez la catégorie **Journaux personnalisés**. Recherchez et cliquez sur le nom de la table que vous avez spécifiée (avec le suffixe `_CL`) dans la configuration.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Capture d’écran des journaux personnalisés Logstash.":::

1. Pour afficher les enregistrements de la table, interrogez-la en utilisant son nom comme schéma.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Capture d’écran d’une requête dans les journaux personnalisés Logstash.":::

## <a name="monitor-output-plugin-audit-logs"></a>Surveiller les journaux d'audit du plug-in de sortie

Pour surveiller la connectivité et l’activité du plug-in de sortie Microsoft Sentinel, activez le fichier journal Logstash approprié. Consultez le document [Structure des répertoires Logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) pour connaître l’emplacement du fichier journal.

Si vous ne voyez aucune donnée dans ce fichier journal, générez et envoyez des événements localement (via les plug-ins d'entrée et de filtrage) pour vous assurer que le plug-in de sortie reçoit des données. Microsoft Sentinel prendra uniquement en charge les problèmes liés au plug-in de sortie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser Logstash pour connecter des sources de données externes à Microsoft Sentinel. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Commencez à détecter les menaces avec Microsoft Sentinel à l’aide de règles [intégrées](detect-threats-built-in.md) ou [personnalisées](detect-threats-custom.md).
