---
title: Guide pratique pour visualiser, modifier et supprimer des ressources
description: Ce guide décrit comment vous pouvez visualiser et modifier les détails d’une ressource.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6013811622492f476e65c91d3c028007e2802c27
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485191"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>Visualiser, modifier et supprimer des ressources dans le catalogue Purview

Cet article explique comment vous pouvez visualiser vos ressources et les détails correspondants. Il explique également comment vous pouvez modifier et supprimer des ressources de votre catalogue.

## <a name="prerequisites"></a>Configuration requise

- Configurez vos sources de données et analysez les ressources dans votre catalogue.
- *Ou* Utilisez les API Purview Atlas pour ingérer les ressources dans le catalogue. 

## <a name="viewing-asset-details"></a>Affichage des détails d’une ressource

Vous pouvez découvrir vos ressources dans Purview comme suit :
- [En parcourant le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [En effectuant une recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)

Une fois que vous avez trouvé la ressource que vous recherchez, vous pouvez visualiser tous ses détails, la modifier ou la supprimer, comme décrit dans les sections suivantes.

## <a name="asset-details-tabs-explained"></a>Explication des onglets des détails de la ressource

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="Onglets des détails de la ressource":::

- **Vue d’ensemble** - L’onglet Vue d’ensemble couvre les détails de base d’une ressource, comme la description, la classification, la hiérarchie et les termes du glossaire.
- **Propriétés** - L’onglet Propriétés couvre à la fois les propriétés de base et les propriétés avancées relatives à une ressource.
- **Schéma** - Le schéma de la ressource, notamment les noms de colonnes, les types de données, les classifications au niveau des colonnes, les termes et les descriptions, est représenté sous l’onglet Schéma.
- **Traçabilité** - Cet onglet contient les détails du graphique de traçabilité pour les ressources où il est disponible.
- **Contacts** - Chaque ressource peut être associée à un propriétaire et à un expert, qui peuvent être visualisés et gérés depuis l’onglet Contacts.
- **Associé** - Cet onglet vous permet d’accéder aux ressources associées à la ressource que vous visualisez. 

## <a name="asset-overview"></a>Vue d’ensemble de la ressource
La section Vue d’ensemble des détails de la ressource vous donne une vue résumée d’une ressource. Les sections qui suivent expliquent les différentes parties de la page Vue d’ensemble.

### <a name="asset-hierarchy"></a>Hiérarchie de la ressource

Vous pouvez visualiser la hiérarchie complète de ma ressource sous l’onglet Vue d’ensemble. Par exemple, si vous accédez à une table SQL, vous pouvez voir le schéma, la base de données et le serveur auquel la table appartient.

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="Hiérarchie de la ressource":::

### <a name="asset-classifications"></a>Classifications de la ressource

Les classifications de la ressource identifient le type des données qui sont représentées ; elles sont appliquées manuellement ou lors d’une analyse. Par exemple : un ID national ou un numéro de passeport sont des classifications prises en charge. (Pour obtenir la liste complète des classifications, consultez la [page des classifications prises en charge](supported-classifications.md).) L’onglet Vue d’ensemble reflète à la fois les classifications au niveau de la ressource et les classifications au niveau des colonnes qui ont été appliquées, ce que vous pouvez aussi afficher dans le cadre du schéma.

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="Classifications de la ressource":::

### <a name="asset-description"></a>Description de la ressource

Vous pouvez visualiser la description d’une ressource dans la section Vue d’ensemble. Vous pouvez ajouter une description d’une ressource en [modifiant la ressource](#editing-assets).

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="Description de la ressource":::

### <a name="asset-glossary-terms"></a>Termes du glossaire des ressources

Les termes du glossaire des ressources sont un vocabulaire géré pour les termes métiers, qui peuvent être utilisés pour classer et associer des ressources dans votre environnement. Par exemple, des termes comme « client », « acheteur », « centre de coût » ou tout autre terme qui donne un contexte à vos données pour vos utilisateurs. Pour plus d’informations, consultez la [page Glossaire métier](concept-business-glossary.md). Vous pouvez visualiser les termes du glossaire d’une ressource dans la section Vue d’ensemble, et vous pouvez ajouter un terme de glossaire à une ressource en [modifiant la ressource](#editing-assets).

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="Termes du glossaire des ressources":::

## <a name="editing-assets"></a>Modification des ressources

Vous pouvez modifier une ressource en sélectionnant l’icône Modifier en haut à gauche de la ressource.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="Boutons de modification et de suppression d’une ressource":::

Au niveau de la ressource, vous pouvez modifier ou ajouter une description, une classification ou un terme de glossaire en restant sur l’onglet Vue d’ensemble de l’écran de modification.

Vous pouvez accéder à l’onglet Schéma dans l’écran de modification pour mettre à jour un nom de colonne, le type de données, la classification au niveau des colonnes, des termes ou la description de la ressource.

Vous pouvez accéder à l’onglet Contact de l’écran de modification pour mettre à jour les propriétaires et les experts de la ressource. Vous pouvez effectuer une recherche par nom complet, par e-mail ou par alias de la personne dans votre annuaire Azure Active Directory.

### <a name="scans-on-edited-assets"></a>Analyses sur des ressources modifiées

Si vous modifiez une ressource en ajoutant une description, une classification au niveau de la ressource, un terme de glossaire ou un contact, des analyses ultérieures continueront de mettre à jour le schéma de la ressource (nouvelles colonnes et classifications détectées par l’analyseur lors d’analyses subséquentes).

Si vous effectuez des mises à jour de niveau de colonne, comme ajouter une description, une classification au niveau de la colonne ou un terme du glossaire, les analyses suivantes continueront également de mettre à jour le schéma de la ressource (les nouvelles colonnes et classifications seront détectées par l’analyseur lors d’analyses subséquentes). 

Même sur des ressources modifiées, après analyse, Azure Purview reflète la vérité du système source. Par exemple, si vous modifiez une colonne et que celle-ci est supprimée de la source, elle est supprimée de votre ressource dans Purview. 

>[!NOTE]
> Si vous mettez à jour le **nom ou le type de données d’une colonne** dans une ressource Azure Purview, les analyses ultérieures **ne mettent pas** à jour le schéma de la ressource. Les nouvelles colonnes et classifications **ne sont pas** détectées.

## <a name="deleting-assets"></a>Suppression de ressources

Vous pouvez supprimer une ressource en sélectionnant l’icône Supprimer sous le nom de la ressource.

### <a name="delete-behavior-explained"></a>Explication du comportement des suppressions

Les ressources que vous supprimez en utilisant le bouton Supprimer sont définitivement supprimées dans Azure Purview. Cependant, si vous exécutez une **analyse complète** sur la source à partir de laquelle la ressource a été ingérée dans le catalogue, la ressource est réingérée et vous pouvez la découvrir en utilisant le catalogue Purview.

Si vous avez une analyse planifiée (hebdomadaire ou mensuelle) sur la source, la **ressource supprimée n’est pas réingérée** dans le catalogue à moins que la ressource n’ait été modifiée par un utilisateur final depuis la dernière exécution de l’analyse.   Par exemple, si une table SQL a été supprimée de Purview, mais qu’après la suppression de la table, un utilisateur a ajouté une nouvelle colonne à la table dans SQL, lors de l’analyse suivante, la ressource est réanalysée et ingérée dans le catalogue.

Si vous supprimez une ressource, seule cette ressource est supprimée. Purview ne prend pas actuellement pas en charge les suppressions en cascade. Par exemple, si vous supprimez une ressource de compte de stockage dans votre catalogue, les conteneurs, les dossiers et les fichiers qu’elle contient ne sont pas supprimés. 


## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
