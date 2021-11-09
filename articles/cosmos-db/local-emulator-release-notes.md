---
title: Téléchargement de l’émulateur Azure Cosmos DB et notes de publication
description: Procurez-vous les notes de publication de l’émulateur Azure Cosmos DB pour connaître les différentes versions et les informations de téléchargement.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: b87f9cef973be22397773ed11701362662f34793
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070980"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Émulateur Azure Cosmos DB – Notes de publication et informations sur le téléchargement
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Cet article présente les notes de publication de l’émulateur Azure Cosmos DB avec la liste des mises à jour de fonctionnalités qui ont été apportées à chaque version. Il liste également la dernière version de l’émulateur pour vous permettre de la télécharger et de l’utiliser.

## <a name="download"></a>Télécharger

| | Lien |
|---------|---------|
|**Téléchargement de MSI**|[Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Prise en main**|[Développer localement avec l’émulateur Azure Cosmos DB](local-emulator.md)|

## <a name="release-notes"></a>Notes de publication

### <a name="2144-25-october-2021"></a>2.14.4 (25 octobre 2021)

 - Cette version met à jour les services Cosmos Emulator en arrière-plan afin qu’ils correspondent aux dernières fonctionnalités en ligne des services Azure Cosmos DB.

### <a name="2143-8-september-2021"></a>2.14.3 (8 septembre 2021)

 - Cette version met à jour les services d’arrière-plan de l’émulateur Cosmos afin qu’ils correspondent aux dernières fonctionnalités d’Azure Cosmos DB, résout des problèmes liés aux données de télémétrie collectées et réinitialise l’image de base pour l’image Docker de l’émulateur Cosmos Linux.

### <a name="2142-12-august-2021"></a>2.14.2 (12 août 2021)

 - Cette version met à jour le contenu de l’Explorateur de données locales vers la dernière version du Portail Azure et réinitialise la base de l’image de l’assistant Docker de l’émulateur Linux Cosmos.

### <a name="2141-18-june-2021"></a>2.14.1 (18 juin 2021)

 - Cette version améliore le temps de démarrage de l’émulateur tout en réduisant l’empreinte de ses données sur le disque. Cette nouvelle optimisation est activée par l’argument « /EnablePreview ».

### <a name="2140-15-june-2021"></a>2.14.0 (15 juin 2021)

 - Cette version met à jour le contenu de l’Explorateur de données locales vers la dernière version du Portail Azure ; dans cette version, nous répondons à un problème connu lors de l’importation de plusieurs éléments de document à l’aide de la fonctionnalité de téléchargement de fichiers JSON.

### <a name="21113-21-april-2021"></a>2.11.13 (21 avril 2021)

 - Cette version met à jour le contenu de l’Explorateur de données local vers la dernière version du portail Azure et ajoute une nouvelle configuration de point de terminaison MongoDB, « 4.0 ».

### <a name="21111-22-february-2021"></a>2.11.11 (22 février 2021)

 - Cette version met à jour le contenu de l’Explorateur de données local vers la dernière version du portail Azure.


### <a name="21110-5-january-2021"></a>2.11.10 (5 janvier 2021)

 - Cette version met à jour le contenu de l’Explorateur de données local vers la dernière version du portail Azure, et ajoute une nouvelle option publique (« /ExportPemCert ») qui permet à l’utilisateur de l’émulateur d’exporter directement le certificat de l’émulateur public sous forme d’un fichier .PEM.

### <a name="2119-3-december-2020"></a>2.11.9 (3 décembre 2020)

 - Cette version traite deux problèmes liés à la fonctionnalité d’émulateur Azure Cosmos DB en plus de la mise à jour de contenu générale qui reflète les fonctionnalités et améliorations les plus récentes dans Azure Cosmos DB :
 * Résolution d’un problème lié au fait que les demandes de charge utile de document volumineux échouent lors de l’utilisation du mode direct et des applications clientes Java.
 * Résolution d’un problème de connectivité avec le point de terminaison MongoDB version 3.6 quand il est ciblé par des applications .NET.

### <a name="2118-6-november-2020"></a>2.11.8 (6 novembre 2020)

 - Cette version comprend une mise à jour pour l’émulateur Cosmos de l’Explorateur de données et corrige un problème où les clients TLS 1.3 essaient d’ouvrir l’Explorateur de données.

### <a name="2116-6-october-2020"></a>2.11.6 (6 octobre 2020)

 - Cette version résout un problème de concurrence quand plusieurs conteneurs pourraient être créés au même moment. Dans ces cas, les données de l’émulateur sont laissées dans un état endommagé et les requêtes d’API suivantes adressées au point de terminaison de l’émulateur peuvent échouer avec des erreurs de type « service indisponible », ce qui nécessite un redémarrage et une réinitialisation des données locales de l’émulateur.

### <a name="2115-23-august-2020"></a>2.11.5 (23 août 2020)

Cette version ajoute deux nouvelles options de démarrage de l’émulateur Cosmos : 

* « /EnablePreview » active les fonctionnalités en préversion pour l’émulateur. Il s’agit des fonctionnalités de préversion qui sont encore en cours de développement ; elles sont accessibles via l’intégration continue et l’exemple d’écriture.
* « /EnableAadAuthentication » permet à l’émulateur d’accepter des jetons Azure Active Directory personnalisés comme alternative aux clés primaires Azure Cosmos. Cette fonctionnalité est toujours en cours de développement ; les affectations de rôles spécifiques et les autres paramètres liés aux autorisations ne sont pas pris en charge actuellement.

### <a name="2112-07-july-2020"></a>2.11.2 (07 juillet 2020)

- Cette version modifie la manière dont sont collectées les traces ETL qui sont nécessaires lors du dépannage de l’émulateur Cosmos. Les outils WPR (Windows Performance Runtime) sont désormais utilisés par défaut pour la capture des traces ETL. La capture basée sur LOGMAN est désormais dépréciée. Ce changement a été en partie nécessaire à cause des mises à jour de sécurité Windows récentes qui impactent le fonctionnement de LOGMAN lorsque celui-ci est exécuté via l’émulateur Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 juin 2020)

