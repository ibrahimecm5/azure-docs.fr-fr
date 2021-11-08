---
title: SQL Assessment
description: Identifiez les problèmes de performances et évaluez la configuration de votre machine virtuelle SQL Server pour vous assurer de suivre les meilleures pratiques à l’aide de la fonctionnalité d’évaluation SQL Assessment dans le portail Azure.
author: ebruersan
ms.author: ebrue
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/02/2021
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: 47b74190884f40e3d1e7504133758e35c1fb3ba7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096768"
---
# <a name="sql-assessment-for-sql-server-on-azure-vms-preview"></a>SQL Assessment pour SQL Server sur des machines virtuelles Azure (préversion)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

La fonctionnalité SQL Assessment du portail Azure identifie les problèmes de performances potentiels et évalue votre instance SQL Server sur les machines virtuelles Azure pour s’assurer qu’elle est configurée conformément aux meilleures pratiques à l’aide de [l’ensemble de règles enrichi](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-assessment-api/DefaultRuleset.csv) fourni par [l’API SQL Assessment](/sql/sql-assessment-api/sql-assessment-api-overview). 

La fonctionnalité SQL Assessment est actuellement disponible en préversion.

## <a name="overview"></a>Vue d’ensemble

Une fois la fonctionnalité SQL Assessment activée, votre instance et vos bases de données SQL Server sont analysées pour fournir des recommandations relatives à des éléments, comme les index, les fonctionnalités dépréciées, les indicateurs de trace activés ou manquants, les statistiques, etc. Les recommandations sont exposées à la [page de gestion des machines virtuelles SQL](manage-sql-vm-portal.md) du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 


Les résultats de l’évaluation sont chargés vers votre [espace de travail Log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) à l’aide de [Microsoft Monitoring Agent (MMA)](../../../azure-monitor/agents/log-analytics-agent.md). Si votre machine virtuelle est déjà configurée pour utiliser Log Analytics, la fonctionnalité SQL Assessment utilise la connexion existante.  Dans le cas contraire, l’extension MMA est installée sur la machine virtuelle SQL Server et connectée à l’espace de travail Log Analytics spécifié.

Le temps d’exécution de l’évaluation dépend de votre environnement (nombre de bases de données, objets, etc.), avec une durée de quelques minutes (une heure maximum). De même, la taille du résultat de l’évaluation dépend également de votre environnement. 

## <a name="prerequisites"></a>Prérequis

Pour utiliser la fonctionnalité SQL Assessment, vous devez disposer des prérequis suivants : 

