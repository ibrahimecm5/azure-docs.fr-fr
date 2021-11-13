---
title: 'Didacticiel : Déployer une application Spring Boot connectée à Apache Kafka sur Confluent Cloud avec un connecteur de service dans Azure Spring Cloud'
description: Créez une application Spring Boot connectée à Apache Kafka sur Confluent Cloud avec un connecteur de service dans Azure Spring Cloud.
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a407d164ec0214ddce6e1d8bf6254876d3642230
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449219"
---
# <a name="tutorial-deploy-a-spring-boot-app-connected-to-apache-kafka-on-confluent-cloud-with-service-connector-in-azure-spring-cloud"></a>Didacticiel : Déployer une application Spring Boot connectée à Apache Kafka sur Confluent Cloud avec un connecteur de service dans Azure Spring Cloud

Découvrez comment accéder à Apache Kafka sur Confluent Cloud pour une application Spring Boot s’exécutant sur Azure Spring Cloud. Dans ce tutoriel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer Apache Kafka sur Confluent Cloud
> * Créer une application Spring Cloud
> * Créer et déployer l’application Spring Boot
> * Connecter Apache Kafka sur Confluent Cloud à Azure Spring Cloud à l’aide d’un connecteur de service

## <a name="1-set-up-your-initial-environment"></a>1. Configurer votre environnement initial

1. Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Installez Java 8 ou 11.
3. Installez l’interface <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 ou version ultérieure, avec laquelle vous exécutez des commandes dans n’importe quel interpréteur de commandes pour provisionner et configurer des ressources Azure.

## <a name="2-clone-or-download-the-sample-app"></a>2. Cloner ou télécharger l’exemple d’application

Clonez l’exemple de dépôt :

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

Accédez ensuite à ce dossier :

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## <a name="3-prepare-cloud-services"></a>3. Préparer les services cloud

### <a name="31-create-an-instance-of-apache-kafka-for-confluent-cloud"></a>3.1 Créer une instance de Apache Kafka pour Confluent Cloud

Créez une instance d’Apache Kafka pour Confluent Cloud, en suivant [ces conseils](../partner-solutions/apache-kafka-confluent-cloud/create.md).

### <a name="32-create-kafka-cluster-and-schema-registry-on-confluent-cloud"></a>3.2 Créer un cluster et un registre de schémas Kafka sur Confluent Cloud

1. Connectez-vous à Confluent Cloud par l’authentification unique (SSO) fournie par Azure

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="Lien de connexion SSO à Confluent Cloud à l’aide du portail Azure" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. Utilisez l’environnement par défaut ou créer-en un

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Environnement cloud d’Apache Kafka sur Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. Créez un cluster Kafka avec les informations suivantes

    * Type de cluster : standard
    * Région/zones : eastus (Virginie), zone unique
    * Nom du cluster : `cluster_1` ou tout autre nom.

1. Dans **Vue d’ensemble du cluster** -> **Paramètres du cluster**, récupérez l’**URL du serveur de démarrage** Kafka et notez-la.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Paramètres de cluster d’Apache Kafka sur Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. Créer des clés API pour le cluster dans **Intégration de données** -> **Clés API** ->  **+ Ajouter une clé** avec **Accès global**. Notez la clé et le secret.
1. Créez une rubrique nommée `test` avec les partitions 6 dans **Rubriques** ->  **+ Ajouter une rubrique**
1. Dans l’environnement par défaut, cliquez sur l’onglet **Registre de schémas**. Activez le registre de schémas et notez le **Point de terminaison d’API**.
1. Créer des clés API pour le registre de schémas. Notez la clé et le secret.

### <a name="33-create-a-spring-cloud-instance"></a>3.3 Créer une instance Spring Cloud

Créez une instance d’Azure Spring Cloud en suivant [ces conseils](../spring-cloud/quickstart.md) dans Java. Assurez-vous que votre instance Spring Cloud est créée dans [la région qui dispose du support du connecteur de service](concept-region-support.md).

## <a name="4-build-and-deploy-the-app"></a>4. Générer et déployer l’application

