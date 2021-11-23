---
title: Haute disponibilité dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB offre une haute disponibilité
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 047e64c47d4d993b72f76d57e24658c3e25d3aee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343987"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Comment Azure Cosmos DB fournit-il une haute disponibilité ?
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB offre une haute disponibilité de deux principales façons. Premièrement, Azure Cosmos DB réplique les données entre les régions configurées dans un compte Cosmos. Deuxièmement, Azure Cosmos DB conserve quatre réplicas de données dans une région.

Azure Cosmos DB est un service de base de données mondialement distribué disponible dans [toutes les régions où Azure est disponible](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Vous pouvez associer n’importe quel nombre de régions Azure avec votre compte Azure Cosmos, et vos données sont répliquées de manière automatique et transparente. Vous pouvez ajouter ou supprimer une région à votre compte Azure Cosmos à tout moment. Cosmos DB est disponible dans les cinq environnements cloud Azure distincts accessibles aux clients :

* Le cloud **public Azure**, qui est disponible dans le monde entier.

* **Azure China 21Vianet** est disponible via un partenariat unique entre Microsoft et 21Vianet, l’un des fournisseurs Internet les plus importants de Chine.

* **Azure Allemagne** fournit des services sous un modèle d’administration de données, qui garantit que les données clientes sont conservées en Allemagne sous le contrôle de T-Systems International GmbH, filiale de Deutsche Telekom, agissant en tant qu’administrateur des données allemandes.

* **Azure Government** est disponible dans quatre régions des États-Unis pour les organismes publics américains et leurs partenaires.

* **Azure Government for Department of Defense (DoD)** est disponible dans deux régions des États-Unis pour le département de la Défense des États-Unis.

Dans une région, Azure Cosmos DB conserve quatre copies de vos données sous forme de réplicas dans des partitions physiques, comme illustré dans l’image suivante :

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partitionnement physique" border="false":::

* Les données dans les conteneurs Azure Cosmos sont [partitionnées horizontalement](partitioning-overview.md).

* Un ensemble de partitions est une collection de plusieurs jeux de réplicas. Dans chaque région, chaque partition est protégée par un jeu de réplicas avec toutes les écritures répliquées et durablement validées par une majorité de réplicas. Les réplicas sont répartis entre 10 et 20 domaines d’erreur.

* Chaque partition est répliquée dans toutes les régions. Chaque région contient toutes les partitions de données d’un conteneur Azure Cosmos, et peut servir des écritures quand les écritures multirégions sont activées.  

Si votre compte Azure Cosmos est distribué entre *N* régions Azure, il y aura au moins *N* x 4 copies de toutes vos données. Pour obtenir une vue d’ensemble plus détaillée de la distribution des données, consultez [Principe de distribution de données mondiale](global-dist-under-the-hood.md). Le fait d’avoir un compte Azure Cosmos dans plus de deux régions améliore la disponibilité de votre application et fournit une latence faible dans les régions associées.

## <a name="slas-for-availability"></a>Contrats SLA pour la disponibilité

Azure Cosmos DB fournit des contrats de niveau de service (SLA) complets englobant le débit, la latence au 99ème centile, la cohérence et une haute disponibilité. Le tableau suivant comporte les garanties de haute disponibilité fournie par Azure Cosmos DB pour les comptes à région unique et multirégion. Pour bénéficier d’une disponibilité d’écriture accrue, configurez vos comptes Azure Cosmos afin d’avoir plusieurs régions d’écriture.

|Type d'opération  | Région unique |Multirégion (écritures dans une seule région)|Multirégion (écritures dans une plusieurs régions) |
|---------|---------|---------|-------|
|Écritures   | 99,99   |99,99   |99,999|
|Lectures    | 99,99   |99,999  |99,999|

> [!NOTE]
> Dans la pratique, la disponibilité d’écriture réelle pour l’obsolescence limitée, la session, les modèles de cohérence éventuelle et à préfixe cohérent, est beaucoup plus importante que les contrats SLA publiés. La disponibilité réelle de lecture pour tous les niveaux de cohérence est beaucoup plus importante que les contrats SLA publiés.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Haute disponibilité avec Azure Cosmos DB en cas de pannes régionales

Dans les rares cas de panne régionale, Azure Cosmos DB s’assure que votre base de données est toujours hautement disponible. Les détails suivants permettent de capturer le comportement d’Azure Cosmos DB pendant une panne, en fonction de la configuration de votre compte Azure Cosmos :

* Avant la reconnaissance d’une opération d’écriture auprès du client alors qu’Azure Cosmos DB est utilisé, les données sont validées durablement par un quorum de réplicas se trouvant la région qui accepte les opérations d’écriture. Pour en savoir plus, consultez [Niveaux de cohérence et débit](./consistency-levels.md#consistency-levels-and-throughput).

* Les comptes multirégion configurés avec plusieurs régions d’écriture sont hautement disponibles pour les écritures et les lectures. Les basculements régionaux sont détectés et traités dans le client Azure Cosmos DB. Ils sont également instantanés et ne nécessitent aucune modification à partir de l’application.

* Les comptes dans une seule région peuvent perdre leur disponibilité en raison d’une panne régionale. Il est toujours recommandé de configurer **au moins deux régions** (de préférence, au moins deux régions d’écriture) avec votre compte Azure Cosmos pour garantir une haute disponibilité en permanence.

> [!IMPORTANT]
> Lorsque vous utilisez des API SQL, vous devez configurer le Kit de développement logiciel (SDK) Cosmos DB pour utiliser toutes les régions de lecture spécifiées afin de tirer parti de la disponibilité accrue. Pour plus d’informations, reportez-vous à cet [article](troubleshoot-sdk-availability.md).

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Comptes multirégion avec une seule région d’écriture (panne de région d'écriture)

* En cas de panne de région d’écriture, le compte Azure Cosmos promeut automatiquement une région secondaire en tant que nouvelle région d’écriture principale lorsque l’option **Activer le basculement automatique** est configurée dans le compte Azure Cosmos. Lorsque cette option est activée, le basculement intervient vers une autre région dans l’ordre de priorité des régions que vous avez spécifié.

* Notez que le basculement manuel ne doit pas être déclenché et qu’il ne réussira pas en présence d’une panne de la région source ou de la région de destination. Cela est dû à une vérification de cohérence requise par la procédure de basculement qui nécessite une connectivité entre les régions.

* Lorsque la région précédemment affectée est de nouveau en ligne, toutes les données d'écriture qui n'étaient pas dupliquées lors de l’échec de la région sont mises à disposition via le [flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed). Les applications peuvent lire le flux de conflits, résoudre les conflits en fonction de la logique propre à l’application, et réécrire les données mises à jour dans le conteneur Azure Cosmos comme il convient.

* Une fois que la région d’écriture précédemment impactée a récupéré, elle devient automatiquement disponible en tant que région de lecture. Vous pouvez basculer vers la région récupérée en tant que région d’écriture. Vous pouvez basculer d’une région à l’autre en utilisant [PowerShell, Azure CLI ou le portail Azure](how-to-manage-database-account.md#manual-failover). Il n'y a **aucune perte de données ou de disponibilité** avant, pendant ou après le changement de région d'écriture, et votre application continue d’être hautement disponible.

> [!IMPORTANT]
> Il est fortement recommandé de configurer les comptes Azure Cosmos utilisés pour les charges de travail de production afin **d’activer le basculement automatique**. Cela permet à Cosmos DB de basculer automatiquement les bases de données de comptes vers des régions disponibles. En l’absence de cette configuration, le compte subira une perte de disponibilité en écriture pendant toute la durée de la panne de la région d’écriture, car le basculement manuel échouera en raison d’un manque de connectivité de la région.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Comptes multirégion avec une seule région d’écriture (panne de région de lecture)

* Pendant une panne de la région de lecture, les comptes Azure Cosmos utilisant un niveau de cohérence ou une cohérence forte avec au moins trois régions de lecture restent hautement disponibles pour les lectures et les écritures.

* Les comptes Azure Cosmos utilisant une cohérence forte avec trois régions (une en écriture, deux en lecture) vont conserver une disponibilité en écriture en cas de défaillance de la région en lecture. Pour les comptes avec deux régions et le basculement automatique activés, le compte cesse d’accepter les écritures jusqu’à ce que la région soit marquée en échec et que le basculement automatique se produise.

* La région impactée est automatiquement déconnectée et marquée comme étant hors connexion. Les [Kits de développement logiciel (SDK) Azure Cosmos DB](sql-api-sdk-dotnet.md) redirigent les appels de lecture vers la prochaine région disponible dans la liste des régions préférées.

* Si aucune des régions dans la liste des régions préférées n'est disponible, les appels sont automatiquement acheminés vers la zone d’écriture en cours.

* Aucune modification n’est nécessaire dans le code de votre application pour gérer la panne de la région de lecture. Lorsque la région de lecture impactée est de nouveau en ligne, elle se synchronise automatiquement avec la région d’écriture active et est à nouveau disponible pour le traitement des requêtes de lecture.

* Les lectures suivantes sont redirigées vers la région récupérée sans modification nécessaire de votre code d’application. Pendant le basculement et la réintégration d’une région ayant précédemment échoué, les garanties de cohérence de lecture continuent à être respectées par Azure Cosmos DB.

* Même dans un cas rare et malheureux où la région Azure est définitivement irrécupérable, il n’y a aucune perte de données si votre compte Azure Cosmos multirégion est configuré avec une cohérence *forte*. Si une région d’écriture est définitivement irrécupérable et en présence d’un compte Azure Cosmos multirégion configuré avec une cohérence de l’obsolescence limitée, la fenêtre de perte de données potentielle est limitée à la fenêtre d’obsolescence (*K* ou *T*) où K = 100 000 mises à jour ou T = 5 minutes, selon ce qui se produit en premier. Pour les niveaux de cohérence session, garantie de préfixe et éventuelle, la fenêtre de perte de données potentielle est limitée à un maximum de 15 minutes. Pour plus d'informations sur les cibles RPO et RTO pour Azure Cosmos DB, consultez [Niveaux de cohérence et durabilité des données](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Prise en charge des zones de disponibilité

En plus de la résilience inter-région, Azure Cosmos DB prend en charge la **redondance de zone** dans les régions prises en charge lors de la sélection d’une région à associer à votre compte Azure Cosmos.

La prise en charge des zones de disponibilité permet à Azure Cosmos DB de s'assurer que les réplicas sont placés dans plusieurs zones d'une région donnée afin d'offrir haute disponibilité et une résilience aux défaillances de zones. Les zones de disponibilité fournissent un contrat SLA de disponibilité à 99,995 % sans modification de la latence. En cas de défaillance d'une seule zone, la redondance de zone assure la durabilité des données avec RPO = 0 et disponibilité avec RTO = 0. La redondance de zone est une fonctionnalité supplémentaire de la réplication régionale. Seule, la redondance de zone ne suffit pas à offrir une résilience régionale.

La redondance de zone peut être configurée uniquement lors de l’ajout d’une nouvelle région à un compte Azure Cosmos. Pour les régions existantes, la redondance de zone peut être activée en supprimant la région, puis en la rajoutant avec la redondance de zone activée. Pour un compte à région unique, cela requiert d’ajouter une région vers laquelle basculer temporairement, puis de supprimer et d’ajouter la région souhaitée avec la redondance de zone activée.

Lorsque vous configurez des écritures multirégions pour votre compte Azure Cosmos, vous pouvez opter pour la redondance de zone, sans frais supplémentaires. Dans le cas contraire, consultez le tableau ci-dessous concernant la tarification de la prise en charge de la redondance de zone. Pour obtenir la liste des régions où des zones de disponibilité sont disponibles, consultez les [zones de disponibilité](../availability-zones/az-region.md).

Le tableau suivant récapitule la fonctionnalité de haute disponibilité des différentes configurations de compte :

|KPI|Région unique sans zone de disponibilité|Région unique avec zones de disponibilité|Multirégion, écritures dans une seule région sans zones de disponibilité|Multirégion, écritures dans une seule région avec zones de disponibilité|Multirégion, écritures dans une plusieurs régions avec ou sans zones de disponibilité|
|---------|---------|---------|---------|---------|---------|
|Contrat SLA de disponibilité en écriture | 99,99 % | 99,995 % | 99,99 % | 99,995 % | 99, 999 % |
|Contrat SLA de disponibilité en lecture  | 99,99 % | 99,995 % | 99, 999 % | 99, 999 % | 99, 999 % |
|Défaillances de zone - Perte de données | Perte de données | Aucune perte de données | Aucune perte de données | Aucune perte de données | Aucune perte de données |
|Défaillances de zone – Disponibilité | Perte de disponibilité | Aucune perte de disponibilité | Aucune perte de disponibilité | Aucune perte de disponibilité | Aucune perte de disponibilité |
|Panne régionale - Perte de données | Perte de données |  Perte de données | Dépend du niveau de cohérence. Pour plus d’informations, consultez [Compromis entre cohérence, disponibilité et performance](./consistency-levels.md). | Dépend du niveau de cohérence. Pour plus d’informations, consultez [Compromis entre cohérence, disponibilité et performance](./consistency-levels.md). | Dépend du niveau de cohérence. Pour plus d’informations, consultez [Compromis entre cohérence, disponibilité et performance](./consistency-levels.md).
|Panne régionale - Disponibilité | Perte de disponibilité | Perte de disponibilité | Aucune perte de disponibilité pour échec dans la région de lecture, temporaire pour échec dans la région d’écriture | Aucune perte de disponibilité pour échec dans la région de lecture, temporaire pour échec dans la région d’écriture | Aucune perte de disponibilité |
|Prix (***1** _) | N/A | Unités de requête approvisionnées x taux de 1,25 | RU provisionnées/s x n régions | RU provisionnées/s x taux 1,25 x n régions (_*_2_**) | Taux d’écriture multirégion x n régions |

***1*** Les unités de requête (RU) de comptes serverless sont multipliées par un facteur de 1,25.

***2*** Taux de 1,25 appliqué uniquement aux régions dans lesquelles une zone de disponibilité est activée.

La fonctionnalité Zones de disponibilité peut être activée via :

* [Azure portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](sql/manage-with-cli.md#add-or-remove-regions)

* [Modèles Microsoft Azure Resource Manager](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Génération d’applications hautement disponibles

* Examinez le [comportement attendu des kits de développement logiciel (SDK) Azure Cosmos](troubleshoot-sdk-availability.md) au cours de ces événements et les configurations qui l’affectent.

* Pour garantir une disponibilité élevée en écriture et en lecture, configurez votre compte Azure Cosmos de façon à ce qu’il s’étendre sur au moins deux régions, avec plusieurs régions d’écriture. Cette configuration offre la plus haute disponibilité, la plus faible latence et la meilleure évolutivité pour les lectures et les écritures soutenues par les contrats SLA. Pour en savoir plus, découvrez comment [configurer votre compte Azure Cosmos avec plusieurs régions d’écriture](tutorial-global-distribution-sql-api.md).

* Pour les comptes Azure Cosmos multirégions qui sont configurés avec une seule région d’écriture, [activez le basculement automatique à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#automatic-failover). Une fois le basculement automatique activé, Cosmos DB bascule automatiquement votre compte en cas de sinistre régional.  

* Même si votre compte Azure Cosmos est hautement disponible, votre application peut ne pas être pas correctement conçue pour rester hautement disponible. Pour tester la haute disponibilité de bout en bout de votre application, dans le cadre de procédures de récupération d’urgence ou de test de votre application, désactivez temporairement le basculement automatique du compte, appelez régulièrement le [basculement manuel à l’aide de PowerShell, d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#manual-failover), puis surveillez le basculement de votre application. Une fois l’opération terminée, vous pouvez basculer vers la région primaire et restaurer le basculement automatique pour le compte.

> [!IMPORTANT]
> N’appelez pas le basculement manuel pendant une panne de Cosmos DB, que ce soit sur la région source ou sur la région de destination, car il nécessite la connectivité des régions pour maintenir la cohérence des données et qu’il est voué à l’échec.

* Dans un environnement de base de données globalement distribuée, il existe une relation directe entre le niveau de cohérence et la durabilité des données en situation de panne à l'échelle d'une région. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Ce délai s’appelle l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. Il s’agit de l’objectif de point de récupération (RPO, recovery point objective). Pour obtenir le RPO et le RTO pour Azure Cosmos DB, consultez [Niveaux de cohérence et durabilité des données](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>À quoi s’attendre lors d’une panne régionale de Cosmos DB

Pour les comptes à une seule région, les clients subiront une perte de disponibilité en lecture et en écriture.

Les comptes multirégions auront des comportements différents selon le tableau suivant.

| Régions d’écriture | Basculement automatique | À quoi s’attendre | Procédure à suivre |
| -- | -- | -- | -- |
| Région d’écriture unique | Non activé | En cas de panne dans une région de lecture lorsque la cohérence forte n’est pas utilisée, tous les clients sont redirigés vers d’autres régions. Aucune perte de disponibilité en lecture ou en écriture. Pas de perte de données. Lorsque vous utilisez une cohérence forte, la panne de la région de lecture peut avoir un impact sur la disponibilité d’écriture s’il reste moins de deux régions de lecture.<p/> En cas de panne dans la région d’écriture, les clients subiront une perte de disponibilité en écriture. Si le niveau de cohérence fort n’est pas sélectionné, il est possible que certaines données n’aient pas été répliquées dans les régions actives restantes. Cela dépend du niveau de cohérence sélectionné, comme décrit dans [cette section](consistency-levels.md#rto). Si la zone touchée subit une perte définitive des données, les données non répliquées peuvent être perdues. <p/> Cosmos DB restaurera automatiquement la disponibilité en écriture à la fin de la panne. | Pendant la panne, assurez-vous qu’il y a suffisamment d’unités de requête approvisionnées dans les régions restantes pour prendre en charge le trafic de lecture. <p/> Ne déclenche *pas* de basculement manuel pendant la panne, car ce dernier ne fonctionnera pas. <p/> Lorsque la panne est terminée, réajustez les unités de requête approvisionnées en conséquence. |
| Région d’écriture unique | activé | En cas de panne dans une région de lecture lorsque la cohérence forte n’est pas utilisée, tous les clients sont redirigés vers d’autres régions. Aucune perte de disponibilité en lecture ou en écriture. Pas de perte de données. Lorsque vous utilisez une cohérence forte, la panne de la région de lecture peut avoir un impact sur la disponibilité d’écriture s’il reste moins de deux régions de lecture.<p/> En cas de panne dans la région d’écriture, les clients subiront une perte de disponibilité en écriture jusqu’à ce que Cosmos DB élise automatiquement une nouvelle région comme nouvelle région d’écriture selon vos préférences. Si le niveau de cohérence fort n’est pas sélectionné, il est possible que certaines données n’aient pas été répliquées dans les régions actives restantes. Cela dépend du niveau de cohérence sélectionné, comme décrit dans [cette section](consistency-levels.md#rto). Si la zone touchée subit une perte définitive des données, les données non répliquées peuvent être perdues. | Pendant la panne, assurez-vous qu’il y a suffisamment d’unités de requête approvisionnées dans les régions restantes pour prendre en charge le trafic de lecture. <p/> Ne déclenche *pas* de basculement manuel pendant la panne, car ce dernier ne fonctionnera pas. <p/> Une fois la panne terminée, vous pouvez replacer la région d’écriture dans la région d’origine et réajuster les RU approvisionnées en conséquence. Les comptes utilisant des API SQL peuvent également récupérer les données non répliquées dans la région défaillante à partir de votre [flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed). |
| Régions d’écriture multiples | Non applicable | Aucune perte de disponibilité en lecture ou en écriture. <p/> Les données récemment mises à jour dans la région défaillante peuvent être indisponibles dans les régions actives restantes. Les niveaux de cohérence session, garantie de préfixe et éventuelle garantissent une obsolescence inférieure à 15 minutes. L’obsolescence limitée garantit moins de K mises à jour ou T secondes, selon la configuration. Si la zone touchée subit une perte définitive des données, les données non répliquées peuvent être perdues. | Pendant la panne, assurez-vous qu’il y a suffisamment d’unités de requête approvisionnées dans les régions restantes pour prendre en charge le trafic supplémentaire. <p/> Lorsque la panne est terminée, vous pouvez réajuster les unités de requête approvisionnées en conséquence. Si possible, Cosmos DB récupérera automatiquement les données non répliquées dans la région défaillante en utilisant la méthode de résolution des conflits configurée pour les comptes de l’API SQL et Dernière écriture prioritaire pour les comptes utilisant d’autres API. |

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite lire les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](./consistency-levels.md)

* [Mise à l’échelle du débit provisionné au niveau global](./request-units.md)

* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)

* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)

* [Configurer votre compte Cosmos avec plusieurs régions d’écriture](how-to-multi-master.md)

* [Comportement du Kit de développement logiciel (SDK) dans les environnements multirégionaux](troubleshoot-sdk-availability.md)
