---
title: Passerelle dédiée Azure Cosmos DB
description: Une passerelle dédiée est un calcul qui est un serveur frontal pour les données d’un compte Azure Cosmos DB. Lorsque vous vous connectez à la passerelle dédiée, elle achemine les requêtes et met en cache les données.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: tisande
ms.openlocfilehash: 2c2681f1d6e9abf66375d8a6a9d0f43683474dcc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060412"
---
# <a name="azure-cosmos-db-dedicated-gateway---overview-preview"></a>Passerelle dédiée Azure Cosmos DB - vue d’ensemble (préversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Une passerelle dédiée est un calcul côté serveur qui est un serveur frontal pour votre compte Azure Cosmos DB. Lorsque vous vous connectez à la passerelle dédiée, elle achemine les requêtes et met en cache les données. À l’instar du débit approvisionné, la passerelle dédiée est facturée à l’heure.

## <a name="overview"></a>Vue d'ensemble

Vous pouvez approvisionner une passerelle dédiée pour améliorer les performances à grande échelle. La mise en cache est la raison la plus courante pour laquelle vous souhaitez configurer une passerelle dédiée. Lorsque vous approvisionnez une passerelle dédiée, un [cache intégré](integrated-cache.md) est automatiquement configuré dans celle-ci. Les lectures de point et les requêtes qui ont une correspondance dans le cache intégré n’utilisent aucune de vos unités de requête. L’approvisionnement d’une passerelle dédiée avec un cache intégré permet d’améliorer les charges de travail intensives, réduisant les coûts d’Azure Cosmos DB.

La passerelle dédiée est intégrée à Azure Cosmos DB. Lorsque vous [approvisionnez une passerelle dédiée](how-to-configure-integrated-cache.md), vous disposez d’un nœud entièrement géré qui achemine les requêtes vers les partitions principales. La connexion à Azure Cosmos DB avec la passerelle dédiée offre une latence plus faible et plus prévisible que la connexion à Azure Cosmos DB avec la passerelle standard. Même les échecs du cache verront une amélioration de la latence en comparant la passerelle dédiée et la passerelle standard.

Votre application utilise une passerelle dédiée moyennant des modifications de code minimales. Les comptes Azure Cosmos DB nouveaux et existants peuvent approvisionner une passerelle dédiée pour améliorer les performances de lecture.

> [!NOTE]
> Avez-vous des commentaires sur la passerelle dédiée ? Nous attendons vos remarques ! N’hésitez pas à partager vos commentaires directement avec l’équipe d’ingénierie Azure Cosmos DB :cosmoscachefeedback@microsoft.com

## <a name="connection-modes"></a>Modes de connexion

Il existe trois façons de se connecter à un compte Azure Cosmos DB :

