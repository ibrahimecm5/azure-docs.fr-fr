---
title: Utiliser des points de terminaison privés pour un accès sécurisé à Purview
description: Cet article décrit une vue d’ensemble générale de la façon dont vous pouvez utiliser un point de terminaison privé pour votre compte Purview
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: dba76c0b2d25c8bc962dd847e0d2ffdaa5bdd3eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234224"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Utiliser des points de terminaison privés pour votre compte Azure Purview

> [!IMPORTANT]
> Si vous avez créé un point de terminaison privé de _portail_ pour votre compte Purview **avant le 27 septembre 2021 à 15h30 UTC**, vous devez effectuer les actions requises détaillées dans [Reconfigurer DNS pour les points de terminaison privés du portail](#reconfigure-dns-for-portal-private-endpoints). **Ces actions doivent être effectuées avant le 12 novembre 2021. Sinon, les points de terminaison privés existants du portail cesseront de fonctionner**.


Cet article décrit comment configurer des points de terminaison privés pour Azure Purview.

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle
Vous pouvez utiliser des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md) pour vos comptes Azure Purview afin de permettre aux utilisateurs d’un réseau virtuel (VNet) d’accéder en toute sécurité au catalogue via une liaison privée. Un point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre compte Purview. Le trafic réseau entre les clients sur le réseau virtuel et le compte Purview traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft. 

Vous pouvez déployer un point de terminaison privé de _compte_ Azure Purview pour autoriser uniquement les appels client à Azure Purview qui proviennent du réseau privé.

Pour vous connecter à Azure Purview Studio à l’aide d’une connectivité de réseau privé, vous pouvez déployer un point de terminaison privé de _portail_.

Vous pouvez déployer des points de terminaison privés _d’ingestion_ si vous avez besoin d’analyser des sources de données IaaS et PaaS Azure dans des réseaux virtuels Azure et des sources de données locales via une connexion privée. Cette méthode garantit l’isolement réseau pour vos métadonnées circulant des sources de données vers Azure Purview Data Map.

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés."::: 

## <a name="prerequisites"></a>Configuration requise

Avant de déployer des points de terminaison privés pour le compte Azure Purview, vérifiez que vous respectez les prérequis suivants :

