---
title: Services internes de Service Connector
description: Découvrez les éléments internes Service Connector, l’architecture, les connexions et la façon dont les données sont transmises.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fda89409a39644057ebd76d949665b281d32dc2a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501663"
---
# <a name="service-connector-internals"></a>Services internes de Service Connector

Service Connector est un fournisseur de ressources d’extension qui propose un moyen simple de créer et de gérer les connexions entre les services.
- Prenez en charge les principales bases de données, le stockage, les services en temps réel, l’état et les magasins secrets utilisés avec votre application cloud native (la liste est développée activement).
- Configurez les paramètres réseau, l’authentification et la gestion des variables ou des propriétés d’environnement de connexion en créant une connexion de service à l’aide d’une seule commande ou en quelques clics.
- Validez les connexions et recherchez les suggestions correspondantes pour résoudre une connexion de service. 

## <a name="service-connection-overview"></a>Vue d’ensemble de la connexion de service

La connexion de service est le concept clé dans le modèle de ressource de Service Connector. Dans Service Connector, une connexion de service représente une abstraction du lien entre deux services. Les propriétés suivantes sont définies sur la connexion de service.

| Propriété | Description |
|--------|-----------|
| Nom de connexion | Nom unique de la connexion de service.  |
| Type de service source | Les services sources sont généralement des services de calcul Azure. Vous trouverez des fonctionnalités Service Connector dans les services de calcul pris en charge en étendant ces fournisseurs de services de calcul Azure.  |
| Type de service cible | Les services cibles sont des services de stockage ou des services de dépendance auxquels vos services de calcul se connectent. Service Connector prend en charge différents types de service cible, notamment les principales bases de données, le stockage, les services en temps réel, l’état et les magasins secrets. |
| Type de client | Le type de client fait référence à votre pile de runtime de calcul, votre infrastructure de développement ou le type de bibliothèque de client spécifique, qui accepte le format spécifique des variables ou des propriétés d’environnement de connexion. |
| Type d’authentification | Type d’authentification utilisé pour la connexion de service. Il peut s’agir d’une chaîne de connexion/clé secrète pure, d’une identité managée ou d’un principal de service. |

Vous pouvez créer plusieurs connexions de service à partir d’une instance de service source si votre instance doit connecter plusieurs ressources cibles. La même ressource cible peut être connectée à partir de plusieurs instances sources. Service Connector gère toutes les connexions dans les propriétés de leur instance source. Cela signifie que vous pouvez créer, récupérer, mettre à jour et supprimer les connexions dans le portail ou la commande CLI de leur instance de service source. 

La connexion prend en charge l’abonnement ou le locataire. Les services source et cible peuvent appartenir à des abonnements ou des locataires différents. Lorsque vous créez une connexion de service, la ressource de connexion se trouve dans la même région que votre instance de service de calcul par défaut.

## <a name="create-or-update-a-service-connection"></a>Créer ou mettre à jour d’une connexion de service

Service Connector effectue plusieurs étapes lors de la création ou de la mise à jour d’une connexion, notamment :

- Configurez les paramètres du réseau et du pare-feu des ressources cibles, en vous assurant que les services source et cible peuvent communiquer entre eux au niveau du réseau.
- Configurer les informations de connexion sur la ressource source
- Configurer les informations d’authentification sur la source et la cible si nécessaire
- La création ou la mise à jour de la connexion prennent en charge la restauration en cas d’échec.

La création et la mise à jour d’une connexion contiennent plusieurs étapes. Si une étape échoue, Service Connector restaure toutes les étapes précédentes pour conserver les paramètres initiaux dans les instances source et cible.

## <a name="connection-configurations"></a>Configurations de la connexion

Une fois qu’une connexion de service est créée, la configuration de la connexion est définie sur le service source.
Dans le portail, accédez à la page **Service Connector (version préliminaire)** . Vous pouvez développer chaque connexion et afficher les configurations de connexion.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="Répertorier la configuration du portail":::

Dans l’interface CLI, vous pouvez utiliser la commande `list-configuration` pour afficher la configuration de la connexion.

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>Convention de nommage pour une configuration

Service Connector définit la configuration (variables d’environnement ou configurations Spring Boot) lors de la création d’une connexion. La ou les paires clé-valeur de variable d’environnement sont déterminées par votre type de client et le type d’authentification. Par exemple, l’utilisation du kit de développement logiciel (SDK) Azure avec une identité managée requiert l’ID client, la clé secrète client, etc. L’utilisation du pilote JDBC requiert une chaîne de connexion de base de données. La règle de nommage de la configuration est la suivante.

Si vous utilisez **Spring Boot** comme type de client :

* La bibliothèque Spring Boot possède sa propre convention de nommage pour chaque service cible. Par exemple, les paramètres de connexion MySQL seraient `spring.datasource.url`, `spring.datasource.username`, `spring.datasource.password`. Les paramètres de connexion Kafka seraient `spring.kafka.properties.bootstrap.servers`.

Si vous utilisez d’**autres types de clients**, à l’exception de Spring Boot :

* Quand vous vous connectez à un service cible, le nom de clé de la première configuration de connexion est au format `{Cloud}_{Type}_{Name}`. Par exemple, `AZURE_STORAGEBLOB_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER`. 
* Pour le même type de ressource cible, le nom de clé de la deuxième configuration de connexion doit être au format `{Cloud}_{Type}_{Connection Name}_{Name}`. Par exemple, `AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER`.

## <a name="validate-a-service-connection"></a>Valider une connexion de service
Les éléments suivants sont vérifiés lors de la validation de la connexion :

* Vérifier si des ressources source et cible existent
* Valider les paramètres de réseau et de pare-feu de la ressource cible
* Valider les informations de connexion sur la ressource source
* Valider les informations d’authentification sur la source et la cible si nécessaire

## <a name="delete-connection"></a>Supprimer la connexion

Les informations de connexion sur la ressource source seront supprimées lors de la suppression de la connexion. 
