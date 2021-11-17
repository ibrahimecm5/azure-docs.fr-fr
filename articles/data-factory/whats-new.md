---
title: Nouveautés d’Azure Data Factory
description: Cette page présente les nouvelles fonctionnalités et les améliorations récentes apportées dans Azure Data Factory. Azure Data Factory est un service cloud managé conçu pour des projets hybrides complexes ETL (extraction, transformation et chargement), ELT (extraction, chargement et transformation) et d’intégration de données.
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fdec259dc93e52a8bc1271de8af90f4b864afd49
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842052"
---
# <a name="whats-new-in-azure-data-factory"></a>Nouveautés d’Azure Data Factory

Le service Azure Data Factory fait l’objet d’améliorations continues. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. 

## <a name="september-2021"></a>Septembre 2021
<br>
<table>
<tr><td><b>Catégorie de service</b></td><td><b>Améliorations du service</b></td><td><b>Détails</b></td></tr>
  <tr><td><b>Intégration continue et livraison continue (CI/CD)</b></td><td>Fonctionnalités de CI/CD étendues</td><td>Vous pouvez maintenant créer une branche Git basée sur n’importe quelle autre branche dans Azure Data Factory.<br><a href="source-control.md#version-control">En savoir plus</a></td></tr>
<tr><td rowspan=3><b>Déplacement des données</b></td><td>Sources Amazon Relational Database Service (RDS) pour Oracle</td><td>Le connecteur de sources Amazon RDS pour Oracle est maintenant disponible dans Azure Data Factory et Azure Synapse.<br><a href="connector-amazon-rds-for-oracle.md">En savoir plus</a></td></tr>
<tr><td>Sources Amazon RDS pour SQL Server</td><td>Le connecteur de sources Amazon RDS pour SQL Server est maintenant disponible dans Azure Data Factory et Azure Synapse.<br><a href="connector-amazon-rds-for-sql-server.md">En savoir plus</a></td></tr>
<tr><td>Prise en charge de la copie parallèle à partir d’Azure Database pour PostgreSQL</td><td>Le connecteur Azure Database pour PostgreSQL prend maintenant en charge les opérations de copie parallèle.<br><a href="connector-azure-database-for-postgresql.md">En savoir plus</a></td></tr>
<tr><td rowspan=3><b>Flux de données</b></td><td>Utilisation d’Azure Data Lake Storage (ADLS) Gen2 pour exécuter des commandes de prétraitement et de post-traitement</td><td>Les commandes de prétraitement et de post-traitement HDFS (Hadoop Distributed File System) peuvent maintenant être exécutées en utilisant des récepteurs ADLS Gen2 dans les flux de données<br><a href="connector-azure-data-lake-storage.md#pre-processing-and-post-processing-commands">En savoir plus</a></td></tr>
<tr><td>Modification des propriétés de flux de données pour les instances existantes d’Azure Integration Runtime (IR)</td><td>Azure Integration Runtime (IR) a été mis à jour afin de permettre la modification des propriétés de flux de données pour les instances d’IR existantes. Vous pouvez maintenant modifier les propriétés de calcul de flux de données sans avoir à créer une nouvelle instance d’Azure IR.<br><a href="concepts-integration-runtime.md">En savoir plus</a></td></tr>
<tr><td>Paramètre TTL pour Azure Synapse qui accélère le temps de démarrage de l’exécution des activités de pipeline</td><td>Azure Synapse Analytics a ajouté le paramètre TTL à Azure Integration Runtime pour que les activités de pipeline de flux de données commencent à s’exécuter en quelques secondes, ce qui réduit de manière considérable le runtime de vos pipelines de flux de données.<br><a href="control-flow-execute-data-flow-activity.md#data-flow-integration-runtime">En savoir plus</a></td></tr>
<tr><td><b>Runtime d’intégration</b></td><td>Disponibilité générale du VNET managé Azure Data Factory</td><td>Vous pouvez provisionner Azure Integration Runtime dans un réseau virtuel (VNET) managé et utiliser des points de terminaison privés pour établir des connexions sécurisées aux magasins de données pris en charge. Le trafic de données passe alors par des liaisons privées Azure Private Link qui fournissent une connectivité sécurisée à la source de données. En outre, cela empêche l’exfiltration de données vers l’Internet public.<br><a href="managed-virtual-network-private-endpoint.md">En savoir plus</a></td></tr>
<tr><td rowspan=2><b>Orchestration</b></td><td>Rendre les pipelines de données opérationnels et fournir un Contrat de niveau de service (SLA) pour ces pipelines</td><td>Avec la nouvelle métrique Exécution de pipeline - Temps écoulé, combinée aux alertes Data Factory, les développeurs de pipelines de données peuvent améliorer les contrats SLA fournis à leurs clients ; vous nous indiquez la durée d’exécution souhaitée pour un pipeline et nous vous avertissons de manière proactive si le pipeline s’exécute plus longtemps que prévu.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/operationalize-and-provide-sla-for-data-pipelines/ba-p/2767768">En savoir plus</a></td></tr>
<tr><td>Activité Échec (préversion publique)</td><td>La nouvelle activité Échec vous permet de lever intentionnellement une erreur dans un pipeline pour une raison quelconque. Par exemple, vous pouvez utiliser l’activité Échec si une activité de recherche ne retourne aucune donnée correspondante ou si une activité personnalisée se termine par une erreur interne.<br><a href="control-flow-fail-activity.md">En savoir plus</a></td></tr>
</table>

