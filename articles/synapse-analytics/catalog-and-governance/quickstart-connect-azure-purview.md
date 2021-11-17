---
title: Connecter l’espace de travail Synapse à Azure Purview 
description: Connectez un compte Azure Purview à un espace de travail Synapse.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/29/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 86e2eee3be8a25cc236a5774ab485c863c8d655b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842522"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>Démarrage rapide : Connectez un compte Azure Purview à un espace de travail Synapse

Dans ce guide de démarrage rapide, vous allez inscrire un compte Azure Purview auprès d’un espace de travail Synapse. Cette connexion vous permet de découvrir les ressources Azure Purview, d’interagir avec elles grâce aux fonctionnalités de Synapse et de transmettre des informations sur de lignage à Purview.

Vous pouvez effectuer les tâches suivantes dans Synapse :
- Utiliser la zone de recherche en haut pour rechercher des ressources Purview en fonction de mots clés 
- Comprendre les données en fonction des métadonnées, de la [traçabilité](../../purview/catalog-lineage-user-guide.md), des annotations 
- Connecter ces données à votre espace de travail avec des services liés ou des jeux de données d’intégration 
- Analyser ces jeux de données avec Synapse Apache Spark, Synapse SQL et Data Flow 
- Exécuter des pipelines et [envoyer des informations de lignage à Purview](../../purview/how-to-lineage-azure-synapse-analytics.md)

