---
title: 'Tutoriel : Déployer une application Spring Boot sur un cluster AKS avec un serveur flexible MySQL au sein d’un réseau virtuel'
description: Découvrez comment créer rapidement une application Spring Boot et la déployer sur AKS avec Azure Database pour MySQL - Serveur flexible, avec une connectivité sécurisée au sein d’un réseau virtuel.
ms.service: mysql
author: shreyaaithal
ms.author: shaithal
ms.topic: tutorial
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8b07ffd9ea4d9cbcba5cf36bcac048f1abccee37
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557352"
---
# <a name="tutorial-deploy-a-spring-boot-application-on-aks-cluster-with-mysql-flexible-server-in-a-vnet"></a>Tutoriel : Déployer une application Spring Boot sur un cluster AKS avec un serveur flexible MySQL dans un réseau virtuel

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Dans ce tutoriel, vous allez apprendre à déployer une application [Spring Boot](https://spring.io/projects/spring-boot) sur un cluster [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md) avec [Azure Database pour MySQL - Serveur flexible](overview.md) dans le back-end, les deux pouvant communiquer de manière sécurisée au sein d’un [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). 

> [!NOTE]
> Ce tutoriel suppose que vous disposez de connaissances de base sur les concepts de Kubernetes ainsi que sur Java Spring Boot et MySQL.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli).
- Un [Java Development Kit](/azure/developer/java/fundamentals/java-support-on-azure) pris en charge, version 8 (inclus dans Azure Cloud Shell).
- L’outil de build [Apache Maven](https://maven.apache.org/).
- Un client [Git](https://github.com/).
- Un client [Docker](https://www.docker.com/).

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Créer un serveur flexible Azure Database pour MySQL 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. Créons un groupe de ressources, *rg-mysqlaksdemo*, avec la commande [az group create](/cli/azure/group#az_group_create) dans la localisation *eastus*.

1. Ouvrez l’invite de commandes. 
1. Connectez-vous à votre compte Azure.
    ```azurecli-interactive
    az login
    ```
1. Choisissez votre abonnement Azure.
    ```azurecli-interactive
    az account set -s <your-subscription-ID>
    ```    
1. Créez le groupe de ressources.
    ```azurecli-interactive
    az group create --name rg-mysqlaksdemo --location eastus
    ```

### <a name="create-a-mysql-flexible-server"></a>Créer un serveur flexible MySQL

Nous allons maintenant créer un serveur flexible dans un réseau virtuel (méthode de connectivité d’accès privé).

1. Créez un réseau virtuel Azure *vnet-mysqlaksdemo* pour toutes les ressources de ce tutoriel et un sous-réseau *subnet-mysql* pour le serveur flexible MySQL.

    ```azurecli-interactive
    az network vnet create \
    --resource-group rg-mysqlaksdemo \
    --name vnet-mysqlaksdemo \
    --address-prefixes 155.55.0.0/16 \
    --subnet-name subnet-mysql \
    --subnet-prefix 155.55.1.0/24 
    ```

1. Créez un serveur flexible Azure Database pour MySQL *mysql-mysqlaksdemo* dans le sous-réseau créé ci-dessus, avec la commande [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create). Remplacez vos valeurs pour le nom d’utilisateur et le mot de passe d’administrateur.

    ```azurecli-interactive
    az mysql flexible-server create \
    --name mysql-mysqlaksdemo \
    --resource-group rg-mysqlaksdemo \
    --location eastus \
    --admin-user <your-admin-username> \
    --admin-password <your-admin-password> \
    --vnet vnet-mysqlaksdemo \
    --subnet subnet-mysql
    ```
    
    Vous avez désormais créé un serveur flexible dans la région eastus avec un calcul B1MS Burstable, 32 Go de stockage, une période de conservation des sauvegardes de 7 jours et dans le sous-réseau *subnet-mysql* fourni. Ce sous-réseau ne doit pas contenir d’autres ressources déjà déployées. Il est délégué à Microsoft.DBforMySQL/flexibleServers.

1. Configurez une nouvelle base de données MySQL ```demo``` à utiliser avec l’application Spring Boot.

    ```azurecli-interactive
    az mysql flexible-server db create \
    --resource-group rg-mysqlaksdemo \
    --server-name mysql-mysqlaksdemo \
    --database-name demo
    ```

## <a name="create-an-azure-container-registry"></a>Créer un Azure Container Registry 

Créez un registre de conteneurs Azure privé dans le groupe de ressources. Ce tutoriel envoie (push) l’exemple d’application en tant qu’image Docker à ce registre au cours des étapes ultérieures. Remplacez ```mysqlaksdemoregistry``` par un nom unique pour votre registre.

```azurecli-interactive
az acr create --resource-group rg-mysqlaksdemo \
--location eastus \
--name mysqlaksdemoregistry \
--sku Basic
```

## <a name="code-the-application"></a>Coder l’application

Dans cette section, nous allons coder l’application de démonstration. Si vous souhaitez allez plus vite, vous pouvez télécharger l’application codée disponible à l’adresse [https://github.com/Azure-Samples/tutorial-springboot-mysql-aks](https://github.com/Azure-Samples/tutorial-springboot-mysql-aks) et passer à la section suivante, [Générer l’image et l’envoyer (push) vers ACR](#build-the-image-and-push-to-acr). 

1. Générez l’application avec Spring Initializr. 

    ```bash
    curl https://start.spring.io/starter.tgz \
    -d dependencies=web,data-jdbc,mysql \
    -d baseDir=springboot-mysql-aks \
    -d bootVersion=2.5.6.RELEASE \
    -d artifactId=springboot-mysql-aks \
    -d description="Spring Boot on AKS connecting to Azure DB for MySQL" \
    -d javaVersion=1.8 | tar -xzvf -
    ```
    
    Une application Spring Boot de base est générée dans le dossier ```springboot-mysql-aks```.
    
    Utilisez votre éditeur de texte préféré comme [VSCode](https://code.visualstudio.com/docs) ou n’importe quel IDE pour les étapes suivantes.

1. Configurez Spring Boot pour qu’il utilise Azure Database pour MySQL - Serveur flexible.

    Ouvrez le fichier src/main/resources/application.properties et ajoutez l’extrait de code ci-après. Ce code lit l’hôte de base de données, le nom de la base de données, le nom d’utilisateur et le mot de passe dans le fichier manifeste Kubernetes.
    
    ```properties
    logging.level.org.springframework.jdbc.core=DEBUG
    spring.datasource.url=jdbc:mysql://${DATABASE_HOST}:3306/${DATABASE_NAME}?serverTimezone=UTC
    spring.datasource.username=${DATABASE_USERNAME}
    spring.datasource.password=${DATABASE_PASSWORD}
    spring.datasource.initialization-mode=always
    ```
    >[!Warning]
    > La propriété de configuration ```spring.datasource.initialization-mode=always``` signifie que Spring Boot génèrera automatiquement un schéma de base de données, à l’aide du fichier ```schema.sql``` que nous créerons ultérieurement, à chaque démarrage du serveur. C’est très utile pour les tests, mais n’oubliez pas que cela supprimera vos données à chaque redémarrage. Par conséquent, ne l’utilisez pas en production.

    >[!Note]
    >Nous ajoutons ```?serverTimezone=UTC``` à la propriété de configuration ```spring.datasource.url``` pour indiquer au pilote JDBC d’utiliser le format de date UTC (Coordinated Universal Time) lors de la connexion à la base de données. Sans cela, notre serveur Java n’utiliserait pas le même format de date que la base de données, ce qui entraînerait une erreur.

1. Créez le schéma de la base de données.
    
    Spring Boot exécute automatiquement ```src/main/resources/schema.sql``` pour créer un schéma de base de données. Créez ce fichier avec le contenu suivant :

    ```sql
    DROP TABLE IF EXISTS todo;
    CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
    ```
1. Codez l’application Java Spring Boot.
    
    Ajoutez le code Java qui utilisera JDBC pour stocker et récupérer des données à partir de votre serveur MySQL. Créez une classe Java ```Todo``` parallèlement à la classe ```DemoApplication```, puis ajoutez le code suivant :

    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.annotation.Id;
    
    public class Todo {
    
        public Todo() {
        }
    
        public Todo(String description, String details, boolean done) {
            this.description = description;
            this.details = details;
            this.done = done;
        }
    
        @Id
        private Long id;
    
        private String description;
    
        private String details;
    
        private boolean done;
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void setDescription(String description) {
            this.description = description;
        }
    
        public String getDetails() {
            return details;
        }
    
        public void setDetails(String details) {
            this.details = details;
        }
    
        public boolean isDone() {
            return done;
        }
    
        public void setDone(boolean done) {
            this.done = done;
        }
    }
    ```
    Cette classe est un modèle de domaine mappé sur la table ```todo``` que vous avez créée précédemment.

    Pour gérer cette classe, vous avez besoin d’un référentiel. Définissez une nouvelle interface ```TodoRepository``` dans le même package :
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.repository.CrudRepository;
    
    public interface TodoRepository extends CrudRepository<Todo, Long> {
    }
    ```

    Ce dépôt est un dépôt géré par Spring Data JDBC.

    Terminez l’application en créant un contrôleur pouvant stocker et récupérer des données. Implémentez une classe ```TodoController``` dans le même package, puis ajoutez le code suivant :
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @RequestMapping("/")
    public class TodoController {
    
        private final TodoRepository todoRepository;
    
        public TodoController(TodoRepository todoRepository) {
            this.todoRepository = todoRepository;
        }
    
        @PostMapping("/")
        @ResponseStatus(HttpStatus.CREATED)
        public Todo createTodo(@RequestBody Todo todo) {
            return todoRepository.save(todo);
        }
    
        @GetMapping("/")
        public Iterable<Todo> getTodos() {
            return todoRepository.findAll();
        }
    }
    ```

1. Créez un fichier Dockerfile dans le répertoire de base *springboot-mysql-aks* et copiez cet extrait de code.

    ```dockerfile
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG DEPENDENCY=target/dependency
    COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
    COPY ${DEPENDENCY}/META-INF /app/META-INF
    COPY ${DEPENDENCY}/BOOT-INF/classes /app
    ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.springbootmysqlaks.DemoApplication"]
    ```

1. Accédez au fichier *pom.xml* et mettez à jour la collection ```<properties>``` dans ce fichier avec le nom du registre de votre instance Azure Container Registry et la dernière version de ```jib-maven-plugin```.
    Remarque : Si votre nom ACR contient des caractères majuscules, veillez à les convertir en caractères minuscules.

    ```xml
    <properties>
        <docker.image.prefix>mysqlaksdemoregistry.azurecr.io</docker.image.prefix>
        <jib-maven-plugin.version>3.1.4</jib-maven-plugin.version>
        <java.version>1.8</java.version>
    </properties>
    ```

1. Mettez à jour la collection ```<plugins>``` dans le fichier *pom.xml* afin qu’il y ait un élément ```<plugin>``` contenant une entrée pour ```jib-maven-plugin```, comme indiqué ci-dessous. Notez que nous utilisons une image de base provenant du Registre de conteneurs Microsoft, ```mcr.microsoft.com/java/jdk:8-zulu-alpine```, qui contient un JDK officiellement pris en charge pour Azure. Pour obtenir d’autres images de base de ce Registre avec les JDK officiellement pris en charge, consultez [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) et [Java SE JDK and Maven](https://hub.docker.com/_/microsoft-java-maven).
    
    ```xml
    <plugin>
        <artifactId>jib-maven-plugin</artifactId>
        <groupId>com.google.cloud.tools</groupId>
        <version>${jib-maven-plugin.version}</version>
        <configuration>
            <from>
                <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
            </from>
            <to>
                <image>${docker.image.prefix}/${project.artifactId}</image>
            </to>
        </configuration>
    </plugin>
    ```

## <a name="build-the-image-and-push-to-acr"></a>Générer l’image et l’envoyer (push) vers ACR

Dans l’invite de commandes, accédez au dossier *springboot-mysql-aks* et exécutez les commandes suivantes pour définir d’abord le nom par défaut pour Azure Container Registry (sinon, vous devrez spécifier le nom dans ```az acr login```), générer l’image, puis l’envoyer vers le registre.

Vérifiez que votre démon Docker est en cours d’exécution lors de l’exécution de cette étape.

```azurecli-interactive
az config set defaults.acr=mysqlaksdemoregistry
az acr login && mvn compile jib:build
```

## <a name="create-a-kubernetes-cluster-on-aks"></a>Créer un cluster Kubernetes sur AKS

Nous allons maintenant créer un cluster AKS dans le réseau virtuel *vnet-mysqlaksdemo*. 

Dans ce tutoriel, nous allons utiliser les fonctionnalités réseau Azure CNI dans AKS. Si vous souhaitez configurer les fonctionnalités réseau kubenet à la place, consultez [Utiliser les fonctionnalités réseau kubenet dans AKS](../../aks/configure-kubenet.md#create-a-service-principal-and-assign-permissions).  

1. Créez un sous-réseau *subnet-aks* pour le cluster AKS à utiliser.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group rg-mysqlaksdemo \
    --vnet-name vnet-mysqlaksdemo \
    --name subnet-aks \
    --address-prefixes 155.55.2.0/24
    ```

1. Obtenez l’ID de ressource de sous-réseau.

    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet show --resource-group rg-mysqlaksdemo --vnet-name vnet-mysqlaksdemo --name subnet-aks --query id -o tsv)
    ```
    
1. Créez un cluster AKS dans le réseau virtuel, avec le registre ACR (Azure Container Registry) *mysqlaksdemoregistry* attaché.

    ```azurecli-interactive
        az aks create \
        --resource-group rg-mysqlaksdemo \
        --name aks-mysqlaksdemo \
        --network-plugin azure \
        --service-cidr 10.0.0.0/16 \
        --dns-service-ip 10.0.0.10 \
        --docker-bridge-address 172.17.0.1/16 \
        --vnet-subnet-id $SUBNET_ID \
        --attach-acr mysqlaksdemoregistry \
        --dns-name-prefix aks-mysqlaksdemo \
        --generate-ssh-keys
    ```

    Les plages d’adresses IP suivantes sont également définies dans le cadre du processus de création du cluster :
    
    - *--service-cidr* est utilisée pour affecter des services internes dans l’adresse IP d’un cluster AKS. Vous pouvez utiliser n’importe quelle plage d’adresses privées répondant aux exigences suivantes :
        - ne peut pas figurer dans la plage d’adresses IP de réseau virtuel de votre cluster ;
        - ne peut pas présenter de chevauchement avec un autre réseau virtuel avec lequel le réseau virtuel du cluster s’apparie ;
        - ne doit avoir aucun élément en commun avec des adresses IP locales ;
        - ne doit pas être dans les plages 169.254.0.0/16, 172.30.0.0/16, 172.31.0.0/16 ou 192.0.2.0/24.
    
    - L’adresse *--dns-service-ip* est l’adresse IP du service DNS du cluster. Cette adresse doit se situer dans la *plage d’adresses du service Kubernetes*. N’utilisez pas la première adresse IP de votre plage d’adresses. La première adresse de votre plage de sous-réseaux est utilisée pour l’adresse *kubernetes.default.svc.cluster.local*.
    
    - *--docker-bridge-address* est l’adresse réseau du pont Docker qui représente l’adresse réseau *docker0* par défaut présente dans toutes les installations Docker. Vous devez choisir un espace d'adressage qui n'entre pas en collision avec le reste des CIDR de vos réseaux, y compris le CIDR du service du cluster et le CIDR du pod.

## <a name="deploy-the-application-to-aks-cluster"></a>Déployer l’application sur le cluster AKS

1. Accédez à votre ressource de cluster AKS sur le portail Azure.

1. Sélectionnez **Ajouter** et **Ajouter avec YAML** à partir de l’un des affichages de ressources (espace de noms, charges de travail, services et entrées, stockage et configuration). 

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-resource-blade.png" alt-text="Capture d’écran montrant la vue des ressources Azure Kubernetes Service sur le portail Azure.":::

1. Collez le code YAML suivant. Remplacez vos valeurs pour le nom d’utilisateur et le mot de passe de l’administrateur du serveur flexible MySQL.

    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: springboot-mysql-aks
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: springboot-mysql-aks
      template:
        metadata:
          labels:
            app: springboot-mysql-aks
        spec:
          containers:
          - name: springboot-mysql-aks
            image: mysqlaksdemoregistry.azurecr.io/springboot-mysql-aks:latest
            env:
            - name: DATABASE_HOST
              value: "mysql-mysqlaksdemo.mysql.database.azure.com"
            - name: DATABASE_USERNAME
              value: "<your-admin-username>"
            - name: DATABASE_PASSWORD
              value: "<your-admin-password>"
            - name: DATABASE_NAME    
              value: "demo"     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: springboot-mysql-aks
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: springboot-mysql-aks
    ```
1. Sélectionnez **Ajouter** en bas de l’éditeur YAML pour déployer l’application.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-add-with-yaml.png" alt-text="Capture d’écran de la page Ajouter avec YAML dans l’éditeur.":::

1. Une fois le fichier YAML ajouté, la visionneuse de ressources affiche votre application Spring Boot. Prenez note de l’adresse IP externe liée incluse dans le service externe.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-external-ip.png" alt-text="Capture d’écran qui montre, dans le portail Azure, l’adresse IP externe du service de cluster Azure Kubernetes.":::

## <a name="test-the-application"></a>Test de l’application

Pour tester l’application, vous pouvez utiliser cURL.

Tout d’abord, créez un élément « todo » dans la base de données avec la commande suivante.

```bash
curl --header "Content-Type: application/json" \
--request POST \
--data '{"description":"configuration","details":"congratulations, you have deployed your application correctly!","done": "true"}' \
http://<AKS-service-external-ip>
```

Ensuite, récupérez les données en utilisant une nouvelle demande cURL ou en entrant l’*adresse IP externe* du cluster dans votre navigateur.

```bash
curl http://<AKS-service-external-ip>
```

Cette commande retourne la liste des éléments « todos », incluant l’élément que vous avez créé.

```json
[{"id":1,"description":"configuration","details":"congratulations, you have deployed your application correctly!","done":true}]
```

Voici une capture d’écran de ces demandes cURL : :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-curl-output.png" alt-text="Capture d’écran montrant la sortie de la ligne de commande des demandes cURL":::

Vous pouvez voir une sortie similaire par le biais de votre navigateur : :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-browser-output.png" alt-text="Capture d’écran qui montre la sortie des demandes dans le navigateur.":::


Félicitations ! Vous avez déployé avec succès une application Spring Boot sur un cluster Azure Kubernetes Service (AKS) avec Azure Database pour MySQL - Serveur flexible dans le back-end ! 


## <a name="clean-up-the-resources"></a>Nettoyer les ressources

Pour éviter des frais Azure, vous devez nettoyer les ressources inutiles.  Lorsque vous n’avez plus besoin du cluster, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le service conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name rg-mysqlaksdemo
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS](../../aks/kubernetes-service-principal.md#additional-considerations). Si vous avez utilisé une identité managée, l’identité est managée par la plateforme et n’a pas besoin d’être supprimée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer une application WordPress sur AKS avec MySQL](tutorial-deploy-wordpress-on-aks.md)

> [!div class="nextstepaction"]
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)