## <a name="august-2021"></a>Août 2021
<br>
<table>
<tr><td><b>Catégorie de service</b></td><td><b>Améliorations du service</b></td><td><b>Détails</b></td></tr>
  <tr><td><b>Intégration continue et livraison continue (CI/CD)</b></td><td>Améliorations apportées au processus CI/CD avec prise en charge de GitHub dans Azure Government et Azure Chine</td><td>Nous avons ajouté la prise en charge de GitHub dans Azure pour le Gouvernement des États-Unis et Azure Chine.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">En savoir plus</a></td></tr>
<tr><td rowspan=2><b>Déplacement des données</b></td><td>Le connecteur de l’API Azure Cosmos DB pour MongoDB prend en charge les versions 3.6 et 4.0 dans Azure Data Factory</td><td>Le connecteur de l’API d’Azure Data Factory Cosmos DB pour MongoDB pour MongoDB prend désormais en charge les versions 3.6 et 4.0 du serveur.<br><a href="connector-azure-cosmos-db-mongodb-api.md">En savoir plus</a></td></tr>
<tr><td>Apporter des améliorations à l’aide de l’instruction COPY pour charger des données dans Azure Synapse Analytics</td><td>Le connecteur Azure Synapse Analytics d’Azure Data Factory prend désormais en charge la copie intermédiaire et la source de copie avec *.* comme wildcardFilename pour l’instruction COPY.<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">En savoir plus</a></td></tr>
<tr><td><b>Flux de données</b></td><td>Les points de terminaison REST sont disponibles comme source et récepteur dans Data Flow</td><td>Dans Azure Data Factory et Azure Synapse Analytics, les flux de données prennent désormais en charge les points de terminaison REST à la fois comme source et comme récepteur avec une prise en charge complète des charges utiles JSON et XML.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">En savoir plus</a></td></tr>
<tr><td><b>Runtime d’intégration</b></td><td>L’Outil de diagnostic est disponible pour le runtime d’intégration auto-hébergé</td><td>Un outil de diagnostic pour le runtime d’intégration auto-hébergé est conçu pour fournir une meilleure expérience utilisateur et permettre aux utilisateurs de détecter des problèmes potentiels. L’outil exécute une série de scénarios de test sur l’ordinateur du runtime d’intégration auto-hébergé et chaque scénario a des cas de contrôle d’intégrité typiques pour les problèmes courants.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">En savoir plus</a></td></tr>
<tr><td><b>Orchestration</b></td><td>Le déclencheur d’événement personnalisé avec l’option de filtrage avancé est en disponibilité générale</td><td>Vous pouvez maintenant créer un déclencheur qui répond à une rubrique personnalisée publiée dans Event Grid. De plus, vous pouvez tirer parti du filtrage avancé pour bénéficier d’un contrôle précis sur les événements auxquels répondre.<br><a href="how-to-create-custom-event-trigger.md">En savoir plus</a></td></tr>
</table>

## <a name="july-2021"></a>Juillet 2021
<br>
<table>
<tr><td><b>Catégorie de service</b></td><td><b>Améliorations du service</b></td><td><b>Détails</b></td></tr>
<tr><td><b>Déplacement des données</b></td><td>Obtenir des pipelines d’ingestion de données pilotées par les métadonnées sur l’outil Copier des données ADF en 10 minutes (préversion publique)</td><td>Avec cet outil, vous pouvez générer des pipelines de copie de données à grande échelle avec une approche pilotée par les métadonnées sur l’outil Copier des données (préversion publique) dans un délai de 10 minutes.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">En savoir plus</a></td></tr>
<tr><td><b>Flux de données</b></td><td>Nouvelles fonctions de mappage ajoutées dans les fonctions de transformation de flux de données</td><td>Un nouvel ensemble de fonctions de transformation de flux de données a été ajouté pour permettre aux ingénieurs de données de générer, de lire et de mettre à jour facilement des types de données de mappage et des structures de mappage complexes.<br><a href="data-flow-expression-functions.md#map-functions">En savoir plus</a></td></tr>
<tr><td><b>Runtime d’intégration</b></td><td>5 nouvelles régions disponibles dans le réseau virtuel (VNET) managé Azure Data Factory (préversion publique)</td><td>Ces 5 nouvelles régions (Chine Est2, Chine Nord2, US Gov Arizona, US Gov Texas, US Gov Virginie) sont disponibles dans le réseau virtuel managé Azure Data Factory (préversion publique).<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">En savoir plus</a></td></tr>
<tr><td rowspan=2><b>Productivité des développeurs</b></td><td>Améliorations de la page d’accueil ADF</td><td>La page d’accueil Data Factory a été repensée avec de meilleures capacités de contraste et de redisposition. De plus, quelques sections ont été introduites dans la page d’accueil pour vous permettre d’améliorer la productivité de votre parcours d’intégration des données.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">En savoir plus</a></td></tr>
<tr><td>Nouvelle page de destination pour Azure Data Factory Studio</td><td>Page de destination du panneau Data Factory dans le portail Azure.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">En savoir plus</a></td></tr>
</table>