## <a name="prerequisites"></a>Prérequis 
- [Compte Azure Purview](../../purview/create-catalog-portal.md) 
- [Espace de travail Synapse](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Autorisations pour la connexion d’un compte Azure Purview 

Pour connecter un compte Azure Purview à un espace de travail Synapse, vous devez disposer d’un rôle **Contributeur** dans l’espace de travail Synapse provenant de la gestion des identités et des accès (IAM) du portail Azure et d’un accès à ce compte Azure Purview. Pour plus d’informations, consultez [Autorisations Azure Purview](../../purview/catalog-permissions.md).

## <a name="connect-an-azure-purview-account"></a>Se connecter à un compte Azure Purview  

Suivez les étapes pour connecter un compte Azure Purview :

1. Accédez à  [https://web.azuresynapse.net](https://web.azuresynapse.net) et connectez-vous à votre espace de travail Synapse. 
2. Allez dans **Gérer** -> **Azure Purview**, sélectionnez **Connexion à un compte Purview**.
3. Vous pouvez choisir **Depuis un abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès.
4. Une fois connecté, vous pouvez voir le nom du compte Purview sous l’onglet **Compte Azure Purview**. 

Si votre compte Purview est protégé par un pare-feu, créez les points de terminaison privés managés pour Purview. Apprenez-en davantage sur la façon de laisser Azure Synapse [accéder à un compte Purview sécurisé](how-to-access-secured-purview-account.md). Vous pouvez le faire lors de la connexion initiale, ou modifier une connexion existante ultérieurement.

Les informations de connexion de Purview sont stockées dans la ressource de l’espace de travail Synapse, comme ci-dessous. Pour établir la connexion par programme, vous pouvez mettre à jour l’espace de travail Synapse et ajouter les paramètres `purviewConfiguration`.

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

## <a name="set-up-authentication"></a>Configurer l’authentification

L’identité managée de l’espace de travail Synapse est utilisée pour authentifier les opérations d’envoi de lignage de l’espace de travail Synapse dans Purview.

Accordez à l’identité managée de l’espace de travail Synapse le rôle **Curateur de données** sur votre **collection racine** Purview. En savoir plus sur le [Contrôle d’accès dans Azure Purview](../../purview/catalog-permissions.md) et comment [Ajouter des rôles et restreindre l’accès par le biais de regroupements](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

Lors de la connexion de l’espace de travail Synapse à Purview dans Synapse Studio, Synapse tente d’ajouter automatiquement une telle attribution de rôle. Si vous détenez le rôle **Administrateur de collection** sur la collection racine Purview et que vous avez accès au compte Purview à partir de votre réseau, cette opération s’effectue avec succès.

## <a name="monitor-purview-connection"></a>Contrôle de la connexion Purview

Une fois l’espace de travail Synapse connecté à un compte Purview, vous voyez la page suivante avec des détails sur les fonctionnalités d’intégration activées.

:::image type="content" source="./media/monitor-purview-connection-status.png" alt-text="Capture d’écran permettant de superviser l’état de l’intégration entre Azure Synapse et Purview.":::

Pour **Traçabilité des données - Pipeline Synapse**, vous pouvez voir l’état suivant :

- **Connecté** : l’espace de travail Synapse est connecté au compte Purview. Notez que cela indique que l’espace de travail Synapse est associé à un compte Purview et qu’il a la permission d’y envoyer (push) la traçabilité. Si votre compte Purview est protégé par un pare-feu, vous devez également vous assurer que le runtime d'intégration utilisé pour exécuter les activités et effectuer la traçabilité push peut atteindre le compte Purview. Pour plus d’informations, consultez [Accéder à un compte Azure Purview sécurisé](how-to-access-secured-purview-account.md).
- **Déconnecté** : l’espace de travail Synapse ne peut pas envoyer (push) la traçabilité à Purview, car le rôle de curateur de données Purview n’est pas accordé à l’identité managée de l’espace de travail Synapse. Pour résoudre ce problème, accédez à votre compte Purview pour vérifier l'attribution des rôles, et accordez manuellement le rôle si nécessaire. Pour plus d’informations, consultez la section [configurer l’authentification](#set-up-authentication) .
- **Inconnu** : Azure Synapse ne peut pas vérifier l’état. Les raisons possibles sont :

    - Impossible d'atteindre le compte Purview depuis votre réseau actuel car le compte est protégé par un pare-feu. Vous pouvez lancer Synapse Studio à partir d’un réseau privé qui dispose d’une connectivité à votre compte Purview à la place.
    - Vous n'avez pas le droit de vérifier les affectations de rôles sur le compte Purview. Vous pouvez contacter l'administrateur du compte Purview pour qu'il vérifie l'attribution des rôles pour vous. Pour en savoir plus sur le rôle Purview nécessaire, consultez la section [Configuration de l'authentification](#set-up-authentication).

## <a name="report-lineage-to-azure-purview"></a>Rapporter la traçabilité à Azure Purview

Une fois que vous avez connecté l’espace de travail Synapse à un compte Purview, lorsque vous exécutez des pipelines, Synapse signale les informations de lignage au compte Purview. Pour connaître le détail des fonctionnalités prises en charge et une présentation complète, voir [Métadonnées et lignage à partir d’Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md).

## <a name="discover-and-explore-data-using-purview"></a>Découvrir et explorer des données avec Purview

Une fois que vous avez connecté l’espace de travail Synapse à un compte Purview, vous pouvez utiliser la barre de recherche au centre de l’espace de travail Synapse pour rechercher des données et effectuer des actions. Consultez [Découvrir, connecter et explorer des données dans Synapse en utilisant Azure Purview](how-to-discover-connect-analyze-azure-purview.md) pour en savoir plus.

## <a name="nextsteps"></a>Étapes suivantes 

[Découvrir, connecter et explorer des données dans Synapse en utilisant Azure Purview](how-to-discover-connect-analyze-azure-purview.md)

[Métadonnées et traçabilité depuis Azure Synapse Analytics](../../purview/how-to-lineage-azure-synapse-analytics.md)

[Accéder à un compte Purview sécurisé](how-to-access-secured-purview-account.md)

[Inscrire et analyser des ressources Azure Synapse dans Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Tracer les données à partir de Power BI dans Azure Purview](../../purview/how-to-lineage-powerbi.md)

[Se connecter à Azure Data Share et Azure Purview](../../purview/how-to-link-azure-data-share.md)
