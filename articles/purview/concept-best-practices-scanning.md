---
title: Meilleures pratiques pour l’analyse des sources de données dans Purview
description: Cet article présente les meilleures pratiques pour l’inscription et l’analyse de diverses sources de données dans Azure Purview.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 85dc954dcb352f9bc2447e72322d35e8d5e73cb3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552655"
---
# <a name="azure-purview-scanning-best-practices"></a>Meilleures pratiques pour l’analyse Azure Purview

Azure Purview prend en charge l’analyse automatisée des sources de données en local, multicloud et SaaS. L’exécution d’une « analyse » appelle le processus pour recevoir les métadonnées des sources de données inscrites. Les métadonnées traitées à la fin du processus d’analyse et de traitement incluent des métadonnées techniques comme les noms des ressources de données (noms de tables/noms de fichiers), la taille de fichier, les colonnes, le lignage des données, etc. Pour des sources de données structurées (par exemple, le système de gestion de base de données relationnelle), les détails du schéma sont également capturés. Le processus de traitement applique des étiquettes de classification automatisée sur les attributs de schéma en fonction de l’ensemble de règles d’analyse configuré et des étiquettes de confidentialité si votre compte Purview est connecté à un Centre de sécurité et conformité (SCC) Microsoft 365. 

## <a name="intended-audience"></a>Public concerné

- Équipe en charge de l’architecture des données
- Conservateur de données
- Administrateur de sources de données
- Ingénieur Données

## <a name="why-do-you-need-best-practices-to-manage-data-sources"></a>Pourquoi avez-vous besoin de bonnes pratiques pour gérer les sources de données ?

Les bonnes pratiques vous permettent d’optimiser les coûts, de générer l’excellence opérationnelle, d’améliorer la conformité de la sécurité et l’efficacité des performances.
Les considérations et recommandations relatives à la conception ont été organisées en fonction des principales étapes impliquées dans le processus de numérisation.

## <a name="register-source-and-establish-connection"></a>Inscrire la source et établir la connexion

### <a name="design-considerations"></a>Remarques relatives à la conception

- La hiérarchie qui s’aligne sur la stratégie de l’organisation (géographique, fonction commerciale, source de données, etc.) définissant les sources de données à inscrire et à analyser doit être créée à l’aide de Collections.

- Par conception, vous ne pouvez pas inscrire des sources de données plusieurs fois dans le même compte Purview. Cette architecture permet d’éviter le risque d’affectation de différents niveaux de contrôle d’accès à une même source de données.

### <a name="design-recommendations"></a>Recommandations de conception

- Si les métadonnées de la même source de données sont consommées par plusieurs équipes, vous pouvez inscrire et gérer la source de données au niveau d’une collection parente et créer des analyses correspondantes sous chaque sous-groupe, afin que les ressources pertinentes apparaissent sous chaque collection enfant. Les sources sans parent sont regroupées dans une zone en pointillés dans la vue cartographique, sans flèches les reliant à un parent.

  :::image type="content" source="media/concept-best-practices/scanning-parent-child.png" alt-text="Capture d’écran montrant Azure Purview avec la source de données inscrite au niveau de la collection parent.":::

- Utilisez l’option **Azure Multiple** si vous devez inscrire plusieurs sources (abonnements Azure ou groupes de ressources) sur le Cloud Azure. Pour plus d’informations, consultez la documentation ci-dessous :
    * [Analyser plusieurs sources dans Azure Purview](./register-scan-azure-multiple-sources.md)
    * [Évaluer la préparation nécessaire pour les sources de données à grande échelle](./tutorial-data-sources-readiness.md) 
    * [Configurer l’accès aux sources de données pour Azure Purview MSI à grande échelle](./tutorial-msi-configuration.md)
     
- Une fois qu’une source de données est inscrite, vous pouvez analyser la même source plusieurs fois, au cas où la même source est utilisée par différentes équipes ou unités commerciales différemment

