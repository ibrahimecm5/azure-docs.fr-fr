---
title: Intégrer Stockage Table Azure avec Service Connector
description: Intégrer Stockage Table Azure à votre application avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 646abc1fc11de0c1d9d5f3c6382e0824c020e85c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096291"
---
# <a name="integrate-azure-table-storage-with-service-connector"></a>Intégrer Stockage Table Azure avec Service Connector

Cette page affiche les types d’authentification et de client pris en charge de Stockage Table Azure à l’aide de Service Connector. Vous pouvez toujours vous connecter à Stockage Table Azure dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net | | | ![icône Oui](./media/green-check.png) | |
| Java | | | ![icône Oui](./media/green-check.png) | |
| Node.js | | | ![icône Oui](./media/green-check.png) | |
| Python | | | ![icône Oui](./media/green-check.png) | |


## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS et Python

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_STORAGETABLE_CONNECTIONSTRING | Chaîne de connexion de Stockage Table | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
