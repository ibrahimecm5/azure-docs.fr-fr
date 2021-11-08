---
title: Intégration d’Apache Kafka sur Confluent Cloud avec le Connecteur de service
description: Intégrez Apache Kafka sur Confluent Cloud dans votre application avec le Connecteur de service.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c0b5c47982fb0b3af7b2ecac6fde12f1e2f2f101
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096296"
---
# <a name="integrate-apache-kafka-on-confluent-cloud-with-service-connector"></a>Intégration d’Apache Kafka sur Confluent Cloud avec le Connecteur de service

Cette page présente les types d’authentification et de client d’Apache Kafka sur Confluent Cloud pris en charge avec le Connecteur de service. Vous pourrez peut-être vous connecter à Apache Kafka sur Confluent Cloud dans d’autres langages de programmation sans utiliser le Connecteur de service. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net | | | ![icône Oui](./media/green-check.png) | |
| Java | | | ![icône Oui](./media/green-check.png) | |
| Java - Spring Boot | | | ![icône Oui](./media/green-check.png) | |
| Node.js | | | ![icône Oui](./media/green-check.png) | |
| Python | | | ![icône Oui](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.js et Python

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_CONFLUENTCLOUDKAFKA_BOOTSTRAPSERVER | Votre serveur de démarrage Kafka | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| AZURE_CONFLUENTCLOUDKAFKA_KAFKASASLCONFIG | Votre configuration SASL Kafka | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_URL | L’URL de votre registre Confluent | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_USERINFO |  Les informations relatives aux utilisateurs de votre registre Confluent | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

### <a name="spring-boot"></a>Spring Boot

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| spring.kafka.properties.bootstrap.servers | Votre serveur de démarrage Kafka | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| spring.kafka.properties.sasl.jaas.config | Votre configuration SASL Kafka | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| spring.kafka.properties.schema.registry.url | L’URL de votre registre Confluent | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| spring.kafka.properties.schema.registry.basic.auth.user.info | Les informations relatives aux utilisateurs de votre registre Confluent | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