1. Compte Azure avec un abonnement actif. [Créez un compte gratuitement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. Réseau virtuel Azure existant. Déployez un nouveau [réseau virtuel Azure](../virtual-network/quick-create-portal.md) si vous n’en avez pas.
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Scénarios de déploiement d’un point de terminaison privé Azure Purview

Utilisez la liste de vérification recommandée suivante pour effectuer le déploiement d’un compte Azure Purview avec des points de terminaison privés :

|Scénario  |Objectifs  |
|---------|---------|
|**Scénario 1** - [Se connecter à Azure Purview et analyser des sources de données de manière privée et sécurisée](./catalog-private-link-end-to-end.md) |Vous devez restreindre l’accès à votre compte Azure Purview uniquement à l’aide d’un point de terminaison privé, notamment à Azure Purview Studio, aux API Atlas et analyser les sources de données localement et sur Azure derrière un réseau virtuel à l’aide du runtime d’intégration auto-hébergé, garantissant ainsi l’isolement réseau de bout en bout. (Déployez des points de terminaison privés de _compte_, de _portail_ et _d’ingestion_.)   |
|**Scénario 2** - [Se connecter de manière privée et sécurisée à votre compte Purview](./catalog-private-link-account-portal.md)   | Vous devez activer l’accès à votre compte Azure Purview, notamment l’accès à _Azure Purview Studio_ et à l’API Atlas via des points de terminaison privés. (Déployez des points de terminaison privés de _compte_ et de _portail_).   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>Matrice de prise en charge pour l’analyse des sources de données via un point de terminaison privé _d’ingestion_

Pour les scénarios où le point de terminaison privé _d’ingestion_ est utilisé dans votre compte Azure Purview, et que l’accès public sur vos sources de données est désactivé, Azure Purview peut analyser les sources de données suivantes qui se trouvent derrière un point de terminaison privé :

|Source de données derrière un point de terminaison privé  |Type de runtime d’intégration  |Type d'informations d'identification  |
|---------|---------|---------|
|Stockage Blob Azure | Runtime d’intégration auto-hébergé | Principal de service|
|Stockage Blob Azure | Runtime d’intégration auto-hébergé | Clé du compte|
|Azure Data Lake Storage Gen 2 | Runtime d’intégration auto-hébergé| Principal de service|
|Azure Data Lake Storage Gen 2 | Runtime d’intégration auto-hébergé| Clé du compte|
|Azure SQL Database | Runtime d’intégration auto-hébergé| Authentification SQL|
|Azure SQL Database | Runtime d’intégration auto-hébergé| Principal de service|
|Azure SQL Managed Instance | Runtime d’intégration auto-hébergé| Authentification SQL|
|Azure Cosmos DB| Runtime d’intégration auto-hébergé| Clé du compte|
|SQL Server | Runtime d’intégration auto-hébergé| Authentification SQL|
|Azure Synapse Analytics | Runtime d’intégration auto-hébergé| Principal de service|
|Azure Synapse Analytics | Runtime d’intégration auto-hébergé| Authentification SQL|

## <a name="reconfigure-dns-for-portal-private-endpoints"></a>Reconfigurer DNS pour les points de terminaison privés du portail

Si vous avez créé un point de terminaison privé de _portail_ pour votre compte Purview **avant le 27 septembre 2021 à 15h30 UTC**, vous devez effectuer les actions requises détaillées dans cette section.

### <a name="review-your-current-dns-settings"></a>Passer en revue vos paramètres DNS actuels

1. Dans le portail Azure, localisez votre compte Purview. Dans le menu gauche, cliquez sur **Mise en réseau**, sélectionnez **Connexions de points de terminaison privés**. Cliquez sur chaque point de terminaison privé dans la liste et suivez les étapes ci-dessous.

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-1.png" alt-text="Capture d’écran montrant le point de terminaison privé Purview"lightbox="media/catalog-private-link/purview-pe-dns-updates-1.png":::.

2. Si la sous-ressource cible est _portail_, passez en revue la **configuration DNS** ; sinon, revenez à l’étape précédente et sélectionnez le point de terminaison privé suivant jusqu’à ce que vous ayez examiné tous les points de terminaison privés et validé tous les points de terminaison privés associés au portail.

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-2.png" alt-text="Capture d’écran montrant le point de terminaison privé Purview du portail"lightbox="media/catalog-private-link/purview-pe-dns-updates-2.png":::.

3. Dans la fenêtre **Configuration DNS**, vérifiez les paramètres actuels :
   
    - S’il existe des enregistrements dans la section **Enregistrements DNS personnalisés**, suivez les étapes décrites dans [Scénarios de correction 1](#scenario-1) et [Scénario de correction 2](#scenario-2).
    
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-3.png" alt-text="Capture d’écran montrant la configuration DNS personnalisée du point de terminaison privé Purview du portail."lightbox="media/catalog-private-link/purview-pe-dns-updates-3.png":::

    - Si la section **Nom de la configuration** contient des enregistrements et si la zone DNS est `privatelink.purviewstudio.azure.com`, aucune action n’est nécessaire pour ce point de terminaison privé. Revenez à l’**étape 1** et passez en revue les points de terminaison privés restants du portail.
  
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-4.png" alt-text="Capture d’écran montrant le point de terminaison privé Purview du portail avec la nouvelle zone DNS"lightbox="media/catalog-private-link/purview-pe-dns-updates-4.png":::.
    
    - Si la section **Nom de la configuration** contient des enregistrements et si la zone DNS est `privatelink.purview.azure.com`, suivez les étapes de [Scénario de correction 3](#scenario-3).

        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-5.png" alt-text="Capture d’écran montrant le point de terminaison privé Purview du portail avec l’ancienne zone DNS"lightbox="media/catalog-private-link/purview-pe-dns-updates-5.png":::.

### <a name="remediation-scenarios"></a>Scénarios de correction

#### <a name="scenario-1"></a>Scénario 1

Si vous **avez ajouté des enregistrements A DNS nécessaires directement à votre DNS ou au fichier d’hôte de vos machines**, **aucune action n’est nécessaire**.
    
:::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-host.png" alt-text="Capture d’écran montrant le fichier d’hôte avec des enregistrements A."lightbox="media/catalog-private-link/purview-pe-dns-updates-host.png":::

#### <a name="scenario-2"></a>Scénario 2

Si vous **avez configuré des serveurs DNS locaux**, des **redirecteurs DNS ou une résolution DNS personnalisée**, passez en revue vos paramètres DNS et effectuez les actions appropriées :

1. Passez en revue votre serveur DNS. Si votre enregistrement DNS est `web.purview.azure.com` ou si votre redirecteur conditionnel est `purview.azure.com`, **aucune action n’est nécessaire**. 

2. Si votre enregistrement DNS est `web.privatelink.purview.azure.com`, mettez à jour l’enregistrement vers `web.privatelink.purviewstudio.azure.com`.

3. Si votre redirecteur conditionnel est `privatelink.purview.azure.com`, NE SUPPRIMEZ PAS la zone. Vous devez ajouter un nouveau redirecteur conditionnel à `privatelink.purviewstudio.azure.com`.

#### <a name="scenario-3"></a>Scénario 3

Si vous avez configuré **l’intégration de la zone DNS privée Azure pour votre compte Purview**, effectuez les étapes suivantes pour redéployer les points de terminaison privés afin de reconfigurer les paramètres DNS :

1. Déployez un nouveau point de terminaison privé du portail :
       
    1. Accédez au [Portail Azure](https://portal.azure.com), puis cliquez sur votre compte Azure Purview, et sous **Paramètres**, sélectionnez **Mise en réseau**, puis sélectionnez **Connexions de point de terminaison privé**.

        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé de portail."lightbox="media/catalog-private-link/purview-pe-reconfigure-portal.png":::

    2. Sélectionnez **+ Point de terminaison privé** pour créer un point de terminaison privé.

    3. Entrez les informations de base.

    4. Sous l’onglet **Ressource**, pour **Type de ressource**, sélectionnez **Microsoft.Purview/account**.

    5. Pour **Ressource**, sélectionnez le compte Azure Purview et pour **Sous-ressource cible**, sélectionnez **portail**.

    6. Sous l’onglet **Configuration**, sélectionnez le réseau virtuel et, si vous le souhaitez, sélectionnez la zone de DNS privé Azure pour créer une zone Azure DNS.
            
        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="Capture d’écran montrant comment créer un point de terminaison privé de portail et des paramètres DNS."lightbox="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png":::

    7. Accédez à la page de résumé, puis sélectionnez **Créer** pour créer le point de terminaison privé du portail.

2. Supprimez le point de terminaison privé du portail précédent associé au compte Purview. 

3. Vérifiez qu’une nouvelle zone de DNS privée Azure `privatelink.purviewstudio.azure.com` est créée lors du déploiement du point de terminaison privé du portail et qu’un enregistrement A correspondant (web) existe dans la zone DNS privée. 
    
4. Assurez-vous que vous êtes en mesure de charger correctement Azure Purview Studio. Il faudra peut-être quelques minutes (environ 10 minutes) avant que le nouveau routage DNS ne prenne effet après la reconfiguration du DNS. Vous pouvez patienter quelques minutes et réessayer s’il ne se charge pas immédiatement.
    
5. Si la navigation échoue, exécutez nslookup web.purview.azure.com qui doit être résolu en une adresse IP privée associée au point de terminaison privé du portail.
  
6. Répétez les étapes 1 à 3 ci-dessus pour tous les points de terminaison privés du portail existants. 

### <a name="validation-steps"></a>Étapes de validation

1. Assurez-vous que vous êtes en mesure de charger correctement Azure Purview Studio. Il faudra peut-être quelques minutes (environ 10 minutes) avant que le nouveau routage DNS ne prenne effet après la reconfiguration du DNS. Vous pouvez patienter quelques minutes et réessayer s’il ne se charge pas immédiatement.

2. Si la navigation échoue, exécutez nslookup `web.purview.azure.com`, qui doit être résolu en une adresse IP privée associée au point de terminaison privé du portail.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)  

Pour consulter les questions fréquentes relatives aux déploiements de points de terminaison privés dans Azure Purview, consultez le [Forum aux questions sur les points de terminaison privés Azure Purview](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Guide de résolution des problèmes 
Pour résoudre les problèmes de configuration de point de terminaison privé pour les comptes Purview, consultez [Résolution des problèmes de configuration de point de terminaison privé pour les comptes Purview](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Limites connues
Pour afficher la liste des limitations actuelles liées aux points de terminaison privés Azure Purview, consultez [Limitations connues des points de terminaison privés Azure Purview](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un réseau privé de bout en bout](./catalog-private-link-end-to-end.md)
- [Déployer un réseau privé pour Studio Purview](./catalog-private-link-account-portal.md)
