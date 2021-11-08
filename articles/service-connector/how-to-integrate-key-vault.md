---
title: Intégrer Azure Key Vault avec Service Connector
description: Intégrer Azure Key Vault à votre application avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b46f9c0080fb13f178ec1dca96fb49316c2f617
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096295"
---
# <a name="integrate-azure-key-vault-with-service-connector"></a>Intégrer Azure Key Vault avec Service Connector

Cette page affiche les types d’authentification pris en charge et les types de clients de Azure Key Vault à l’aide de Service Connector Vous pouvez toujours être en mesure de vous connecter à Azure Key Vault dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net | | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java | | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java - Spring Boot | | | | ![icône Oui](./media/green-check.png) |
| Node.js | | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Python | | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Identité managée affectée par le système**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Votre étendue RBAC Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Votre point de terminaison Key Vault | `https://{yourKeyVault}.vault.azure.net/` |

**Identité managée affectée par l’utilisateur**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Votre étendue RBAC Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Votre point de terminaison Key Vault | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Votre ID client | `{yourClientID}` |

**Principal du service**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Votre étendue RBAC Azure | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Votre point de terminaison Key Vault | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | Votre ID client | `{yourClientID}` |
| AZURE_KEYVAULT_CLIENTSECRET | Votre clé secrète client | `{yourClientSecret}` |
| AZURE_KEYVAULT_TENANTID | Votre ID de locataire | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**Principal du service**
| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| azure.keyvault.uri | L’URL de votre point de terminaison Key Vault | `"https://{yourKeyVaultName}.vault.azure.net/"` |
| azure.keyvault.client-id | Votre ID client | `{yourClientID}` |
| azure.keyvault.client-key | Votre clé secrète client | `{yourClientSecret}` |
| azure.keyvault.tenant-id |  Votre ID de locataire | `{yourTenantID}` |
| azure.keyvault.scope | Votre étendue RBAC Azure | `https://management.azure.com/.default` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