### <a name="41-build-the-sample-app-and-create-a-new-spring-app"></a>4.1 Générer l’exemple d’application et créer une application Spring

1. Connectez-vous à Azure et définissez votre abonnement.

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Générer le projet à l’aide de Gradle

```Bash
./gradlew build
```

1. Créez l’application avec un point de terminaison public affecté. Si vous avez sélectionné Java version 11 lors de la génération du projet Spring Cloud, incluez le commutateur --runtime-version=Java_11.

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## <a name="42-create-service-connection-using-service-connector"></a>4.2 Créer une connexion de service à l’aide du connecteur de service

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/Azure-CLI)

Exécutez la commande suivante pour connecter votre Apache Kafka sur Confluent Cloud à votre application Spring Cloud.

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* **Remplacez** *\<your-resource-group-name>* par le nom du groupe de ressources que vous avez créé pour votre instance Spring Cloud.
* **Remplacez** *\<kafka-bootstrap-server-url>* par l’URL de votre serveur de démarrage Kafka (la valeur doit être similaire à `pkc-xxxx.eastus.azure.confluent.cloud:9092`).
* **Remplacez** *\<cluster-api-key>* et *\<cluster-api-secret>* par les clé et secret API de votre cluster.
* **Remplacez** *\<kafka-schema-registry-endpoint>* par votre point de terminaison de registre de schémas Kafka (la valeur doit être similaire à `https://psrc-xxxx.westus2.azure.confluent.cloud`)
* **Remplacez** *\<registry-api-key>* et *\<registry-api-secret>* par les clé et secret API de votre cluster votre registre de schémas Kafka.

> [!NOTE]
> Si vous voyez le message d’erreur « L’abonnement n’est pas inscrit pour utiliser Microsoft.ServiceLinker », exécutez `az provider register -n Microsoft.ServiceLinker` pour inscrire le fournisseur de ressources Service Connector et réexécutez la commande de connexion. 

#### <a name="portal"></a>[Portail](#tab/Azure-portal)

Cliquez sur **Connecteur de service (préversion)** , puis sélectionnez ou entrez les paramètres suivants.

| Paramètre      | Valeur suggérée  | Description                               |
| ------------ |  ------- | -------------------------------------------------- |
| **Type de service** | Apache Kafka sur Confluent Cloud | Type du service cible. Si vous n’avez pas d’Apache Kafka sur Confluent Cloud, suivez les étapes précédentes de ce didacticiel. |
| **Nom** | Nom unique généré | Nom de connexion qui identifie la connexion entre votre Spring Cloud et le service cible  |
| **URL du serveur de démarrage Kafka** | URL de votre serveur de démarrage Kafka | Vous pouvez obtenir cette valeur à partir de l’étape 3.2 |
| **Clé API du cluster** | Clé API de votre cluster |  |
| **Secret API du cluster** |  Secret API de votre cluster |  |
| **Créer une connexion pour le registre de schémas**  | checked | Crée également une connexion au registre de schémas |
| **Point de terminaison du registre de schémas** | Point de terminaison de votre registre de schémas Kafka  |  |
| **Clé API du registre de schémas** | Clé API de votre registre de schémas Kafka | |
| **Secret API du registre de schémas** | Secret API de votre registre de schémas Kafka | |

Sélectionnez **Vérifier + créer** pour passer en revue les paramètres de la connexion. Ensuite, sélectionnez **Créer** pour démarrer la création de la connexion au service.

---

## <a name="43-deploy-the-jar-file-for-the-app"></a>4.3 déployer le fichier Jar pour l’application

Exécutez la commande suivante pour charger le fichier jar (`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`) dans votre application Spring Cloud

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## <a name="5-validate-the-kafka-data-ingestion"></a>5. Valider l’ingestion de données Kafka

Accédez au point de terminaison de votre application Spring Cloud à partir du portail Azure, puis cliquez sur l’URL de l’application. Le message « 10 messages ont été produits pour le test de rubrique » s’affiche.

Accédez ensuite au portail Confluent. La page de la rubrique indique le débit de production.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="Exemples de métriques" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
