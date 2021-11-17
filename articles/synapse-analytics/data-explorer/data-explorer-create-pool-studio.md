---
title: 'Démarrage rapide : Créer un pool Data Explorer en utilisant Synapse Studio (Préversion)'
description: Créez un pool Data Explorer en utilisant Synapse Studio en suivant les étapes de ce guide.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59a22c976650ea0f7aed7ca3eb2da72702aef5bf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424776"
---
# <a name="quickstart-create-a-data-explorer-pool-using-synapse-studio-preview"></a>Démarrage rapide : Créer un pool Data Explorer en utilisant Synapse Studio (Préversion)

Azure Synapse Data Explorer est un service d'analyse de données rapide et entièrement géré permettant d'analyser en temps réel de gros volumes de données en continu à partir d'applications, de sites Web, de dispositifs IoT, etc. Pour utiliser Data Explorer, vous devez d'abord créer un pool Data Explorer.

Ce guide de démarrage rapide décrit les étapes à suivre pour créer un pool Data Explorer dans un espace de travail Synapse à l'aide de Synapse Studio.

> [!IMPORTANT]
> La facturation des instances de Data Explorer est calculée au prorata de la minute, que vous les utilisiez ou non. Veillez à fermer votre instance de Data Explorer lorsque vous avez fini de l'utiliser, ou définissez un court délai d'attente. Pour plus d’informations, consultez **Nettoyer les ressources**.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Espace de travail Synapse](../quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accéder à l’espace de travail Synapse

1. Naviguez vers l'espace de travail Synapse où le pool Data Explorer sera créé en tapant le nom du service (ou le nom de la ressource directement) dans la barre de recherche.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-synapse-workspace.png" alt-text="Barre de recherche du portail Azure contenant le texte « espaces de travail Synapse ».":::

1. Dans la liste des espaces de travail, saisissez le nom (ou une partie du nom) de l’espace de travail à ouvrir. Pour cet exemple, nous allons utiliser un espace de travail nommé **contosoanalytics**.

    :::image type="content" source="media/create-data-explorer-pool-studio/filter-synapse-workspace.png" alt-text="Liste des espaces de travail Synapse filtrés pour afficher ceux contenant le nom contoso.":::

## <a name="launch-synapse-studio"></a>Lancer Synapse Studio

Dans la vue d’ensemble de l’espace de travail, sélectionnez l’**URL web de l’espace de travail** pour ouvrir Synapse Studio.

:::image type="content" source="media/create-data-explorer-pool-studio/launch-synapse-studio.png" alt-text="Vue d’ensemble de l’espace de travail Synapse du portail Azure avec l’option Lancer Synapse Studio mise en surbrillance.":::

## <a name="create-a-new-data-explorer-pool"></a>Créer un nouveau pool Data Explorer

1. Sur la page d’accueil de Synapse Studio, accédez au **hub de gestion** dans le volet de navigation gauche en sélectionnant l’icône **Gérer**.

    :::image type="content" source="media/create-data-explorer-pool-studio/select-synapse-studio-management-hub.png" alt-text="Page d’accueil de Synapse Studio avec la section Hub de gestion mise en surbrillance.":::

1. Une fois dans le Management Hub, naviguez vers la section des **pools Data Explorer** pour voir la liste actuelle des pools Data Explorer qui sont disponibles dans l'espace de travail.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-data-explorer-pool-studio.png" alt-text="Concentrateur de gestion Synapse Studio avec la navigation des pools Data Explorer sélectionnée":::

1. Sélectionnez **+Nouveau**. L'assistant de création d'un nouveau pool Data Explorer apparaît.

1. Sous l’onglet **Général**, entrez les informations suivantes :

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom du pool Data Explorer | contosodataexplorer | Il s'agit du nom que portera le pool Data Explorer. |
    | Charge de travail | Optimisé pour le calcul | Cette charge de travail offre un ratio plus élevé entre le CPU et le stockage SSD. |
    | Taille du nœud | Petite (4 cœurs) | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-basics-studio.png" alt-text="Principes de base du nouveau pool de l'explorateur de données de Synapse Studio":::

    > [!IMPORTANT]
    > Notez qu'il existe des limitations spécifiques concernant les noms que les pools Data Explorer peuvent utiliser. Les noms ne doivent contenir que des lettres minuscules et des chiffres. Ils doivent comporter entre 4 et 15 caractères, et commencer par une lettre.

1. Sélectionnez **Suivant : Paramètres supplémentaires**. Utilisez les paramètres suivants et laissez les valeurs par défaut pour les autres paramètres.


    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Mise à l’échelle automatique | Désactivé | Ce guide de démarrage rapide ne nécessite pas de mise à l’échelle automatique |
    | Nombre d’instances | 2 | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Principes de base du nouveau pool de l'explorateur de données de Synapse Studio":::

1. Sélectionnez **Suivant : étiquettes**. N’ajoutez aucune étiquette.
1. Sélectionnez **Revoir + créer**.
1. Passez en revue les détails en vous assurant qu’ils sont corrects, puis sélectionnez **Créer**.

    Le pool Data Explorer commencera le processus de provisionnement.

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-studio.png" alt-text="Créer un nouveau pool Data Explorer dans Synapse Studio":::

1. Une fois le provisionnement terminé, revenez à l'espace de travail et vérifiez que le nouveau pool Data Explorer apparaît dans la liste.

    :::image type="content" source="media/create-data-explorer-pool-studio/verify-data-explorer-pool-studio.png" alt-text="Synapse Studio nouvelle liste de pools Data Explorer":::

## <a name="clean-up-data-explorer-pool-resources-using-synapse-studio"></a>Nettoyage des ressources du pool Data Explorer à l'aide de Synapse Studio

Suivez les étapes suivantes pour supprimer le pool Data Explorer de l'espace de travail à l'aide de Synapse Studio.

> [!WARNING]
> La suppression d'un pool d'explorateurs de données entraîne la suppression du moteur d'analyse de l'espace de travail. Il ne sera plus possible de se connecter au pool, et toutes les requêtes, pipelines et notebooks qui utilisent le pool supprimé ne fonctionneront plus.

### <a name="delete-the-data-explorer-pool"></a>Supprimer le pool d’Explorateur de données

1. Naviguez vers les pools de l'Explorateur de données dans l'espace de travail.
1. Pour supprimer le pool d’Explorateur de données (dans ce cas, **contosodataexplorer**), sélectionnez **plus [...]**  > **Supprimer**.

    :::image type="content" source="media/create-data-explorer-pool-studio/list-data-explorer-pool-studio.png" alt-text="Liste des pools Data Explorer, avec le pool récemment créé sélectionné.":::

1. Pour confirmer la suppression, saisissez le nom du pool à supprimer, puis sélectionnez **Supprimer**.

    :::image type="content" source="media/create-data-explorer-pool-studio/confirm-deletion-data-explorer-pool-studio.png" alt-text="Confirmez la suppression du pool récemment créé.":::

1. Une fois le processus terminé avec succès, vérifiez que le pool n'apparaît plus dans la liste.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’ingestion](ingest-data/data-explorer-ingest-data-overview.md)
- [Analyser avec l’Explorateur de données](../get-started-analyze-data-explorer.md)
- [Surveiller les pools de l’Explorateur de données](data-explorer-monitor-pools.md)