- [Mode direct](#connect-to-azure-cosmos-db-using-direct-mode)
- [Mode passerelle utilisant la passerelle standard](#connect-to-azure-cosmos-db-using-gateway-mode)
- [Mode passerelle utilisant la passerelle dédiée](#connect-to-azure-cosmos-db-using-the-dedicated-gateway) (disponible uniquement pour les comptes d’API SQL)

### <a name="connect-to-azure-cosmos-db-using-direct-mode"></a>Se connecter à Azure Cosmos DB dans le mode direct

Quand vous vous connectez à Azure Cosmos DB à l’aide du mode direct, votre application se connecte directement au serveur principal Azure Cosmos DB. Même si vous avez de nombreuses partitions physiques, le routage des requêtes est géré entièrement côté client. Le mode direct offre une faible latence, car votre application peut communiquer directement avec le backend Azure Cosmos DB et n’a pas besoin d’un tronçon réseau intermédiaire.

Représentation graphique de la connexion en mode direct :

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="Image illustrant le fonctionnement du mode direct Cosmos DB" border="false":::

### <a name="connect-to-azure-cosmos-db-using-gateway-mode"></a>Se connecter à Azure Cosmos DB à l’aide du mode passerelle

Si vous vous connectez à Azure Cosmos DB à l’aide du mode passerelle, votre application se connecte en premier lieu à un nœud frontal, qui gère le routage de la requête vers les nœuds principaux appropriés. Étant donné que le mode passerelle implique un tronçon réseau supplémentaire, vous pouvez observer une latence légèrement supérieure par rapport au mode direct. 

Quand vous vous connectez à Azure Cosmos DB avec le mode passerelle, vous pouvez le faire avec l’une des options suivantes :

* **Passerelle standard** : si le serveur principal, qui inclut votre débit et votre stockage configurés, a une capacité dédiée par conteneur, la passerelle standard est partagée entre plusieurs comptes Azure Cosmos. Il est pratique pour de nombreux clients de partager une passerelle standard, car le nombre de ressources de calcul consommées par chaque client individuel est faible.
* **Passerelle dédiée** : le backend et la passerelle disposent tous deux d’une capacité dédiée. Le cache intégré nécessite une passerelle dédiée car il requiert un processeur et une mémoire importants, spécifiques à votre compte Azure Cosmos.

### <a name="connect-to-azure-cosmos-db-using-the-dedicated-gateway"></a>Se connecter à Azure Cosmos DB à l’aide d’une passerelle dédiée

Vous devez vous connecter à Azure Cosmos DB à l’aide de la passerelle dédiée afin d’utiliser le cache intégré. La passerelle dédiée a un point de terminaison différent de celui fourni avec votre compte Azure Cosmos DB. Lorsque vous vous connectez à votre point de terminaison de passerelle dédié, votre application envoie une requête à la passerelle dédiée, qui achemine ensuite la requête vers différents nœuds du principal. Si possible, le cache intégré traitera le résultat.

Diagramme de connexion en mode passerelle avec une passerelle dédiée :

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="Image illustrant le fonctionnement de la passerelle Cosmos DB dédiée" border="false":::
 
## <a name="provisioning-the-dedicated-gateway"></a>Approvisionnement de la passerelle dédiée

Un cluster de passerelle dédié peut être approvisionné dans des comptes d’API (SQL) principaux. Un cluster de passerelle dédié peut avoir jusqu’à cinq nœuds, et vous pouvez ajouter ou supprimer des nœuds à tout moment. Tous les nœuds de passerelle dédiés de votre compte [partagent la même chaîne de connexion](how-to-configure-integrated-cache.md#configuring-the-integrated-cache).

Les nœuds de passerelle dédiés sont indépendants les uns des autres. Lorsque vous approvisionnez plusieurs nœuds de passerelle dédiés, n’importe quel nœud unique peut acheminer une requête donnée. De plus, chaque nœud possède un cache intégré distinct des autres. Les données mises en cache dans chaque nœud dépendent des données qui ont été [écrites ou lues](integrated-cache.md#item-cache) récemment par ce nœud spécifique. En d’autres termes, si un élément ou une requête est mis en cache sur un nœud, il ne l’est pas nécessairement sur les autres.

Pour le développement, nous vous recommandons de commencer avec un nœud, mais pour la production, vous devez configurer trois nœuds ou plus afin de bénéficier de la haute disponibilité. [Découvrez comment approvisionner un cluster de passerelle dédié avec un cache intégré](how-to-configure-integrated-cache.md). L’approvisionnement de plusieurs nœuds de passerelle dédiés permet au cluster de passerelle dédié de continuer à acheminer les requêtes et à traiter les données mises en cache, même si l’un des nœuds de passerelle dédiés n’est pas disponible.

Étant donné qu’il s’agit d’une préversion publique, la passerelle dédiée ne dispose pas d’un SLA de disponibilité. Toutefois, vous devez généralement vous attendre à une disponibilité comparable pour le reste de votre compte Azure Cosmos DB.

La passerelle dédiée est disponible dans les tailles suivantes :

| **Nom de la référence SKU** | **Processeurs virtuels** | **Mémoire**  |
| ------------ | -------- | ----------- |
| **D4s**      | **4**    | **16 Go** |
| **D8s**      | **8**    | **32 Go** |
| **D16s**     | **16**   | **64 Go** |

> [!NOTE]
> Une fois créée, vous ne pouvez pas modifier la taille des nœuds de passerelle dédiés. Toutefois, vous pouvez ajouter ou supprimer des nœuds.

## <a name="dedicated-gateway-in-multi-region-accounts"></a>Passerelle dédiée dans les comptes à plusieurs régions

Lorsque vous approvisionnez un cluster de passerelle dédié dans des comptes à plusieurs régions, des clusters de passerelle dédiés identiques sont approvisionnés dans chaque région. Par exemple, considérez un compte Azure Cosmos DB dans les régions USA Est et Europe Nord. Si vous configurez un cluster de passerelle dédié avec deux nœuds D8 dans ce compte, vous auriez quatre nœuds D8 au total - deux dans la région USA Est et deux dans la région Europe Nord. Vous n’avez pas besoin de configurer explicitement des passerelles dédiées dans chaque région, et votre chaîne de connexion reste la même. Il n’y a pas non plus de modifications apportées aux meilleures pratiques pour effectuer des basculements.

> [!NOTE]
> Vous ne pouvez pas approvisionner un cluster de passerelle dédié dans des comptes avec des zones de disponibilité activées

Comme les nœuds d’un cluster, les nœuds de passerelle dédiés dans les régions sont indépendants. Il est possible que les données mises en cache dans chaque région soient différentes, en fonction des lectures ou écritures récentes dans cette région.

## <a name="limitations"></a>Limites

La passerelle dédiée présente les limitations suivantes pendant la préversion publique :

- Les passerelles dédiées sont uniquement prises en charge sur les comptes d’API SQL.
- Vous ne pouvez pas approvisionner une passerelle dédiée dans des comptes Azure Cosmos DB avec des [pare-feu IP](how-to-configure-firewall.md) ou [Azure Private Link](how-to-configure-private-endpoints.md) configuré.
- Vous ne pouvez pas approvisionner une passerelle dédiée dans un compte Azure Cosmos DB dans un [Réseau virtuel (Vnet)](how-to-configure-vnet-service-endpoint.md)
- Vous ne pouvez pas approvisionner une passerelle dédiée dans des comptes Azure Cosmos DB avec des [zones de disponibilité](high-availability.md#availability-zone-support).
- Vous ne pouvez pas utiliser le [contrôle d’accès en fonction du rôle (RBAC)](how-to-setup-rbac.md) pour authentifier les demandes de plan de données routées via la passerelle dédiée

## <a name="supported-regions"></a>Régions prises en charge

La passerelle dédiée est en version préliminaire publique et n’est pas encore prise en charge dans toutes les régions Azure. Tout au long de la version préliminaire publique, nous ajouterons de nouvelles capacités. Nous n’aurons pas de restrictions de région quand la passerelle dédiée est généralement disponible.

Liste actuelle des régions Azure prises en charge :

| **Amérique** | **Europe et Afrique**  | **Asie-Pacifique**  |
| ------------ | -------- | ----------- | 
| Brésil Sud      | France Centre    | Centre de l’Australie |
| Centre du Canada  | France Sud    | Centre de l’Australie 2 |
| Est du Canada     | Allemagne Nord   | Australie Sud-Est |
| USA Centre     | Allemagne Centre-Ouest   | Inde centrale |
| USA Est     | Europe Nord   | Asie Est |
| USA Est 2     | Suisse Nord   | OuJapon Est |
| Centre-Nord des États-Unis     | Sud du Royaume-Uni   | Centre de la Corée |
| États-Unis - partie centrale méridionale     | Ouest du Royaume-Uni   | Corée du Sud |
| Centre-USA Ouest     | Europe Ouest   | Asie Sud-Est |
| USA Ouest     |   | Émirats arabes unis Centre |
| USA Ouest 2     |    | Inde Ouest |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation des passerelles dédiées, consultez les articles suivants :

- [Cache intégré](integrated-cache.md)
- [Configurer le cache intégré](how-to-configure-integrated-cache.md)
- [FAQ sur le cache intégré](integrated-cache-faq.md)
- Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
    - Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](convert-vcore-to-request-unit.md) 
    - Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)