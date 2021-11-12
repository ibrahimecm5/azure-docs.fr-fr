---
title: Télécharger des données de facturation dans Azure et les afficher dans le portail Azure
description: Télécharger des données de facturation dans Azure et les afficher dans le portail Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: b54d77c5abb5e8043368ad1e8bce3d3865e68fd7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558759"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Télécharger des données de facturation dans Azure et les afficher dans le portail Azure




## <a name="connectivity-modes---implications-for-billing-data"></a>Modes de connectivité – Implications pour les données de facturation

Il existe deux modes dans lesquels vous pouvez déployer vos services de données Azure Arc :

- **Connecté indirectement** – Il n’existe pas de connexion directe à Azure. Les données sont envoyées à Azure uniquement via un processus d’exportation/chargement.
- **Connecté directement** – Dans ce mode, il y aura une dépendance du service Kubernetes avec Azure Arc pour fournir une connexion directe entre Azure et le cluster Kubernetes sur lequel les services de données avec Azure Arc sont déployés. Cela vous permet d’obtenir davantage de capacités d’Azure ainsi que d’utiliser le portail Azure pour gérer vos services de données avec Azure Arc de la même manière que vous gérez vos services de données dans Azure PaaS.  

Si vous le souhaitez, vous pouvez en savoir plus sur la différence entre les [modes de connectivité](./connectivity.md).

Dans le mode connecté indirectement, les données de facturation sont régulièrement exportées du contrôleur de données Azure Arc vers un fichier sécurisé, puis chargées dans Azure et traitées.  Dans le mode connecté directement à venir, les données de facturation seront automatiquement envoyées à Azure environ 1 fois par heure pour fournir une vue en quasi-temps réel des coûts de vos services. Le processus d’exportation et de chargement des données dans le mode connecté indirectement peut également être automatisé à l’aide de scripts, ou nous pouvons créer un service qui le fera pour vous.

## <a name="upload-billing-data-to-azure---indirectly-connected-mode"></a>Charger des données de facturation sur Azure - Mode connexion indirecte

> [!NOTE]
> Le chargement des données d’utilisation (facturation) s’effectue automatiquement en mode connexion directe. Les instructions suivantes ne sont valables que pour le mode connexion indirecte. 

Pour charger des données de facturation dans Azure, les actions suivantes doivent avoir lieu au préalable :

1. Créez un service de données avec Azure Arc si vous n’en avez pas encore. Par exemple, créez l’un des éléments suivants :
   - [Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md)
   - [Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)
2. Attendez au moins 2 heures à partir de la création du service de données afin que le processus de collecte de la télémétrie de facturation puisse collecter des données de facturation.
3. Suivez les étapes décrites dans [Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) pour obtenir le programme d’installation avec les conditions préalables requises pour le chargement des données d’utilisation/de facturation/de journal, puis passez à [Charger des données d’utilisation sur Azure](upload-usage-data.md) pour charger les données de facturation. 


## <a name="view-billing-data-in-azure-portal"></a>Afficher les données de facturation dans le portail Azure

Pour afficher les données de facturation dans le portail Azure, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Dans la zone de recherche en haut du type d’écran, tapez **Cost Management**, puis cliquez sur le service Cost Management.
1. Sous **Vue d’ensemble de la gestion des coûts**, cliquez sur l’onglet **Gestion des coûts**.
1. Cliquez sur l’onglet **Analyse des coûts** à gauche.
1. Cliquez sur le bouton **Coût par ressource** en haut de la vue.
1. Assurez-vous que l’Étendue est définie sur l’abonnement dans lequel vos ressources de service de données ont été créées.
1. Sélectionnez **Coût par ressource** dans la liste déroulante Afficher en regard du sélecteur Étendue en haut de la vue.
1. Assurez-vous que le filtre de date est défini sur **Mois en cours** ou sur un autre intervalle de temps semblant logique compte tenu de la date de création des ressources de votre service de données.
1. Si vous souhaitez filtrer sur un seul type de service de données avec Azure Arc, cliquez sur **Ajouter un filtre** afin d’ajouter un filtre par **Type de ressource** = `Microsoft.AzureArcData/<data service type>`.
1. Vous voyez maintenant une liste de toutes les ressources qui ont été créées et chargées dans Azure. Étant donné que le compteur de facturation indique $0, le coût affiché sera toujours $0.

## <a name="download-billing-data"></a>Télécharger les données de facturation

Vous pouvez télécharger les données de synthèse de facturation directement à partir du portail Azure.

1. Dans la vue **Analyse des coûts -> Afficher par type de ressource** que vous avez atteinte en suivant les instructions ci-dessus, cliquez sur le bouton Télécharger en haut.
1. Choisissez le type de fichier à télécharger (Excel ou CSV), puis cliquez sur le bouton **Télécharger des données**.
1. Ouvrez le fichier dans un éditeur approprié en fonction du type de fichier sélectionné.

## <a name="export-billing-data"></a>Exporter les données de facturation

Vous pouvez également exporter automatiquement les données d’utilisation et de facturation **détaillées** à intervalles réguliers dans un conteneur Stockage Azure en créant un travail d’exportation pour la facturation. Cela est utile si vous souhaitez afficher les détails de votre facturation, par exemple le nombre d’heures facturées pour une instance donnée au cours de la période de facturation.

Pour configurer un travail d’exportation de facturation, procédez comme suit :

1. Cliquez **Exportations** à gauche.
1. Cliquez sur **Add**.
1. Entrez un nom et une fréquence d’exportation, puis cliquez sur Suivant.
1. Choisissez un compte de stockage existant ou créez-en un, puis remplissez le formulaire pour spécifier le compte de stockage, le conteneur et le chemin d’accès du répertoire dans lequel exporter les fichiers de données de facturation, puis cliquez sur Suivant.
1. Cliquez sur **Créer**.

Les fichiers d’exportation des données de facturation seront disponibles dans environ 4 heures, et seront exportés selon la planification que vous avez spécifiée lors de la création du travail d’exportation de la facturation.

Pour afficher les fichiers de données de facturation exportés, procédez comme suit :

Vous pouvez valider les fichiers de données de facturation dans le portail Azure. 

> [!IMPORTANT]
> Après avoir créé le travail d’exportation de facturation, patientez 4 heures avant de passer aux étapes suivantes.

1. Dans la zone de recherche en haut du portail, tapez **Comptes de stockage**, puis cliquez sur **Comptes de stockage**.
3. Cliquez sur le compte de stockage que vous avez spécifié lors de la création du travail d’exportation de facturation ci-dessus.
4. Cliquez sur Conteneurs à gauche.
5. Cliquez sur le conteneur que vous avez spécifié lors de la création du travail d’exportation de facturation ci-dessus.
6. Cliquez sur le dossier que vous avez spécifié lors de la création du travail d’exportation de facturation ci-dessus.
7. Explorez les dossiers et fichiers générés, puis cliquez sur l’un des fichiers. csv générés.
8. Cliquez sur le bouton **Télécharger** pour enregistrer le fichier dans votre dossier Téléchargements local.
9. Ouvrez le fichier à l’aide d’une visionneuse de fichiers. csv telle qu’Excel.
10. Filtrez les résultats pour afficher uniquement les lignes avec le **Type de ressource** = `Microsoft.AzureArcData/<data service resource type`.
11. Vous verrez le nombre d’heures d’utilisation de l’instance au cours de la période actuelle de 24 heures dans la colonne UsageQuantity.
