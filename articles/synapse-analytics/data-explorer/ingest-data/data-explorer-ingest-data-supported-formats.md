---
title: Formats de données pris en charge par Azure Synapse Data Explorer pour l’ingestion.
description: Découvrez les différents formats de données et de compression pris en charge par Azure Synapse Data Explorer pour l’ingestion.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: f38325e37b548c7f8a99d38eacae26aa24756250
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478964"
---
# <a name="data-formats-supported-by-azure-synapse-data-explorer-for-ingestion-preview"></a>Formats de données pris en charge par Azure Synapse Data Explorer pour l’ingestion (préversion)

L’ingestion des données est le processus par lequel les données sont ajoutées à une table et deviennent disponibles pour une requête dans Data Explorer. Pour toutes les méthodes d’ingestion autres que l’ingestion à partir d’une requête, les données doivent être dans l’un des formats pris en charge. Le tableau suivant liste et décrit les formats pris en charge par Data Explorer pour l’ingestion de données.

> [!NOTE]
> Avant d’ingérer des données, vérifiez que celles-ci sont correctement mises en forme et qu’elles définissent les champs attendus. Nous vous recommandons d’utiliser votre validateur habituel pour vérifier que le format est valide. Par exemple, vous pouvez trouver les validateurs suivants utiles pour la vérification des fichiers CSV ou JSON :
>
> * CSV : http://csvlint.io/
> * JSON : https://jsonlint.com/
>
> Pour en savoir plus sur ce qui provoque l’échec de l’ingestion, consultez [Échec de l’ingestion](/azure/data-explorer/kusto/management/ingestionfailures?context=/azure/synapse-analytics/context/context) et [Codes d’erreur d’ingestion dans Data Explorer](/azure/data-explorer/error-codes?context=/azure/synapse-analytics/context/context).

|Format   |Extension   |Description|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |Format [AVRO](https://avro.apache.org/docs/current/) avec prise en charge des [types logiques](https://avro.apache.org/docs/current/spec.html#Logical+Types). Les codecs de compression suivants sont pris en charge : `null`, `deflate` et `snappy`. L’implémentation du lecteur du format `apacheavro` est basée sur la [bibliothèque Apache Avro](https://github.com/apache/avro) officielle.|
|Avro     |`.avro`     |Implémentation héritée du format [AVRO](https://avro.apache.org/docs/current/) basée sur la [bibliothèque .NET](https://www.nuget.org/packages/Microsoft.Hadoop.Avro). Les codecs de compression suivants sont pris en charge : `null`, `deflate` (pour `snappy`, utilisez le format de données `ApacheAvro`).|
|CSV      |`.csv`      |Fichier texte avec des valeurs séparées par des virgules (`,`). Consultez [RFC 4180 : _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Fichier texte avec des objets JSON délimités par `\n` ou `\r\n`. Consultez [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Fichier texte avec un tableau JSON de conteneurs des propriétés (représentant chacun un enregistrement) ou n’importe quel nombre de conteneurs des propriétés délimités par des espaces, `\n` ou `\r\n`. Chaque conteneur de propriétés peut être réparti sur plusieurs lignes. Ce format est préférable à `JSON`, sauf si les données ne sont pas des conteneurs de propriétés.|
|ORC      |`.orc`      |[Fichier ORC](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |[Fichier Parquet](https://en.wikipedia.org/wiki/Apache_Parquet). |
|PSV      |`.psv`      |Fichier texte avec des valeurs séparées par des barres verticales (<code>&#124;</code>).|
|RAW      |`.raw`      |Fichier texte dont le contenu entier est une valeur de chaîne unique.|
|SCsv     |`.scsv`     |Fichier texte avec des valeurs séparées par des points-virgules (`;`).|
|SOHsv    |`.sohsv`    |Fichier texte avec des valeurs séparées par SOH. (SOH est le point de code ASCII 1 ; ce format est utilisé par Hive sur HDInsight.)|
|TSV      |`.tsv`      |Fichier texte avec des valeurs séparées par des tabulations (`\t`).|
|TSVE     |`.tsv`      |Fichier texte avec des valeurs séparées par des tabulations (`\t`). Une barre oblique inverse (`\`) est utilisée pour l’échappement.|
|TXT      |`.txt`      |Fichier texte dont les lignes sont délimitées par `\n`. Les lignes vides sont ignorées.|
|W3CLOGFILE |`.log`    |Format du [fichier journal web](https://www.w3.org/TR/WD-logfile.html) standardisé par le W3C. |

## <a name="supported-data-compression-formats"></a>Formats de compression de données pris en charge

Les objets blob et les fichiers peuvent être compressés par l’un des algorithmes de compression suivants :

|Compression|Extension|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Indiquez la compression en ajoutant l’extension au nom de l’objet blob ou du fichier.

Par exemple :

* `MyData.csv.zip` indique un objet blob ou un fichier au format CSV, compressé avec ZIP (archive ou fichier unique)
* `MyData.json.gz` indique un objet blob ou un fichier au format JSON, compressé avec GZip.

Les noms d’objets blob ou de fichiers qui n’incluent pas les extensions de format mais simplement la compression (par exemple, `MyData.zip`) sont également pris en charge. Dans ce cas, le format de fichier doit être spécifié en tant que propriété d’ingestion, car il ne peut pas être déduit.

> [!NOTE]
> * Certains formats de compression assurent le suivi de l’extension de fichier d’origine dans le cadre du flux compressé. Cette extension est généralement ignorée lors du processus de vérification du format de fichier. S’il n’est pas possible de déterminer le format de fichier à partir du nom de l’objet blob ou du fichier (compressé), vous devez le spécifier par le biais de la propriété d’ingestion `format`.
> * À ne pas confondre avec le codec de compression interne (au niveau du bloc) utilisé par les formats `Parquet`, `AVRO` et `ORC`. Le nom de la compression interne est généralement ajouté à un nom de fichier avant l’extension de format de fichier, par exemple `file1.gz.parquet`, `file1.snappy.avro`, etc.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur l’[ingestion de données](data-explorer-ingest-data-overview.md).
- Découvrez-en plus sur les [propriétés d’ingestion de données](data-explorer-ingest-data-properties.md)