- Votre machine virtuelle SQL Server doit être inscrite auprès de [l’extension SQL Server IaaS en mode complet](sql-agent-extension-manually-register-single-vm.md#full-mode). 
- Un [espace de travail Log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) dans le même abonnement que votre machine virtuelle SQL Server pour charger les résultats de l’évaluation. 


## <a name="enable"></a>Activer

Pour activer les évaluations SQL Assessment, procédez comme suit : 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre [ressource de machine virtuelle SQL Server](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Sélectionnez **SQL Assessment** sous **Paramètres**. 
1. Sélectionnez **Activer SQL Assessment** ou **Configuration** pour accéder à la page **Configuration**. 
1. Cochez la case **Activer SQL Assessment** et fournissez les informations suivantes :
    1. [Espace de travail log Analytics](../../../azure-monitor/logs/quick-create-workspace.md) sur lequel les évaluations sont chargées. Si la machine virtuelle SQL Server n’a pas été associée à un espace de travail précédemment, choisissez un espace de travail existant dans l’abonnement dans la liste déroulante. Dans le cas contraire, l’espace de travail associé précédemment est déjà rempli.  
    1. **Calendrier d’exécution**. Vous pouvez choisir d’exécuter des évaluations à la demande ou automatiquement selon une planification. Si vous choisissez une planification, indiquez la fréquence (hebdomadaire ou mensuelle), le jour de la semaine, la périodicité (toutes les 1 à 6 semaines) et l’heure de la journée à laquelle les évaluations doivent démarrer (heure locale à machine virtuelle). 
1. Sélectionnez **Appliquer** pour enregistrer vos modifications et déployer l’agent Microsoft Monitoring Agent sur votre machine virtuelle SQL Server si elle n’est pas déjà déployée. Une notification du portail Azure vous informe une fois que la fonctionnalité SQL Assessment est prête pour votre machine virtuelle SQL Server. 
    

## <a name="assess-sql-server-vm"></a>Évaluer la machine virtuelle SQL Server

Exécution des évaluations :
- Selon une planification
- À la demande

### <a name="run-scheduled-assessment"></a>Exécuter une évaluation planifiée

Si vous définissez une planification dans le panneau de configuration, une évaluation s’exécute automatiquement à la date et à l’heure spécifiées. Choisissez **Configuration** pour modifier votre calendrier d’évaluation. Une fois que vous avez fourni une nouvelle planification, la planification précédente est remplacée.  

### <a name="run-on-demand-assessment"></a>Exécuter une évaluation à la demande

Une fois la fonctionnalité SQL Assessment activée pour votre machine virtuelle SQL Server, il est possible d’effectuer une évaluation à la demande. Pour ce faire, sélectionnez **Exécuter l’évaluation** dans le panneau SQL Assessment de la page des [ressources de machine virtuelle SQL Server du portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines).


## <a name="view-results"></a>Afficher les résultats

La section **résultats des évaluations** de la page **SQL Assessment** affiche la liste des exécutions de l’évaluation les plus récentes. Chaque ligne affiche l’heure de début d’une exécution et l’état : planifié, en cours d’exécution, chargement des résultats, terminés ou échec. Chaque exécution d’évaluation se compose de deux parties : évaluation de votre instance et chargement des résultats dans votre espace de travail Log Analytics. Le champ d’état couvre les deux parties. Les résultats de l’évaluation s’affichent dans les classeurs Azure. 

Accédez au classeur Azure de résultats de l’évaluation de trois manières : 
- Sélectionnez le **bouton Afficher l’évaluation réussie la plus récente** sur la page **SQL Assessment**.
- Choisissez une exécution terminée dans la section **Résultats de l’évaluation** de la page **SQL Assessment**. 
- Sélectionnez **Afficher les résultats de l’évaluation** dans les **10 premières recommandations** exposées dans la page **Vue d’ensemble** de la page des ressources de votre machine virtuelle SQL. 

Une fois le classeur ouvert, vous pouvez utiliser la liste déroulante pour sélectionner les exécutions précédentes. Vous pouvez afficher les résultats d’une exécution unique à l’aide de la page **Résultats** ou passer en revue les tendances historiques à l’aide de la page **Tendances**.

### <a name="results-page"></a>Page de résultats

La page **Résultats** organise les recommandations à l’aide d’onglets pour *Tout, nouveau, résolu*. Utilisez ces onglets pour afficher toutes les recommandations de l’exécution en cours, toutes les nouvelles recommandations (delta des séries précédentes) ou les recommandations résolues à partir des exécutions précédentes. Les onglets vous aident à suivre la progression entre les exécutions. L’onglet *Insights* identifie les problèmes récurrents et les bases de données présentant le plus grand nombre de problèmes. Utilisez-les pour déterminer les points sur lesquels concentrer vos efforts. 

Les résultats de l’évaluation des groupes de graphiques présentent les niveaux de gravité : élevé, moyen et faible. Sélectionnez chaque catégorie pour afficher la liste des recommandations ou recherchez des expressions clés dans la zone de recherche. Il est préférable de commencer par les recommandations les plus graves et de descendre dans la liste.

Triez par **Nom** dans la table pour afficher les recommandations regroupées pour une base de données ou une instance particulière. Utilisez la zone de recherche pour afficher certains types de recommandations en fonction de la valeur d’étiquette ou d’une phrase clé, comme performance. Utilisez la flèche vers le bas située en haut à droite du tableau pour exporter les résultats dans un fichier Excel. 

La section **Réussi** du graphique identifie les recommandations que votre système applique déjà. 

Affichez des informations détaillées pour chaque recommandation en sélectionnant le champ **Message**, par exemple une description longue, ainsi que les ressources en ligne pertinentes. 
 
### <a name="trends-page"></a>Page Tendances

Vous trouverez trois graphiques dans la page **Tendances** pour afficher les modifications dans le temps : ensemble des problèmes, nouveaux problèmes et problèmes résolus. Les graphiques vous aident à observer votre progression. Dans l’idéal, le nombre de recommandations doit diminuer, tandis que le nombre de problèmes résolus doit augmenter. La légende indique le nombre moyen de problèmes pour chaque niveau de gravité. Pointez sur les barres pour afficher les valeurs individuelles de chaque exécution. 

Si plusieurs exécutions sont effectuées en une seule journée, seule la dernière exécution est incluse dans les graphiques de la page **Tendances**. 

## <a name="known-issues"></a>Problèmes connus

Vous pouvez rencontrer certains des problèmes connus suivants lors de l’utilisation des évaluations SQL Assessment. 

### <a name="deployment-failure-for-enable-or-run-assessment"></a>Échec du déploiement pour Activer ou Exécuter l’évaluation 

Reportez-vous à [l’historique de déploiement](../../../azure-resource-manager/templates/deployment-history.md) du groupe de ressources qui contient la machine virtuelle SQL pour afficher le message d’erreur associé à l’action qui a échoué. 
 
### <a name="failed-assessments"></a>Échecs d’évaluation 

**Échec de l’exécution de l’évaluation** Cela indique que l’extension IaaS SQL a rencontré un problème lors de l’exécution de l’évaluation. Le message d’erreur détaillé est disponible dans le journal d’extension à l’intérieur de la machine virtuelle à l’emplacement `C:\WindowsAzure\Logs\Plugins\Microsoft.SqlServer.Management.SqlIaaSAgent\2.0.X.Y` où `2.0.X.Y ` est le dossier de la version la plus récente.  

**Échec du chargement du résultat dans l’espace de travail Log Analytics** Cela indique que l’agent Microsoft Monitoring Agent (MMA) n’a pas pu charger les résultats dans le délai spécifié. Assurez-vous que l’extension MMA est [correctement provisionnée](../../../azure-monitor/visualize/vmext-troubleshoot.md) et reportez-vous au [Guide de résolution des problèmes](../../../azure-monitor/agents/agent-windows-troubleshoot.md) pour MMA afin d’identifier le « Problème lié aux journaux personnalisés » dans le guide. 

>[!TIP]
>Si vous avez appliqué TLS 1.0 ou une version ultérieure dans Windows et désactivé les anciens protocoles SSL comme décrit [ici](/troubleshoot/windows-server/windows-security/restrict-cryptographic-algorithms-protocols-schannel#schannel-specific-registry-keys), vous devez également vous assurer que .NET Framework est [configuré](../../../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12) pour utiliser un chiffrement fort. 

**Le résultat a expiré en raison de la rétention des données de l’espace de travail log Analytics** Cela indique que les résultats ne sont plus conservés dans l’espace de travail Log Analytics en fonction de sa stratégie de rétention. Vous pouvez [modifier la période de rétention](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) de l’espace de travail

## <a name="next-steps"></a>Étapes suivantes

- Pour enregistrer votre machine virtuelle SQL Server avec l’extension IaaS SQL Server dans SQL Server sur des machines virtuelles Azure, consultez les articles concernant [l’installation automatique](sql-agent-extension-automatic-registration-all-vms.md), les [machines virtuelles uniques](sql-agent-extension-manually-register-single-vm.md) ou les [Machines virtuelles en bloc](sql-agent-extension-manually-register-vms-bulk.md).
- Pour découvrir plus de fonctionnalités disponibles pour l’extension IaaS SQL Server dans SQL Server sur des machines virtuelles Azure, consulter [Gérer des machines virtuelles SQL Server à partir du portail Azure](manage-sql-vm-portal.md)