## <a name="june-2021"></a>Juin 2021
<br>
<table>
<tr><td><b>Catégorie de service</b></td><td><b>Améliorations du service</b></td><td><b>Détails</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>Déplacement des données</b></td><td>Nouvelle expérience utilisateur avec l’outil Copier des données d’Azure Data Factory</td><td>L’outil remanié Copier des données est désormais disponible avec une expérience d’ingestion des données améliorée.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">En savoir plus</a></td></tr>
<tr><td>MongoDB et MongoDB Atlas sont pris en charge à la fois comme source et récepteur</td><td>Cette amélioration prend en charge la copie de données entre tous les magasins de données pris en charge et les bases de données MongoDB ou MongoDB Atlas.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">En savoir plus</a></td></tr>
<tr><td>Always Encrypted est pris en charge pour les connecteurs Azure SQL Database, Azure SQL Managed Instance et SQL Server en tant que source et récepteur</td><td>Always Encrypted est disponible dans Azure Data Factory pour les connecteurs Azure SQL Database, Azure SQL Managed Instance et SQL Server pour l’activité de copie.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">En savoir plus</a></td></tr>
<tr><td>La définition des métadonnées personnalisées est prise en charge dans l’activité de copie lors de la réception sur ADLS Gen2 ou un objet blob Azure</td><td>Lors de l’écriture dans ADLS Gen2 ou un objet blob Azure, l’activité de copie prend en charge la définition de métadonnées personnalisées ou le stockage des informations sur les dernières modifications du fichier source en tant que métadonnées.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">En savoir plus</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>Flux de données</b></td><td>SQL Server est désormais pris en charge en tant que source et récepteur dans les flux de données</td><td>SQL Server est désormais pris en charge en tant que source et récepteur dans les flux de données. Suivez le lien pour obtenir des instructions sur la configuration de votre mise en réseau à l’aide de la fonctionnalité de VNET managé Azure Integration Runtime en vue de communiquer avec vos instances SQL Server locales et cloud basées sur une machine virtuelle.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">En savoir plus</a></td></tr>
<tr><td>La réutilisation rapide du cluster de flux de données est désormais activée par défaut pour tous les nouveaux Azure Integration Runtime</td><td>ADF est heureux d’annoncer la disponibilité générale de la fonctionnalité populaire de réutilisation rapide du flux de données. La réutilisation rapide est désormais activée par défaut pour tous les nouveaux Azure Integration Runtime.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">En savoir plus</a></td></tr>
<tr><td>Activité Power Query (préversion publique)</td><td>Vous pouvez désormais générer des mappages de champs complexes sur votre récepteur Power Query à l’aide du data wrangling dans Azure Data Factory. Le récepteur est maintenant configuré dans le pipeline dans l’activité Power Query (préversion publique) pour s’adapter à cette mise à jour.<br><a href="wrangling-tutorial.md">En savoir plus</a></td></tr>
<tr><td>Interface de supervision des flux de données mise à jour dans Azure Data Factory</td><td>Azure Data Factory dispose d’une nouvelle mise à jour de l’interface utilisateur de supervision pour faciliter l’affichage de vos exécutions de travaux ETL de flux de données et identifier rapidement les zones pour le réglage des performances.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">En savoir plus</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>Exécutez n’importe quelle requête SQL en 3 étapes simples à l’aide du service SSIS dans Azure Data Factory</td><td>Ce billet indique 3 étapes simples pour exécuter des instructions/scripts SQL n’importe où avec le service SSIS dans Azure Data Factory.<ol><li>Préparez votre runtime d’intégration auto-hébergé/SSIS Integration Runtime.</li><li>Préparez une activité Exécuter le package SSIS dans le pipeline Azure Data Factory.</li><li>Exécutez l’activité Exécuter le package SSIS sur runtime d’intégration auto-hébergé/SSIS Integration Runtime.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">En savoir plus</a></td></tr>
</table>

## <a name="more-information"></a>Autres informations

- [Blog – Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [Vidéos](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)
