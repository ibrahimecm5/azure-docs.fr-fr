---
title: Présentation des jeux de ressources
description: Cet article explique ce que sont les jeux de ressources et comment Azure Purview les crée.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/24/2021
ms.openlocfilehash: 4e15f2813a0aea04f63cc11b3273c4a91d44afb2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845871"
---
# <a name="understanding-resource-sets"></a>Présentation des jeux de ressources

Cet article vous aide à comprendre comment Azure Purview utilise des jeux de ressources pour mapper des ressources de données à des ressources logiques.

## <a name="background-info"></a>Informations générales

Les systèmes de traitement de données à grande échelle stockent généralement une table unique sur un stockage en tant que fichiers multiples. Dans le catalogue de données Azure Purview, ce concept est représenté à l’aide de jeux de ressources. Un jeu de ressources est un objet unique dans le catalogue qui représente un grand nombre de ressources dans le stockage.

Supposons par exemple que votre cluster Spark a rendu persistant un DataFrame dans une source de données Azure Data Lake Storage (ADLS) Gen2. Bien que dans Spark, le tableau ressemble à une ressource logique unique, sur le disque, il y a probablement des milliers de fichiers Parquet, chacun représentant une partition du contenu total de DataFrame. Les données IoT et les données de journal web sont confrontées au même défi. Imaginez que vous avez un capteur qui génère des fichiers journaux plusieurs fois par seconde. Cela ne prendra pas trop de temps tant que vous n’aurez pas de centaines de milliers de fichiers journaux à partir de ce capteur unique.

## <a name="how-azure-purview-detects-resource-sets"></a>Comment Azure Purview détecte les jeux de ressources

Azure Purview prend en charge la détection des ensembles de ressources dans Stockage Blob Azure, ADLS Gen1, ADLS Gen2, Azure Files et Amazon S3.

Azure Purview détecte automatiquement les jeux de ressources lors de l’analyse. Cette fonctionnalité examine toutes les données qui sont ingérées par le biais de l’analyse et les compare à un ensemble de modèles définis.

Par exemple, supposons que vous analysiez une source de données dont l’URL est `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet`. Azure Purview examine les segments de ligne et détermine s’ils correspondent à des modèles intégrés. Il possède des modèles intégrés pour les GUID, les nombres, les formats de date, les codes de localisation (par exemple, en-us), etc. Dans ce cas, le modèle de nombre correspond à *23*. Azure Purview part du principe que ce fichier fait partie d’un jeu de ressources nommé `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet`.

Ou, pour une URL telle que `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json`, Azure Purview correspond à la fois au modèle de localisation et au modèle de nombre, ce qui génère un jeu de ressources nommé `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`.

À l’aide de cette stratégie, Azure Purview mappe les ressources suivantes au même jeu de ressources, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

### <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Types de fichiers qui ne seront pas détectés par Azure Purview en tant que jeux de ressources

Purview ne tente pas de classer la plupart des types de fichiers de document tels que Word, Excel ou PDF comme jeux de ressources. L’exception est le format CSV, car il s’agit d’un format de fichier partitionné courant.

## <a name="how-azure-purview-scans-resource-sets"></a>Comment Azure Purview analyse les jeux de ressources

Quand Azure Purview détecte des ressources qu’il considère comme faisant partie d’un jeu de ressources, il passe d’une analyse complète à un exemple d’analyse. Un exemple d’analyse ouvre uniquement un sous-ensemble des fichiers qu’il pense se trouver dans le jeu de ressources. Pour chaque fichier qu’il ouvre, il utilise son schéma et exécute ses classifieurs. Azure Purview recherche ensuite la ressource la plus récente parmi les ressources ouvertes et utilise le schéma et les classifications de cette ressource dans l’entrée pour l’ensemble des ressources dans le catalogue.

## <a name="advanced-resource-sets"></a>Jeux de ressources avancés

