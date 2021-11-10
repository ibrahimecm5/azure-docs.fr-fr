---
title: Indexer des données à l’aide de connecteurs Power Query (préversion)
titleSuffix: Azure Cognitive Search
description: Importez des données à partir de différentes sources de données à l’aide des connecteurs Power Query.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 5ff733cca02d8c4ca9d3014296cffd8db87c3c3a
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892814"
---
# <a name="index-data-using-power-query-connectors-preview"></a>Indexer des données à l’aide de connecteurs Power Query (préversion)

> [!IMPORTANT] 
> La prise en charge des connecteurs Power Query est actuellement disponible en **préversion publique contrôlée**. [Inscrivez-vous](https://aka.ms/azure-cognitive-search/indexer-preview) pour demander un accès.

Si vous utilisez un indexeur pour analyser des sources de données externes à des fins d’indexation, vous pouvez désormais utiliser certains connecteurs [Power Query](/power-query/power-query-what-is-power-query) pour votre connexion à la source de données dans Recherche cognitive Azure.

Les connecteurs Power Query peuvent atteindre un éventail plus large de sources de données, y compris celles d’autres fournisseurs de cloud. Les nouvelles sources de données prises en charge dans cette préversion comprennent :

+ Amazon Redshift
+ Elasticsearch
+ PostgreSQL
+ Objets Salesforce
+ Rapports Salesforce
+ Smartsheet
+ Snowflake

Cet article présente une approche basée sur le portail Azure pour configurer un indexeur à l’aide de connecteurs Power Query. Il n’existe actuellement aucune prise en charge du Kit de développement logiciel (SDK).

> [!NOTE]
> Les fonctionnalités d’évaluation sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-functionality"></a>Fonctionnalités prises en charge
Les connecteurs Power Query sont utilisés dans les indexeurs. Dans Recherche cognitive Azure, un indexeur est un analyseur qui extrait les données et métadonnées pouvant faire l’objet d’une recherche d’une source de données externe et alimente un index en fonction des mappages champ à champ entre l’index et votre source de données. Cette approche est parfois appelée « modèle d’extraction », car le service extrait des données sans que vous ayez à écrire un code qui ajoute des données à un index. Les indexeurs offrent aux utilisateurs un moyen pratique d’indexer le contenu de leur source de données sans avoir à écrire leur propre analyseur ou modèle de transmission de type push.

Les indexeurs qui référencent les sources de données Power Query proposent le même niveau de prise en charge des ensembles de compétences, des planifications, de la logique de détection des modifications de la limite supérieure et de la plupart des paramètres pris en charge par d’autres indexeurs.

## <a name="prerequisites"></a>Prérequis
Avant de commencer à extraire des données de l’une des sources de données prises en charge, vous devez vous assurer que toutes vos ressources sont configurées.
+ Service Recherche cognitive Azure
    + Le service Recherche cognitive Azure configuré dans une [région prise en charge](search-how-to-index-power-query-data-sources.md#regional-availability).
    + Assurez-vous que l’équipe de Recherche cognitive Azure a activé votre service de recherche pour la préversion. Vous pouvez vous inscrire à la préversion en remplissant [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). 
+ Compte Stockage Blob Azure
    + Un compte Stockage Blob Azure est requis pour que la préversion soit utilisée comme intermédiaire pour vos données. Les données circuleront de votre source de données vers Stockage Blob, puis vers l’index. Cette exigence existe uniquement pour la préversion contrôlée initiale.

## <a name="getting-started-using-the-azure-portal"></a>Bien démarrer à l’aide du portail Azure
Le portail Azure prend en charge les connecteurs Power Query. En analysant les données et en lisant les métadonnées dans le conteneur, l’Assistant Importation de données peut créer un index par défaut, mapper des champs sources aux champs d’index cibles et charger l’index en une seule opération. Selon la taille et la complexité de la source de données, vous pouvez obtenir un index de recherche en texte intégral opérationnel en quelques minutes.

 La vidéo suivante montre comment configurer un connecteur Power Query dans Recherche cognitive Azure.
 
> [!VIDEO https://www.youtube.com/embed/uy-l4xFX1EE]

### <a name="step-1--prepare-source-data"></a>Étape 1 : Préparer les données sources
Assurez-vous que votre source de données contient des données. L’Assistant Importation de données lit les métadonnées et effectue un échantillonnage des données pour déduire un schéma d’index, mais il charge également des données à partir de votre source de données. Si les données sont manquantes, l’Assistant s’arrête et renvoie une erreur. 

### <a name="step-2--start-import-data-wizard"></a>Étape 2 : Démarrer l’Assistant Importation de données
Une fois que votre demande d’accès à la préversion aura été approuvée, l’équipe de Recherche cognitive Azure vous fournira un lien vers le portail Azure qui utilise un indicateur de fonctionnalité afin que vous puissiez accéder aux connecteurs Power Query. Ouvrez cette page et démarrez l’Assistant à partir de la barre de commandes dans la page du service Recherche cognitive Azure en sélectionnant **Importer des données**.

:::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Capture d’écran de la commande Importer des données" border="true":::

### <a name="step-3--select-your-data-source"></a>Étape 3 : Sélectionner votre source de données
Il existe quelques sources de données à partir desquelles vous pouvez extraire des données à l’aide de cette préversion. Toutes les sources de données qui utilisent Power Query incluent une mention « Alimenté par Power Query » sur leur vignette. Sélectionnez votre source de données. 
 
![Sélectionner une source de données](./media/search-power-query-connectors/power-query-import-data.png "Sélectionner une source de données")

Une fois que vous avez sélectionné votre source de données, sélectionnez **Suivant : Configurer vos données** pour passer à la section suivante.

### <a name="step-4--configure-your-data"></a>Étape 4 : Configurer vos données
Une fois que vous avez sélectionné votre source de données, vous allez configurer votre connexion. Chaque source de données nécessitera des informations différentes. Pour quelques sources de données, la documentation de Power Query fournit des informations supplémentaires sur la façon de vous connecter à vos données. 

+ [PostgreSQL](/power-query/connectors/postgresql)
+ [Objets Salesforce](/power-query/connectors/salesforceobjects)
+ [Rapports Salesforce](/power-query/connectors/salesforcereports)

Une fois que vous avez fourni vos informations d’identification de connexion, sélectionnez **Suivant**.

### <a name="step-5--select-your-data"></a>Étape 5 : Sélectionner vos données
L’Assistant Importation affiche un aperçu des différentes tables disponibles dans votre source de données. Dans cette étape, vous allez sélectionner une table qui contient les données que vous souhaitez importer dans votre index.
 
![Aperçu de vos données](./media/search-power-query-connectors/power-query-preview-data.png "Prévisualiser vos données")

Une fois que vous avez sélectionné votre table, sélectionnez **Suivant**.

### <a name="step-6--transform-your-data-optional"></a>Étape 6 : Transformer vos données (facultatif)
Les connecteurs Power Query vous offrent une expérience d’interface utilisateur enrichie qui vous permet de manipuler vos données pour que vous puissiez envoyer les bonnes données à votre index. Vous pouvez supprimer des colonnes, filtrer les lignes et bien plus encore. 

Il n’est pas nécessaire de transformer vos données avant de les importer dans Recherche cognitive Azure.

![Transformer vos données](./media/search-power-query-connectors/power-query-transform-your-data.png "Transformer vos données") 

Pour plus d’informations sur la transformation des données avec Power Query, consultez [Utilisation de Power Query dans Power BI Desktop](/power-query/power-query-quickstart-using-power-bi). 

Une fois que vous avez terminé de transformer vos données, sélectionnez **Suivant**.

### <a name="step-7--add-azure-blob-storage"></a>Étape 7 : Ajouter un stockage BLOB Azure
La préversion des connecteurs Power Query vous oblige actuellement à fournir un compte de stockage BLOB. Cette étape existe uniquement pour la préversion contrôlée initiale. Ce compte de stockage BLOB servira de stockage temporaire pour les données qui passent de votre source de données à un index Recherche cognitive Azure.

Nous vous recommandons de fournir une chaîne de connexion de compte de stockage avec accès complet : 
```
{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }
```

Vous pouvez obtenir la chaîne de connexion sur le portail Azure en sélectionnant le panneau du compte de stockage > Paramètres > Clés (pour les comptes de stockage Classic) ou en sélectionnant Paramètres > Clés d’accès (pour les comptes de stockage ARM).

Une fois que vous avez fourni un nom de source de données et une chaîne de connexion, sélectionnez « Suivant : Ajouter des compétences cognitives (facultatif) ». 

### <a name="step-8--add-cognitive-skills-optional"></a>Étape 8 : Ajouter des compétences cognitives (facultatif)
L’[enrichissement par IA](cognitive-search-concept-intro.md) est une extension des indexeurs qui peut être utilisée pour faciliter la recherche dans votre contenu.

Il s’agit d’une étape facultative pour cette préversion. Lorsque vous avez terminé, sélectionnez **Suivant : Personnaliser l’index cible**.

### <a name="step-9--customize-target-index"></a>Étape 9 : Personnaliser l’index cible
Sur la page Index, vous devez voir une liste de champs avec un type de données et une série de cases à cocher permettant de définir les attributs de l’index. L’Assistant peut générer une liste de champs basée sur les métadonnées et en échantillonnant les données sources.

Vous pouvez sélectionner des attributs en bloc en cliquant sur la case à cocher en haut de la colonne d’attribut. Choisissez Récupérable et Possibilité de recherche pour chaque champ qui doit être retourné vers une application cliente et soumis à un traitement de recherche de texte intégral. Vous remarquerez que les entiers ne peuvent pas être recherchés en texte intégral ou partiel (les nombres sont évalués textuellement et sont généralement utiles dans les filtres).

Pour plus d’informations, passez en revue la description des attributs d’index et des analyseurs de langage.

Prenez un moment pour passer en revue vos sélections. Une fois que vous exécutez l’Assistant, des structures de données physiques sont créées et vous ne pourrez pas modifier la plupart des propriétés de ces champs sans supprimer et recréer tous les objets.

![Créer votre index](./media/search-power-query-connectors/power-query-index.png "Créer votre index")

Lorsque vous avez terminé, sélectionnez **Suivant : Créer un indexeur**.

### <a name="step-10--create-an-indexer"></a>Étape 10 : Créer un indexeur
La dernière étape consiste à créer l’indexeur. Le fait de nommer l’indexeur lui permet d’exister comme ressource autonome, que vous pouvez planifier et gérer indépendamment de l’objet index et de l’objet source de données, créés dans la même séquence de l’Assistant.

La sortie de l’Assistant Importation de données est un indexeur qui analyse votre source de données et importe les données que vous avez sélectionnées dans un index sur Recherche cognitive Azure.

Lors de la création de l’indexeur, vous pouvez choisir d’exécuter l’indexeur selon une planification et d’ajouter la détection des modifications. Pour ajouter la détection des modifications, désignez une colonne « Limite supérieure ».

![Créer votre indexeur](./media/search-power-query-connectors/power-query-indexer-configuration.png "Créer votre indexeur")

Une fois que vous avez terminé de renseigner cette page, sélectionnez **Envoyer**.

## <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection des modifications de limite supérieure
Cette stratégie de détection des modifications s’appuie sur une colonne « Limite supérieure » qui capture la version ou l’heure pour laquelle une ligne a été mise à jour.

### <a name="requirements"></a>Spécifications
+ Toutes les insertions spécifient une valeur pour la colonne.
+ Toutes les mises à jour d'un élément modifient également la valeur de la colonne.
+ La valeur de cette colonne augmente à chaque insertion ou mise à jour.

## <a name="unsupported-column-names"></a>Noms de colonne non pris en charge
Les noms de champs d’un index Recherche cognitive Azure doivent répondre à certaines exigences. L’une de ces exigences est que certains caractères tels que « / » ne sont pas autorisés. Si un nom de colonne dans votre base de données ne répond pas à ces exigences, la détection du schéma de l’index ne reconnaîtra pas votre colonne comme un nom de champ valide et vous ne verrez pas cette colonne répertoriée comme un champ suggéré pour votre index. Normalement, l’utilisation de [mappages de champs](search-indexer-field-mappings.md) résoudrait ce problème, mais les mappages de champs ne sont pas pris en charge par le portail.

Pour indexer le contenu d’une colonne de votre table dont le nom de champ n’est pas pris en charge, renommez la colonne pendant la phase « Transformer vos données » du processus d’importation de données. Par exemple, vous pouvez renommer une colonne nommée « Code de facturation/code postal » en « codepostal ». En renommant la colonne, la détection du schéma de l’index la reconnaîtra comme un nom de champ valide et l’ajoutera comme suggestion à votre définition d’index.

## <a name="regional-availability"></a>Disponibilité régionale
La préversion est disponible uniquement pour les clients disposant de services de recherche dans les régions suivantes :
+ USA Centre
+ USA Est
+ USA Est 2
+ Centre-Nord des États-Unis
+ Europe Nord
+ États-Unis - partie centrale méridionale
+ Centre-USA Ouest
+ Europe Ouest
+ USA Ouest
+ USA Ouest 2

## <a name="preview-limitations"></a>Limitations de la version préliminaire
Cette préversion offre de nombreuses raisons de se réjouir, mais elle comporte quelques limites. Cette section décrit les limites spécifiques à la version actuelle de la préversion.
+ Cette version de la préversion ne prend pas en charge l’extraction de données binaires à partir de votre source de données. 
+ Les [sessions de débogage](cognitive-search-debug-session.md) ne sont pas prises en charge pour le moment.

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à extraire des données à partir de nouvelles sources de données à l’aide des connecteurs Power Query. Pour en savoir plus sur les indexeurs, consultez [Indexeurs dans Recherche cognitive Azure](search-indexer-overview.md).