- Cette version corrige deux bogues liés à l’émulateur Data Explorer. Dans certains cas, quand vous utilisez l’émulateur Data Explorer par le biais d’un navigateur web, il ne parvient pas à se connecter au point de terminaison de l’émulateur Cosmos, et toutes les actions associées, telles que la création d’une base de données ou d’un conteneur, génèrent une erreur. Le deuxième problème résolu est lié à la création d’un élément à partir d’un fichier JSON à l’aide de l’action de chargement de Data Explorer.

### <a name="2110"></a>2.11.0

- Cette version introduit la prise en charge du débit provisionné par la mise à l’échelle automatique. Ces nouvelles fonctionnalités comprennent la possibilité de définir un niveau personnalisé du débit maximal provisionné dans les unités de requête (RU/s), d’activer la mise à l’échelle automatique sur les bases de données et conteneurs existants ainsi que la prise en charge programmatique au moyen des kits SDK Azure Cosmos DB.
- Correction d’un problème lors de l’interrogation d’un grand nombre de documents (plus de 1 Go) pendant lequel l’émulateur échoue avec le code d’état d’erreur interne 500.

### <a name="292"></a>2.9.2

- Cette version corrige un bogue pendant l’activation de la prise en charge du point de terminaison MongoDb version 3.2. Elle ajoute également la prise en charge de la génération de traces ETL à des fins de dépannage à l’aide de WPR au lieu de LOGMAN.

### <a name="291"></a>2.9.1

- Cette version corrige deux problèmes dans la prise en charge de l’API de requête, et restaure la compatibilité avec les anciens systèmes d’exploitation tels que Windows Server 2012.

### <a name="290"></a>2.9.0

- Cette version ajoute une option permettant de définir la cohérence avec un préfixe cohérent et d’augmenter les limites maximales pour les utilisateurs et les autorisations.

### <a name="272"></a>2.7.2

- Cette version ajoute la prise en charge du serveur MongoDB version 3.6 à l’émulateur Cosmos. Pour démarrer un point de terminaison MongoDB qui cible la version 3.6 du service, démarrez l’émulateur à partir d’une ligne de commande d’administrateur avec l’option « /EnableMongoDBEndpoint=3.6 ».

### <a name="270"></a>2.7.0

- Cette version corrige une régression qui empêchait les utilisateurs d’exécuter des requêtes sur le compte d’API SQL à partir de l’émulateur lors de l’utilisation de clients .NET Core ou .NET x86.

### <a name="246"></a>2.4.6

- Cette version offre une parité avec les fonctionnalités du service Azure Cosmos à partir de juillet 2019, avec les exceptions indiquées dans [Développer localement avec l’émulateur Azure Cosmos DB](local-emulator.md). Elle résout aussi plusieurs bogues liés à l’arrêt de l’émulateur quand il est appelé via la ligne de commande, et les remplacements d’adresses IP internes pour les clients SDK utilisant une connectivité en mode direct.

### <a name="243"></a>2.4.3

- Désactivation du démarrage du service de MongoDB par défaut. Seul le point de terminaison SQL est activé par défaut. L’utilisateur doit démarrer le point de terminaison manuellement à l’aide de l’option de ligne de commande /EnableMongoDbEndpoint. C’est maintenant similaire aux autres points de terminaison de service, tels que Gremlin, Cassandra et Table.
- Correction d’un bogue dans l’émulateur lors du démarrage avec « /AllowNetworkAccess », où les points de terminaison Gremlin, Cassandra et Table ne géraient pas correctement les requêtes des clients externes.
- Ajoutez des ports de connexion directe aux paramètres de règles de pare-feu.

### <a name="240"></a>2.4.0

- Correction d’un problème avec l’émulateur, qui ne parvenait pas à démarrer lorsque des applications de surveillance réseau, comme le client Pulse, sont présentes sur l’ordinateur hôte.