Pour plus d’informations sur la définition d’une hiérarchie pour l’inscription de sources de données, consultez les [bonnes pratiques relatives à l’architecture des collections](./concept-best-practices-collections.md)

## <a name="scanning"></a>Analyse

### <a name="design-considerations"></a>Remarques relatives à la conception

- Une fois la source de données inscrite, vous devez configurer une analyse pour gérer l’analyse et la gestion des métadonnées automatisées et sécurisées
- La configuration de l’analyse comprend la configuration du nom de l’analyse, de l’étendue de l’analyse, du runtime d’intégration, de la fréquence de déclenchement de l’analyse, de l’ensemble de règles d’analyse et du jeu de ressources de manière unique pour chaque source de données par fréquence d’analyse.
- Avant de créer des informations d’identification, prenez en compte vos types de sources de données et vos besoins en matière de mise en réseau pour décider quelle méthode d'authentification et quel runtime d'intégration est nécessaire pour votre scénario.

### <a name="design-recommendations"></a>Recommandations de conception

Pour éviter les coûts inattendus et le travail, nous vous recommandons de planifier et de suivre l’ordre ci-dessous lors de la configuration de l’analyse, après avoir inscrit votre source dans la [collection](./how-to-create-and-manage-collections.md) appropriée :

1. Identifiez vos exigences de classification à partir des règles de classification intégrées du système et/ou créez des règles de classification personnalisée si nécessaire, en fonction des besoins spécifiques du secteur, de l’entreprise et/ou régionaux (qui ne sont pas disponibles prêts à l’emploi)
    - Utilisez la méthode d’expression régulière si l’élément de données classifié peut être régulièrement exprimé à l’aide d’un modèle d’expression régulière ou si le modèle peut être généré à l’aide du fichier de données. Assurez-vous que l’échantillon de données reflète la population.
    - Utilisez la méthode du dictionnaire si la liste de valeurs dans l’élément de données est supposée se conformer à un jeu de données donné.
    - Pour plus d’informations, reportez-vous au didacticiel sur la [création d’une classification personnalisée et d’une règle de classification](./create-a-custom-classification-and-classification-rule.md)

2. Le processus de classification peut entraîner une durée d’analyse plus longue, entraînant un coût supplémentaire. Par conséquent, il est recommandé de sélectionner uniquement les règles de classification pertinentes pour la source de données que vous analysez.
    - Créer des ensembles de règles d’analyse par source et sélectionner les règles de classification appropriées, y compris les règles de classification système et personnalisées configurées
    - Lors de la configuration d’un ensemble de règles d’analyse, vérifiez les éléments suivants :
        - Vérifiez si l’ensemble de règles d’analyse par défaut du système est suffisant pour la source de données en cours d’analyse, sinon définir votre ensemble de règles d’analyse personnalisé
        - L’ensemble de règles d’analyse personnalisé peut inclure à la fois les valeurs par défaut du système et personnalisées. Par conséquent, décochez celles qui ne sont pas pertinentes pour les ressources de données analysées
        - Vérifiez toujours les règles par défaut du système en matière de classification en fonction de votre secteur d’activité, de votre entreprise et/ou de vos besoins régionaux spécifiques
        - Le cas échéant, créez un ensemble de règles personnalisé pour exclure les étiquettes de classification indésirables. Par exemple, l’ensemble de règles système contient des modèles de code gouvernementaux génériques pour le monde, pas seulement les États-Unis. Ainsi, vos données peuvent correspondre au modèle d’un autre type, par exemple « numéro de permis de conduire belge »
        - Limitez les règles de classification personnalisée aux étiquettes les plus importantes et pertinentes, afin d’éviter tout encombrement (trop d’étiquettes marquées pour la ressource)
        - Scénario de mise à jour : une analyse complète est déclenchée si vous modifiez la classification personnalisée ou l’ensemble de règles d’analyse. Par conséquent, il est recommandé de configurer l’ensemble de règles de classification et d’analyse de manière appropriée afin d’éviter de réutiliser et d’effectuer des analyses complètes coûteuses
        - La classification des données multilingues n’est pas encore prise en charge
        - Lors de la création d’une analyse de classification personnalisée, assurez-vous que la règle et le seuil du modèle ont été vérifiés et configurés correctement pour empêcher une classification erronée
        - Lors de l’analyse d’un compte de stockage, Azure Purview se sert d’un ensemble de modèles définis pour déterminer si un groupe d’éléments constitue un jeu de ressources. Les règles de modèle de jeu de ressources vous permettent de personnaliser ou de changer la manière dont Azure Purview détecte les ressources regroupées comme jeux de ressources et la façon dont elles sont affichées dans le catalogue. Veuillez consulter [Comment créer des règles de modèle d’ensemble de ressources](./how-to-resource-set-pattern-rules.md) pour plus de détails. 
        > [!NOTE]
        > Cette fonctionnalité a des considérations relatives aux coûts, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/azure-purview/) pour plus d’informations

