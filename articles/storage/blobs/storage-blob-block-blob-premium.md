---
title: Compte de stockage d’objets blob de blocs Premium
titleSuffix: Azure Storage
description: Obtenez des latences inférieures et cohérentes pour les charges de travail Stockage Azure qui requièrent des temps de réponse rapides et cohérents.
author: normesta
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8f394bb69197e9268fb986f7a8b971206aa74fc4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444835"
---
# <a name="premium-block-blob-storage-accounts"></a>Compte de stockage d’objets blob de blocs Premium

Les comptes de stockage d'objets blob de blocs Premium du niveau de performance Premium permettent d'accéder aux données à l'aide de matériel hautes performances. Les données sont stockées sur des disques SSD optimisés pour une latence faible. Les disques SSD offrent un débit plus élevé que les disques durs traditionnels. Le transfert de fichiers est beaucoup plus rapide, car les données sont stockées sur des puces mémoire accessibles instantanément. Toutes les parties d’un lecteur sont accessibles simultanément. En revanche, les performances d’un lecteur de disque dur (HDD) dépendent de la proximité des données aux têtes de lecture/écriture. 

## <a name="high-performance-workloads"></a>Charges de travail haute performance

Les comptes de stockage d’objets blob de blocs Premium sont parfaits pour les charges de travail qui nécessitent des temps de réponse rapides et cohérents et/ou un nombre élevé d’opérations d’entrée-sortie par seconde (IOP). Voici quelques exemples de charges de travail :

- **Charges de travail interactives**. Les applications hautement interactives et en temps réel doivent écrire des données rapidement. Les applications de commerce électronique et de cartographie requièrent souvent des mises à jour instantanées et des commentaires des utilisateurs. Par exemple, dans une application d’e-commerce, les éléments les moins fréquemment consultés ne sont probablement pas mis en cache. Toutefois, ils doivent être affichés instantanément au client sur demande. La modification interactive ou les applications de jeux en ligne multi-joueurs maintiennent une expérience de qualité en fournissant des mises à jour en temps réel.  

- **Analytique du streaming/de l’IoT**. Dans un scénario IoT, un grand nombre d’opérations d’écriture plus petites peuvent être envoyées (push) chaque seconde dans le cloud. De grandes quantités de données peuvent être saisies, agrégées à des fins d’analyse, puis supprimées presque immédiatement. Les capacités d’ingestion élevées du stockage d’objets blob de blocs Premium le rendent efficace pour ce type de charge de travail.

- **Intelligence artificielle/apprentissage automatique (IA/ML)** . IA/ML traite de la consommation et du traitement de différents types de données, tels que les visuels, la parole et le texte. Ce type de charge de travail de calcul haute performance gère des volumes importants de données nécessitant une réponse rapide et des temps d’ingestion efficaces pour l’analyse des données.

## <a name="cost-effectiveness"></a>Rentabilité
  
Les comptes de stockage d’objets blob de blocs Premium ont un coût de stockage plus élevé mais un coût de transaction plus faible par rapport aux comptes v2 standard à usage général. Si vos applications et charges de travail exécutent un grand nombre de transactions, le stockage d’objets blob de blocs Premium peut être rentable, surtout si la charge de travail effectue un grand nombre d’écritures.

Dans la plupart des cas, les charges de travail qui exécutent plus de 35 à 40 transactions par seconde par téraoctet (TPS/To) sont de bons candidats pour ce type de compte. Par exemple, si votre charge de travail exécute 500 millions d’opérations de lecture et 100 millions d’opérations d’écriture par mois, vous pouvez calculer les TPS/To comme suit : 

- Transactions d’écriture par seconde = 100 millions / (30 x 24 x 60 x 60) = **39** (_arrondi au nombre entier le plus proche_) 

- Transactions de lecture par seconde = 500 millions / (30 x 24 x 60 x 60) = **193** (_arrondi au nombre entier le plus proche_)

- Nombre total de transactions par seconde = **193** + **39** = **232** 

- En supposant que votre compte contenait des données **5 To** en moyenne, les TPS/To sont de **230/5** = **46**. 

> [!NOTE]
> Les prix varient selon l’opération et selon la région. Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour comparer les prix des niveaux de performance standard et Premium. 

Le tableau suivant illustre la rentabilité des comptes de stockage d’objets blob de blocs Premium. Les valeurs de ce tableau sont basées sur un compte de stockage d’objets blob de blocs Premium compatible avec Azure Data Lake Storage Gen2 (également appelé [niveau Premium pour Azure Data Lake Storage](premium-tier-for-data-lake-storage.md)). Chaque colonne représente le nombre de transactions sur un mois. Chaque ligne représente le pourcentage de transactions qui sont des transactions de lecture. Chaque cellule du tableau indique le pourcentage de réduction des coûts associé à un pourcentage de transactions de lecture et au nombre de transactions exécutées.

