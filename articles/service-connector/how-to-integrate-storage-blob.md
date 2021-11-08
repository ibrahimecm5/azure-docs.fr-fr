---
title: Intégrer Stockage Blob Azure avec Service Connector
description: Intégrer Stockage Blob Azure dans votre application avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b0fa624b21125ec6cb6a319e9526f1787565a7f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096293"
---
# <a name="integrate-azure-blob-storage-with-service-connector"></a>Intégrer Stockage Blob Azure avec Service Connector

Cette page affiche les types d’authentification pris en charge et les types de client de Stockage Blob Azure à l’aide de Service Connector. Cous pouvez toujours être en mesure de vous connecter à Stockage Blob Azure dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Java - Spring Boot | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Node.js | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |
| Python | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_STORAGEBLOB_CONNECTIONSTRING | Chaîne de connexion du stockage Blob | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**Identité managée affectée par le système**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Point de terminaison de stockage Blob | `https://{storageAccountName}.blob.core.windows.net/` |

**Identité managée affectée par l’utilisateur**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Point de terminaison de stockage Blob | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | Votre ID client | `{yourClientID}` |

**Principal du service**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Point de terminaison de stockage Blob | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | Votre ID client | `{yourClientID}` |
| AZURE_STORAGEBLOB_CLIENTSECRET | Votre clé secrète client | `{yourClientSecret}` |
| AZURE_STORAGEBLOB_TENANTID | Votre ID d’abonné | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**Secret/ConnectionString**

| Propriétés de l’application | Description | Valeur d'exemple |
| --- | --- | --- |
| azure.storage.account-name | Nom de votre compte de stockage Blob | `{storageAccountName}` |
| azure.storage.account-key | Votre clé de compte de stockage Blob | `{yourSecret}` |
| azure.storage.blob-endpoint | Votre point de terminaison de stockage Blob | `https://{storageAccountName}.blob.core.windows.net/` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