Par défaut, Azure Purview détermine le schéma et les classifications des jeux de ressources en fonction des [règles d’échantillonnage du fichier de jeu de ressources](sources-and-scans.md#resource-set-file-sampling). Azure Purview peut personnaliser et enrichir davantage vos ressources de jeu de ressources grâce à la fonctionnalité **Jeux de ressources avancés**. Lorsque les jeux de ressources avancés sont activés, Azure Purview exécute des agrégations supplémentaires pour calculer les informations suivantes sur les ressources de jeu de ressources :

- Schéma et classifications les plus à jour pour refléter avec précision la dérive du schéma des métadonnées modifiées.
- Exemple de chemin d’accès à partir d’un fichier qui inclut le jeu de ressources.
- Nombre de partitions qui indique le nombre de fichiers qui composent le jeu de ressources. 
- Nombre de schémas qui affiche combien de schémas uniques ont été trouvés. Cette valeur est un nombre compris entre 1 et 5, ou pour les valeurs supérieures à 5, 5+.
- Liste de types de partition lorsque plusieurs types de partition sont inclus dans l’ensemble de ressources. Par exemple, un capteur IoT peut produire à la fois des fichiers XML et JSON, bien que les deux soient logiquement intégrés au même jeu de ressources.
- Taille totale de tous les fichiers qui composent le jeu de ressources. 

Ces propriétés se trouvent sur la page de détails de la ressource du jeu de ressources.

:::image type="content" source="media/concept-resource-sets/resource-set-properties.png" alt-text="Les propriétés calculées lorsque les jeux de ressources avancés sont activés" border="true":::

L’activation des jeux de ressources avancés permet également de créer des [règles de modèle de jeu de ressources](how-to-resource-set-pattern-rules.md) qui permettent de personnaliser la façon dont Azure Purview regroupe les jeux de ressources lors de l’analyse. 

### <a name="turning-on-advanced-resource-sets"></a>Activation des jeux de ressources avancés

Les jeux de ressources avancés sont désactivés par défaut dans toutes les nouvelles instances Azure Purview. Les jeux de ressources avancés peuvent être activés à partir des **informations de compte** dans le hub de gestion.

:::image type="content" source="media/concept-resource-sets/advanced-resource-set-toggle.png" alt-text="Activez un jeu de ressources avancé." border="true":::

Après l’activation des jeux de ressources avancés, les enrichissements supplémentaires se produisent sur toutes les ressources nouvellement ingérées. L’équipe Azure Purview recommande d’attendre une heure avant d’analyser les nouvelles données du lac de données après avoir activé la fonctionnalité.

> [!IMPORTANT]
> L’activation de jeux de ressources avancés aura un impact sur le taux d’actualisation des informations sur les ressources et la classification. Lorsque les jeux de ressources avancées sont activés, les informations sur les ressources et la classification ne sont mises à jour que deux fois par jour.

## <a name="built-in-resource-set-patterns"></a>Modèles de jeu de ressources intégrés

Azure Purview prend en charge les modèles de jeu de ressources suivants. Ces modèles peuvent apparaître sous la forme d’un nom dans un répertoire ou en tant que partie d’un nom de fichier.
### <a name="regex-based-patterns"></a>Modèles basés sur une expression régulière

| Nom du modèle | Nom d’affichage | Description |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Identificateur global unique, tel que défini dans la norme [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Nombre       | {N}          | Un ou plusieurs chiffres. |
| Formats de date et heure | {Year}{Month}{Day}{N}     | Nous prenons en charge différents formats de date et d’heure, mais tous sont représentés par {Year}[delimiter]{Month}[delimiter]{Day} ou une série de {N}. |
| 4ByteHex     | {HEX}        | Nombre hexadécimal à quatre chiffres. |
| Localisation | {LOC}        | Une balise de langue telle que définie dans la norme [BCP 47](https://tools.ietf.org/html/bcp47), les noms contenant les caractères « - » et « _ » sont pris en charge (par exemple, en_ca et en-ca). |

### <a name="complex-patterns"></a>Modèles complexes

| Nom du modèle | Nom d’affichage | Description |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificateur du fichier de partition Spark |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | Modèle année/mois/jour couvrant plusieurs dossiers |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-data-catalog"></a>Affichage des jeux de ressources dans le catalogue de données Azure Purview

Quand Azure Purview correspond à un groupe de ressources dans un jeu de ressources, il tente d’extraire les informations les plus utiles à utiliser comme nom d’affichage dans le catalogue. Voici quelques exemples de la convention d’affectation de noms par défaut : 

### <a name="example-1"></a>Exemple 1

Nom qualifié : `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nom d’affichage : « nom de la sortie Spark »

### <a name="example-2"></a>Exemple 2

Nom qualifié : `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nom d’affichage : « mes données partitionnées »

### <a name="example-3"></a>Exemple 3

Nom qualifié : `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nom d’affichage : « données »

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Personnalisation du regroupement de jeux de ressources à l’aide de règles de modèle

Lors de l’analyse d’un compte de stockage, Azure Purview se sert d’un ensemble de modèles définis pour déterminer si un groupe d’éléments constitue un jeu de ressources. Dans certains cas, le regroupement en jeu de ressources d’Azure Purview peut ne pas refléter précisément votre patrimoine de données. Voici des exemples de problèmes potentiels :

- marque de manière incorrecte une ressource comme étant un jeu de ressources ;
- place une ressource dans le mauvais jeu de ressources ;
- marque de manière incorrecte une ressource comme n’étant pas un jeu de ressources.

Pour personnaliser ou remplacer la manière dont Azure Purview détecte les ressources regroupées comme jeux de ressources et leur mode d’affichage dans le catalogue, vous pouvez définir des règles de modèle dans le centre de gestion. Pour obtenir des instructions détaillées ainsi que la syntaxe, consultez les [règles de modèle de jeu de ressources](how-to-resource-set-pattern-rules.md).

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main Azure Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
