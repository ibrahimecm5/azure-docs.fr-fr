---
title: Inventaire des ressources de Microsoft Defender pour le cloud
description: Découvrez l’expérience de gestion des ressources de Microsoft Defender pour le cloud fournissant une visibilité complète de l’ensemble de vos ressources surveillées par Defender pour le cloud.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/02/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 548e622548f7b55259cb08a5937d45ab2766df3f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061113"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>Utiliser l’inventaire des ressources pour gérer l’état de sécurité de vos ressources

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

La page d’inventaire des ressources de Microsoft Defender pour le cloud est une page unique pour l’affichage de la situation de sécurité des ressources que vous avez connectées à Microsoft Defender pour le cloud. 

Defender pour le cloud analyse régulièrement l’état de sécurité des ressources connectées à vos abonnements pour identifier des failles de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités.

Lorsqu’une ressource contient des recommandations en suspens, celles-ci apparaissent dans l’inventaire.

Utilisez cette vue et ses filtres pour répondre à des questions telles que les suivantes :

- Quels sont mes abonnements dont les fonctionnalités de sécurité renforcée sont activées qui ont des recommandations en suspens ?
- Parmi mes machines ayant la balise « Production », lesquelles n’ont pas l’agent Log Analytics ?
- Combien de mes machines, marquées d’une balise spécifique, ont des recommandations en suspens ?
- Quels ordinateurs d’un groupe de ressources spécifique présentent une vulnérabilité connue (à l’aide d’un numéro de CVE) ?

Les possibilités de gestion des ressources de cet outil sont considérables et continuent à se développer. 

> [!TIP]
> Les recommandations de sécurité sur la page d’inventaire des ressources sont les mêmes que celles de la page **Recommandations**, mais ici, elles sont affichées selon la ressource affectée. Pour plus d’informations sur l’application de recommandations, consultez [Implémentation des recommandations de sécurité dans Microsoft Defender pour le cloud](review-security-recommendations.md).


