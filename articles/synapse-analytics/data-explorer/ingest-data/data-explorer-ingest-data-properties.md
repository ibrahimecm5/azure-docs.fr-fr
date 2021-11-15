---
title: Propriétés d’ingestion des données pour l’explorateur de données Azure Synapse (préversion)
description: Découvrez les différentes propriétés d’ingestion des données prises en charge par l’explorateur de données Azure Synapse.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e598f3fdd03721def28ac8a0f90e3bd12958783c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479000"
---
# <a name="azure-synapse-data-explorer-data-ingestion-properties-preview"></a>Propriétés d’ingestion de données d’explorateur de données Azure Synapse (préversion)

L’ingestion des données est le processus par lequel les données sont ajoutées à une table et deviennent disponibles pour une requête dans l’explorateur de données. Vous ajoutez des propriétés à la commande d’ingestion après le mot clé `with`.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Le tableau suivant répertorie les propriétés prises en charge par l’explorateur de données, fournit une description et des exemples : 

|Propriété              |Description                                              |Exemple                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Valeur de chaîne qui indique comment mapper les données du fichier source aux colonnes réelles de la table. Définissez la valeur `format` avec le type de mappage approprié. Consultez [Mappages de données](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(sont dépréciés : `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Valeur de chaîne qui indique comment mapper les données du fichier source aux colonnes réelles de la table en utilisant un objet de stratégie de mappage nommé. Définissez la valeur `format` avec le type de mappage approprié. Consultez [Mappages de données](/azure/data-explorer/kusto/management/mappings?context=/azure/synapse-analytics/context/context).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(sont dépréciés : `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Valeur DateHeure (sous forme de chaîne ISO8601) à utiliser comme heure de création des étendues de données ingérées. Si elle n’est pas spécifiée, la valeur actuelle (`now()`) est utilisée. En cas d’ingestion de données anciennes, vous pouvez remplacer les valeurs par défaut pour que la stratégie de conservation soit appliquée correctement. Lorsqu’elle est spécifiée, assurez-vous que la propriété `Lookback` de la [stratégie de fusion des étendues](/azure/data-explorer/kusto/management/mergepolicy?context=/azure/synapse-analytics/context/context) effective de la table cible est alignée sur la valeur spécifiée.|`with (creationTime="2017-02-13")`|
|`extend_schema`|Valeur booléenne qui, si elle est spécifiée, indique à la commande d’étendre le schéma de la table (la valeur par défaut est `false`). Cette option s’applique uniquement aux commandes `.append` et `.set-or-append`. Les seules extensions de schéma autorisées sont celles pour lesquelles des colonnes supplémentaires sont ajoutées à la fin de la table.|Si le schéma de la table d’origine est `(a:string, b:int)`, une extension de schéma valide est `(a:string, b:int, c:datetime, d:string)` et non `(a:string, c:datetime)`|
|`folder` |Pour les commandes [ingest-from-query](/azure/data-explorer/kusto/management/data-ingestion/ingest-from-query?context=/azure/synapse-analytics/context/context), dossier à attribuer à la table. Si la table existe déjà, cette propriété remplace le dossier de la table.|`with (folder="Tables/Temporary")`|
|`format` |Format des données (voir [formats de données pris en charge](data-explorer-ingest-data-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Valeur de chaîne qui, si elle est spécifiée, empêche l’ingestion de s’effectuer correctement si la table a déjà des données balisées avec une balise `ingest-by:` de la même valeur. Cela garantit une ingestion des données idempotent. Pour plus d’informations, consultez [Étiquettes ingest-by:](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags).|Les propriétés `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` indiquent que si des données existent déjà avec l’étiquette `ingest-by:Part0001`, vous ne devez pas effectuer l’ingestion actuelle. Si elles n’existent pas déjà, l’étiquette doit être définie dans la nouvelle ingestion (au cas où une future ingestion tente d’ingérer une nouvelle fois les mêmes données).|
|`ignoreFirstRecord` |Valeur booléenne qui, si elle a la valeur `true`, indique que l’ingestion doit ignorer le premier enregistrement de chaque fichier. Cette propriété est utile pour les fichiers au format `CSV` (et similaires) si le premier enregistrement dans le fichier représente les noms de colonne. Par défaut, la valeur `false` est supposée.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Valeur booléenne qui, si elle est spécifiée, indique que la commande doit rendre persistants les résultats détaillés (même en cas de réussite) afin que la commande [.show operation details](/azure/data-explorer/kusto/management/operations?context=/azure/synapse-analytics/context/context#show-operation-details) puisse les récupérer. La valeur par défaut est `false`.|`with (persistDetails=true)`|
|`policy_ingestiontime`|Valeur booléenne qui, si elle est spécifiée, indique d’activer ou non la [stratégie de durée d’ingestion](/azure/data-explorer/kusto/management/ingestiontimepolicy?context=/azure/synapse-analytics/context/context) sur une table créée par cette commande. Par défaut, il s’agit de `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Valeur booléenne qui, si elle est spécifiée, indique si la commande peut recréer ou non le schéma de la table. Cette propriété s’applique uniquement à la commande `.set-or-replace`. Cette propriété est prioritaire sur la propriété `extend_schema` si les deux sont définies.|`with (recreate_schema=true)`|
|`tags`|Liste d’[étiquettes](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) à associer aux données ingérées, sous forme de chaîne JSON |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Chaîne JSON qui indique les validations à exécuter pendant l’ingestion. Consultez [Ingestion des données](data-explorer-ingest-data-overview.md) pour avoir une explication des différentes options.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')` (stratégie par défaut)|
|`zipPattern`|Utilisez cette propriété en cas d’ingestion des données à partir d’un stockage qui a une archive ZIP. Il s’agit d’une valeur de chaîne indiquant l’expression régulière à utiliser pour sélectionner les fichiers de l’archive ZIP à ingérer.  Tous les autres fichiers de l’archive sont ignorés.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[ingestion des données](data-explorer-ingest-data-overview.md).
- En savoir plus sur les [formats de données pris en charge](data-explorer-ingest-data-supported-formats.md).
