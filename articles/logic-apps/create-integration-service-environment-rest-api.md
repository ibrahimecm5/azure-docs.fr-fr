---
title: Créer des environnements de service d'intégration (ISE) à l'aide de l'API REST Logic Apps
description: Créez un environnement de service d'intégration (ISE) à l'aide de l'API REST Logic Apps afin de pouvoir accéder aux réseaux virtuels Azure à partir d'Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: f50fecfb2675e870025d9e188d5303aacf77f7a2
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112295720"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Créer un environnement de service d'intégration (ISE) à l'aide de l'API REST Logic Apps

Pour les scénarios dans lesquels vos applications logiques et vos comptes d’intégration doivent accéder à un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), vous pouvez créer un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) à l’aide de l’API REST Logic Apps. Pour en savoir plus sur les ISE, consultez [Accéder aux ressources Réseau virtuel Microsoft Azure à partir d’Azure Logic Apps](connect-virtual-network-vnet-isolated-environment-overview.md).

Cet article explique comment créer un ISE à l’aide de l’API REST Logic Apps. Si vous le souhaitez, vous pouvez également activer une [identité managée affectée par le système ou par l’utilisateur](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) sur votre ISE, mais uniquement à l’aide de l’API REST Logic Apps pour l’instant. Cette identité permet à votre ISE d’authentifier l’accès à des ressources sécurisées, comme des machines virtuelles et d’autres systèmes ou services, qui se trouvent à l’intérieur d’un réseau virtuel Azure ou y sont connectées. De cette façon, vous n’êtes pas obligé de vous connecter avec vos informations d’identification.

Pour plus d’informations sur les autres méthodes de création d’un ISE, consultez les articles suivants :