3. Configurer une analyse pour la ou les sources de données inscrites
    - **Nom de l’analyse** : par défaut, Purview utilise une convention d’affectation de noms **SCAN-[A-Z][a-z][a-z]** qui n’est pas utile lorsque vous tentez d’identifier une analyse que vous avez exécutée. Il est recommandé d’utiliser une convention d’affectation de noms significative.  Une instance peut nommer l’analyse en tant qu'_environment-source-frequency-time_, par exemple DEVODS-Daily-0200, qui représente une analyse quotidienne à 0200 heures.
    
    - **Authentification**
        - Azure Purview offre différentes méthodes d’authentification pour analyser les sources de données, en fonction du type de sources (Cloud Azure ou des sources locales ou tierces). Il est recommandé de suivre le principe de privilège minimum pour la méthode d’authentification ci-dessous, selon l’ordre de préférence :
            - Identité gérée par MSI portée (par exemple, pour les sources Azure Data Lake Gen2)
            - Identité managée affectée par l’utilisateur
            - Principal de service
            - Authentification SQL (par exemple, pour les sources local ou Azure SQL)
            - Clé de compte ou authentification de base (par exemple, pour les sources SAP S/4HANA)
            > [!Note]
            > Si un pare-feu est activé pour le compte de stockage, vous devez utiliser la méthode d’authentification Identité managée lors de la configuration d’une analyse.
            > Lors de la configuration d’informations d’identification, le nom des informations d’identification ne peut contenir que des _lettres, des chiffres, des traits de soulignement et des traits d’union_. 

    - **Runtime d’intégration**
        - Utilisez le runtime d’intégration de résolution automatique Azure, dans la mesure du possible.
        - Si le point de terminaison public est limité pour les sources de données analysées, vous devez configurer un runtime d’intégration auto-hébergé (SHIR) et créer des informations d’identification. SHIR est un outil qui permet au client d’exécuter l’analyse sur son ordinateur. Il remplace le calcul Azure dans lequel l’analyse s’exécute normalement avec l’ordinateur du client.
        - Lorsque vous utilisez un point de terminaison privé pour la réception, vous devez utiliser un runtime d'intégration auto-hébergé pour analyser les sources de données
        - Vérifiez que la [dernière version](https://www.microsoft.com/download/details.aspx?id=39717) de SHIR est installée.
        - Une fois SHIR supprimé, toutes les analyses en cours qui en dépendent échouent.
      <!--
        - In Azure Purview there is no concept of SHIR HA or Shared SHIR.
        -->
        - Quand vous utilisez SHIR, assurez-vous que la mémoire est suffisante pour la source de données en cours d’analyse. Par exemple, lors de l’utilisation de SHIR pour l’analyse de la source SAP, si vous observez « erreur de mémoire insuffisante » :
            - Assurez-vous que l’ordinateur SHIR dispose de suffisamment de mémoire (il est recommandé d’avoir 128 Go)
            - Dans le paramètre d’analyse, définissez la quantité de mémoire maximale disponible comme valeur appropriée (par exemple, 100)
            - Pour plus d’informations, reportez-vous au 5.f dans [ce](./register-scan-sapecc-source.md#create-and-run-scan) document

    - **Étendue de l’analyse**
        - Lors de la configuration de l’étendue de l’analyse, sélectionnez uniquement les ressources, qui sont pertinentes au niveau granulaire ou au niveau parent. Cela permet de s’assurer que le coût d’analyse est optimal et que les performances sont efficaces. Toutes les ressources futures sous un certain parent sont automatiquement sélectionnées si le parent est entièrement ou partiellement vérifié. Par exemple :
            - Pour Azure SQL Database or ADLS Gen2, vous pouvez limiter votre analyse à des parties spécifiques de la source de données, telles que des dossiers, des sous-dossiers, des collections ou des schémas, en cochant les éléments appropriés dans la liste.
            - Pour Oracle, la base de données metastore Hive et les sources Teradata, une liste spécifique de schémas à exporter peut être spécifiée par le biais de valeurs séparées par des points-virgules ou par le biais de modèles de nom de schéma à l’aide des expressions SQL LIKE.
            - Pour des requêtes Google Big, la liste spécifique des jeux de données à exporter peut être spécifiée à l’aide de valeurs séparées par des points-virgules.
            - Lorsque vous créez une analyse pour un compte AWS entier, vous pouvez sélectionner des compartiments spécifiques à analyser. Lorsque vous créez une analyse pour un compartiment AWS S3 spécifique, vous pouvez sélectionner des dossiers spécifiques à analyser.
            - Pour Erwin, vous pouvez étendre votre analyse en fournissant une liste de chaînes de localisateur de modèles Erwin séparées par des points-virgules.
            - Pour Cassandra, une liste spécifique d’espaces clés à exporter peut être spécifiée par le biais de valeurs séparées par des points-virgules ou par le biais de modèles de nom d’espaces clés à l’aide d’expressions SQL LIKE.
            - Pour Looker, vous pouvez étendre votre analyse en fournissant une liste de chaînes de projets Looker séparées par des points-virgules.
            - Pour un locataire Power BI, vous pouvez uniquement spécifier s’il faut inclure ou exclure l’espace de travail personnel.
            - En général, il est recommandé d’utiliser « ignorer les modèles » (le cas échéant) sur la base d’un caractère générique (par exemple, pour les lacs de données), pour exclure les fichiers Temp, config, les tables système RDMS ou les tables de sauvegarde / STG
            - Lors de l’analyse de documents/données non structurées, évitez d’analyser un grand nombre de documents de ce type, car l’analyse traite les 20 premiers Mo de ces documents et peut entraîner une durée d’analyse plus longue.

    - **Ensemble de règles d’analyse**
        - Lors de la configuration de l’ensemble de règles d’analyse, veillez à configurer l’ensemble de règles d’analyse système/personnalisé approprié créé précédemment
        - Il existe une option pour créer des types de dossier personnalisé et vous pouvez remplir les détails en conséquence. Actuellement, Azure Purview prend en charge un seul caractère dans un délimiteur personnalisé. Si vous utilisez des délimiteurs personnalisés tels que ~ dans vos données réelles, vous devez créer un nouvel ensemble de règles d’analyse

    - **Type et planification de l’analyse**
        - Le processus d’analyse peut être configuré pour exécuter des analyses complètes ou incrémentielles.
        - Le processus d’analyse peut être déclenché pour s’exécuter immédiatement, ou planifié pour s’exécuter régulièrement (de façon hebdomadaire ou mensuellement) pour maintenir à jour vos métadonnées. L'utilisation des ressources dépend aussi fortement de la quantité de données qui sont analysées.
        - Il est recommandé d’exécuter les analyses en dehors des heures de travail ou hors service afin d’éviter toute surcharge de traitement sur la source.
        - L’analyse initiale est une analyse complète, et chaque analyse suivante est incrémentielle. Les analyses suivantes peuvent être planifiées en tant qu’analyses incrémentielles périodiques.
        - La fréquence des analyses doit correspondre à la planification de la gestion des modifications de la source de données et/ou aux besoins de l’entreprise. Par exemple :
            - Si la structure source peut changer (les ressources ou les champs d’une ressource sont ajoutés, modifiés ou supprimés) chaque semaine, la fréquence d’analyse doit être synchronisée avec la même.
            - Si les étiquettes de classification/confidentialité sont supposées être à jour sur une base hebdomadaire (peut être due à des raisons réglementaires), la fréquence d’analyse doit être hebdomadaire.
            Par exemple, si les fichiers de partition sont ajoutés chaque semaine dans un lac de données source, vous pouvez planifier des analyses mensuelles au lieu d’analyses hebdomadaires, car il n’y a aucune modification dans les métadonnées (en supposant qu’il n’existe aucun nouveau scénario de classification)
            - Lors de la planification d’une analyse qui doit être exécutée le même jour où elle est créée, l’heure de début doit être antérieure à l’heure de l’analyse d’au moins une minute.
            - La durée maximale pendant laquelle l’analyse peut s’exécuter est de sept jours (peut-être en raison de problèmes de mémoire), ce qui exclut le processus d’ingestion. Si la progression n’a pas été mise à jour au bout de sept jours, l’analyse est marquée comme ayant échouée. Le processus d’ingestion (dans le catalogue) ne présente actuellement aucune limitation.

    - **Annulation des analyses**
        - Actuellement, les analyses peuvent uniquement être annulées ou suspendues si l’état de l’analyse est passé de l’état « en cours » à l’état « en attente » après le déclenchement de l’analyse.
        - L’annulation d’une analyse enfant individuelle n’est pas prise en charge
        <!--
        - <need to add the concept of a parent and child scan
        - To remove any scanned assets from Purview you should remove asset by asset
        -->

    - **Scénarios de suppression et de mise à jour**
        - Si un champ/une colonne, une table ou un fichier est supprimé du système source après l’exécution de l’analyse, il est uniquement reflété (supprimé) dans Purview après la prochaine analyse complète/incrémentielle planifiée.
        - Un élément multimédia peut être supprimé du catalogue Azure Purview à l’aide de l’icône **supprimer** sous le nom de la ressource (cela ne supprime pas l’objet dans la source). Toutefois, si vous exécutez une analyse complète sur la même source, elle est de nouveau ingérée dans le catalogue. Si vous avez planifié une analyse hebdomadaire/mensuelle à la place (incrémentielle), la ressource supprimée n’est pas prélevée à moins que l’objet ne soit modifié à la source (par exemple, une colonne est ajoutée/supprimée de la table).
        - Si vous effectuez une mise à jour au niveau de la ressource, comme ajouter une description, une classification au niveau de la ressource, un terme du glossaire ou un contact à une ressource, les analyses suivantes vont mettre à jour le schéma de la ressource (les nouvelles colonnes et les classifications sont détectées par l’analyseur lors d’exécutions d’analyse suivantes).
        - Si vous effectuez une mise à jour au niveau de la colonne, comme ajouter une description, une classification au niveau de la colonne ou un terme du glossaire, ou comme mettre à jour le type de données ou le nom de la colonne, les analyses suivantes ne vont pas mettre à jour le schéma de la ressource (les nouvelles colonnes et les classifications ne seront pas détectées par l’analyseur lors exécutions d’analyse suivantes).

        Pour plus d’informations, consultez le didacticiel sur l'[affichage, la modification et la suppression de ressources](./catalog-asset-details.md)

## <a name="next-steps"></a>Étapes suivantes
-  [Gérer les sources de données](./manage-data-sources.md)
