---
title: 'Démarrage rapide : Créer un pool Explorateur de données à l’aide du portail Azure (préversion)'
description: Créez un pool Explorateur de données à l’aide du portail Azure en suivant les étapes décrites dans ce guide.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8cf272407e64c612c4a3debb66ba13f7147cd227
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097534"
---
# <a name="quickstart-create-a-data-explorer-pool-using-the-azure-portal-preview"></a>Démarrage rapide : Créer un pool Explorateur de données à l’aide du portail Azure (préversion)

Azure Synapse Data Explorer est un service d'analyse de données rapide et entièrement géré permettant d'analyser en temps réel de gros volumes de données en continu à partir d'applications, de sites Web, de dispositifs IoT, etc. Pour utiliser Data Explorer, vous devez d'abord créer un pool Data Explorer.

Cet article décrit les étapes à suivre pour créer un pool Explorateur de données dans un espace de travail Synapse à l’aide du portail Azure.

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

    ![Barre de recherche du portail Azure contenant le texte « espaces de travail Synapse ».](../media/quickstart-create-sql-pool/create-sql-pool-00a.png)

1. Dans la liste des espaces de travail, saisissez le nom (ou une partie du nom) de l’espace de travail à ouvrir. Pour cet exemple, nous allons utiliser un espace de travail nommé **contosoanalytics**.

    ![Liste des espaces de travail Synapse filtrés pour afficher ceux contenant le nom contoso.](../media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-a-new-data-explorer-pool"></a>Créer un nouveau pool Data Explorer

1. Dans l’espace de travail Synapse où vous souhaitez créer le pool Explorateur de données, sélectionnez **Nouveau pool Explorateur de données**.

    ![Page d’accueil du portail Azure avec la section Vue d’ensemble en évidence.](media/create-data-explorer-pool-portal/goto-data-explorer-pool-portal.png)

1. Sous l’onglet **Général**, entrez les informations suivantes :

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Nom du pool Data Explorer | contosodataexplorer | Il s'agit du nom que portera le pool Data Explorer. |
    | Charge de travail | Optimisé pour le calcul | Cette charge de travail offre un ratio plus élevé entre le CPU et le stockage SSD. |
    | Taille du nœud | Petite (4 cœurs) | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    ![Concepts de base pour le nouveau pool Explorateur de données du portail Azure](media/create-data-explorer-pool-portal/create-data-explorer-pool-basics-portal.png)

    > [!IMPORTANT]
    > Notez qu'il existe des limitations spécifiques concernant les noms que les pools Data Explorer peuvent utiliser. Les noms ne doivent contenir que des lettres minuscules et des chiffres. Ils doivent comporter entre 4 et 15 caractères, et commencer par une lettre.

1. Sélectionnez **Suivant : Paramètres supplémentaires**. Utilisez les paramètres suivants et laissez les valeurs par défaut pour les autres paramètres.

    | Paramètre | Valeur suggérée | Description |
    |--|--|--|
    | Mise à l'échelle | Mise à l’échelle manuelle | Ce guide de démarrage rapide ne nécessite pas de mise à l’échelle automatique |
    | Nombre de numéros | 2 | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    ![Paramètres avancés pour le nouveau pool Explorateur de données du portail Azure](media/create-data-explorer-pool-portal/create-data-explorer-pool-advanced-settings-portal.png)

1. Sélectionnez **Suivant : étiquettes**. N’ajoutez aucune étiquette.
1. Sélectionnez **Revoir + créer**.
1. Passez en revue les détails en vous assurant qu’ils sont corrects, puis sélectionnez **Créer**.

    Le pool Data Explorer commencera le processus de provisionnement.

    ![Créer un nouveau pool Explorateur de données du portail Azure](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. Une fois l’approvisionnement terminé, revenez à la page **Vue d’ensemble** de l’espace de travail et vérifiez que la page d’accueil du nouveau pool Explorateur de données s’affiche.

    ![Liste du nouveau pool Explorateur de données du portail Azure](media/create-data-explorer-pool-portal/verify-data-explorer-pool-portal.png)

## <a name="clean-up-data-explorer-pool-resources-using-the-azure-portal"></a>Nettoyer les ressources du pool Explorateur de données à l’aide du portail Azure

Suivez les étapes suivantes pour supprimer le pool Explorateur de données de l’espace de travail à l’aide du portail Azure.

> [!WARNING]
> La suppression d'un pool d'explorateurs de données entraîne la suppression du moteur d'analyse de l'espace de travail. Il ne sera plus possible de se connecter au pool, et toutes les requêtes, pipelines et notebooks qui utilisent le pool supprimé ne fonctionneront plus.

### <a name="delete-the-data-explorer-pool"></a>Supprimer le pool d’Explorateur de données

1. Naviguez vers les pools de l'Explorateur de données dans l'espace de travail.
1. Pour supprimer le pool d’Explorateur de données (dans ce cas, **contosodataexplorer**), sélectionnez **plus [...]**  > **Supprimer**.

    ![Liste des pools Data Explorer, avec le pool récemment créé sélectionné.](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. Pour confirmer la suppression, saisissez le nom du pool à supprimer, puis sélectionnez **Supprimer**.

    ![Confirmez la suppression du pool récemment créé.](media/create-data-explorer-pool-portal/confirm-deletion-data-explorer-pool-portal.png)

1. Une fois le processus terminé avec succès, vérifiez que le pool n'apparaît plus dans la liste.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Créer un pool Explorateur de données en utilisant Synapse Studio](data-explorer-create-pool-studio.md)