* [Créer un ISE à l’aide du portail Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Créer un ISE à l’aide de l’exemple de modèle de démarrage rapide Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/integration-service-environment)
* [Créer un ISE qui prend en charge l’utilisation de clés gérées par le client pour le chiffrement des données au repos](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prérequis

* Les [conditions préalables](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) et les [conditions d’accès](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) sont les mêmes que lorsque vous créez un ISE à partir du portail Azure.

* Toutes les ressources supplémentaires que vous souhaitez utiliser avec votre ISE afin de pouvoir inclure leurs informations dans la définition d’ISE, par exemple : 

  * Pour permettre la prise en charge d’un certificat auto-signé, vous devez inclure les informations relatives à ce certificat dans la définition d’ISE.

  * Pour activer l’identité managée affectée par l’utilisateur, vous devez créer cette identité à l’avance et inclure les propriétés `objectId`, `principalId` et `clientId` et leurs valeurs dans la définition d’ISE. Pour plus d’informations, consultez [Créer une identité managée affectée par l’utilisateur dans le portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Un outil que vous pouvez utiliser pour créer votre ISE en appelant l’API REST Logic Apps avec une requête PUT HTTPS. Par exemple, vous pouvez utiliser [Postman](https://www.getpostman.com/downloads/) ou créer une application logique qui effectue cette tâche

## <a name="create-the-ise"></a>Créer l’ISE

Pour créer votre ISE en appelant l’API REST Logic Apps, émettez cette requête PUT HTTPS :

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Avec l’API REST version 2019-05-01 de Logic Apps, vous devez émettre votre propre requête PUT HTTP pour les connecteurs ISE.

Le déploiement prend généralement deux heures maximum. Parfois, le déploiement peut prendre jusqu’à quatre heures. Pour suivre l'état du déploiement, accédez au [portail Azure](https://portal.azure.com) et sélectionnez l'icône Notifications sur la barre d'outils Azure. Le volet Notifications s'ouvre alors.

> [!NOTE]
> Si le déploiement échoue ou si vous supprimez votre ISE, Azure peut prendre jusqu’à une heure avant de libérer vos sous-réseaux. Ce délai signifie que vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE.
>
> Si vous supprimez votre réseau virtuel, Azure a généralement besoin de jusqu'à deux heures pour libérer vos sous-réseaux, mais cette opération peut prendre plus longtemps. 
> Lors de la suppression de réseaux virtuels, assurez-vous qu’aucune ressource n’est restée connectée. 
> Consultez [Supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>En-tête de requête

Dans l’en-tête de la requête, incluez les propriétés suivantes :

* `Content-type`: affectez la valeur `application/json` à cette propriété.

* `Authorization`: affectez comme valeur de cette propriété le jeton du porteur pour le client qui a accès à l’abonnement Azure ou au groupe de ressources que vous souhaitez utiliser

<a name="request-body"></a>

## <a name="request-body"></a>Corps de la demande

Dans le corps de la demande, indiquez la définition de ressource à utiliser pour créer votre ISE, y compris les informations relatives aux capacités supplémentaires que vous souhaitez activer sur votre ISE, par exemple :

* Pour créer un ISE qui autorise l’utilisation d’un certificat auto-signé et d’un certificat émis par l’autorité de certification d’entreprise qui est installé à l’emplacement `TrustedRoot`, incluez l’objet `certificates` à l’intérieur de la section `properties` de la définition d’ISE, comme le décrit cet article ultérieurement.

* Pour créer un ISE qui utilise une identité managée affectée par le système ou par l’utilisateur, incluez l’objet `identity` avec le type d’identité managée et d’autres informations requises dans la définition d’ISE, comme le décrit cet article ultérieurement.

* Pour créer un ISE qui utilise des clés gérées par le client et Azure Key Vault pour chiffrer les données au repos, incluez les [informations qui permettent la prise en charge des clés gérées par le client](customer-managed-keys-integration-service-environment.md). Vous pouvez configurer des clés gérées par le client *uniquement au moment de la création*, pas par la suite.

### <a name="request-body-syntax"></a>Syntaxe du corps de la demande

Voici la syntaxe du corps de la requête, qui décrit les propriétés à utiliser lors de la création de votre ISE :

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Exemple de corps de requête

Cet exemple de corps de requête montre les exemples de valeurs :

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Ajouter des certificats racines personnalisés

Vous utilisez souvent ISE pour vous connecter à des services personnalisés sur votre réseau virtuel ou localement. Ces services personnalisés sont souvent protégés par un certificat émis par une autorité de certification racine personnalisée, telle qu’une autorité de certification d’entreprise ou un certificat auto-signé. Pour plus d’informations sur l’utilisation de certificats auto-signés, consultez [Sécuriser l’accès et les données – Accès pour les appels sortants à d’autres services et systèmes](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Pour que votre environnement ISE se connecte correctement à ces services via le protocole TLS (Transport Layer Security), votre ISE doit accéder à ces certificats racines.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Considérations relatives à l’ajout de certificats racines personnalisés

Avant de mettre à jour votre ISE avec un certificat racine approuv personnalisé, passez en revue les considérations suivantes :

* Veillez à charger le certificat racine *et* tous les certificats intermédiaires. Le nombre maximal de certificats est de 20.

* Le nom d’objet du certificat doit correspondre au nom d’hôte du point de terminaison cible que vous souhaitez appeler à partir d’Azure Logic Apps. 

* Le chargement des certificats racines est une opération de remplacement dans laquelle le chargement le plus récent remplace les chargements précédents. Par exemple, si vous envoyez une requête qui charge un certificat, puis envoyez une autre demande pour charger un autre certificat, votre ISE utilise uniquement le deuxième certificat. Si vous devez utiliser les deux certificats, ajoutez-les ensemble dans la même demande.  

* Le chargement des certificats racines est une opération asynchrone qui peut prendre un certain temps. Pour vérifier l’état ou le résultat, vous pouvez envoyer une demande `GET` à l’aide du même URI. Le message de réponse contient un champ `provisioningState` qui renvoie la valeur `InProgress` lorsque l’opération de chargement est encore en cours. Lorsque la valeur `provisioningState` correspond à `Succeeded`, l’opération de chargement est terminée.

#### <a name="request-syntax"></a>Syntaxe de la requête

Pour mettre à jour votre ISE avec un certificat racine approuv personnalisé, envoyez la requête PATCH HTTPS suivante à l’[URL d’Azure Resource Manager, qui diffère selon votre environnement Azure](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane), par exemple :

| Environnement | URL Azure Resource Manager |
|-------------|----------------------------|
| Azure global (multilocataire) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Syntaxe du corps de la demande pour l’ajout de certificats racines personnalisés

Voici la syntaxe du corps de la demande, qui décrit les propriétés à utiliser lorsque vous ajoutez des certificats racines :

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des ressources à des environnements de service d'intégration](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gérer les environnements de service d’intégration](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
