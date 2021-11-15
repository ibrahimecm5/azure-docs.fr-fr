---
title: 'Tutoriel : Déployer une application Spring Cloud connectée à Azure Database pour MySQL avec Service Connector'
description: Créez une application Spring Boot connectée à Azure Database pour MySQL avec Service Connector.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f25ab8e1eb144675567316194c9b4ca3a547f111
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475233"
---
# <a name="tutorial-deploy-spring-cloud-application-connected-to-azure-database-for-mysql-with-service-connector"></a>Tutoriel : Déployer une application Spring Cloud connectée à Azure Database pour MySQL avec Service Connector

Dans ce tutoriel, vous allez effectuer les tâches suivantes à l’aide du portail Azure ou d’Azure CLI. Les deux méthodes sont expliquées dans les procédures suivantes.

> [!div class="checklist"]
> * Provisionner une instance Azure Spring Cloud
> * Créer et déployer des applications sur Azure Spring Cloud
> * Intégrer Azure Spring Cloud à Azure Database pour MySQL avec Service Connector

## <a name="1-prerequisites"></a>1. Prérequis

* [Installez JDK 8 ou JDK 11](/azure/developer/java/fundamentals/java-jdk-install)
* [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)
* [Installez Azure CLI version 2.0.67 ou ultérieure](/cli/azure/install-azure-cli) et installez l’extension Azure Spring Cloud avec la commande : `az extension add --name spring-cloud`

## <a name="2-provision-an-instance-of-azure-spring-cloud"></a>2. Provisionner une instance Azure Spring Cloud

La procédure suivante utilise l’extension Azure CLI pour provisionner une instance d’Azure Spring Cloud.

1. Mettez à jour Azure CLI avec l’extension Azure Spring Cloud.

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Connectez-vous à Azure CLI et choisissez votre abonnement actif.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Préparez un nom pour votre service Azure Spring Cloud.  Le nom doit comporter entre 4 et 32 caractères, et contenir uniquement des lettres minuscules, des chiffres et des traits d’union.  Le premier caractère du nom du service doit être une lettre, et le dernier doit être une lettre ou un chiffre.

1. Créez un groupe de ressources destiné à contenir votre service Azure Spring Cloud.  Créez une instance du service Cloud Azure Spring.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg
    az spring-cloud create -n <service instance name> -g ServiceConnector-tutorial-rg
    ```

## <a name="3-create-an-azure-database-for-mysql"></a>3. Créer une base de données Azure Database pour MySQL

La procédure suivante utilise l’extension Azure CLI pour provisionner une instance d’Azure Database pour MySQL.

1. Installez l’extension [db-up](/cli/azure/ext/db-up/mysql).

```azurecli
az extension add --name db-up
```

Créez un serveur Azure Database pour MySQL à l’aide de la commande suivante :

```azurecli
az mysql up --resource-group ServiceConnector-tutorial-rg --admin-user <admin-username> --admin-password <admin-password>
```

- Pour *\<admin-username>* et *\<admin-password>* , spécifiez les informations d’identification afin de créer un utilisateur administrateur pour ce serveur MySQL. Le nom d’utilisateur de l’administrateur ne doit pas être *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* ou *public*. Il ne peut pas commencer par *pg_* . Le mot de passe doit contenir entre **8 et 128 caractères** de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (par exemple, !, #, %). Le mot de passe ne peut pas contenir le nom d’utilisateur.

Le serveur est créé avec les valeurs par défaut suivantes (sauf si vous les remplacez manuellement) :

**Paramètre** | **Valeur par défaut** | **Description**
---|---|---
server-name | Générée par le système | Un nom unique qui identifie votre serveur Azure Database pour MySQL.
sku-name | GP_Gen5_2 | Nom du la référence SKU. Suit la convention {niveau tarifaire}\_{génération de calcul}\_{vCores} dans le raccourci. Par défaut, il s’agit d’un serveur à usage général de 5e génération à 2 vCores. Consultez notre [page de tarifs](https://azure.microsoft.com/pricing/details/mysql/) pour plus d’informations sur les niveaux de service.
backup-retention | 7 | Durée pendant laquelle la sauvegarde doit être conservée. Exprimée en jours.
geo-redundant-backup | Désactivé | Indique si les sauvegardes géoredondantes doivent être activées ou non pour ce serveur.
location | westus2 | Emplacement Azure du serveur.
ssl-enforcement | activé | Indique si le protocole SSL doit être activé ou non pour ce serveur.
storage-size | 5120 | Capacité de stockage du serveur (en mégaoctets).
version | 5.7 | Version principale de MySQL.


> [!NOTE]
> Pour plus d’informations sur la commande `az mysql up` et ses paramètres supplémentaires, consultez la [documentation Azure CLI](/cli/azure/mysql#az_mysql_up).

Une fois que votre serveur est créé, il présente les paramètres suivants :

- Une règle de pare-feu appelée « devbox » est créée. Azure CLI tente de détecter l’adresse IP de la machine à partir de laquelle la commande `az mysql up` est exécutée et autorise cette adresse IP.
- « Autoriser l’accès aux services Azure » est défini sur ACTIVÉ. Ce paramètre configure le pare-feu du serveur pour qu’il accepte les connexions de toutes les ressources Azure, y compris celles qui ne font pas partie de votre abonnement.
- Le paramètre `wait_timeout` est défini sur 8 heures
- Une base de données nommée `sampledb` est créée
- Un utilisateur nommé « root » est créé avec des privilèges `sampledb`

## <a name="4-build-and-deploy-the-app"></a>4. Générer et déployer l’application

1. Créez l’application avec un point de terminaison public affecté. Si vous avez sélectionné Java version 11 lors de la génération du projet Spring Cloud, incluez le commutateur `--runtime-version=Java_11`.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg --assign-endpoint true
    ```

1. Créez des connexions de service entre Spring Cloud et la base de données MySQL.

    ```azurecli
    az spring-cloud connection create mysql
    ```

    > [!NOTE]
    > Si vous voyez le message d’erreur « L’abonnement n’est pas inscrit pour utiliser Microsoft.ServiceLinker », exécutez `az provider register -n Microsoft.ServiceLinker` pour inscrire le fournisseur de ressources Service Connector et réexécutez la commande de connexion. 

1. Cloner l’exemple de code

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-springcloud-mysql-springboot.git
    ```

1. Générez le projet avec Maven.

    ```bash
    cd serviceconnector-springcloud-mysql-springboot
    mvn clean package -DskipTests 
    ```

1. Déployez le fichier Jar pour l’application (`target/demo-0.0.1-SNAPSHOT.jar`).

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg  --artifact-path target/demo-0.0.1-SNAPSHOT.jar
    ```

1. Interrogez l’état de l’application après les déploiements à l’aide de la commande suivante.

    ```azurecli
    az spring-cloud app list -o table
    ```

    Un résultat similaire à ce qui suit doit s’afficher.

    ```
    Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
    -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
    hellospring         eastus    <resource group>   default                                                                       Succeeded              1      2         1/1                 0/1                    -
    ```

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