## <a name="availability"></a>Disponibilité
|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Gratuit *<br>* Certaines fonctionnalités de la page d’inventaire, notamment l’[inventaire logiciel](#access-a-software-inventory), nécessitent la mise en place de solutions payantes|
|Rôles et autorisations obligatoires :|tous les utilisateurs|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nationaux/souverains (Azure Government, Azure China 21Vianet)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Quelles sont les principales fonctionnalités de l’inventaire des ressources ?
La page d’inventaire fournit les outils suivants :

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Principales fonctionnalités de la page d’inventaire des ressources dans Microsoft Defender pour le cloud." lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 - Résumés
Avant de définir des filtres, sachez qu'une bande de valeurs bien visible en haut de la vue de l'inventaire affiche les éléments suivants :

- **Total des ressources** : nombre total de ressources connectées à Defender pour le cloud.
- **Ressources non saines** : Ressources ayant des recommandations de sécurité actives. [En savoir plus sur les recommandations de sécurité.](review-security-recommendations.md)
- **Ressources non analysées** : Ressources ayant des problèmes d’analyse de l’agent (l’agent Log Analytics est déployé, mais il n’envoie pas de données ou a d’autres problèmes d’intégrité).
- **Abonnements non inscrits** : tout abonnement dans l’étendue sélectionnée qui n’a pas encore été connecté à Microsoft Defender pour le cloud.

### <a name="2---filters"></a>2 - Filtres
Les différents filtres situés en haut de la page permettent d'affiner rapidement la liste des ressources en fonction de la question à laquelle vous tentez de répondre. Par exemple, si vous souhaitez répondre à la question *Parmi mes machines ayant la balise « Production », lesquelles n'ont pas l'agent Log Analytics ?* , vous pouvez combiner les filtres **Analyse de l'agent** et **Balises**.

Dès que vous avez appliqué des filtres, les valeurs récapitulatives sont mises à jour pour se rapporter aux résultats de la requête. 

### <a name="3---export-and-asset-management-tools"></a>3 - Outils d'exportation et de gestion des ressources

**Options d'exportation** : l'inventaire contient une option permettant d'exporter vers un fichier CSV les résultats des options de filtrage que vous avez sélectionnées. Vous pouvez également exporter la requête proprement dite vers Azure Resource Graph Explorer pour affiner, enregistrer ou modifier la requête KQL (Kusto Query Language).

> [!TIP]
> La documentation KQL fournit une base de données avec quelques exemples de données ainsi que quelques requêtes simples pour vous faire une idée du langage. [Pour en savoir plus, consultez ce didacticiel KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**Options de gestion des ressources** : une fois que vous avez trouvé les ressources qui correspondent à vos requêtes, l’inventaire fournit des raccourcis pour les opérations telles que :

- Attribuer des balises aux ressources filtrées : activez les cases à cocher à côté des ressources que vous souhaitez baliser.
- Intégrer de nouveaux serveurs à Defender pour le cloud : utilisez le bouton de barre d’outils **Ajouter des serveurs non-Azure**.
- Automatiser les charges de travail avec Azure Logic Apps : utilisez le bouton **Déclencher l’application logique** pour exécuter une application logique sur une ou plusieurs ressources. Vos applications logiques doivent être préparées à l’avance et accepter le type de déclencheur approprié (requête HTTP). [En savoir plus sur Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>Fonctionnement de l’inventaire des ressources

L’inventaire des ressources utilise [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml), un service Azure qui permet d’interroger les données relatives à la situation de sécurité de Defender pour le cloud sur plusieurs abonnements.

ARG est conçu pour permettre une exploration efficace des ressources avec la possibilité d’interroger à grande échelle.

Grâce au [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/), l’inventaire des ressources peut rapidement produire des informations détaillées en croisant les données de Defender pour le cloud avec les propriétés d’autres ressources.


## <a name="how-to-use-asset-inventory"></a>Utilisation de l’inventaire des ressources

1. Dans la barre latérale de Defender pour le cloud, sélectionnez **Inventaire**.

1. Utilisez l’option **Filtrer par nom** pour afficher une ressource spécifique, ou utilisez les filtres comme décrit ci-dessous.

1. Sélectionnez les options pertinentes dans les filtres pour créer la requête spécifique que vous souhaitez effectuer.

    Par défaut, les ressources sont triées selon le nombre de recommandations de sécurité actives.

    > [!IMPORTANT]
    > Les options de chaque filtre sont spécifiques aux ressources des abonnements actuellement sélectionnés **et** à vos sélections dans les autres filtres.
    >
    > Par exemple, si vous avez sélectionné un seul abonnement et que celui-ci ne comporte aucune ressource avec des recommandations de sécurité à corriger (0 ressource non saine), le filtre **Recommandations** n’aura aucune option. 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Utiliser les options de filtrage de l’inventaire des ressources de Microsoft Defender pour le cloud pour filtrer les ressources sur les ressources de production qui ne sont pas surveillées":::

1. Pour utiliser le filtre **Les résultats de sécurité contiennent**, entrez en texte libre l’ID, la vérification de sécurité ou le nom CVE du résultat d’une vulnérabilité pour filtrer les ressources concernées :

    ![Filtre « Les résultats de sécurité contiennent »](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > Les filtres **Les résultats de sécurité contiennent** et **Balises** n’acceptent qu’une seule valeur. Pour filtrer sur plusieurs valeurs, utilisez **Ajouter des filtres**.

1. Pour utiliser le filtre **Defender pour le cloud**, sélectionnez une ou plusieurs options (Désactivé, Activé ou Partiel) :

    - **Désactivé** : ressources non protégées par un plan Microsoft Defender. Vous pouvez cliquer avec le bouton droit sur l'un de ces éléments et les mettre à niveau :

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Mettez à niveau une ressource à protéger par le plan Microsoft Defender approprié en cliquant avec le bouton droit." lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **Activé** : ressources protégées par un plan Microsoft Defender
    - **Partiel** : s’applique aux **abonnements** pour lesquels certains plans Microsoft Defender ont été désactivés. Par exemple, l’abonnement suivant a sept plans Microsoft Defender désactivés.

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Abonnement partiellement protégé par des plans Microsoft Defender.":::

1. Pour examiner plus en détail les résultats de votre requête, sélectionnez les ressources qui vous intéressent.

1. Pour afficher les options de filtre actuellement sélectionnées en tant que requête dans l’Explorateur Resource Graph, sélectionnez **Ouvrir la requête**.

    ![Requête d’inventaire dans ARG.](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Si vous avez défini des filtres et laissé la page ouverte, Defender pour le cloud ne met pas à jour les résultats automatiquement. Les modifications apportées aux ressources n’ont pas d’impact sur les résultats affichés, sauf si vous rechargez manuellement la page ou si vous sélectionnez **Actualiser**.

## <a name="access-a-software-inventory"></a>Accéder à un inventaire logiciel

Si vous avez activé l’intégration avec Microsoft Defender pour point de terminaison et avec activé Microsoft Defender pour les serveurs, vous aurez accès à l’inventaire logiciel.

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="Si vous avez activé la solution de gestion des menaces et des vulnérabilités, l’inventaire des ressources de Defender pour le cloud offre un filtre permettant de sélectionner les ressources en fonction de leurs logiciels installés.":::

> [!NOTE]
> L’option « Blank » (vide) montre les machines qui ne disposent pas de Microsoft Defender pour point de terminaison (ou de Microsoft Defender pour les serveurs).

Outre les filtres de la page d'inventaire des ressources, vous pouvez explorer les données de l'inventaire logiciel à partir d’Azure Resource Graph Explorer.

Exemples d'utilisation d’Azure Resource Graph Explorer pour accéder aux données de l'inventaire logiciel et les explorer :

1. Ouvrez l’**Explorateur Azure Resource Graph**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Lancement de la page de recommandations de l’Explorateur Azure Resource Graph**" :::

1. Sélectionnez l’étendue d'abonnement suivante : securityresources/softwareinventories

1. Entrez l’une des requêtes suivantes (ou personnalisez-les ou écrivez votre propre !), puis sélectionnez **Exécuter la requête**.

    - Pour générer une liste de base des logiciels installés :

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - Pour filtrer par numéros de version :

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - Pour identifier les ordinateurs avec une combinaison de produits logiciels :

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - Combinaison d’un produit logiciel avec une autre recommandation de sécurité :

        (Dans cet exemple, ordinateurs sur lesquels MySQL est installé et dont les ports de gestion sont exposés)

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```



## <a name="faq---inventory"></a>FAQ – Inventaire

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Pourquoi tous mes abonnements, ordinateurs, comptes de stockage, etc. ne sont-ils pas affichés ?

La vue de l’inventaire répertorie vos ressources connectées Defender pour le cloud du point de vue de la gestion de la situation de sécurité cloud (CSPM). Les filtres ne renvoient pas toutes les ressources de votre environnement, mais seulement celles qui ont des recommandations en suspens (ou « actives »). 

Par exemple, la capture d’écran suivante montre un utilisateur ayant accès à 8 abonnements, mais seulement 7 de ces abonnements ont des recommandations. Ainsi, si l’on applique le filtre **Type de ressource = Abonnements**, seuls ces 7 abonnements avec des recommandations actives apparaissent dans l’inventaire :

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Tous les abonnements ne sont pas retournés lorsqu’il n’y a pas de recommandations actives." lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>Pourquoi certaines de mes ressources affichent-elles des valeurs vides dans les colonnes de Defender pour le cloud ou de l’agent de surveillance ?

Certaines ressources surveillées par Defender pour le cloud n’ont pas d’agent. Par exemple, les comptes Stockage Azure ou des ressources PaaS telles que les disques, Logic Apps, Data Lake Analysis et Event Hub, n’ont pas besoin que les agents soient surveillés par Defender pour le cloud.

Lorsque la tarification ou l’analyse de l’agent ne sont pas pertinentes pour une ressource, rien n’est indiqué dans ces colonnes d’inventaire.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Certaines ressources affichent des informations vides dans les colonnes de l’agent de surveillance ou de Defender pour le cloud." lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit la page inventaire des ressources de Microsoft Defender pour le cloud.

Pour plus d’informations sur les outils connexes, consultez les pages suivantes :

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/)
