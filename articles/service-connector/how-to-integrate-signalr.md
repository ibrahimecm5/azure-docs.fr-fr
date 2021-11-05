---
title: Intégrer Azure SignalR Service avec Service Connector
description: Intégrer Azure SignalR Service à votre application avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 688321101923577fb5ef44ac8d7f23a63bf4b08c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096215"
---
# <a name="integrate-azure-signalr-service-with-service-connector"></a>Intégrer Azure SignalR Service avec Service Connector

Cette page affiche les types de client et d’authentification pris en charge d’Azure SignalR Service avec Service Connector. Vous pouvez toujours être en mesure de vous connecter à Azure SignalR Service dans d’autres langages de programmation sans utiliser Service Connector. Cette page affiche également le nom et la valeur de la variable d’environnement par défaut (ou configuration du Spring Boot) que vous recevez lorsque vous créez la connexion au service. Vous pouvez en savoir plus sur la [convention d’affectation des noms des variables de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Service de calcul pris en charge

- Azure App Service

## <a name="supported-authentication-types-and-client-types"></a>Types d’authentification et de clients pris en charge

| Type de client | Identité managée affectée par le système | Identité managée affectée par l’utilisateur | Secret/ConnectionString | Principal de service |
| --- | --- | --- | --- | --- |
| .Net | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) | ![icône Oui](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Noms des variables d’environnement par défaut ou propriétés de l’application

### <a name="net"></a>.NET

**Secret/ConnectionString**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Chaîne de connexion SignalR Service | `Endpoint=https://{signalrName}.service.signalr.net;AccessKey={};Version=1.0;` |

**Identité managée affectée par le système**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Chaîne de connexion SignalR Service avec une identité gérée | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Identité managée affectée par l’utilisateur**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Chaîne de connexion SignalR Service avec une identité gérée | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Principal du service**

| Nom de variable d’environnement par défaut | Description | Valeur d'exemple |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | Chaîne de connexion SignalR Service avec un principal de service | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};ClientSecret={};TenantId={};Version=1.0;` |

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
