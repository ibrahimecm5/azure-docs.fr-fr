---
title: Accéder à Config Server et Service Registry
titleSuffix: Azure Spring Cloud
description: Comment accéder aux points de terminaison de Config Server et Service Registry avec le contrôle d’accès en fonction du rôle d’Azure Active Directory.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 9c4a5715af9fe98f5e577410219a54368c27b6df
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054872"
---
# <a name="access-config-server-and-service-registry"></a>Accéder à Config Server et Service Registry

Cet article explique comment accéder aux services Spring Cloud Config Server et Spring Cloud Service Registry managés par Azure Spring Cloud à l’aide de la fonctionnalité RBAC (contrôle d’accès en fonction du rôle) d’Azure AD (Azure Active Directory).

> [!NOTE]
> Les applications déployées et exécutées dans le service Azure Spring Cloud sont automatiquement associées à l’authentification et à l’autorisation basées sur les certificats lors de l’accès au serveur de configuration du Spring Cloud géré et à Config Server. Vous n’avez pas besoin de suivre ces conseils pour ces applications. Les certificats associés sont entièrement gérés par la plateforme Azure Spring Cloud et sont automatiquement injectés dans votre application lorsque vous êtes connecté à Config Server et au registre de service.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Attribuer un rôle à un utilisateur, un groupe ou un principal de service Azure AD, ou à une identité managée pour les ressources Azure

Attribuer le rôle à l'utilisateur [user | group | service-principal | managed-identity] à la portée [management-group | subscription | resource-group | resource].

| Nom de rôle                                       | Description                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Lecteur de serveur Azure Spring Cloud Config         | Autoriser l'accès en lecture au serveur Azure Spring Cloud Config.                       |
| Contributeur au serveur Azure Spring Cloud Config    | Autorisez l'accès en lecture, écriture et suppression au serveur Azure Spring Cloud Config.    |
| Lecteur de registre du service Azure Spring Cloud      | Autoriser l'accès en lecture au registre du service Azure Spring Cloud.                    |
| Contributeur au registre du service Azure Spring Cloud | Autoriser l'accès en lecture, écriture et suppression au registre du service Azure Spring Cloud. |

Pour connaître la procédure détaillée, consultez [Attribution de rôles Azure à l’aide du Portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="access-config-server-and-service-registry-endpoints"></a>Accéder aux points de terminaison de Config Server et Service Registry

Après l'attribution du rôle, le bénéficiaire peut accéder au Spring Cloud Config Server et aux points d'extrémité du Spring Cloud Service Registry à l'aide des procédures suivantes :

1. Obtenez un jeton d’accès. Une fois qu'un utilisateur Azure AD s'est vu attribuer le rôle, il peut utiliser les commandes suivantes pour se connecter à Azure CLI avec un utilisateur, un principal de service ou une identité gérée pour obtenir un jeton d'accès. Pour plus d’informations, consultez [Authentifier Azure CLI](/cli/azure/authenticate-azure-cli).

    ```azurecli
    az login
    az account get-access-token
    ```

1. Composez le point de terminaison. Nous prenons en charge les points de terminaison par défaut des services Spring Cloud Config Server et Spring Cloud Service Registry managés par Azure Spring Cloud.

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > Si vous utilisez Azure China, remplacez `*.azuremicroservices.io` par `*.microservices.azure.cn`. Pour plus d'informations, consultez la section [Vérifier les points de terminaison dans Azure](/azure/china/resources-developer-guide#check-endpoints-in-azure) dans le [guide du développeur Azure China](/azure/china/resources-developer-guide).

1. Accédez au point de terminaison composé avec le jeton d’accès. Placez le jeton d’accès dans un en-tête pour fournir l’autorisation : `--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`.

    Par exemple :

    a. Accédez à un point de terminaison comme *' https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health '* pour voir l'état de santé de Config Server.

    b. Accédez à un endpoint comme *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps '* pour voir les applications enregistrées dans le Spring Cloud Service Registry (Eureka ici).

    Si la réponse est *401 Non autorisé*, vérifiez si le rôle a bien été attribué.  Il faudra plusieurs minutes pour que le rôle prenne effet ou pour vérifier que le jeton d'accès n'a pas expiré.

Pour plus d'informations sur le point de terminaison de l'actionneur, voir [Points de terminaison prêts pour la production](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints).

Pour les points de terminaison Eureka, voir [Eureka-REST-operations](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)

Pour les points de terminaison du serveur de configuration et les informations détaillées sur le chemin d'accès, voir [ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) et [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java) .

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>Enregistrez les applications Spring Boot sur le serveur de configuration Spring Cloud et le registre de services gérés par Azure Spring Cloud

Une fois le rôle attribué, vous pouvez enregistrer les applications Spring Boot sur Spring Cloud Config Server et Service Registry gérés par Azure Spring Cloud avec l'authentification par jeton Azure AD. Le serveur de configuration et le registre des services prennent en charge le [modèle REST personnalisé](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template) pour injecter le jeton de porteur pour l'authentification.

Pour plus d'informations, consultez les échantillons [Access Azure Spring Cloud managed Config Server](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client) et [Access Azure Spring Cloud managed Spring Cloud Service Registry](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client). Les sections suivantes expliquent certains détails importants de ces échantillons.

**In *AccessTokenManager.java*:**

`AccessTokenManager` est chargé d’obtenir un jeton d’accès à partir de Azure AD. Configurez les informations de connexion du principal du service dans le fichier *application.properties* et initialisez `ApplicationTokenCredentials`pour obtenir le jeton. Vous trouverez ce fichier dans les deux exemples.

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

**In *CustomConfigServiceBootstrapConfiguration.java*:**

`CustomConfigServiceBootstrapConfiguration` implémente le modèle REST personnalisé pour le serveur de configuration et injecte le jeton à partir de Azure AD en tant qu' `Authorization` en-têtes. Vous pouvez trouver ce fichier dans l'[exemple de Config Server](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client).

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

**In *CustomRestTemplateTransportClientFactories.java*:**

Les deux classes précédentes concernent l’implémentation du modèle de modèle REST personnalisé pour le Registre du service Spring Cloud. Le `intercept` composant est le même que dans le serveur de configuration ci-dessus. Veillez à ajouter `factory.mappingJacksonHttpMessageConverter()` aux convertisseurs de messages. Ce fichier se trouve dans l'[exemple de Registre Spring Cloud service](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client).

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

Si vous exécutez des applications sur un cluster Kubernetes, nous vous recommandons d’utiliser une adresse IP pour inscrire le registre Spring Cloud service pour l’accès.

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>Étapes suivantes

* [Authentifier Azure CLI](/cli/azure/authenticate-azure-cli)
* [Production ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) (Points de terminaison prêts pour la production)
* [Créer des rôles et des autorisations](how-to-permissions.md)