Par exemple, en supposant que votre compte se trouve dans la région USA Est 2, le nombre de transactions avec votre compte dépasse 90 M et 70 % de ces transactions sont des transactions de lecture, les comptes de stockage d’objets blob de blocs Premium sont plus rentables.

> [!div class="mx-imgBorder"]
> ![Tableau des performances](./media/storage-blob-performance-tiers/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> Si vous préférez évaluer la rentabilité en fonction du nombre de transactions par seconde pour chaque To de données, vous pouvez utiliser les en-têtes de colonne qui apparaissent en bas du tableau. 

## <a name="premium-scenarios"></a>Scénarios Premium

Cette section contient des exemples concrets de la façon dont certains de nos partenaires stockage Azure utilisent le stockage d’objets blob de blocs Premium. Certains d’entre aux activent également Azure Data Lake Storage Gen2 qui introduit une structure de fichiers hiérarchique pouvant améliorer les performances des transactions dans certains scénarios. 

> [!TIP]
> Si vous avez un cas d’usage d’analyse, nous vous recommandons vivement d’utiliser Azure Data Lake Storage Gen2 avec un compte de stockage d’objets blob de blocs Premium. 

Cette section contient les exemples suivants :

- [Hydratation rapide des données](#fast-data-hydration)
- [Applications d’édition interactive](#interactive-editing-applications)
- [Logiciel de visualisation de données](#data-visualization-software)
- [Entreprises de commerce électronique](#e-commerce-businesses)
- [Analytique interactive](#interactive-analytics)
- [Pipelines de traitement des données](#data-processing-pipelines)
- [Internet des objets (IoT)](#internet-of-things-iot)
- [Machine Learning](#machine-learning)
- [Analyse de diffusion en continu en temps réel](#real-time-streaming-analytics)

### <a name="fast-data-hydration"></a>Hydratation rapide des données

Le stockage d’objets blob de blocs Premium peut vous aider à *hydrater* ou mettre en ligne votre environnement rapidement. Dans les secteurs tels que les banques, certaines exigences réglementaires peuvent obliger les entreprises à détruire régulièrement leurs environnements, puis à les rétablir à partir de zéro. Les données utilisées pour alimenter l’environnement doivent être chargées rapidement. 

Certains de nos partenaires stockent une copie de leur instance MongoDB chaque semaine dans un compte de stockage d’objets blob de blocs Premium. Le système est ensuite détruit. Pour remettre le système en ligne rapidement, la copie la plus récente de l’instance MongoDB est lue et chargée. À des fins d’audit, les copies précédentes sont conservées dans le stockage cloud pendant une période donnée.

### <a name="interactive-editing-applications"></a>Applications d’édition interactive

Dans les applications où plusieurs utilisateurs modifient le même contenu, la vitesse des mises à jour devient critique pour maintenir la fluidité de l’expérience utilisateur. 

Certains de nos partenaires développent des logiciels d’édition vidéo. Toute mise à jour qu’un utilisateur effectue dans une vidéo est immédiatement visible par les autres utilisateurs. Les utilisateurs peuvent se concentrer sur leurs tâches au lieu d’attendre l’affichage des mises à jour du contenu. Les latences faibles associées au stockage d’objets blob de blocs Premium aident à créer cette expérience transparente et collaborative.  

### <a name="data-visualization-software"></a>Logiciel de visualisation de données

Les utilisateurs peuvent être beaucoup plus productifs avec les logiciels de visualisation des données si le temps de rendu est rapide. 
 
Nous avons vu des entreprises dans le secteur de la cartographie utiliser des éditeurs de cartographie pour détecter les problèmes liés aux cartes. Ces éditeurs utilisent des données générées à partir de données client du système GPS (Global Positioning System). Pour créer des chevauchements de carte, le logiciel d’édition affiche de petites sections d’une carte en effectuant rapidement des recherches clés. 

Dans un cas, avant d’utiliser le stockage d’objets blob de blocs Premium, un partenaire a utilisé des clusters HBase avec un stockage standard Usage général v2. Toutefois, il est devenu coûteux de conserver les clusters de grande taille en cours d’exécution. Ce partenaire a décidé de quitter cette architecture et a utilisé le stockage d’objets blob de blocs Premium pour les recherches clés rapides. Pour créer des chevauchements, il a utilisé des API REST afin d’afficher des vignettes correspondant à des coordonnées GPS. Le compte de stockage d’objets blob de blocs Premium a fourni une solution économique, et les latences étaient beaucoup plus prévisibles.

### <a name="e-commerce-businesses"></a>Entreprises de commerce électronique

En plus de prendre en charge leurs magasins publics, les entreprises de commerce électronique peuvent également fournir des solutions d’analyse et d’entreposage de données aux équipes internes. Nous avons vu que les partenaires utilisent des comptes de stockage d’objets blob de blocs Premium pour prendre en charge les exigences de faible latence de ces solutions d’analyse et d’entreposage de données. Dans un cas, une équipe de catalogue gère une application d’entreposage de données pour les données relatives aux offres, aux tarifs, aux méthodes d’expédition, aux fournisseurs, à l’inventaire et à la logistique. Les informations sont interrogées, analysées, extraites et regroupées pour plusieurs cas d’usage. L’équipe exécute des analyses sur ces données pour fournir aux différentes équipes de merchandising des informations et des informations pertinentes. 

### <a name="interactive-analytics"></a>Analytique interactive

Dans presque tous les secteurs d’activité, il est nécessaire que les entreprises interrogent et analysent leurs données de manière interactive. 

Les scientifiques des données, les analystes et les développeurs peuvent obtenir des informations plus rapides en exécutant des requêtes sur des données stockées dans un compte de stockage d’objets blob de blocs Premium. Les dirigeants peuvent charger leurs tableaux de bord bien plus rapidement lorsque les données qui s’affichent dans ces tableaux de bord proviennent d’un compte de stockage d’objets blob de blocs Premium au lieu d’un compte v2 standard à usage général. 

Dans un scénario, les analystes devaient analyser rapidement les données de télémétrie provenant de millions d’appareils pour mieux comprendre comment leurs produits sont utilisés et pour prendre des décisions en matière de mise en production. Le stockage de données dans des bases de données SQL est onéreux. Pour réduire les coûts et augmenter la surface d’exposition interrogeable, ils utilisent un compte de stockage d’objets blob de blocs Premium compatible avec Azure Data Lake Storage Gen2 et effectuent le calcul dans Presto et Spark pour produire des insights à partir des tables Hive. De cette façon, même les données rarement consultées disposent de toute la puissance du calcul comme les données fréquemment sollicitées.

Pour combler l'écart entre les performances inférieures à la seconde de SQL et les opérations d'entrée-sortie par seconde (IOP) de Presto vers un stockage externe, la cohérence et la vitesse sont essentielles, en particulier lorsqu'il s'agit de petits fichiers ORC (optimized row columnar). Un compte de stockage blob de blocs Premium, lorsqu'il est utilisé avec Data Lake Storage Gen2, a démontré à plusieurs reprises un triplement des performances par rapport à un compte v2 standard à usage général dans ce scénario. Les requêtes sont exécutées suffisamment rapidement comme si l’exécution s’effectuait localement sur l’ordinateur de calcul. 

Dans un autre cas, un partenaire stocke et interroge les journaux qui sont générés à partir de leur solution de sécurité. les journaux sont générés à l’aide de Databricks, puis stockés dans un compte de stockage d’objets blob de blocs Premium compatible avec Data Lake Storage Gen2. Les utilisateurs finaux interrogent et recherchent ces données à l’aide d’Azure Data Explorer. Ils ont choisi ce type de compte pour augmenter la stabilité ainsi que les performances des requêtes interactives. Ils définissent également la stratégie `Delete Action` de gestion du cycle de vie sur plusieurs jours, ce qui permet de réduire les coûts. Cette stratégie les empêche de conserver les données indéfiniment. Au lieu de cela, les données sont supprimées une fois qu’elles ne sont plus nécessaires. 

### <a name="data-processing-pipelines"></a>Pipelines de traitement des données

Dans presque tous les secteurs d’activité, il est nécessaire que les entreprises traitent les données. Les données brutes de plusieurs sources doivent être nettoyées et traitées afin qu’elles soient utiles pour la consommation en aval dans des outils comme des tableaux de bord de données qui aident les utilisateurs à prendre des décisions. 

Si la vitesse de traitement n’est pas toujours la préoccupation principale du traitement des données, certains secteurs de l’informatique en ont besoin. Par exemple, les sociétés du secteur des services financiers ont souvent besoin de traiter les données de manière fiable et le plus rapidement possible. Pour détecter les fraudes, ces sociétés doivent traiter les entrées de diverses sources, identifier les risques pour leurs clients et prendre rapidement des mesures. 

Dans certains cas, nous avons vu que les partenaires utilisent plusieurs comptes de stockage standard pour stocker les données de diverses sources. Certaines de ces données sont alors déplacées vers un compte de stockage d’objets blob de blocs Premium compatible avec Data Lake Storage, où une application de traitement des données lit fréquemment les données qui viennent d’arriver. Les appels de liste de répertoires dans ce compte étaient beaucoup plus rapides et beaucoup plus cohérents que dans un compte v2 standard, à usage général. La vitesse et la cohérence offertes par le compte ont permis de garantir que les nouvelles données étaient toujours mises à la disposition des systèmes de traitement en aval le plus rapidement possible. Cela leur a permis de détecter, puis d’agir rapidement sur les risques potentiels de sécurité.
     
### <a name="internet-of-things-iot"></a>Internet des objets (IoT)

L’IoT est devenu une partie importante de nos vies quotidiennes. L’IoT permet de suivre les mouvements des voitures, de contrôler les indicateurs et de surveiller notre santé. Il a également des applications industrielles. Par exemple, les entreprises utilisent IoT pour activer leurs projets Smart Factory, améliorer la production agricole et les plateformes pétrolières pour la maintenance prédictive. Les comptes de stockage blob de blocs Premium apportent une valeur ajoutée significative à ces scénarios.
 
Nous avons des partenaires dans le secteur de l’exploration de données. Ils utilisent un compte de stockage blob de blocs Premium compatible avec Data Lake Storage Gen2 avec HDInsight (Hbase) pour ingérer les données de capteur de série chronologique à partir de plusieurs types d’équipement d’exploration de données, avec un profil de charge très important. Le stockage d’objets blob de blocs Premium a aidé à répondre à leurs besoins en matière d’ingestion de taux d’échantillonnage élevé. Cela est également rentable, car le stockage d’objets blob de blocs Premium est à coût optimisé pour les charges de travail qui effectuent un grand nombre de transactions d’écriture, et cette charge de travail génère un grand nombre de transactions d’écriture de petite taille (en dizaines de milliers par seconde).  

### <a name="machine-learning"></a>Machine Learning

Dans de nombreux cas, une grande quantité de données doit être traitée pour former un modèle de Machine Learning. Pour effectuer ce traitement, les machines de calcul doivent s’exécuter pendant une longue période. En comparaison des coûts de stockage, les coûts de calcul sont généralement considérés comme un pourcentage beaucoup plus important de votre facture. Par conséquent, la réduction de la durée d’exécution de vos machines de calcul permet d’effectuer des économies considérables. La faible latence que vous procurez à l’aide du stockage d’objets blob de blocs Premium peut considérablement réduire cette durée et votre facture.

Nous avons des partenaires qui déploient des pipelines de traitement des données sur des clusters Spark où ils exécutent un apprentissage et une inférence de Machine Learning. Ils stockent les tables Spark (fichiers parquet) et les points de contrôle dans un compte de stockage d’objets blob de blocs Premium. Les points de contrôle Spark peuvent créer un grand nombre de fichiers et de dossiers imbriqués. Leurs opérations de liste de répertoires sont rapides, car elles associent la faible latence d’un compte de stockage d’objets blob de blocs premium à la structure de données hiérarchique disponible avec Data Lake Storage Gen2. 

Nous avons également des partenaires dans le secteur des semi-conducteurs avec des cas d’usage qui croisent IoT et Machine Learning. Les appareils IoT reliés aux machines de l’usine de fabrication prennent des photos de plaquettes semi-conductrices et les envoient à leur compte. À l’aide de l’inférence de Deep Learning, le système peut informer les ordinateurs sur site en cas de problème avec la production et si une action doit être effectuée. Ils doivent être en mesure de gérer et de traiter les images de manière rapide et fiable. L’utilisation d’un compte de stockage blob de blocs compatible avec Data Lake Storage Gen2 permet d’effectuer cette opération. 

### <a name="real-time-streaming-analytics"></a>Analyse de streaming en temps réel

Pour prendre en charge l’analytique interactive quasiment en temps réel, un système doit recevoir et traiter de grandes quantités de données, puis mettre ces données à la disposition des systèmes en aval. L’utilisation d’un compte de stockage d’objets blob de blocs premium compatible avec Data Lake Storage Gen2 est parfaite pour ces types de scénarios.

Les entreprises du secteur des médias et du spectacle peuvent générer un grand nombre de journaux et de données de télémétrie dans un laps de temps limité lorsqu’elles diffusent un événement. Certains de nos partenaires s’appuient sur plusieurs partenaires CDN (content delivery network) pour le streaming. Ils doivent prendre des décisions en temps quasi réel sur les CDN partenaires afin d’allouer le trafic. Par conséquent, les données doivent être disponibles pour interroger quelques secondes après leur réception. Pour faciliter cette prise de décision rapide, ils utilisent des données stockées avec dans le stockage d’objets blob de blocs Premium et traitent ces données dans Azure Data Explorer (ADX). Toutes les données de télémétrie téléchargées vers le stockage sont transformées dans ADX, où elles peuvent être stockées dans un format familier que les opérateurs et les cadres peuvent interroger rapidement et en toute fiabilité.

Les données sont chargées dans plusieurs comptes de stockage d’objets blob de performances premium. Chaque compte est connecté à une ressource Event Grid et Event Hub. ADX récupère les données du Stockage Blob, effectue toutes les transformations nécessaires pour normaliser les données (par exemple : décompression des fichiers zip ou conversion de JSON en CSV). Ensuite, les données sont rendues disponibles pour la requête via ADX et les tableaux de bord affichés dans Grafana. Les tableaux de bord Grafana sont utilisés par les opérateurs, les cadres et les autres utilisateurs. Le client conserve ses journaux originaux dans le stockage Premium, ou il les copie dans un compte de stockage v2 à usage général où ils peuvent être stockés dans le niveau d’accès chaud ou froid pour une rétention à long terme et une analyse ultérieure.

## <a name="getting-started-with-premium"></a>Bien démarrer avec Premium

Tout d’abord, vérifiez que vos fonctionnalités de Stockage Blob favorites sont compatibles avec les comptes de stockage d’objets blob de blocs Premium, puis créez le compte. 

>[!NOTE]
> Vous ne pouvez pas convertir un compte de stockage standard Usage général v2 existant en compte de stockage d’objets blob de blocs Premium. Pour migrer vers un compte de stockage d’objet blob de blocs Premium, vous devez créer un compte d’objets blob de blocs et migrer les données vers le nouveau compte. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Vérifier la compatibilité des fonctionnalités de Stockage Blob

Certaines fonctionnalités de Stockage Blob ne sont pas encore prises en charge ou elles disposent d’une prise en charge partielle dans des comptes de stockage d’objets blob de blocs Premium. Avant de choisir le niveau Premium, consultez l’article [Prise en charge des fonctionnalités de Stockage Blob dans les comptes de Stockage Azure](storage-feature-support-in-storage-accounts.md) pour déterminer si les fonctionnalités que vous avez l’intention d’utiliser sont entièrement prises en charge dans votre compte. La prise en charge des fonctionnalités est en constante évolution. Veillez donc à consulter régulièrement cet article pour découvrir les mises à jour.

### <a name="create-a-new-storage-account"></a>Créer un compte de stockage

Pour créer un compte de stockage d’objets blob de blocs Premium, veillez à choisir l’option de performance **Premium** et le type de compte d’**o0bjets blob de blocs** lors de la création du compte.

> [!div class="mx-imgBorder"]
> ![Créer un compte BlockBlobStorage](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

> [!NOTE]
> Certaines fonctionnalités de Stockage Blob ne sont pas encore prises en charge ou elles disposent d’une prise en charge partielle dans des comptes de stockage d’objets blob de blocs Premium. Avant de choisir le niveau Premium, consultez l’article [Prise en charge des fonctionnalités de Stockage Blob dans les comptes de Stockage Azure](storage-feature-support-in-storage-accounts.md) pour déterminer si les fonctionnalités que vous avez l’intention d’utiliser sont entièrement prises en charge dans votre compte. La prise en charge des fonctionnalités est en constante évolution. Veillez donc à consulter régulièrement cet article pour découvrir les mises à jour.

Si votre compte de stockage va être utilisé pour l’analytique, nous vous recommandons vivement d’utiliser Azure Data Lake Storage Gen2 avec un compte de stockage d’objets blob de blocs Premium. Pour déverrouiller les fonctionnalités Azure Data Lake Storage Gen2, activez le paramètre **Espace de noms hiérarchique** sous l’onglet **Avancé** de la page **Créer un compte de stockage**. 

L’illustration suivante montre ce paramètre dans la page **Créer un compte de stockage**.

> [!div class="mx-imgBorder"]
> ![Paramètre d’espace de noms hiérarchique](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Pour obtenir des instructions complètes, consultez [Créer un compte de stockage](../common/storage-account-create.md).



## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble du compte de stockage](../common/storage-account-overview.md)
- [Introduction à Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
- [Créer un compte de stockage à utiliser avec Data Lake Storage Gen2](create-data-lake-storage-account.md)
- [Niveau Premium pour Azure Data Lake Storage](premium-tier-for-data-lake-storage.md)
