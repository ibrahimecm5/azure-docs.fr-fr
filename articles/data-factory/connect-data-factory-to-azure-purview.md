---
title: Connecter une fabrique de données à Azure Purview
description: En savoir plus sur la connexion d’une fabrique de données à Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 10/25/2021
ms.openlocfilehash: 026dc4cb24c5e7b51fc5eec918cf92d3b6b23090
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846135"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connecter Data Factory à Azure Purview (préversion)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) est un service unifié de gouvernance des données qui vous aide à gérer et à régir vos données locales, multiclouds et SaaS (software-as-a-service). Vous pouvez connecter votre Data Factory à Azure Purview. Cette connexion vous permet d’utiliser Azure Purview pour capturer les données de traçabilité, ainsi que découvrir et explorer les ressources Azure Purview.

## <a name="connect-data-factory-to-azure-purview"></a>Connecter Data Factory à Azure Purview

Vous pouvez connecter Data Factory à Azure Purview de deux façons :

- [Connecter un compte Azure Purview à Data Factory](#connect-to-azure-purview-account-in-data-factory)
- [Inscrire Data Factory dans Azure Purview](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Se connecter au compte Azure Purview dans Data Factory

Vous devez détenir le rôle de **Propriétaire** ou de **Contributeur** dans votre fabrique de données pour vous connecter à un compte Azure Purview.

Pour établir la connexion sur l’interface utilisateur de création de Data Factory :

1. Dans l’IU de création ADF, accédez à **Gestion** -> **Azure Purview**, et sélectionner **Connexion à un compte Purview**. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Capture d’écran de l’inscription d’un compte Purview.":::

2. Choisissez **A partir de l’abonnement Azure** ou **Entrer manuellement**. **Depuis un abonnement Azure**, vous pouvez sélectionner le compte auquel vous avez accès.

3. Une fois connecté, vous pouvez voir le nom du compte Purview sous l’onglet **Compte Purview**.

Si votre compte Purview est protégé par un pare-feu, créez les points de terminaison privés managés pour Purview. En savoir plus sur la façon de permettre à Data Factory d'[accéder à un compte sécurisé Purview](how-to-access-secured-purview-account.md). Vous pouvez le faire lors de la connexion initiale, ou modifier une connexion existante ultérieurement.

Les informations de connexion de Purview sont stockées dans la ressource de la fabrique de données, comme ci-dessous. Pour établir la connexion de manière programmatique, vous pouvez mettre à jour la fabrique de données et ajouter les paramètres `purviewConfiguration`. Lorsque vous voulez pousser le lignage à partir des activités SSIS, ajouter également une `catalogUri`balise supplémentaire.

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    ...
    "identity": {...},
    "tags": {
        "catalogUri": "<PurviewAccountName>.purview.azure.com/catalog //Note: used for SSIS lineage only"
    }
}
```

### <a name="register-data-factory-in-azure-purview"></a>Inscrire Data Factory dans Azure Purview

Pour savoir comment inscrire Data Factory dans Azure Purview, consultez [Guide pratique pour connecter Azure Data Factory et Azure Purview](../purview/how-to-link-azure-data-factory.md).

## <a name="set-up-authentication"></a>Configurer l’authentification

L’identité managée de la fabrique de données est utilisée pour authentifier les opérations push de traçabilité de la fabrique de données vers Purview. 

Accordez à l’identité managée de la fabrique de données le rôle **Curateur de données** sur votre **collection racine** Purview. En savoir plus sur le [Contrôle d’accès dans Azure Purview](../purview/catalog-permissions.md) et comment [Ajouter des rôles et restreindre l’accès par le biais de regroupements](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections).

Lors de la connexion de la fabrique de données à Purview sur l’interface utilisateur de création, ADF tente d’ajouter automatiquement une telle attribution de rôle. Si vous détenez le rôle **Administrateur de collection** sur la collection racine Purview et que vous avez accès au compte Purview à partir de votre réseau, cette opération s’effectue avec succès.

## <a name="monitor-purview-connection"></a>Contrôle de la connexion Purview

Une fois que vous avez connecté l'usine de données à un compte Purview, vous voyez la page suivante avec des détails sur les capacités d'intégration activées.

:::image type="content" source="./media/data-factory-purview/monitor-purview-connection-status.png" alt-text="Capture d'écran permettant de contrôler l'état de l'intégration entre Azure Data Factory et Purview.":::

Pour **le lignage des données-pipeline**, vous pouvez voir l’état suivant :

- **Connecté** : la fabrique de données est connectée au compte Purview. Notez que cela indique que l'usine de données est associée à un compte Purview et qu'elle a la permission d'y pousser la lignée. Si votre compte Purview est protégé par un pare-feu, vous devez également vous assurer que le runtime d'intégration utilisé pour exécuter les activités et effectuer la traçabilité push peut atteindre le compte Purview. En savoir plus sur [Accéder à un compte Azure Purview sécurisé à partir d'Azure Data Factory](how-to-access-secured-purview-account.md).
- **Déconnecté** : L'usine de données ne peut pas pousser le lignage vers Purview car le rôle de curateur de données Purview n'est pas accordé à l'identité gérée de l'usine de données. Pour résoudre ce problème, accédez à votre compte Purview pour vérifier l'attribution des rôles, et accordez manuellement le rôle si nécessaire. Pour plus d’informations, consultez la section [configurer l’authentification](#set-up-authentication) .
- **Inconnu**: Data Factory ne peut pas vérifier l’état. Les raisons possibles sont :

    - Impossible d'atteindre le compte Purview depuis votre réseau actuel car le compte est protégé par un pare-feu. Vous pouvez lancer l’interface de chargement automatique à partir d’un réseau privé qui dispose d’une connectivité à votre compte Purview à la place.
    - Vous n'avez pas le droit de vérifier les affectations de rôles sur le compte Purview. Vous pouvez contacter l'administrateur du compte Purview pour qu'il vérifie l'attribution des rôles pour vous. Pour en savoir plus sur le rôle Purview nécessaire, consultez la section [Configuration de l'authentification](#set-up-authentication).

## <a name="report-lineage-data-to-azure-purview"></a>Rapporter les données de traçabilité à Azure Purview

Une fois que vous avez connecté la fabrique de données à un compte Purview, lorsque vous exécutez des pipelines, Data Factory envoie les informations de traçabilité au compte Purview. Pour plus d’informations sur les fonctionnalités prises en charge, consultez [Activités Azure Data Factory prises en charge](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities). Pour une procédure pas à pas de bout en bout, reportez-vous au [Didacticiel : transmettre les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md).

## <a name="discover-and-explore-data-using-purview"></a>Découvrir et explorer des données avec Purview

Une fois que vous avez connecté la fabrique de données à un compte Purview, vous pouvez utiliser la barre de recherche située en haut au centre de l’IU de création Data Factory pour rechercher des données et effectuer des actions. En savoir plus sur [Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md)

[Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)

[Accéder à un compte Purview sécurisé](how-to-access-secured-purview-account.md)
